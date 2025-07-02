# Vue 3 组合式 API 模板语法要点总结

---

## 1. 数据绑定

- **插值表达式**：  
  `{{ variable }}`  
  绑定响应式数据到模板。

- **属性绑定**：  
  `:attr="value"` 或 `v-bind:attr="value"`  
  例：`<img :src="imgUrl" :alt="desc" />`

- **布尔属性简写**：  
  例：`<button :disabled="isDisabled">`

---

## 2. 事件绑定

- **基本用法**：  
  `@event="handler"` 或 `v-on:event="handler"`  
  例：`<button @click="doSomething">Click</button>`

- **传参**：  
  例：`<button @click="doSomething('param')">`

- **修饰符**：  
  - `.stop`：阻止冒泡
  - `.prevent`：阻止默认
  - `.self`：仅自身触发
  - `.once`：只触发一次  
  例：`<form @submit.prevent="onSubmit">`

---

## 3. 条件渲染

- `v-if` / `v-else-if` / `v-else`  
  例：  
  ```vue
  <div v-if="isShow">A</div>
  <div v-else>B</div>
  ```

- `v-show`  
  仅控制 display 显隐，适合频繁切换。

---

## 4. 列表渲染

- `v-for="item in list" :key="item.id"`  
  例：  
  ```vue
  <li v-for="user in users" :key="user.id">{{ user.name }}</li>
  ```

---

## 5. 表单与双向绑定

- `v-model`  
  例：  
  ```vue
  <input v-model="inputValue" />
  <select v-model="selected">
    <option value="A">A</option>
  </select>
  ```

- **修饰符**：  
  - `.lazy`：失焦或回车时同步
  - `.number`：自动转数字
  - `.trim`：去除首尾空格

---

## 6. 计算属性与方法

- 直接在模板中使用 `setup` 返回的 `computed`、`ref`、`reactive` 数据和方法。

---

## 7. 组件通信

- **Props 传递**：  
  `<Child :msg="parentMsg" />`

- **事件派发**：  
  `<Child @custom-event="handler" />`

- **v-model 绑定子组件**：  
  `<Child v-model="value" />`  
  子组件用 `defineProps` 和 `defineEmits`。

---

## 8. 插槽

- **默认插槽**：  
  `<slot></slot>`

- **具名插槽**：  
  `<slot name="header"></slot>`

- **作用域插槽**：  
  ```vue
  <slot :data="data"></slot>
  ```

---

## 9. 动态组件与异步组件

- **动态组件**：  
  `<component :is="componentName"></component>`

---

## 10. 指令

- **内置指令**：  
  - `v-text`、`v-html`、`v-show`、`v-if`、`v-for`、`v-model`、`v-on`、`v-bind`、`v-slot`
- **自定义指令**：  
  `<div v-my-directive></div>`

---

## 11. 模板引用

- `ref="el"`  
  结合 `const el = ref(null)` 和 `onMounted` 获取 DOM。

---

## 12. 其他

- **模板只能有一个根节点**（Vue 3 支持多个根节点，但推荐结构清晰）。
- **表达式限制**：模板表达式应简单，不支持语句（如 if/for/赋值）。

---

如需具体代码示例或某一语法点详细讲解，请告知！ 