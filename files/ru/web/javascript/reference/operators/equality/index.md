---
title: Нестрогое равенство (==)
slug: Web/JavaScript/Reference/Operators/Equality
l10n:
  sourceCommit: f616cb604af851f77f8cd59368e94ee3e43a8838
---

{{jsSidebar("Operators")}}

Оператор **нестрогого равенства (`==`)** проверяет, являются ли два его операнда равными, возвращая логическое значение.
В отличие от оператора [строгого равенства](/ru/docs/Web/JavaScript/Reference/Operators/Strict_equality) пытается привести и сравнить операнды разных типов.

{{InteractiveExample("JavaScript Demo: Expressions - Equality operator")}}

```js interactive-example
console.log(1 == 1);
// Expected output: true

console.log("hello" == "hello");
// Expected output: true

console.log("1" == 1);
// Expected output: true

console.log(0 == false);
// Expected output: true
```

## Синтаксис

```js-nolint
x == y
```

## Описание

Операторы нестрогого равенства (`==` и `!=`) используют алгоритм [IsLooselyEqual](https://tc39.es/ecma262/#sec-islooselyequal). Его можно описать приблизительно так:

1. Если операнды имеют одинаковый тип, они сравниваются следующим образом:
   - Объект: возвращается `true` только если оба операнда ссылаются на один и тот же объект.
   - Строка: возвращается `true` только если оба операнда содержат одинаковые символы в одинаковом порядке и регистре.
   - Число: возвращается `true` только если оба операнда имеют идентичные значения. `+0` и `-0` считаются равными между собой. Если один из операндов является `NaN`, возвращается `false`. Таким образом, `NaN` никогда не равен `NaN`.
   - Логическое значение: возвращается `true` только если оба операнда `true` или оба `false`.
   - BigInt: возвращается `true` только если оба операнда имеют идентичные значения.
   - Символ: возвращается `true` только если оба операнда ссылаются на один и тот же символ.
2. Если один из операндов является `null` или `undefined`, другой также должен быть `null` или `undefined`, чтобы возвращалось `true`. Иначе возвращается `false`.
3. Если один из операндов является объектом, а другой примитивом, то [объект приводится к примитиву](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-toprimitive).
4. На этом шаге оба операнда приведены к одному типу (строка, число, логическое значение, символ или BigInt). В некоторых случаях могут производиться дополнительные преобразования.
   - Если оба операнда одинакового типа, то они сравниваются, используя шаг 1.
   - Если один из операндов является символьным значением, а другой — нет, то возвращается `false`.
   - Если один из операндов является логическим значением, а другой — нет, то [логическое значение приводится к числу](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-tonumber): `true` преобразуется в 1, а `false` в 0. Затем снова происходит сравнение.
   - Число и строка: [строка приводится к числу](/ru/docs/Web/JavaScript/Reference/Global_Objects/Number#описание). Неудачная конвертация приводит к результату `NaN`, что гарантирует возвращение `false`.
   - Число и BigInt: сравниваются их числовые значения. Если числом является ±Infinity или `NaN`, возвращается `false`.
   - Строка и BigInt: строка приводится к BigInt с использованием того же самого алгоритма, что и в конструкторе [`BigInt()`](/ru/docs/Web/JavaScript/Reference/Global_Objects/BigInt/BigInt). При неудачной конвертации возвращается `false`.

Нестрогое равенство _симметрично_: `A == B` всегда идентично `B == A` для любых значений `A` и `B` (кроме порядка применённых преобразований).

Основная разница между этим оператором и оператором [строгого равенства](/ru/docs/Web/JavaScript/Reference/Operators/Strict_equality) (`===`) в том, что оператор строгого равенства не пытается привести операнды к одному типу. Вместо этого он всегда считает операнды разных типов не равными. Оператор строгого равенства, по сути, выполняет только шаг 1 и возвращает `false` для всех остальных случаев.

Существует «умышленное нарушение» вышеприведённого алгоритма: если одним из операндов является [`document.all`](/ru/docs/Web/API/Document/all), он считается равным `undefined`. Это означает, что `document.all == null` равно `true`, но `document.all === undefined && document.all === null` равно `false`.

## Примеры

### Сравнение без приведения типов

```js
1 == 1; // true
"hello" == "hello"; // true
```

### Сравнение с приведением типов

```js
"1" == 1; // true
1 == "1"; // true
0 == false; // true
0 == null; // false
0 == undefined; // false
0 == !!null; // true, обратите внимание на двойное логическое отрицание
0 == !!undefined; // true, обратите внимание на двойное логическое отрицание
null == undefined; // true

const number1 = new Number(3);
const number2 = new Number(3);
number1 == 3; // true
number1 == number2; // false
```

### Сравнение объектов

```js
const object1 = {
  key: "value",
};

const object2 = {
  key: "value",
};

console.log(object1 == object2); // false
console.log(object1 == object1); // true
```

### Сравнение строк и строковых объектов

Обратите внимание, что строки, созданные при помощи `new String()` являются объектами. Если сравнивать такой объект со строковым литералом, то он будет приведён к строковому литералу и сравнение будет произведено по содержимому. Однако, если оба операнда являются объектами типа `String`, то они сравниваются как объекты и сравнение будет произведено по ссылке:

```js
const string1 = "hello";
const string2 = String("hello");
const string3 = new String("hello");
const string4 = new String("hello");

console.log(string1 == string2); // true
console.log(string1 == string3); // true
console.log(string2 == string3); // true
console.log(string3 == string4); // false
console.log(string4 == string4); // true
```

### Сравнение дат и строк

```js
const d = new Date("1995-12-17T03:24:00");
const s = d.toString(); // например: "Sun Dec 17 1995 03:24:00 GMT-0800 (Pacific Standard Time)"
console.log(d == s); // true
```

### Сравнение массивов и строк

```js
const a = [1, 2, 3];
const b = "1,2,3";
a == b; // true, `a` приводится к строке

const c = [true, 0.5, "hey"];
const d = c.toString(); // "true,0.5,hey"
c == d; // true
```

## Спецификации

{{Specifications}}

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- [Нестрогое неравенство (`!=`)](/ru/docs/Web/JavaScript/Reference/Operators/Inequality)
- [Строгое равенство (`===`)](/ru/docs/Web/JavaScript/Reference/Operators/Strict_equality)
- [Строгое неравенство (`!==`)](/ru/docs/Web/JavaScript/Reference/Operators/Strict_inequality)
