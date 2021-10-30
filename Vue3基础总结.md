# Vue3.0

> 2 年开发，99 位贡献者，2600 次提交，628 次 pr，打包体积减小 41%，初次渲染快 55%，更新快 133%，内存使用减少 54%

### Vue2.x 的不足

- 全局 API 容易污染全局环境
- Mixin 缺点：命名冲突、不清楚变量来源、重用到组件的问题
- 响应式:

  - 不允许动态添加根级别的响应式属性
  - 对象：不能检测对象属性的添加和移除，可以使用 Vue.set(object,propertyName,value)
  - 数组：1、不能利用索引直接设置一个数组项、2、不能修改数组的长度

    ```js
    // 可以通过Vue.set和splice()来解决
    Vue.set(vm.items, indexOfItem, newValue);
    // Array.prototype.splice
    vm.items.splice(indexOfItem, 1, newValue);
    vm.$set(vm.items, indexOfItem, newValue);
    vm.items.splice(newLength);

    // 响应式原理
    Object.defineProperty(data, "count", {
      get() {},
      set() {},
    });
    new Proxy(data, {
      get(key) {},
      set(key, value) {},
    });
    ```

### Vue3 常用 API

- setup()：在组件被创建之前，props 被解析之后执行。它是组合式 API 的入口。

  - 执行 setup 时，组件实例尚未被创建。因此，你只能访问以下 property：props、attrs、slots、emit，无法访问以下组件选项：data、computed、methods、refs (模板 ref)
  - 若要对传递给 setup() 的参数进行类型推断，你需要使用 defineComponent。
    ```js
    import axios from "axios";
    import { defineComponent } from "vue";
    export default defineComponent({
      async setup() {
        const res = await axios.get("url");
        return {
          data: res.data,
        };
      },
    });
    ```
  - 参数：

    - props：因为 props 是响应式的，你不能使用 ES6 解构，它会消除 prop 的响应性。如果需要解构 prop，可以在 setup 函数中使用 toRefs

      ```js
      import { toRefs } from 'vue'
      setup(props) {
        const { title } = toRefs(props)
        console.log(title.value)
      }

      //如果 title 是可选的 prop，props 中可能没有 title 。toRefs 将不会为 title 创建一个 ref 。需要用 toRef 替代它
      import { toRef } from 'vue'
      setup(props) {
        const title = toRef(props, 'title')
        console.log(title.value)
      }
      ```

  - context：是一个普通的 JavaScript 对象，不是响应式的，可以安全地使用 ES6 解构 attrs, slots, emit, expose

    ```html
    <!-- 获取自定义事件 -->
    <modal isOpen="ifOpen" @closeModel="closeIt">弹层</modal>
    ```

    ```js
    import { defineComponent } from "vue";
    export default defineComponent({
      setup(props, context) {
        const buttonClick = () => {
          // emit派发事件名称
          context.emit("closeModel");
        };
        return {
          buttonClick,
        };

        // context.attrs是None-Props属性
        // context.slots相当于vue2.x中的this.$slots
        // 通过h渲染，将插槽里的值渲染到div元素里
        // return ()=>h('div',{},context.slots.default())
      },
    });
    ```

  - 组合式 API 生命周期钩子
    - beforeCreate -> 使用 setup()
    - created -> 使用 setup()
    - beforeMount -> onBeforeMount
    - mounted -> onMounted
    - beforeUpdate -> onBeforeUpdate
    - updated -> onUpdated
    - beforeUnmount -> onBeforeUnmount
    - unmounted -> onUnmounted
    - renderTracked -> onRenderTracked // 每次渲染后重新收集响应式依赖
    - renderTriggered -> onRenderTriggered // 每次触发页面重新渲染是自动执行
    - activated -> onActivated
    - deactivated -> onDeactivated
    - errorCaptured -> onErrorCaptured
      ```js
      setup(){
        const error = ref(null);
        errorCaptured(e){
          error.value = e
          return true
        }
      }
      ```

- ref、reactive、toRefs、toRef 响应式的引用

  - ref：将基本数据类型的值变成响应式的值。ref 对象具有指向内部值的单个 property.value。

    ```js
    const count = ref(0); // proxy({value:0})
    console.log(count.value); // 0
    count.value++;
    console.log(count.value); // 1
    ```

  - reactive：返回对象的响应式副本

    ```js
    setup(){
      const data = reactive({
        count: 0,
        increase: () => data.count++,
        double: computed(() => data.count * 2),
      });
      // toRefs将proxy({count:0,...})=>{count:proxy({value,0}),...}转换成可以解构的响应式
      const refData = toRefs(data);
      // reactive通过proxy将{name:'bj'}=> proxy({name:'bj'})响应式
      const obj = reactive({name:'bj'});
      // readOnlyObj只能读取不能修改，可以传给子组件，子组件不能修改父组件，单向数据流
      const readOnlyObj = readonly(obj)
      return {
        ...refData,
        obj
      };
    }
    ```

  - toRefs：是解构的响应式对象仍具备响应式
    > 将响应式对象转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 ref。可以在不丢失响应性的情况下对返回的对象进行解构/展开
  - toRef：对象可选属性的响应式需要用 toRef，如果没有这个属性，会为对象加上这个属性，属性值为空，如果没有这个属性 toRefs 会报错

    ```js
    setup(){
      // 如果data中没有age熟悉，就会添加一个默认的data.age.value=''
      const age = toRef(data, "age");
      setTimeout(() => {
        age.value = 18;
      }, 500);
      return { age };
    }

    ```

