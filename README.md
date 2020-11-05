
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

<details>
 <summary><b>18. Поиск в ширину в дереве.</b></summary>
	
```js
let tree = {
	"10": {
		value: "10",
		left: "4",
		right: "17",
	},
	"4": {
		value: "4",
		left: "1",
		right: "9",
	},
	"17": {
		value: "17",
		left: "12",
		right: "18",
	},
	"1": {
		value: "1",
		left: null,
		right: null,
	},
	"9": {
		value: "9",
		left: null,
		right: null,
	},
	"12": {
		value: "12",
		left: null,
		right: null,
	},
	"18": {
		value: "18",
		left: null,
		right: null,
	},
};

let BreadthFirstSearch = (tree, rootNode, searchValue) => {
	// make a queue array
	let queue = [];
	// populate it with the node that will be the root of your search
	queue.push(rootNode);

	// search the queue until it is empty
	while (queue.length > 0) {
	// assign the top of the queue to variable currentNode
		let currentNode = queue[0];
		console.log("Current node is:" + currentNode.value);

		// if currentNode is the node we're searching for, break & alert
		if (currentNode.value === searchValue) {
			console.log("Found it!");
			return;
		}

		// if currentNode has a left child node, add it to the queue.
		if (currentNode.left !== null) {
			queue.push(tree[currentNode.left]);
		}

		// if currentNode has a right child node, add it to the queue.
		if (currentNode.right !== null) {
			queue.push(tree[currentNode.right]);
		}
		// remove the currentNode from the queue.
		queue.shift();	
    }
	console.log("Sorry, no such node found :(");	
}

BreadthFirstSearch(tree, tree[10], "12");
BreadthFirstSearch(tree, tree[10], "42");
BreadthFirstSearch(tree, tree[17], "18");

let alternateBreadthFirstSearch = (tree, rootNode, searchValue) => {
	// make a queue array
	let queue = [];
  	let path = [];
	// populate it with the node that will be the root of your search
	queue.push(rootNode);

	// search the queue until it is empty
	while (queue.length > 0) {
		// assign the top of the queue to variable currentNode
		let currentNode = queue[0];
		path.push(currentNode.value);

		// if currentNode is the node we're searching for, break & alert
		if (currentNode.value === searchValue) {
      console.log('we found a node =', searchValue)
			return path;
		}

		// if currentNode has a left child node, add it to the queue.
		if (currentNode.left !== null) {
			queue.push(tree[currentNode.left]);
		}

		// if currentNode has a right child node, add it to the queue.
		if (currentNode.right !== null) {
			queue.push(tree[currentNode.right]);
		}
		// remove the currentNode from the queue.
		queue.shift();
	}
	console.log("Sorry, no such node found :(");
};
console.log('alternate')
alternateBreadthFirstSearch(tree, tree[10], "12")
alternateBreadthFirstSearch(tree, tree[10], "42")
alternateBreadthFirstSearch(tree, tree[17], "18")

```
</details>

<details>
	<summary><b>19. Typescript features.</b></summary>
	
```js

1.
interface IDog{
   name:  string;
   age: number;
   kidFriendly: boolean;
}

interface ICat{
   name: string;
   age: number;
   activityLevel: number;
}

type Animal = IDog | ICat;

/** Is the animal a dog ? */
const isDog = (animal: Animal) : animal is IDog => (animal as IDog).kidFriendly !== undefined;


const kitty: ICat = {
    name:  'string',
    age: 2,
    activityLevel: 4
}

const woof: IDog = {
    name:  'string',
    age: 2,
    kidFriendly: true
}
if(isDog(kitty)){
   console.log(kitty.kidFriendly);
}
if(isDog(woof)){
   console.log(woof.kidFriendly);
}
console.log(isDog(kitty), isDog(woof))

2. 
interface IDog{
   name: string;
   age: number;
   kidFriendly: boolean;
}

const dog : Partial<IDog> = {
   name: "Rex"
}

3.
const walkDog = (dogName: string, distance: number) => { /** ... */ }

const params: Parameters<typeof walkDog> = ["Rex", 48]; // you can't do [3, '24']

4.
class Dog{
   private _name: string = "";

   get name(): string{
      return this._name;
   }

   /** Check the length of the name before setting it **/
   set name(newName: string){
      if(newName.length < 8) {
         throw new Error(`The dog's name needs at least 8 charachters`)
      }

      this._name = newName;
   }
}

