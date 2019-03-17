### js 数组去重

1. 利用 ES6 Set 去重
   ```js
   function unique(arr) {
     return Array.from(new Set(arr))
   }
   ```
