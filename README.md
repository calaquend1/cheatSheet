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
```

1. если есть return - в then будет значение функции
2. если return - то выполнение начинается после resolve
3. если function (e) {} или e => {} - то в функции будет значение, если оно передано return, иначе undefined
4. если нет return - then функции 3 начинает работать сразу же после функции 1
5. если .then(do1()) - то функция начинает работать одновременно с предыдущей и возвращает результат
6. если .then(do2) - то функция начинает работать после и получает результат


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
## Available Scripts

In the project directory, you can run:

### `npm start`




**Note: this is a one-way operation. Once you `eject`, you can’t go back!**



