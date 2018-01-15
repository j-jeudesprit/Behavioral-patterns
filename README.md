  ![](https://i.imgur.com/29maQkl.png)  

***    
**Ссылки на конкретный паттерн**:  

 * [Chain of Responsibility](#chain-of-responsibility)
 * [Command](#command)  
 * [Interpreter](#interpreter)
 * [Iterator](#iterator)
 * [Mediator](#mediator)     
 * [Memento](#memento)  
 * [Observer](#observer) 
 * [State](#state) 
 * [Strategy](#strategy)   
 * [Template Method](#template-method)     
 * [Visitor](#visitor) 

***
[Chain of Responsibility](#chain-of-responsibility)  
--------------      
![](https://refactoring.guru/images/patterns/content/chain-of-responsibility/chain-of-responsibility-2x.png)   
  
**Цепочка обязанностей** — это поведенческий паттерн проектирования, который позволяет передавать запросы по цепочке обработчиков. Каждый узел цепи, сам решает, может ли он обработать или же передаст дальше. 

**Цепочка обязанностей** широко применяется повсеместно, в том числе в мобильных и не только устройствах. Когда вы нажимаете на кнопку на экране вашего телефона, то сам объект, предоставляющий вам эту кнопку на экране, может не нести ответсвенности за все возможные и необходимые действия. Если какую-либо возложенную на него задачу он не может сделать, он передаёт задачу по цепочке, объекту стоящему за ним, это может быть какой-то виджет. И так далее, вплоть до основного объекта, который предоставляет так называемое окно вашего приложения. 

![](https://cdn-images-1.medium.com/max/1600/1*uyNSXJFY-E5vzhOO82TBlQ.png)

Вы всегда можете вместо кучи условных оператор в вашем коде, выделить проверки в отдельные классы и применить паттерн **Цепочка обязанностей**. В таком случаи ваш код перестанет быть комком сложных и понятной лишь вам, логики проверок. 

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/problem2-ru-2x.png)

Такая каша должна превратиться, после применения паттерна в человека-подобный вид:  

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/solution1-ru-2x.png)  

Нужно понимать, что связи между классами проверок у вас могли существовать и до принятия этого паттерна, в таком случаи вам не нужно будет выстраивать связь между этими классами, в противном случаи, вам нужно будет в первую очередь позаботиться об этих связях. 

Обычно для начала определяют общий интерфейс проверок, который будет соблюдать каждый участник цепочки. При этом, как было написано ранее, всегда нужно иметь ссылку на следующего в цепочке. Каждый участник цепи должен решать сам, подходит ли он для переданной ему задачи или же ему стоит её передать дальше.

```swift
class Request {
    // ...
}

protocol Handler {
    func setNext(_ next: Handler) -> Handler
    func handle(_ request: Request)
    // ...
}

class Application: Handler {
    private var next: Handler!

    func setNext(_ next: Handler) -> Handler {
        self.next = next
        return self.next
    }

    func handle(_ request: Request) {
        if ... {
            // Обрабатываем сами
            // ...
        } else {
            // Либо передаём дальше
            next.handle(request)
        }
    }

    // ...
}

class Widget: Handler {
    private var next: Handler!

    func setNext(_ next: Handler) -> Handler {
        self.next = next
        return self.next
    }

    func handle(_ request: Request) {
        if ... {
            // Обрабатываем сами
            // ...
        } else {
            // Либо передаём дальше
            next.handle(request)
        }
    }

    // ...
}

class Dialog: Handler {
    private var next: Handler!

    func setNext(_ next: Handler) -> Handler {
        self.next = next
        return self.next
    }

    func handle(_ request: Request) {
        if ... {
            // Обрабатываем сами
            // ...
        } else {
            // Либо передаём дальше
            next.handle(request)
        }
    }

    // ...
}

class Button: Handler {
    private var next: Handler!

    func setNext(_ next: Handler) -> Handler {
        self.next = next
        return self.next
    }

    func handle(_ request: Request) {
        if ... {
            // Обрабатываем сами
            // ...
        } else {
            // Либо передаём дальше
            next.handle(request)
        }
    }

    // ...
}

// Main

let app = Application()
let widget = Widget()
let button = Button()
button.setNext(widget)
let dialog = Dialog()
dialog.setNext(widget)

let someRequest = Request()
button.handle(someRequest)
```


Передавая таким образом некий запрос *Button*, произойдет проверка, а сможет ли он обработать этот запрос, если нет, то это попытается сделать *Widget* и так далее. Нам как клиенту, совершенно без разницы, кто из них ответит на наш запрос, главное получить на него ответ!

![](https://i.imgur.com/D4SclSn.jpg)

***
[Command](#command)  
--------------      
![](https://refactoring.guru/images/patterns/content/command/command-2x.png)   
  
**Команда** — это поведенческий паттерн проектирования, который инкапсулирует запрос как объект, позволяя передавать их как аргументы при вызове методов, ставить запросы в очередь, вести логи и поддерживать отмену запроса. 

Иногда необходимо посылать объектам запросы, ничего не зная о том, выполнение какой операции запрошено и кто является получателем. Например, в библиотеках для построения пользовательских интерфейсов встречаются такие объекты, как кнопки и меню, которые посылают запрос в ответ на действие пользователя. Но в саму библиотеку не заложена возможность обрабатывать этот запрос, так как только приложение, использующее её, располагает информацией о том, что следует сделать. Проектировщик библиотеки не владеет никакой информацией о получателе запроса и о том, какие операции тот должен выполнить.
Паттерн **Команда** позволяет библиотечным объектам отправлять запросы неизвестным объектам приложения, преобразовав сам запрос в объект. Этот объект можно хранить и передавать, как и любой другой. В основе описываемого паттерна лежит интерфейс, в котором объявлены операции для выполнения. В простейшей своей форме этот интерфейс состоит из одной абстрактной операции *execute*. Конкретные классы реализующие интерфейс определяют пару «получатель действие», сохраняя получателя в переменной экземпляра, и реализуют операцию *execute*, так чтобы она посылала запрос. У получателя есть информация, необходимая для выполнения запроса.

```swift
// Получатели

class Application { }
class Document { }

// Команды

protocol Command {
    func execute()
}

class OpenCommand: Command {
    private var responce: Application

    init(responce: Application) {
        self.responce = responce
    }

    func execute() {
        // ...
    }

    // ...
}

class PasteCommand: Command {
    private var responce: Document

    init(responce: Document) {
        self.responce = responce
    }

    func execute() {
        // ...
    }

    // ...
}
```

Иногда может возникнуть потребность в выполнении сразу нескольких команд, для таких случаев можно определить следующий класс: 

```swift
class MultiCommand: Command {
    private var list = [Command]()

    func execute() {
        for command in list {
            command.execute()
        }
    }

    func add(_ command: Command) {
        // ...
    }

    func remove(_ command: Command) {
        // ...
    }

    // ...
}
```


Теперь в коде мы может легко передавать любую команду, при этом мы не знаем, что это за команда и кому она адресована, да нам собственно это и не важно. 

```swift
class Client {
    var command: Command!

    func action1() {
        // ...
        if ... {
            command.execute()
        }
    }

    func action2() {
        // ...
        if ... {
            command.execute()
        }
    }

    // ...
}

// Main

let client = Client()

client.command = PasteCommand(responce: Document())
client.action1()
client.action2()

client.command = OpenCommand(responce: Application())
client.action1()
client.action2()

client.command = MultiCommand()
let multi = client.command! as! MultiCommand
multi.add(OpenCommand(responce: Application()))
multi.add(PasteCommand(responce: Document()))
client.action1()
client.action2()
```
***
[Interpreter](#interpreter)
--------------      

**Интерпретатор** — это поведенческий паттерн проектирования, который определяет представление его грамматики, а также интерпретатор предложений этого языка.
  
Если некоторая задача возникает часто, то имеет смысл представить её конкретные проявления в виде предложений на простом языке. Затем можно будет создать интерпретатор, который решает задачу, анализируя предложения этого языка.  

Например, поиск строк по образцу – весьма распространенная задача. Регулярные выражения – это стандартный язык для задания образцов поиска. Вместо того чтобы программировать специализированные алгоритмы для сопоставления строк с каждым образцом, не проще ли построить алгоритм поиска так, чтобы он мог интерпретировать регулярное выражение, описывающее множество строк образцов?  

Паттерн интерпретатор определяет грамматику простого языка, представляет предложения на этом языке и интерпретирует их. Для приведенного примера паттерн описывает определение грамматики и интерпретации языка регулярных выражений.  

Предположим, что они описаны следующей грамматикой:
  
```  
expression ::= literal | alternation | sequence | repetition | '(' expression ')'
alternation ::= expression '|' expression
sequence ::= expression '&' expression
repetition ::= expression '*'
literal ::= 'a' | 'b' | 'c' | ... { 'a' | 'b' | 'c' | ... }*  
```

где *expression* – это начальный символ, а *literal* – терминальный символ, определяющий простые слова.  

Паттерн **Интерпретатор** использует класс для представления каждого правила грамматики. Символы в правой части правила – это переменные экземпляров таких классов. Для представления приведенной выше грамматики требуется пять классов: интерфейс *RegularExpression* и четыре его подкласса *LiteralExpression*, *AlternationExpression*, *SequenceExpression* и *RepetitionExpression*. В последних трех подклассах определены переменные для хранения подвыражений, т.к. они представляют собой начальные, не терминальные символы.   
    
![](https://i.imgur.com/xNqvWex.jpg)  

Каждое регулярное выражение, описываемое этой грамматикой, представляется в виде абстрактного синтаксического дерева, в узлах которого находятся экземпляры этих классов. Например, дерево    
  
![](https://i.imgur.com/C5PNclC.jpg)  
  
представляет выражение ```raining & (dogs | cats) * ```    

  
[Отличный пример реализации на Swift ](https://github.com/kingreza/Swift-Interpreter)

***
[Iterator](#iterator)
--------------      
![](https://refactoring.guru/images/patterns/content/iterator/iterator-2x.png)  
  
**Итератор** — это поведенческий паттерн проектирования, который предоставляет последовательный доступ ко всем элементам объекта, не раскрывая его внутреннего представления.


Составной объект, скажем список, должен предоставлять способ доступа к своим элементам, не раскрывая их внутреннюю структуру. Более того, иногда требуется обходить список по разному, в зависимости от решаемой задачи. Но вряд ли вы захотите засорять интерфейс класса *List* операциями для различных вариантов обхода, даже если все их можно предвидеть заранее. Кроме того, иногда нужно, что  бы в один и тот же момент было определено несколько активных обходов списка.  

Все это позволяет сделать паттерн **Итератор**. Основная его идея в том, чтобы за доступ к элементам и способ обхода отвечал не сам список, а отдельный объект  итератор. В классе *Iterator* определен интерфейс для доступа к элементам списка. Объект этого класса отслеживает текущий элемент, то есть он располагает информацией, какие элементы уже посещались.

![](https://i.imgur.com/csgwhjG.jpg)

Для случая, когда конкретный тип списка неизвестен, соответсвенно неизвестен заранее итератор, который нужно будет создавать, используют фабричный метод *CreateIterator* и следующую схему.

![](https://i.imgur.com/pluuKoK.jpg)

Также вместо обычных методов в итераторах, лучше использовать операторные методы, по типу ```++, --``` и т.п.

Касаемо языка Swift, то для этих целей у языка из коробки существуют протоколы, реализуя которые, можно добиться той же функциональности(*Sequence*, *Collection*, *IteratorProtocol*, ...).  


***
[Mediator](#mediator)
--------------      
![](https://refactoring.guru/images/patterns/content/mediator/mediator-2x.png)  
  
**Посредник** — это поведенческий паттерн проектирования, который определяет объект, инкапсулирующий способ взаимодействия множества объектов. Обеспечивает слабую связность системы.

Представим, что мы разрабатывает некий диалог(диалог регистрации) с пользователем, в котором участвуют множество кнопок, чекбоксов, полей ввода и т.п. При этом логика их взаимодействия может быть самой разной. Если мы не ввели что-то, то какая-либо из кнопок будет неактивной, если же ввели, она становится активной. Активация тех или иных полей ввода, передача информации между элементами, для проверки информации и так далее. Всё это может раздуться в огромное количество связей между всеми участниками нашего диалога.   
  
В качестве другого примера можно привести диспетчерскую в аэропорту. Самолётам при посадке или взлёте не нужно спрашивать у других самолётов, которые быть может в этот момент могут садиться или взлетать. Каждый самолёт спрашивает разрешение лишь у диспетчера. Диспетчер сам контролирует воздушное пространство близ аэропорта и не перекладывают задачу на самолёты. 

Сильная связность такой системы не позволяет рассматривать объекты системы отдельно, т.к. они жёстко привязаны к другим объектам системы. 

**Посредник** решает эту проблему, путём введение интерфейса, который определяет обычно лишь один метод *notify*. Конкретные классы, (то есть посредников может быть несколько, мы не привязываемся жёстко к одному посреднику) реализуют этот интерфейс и определяют всю логику взаимодействия. Теперь каждому объекту достаточно хранить ссылку на конкретного посредника и в случаи надобности передавать оповещение посреднику. Где уже посредник, зная от кого он получил информацию, сообщит об этом нужным объектам. 

```swift
protocol Mediator {
    func notify(_ sender: Component, with event: String)
    // ...
}

class AuthDialog: Mediator {
    func notify(_ sender: Component, with event: String) {
        // ...
    }

    // ...
}

protocol Component {
    var dialog: Mediator { get set }
    // ...
}

class Button: Component {
    var dialog: Mediator

    init(dialog: Mediator) {
        self.dialog = dialog
    }

    func click() {
        // ...
        dialog.notify(self, with: "Clicked")
        // ...
    }

    // ...
}

class TextBox: Component {
    var dialog: Mediator

    init(dialog: Mediator) {
        self.dialog = dialog
    }

    func input() {
        // ...
        dialog.notify(self, with: "Inputted")
        // ...
    }

    // ...
}
```

***
[Memento](#memento)
--------------      
![](https://refactoring.guru/images/patterns/content/memento/memento-2x.png)  
  
**Хранитель** — это поведенческий паттерн проектирования, который не нарушая инкапсуляции, фиксирует и выносит за пределы объекта его внутреннее состояние так, чтобы позже можно было восстановить в нём объект.

Иногда необходимо тем или иным способом зафиксировать внутреннее состояние объекта. Такая потребность возникает, например, при реализации контрольных точек и механизмов отката, позволяющих пользователю отменить пробную операцию или восстановить состояние после ошибки. Его необходимо где-то сохранить, чтобы позднее восстановить в нем объект. Но обычно объекты инкапсулируют всё своё состояние или хотя бы его часть, делая его недоступным для других объектов, так что сохранить состояние извне невозможно. Раскрытие же состояния явилось бы нарушением принципа инкапсуляции и поставило бы под угрозу надежность и расширяемость приложения.

Паттерн **Хранитель** поможет решить данную проблему. **Хранитель** – это объект, в котором сохраняется внутреннее состояния другого объекта – хозяина хранителя. Для работы механизма отката нужно, чтобы хозяин предоставил хранитель, когда возникнет необходимость записать контрольную точку состояния хозяина. Только хозяину разрешено помещать в хранитель информацию и извлекать её оттуда, для других объектов хранитель непрозрачен. 

[Одна из возможных реализаций на Swift](http://audreyli.me/2015/07/15/a-design-pattern-story-in-swift-chapter-17-memento/)

***
[Observer](#observer) 
--------------      
![](https://refactoring.guru/images/patterns/content/observer/observer-2x.png)  
  
**Наблюдатель** — это поведенческий паттерн проектирования, который предоставляет механизм оповещений при изменении состояния одного объекта, всем зависящим от него объектам.

Представим простую ситуацию, вы ждете появление некоего товара в магазине. Каждый раз ходить в магазин и узнавать о его наличии, дело не хитрое. С другой стороны  и от самого магазина вы бы не хотели получать различного рода письма, даже если среди них и будет нужное вам оповещение. Самым простым решение будет возможность подписки на изменения конкретного объекта, то есть товара в магазине и возможность отписки от оповещений. Такую задачу решает паттерн **Наблюдатель**.

Представим, что наш товар это конкретный субъект и вообще говоря их может быть сколько угодно. Для этой цели мы определим интерфейс *Subject*, в котором будут методы подписки/отписки и оповещения. Так же определим интерфейс *Observer* с методом обновления, что бы любой мог стать наблюдателем. В первую очередь этот интерфейс нужен для того, что бы интерфейсу *Subject* не было никакой разницы, кто на него подписан. Реализую интерфейс *Observer* можно стать наблюдателем. 

```swift
protocol Observer {
    var subject: Subject { get }
    func notify()
}

//###############################

protocol Subject {
    var observerCollection: [Observer] { get }

    func register(_ observer: Observer)
    func unregister(_ observer: Observer)
    func notifyObservers()
}

//###############################

class ConcreateSubject: Subject {
    var observerCollection = [Observer]()

    func register(_ observer: Observer) {
        observerCollection.append(observer)
    }

    func unregister(_ observer: Observer) {
        // ...
    }

    func notifyObservers() {
        for observer in observerCollection {
            observer.notify()
        }
    }

    // ...
}

//###############################

class ConcreateObserverA: Observer {
    var subject: Subject

    func notify() {
        // ...
    }

    // ...
}

class ConcreateObserverB: Observer {
    var subject: Subject

    func notify() {
        // ...
    }

    // ...
}
```

Объект *ConcreteSubject* уведомляет своих наблюдателей о любом изменении, которое могло бы привести к рассогласованности состояний наблюдателя и субъекта. После получения от конкретного субъекта уведомления об изменении объект *ConcreteObserver* может запросить у субъекта дополнительную информацию, которую использует для того, чтобы оказаться в состоянии, согласованном с состоянием субъекта.


