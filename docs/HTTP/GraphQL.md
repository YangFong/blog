有没有想过，一个端口解决所有请求（不论增删改查），早期时我做过不少努力（不为别的，就是懒得写那么多），对于参数简单的请求可以做到这种统一。

:::note
依照事实来说，这种思考会延缓开发速度，不过不可否认对自我的提升，从而增加以后的开发速度，甚至说发现新世界。
:::

```ts
/**
 * ### 返回下拉框形式的数据，用于下拉框消费
 * @param {string} tableName 表名称
 */
export const options = (tableName: string) => {
  return sql.query(`
    SELECT 
      name AS label,
      ${tableName}_id AS value 
    FROM 
      ${tableName}
  `);
};
```

而后，发现当前存在一种事物，可以做到想要什么就要什么（只要存在），便是 **GraphQL**

```ts
/**
 * 生成下拉框选项
 *
 * @param {SelectTableName} selectTableName 数据表名称
 */
export default function useSelect(selectTableName: SelectTableName) {
  const [options, setOptions] = useState<Option[]>([]);
  useEffect(() => {
    post(`{
      select (tableName: "${selectTableName}") {
        label
        value
      }
    }`).then(setOptions);
  }, []);
  return options;
}
```

返回的模拟数据

```json
{
  "data": [
    {
      "label": "a",
      "value": "1"
    },
    {
      "label": "b",
      "value": "2"
    },
    {
      "label": "c",
      "value": "3"
    }
  ]
}
```

若是不要 `label`，可以在请求就定好

```js
post(`
    {
      select (tableName: "user") {
        value
      }
    }
`);
```

```json
{
  "data": [
    {
      "value": "1"
    },
    {
      "value": "2"
    },
    {
      "value": "3"
    }
  ]
}
```

---

更多的说法交给官方吧

- [GraphQL](https://graphql.cn/)
