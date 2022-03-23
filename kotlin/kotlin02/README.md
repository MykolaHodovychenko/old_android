# Классы

Классы в Kotlin объявляются с помощью использования ключевого слова class

```kotlin
class Person {}
```

Если у класса нет тела, фигурные скобки могут быть опущены

```kotlin
class Person
```

Классы могут содержать в себе:

- конструкторы и инициализирующие блоки
- функции
- свойства (в Java - поля)
- вложенные классы
- объявления объектов

Классы могут быть производными друг от друга и формировать иерархии наследования.

Первым важным отличием класса в Java от класса в Kotlin - наличие такого понятия, как **заголовок класса**. Его наличие связано с конструктором и будет объяснено позже. Заголовок является необязательным.

## Конструктор

Класс в Kotlin может иметь **основной конструктор** (**primary constructor**) и один или более **дополнительных конструкторов** (**secondary constructor**).

Смысл такого шага заключается в том, что часто конструктор выступает просто способом инициализировать поля класса. Чтобы избавиться от церемониального кода (boilerplate code), Kotlin предлагает сделать это с помощью основного конструктора и заголовка класса. Запомните, что основной конструктор всегда указывается в заголовке класса.

Для примера, возьмем класс `Student` с полями "фамилия", "имя", "группа" и "количество лет". Поле "фамилия" и "имя" будут неизменными, а два остальных поля могут менять свое значение.

Для языка Java описание класса будет выглядеть следующим образом

```java
class Student {
    private final String firstName;
    private final String lastName;
    private String group;
    private int years;

    public Student(String firstName, String lastName, String group, int years) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.group = group;
        this.years = years;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public String getGroup() {
        return group;
    }

    public void setGroup(String group) {
        this.group = group;
    }

    public int getYears() {
        return years;
    }

    public void setYears(int years) {
        this.years = years;
    }
}
```

В языке Kotlin этого можно достичь следующим способом

```kotlin
class Student(val firstName: String, val lastName: String, var group: String, var years: Int)
```

Таким образом, в классе будут созданы члены класса, будет создан код конструктора и геттеры\сеттеры там, где это нужно. Если посмотреть байткод и сгенерировать код Java, то мы увидим такой код

```java
public final class Student {
   @NotNull
   private final String firstName;
   @NotNull
   private final String lastName;
   @NotNull
   private String group;
   private int years;

   @NotNull
   public final String getFirstName() {
      return this.firstName;
   }

   @NotNull
   public final String getLastName() {
      return this.lastName;
   }

   @NotNull
   public final String getGroup() {
      return this.group;
   }

   public final void setGroup(@NotNull String var1) {
      Intrinsics.checkNotNullParameter(var1, "<set-?>");
      this.group = var1;
   }

   public final int getYears() {
      return this.years;
   }

   public final void setYears(int var1) {
      this.years = var1;
   }

   public Student(@NotNull String firstName, @NotNull String lastName, @NotNull String group, int years) {
      Intrinsics.checkNotNullParameter(firstName, "firstName");
      Intrinsics.checkNotNullParameter(lastName, "lastName");
      Intrinsics.checkNotNullParameter(group, "group");
      super();
      this.firstName = firstName;
      this.lastName = lastName;
      this.group = group;
      this.years = years;
   }
}
```

Если не принимать во внимание различные проверки, то этот код практически совпадает с кодом на Java, который мы сами написали ранее.

Здорово, но что если вы хотите в конструкторе не просто присвоить начальные значения полям, но и выполнить некоторый код? Для этого можно воспользоваться **блоками инициализации** (**initializers blocks**), которые помечаются словом `init`.

При создании объекта класса, блоки инициализации выполняются в том порядке, в котором они идут в теле класса, чередуясь с инициализацией свойств.

```kotlin
class Student(val firstName: String, val lastName: String, var group: String, var years: Int) {
    init {
        println("Первый блок инициализации сработал")
    }

    init {
        println("Второй блок инициализации сработал")
    }
}
```

Параметры основного конструктора могут быть использованы в инициализирующих блоках, а также при инициализации свойств в теле класса.

Если у конструктора есть аннотации или модификаторы видимости, ключевое слово `constructor` обязательно, и модификаторы используются перед ним.

```kotlin
class Student private constructor(val firstName: String, val lastName: String, var group: String, var years: Int) {
    init {
        println("Первый блок инициализации сработал")
    }

    init {
        println("Второй блок инициализации сработал")
    }
}
```

### Дополнительные конструкторы

В классах также могут быть объявлены **дополнительные конструкторы** (**secondary constructors**), перед которыми используется ключевое слово `constructor`

```kotlin
class Group(val students: MutableList<Student> = mutableListOf())

class Student {
    constructor(group: Group) {
        group.students.add(this) // При создании студента, объект добавляется в список группы
    }
}
```

