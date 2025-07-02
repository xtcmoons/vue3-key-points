# Vue3 组件 Props 知识点总结

> 参考：[Vue3 官方文档 Props](https://cn.vuejs.org/guide/components/props.html#prop-validation)

---

## 1. Props 声明

### `<script setup>` 语法
```vue
<script setup>
const props = defineProps(['foo'])
console.log(props.foo)
</script>
```

### 选项式 API
```js
export default {
  props: ['foo'],
  created() {
    console.log(this.foo)
  }
}
```

---

## 2. 类型声明

### 对象语法
```js
export default {
  props: {
    title: String,
    likes: Number
  }
}
```
或
```vue
<script setup>
defineProps({
  title: String,
  likes: Number
})
</script>
```

### TypeScript 类型声明
```vue
<script setup lang="ts">
defineProps<{
  title?: string
  likes?: number
}>()
</script>
```

---

## 3. 默认值与必填

```js
export default {
  props: {
    msg: {
      type: String,
      required: true
    },
    count: {
      type: Number,
      default: 0
    }
  }
}
```

---

## 4. 多类型与自定义校验

```js
export default {
  props: {
    value: [String, Number],
    status: {
      validator(value) {
        return ['success', 'warning', 'danger'].includes(value)
      }
    }
  }
}
```

---

## 5. 带工厂函数的默认值（对象/数组）

```js
export default {
  props: {
    options: {
      type: Array,
      default() {
        return []
      }
    }
  }
}
```

---

## 6. Boolean 类型的特殊处理

```js
export default {
  props: {
    disabled: Boolean
  }
}
```
使用时：
```vue
<MyComponent disabled />
<!-- 等价于 :disabled="true" -->
```

---

## 7. 解构与响应性

```vue
<script setup>
const { foo = 'default' } = defineProps<{ foo?: string }>()
</script>
```
注意：解构后用于 watch 时需用 getter 包裹：
```js
watch(() => foo, (val) => { /* ... */ })
```

---

## 8. 单向数据流

Props 是单向的，父组件数据流向子组件，子组件不应直接修改 props，而应通过事件通知父组件修改。

---

## 9. 传递复杂类型

对象和数组作为 props 传递时，子组件可以修改其内部属性（因为是引用），但不推荐这样做，建议通过事件通知父组件修改。

---

## 10. 运行时类型检查

支持的类型有：String、Number、Boolean、Array、Object、Date、Function、Symbol、Error 及自定义类。

---

如需更详细的某一部分说明或更多代码示例，请查阅[官方文档](https://cn.vuejs.org/guide/components/props.html#prop-validation)。 