```
</details>

<details>
	<summary><b>20. abc.curry, задача на собеседование.</b></summary>
	
```js

function abc(a, b, c) {
  return a + b + c;
}

function abcdef(a, b, c, d, e, f) {
  return a + b + c + d + e + f;
}

abc.curry = abcdef.curry = function abc(...args) {
    let res = [...args].join('');

    function curry(...args) {
        res += [...args].join('');
        return curry;
    }

    curry.toString = curry.valueOf = function () {
        return res;
    }
    return curry;
}


console.log(abc.curry('A','B','C'))
console.log(abc.curry('A','B','C')('D'))
console.log(abc.curry('A')('B')('C')); // 'ABC'
console.log(abc.curry('A', 'B')('C')); // 'ABC'
console.log(abc.curry('A', 'B', 'C')); // 'ABC'

console.log(abcdef.curry('A')('B')('C')('D')('E')('F')); // 'ABCDEF'
console.log(abcdef.curry('A', 'B', 'C')('D', 'E', 'F')); // 'ABCDEF'
```

</details>

<details>
	<summary><b>21. Мемоизация</b></summary>
	
```js
	
function memoize(fn) {
  let isCalculated = false;
  let lastResult;
  return function memoizedFn() { // Return the generated function!
    if (isCalculated) {
      return lastResult;
    }
    let result = fn();
    lastResult = result;
    isCalculated = true;
    return result;
  }
}

```
</details>

<details>
	<summary><b>22. Шаблон</b></summary>
	
```js
	
```
</details>

<details>
	<summary><b>23. Euclidean Algorithm of finding greatest common divisor (GCD)</b></summary>
	
```js
/**
 * Recursive version of Euclidean Algorithm of finding greatest common divisor (GCD).
 * @param {number} originalA
 * @param {number} originalB
 * @return {number}
 */
export default function euclideanAlgorithm(originalA, originalB) {
  // Make input numbers positive.
  const a = Math.abs(originalA);
  const b = Math.abs(originalB);

  // To make algorithm work faster instead of subtracting one number from the other
  // we may use modulo operation.
  return (b === 0) ? a : euclideanAlgorithm(b, a % b);
}
```
</details>

<details>
	<summary><b>24. Pascal's triangle</b></summary>
	
```js
	/**
 * @param {number} lineNumber - zero based.
 * @return {number[]}
 */
export default function pascalTriangle(lineNumber) {
  const currentLine = [1];

  const currentLineSize = lineNumber + 1;

  for (let numIndex = 1; numIndex < currentLineSize; numIndex += 1) {
    // See explanation of this formula in README.
    currentLine[numIndex] = (currentLine[numIndex - 1] * (lineNumber - numIndex + 1)) / numIndex;
  }

  return currentLine;
}
```
</details>

<details>
	<summary><b>25. is power of two (bitwise)</b></summary>
	
```js
	  
/**
 * @param {number} number
 * @return {boolean}
 */
export default function isPowerOfTwoBitwise(number) {
  // 1 (2^0) is the smallest power of two.
  if (number < 1) {
    return false;
  }

  /*
   * Powers of two in binary look like this:
   * 1: 0001
   * 2: 0010
   * 4: 0100
   * 8: 1000
   *
   * Note that there is always exactly 1 bit set. The only exception is with a signed integer.
   * e.g. An 8-bit signed integer with a value of -128 looks like:
   * 10000000
   *
   * So after checking that the number is greater than zero, we can use a clever little bit
   * hack to test that one and only one bit is set.
   */
  return (number & (number - 1)) === 0;
}
```
</details>

<details>
	<summary><b>26. Количество индексов строк, в которых элементы разные</b></summary>
	
```js
	/**
 * @param {string} a
 * @param {string} b
 * @return {number}
 */
