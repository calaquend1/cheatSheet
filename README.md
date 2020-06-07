Функция, которая ищет все комбинации чисел, которые составляет искомую сумму.
```js 
function find_optimized(task) { 
  var results=[] 
  var new_tasks=[] 

  for(i in task.ar){ 
    if(task.sum==task.ar[i]){ // Нашли элемент = требуемой сумме? Это будет концом цепочки. 
      results.push([task.ar[i]]) 
    } else // Если поиск небесполезен - попробуем поискать, начиная с текущего элемента 
      if((task.ar.length-1>i)&&(task.sum>task.ar[i])) { 
        // Рекурсия с новой (меньшей) суммой и входным массивом с выколотым текущим элементом. 
        sub_array=find_optimized( {prev: task.ar[i], sum: task.sum-task.ar[i], ar: task.ar.slice(i*1+1)}); 
        // Складываем в тот же плоский массив результаты поиска 
      for(i in sub_array[1]){ 
        results.push([sub_array[0]].concat(sub_array[1][i])); 
      } 
    }
  } 
// Если не на вершине стека - вернём предыдущий элемент цепочки. Если что-то нашли - вернём ещё и массив результатов. 
  return ((results.length==0) ? task.prev:(task.prev==undefined ? results:[task.prev, results])); 
} 

console.log(find_optimized({sum: 26, ar: [7, 10, 2, 5, 3, 1]}))
```
Throttle function: не даёт запускать функцию чаще, чем нужно.
```js
function throttle(func, ms) { 
  var isThrottled = false, 
  savedArgs, 
  savedThis; 

  function wrapper() { 
    if (isThrottled) { 
      savedArgs = arguments; 
      savedThis = this; 
      return; 
    } 

    func.apply(this, arguments); 

    isThrottled = true; 

    setTimeout(function() { 
      isThrottled = false; 
      if (savedArgs) { 
        wrapper.apply(savedThis, savedArgs); 
        savedArgs = savedThis = null; 
      } 
    }, ms); 
    } 

  return wrapper; 
}
```

Regular Expression
```js
  res = '1?2&3/4/5'.split(/\?|&|[/]/);
  (5) ["1", "2", "3", "4", "5"]
```
Решето Аткина, простые числа

```js
function sieveOfAtkin(limit){
  var limitSqrt = Math.sqrt(limit);
  var sieve = [];
  var n;

//prime start from 2, and 3
  sieve[2] = true;
  sieve[3] = true;

  for (var x = 1; x <= limitSqrt; x++) {
    var xx = x*x;
    for (var y = 1; y <= limitSqrt; y++) {
      var yy = y*y;
      if (xx + yy >= limit) {
        break;
      }
// first quadratic using m = 12 and r in R1 = {r : 1, 5}
      n = (4 * xx) + (yy);
      if (n <= limit && (n % 12 == 1 || n % 12 == 5)) {
        sieve[n] = !sieve[n];
      }
// second quadratic using m = 12 and r in R2 = {r : 7}
      n = (3 * xx) + (yy);
      if (n <= limit && (n % 12 == 7)) {
        sieve[n] = !sieve[n];
      }
// third quadratic using m = 12 and r in R3 = {r : 11}
      n = (3 * xx) - (yy);
      if (x > y && n <= limit && (n % 12 == 11)) {
        sieve[n] = !sieve[n];
      }
    }
  }

// false each primes multiples
  for (n = 5; n <= limitSqrt; n++) {
  if (sieve[n]) {
    x = n * n;
    for (i = x; i <= limit; i += x) {
      sieve[i] = false;
    }
  }
}

//primes values are the one which sieve[x] = true
return sieve;
}

primes = sieveOfAtkin(5000);
```
Про Промисы (+статья в этом репозитории)
```js
function f1() {
  return Promise.resolve(12).then(() => {return 'f1'});
}
function f2() {
  return Promise.resolve(13).then(() => {return 'f2'});
}
function f3() {
  return Promise.resolve(14).then(() => {return 'f3'});
}
f = [f1(),f2(),f3()]
function executeSequentially(f) {
  var x = []
  var result = Promise.resolve();
  f.forEach(function (promiseFactory) {
    result = result.then(promiseFactory());
    x.push(result)
  });
console.log(x)
return result;
}

норм вариант

async function f1() {
  await Promise.resolve(12);
  return "f1";
}
async function f2() {
  await Promise.resolve(13);
  return "f2";
}
async function f3() {
  await Promise.resolve(14);
  return "f3";
}
f = [f1, f2, f3];
function executeSequentially(f) {
  var x = [];
  var result = Promise.resolve();
  f.forEach(function(promiseFactory) {
    result = result.then(promiseFactory);
    x.push(result);
  });
  return x;
}

executeSequentially(f).forEach(res => res.then(res2 => console.log(res2,'3')))
```

1. если есть return - в then будет значение функции
2. если return - то выполнение начинается после resolve
3. если function (e) {} или e => {} - то в функции будет значение, если оно передано return, иначе undefined
4. если нет return - then функции 3 начинает работать сразу же после функции 1
5. если .then(do1()) - то функция начинает работать одновременно с предыдущей и возвращает результат
6. если .then(do2) - то функция начинает работать после и получает результат

