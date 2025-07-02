# Vue 3 以 use 开头的函数有什么作用

## 1. 概述

在 Vue 3 中，以 `use` 开头的函数通常被称为"组合式函数"（Composables）。它们是基于 Composition API 的一种代码组织和复用方式。

---

## 2. 主要作用

1. **封装可复用逻辑**
   - `use` 开头的函数用于封装一段可复用的响应式逻辑（如状态管理、事件监听、数据获取等），方便在多个组件中共享和复用。

2. **组合式 API 的核心实践**
   - 这是 Vue 3 Composition API 的核心实践方式。通过调用这些 `use` 函数，可以在 `setup()` 或 `<script setup>` 中灵活组合各种功能。

3. **命名规范**
   - 以 `use` 开头是一种社区约定，便于一眼识别该函数为组合式函数（类似 React 的 Hook）。

4. **返回响应式数据和方法**
   - `use` 函数通常会返回响应式的 state、计算属性、方法等，供组件使用。

---

## 3. 典型例子

### 官方内置 use 函数

- `useAttrs()`：获取组件的 $attrs。
- `useSlots()`：获取插槽内容。
- `useRoute()`、`useRouter()`（Vue Router 插件）：获取路由信息或路由实例。

### 自定义 use 函数

```js
// useMouse.js
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(e) {
    x.value = e.pageX
    y.value = e.pageY
  }

  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  return { x, y }
}
```

在组件中使用：

```js
<script setup>
import { useMouse } from './useMouse'
const { x, y } = useMouse()
</script>
```

---

## 4. 总结

- 以 `use` 开头的函数是 Vue 3 组合式 API 的最佳实践，用于封装和复用逻辑。
- 它们让代码更模块化、易维护、易测试。
- 推荐自定义组合逻辑时都以 `use` 开头命名。

---

**参考：**
- [Vue 官方文档：组合式函数 Composables](https://vuejs.org/guide/reusability/composables.html) 