export default function hammingDistance(a, b) {
  if (a.length !== b.length) {
    throw new Error('Strings must be of the same length');
  }

  let distance = 0;

  for (let i = 0; i < a.length; i += 1) {
    if (a[i] !== b[i]) {
      distance += 1;
    }
  }

  return distance;
}
```
</details>

<details>
	<summary><b>27. Power set</b></summary>
	
```js
/**
 * @param {*[]} originalSet - Original set of elements we're forming power-set of.
 * @param {*[][]} allSubsets - All subsets that have been formed so far.
 * @param {*[]} currentSubSet - Current subset that we're forming at the moment.
 * @param {number} startAt - The position of in original set we're starting to form current subset.
 * @return {*[][]} - All subsets of original set.
 */
function btPowerSetRecursive(originalSet, allSubsets = [[]], currentSubSet = [], startAt = 0) {
  // Let's iterate over originalSet elements that may be added to the subset
  // without having duplicates. The value of startAt prevents adding the duplicates.
  for (let position = startAt; position < originalSet.length; position += 1) {
    // Let's push current element to the subset
    currentSubSet.push(originalSet[position]);

    // Current subset is already valid so let's memorize it.
    // We do array destruction here to save the clone of the currentSubSet.
    // We need to save a clone since the original currentSubSet is going to be
    // mutated in further recursive calls.
    allSubsets.push([...currentSubSet]);

    // Let's try to generate all other subsets for the current subset.
    // We're increasing the position by one to avoid duplicates in subset.
    btPowerSetRecursive(originalSet, allSubsets, currentSubSet, position + 1);

    // BACKTRACK. Exclude last element from the subset and try the next valid one.
    currentSubSet.pop();
  }

  // Return all subsets of a set.
  return allSubsets;
}

/**
 * Find power-set of a set using BACKTRACKING approach.
 *
 * @param {*[]} originalSet
 * @return {*[][]}
 */
export default function btPowerSet(originalSet) {
  return btPowerSetRecursive(originalSet);
}	
```
</details>

<details>
	<summary><b>28. Integer partition</b></summary>
	
```js
	/**
 * @param {number} number
 * @return {number}
 */
export default function integerPartition(number) {
  // Create partition matrix for solving this task using Dynamic Programming.
  const partitionMatrix = Array(number + 1).fill(null).map(() => {
    return Array(number + 1).fill(null);
  });

  // Fill partition matrix with initial values.

  // Let's fill the first row that represents how many ways we would have
  // to combine the numbers 1, 2, 3, ..., n with number 0. We would have zero
  // ways obviously since with zero number we may form only zero.
  for (let numberIndex = 1; numberIndex <= number; numberIndex += 1) {
    partitionMatrix[0][numberIndex] = 0;
  }

  // Let's fill the first column. It represents the number of ways we can form
  // number zero out of numbers 0, 0 and 1, 0 and 1 and 2, 0 and 1 and 2 and 3, ...
  // Obviously there is only one way we could form number 0
  // and it is with number 0 itself.
  for (let summandIndex = 0; summandIndex <= number; summandIndex += 1) {
    partitionMatrix[summandIndex][0] = 1;
  }

  // Now let's go through other possible options of how we could form number m out of
  // summands 0, 1, ..., m using Dynamic Programming approach.
  for (let summandIndex = 1; summandIndex <= number; summandIndex += 1) {
    for (let numberIndex = 1; numberIndex <= number; numberIndex += 1) {
      if (summandIndex > numberIndex) {
        // If summand number is bigger then current number itself then just it won't add
        // any new ways of forming the number. Thus we may just copy the number from row above.
        partitionMatrix[summandIndex][numberIndex] = partitionMatrix[summandIndex - 1][numberIndex];
      } else {
        /*
         * The number of combinations would equal to number of combinations of forming the same
         * number but WITHOUT current summand number PLUS number of combinations of forming the
         * <current number - current summand> number but WITH current summand.
         *
         * Example:
         * Number of ways to form 5 using summands {0, 1, 2} would equal the SUM of:
         * - number of ways to form 5 using summands {0, 1} (we've excluded summand 2)
         * - number of ways to form 3 (because 5 - 2 = 3) using summands {0, 1, 2}
         * (we've included summand 2)
        */
        const combosWithoutSummand = partitionMatrix[summandIndex - 1][numberIndex];
        const combosWithSummand = partitionMatrix[summandIndex][numberIndex - summandIndex];

        partitionMatrix[summandIndex][numberIndex] = combosWithoutSummand + combosWithSummand;
      }
    }
  }

  return partitionMatrix[number][number];
}
```
</details>

<details>
	<summary><b>29. combination Sum</b></summary>
	
```js
	  
