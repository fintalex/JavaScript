JavaScript
==========

## <a name='OGL'> Оглавление </a>

1. [Прототипы и наследование](#ProtoAndInher)
2. [Конструкторы и классы](#ConstrAndClass)
3. [Цепные вызовы методов](#ChainChalMethod)
4. [JSON](#JSON)
5. [Массивы](#Array)
6. [ES5 методы массивов](#ES5ArrayMethods)
7. [Объекты Math](#Math)
8. [Класс Date](#Date)

###<a name='ProtoAndInher'> Прототипы и наследование </a>

Любой объект в JS имеет второй связанный с ним объект который называется прототипом.

Объект наследует свойства прототипа (Prototype Inheritance).

И это единственное наследование которое существует в JS. 

- наследование в JS сводится к тому что мы создаем объект
- а потом создаем новый объект с помощью ключевого слова create
- для чего это, допусти нам нужно создать много однотипных объектов.
- и в таком случае лучше создать прототип и общие для него свойства. 

```JS 
var Person = {
	constructor: function (name, age, gender) {
		this.name = name;
		this.age = age; 
		this.gender = gender;
		return this; // обязательно для возвращения
	},
	greet: function() {
		console.log("Hi, I am " + this.name);
	}
};

var person, anotherPerson, thirdPerson;

person = Object.create(Person).constructor("vasya", 22, "male");
anotherPerson = Object.create(Person).constructor("misha", 32, "male");
thirdPerson = Object.create(Person).constructor("petya", 42, "famale");

console.log(person.name);
console.log(anotherPerson.name);
console.log(thirdPerson.name);
```

---

Допустим нам нужен новый класс WebDeveloper который должен иметь все те же свойства что и класс Person. 

```JS 
var WebDeveloper = Object.create(Person);
// более расширенные конструктор - хотим добавить свойство скилс
WebDeveloper.constructor = function(name, age, gender, skills) {
	// чтобы заново не перечислять свойства базового класса
	Person.constructor.apply(this, arguments);
	this.skills = skills || []; // указываем скилы или пустой массив, если скилы не передали
	return this; // обязательно в конструкторе	
};

var webDev = Object.create(WebDeveloper).constructor("Mark", 33, "male", ["css", "html", "ruby"]);
console.log(webDev.skills);
```

Можно добавить на прототип еще один метод и попробуем его вызвать. А также мы можем обращаться к методам родительского класса Person.

```JS

WebDeveloper.develope = function() {
	console.log("working...");
};

webDev.develope(); // working... 
webDev.greet(); //Hi, I am Mark 
```

**[Оглавление](#OGL)**

###<a name='ConstrAndClass'> Конструкторы и классы </a>

Конструктор это функция вызванная с ключевым словом new возвращает новый объект. 

- Конструкторы принято называть с большой буквы.
- Конструкторы не отличаются от функций ничем. 
- Все функции являются объектами.
- У всех функций есть свойство prototype.


```JS
var Person, person, anotherPerson;

Person = function (name) {
	this.name = name;
}

person = new Person("Vasya");
console.log(person.name);

anotherPerson = new Person ("Petya");
console.log(anotherPerson.name);
```

В данном примере определили конструктор Person и создали два экземпляра этого класса. 

---


Класс это множесество всех объектов которые наследуют свои свойства от одного объекта. Поэтому каждая функция также имеет свойство prototype, в котором хранится протип, свойства которого будут наследоваться объектами создаваемые при помощь конструктора. 

```JS 
Person.prototype.greet = function() {
	console.log("Hello, my name is " + this.name);
}
anotherPerson.greet();
```

---

#####Проверка принадлежности объекта классу

```JS
absentPerson = "Dima";
console.log(anotherPerson instanceof Person); // true
console.log(Person.prototype.isPrototypeOf(anotherPerson)); // true
console.log(Person.prototype.isPrototypeOf(absentPerson)); // false
```

##### Способ получения прототипа объекта напрямую.

```JS 
var Developer, devel;

Developer = function(name, skills) {
	Person.apply(this, arguments);
	this.skills = skills || [];
};

Developer.prototype = Object.create(Person.prototype);
Developer.prototype.constructor = Developer;

devel = new Developer("Alex", ["css", "html", "c#"]);
console.log(devel.name);
console.log(devel.skills);
devel.greet();
```

- для наследования лучше использовать Object.create
- но конструктор по умолчанию будет указывать на функцию Person. А чтобы конструктор указывал на функцию Developer- нужно его переопределить как Developer.prototype.constructor = Developer;
- объект класса Developer имеет все совйства от родительского и от прототипа.

---

Так как все объекты наслледуются от одного класса в JS Object, то все объекты имеют свойства toString и valueOf.
Но можно переопределить стандартный метод у прототипа нашего класса, например если toString будет возращать только имя.

```JS 
console.log(devel.toString()); // [object Object] 
Person.prototype.toString = function () {
	return this.name;
}
console.log(devel.toString()); // Alex
```

Таким образом можно изменить поведения нашего класса при преобразовании в строку:

```JS
console.log("the name is: " + devel);
```

- при преобразовании массива мы будем получать просто список объектов через ",".
- при преобразовании функции мы будем получать строку в виде исходного кода функции.

**[Оглавление](#OGL)**

###<a name='ChainChalMethod'> Цепные вызовы методов </a>

В JS уже есть методы которые можно вызывать по цепочке, например при работе со строками

```JS 
var st, newStr; 

str = "Мальенький кролик по травке скочит";

newStr = str
	.replace("кролик", "ежик") // заменим
	.concat(" , травка ежику пузико щекочит") // добавим что то в конец
	.toUpperCase() // к верхнему регистру
	.replace(/ /g, "\n") // заменим все пробелы на переносы строк
	.slice(10); // уберем первые 10 символов

console.log(newStr);
```

Каждый метод возвращает в новую строку, и нам не надо создавать новые переменные, а просто вызывать методы по цепочке.

Тоже самое можно использовать при работе с объектами.

```JS
// нам нужен класс для работы с двумерными векторами
var Vec2 = function(x,y) {
	this.x = x; 
	this.y = y;
};

// метод для сложения вектора
// !!!!!!!!! и этот метод мы хотим использовать в цепочке вызовов и метода Update ниже 
Vec2.prototype.add = function(vec) { 
	this.x += vec.x;
	this.y += vec.y;
	return this; // поэтому когда мы все сложили мы должны вернуть наш объект
};

// мтеод умножения вектора
Vec2.prototype.multScalar = function(scalar) { 
	this.x *= scalar;
	this.y *= scalar;
	return this;
};

// какие-то внешние факторы
var world = {
	gravity: new Vec2(0,1),
	airResistance: .9,  // сопротивление воздуха
	wind: new Vec2(10, 1), // боковой ветер
	control: new Vec2( -3, -5) 
};

// есть какой то объект, у которого есть скорость, и метод update
var object = {
	position: new Vec2 (10, 20),
	speed: new Vec2(1, 3),
	update: function() { // обновить положение объекта , для отрисовки в следующий раз
		this.speed
			.add(world.gravity) // а теперь мы можем 
			.add(world.wind) // вызывать  метод add 
			.add(world.control) // сколько угодно
			.multScalar(world.airResistance);

		return this.position.add(this.speed);
	}
};

console.log(object.update());
console.log(object.update());
console.log(object.update());
console.log(object.update());
console.log(object.update());
```

> Vec2 {x: 17.2, y: 20, add: function, multScalar: function} 

> Vec2 {x: 29.979999999999997, y: 17.3, add: function, multScalar: function} 

> Vec2 {x: 47.782, y: 12.170000000000002, add: function, multScalar: function} 

> Vec2 {x: 70.1038, y: 4.853000000000002, add: function, multScalar: function} 

> Vec2 {x: 96.49342000000001, y: -4.432299999999999, add: function, multScalar: function} 

**[Оглавление](#OGL)**

####<a name='JSON'> JSON </a>

Сериализация объекта - преобразование объекта в строки. 

- Например при передачи данных с сервера 
- Также можно хранить данные локально в localStorage

```JS 
var user = {
	name: "Alex",
	age: 25
};

// для получения строки из объекта
var userData = JSON.stringify(user);
console.log(userData); // {"name":"Alex","age":25} 

// для того чтобы получить из строки объект
console.log(JSON.parse(userData)); // Object {name: "Alex", age: 25} 
```

Но если у объекта есть метод toJSON - то он будет определять что мы будем преобразовывать в строку. Таким образом мы можем определить что нужно отображать в строке (в примере ниже мы скрыли id и friends)

```JS 
var user = {
	name: "Alex",
	age: 25,
	id: 3,
	lastVisit: Date.now(),
	friends: [234, 232, 222],
	toJSON: function () {
		return {
			name: this.name, 
			age: this.age,
			lastVisit: this.lastVisit
		}
	}
};

// для получения строки из объекта
var userData = JSON.stringify(user);
console.log(userData); // {"name":"Alex","age":25,"lastVisit":1414479825874} 

// для того чтобы получить из строки объект
console.log(JSON.parse(userData)); // Object {name: "Alex", age: 25, lastVisit: 1414479825874} 
```
**[Оглавление](#OGL)**

###<a name='Array'> Массивы </a>

В данном разделе не будем говорить что такое масси и с чем его едят. Просто скажем некоторые хитрости и особенности работы с массивами, которые немного не укладываются в голове у тех, кто работает с C#

+ Элементы в массивы не обязательно должны идти под последовательными индексами

```JS
var  months = ["January", "February", "Marth", "April"];
months[11] = "December";
console.log(months); // ["January", "February", "Marth", "April", 11: "December"] 
```

---

+ Массивы в JS являются объектами

```JS
console.log(typeof months); // object
```

Поэтому у массивов есть функция-конструктор - Array. И мы можем с помощью него создавать новые массивы, передавая параметрами элементы массива. Кроме одного случая когда мы передаем один параметр - мы задаем длинну массива. 

```js
var secondArray = new Array (12);
console.log(secondArray); // []
console.log(secondArray.length); // 12
```

Но смысла в таком объявлении нет, так массивы в JS динамические и мы можем объявлять больше 12 элементов массива.

---

В литералах массивова мы можем пропускать элементы при объявлении. И пропущенные элементы будут иметь тип undefined. Если мы не объявим ни одного элемента массива, то длина такого элемента будет на один меньше ожидаемого, так как в литералах массива допускается лишняя запятая в конце. 

```JS 
var someArray = [ ,,,45];
console.log(someArray); // [3: 45] 
console.log(someArray[2]); // undefined 
```

---

Индексы массивов это по сути тоже самое что и имена свойств объектов. И также как и именна свойст объектов они хранятся в виде строк и индекс превращается в строку. Правда индексами могут быть только неотрицательные числа. 

```JS 
months[45] = "UnrealMonth";
months["fiftieth"] = "Fiftieth month";
console.log(months); // ["January", "February", "Marth", "April", 11: "December", 45: "UnrealMonth", fiftieth: "Fiftieth month"]
```

Когда мы добавляем элементы с разными индексами автоматически обновляется свойство length. И это единственное отличие индексов от имен свойств. И это то что делает массив массивом.

---

Если присвоим свойству length какое-то значение то все элементы с индексом выше этого индекса будут удалены. 

```JS 
months.length = 2;
console.log(months); // ["January", "February", fiftieth: "Fiftieth month"] 
```

Как видим удалились все элементы с индексом >= 2 , но не удалился элемент с индексом fiftieth. 

Если удалять элемент с помощью ключевого слова delete, не изменят его длинны, и элементы не сдвигаются. 

```JS 
delete months[0];
console.log(months); // [1: "February", fiftieth: "Fiftieth month"] 
console.log(months[0]); // undefined 
```

---

**Методы массива**

Для рассмотрения метода возьмем некоторый массив из строк.

```JS 
var array = ["Monday","Tuesday", "Wednesday", "Thursday", "Friday"];

var array = ["Monday","Tuesday", "Wednesday", "Thursday", "Friday"];

// возвращает все элементы массива через ","
console.log(array.join()); // Monday,Tuesday,Wednesday,Thursday,Friday 
// принимает любую строку в качестве разделителя
console.log(array.join(" | ")); // Monday | Tuesday | Wednesday | Thursday | Friday 

// некоторые методы изменяют исходный массив
// переворачивает массив и изменяет исходный
console.log(array.reverse()); //["Friday", "Thursday", "Wednesday", "Tuesday", "Monday"] 
console.log(array); // ["Friday", "Thursday", "Wednesday", "Tuesday", "Monday"] 

console.log(array.sort()); // ["Friday", "Monday", "Thursday", "Tuesday", "Wednesday"] 

// просто скалдывает с другими элементами, и не изменяет исходный
console.log(array.concat("Sunday", ["Monday", "Tuesday"])); // ["Friday", "Monday", "Thursday", "Tuesday", "Wednesday", "Sunday", "Monday", "Tuesday"] 

// slice работает также как и со строками
// 
console.log(array.slice(1)); // ["Monday", "Thursday", "Tuesday", "Wednesday"] 
// изменяет исходный массив
// вставляет в массив 
// возвращает то что вырезал
console.log(array.splice(1,1, "Some", "day")); // ["Monday"] 
console.log(array); // ["Friday", "Some", "day", "Thursday", "Tuesday", "Wednesday"] 

// вставляет в конец новый элемент
array.push("weekend"); 
console.log(array);// ["Friday", "Some", "day", "Thursday", "Tuesday", "Wednesday", "weekend"] 

// просто вставляет в начало новый элемент 
array.unshift("work"); 
console.log(array);// ["work", "Friday", "Some", "day", "Thursday", "Tuesday", "Wednesday", "weekend"] 

// pop просто удаляет последний элемент и возвращает его его
console.log(array.pop()); // weekend 
console.log(array.pop()); // Wednesday 
console.log(array.pop()); // Tuesday 
console.log(array.pop()); // Thursday 
console.log(array); // ["work", "Friday", "Some", "day"] 

// shift удаляет элемент из начала массива и возвращает его
console.log(array.shift()); // work 
console.log(array); // ["Friday", "Some", "day"] 
```

**[Оглавление](#OGL)**

###<a name='ES5ArrayMethods'> ES5 методы массивов </a>

Все следующие методы не изменяют массив. 
Эта функция будет передаваться первым аргументом.

```JS
var array = ["javascript", "css", "html", "c#", "angular"];
array.forEach(function(element, index, arr) {
	array[index] = element.toUpperCase();
});
console.log(array); // ["JAVASCRIPT", "CSS", "HTML", "C#", "ANGULAR"] 
```

- первый аргумент - функция (анонимная, так как нет смысла давать ей какое то имя)
- это callback функция будет вызываться для каждого элемента в массиве с тремя агрументами:
	- element - ссылка на элемент;
	- index - индекс этого элемента;
	- arr - и ссылка на сам массив;

В теле этой функции мы можем делать все что угодно, в нашем случаем мы каждому элементу подняли регистр.

--- 

Тоже самое можно было сделать при помощи map

```JS
console.log(array.map(function(e) { return e.toLowerCase(); })); //["javascript", "css", "html", "c#", "angular"] 
```

 Возвращает новый массив. Каждый элемент которого формируется из значений, которые возвращаются из функции, которую мы передаем в качестве первого аргумента.
 
 --- 
 
 Метод filter позволяет отсеять элементы по какому либо критерию. Следующий пример отсеит все строки которые содержат букву А.
 
 ```JS
 var filtered = array.filter ( function(e) {
	return e.indexOf('A') === -1; // ["CSS", "HTML", "C#"] 
});
console.log(filtered); 
```

---

Метод every возвращает булевое значение. Если для массива будет выполнено какое-то условие выполняющееся для всех элементов он вернет true, и вернет false если хоть для одного элемента оно не будет выполнено. 

```JS
console.log(array.every(function(e){ return e.length >=2})); // true 
```

Аналогичный метод some который возвращает true если хотя бы один элемент выполняет условие функции, и false если ни один элемент не выполняет условие функции. 

```JS
console.log(array.some(function(e){ return e.indexOf('#') > -1})); // true 
```

--- 

Метод возвращает одно значение, которое получается в рез-те выполнения функции для каждого элемента массива с возможность сохранения промежуточного результата.
Метод reduce и reduceRight проходят массива слева направо и справа налево и callback принимает 4 аргумента но мы будем использовать только первые два.  а - промежуточное значение, b - текущий элемент массива.

```JS
var numbers = [1,2,3,4,5,6];
var reduced = numbers.reduce(function(a,b, index, arr) {
	// return a + b; // просто сложить все элементы
	 return a * ( b%3 ===  0? b : 1); // перемножить числа кратные трем
});
console.log(reduced); // 18

var reduced = numbers.reduceRight(function(a,b) {
	 return a - b ; 
});
console.log(reduced); // -9
```

---

Ну и еще пара методов работы с массивами - indexOf и lastIndexOf.

```JS
var numbers = [1,2,3,4,5,3,6];
console.log(numbers.indexOf(3));
console.log(numbers.lastIndexOf(3));
```

Если метод не найдет элемент то он вернет -1 как и при работе со строками.

**[Оглавление](#OGL)**

###<a name='Math'> Объекты Math </a>

Немного об объекте Math. 

```JS
console.log(Math.pow(2,5)); // возводит в степень
console.log(Math.sqrt(400)); // квадратный корень
console.log(Math.sqrt(9));
console.log(Math.abs(-300)); // взятие по модую

console.log(Math.round(9.6)); // округление - обычное
console.log(Math.round(9.2));

console.log(Math.floor(5.9)); // для принудительного округления до меньшего
console.log(Math.ceil(5.2)); // для принудительного округления до большего

console.log(Math.max(6,2,5,3)); // возвращает максимальное из аргументов
console.log(Math.min(6,2,5,3));

console.log(Math.exp(2)); // возводит числе e в указанную степень 

console.log(Math.log(5)); //логарифм

console.log(Math.sin(1));
console.log(Math.cos(1));
console.log(Math.tan(1));
console.log(Math.asin(1));

console.log(Math.PI);
console.log(Math.E);

console.log(Math.LN10);
console.log(Math.LN2);
console.log(Math.SQRT2);
console.log(Math.SQRT1_2);

console.log(Math.random()); // возвращает случайное число от 0 - 1
console.log(Math.random());
console.log(Math.random());
console.log(Math.random());

// функция которрая возвращает случайное число в заданном диапазоне
var getRandom = function (min, max) {
	return Math.random() * (max - min) + min;
};

console.log(getRandom(5,10));
console.log(getRandom(70,100));
console.log(getRandom(100,200));
```

**[Оглавление](#OGL)

***<a name='Date'> Оглавление </a>

Теперь немного о дате. Для работы с датами мы используем класс Date, но можно было бы использовать какие-нибудь сторонние бибиотеки. 

```JS
var date = new Date(1999, 1, 1, 10, 22, 12, 345);

console.log(date);

console.log(date.getMilliseconds()); // 345 // получение милисекунд
console.log(date.getHours()); // 10 
console.log(date.getMinutes()); // 22
console.log(date.getSeconds()); // 12
console.log("===");
console.log(date.getDay()); // число месяца
console.log(date.getHours()); // час
console.log(date.getMonth()); // месяц
console.log(date.getYear()); // год - возвращает последние две цифры
console.log(date.getFullYear()); // год - возращает 4 цифры.

date.setDate(15); // установить 15 число
console.log(date);
```

---

Дата и время в JS  хранятвся в виде количества милесекунда прошедших с 1 января 1970г. 
Это называется timeSpan. В этом мы можем убедится с помощью метода getTime. Но если мы укажем дату ранее 1970, то мы получим отрицательное число.

```JS
console.log(date.getTime()); // 919063332345 
console.log(new Date(1945, 0,1).getTime()); //-788929200000 
```

Если в конструктор передать один аргумент то это будет наш timespan.

```JS 
console.log(new Date(346534)); // Thu Jan 01 1970 03:05:46 GMT+0300 (Московское время (зима)) 
```

--- 

```JS
// для вывода только времени или только даты
console.log(date.toTimeString()); // 10:22:12 GMT+0300 (Московское время (зима)) 
console.log(date.toDateString()); // Mon Feb 15 1999 

// для получения локального формата
console.log(date.toLocaleTimeString()); // 10:22:12 
console.log(date.toLocaleDateString()); // 15.02.1999 
```




