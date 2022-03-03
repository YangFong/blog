ES6 在当前来说，是属于一种**基本功**，因为这是语言自己扩展，而不是一种需要导入的方案。

## 好处是什么

简单的话语来说：**用更少的代码做更多的事**。比如数组去重：

```js
// 传统
function remove(iArr) {
  var map = {};
  var res = [];
  for (var i = 0; i < iArr.length; i++) {
    var num = iArr[i];
    if (!map[num]) {
      res.push(num);
      map[num] = true;
    }
  }
  return res;
}
var arr = [1, 1, 2, 2, 3, 3, 4];

// [1, 2, 3, 4]
console.log(remove(arr));
```

```js
// ES6
let arr = [1, 1, 2, 2, 3, 3, 4];
// [1, 2, 3, 4]
console.log([...new Set(arr)]);
```

其他方面的增强：

- 消除变量提升（`let` 与 `const`）
- 函数式编程（箭头函数、默认参数与收集参数）
- 成熟的异步解决方案（`Promise`）
- 新增数据结构（`Set` 与 `Map`）
- 面向对象（启用 `class`）
- ...

## 学习

- [尚硅谷 Web 前端 ES6 教程，涵盖 ES6-ES11](https://www.bilibili.com/video/BV1uK411H7on?from=search&seid=12482990126117127727&spm_id_from=333.337.0.0)（视频）
- [ES6 入门教程](https://es6.ruanyifeng.com/)（文档）