/**
 * @param {number[]} candidates - candidate numbers we're picking from.
 * @param {number} remainingSum - remaining sum after adding candidates to currentCombination.
 * @param {number[][]} finalCombinations - resulting list of combinations.
 * @param {number[]} currentCombination - currently explored candidates.
 * @param {number} startFrom - index of the candidate to start further exploration from.
 * @return {number[][]}
 */
function combinationSumRecursive(
  candidates,
  remainingSum,
  finalCombinations = [],
  currentCombination = [],
  startFrom = 0,
) {
  if (remainingSum < 0) {
    // By adding another candidate we've gone below zero.
    // This would mean that the last candidate was not acceptable.
    return finalCombinations;
  }

  if (remainingSum === 0) {
    // If after adding the previous candidate our remaining sum
    // became zero - we need to save the current combination since it is one
    // of the answers we're looking for.
    finalCombinations.push(currentCombination.slice());

    return finalCombinations;
  }

  // If we haven't reached zero yet let's continue to add all
  // possible candidates that are left.
  for (let candidateIndex = startFrom; candidateIndex < candidates.length; candidateIndex += 1) {
    const currentCandidate = candidates[candidateIndex];

    // Let's try to add another candidate.
    currentCombination.push(currentCandidate);

    // Explore further option with current candidate being added.
    combinationSumRecursive(
      candidates,
      remainingSum - currentCandidate,
      finalCombinations,
      currentCombination,
      candidateIndex,
    );

    // BACKTRACKING.
    // Let's get back, exclude current candidate and try another ones later.
    currentCombination.pop();
  }

  return finalCombinations;
}

/**
 * Backtracking algorithm of finding all possible combination for specific sum.
 *
 * @param {number[]} candidates
 * @param {number} target
 * @return {number[][]}
 */
export default function combinationSum(candidates, target) {
  return combinationSumRecursive(candidates, target);
}
```
</details>
<details>
	<summary><b>30. html progress bar</b></summary>
	
```js
	<progress max="100" value="66"></progress>
```
</details>
<details>
	<summary><b>31. reverse traversal</b></summary>
	
```js
	**
 * Traversal callback function.
 * @callback traversalCallback
 * @param {*} nodeValue
 */

/**
 * @param {LinkedListNode} node
 * @param {traversalCallback} callback
 */
function reverseTraversalRecursive(node, callback) {
  if (node) {
    reverseTraversalRecursive(node.next, callback);
    callback(node.value);
  }
}

/**
 * @param {LinkedList} linkedList
 * @param {traversalCallback} callback
 */
export default function reverseTraversal(linkedList, callback) {
  reverseTraversalRecursive(linkedList.head, callback);
}
```
</details>
<details>
	<summary><b>32. Проход конём всей доски</b></summary>
	
```js
	/**
 * @param {number[][]} chessboard
 * @param {number[]} position
 * @return {number[][]}
 */
