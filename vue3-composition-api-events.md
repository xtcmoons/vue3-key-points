# Vue 3 组合式 API 事件知识点与最佳实践

> 参考：[Vue 官方文档](https://cn.vuejs.org/guide/components/events.html) · [DEV: Vue 3 Composition API Tips](https://dev.to/delia_code/the-ultimate-guide-to-vue-3-composition-api-tips-and-best-practices-54a6) · [Medium: Vue 3 Best Practices](https://medium.com/@ignatovich.dm/vue-3-best-practices-cb0a6e281ef4)

---

## 1. 事件基础知识

- 父组件通过 `v-on`（`@`）监听子组件自定义事件。
- 子组件通过 `emit` 触发事件。
- 组合式 API 推荐使用 `defineEmits`（`<script setup>`）或 `ctx.emit`（普通 setup）来声明和触发事件。

---

## 2. 组合式 API 事件最佳实践

### 2.1 `<script setup>` 语法糖
- 使用 `defineEmits` 明确声明事件类型，提升类型安全和可读性。
- 推荐配合 TypeScript 使用，事件参数类型一目了然。

### 2.2 事件命名规范
- 使用 kebab-case（短横线）命名事件，如 `user-select`。
- 事件名应表达动作（如 select、update、delete）。

### 2.3 事件参数设计
- 事件参数应简洁明了，避免传递整个组件实例。
- 推荐传递原始数据或对象。

### 2.4 事件类型声明（TypeScript）
- 使用类型接口声明事件及参数，提升开发体验和可维护性。

---

## 3. 完整代码示例

### 子组件 UserList.vue

```vue
<template>
  <ul>
    <li
      v-for="user in users"
      :key="user.id"
      @click="onUserClick(user)"
      style="cursor:pointer"
    >
      {{ user.name }}
    </li>
  </ul>
</template>

<script setup lang="ts">
// 1. 定义 props 和 emits 类型
interface User {
  id: number
  name: string
  email: string
}

const props = defineProps<{
  users: User[]
}>()

const emit = defineEmits<{
  (e: 'user-select', user: User): void
}>()

// 2. 事件处理函数
function onUserClick(user: User) {
  emit('user-select', user)
}
</script>
```

---

### 父组件 App.vue

```vue
<template>
  <h2>用户列表</h2>
  <UserList :users="users" @user-select="handleUserSelect" />
  <div v-if="selectedUser">
    <h3>已选用户：</h3>
    <pre>{{ selectedUser }}</pre>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import UserList from './components/UserList.vue'

interface User {
  id: number
  name: string
  email: string
}

const users = ref<User[]>([
  { id: 1, name: '张三', email: 'zhangsan@example.com' },
  { id: 2, name: '李四', email: 'lisi@example.com' }
])

const selectedUser = ref<User | null>(null)

function handleUserSelect(user: User) {
  selectedUser.value = user
}
</script>
```

---

## 4. 进阶：自定义事件类型声明

- 推荐在大型项目中为事件类型单独声明接口，便于维护和类型推断。

```ts
// emits.d.ts
export interface UserListEmits {
  (e: 'user-select', user: User): void
}
```
然后在组件中：
```ts
const emit = defineEmits<UserListEmits>()
```

---

## 5. 参考最佳实践

- 事件只做通知，不做数据变更，数据变更应由父组件完成。
- 事件参数类型明确，避免 any。
- 事件名与组件 props、方法区分开，避免歧义。
- 充分利用 TypeScript 类型推断和校验。

---

## 6. 参考资料

- [Vue 3 官方文档：组件事件](https://cn.vuejs.org/guide/components/events.html)
- [Vue 3 组合式 API 最佳实践（DEV）](https://dev.to/delia_code/the-ultimate-guide-to-vue-3-composition-api-tips-and-best-practices-54a6)
- [Vue 3 组合式 API 最佳实践（Medium）](https://medium.com/@ignatovich.dm/vue-3-best-practices-cb0a6e281ef4)

---

如需更复杂的事件通信（如多层嵌套、全局事件），可考虑 provide/inject、mitt 或状态管理（如 Pinia）等高级方案。 