1. ref 是针对原始类型的值。包括，Number、String、Symbol、Boolean等
2. 需要使用 `.value` 的方式访问响应式的值

### reactive
1. reactive 是针对非原始类型的值，例如数组、对象。这是因为 proxy 仅对非原始类型的值生效
2. 使用 reacitve 创建的响应式对象在 script 中无需使用 `.value` 的方式访问

- [ ] 源码学习