function getPossibleMoves(chessboard, position) {
  // Generate all knight moves (even those that go beyond the board).
  const possibleMoves = [
    [position[0] - 1, position[1] - 2],
    [position[0] - 2, position[1] - 1],
    [position[0] + 1, position[1] - 2],
    [position[0] + 2, position[1] - 1],
    [position[0] - 2, position[1] + 1],
    [position[0] - 1, position[1] + 2],
    [position[0] + 1, position[1] + 2],
    [position[0] + 2, position[1] + 1],
  ];

  // Filter out all moves that go beyond the board.
  return possibleMoves.filter((move) => {
    const boardSize = chessboard.length;
    return move[0] >= 0 && move[1] >= 0 && move[0] < boardSize && move[1] < boardSize;
  });
}

/**
 * @param {number[][]} chessboard
 * @param {number[]} move
 * @return {boolean}
 */
function isMoveAllowed(chessboard, move) {
  return chessboard[move[0]][move[1]] !== 1;
}

/**
 * @param {number[][]} chessboard
 * @param {number[][]} moves
 * @return {boolean}
 */
function isBoardCompletelyVisited(chessboard, moves) {
  const totalPossibleMovesCount = chessboard.length ** 2;
  const existingMovesCount = moves.length;

  return totalPossibleMovesCount === existingMovesCount;
}

/**
 * @param {number[][]} chessboard
 * @param {number[][]} moves
 * @return {boolean}
 */
function knightTourRecursive(chessboard, moves) {
  const currentChessboard = chessboard;

  // If board has been completely visited then we've found a solution.
  if (isBoardCompletelyVisited(currentChessboard, moves)) {
    return true;
  }

  // Get next possible knight moves.
  const lastMove = moves[moves.length - 1];
  const possibleMoves = getPossibleMoves(currentChessboard, lastMove);

  // Try to do next possible moves.
  for (let moveIndex = 0; moveIndex < possibleMoves.length; moveIndex += 1) {
    const currentMove = possibleMoves[moveIndex];

    // Check if current move is allowed. We aren't allowed to go to
    // the same cells twice.
    if (isMoveAllowed(currentChessboard, currentMove)) {
      // Actually do the move.
      moves.push(currentMove);
      currentChessboard[currentMove[0]][currentMove[1]] = 1;

      // If further moves starting from current are successful then
      // return true meaning the solution is found.
      if (knightTourRecursive(currentChessboard, moves)) {
        return true;
      }

      // BACKTRACKING.
      // If current move was unsuccessful then step back and try to do another move.
      moves.pop();
      currentChessboard[currentMove[0]][currentMove[1]] = 0;
    }
  }

  // Return false if we haven't found solution.
  return false;
}

/**
 * @param {number} chessboardSize
 * @return {number[][]}
 */
export default function knightTour(chessboardSize) {
  // Init chessboard.
  const chessboard = Array(chessboardSize).fill(null).map(() => Array(chessboardSize).fill(0));

  // Init moves array.
  const moves = [];

  // Do first move and place the knight to the 0x0 cell.
  const firstMove = [0, 0];
  moves.push(firstMove);
  chessboard[firstMove[0]][firstMove[0]] = 1;

  // Recursively try to do the next move.
  const solutionWasFound = knightTourRecursive(chessboard, moves);

  return solutionWasFound ? moves : [];
}
```
</details>
<details>
	<summary><b>33. rain terraces</b></summary>
	
```js
	/**
 * DYNAMIC PROGRAMMING approach of solving Trapping Rain Water problem.
 *
 * @param {number[]} terraces
 * @return {number}
 */
