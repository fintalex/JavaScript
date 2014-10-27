JavaScript
==========

####Конструкторы и классы

Конструктор это функция которая вызванная с ключевым словом new возвращает новый объект. 
- Конструкторы принято называть с большой буквы.
- Конструкторы не отличаются от функций ничем. 

```
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
