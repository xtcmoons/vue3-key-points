# Vue 3 `defineExpose()` 最佳实践

`defineExpose()` 是 Vue 3 组合式 API 中用于在 `<script setup>` 语法下，向父组件暴露当前组件内部属性或方法的函数。其最佳实践如下：

## 1. 只暴露必要的属性和方法
- **最小暴露原则**：只暴露父组件确实需要访问的属性或方法，避免暴露全部内部实现，减少耦合。

```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
function increment() {
  count.value++
}
defineExpose({ count, increment }) // 只暴露需要的
</script>
```

## 2. 避免暴露响应式对象的内部实现
- 如果只需要暴露数据，优先暴露 ref 或 computed，而不是整个复杂对象，防止父组件误操作。

## 3. 明确文档和注释
- 对暴露的属性和方法加注释，说明用途，便于团队协作和维护。

## 4. 不要滥用
- `defineExpose()` 主要用于父组件通过 `ref` 获取子组件实例时访问，不建议频繁使用。大多数情况下，父子通信应优先用 props 和 emits。

## 5. 类型安全
- 在 TypeScript 项目中，给暴露的属性和方法加类型注解，提升可维护性。

```ts
// 在 <script setup lang="ts"> 中
import type { Ref } from 'vue'
defineExpose<{ count: Ref<number>, increment: () => void }>({
  count,
  increment
})
```

## 6. 结合 `ref` 使用
- 父组件通过 `ref` 获取子组件实例后，才能访问 `defineExpose` 暴露的内容。

```vue
<!-- Parent.vue -->
<template>
  <Child ref="childRef" />
</template>
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'
const childRef = ref()
onMounted(() => {
  childRef.value.increment()
})
</script>
```

---

### 总结
- 只暴露必要内容，避免暴露实现细节
- 优先用 props/emits 进行通信
- 加注释和类型
- 仅在确有需要时使用 