задача на замыкания
```js
function makeCounter(number) {
  let count = 0;

  return function() {
    count++
    return number - count + 1 > 0 ? 'yes' : 'no'; // есть доступ к внешней переменной "count"
  };
}

const count = makeCounter(3)

count() // "yes"
count() // "yes"
count() // "yes"

count() // "no"

```
выводит все правильные комбинации скобок
```js
function foo(s, l, r, pairs){
  if (l === pairs && r === pairs){
  console.log(s)
  } else {
    if (l<pairs){
      foo(s + '(', l + 1, r, pairs)
    }
    if (r < l){
      foo(s + ')', l, r + 1, pairs)
    }
  }
}


foo('', 0, 0, 3)
```
промисы
```js
Promise.resolve(1)
.then(x => x + 1) // возвращает 2
.then(x => { throw x }) // выкидывает 2 в следующий catch
.then(x => console.log(x)) // ничего не делает
.catch(err => console.log(err)) // выводит 2
.then(x => Promise.resolve(1)) // возвращает 1
.catch(err => console.log(err)) // ничего не выводит
.then(x => console.log(x)) // выводит 1
```
составляем из массива строку с промежутками и значенями внутри массива
```js
const first = [1, 4, 5, 2, 3, 9, 8, 11, 14, 0, 13, 40, -1]; // "0-5,8-9,11"
const second = [1, 4, 3, 2]; // "1-4"
const third = [1, 4]; // "1,4"

const d = array => {
  const arr = [...new Set(array)].sort((a, b) => a - b);
  let begin;
  let end;
  let diap = "";
  arr.forEach((item, id) => {
    if (id === 0) {
      begin = item; // записываем начало
      end = item; // записываем конец
      if (item !== arr[id + 1] - 1) {
        diap += `${item},`; // иначе записываем одно число
      }
    } else if (item === end + 1) {
      end = item; // продолжаем промежуток
      if (arr.length === id + 1) {
        if (begin !== end) {
          // если между началом и концом есть промежуток
          diap += `${begin}-${end},`;
        } else {
          diap += `${item}`; // иначе записываем одно число
        }
      }
    } else if (begin !== end) {
      // если между началом и концом есть промежуток
      diap += `${begin}-${end},`;
      if (arr.length === id + 1) {
        diap += `${item}`; // иначе записываем одно число
      } else if (item !== arr[id + 1] - 1) {
        diap += `${item},`;
      } // составляем промежуток
      begin = item;
      end = item;
    } else {
      if (arr.length === id + 1) {
        diap += `${item}`; // иначе записываем одно число
      }
      begin = item;
      end = item;
    }
  });
  return diap;
};
console.log(d(first));
console.log(d(second));
console.log(d(third));
можно ещё запятые убрать в конце
```
меняем местами ключи и значения в объекте
```js
"use strict";
function objectFlip(obj) {
  const ret = {};
  Object.keys(obj).forEach(key => {
    ret[obj[key]] = key;
  });
  return ret;
}

const x = {a: '', b: {}, c: () => {}, d: []}

console.log(objectFlip(x));
console.log(objectFlip(x)[''],' - обращаемся к пустой строке') // d
console.log(objectFlip(x)[{}],' - обращаемся к пустому объекту') // b
console.log(objectFlip(x)[() => {}],' - обращаемся к функции') // c
console.log(objectFlip(x)[[]],' - обращаемся к пустому массиву') // d, если убрать d: [], то обращение к пустому массиву вернёт a
```
Given an array where elements are sorted in ascending order,
convert it to a height balanced BST.
+search, is element in bst
```js
class TreeNode {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}
let myNode = new TreeNode(8);
console.log(myNode, "myNode"); //  { val: 8, left: null, right: null }

var sortedArrayToBST = function(nums) {
  //base cases
  if (nums.length === 1) return new TreeNode(nums[0]);
  if (nums.length === 0) return null;

  //create a new TreeNode(center)
  let centerIdx = Math.floor(nums.length / 2);
  let root = new TreeNode(nums[centerIdx]);

  //set left node to center of left subtree
  let leftSubtree = nums.slice(0, centerIdx);
  root.left = sortedArrayToBST(leftSubtree);

  //set right node to center of right subtree
  let rightSubtree = nums.slice(centerIdx + 1, nums.length);
  root.right = sortedArrayToBST(rightSubtree);

  return root;
};

const x = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11];

const search = (node, number) => {
  if (!node) return null;
  if (number !== 0 && !number) return null;
  if (node.val === number) return `${number} is here`;
  if (node.val > number) return search(node.left, number);
  if (node.val < number) return search(node.right, number);
};

console.log(sortedArrayToBST(x));
//T O(log n) S O(n) recursion stack space
console.log(search(sortedArrayToBST(x), 11));

```
```js
function sum(n) {
    let res = n;

    function sum1(n) {
        res += n;
        return sum1;
    }

    sum1.toString = sum1.valueOf = function () {
        return res;
    }

    return sum1;
}

const a = sum(1);
a(2);

console.log(0 + a); // 3

a(-3);
console.log(0 + a); // 0
```