export default function dpRainTerraces(terraces) {
  let waterAmount = 0;

  // Init arrays that will keep the list of left and right maximum levels for specific positions.
  const leftMaxLevels = new Array(terraces.length).fill(0);
  const rightMaxLevels = new Array(terraces.length).fill(0);

  // Calculate the highest terrace level from the LEFT relative to the current terrace.
  [leftMaxLevels[0]] = terraces;
  for (let terraceIndex = 1; terraceIndex < terraces.length; terraceIndex += 1) {
    leftMaxLevels[terraceIndex] = Math.max(
      terraces[terraceIndex],
      leftMaxLevels[terraceIndex - 1],
    );
  }

  // Calculate the highest terrace level from the RIGHT relative to the current terrace.
  rightMaxLevels[terraces.length - 1] = terraces[terraces.length - 1];
  for (let terraceIndex = terraces.length - 2; terraceIndex >= 0; terraceIndex -= 1) {
    rightMaxLevels[terraceIndex] = Math.max(
      terraces[terraceIndex],
      rightMaxLevels[terraceIndex + 1],
    );
  }

  // Not let's go through all terraces one by one and calculate how much water
  // each terrace may accumulate based on previously calculated values.
  for (let terraceIndex = 0; terraceIndex < terraces.length; terraceIndex += 1) {
    // Pick the lowest from the left/right highest terraces.
    const currentTerraceBoundary = Math.min(
      leftMaxLevels[terraceIndex],
      rightMaxLevels[terraceIndex],
    );

    if (currentTerraceBoundary > terraces[terraceIndex]) {
      waterAmount += currentTerraceBoundary - terraces[terraceIndex];
    }
  }

  return waterAmount;
}
```
</details>
<details>
	<summary><b>34. longestCommonSubstring</b></summary>
	
```js
	/**
 * @param {string} string1
 * @param {string} string2
 * @return {string}
 */
export default function longestCommonSubstring(string1, string2) {
  // Convert strings to arrays to treat unicode symbols length correctly.
  // For example:
  // '𐌵'.length === 2
  // [...'𐌵'].length === 1
  const s1 = [...string1];
  const s2 = [...string2];

  // Init the matrix of all substring lengths to use Dynamic Programming approach.
  const substringMatrix = Array(s2.length + 1).fill(null).map(() => {
    return Array(s1.length + 1).fill(null);
  });

  // Fill the first row and first column with zeros to provide initial values.
  for (let columnIndex = 0; columnIndex <= s1.length; columnIndex += 1) {
    substringMatrix[0][columnIndex] = 0;
  }

  for (let rowIndex = 0; rowIndex <= s2.length; rowIndex += 1) {
    substringMatrix[rowIndex][0] = 0;
  }

  // Build the matrix of all substring lengths to use Dynamic Programming approach.
  let longestSubstringLength = 0;
  let longestSubstringColumn = 0;
  let longestSubstringRow = 0;

  for (let rowIndex = 1; rowIndex <= s2.length; rowIndex += 1) {
    for (let columnIndex = 1; columnIndex <= s1.length; columnIndex += 1) {
      if (s1[columnIndex - 1] === s2[rowIndex - 1]) {
        substringMatrix[rowIndex][columnIndex] = substringMatrix[rowIndex - 1][columnIndex - 1] + 1;
      } else {
        substringMatrix[rowIndex][columnIndex] = 0;
      }

      // Try to find the biggest length of all common substring lengths
      // and to memorize its last character position (indices)
      if (substringMatrix[rowIndex][columnIndex] > longestSubstringLength) {
        longestSubstringLength = substringMatrix[rowIndex][columnIndex];
        longestSubstringColumn = columnIndex;
        longestSubstringRow = rowIndex;
      }
    }
  }

  if (longestSubstringLength === 0) {
    // Longest common substring has not been found.
    return '';
  }

  // Detect the longest substring from the matrix.
  let longestSubstring = '';

  while (substringMatrix[longestSubstringRow][longestSubstringColumn] > 0) {
    longestSubstring = s1[longestSubstringColumn - 1] + longestSubstring;
    longestSubstringRow -= 1;
    longestSubstringColumn -= 1;
  }

  return longestSubstring;
}
```
</details>
<details>
	<summary><b>35. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>36. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>37. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>38. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>39. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>40. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>41. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>42. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>43. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>44. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>45. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>46. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>47. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>48. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>49. Шаблон</b></summary>
	
```js
	
```
</details>
<details>
	<summary><b>50. Шаблон</b></summary>
	
```js
	
```
</details>