- computed：计算属性

  ```js
  const count = ref(1);
  const plusOne = computed(() => count.value + 1);
  console.log(plusOne.value); // 2
  plusOne.value++; // 错误

  // 接受一个具有 get 和 set 函数的对象，用来创建可写的 ref 对象
  const count = ref(1);
  const plusOne = computed({
    get: () => count.value + 1,
    set: (val) => {
      count.value = val - 1;
    },
  });
  plusOne.value = 1;
  console.log(count.value); // 0
  ```

- watch、watchEffect
  > [Vue warn]:Invalid watch source: watch source can only be a getter/effect function, a ref, a reactive object, or an array of these type
  - watch
    - 具有一定的惰性 lazy 第一次不执行
    - 参数可以拿到原始值和当前值
    - 可以监听多个数据的变化
    ```js
    watch([count, () => data.name], (newVal, oldVal) => {
      console.log(newVal, oldVal);
    });
    ```
  - watchEffect
    - 立即执行，没有惰性 immediate
    - 不需要传监听内容，自动感知代码依赖，只有一个回调函数，依赖代码改变触发
    - 不能获取之前数据的值
      ```js
      const stop = watchEffect(() => {
        console.log(obj.name);
        setTimeout(() => {
          stop(); // 取消watchEffect监听
        }, 500);
      });
      ```
- Suspense 异步组件
  ```html
  <Suspense>
    <template #default>
      <async-show>
    </template>
    <template #fallback>
      <p>Loading...</p>
    </template>
  </Suspense>
  ```
- 自定义函数 hooks

  ```js
  // 获取鼠标位置
  import { ref, onMounted, onUnmounted } from "vue";
  const position = () => {
    const x = ref(0);
    const y = ref(0);
    const getPosition = (e) => {
      x.value = e.pageX;
      y.value = e.pageY;
    };
    onMounted(() => {
      document.addEventListener("click", getPosition);
    });
    onUnmounted(() => {
      document.removeEventListener("click", getPosition);
    });
  };
  export default position;
  ```

  ```js
  // loading
  import { ref } from "vue";
  import axios from "axios";
  function reqLoading(url) {
    const res = ref(null);
    const loading = ref(true);
    const noLoading = ref(false);
    const error = ref(error);
    axios
      .get(url)
      .then((res) => {
        loading.value = false;
        noLoading.value = true;
        res.value = res.data;
      })
      .catch((e) => {
        loading.value = false;
        error.value = e.message;
      });
    return {
      res,
      loading,
      noLoading,
      error,
    };
  }
  export default reqLoading;
  ```

- provide 和 inject

  - inject 函数有两个参数：
    - inject 的 property 的 name
    - 默认值 (可选)

  ```js
  // 父组件
  setup() {
    const location = ref('North Pole')
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })
    const updateLocation = () => {
      location.value = 'South Pole'
    }
    // readonly包裹，单项数据流原则，子组件只能通过父组件的方法来修改
    provide('location', readonly(location))
    provide('geolocation', readonly(geolocation))
    provide('updateLocation', updateLocation)
  }
  // 子组件
  setup() {
    const userLocation = inject('location', 'The Universe')
    const userGeolocation = inject('geolocation')
    const updateUserLocation = inject('updateLocation')

    return {
      userLocation,
      userGeolocation,
      updateUserLocation
    }
  }
  ```

### Demo：todoList

```js
// 列表的属性和方法抽离
const listControl = () => {
  const { reactice } = Vue;
  const list = reactive([]);
  const addItem = (item) => {
    list.push(item);
  };
  return { list, addItem };
};
// 提交的属性和方法抽离
const submit = () => {
  const { ref } = Vue;
  const inputValue = ref(null);
  const inputChange = (e) => {
    inputValue.value = e.target.value;
  };
  return { inputValue, inputChange };
};
const app = Vue.createApp({
  // 调度获取需要返回的data
  setup() {
    const { list, addItem } = listControl();
    const { inputValue, inputChange } = submit();
    return {
      list,
      addItem,
      inputValue,
      inputChange,
    };
  },
  template: `
  <div>
    <div>
      <input :value="inputValue" @input="inputChange"/>
      <buttom @click="()=>addItem(inputValue)">提交</buttom>
    </div>
    <ul>
      <li v-for = "(item,idx) in list" :key="idx">{{item}}</li>
    </ul>
  </div>
  `,
});
```
