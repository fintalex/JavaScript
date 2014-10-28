JavaScript
==========

## <a name='OGL'> Оглавление </a>

1. [Прототипы и наследование](#ProtoAndInher)
1. [Конструкторы и классы](#ConstrAndClass)

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

