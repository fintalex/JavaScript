JavaScript
==========

## <a name='OGL'> Оглавление </a>

1. [Прототипы и наследование](#ProtoAndInher)
2. [Конструкторы и классы](#ConstrAndClass)
3. [Цепные вызовы методов](#ChainChalMethod)
4. [JSON](#JSON)

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