Если у класса есть основной конструктор, каждый дополнительный конструктор должен прямо или косвенно (через другие конструкторы) ссылаться на основной. Это реализуется с помощью ключевого слова `this`

```kotlin
fun main() {
    val group = Group()
    val student1 = Student("ivan", "ivanov")
    val student2 = Student("ivan", "ivanov", group)
}

class Group(val students: MutableList<Student> = mutableListOf())

class Student(val firstName: String, val lastName: String) {

    constructor(firstName: String, lastName: String, group: Group) : this(firstName, lastName) {
        group.students.add(this)
    }
}
```

Как мы видим, дополнительный конструктор **обязан** ссылаться на основной, а так как основной конструктор принимает два параметра, то входные аргументы дополнительного конструктора должны включать поля для основного + какие-то свои дополнительные поля.

Так как основной конструктор отрабатывает первым, а уже потом отрабатывает дополнительный, то блоки инициализации будут выполнены перед дополнительным конструктором.

```kotlin
class Test(val value: String) {
    init {
        println("Сначала отработает основной конструктор")
        println("Этот блок инициализации отработает вторым по счету.")
    }
    
    constructor() : this("some string") {
        println("Этот дополнительный конструктор отработает третьим по счету")
    }
}
```

Даже если у класса нет основного конструктора, но есть блоки инициализации, они все равно будут выполнены перед дополнительным конструктором, так как всё равно происходит неявная ссылка на основной конструктор.

```kotlin
class Test {
    init {
        println("Этот блок отработает первым")
    }

    constructor() {
        println("Дополнительный конструктор отработает вторым")
    }
}
```

## Свойства (поля в Java)

Еще одним важнейшим отличием Java от Kotlin является то, что в Kotlin нет понятия - **поле класса** (**class field**). Вместо него существует понятие - **свойства** (**property**).

Если говорить просто, то свойство - это обертка над полем. Свойство включает в себя не только значение, но и методы доступа к полю - геттер и сеттер.

Следующий код на Kotlin

```kotlin
class Person {
    
    var name: String = ""
}
```

будет аналогичен следующему коду на Java

```java
public final class Person {
   @NotNull
   private String name = "";

   @NotNull
   public final String getName() {
      return this.name;
   }

   public final void setName(@NotNull String var1) {
      Intrinsics.checkNotNullParameter(var1, "<set-?>");
      this.name = var1;
   }
}
```

То есть, для свойства типа `var` в Kotlin будут автоматически сгенерированы геттер и сеттер. Для свойства типа `val` будет сгенерирован только геттер.

Код на Kotlin

```kotlin
class Person {
    val id: Int = 100
}
```

будет аналогичен следующему коду на Java

```java
public final class Person {
   private final int id = 100;

   public final int getId() {
      return this.id;
   }
}
```

Для того, чтобы воспользоваться свойством, нужно обратиться к нему по имени. В этом случае будет использоваться геттер или сеттер (в зависимости от типа обращения)

```kotlin
fun main() {
    val person = Person()
    person.name = "Ivan" // будет вызван сеттер
    println(person.name) // будет вызван геттер
}

class Person {
    var name: String = ""
}
```

То есть, теперь мы избавились от необходимости писать везде `get...()` и `set...()`

Если вам необходимо, вы можете самостоятельно определить методы доступа для свойства. Например, если определить пользовательский геттер, то он будет автоматически вызываться, когда будет происходить обращение к свойству.

```kotlin
class Rectangle(val width: Double, val height: Double) {
    val area: Double
        get() {
            return this.height * this.width
        }
}
```

Если вы определили свой геттер и из него можно определить тип свойства, его можно не писать

```kotlin
class Rectangle(val width: Double, val height: Double) {
    val area get() = this.height * this.width
}
```

Если вы определили пользовательский сеттер, он будет вызываться каждый раз, когда вы присваиваете значение свойству, за исключением инициализации.

```kotlin
    var group: String = ""
        set(value) {
            println("group number changed")
            field = value
        }
```

По договоренности, имя параметра сеттера - `value`, но теоретически можно использовать другое.

Обратите внимание на переменную **field**. В Kotlin поле используется только как часть свойства для хранения его значения в памяти. Поля не могут быть объявлены напрямую. Однако, когда свойству требуется **теневое поле** (**backing field**), Kotlin предоставляет его идентификатором **field**. Его можно использовать только в методах доступа к свойству.

Вы можете изменить область видимости сеттера, но **область видимости геттера всегда соответствует области видимости свойства**.

Если вам нужно изменить область видимости метода доступа или пометить его аннотацией, при этом не меняя реализацию, вы можете объявить метод доступа без объявления его тела.

```kotlin
class Person {
    var name: String = ""
        @Inject set

    var group: String = ""
        private set
}
```