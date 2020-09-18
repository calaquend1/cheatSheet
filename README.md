
<details>
 <summary><b>1. Функция, которая ищет все комбинации чисел, которые составляет искомую сумму.</b></summary>
  
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
</details>

<details>
 <summary><b>2. Throttle function: не даёт запускать функцию чаще, чем нужно.</b></summary>

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
</details>

<details>
 <summary><b>3. regexp для разделения строк</b></summary>


```js
  res = '1?2&3/4/5'.split(/\?|&|[/]/);
  (5) ["1", "2", "3", "4", "5"]
```
</details>

<details>
 <summary><b>4. Реализация решета Аткина</b></summary>

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
</details>

<details>
 <summary><b>5. Промисы</b></summary>
  
```js

Promise.resolve(1)
.then(x => x + 1) // возвращает 2
.then(x => { throw x }) // выкидывает 2 в следующий catch
.then(x => console.log(x)) // ничего не делает
.catch(err => console.log(err)) // выводит 2
.then(x => Promise.resolve(1)) // возвращает 1
.catch(err => console.log(err)) // ничего не выводит
.then(x => console.log(x)) // выводит 1

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

</details>

<details>
 <summary><b>6. Замыкания и итераторы, реализация одной и той же задачи</b></summary>

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

function* countIterator(number) {
    for (let i = 0; i < number; i++) {
      yield 'yes'
    }
    while(true) {
      yield 'no'
    }
}

const count = countIterator(3)
count.next().value // "yes"
count.next().value // "yes"
count.next().value // "yes"
count.next().value // "no"
...
```
</details>

<details>
 <summary><b>7. Выводит все правильные комбинации скобок </b></summary>

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
</details>

<details>
 <summary><b>8. Составляем из массива строку с промежутками и значенями внутри массива</b></summary>

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
</details>

<details>
 <summary><b>9. Меняем местами ключи и значения в объекте</b></summary>

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
</details>

<details>
 <summary><b>10. Given an array where elements are sorted in ascending order, convert it to a height balanced BST. +search, is element in bst</b></summary>

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
</details>

<details>
 <summary><b>11. Какая-то задача со строками</b></summary>

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
</details>

<details>
 <summary><b>12. Использование генераторов для поиска в строке</b></summary>

```js
const UserRegex = new RegExp(/@(\w+)/, "g");

function* getUsernames(string) {
    let match = null;
    do {
        match = UserRegex.exec(string);
        if (match) {
            yield match;
        }
    } while (match);
}

const string = "this is a test with @swizec and @kyleshevlin, maybe @lukeed05"
for (const username of getUsernames(string)) {
  console.log(username)
}

```

</details>
<details>
 <summary><b>13. Использование генераторов для заполнения массива в приложении</b></summary>
 
 ```js
        function* repeatedArray(arr) {
          let index = 0;
          while (true) {
            yield arr[index++ % arr.length];
          }
        }
  
        const lifts = ['squat', 'bench', 'deadlift', 'press'];
        
        const nextLiftGenerator = repeatedArray(lifts);
        const numWeeks = 3;
        const daysPerWeek = 6;
        const totalNumSessions = numWeeks * daysPerWeek;
        
        // This creates an empty array of totalNumSessions length
        // for me to map over
        
        const cycle = [...Array(totalNumSessions)].map(() => ({
          lift: nextLiftGenerator.next().value,
        }));
 ```

</details>

<details>
 <summary><b>14. This example features an asynchronous implementation of the print function for simulating a human typing a phrase.</b></summary>
 
 ```js
function* type (string) {
  let index = 0;
  
  while (index < string.length) {
    yield string.slice(0, ++index);
  }
  
  return string;
}

const print = (render, text, timeout) => {
  render(text);
  
  return new Promise(resolve => {
    setTimeout(resolve, timeout);
  });
};

(async function () {
  for (const value of type("Hello, World!")) {
    await print(console.log, value, 250);
  }
})();
 ```

</details>

<details>
 <summary><b>15. Числа Фибоначчи через генераторы.</b></summary>
 
 ```js
function* fib (n) {
  const isInfinite = n === undefined;
  let current = 0;
  let next = 1;

  while (isInfinite || n--) {
    yield current;
    [current, next] = [next, current + next];
  }
}

 ```

