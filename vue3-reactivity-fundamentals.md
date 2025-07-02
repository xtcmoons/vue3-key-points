# Vue 3 响应式基础与最佳实践

## 一、核心概念

### 1. 响应式系统
- Vue 3 使用 JavaScript 的 Proxy 实现响应式追踪，能高效侦测对象属性的变化。
- 响应式数据的变更会自动驱动视图更新。

### 2. ref 与 reactive

#### ref
- 用于声明**基本类型**或单一值的响应式数据。
- 语法：`const count = ref(0)`
- 访问和赋值需用 `.value`，如 `count.value++`
- 在模板中可直接用 `{{ count }}`，无需 `.value`，模板会自动解包。

#### reactive
- 用于声明**对象、数组、Map、Set**等复杂类型的响应式数据。
- 语法：`const state = reactive({ count: 0 })`
- 直接通过属性访问和赋值，如 `state.count++`
- 只能用于对象类型，不能直接用于基本类型。

### 3. 响应式代理与原始值
- 通过 reactive 创建的对象是代理对象，原始对象不会变为响应式。
- 始终通过代理对象访问和修改数据，保证响应性。

### 4. ref 与 reactive 的解包
- 在模板中，顶层的 ref 会自动解包。
- 在 reactive 对象的属性中，ref 也会自动解包。
- 在数组、Map、Set 等集合类型中，ref 不会自动解包，需手动 `.value`。

### 5. 响应性丢失的场景
- 解构 reactive 对象的属性为本地变量时，会丢失响应性。
- 替换整个 reactive 对象会丢失响应性连接。

---

## 二、最佳实践

### 1. 推荐使用 ref 作为主要响应式 API
- **原理说明**：ref 适用于所有类型（基本类型、对象、数组等），其本质是一个带有 .value 属性的响应式对象。ref 在模板中会自动解包，书写更简洁。
- **适用场景**：单一值、基本类型、需要整体替换的对象、DOM/组件引用。
- **常见误区**：ref 包裹对象/数组时，访问和赋值都要用 .value。
- **注意事项**：ref 更适合与 TypeScript 配合，类型推断更友好。

```js
// 基本类型
const count = ref(0)
count.value++

// 对象/数组
const user = ref({ name: 'Tom', age: 18 })
user.value.name = 'Jerry'

const arr = ref([1, 2, 3])
arr.value.push(4)

// DOM/组件引用
const inputRef = ref(null)
onMounted(() => {
  inputRef.value.focus()
})
```

### 2. 避免响应性丢失
- **原理说明**：reactive 只能追踪对象本身的属性，解构出来的变量与原对象失去响应式连接。直接替换 reactive 对象会丢失响应性，因为响应性是绑定在原始对象的代理上的。
- **适用场景**：需要频繁修改对象属性时，始终通过代理对象访问和修改。
- **常见误区**：
  - 解构 reactive 属性为本地变量后，变量不再响应式。
  - 直接用新对象替换 reactive 对象会丢失响应性。
- **注意事项**：如需解构，建议用 computed 或 ref 代理属性。

```js
const state = reactive({ count: 0 })

// 错误：解构会丢失响应性
let { count } = state
count++ // 不会影响 state.count

// 正确：始终通过 state.count 访问
state.count++

// 正确：用 computed 代理属性
const countRef = computed({
  get: () => state.count,
  set: v => (state.count = v)
})
countRef.value++

// 错误：直接替换对象会丢失响应性
// state = reactive({ count: 1 })

// 正确：只修改属性
state.count = 2
```

### 3. 在模板中优先使用顶层 ref
- **原理说明**：Vue 模板会自动解包顶层 ref，避免频繁书写 .value，提升开发体验。
- **适用场景**：setup 返回的 ref 变量直接用于模板。
- **常见误区**：嵌套对象的 ref 不会自动解包，如 object.id，需手动 .value。
- **注意事项**：尽量将需要在模板中用到的 ref 提升为顶层变量。

```js
// setup 中
const count = ref(0)
const user = ref({ name: 'Tom' })
return { count, user }
```

