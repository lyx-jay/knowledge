作用：将一部分类型变为可选
```ts
type Pages = {
	title: string,
	date: string,
	author: string
}

type NEW_Pages = Optional<Pages, 'data' | 'author'>
```

```ts
type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>
```