</details>

<details>
 <summary><b>16. Ещё генераторы.</b></summary>
 
 ```js
const [...x] = (function* generator(n){ 
 let i = 0; 
 while(i<=n){ 
  yield i++
 } 
 return n
})(8);
```

```js
const a = [1, 2, 3];
const b = [4, 5, 6];

function* c () {
  yield 7;
  yield 8;
  yield 9;
}

function* gen () {
  yield* a;
  yield* b;
  yield* c();
  yield 10;
}

const [...sequence] = gen();
console.log(sequence); // [1,2,3,4,5,6,7,8,9,10]
```

```js
const sqrt = Math.sqrt;
const pow = Math.pow;

const fibCalc = n => Math.round(
  (1 / sqrt(5)) *
  (
    pow(((1 + sqrt(5)) / 2), n) -
    pow(((1 - sqrt(5)) / 2), n)
  )
);

function* fib (n) {
  const isInfinite = n === undefined;
  let current = 0;

  while (isInfinite || n--) {
    yield fibCalc(current);
    current++;
  }
}
 ```
 ```js
 const countToThree = {
  a: 1,
  b: 2,
  c: 3
};

countToThree[Symbol.iterator] = function* () {
  const keys = Object.keys(this);
  const length = keys.length;

  for (const key in this) {
    yield this[key];
  }
};

let [...three] = countToThree;
console.log(three); // [ 1, 2, 3 ]
 ```
 </details>
 <details>
 <summary><b>17. Алгоритм Дейкстры.</b></summary>
 
 ```js
  let graph = {
	start: { A: 5, B: 2 },
	A: { start: 1, C: 4, D: 2 },
	B: { A: 8, D: 7 },
	C: { D: 6, finish: 3 },
	D: { finish: 1 },
	finish: {},
};

let shortestDistanceNode = (distances, visited) => {
  // create a default value for shortest
	let shortest = null;
	
  	// for each node in the distances object
	for (let node in distances) {
    	// if no node has been assigned to shortest yet
  		// or if the current node's distance is smaller than the current shortest
		let currentIsShortest =
			shortest === null || distances[node] < distances[shortest];
        	
	  	// and if the current node is in the unvisited set
		if (currentIsShortest && !visited.includes(node)) {
            // update shortest to be the current node
			shortest = node;
		}
	}
	return shortest;
};

let findShortestPath = (graph, startNode, endNode) => {
 
 // track distances from the start node using a hash object
   let distances = {};
 distances[endNode] = "Infinity";
 distances = Object.assign(distances, graph[startNode]);
// track paths using a hash object
 let parents = { endNode: null };
 for (let child in graph[startNode]) {
  parents[child] = startNode;
 }
  
 // collect visited nodes
   let visited = [];
// find the nearest node
   let node = shortestDistanceNode(distances, visited);
 
 // for that node:
 while (node) {
 // find its distance from the start node & its child nodes
  let distance = distances[node];
  let children = graph[node]; 
      
 // for each of those child nodes:
      for (let child in children) {
  
  // make sure each child node is not the start node
        if (String(child) === String(startNode)) {
          continue;
       } else {
          // save the distance from the start node to the child node
          let newdistance = distance + children[child];
// if there's no recorded distance from the start node to the child node in the distances object
// or if the recorded distance is shorter than the previously stored distance from the start node to the child node
          if (!distances[child] || distances[child] > newdistance) {
// save the distance to the object
     distances[child] = newdistance;
// record the path
     parents[child] = node;
    } 
         }
       }  
      // move the current node to the visited set
      visited.push(node);
// move to the nearest neighbor node
      node = shortestDistanceNode(distances, visited);
    }
  
 // using the stored paths from start node to end node
 // record the shortest path
 let shortestPath = [endNode];
 let parent = parents[endNode];
 while (parent) {
  shortestPath.push(parent);
  parent = parents[parent];
 }
 shortestPath.reverse();
  
 //this is the shortest path
 let results = {
  distance: distances[endNode],
  path: shortestPath,
 };
 // return the shortest path & the end node's distance from the start node
   return results;
};

console.log(findShortestPath(graph, "start", "end"));

console.log(findShortestPath(graph, "A", "B"));

console.log(findShortestPath(graph, "A", "start"));

```

</details>