```vue
<!-- 模板中 -->
<div>{{ count }}</div> <!-- 自动解包，无需 .value -->
<div>{{ user.name }}</div> <!-- user 是 ref，user.name 不是 ref，自动解包 -->
<!-- 错误用法：嵌套 ref 不会自动解包 -->
<div>{{ user.value.name }}</div> <!-- 不推荐 -->
```

### 4. 组合使用 ref 和 reactive
- **原理说明**：ref 和 reactive 可以灵活组合，满足复杂场景下的响应式需求。
- **适用场景**：
  - 需要响应式对象的某个属性单独为 ref。
  - 需要整体替换对象时用 ref 包裹 reactive。
- **常见误区**：ref 作为 reactive 属性时，赋新 ref 会断开原 ref 的响应性联系。
- **注意事项**：避免在 reactive 对象中频繁替换 ref 属性。

```js
// ref 作为 reactive 属性
const name = ref('Tom')
const state = reactive({ name, age: 18 })
state.name.value = 'Jerry' // 正确

// 替换 ref 属性会断开原 ref
state.name = ref('Alice') // state.name 已不再与原 name ref 同步

// reactive 对象赋值给 ref
const user = ref(reactive({ name: 'Alice', age: 20 }))
user.value.name = 'Bob'

// 进阶：响应式 Map/Set
const map = reactive(new Map())
map.set('count', ref(0))
map.get('count').value++
```

### 5. 事件与方法中的响应式
- **原理说明**：ref 在 JS 逻辑中必须通过 .value 访问和赋值，否则不会触发响应式更新。
- **适用场景**：事件处理、方法、watch、computed 等逻辑中操作 ref。
- **常见误区**：忘记 .value 导致数据未更新。
- **注意事项**：模板中无需 .value，JS 逻辑中必须 .value。

```js
const count = ref(0)
function increment() {
  count.value++
}

// watch 监听 ref
watch(count, (newVal, oldVal) => {
  console.log('count changed:', newVal)
})

// computed 依赖 ref
const double = computed(() => count.value * 2)
```

### 6. 复杂集合类型
- **原理说明**：reactive 只能递归代理对象属性，不能自动解包集合中的 ref。
- **适用场景**：数组、Map、Set 等集合类型需要响应式时。
- **常见误区**：集合中的 ref 访问时忘记 .value。
- **注意事项**：集合元素为 ref 时，访问需 .value。

```js
const books = reactive([ref('Vue 3 Guide'), ref('Vue 3 实战')])
books.forEach(book => {
  console.log(book.value)
})

const set = reactive(new Set([ref(1), ref(2)]))
for (const item of set) {
  console.log(item.value)
}

const map = reactive(new Map([['count', ref(0)]]))
map.get('count').value++
```

### 7. 组件间状态管理
- **原理说明**：全局状态推荐用 Pinia 等库，底层依然用 ref/ reactive 实现响应式。
- **适用场景**：多组件共享、跨页面状态。
- **常见误区**：直接用 reactive 管理全局状态，类型推断和维护不如 ref 友好。
- **注意事项**：Pinia 推荐组合式写法，充分利用 ref 的类型推断和响应式特性。

```js
// store.js (Pinia 示例)
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const double = computed(() => count.value * 2)
  function increment() {
    count.value++
  }
  return { count, double, increment }
})

// 组件中使用
const counter = useCounterStore()
counter.increment()
console.log(counter.double) // 2
```

---

## 三、常见误区与防坑

- **误区1**：直接替换 reactive 对象会丢失响应性，应只修改属性。
- **误区2**：解构 reactive 属性后，变量不再响应式。
- **误区3**：在模板中访问嵌套 ref（如 `object.id`），不会自动解包，需手动 `.value` 或提升为顶层属性。

---

## 四、代码示例

```js
import { ref, reactive } from 'vue'

export default {
  setup() {
    // 基本类型
    const count = ref(0)
    // 对象类型
    const state = reactive({ name: 'Vue', age: 3 })

    // 推荐：只修改属性
    function updateName(newName) {
      state.name = newName
    }

    // 错误：不要直接替换对象
    // state = reactive({ name: 'New' }) // 会丢失响应性

    return { count, state, updateName }
  }
}
```

---

## 五、官方文档参考

- [Vue 3 响应式基础](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals.html)

如需更详细的代码示例或某一细节讲解，请随时告知！ 