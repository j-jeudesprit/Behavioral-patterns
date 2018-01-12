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

**Цепочка обязанностей** широко применяется повсеместно, в том числе в мобильных и не только устройствах. Когда вы нажимаете на кнопку на экране вашего телефона, то сам объект, предоставляющий вам эту кнопку на экране, может не нести ответсвенности за все возможные и необходимые действия. Если какую-либо возложенную на него задачу он не может сделать, он передаёт задачу по цепочке объекту стоящему за ним, это может быть какой-то виджет. И так далее, вплоть до основного объекта, который предоставляет так называемое окно вашего приложения. 

![](https://cdn-images-1.medium.com/max/1600/1*uyNSXJFY-E5vzhOO82TBlQ.png)

Вы всегда можете вместо кучи условных оператор в вашем коде, выделить проверки в отдельные классы и применить паттерн **Цепочка обязанностей**. В таком случаи ваш код перестанет быть комком сложных и понятной лишь вам логики проверок. 

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/problem2-ru-2x.png)

Такая каша должна превратиться, после применения паттерна в человека-подобный вид:  

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/solution1-ru-2x.png)  

Нужно понимать, что связи между классами проверок у вас могли  существовать и до принятия этого паттерна, в таком случаи вам не нужно будет выстраивать связь между этими классами, в противном случаи, вам нужно будет в первую очередь позаботиться об этих связях. 

Обычно для начала определяют общий интерфейс проверок, который будет соблюдать каждый участник цепочки. При этом как было написано ранее, всегда нужно иметь ссылку на следующего в цепочке. Каждый участник цепи должен решать сам, подходит ли он для переданной ему задачи или же ему стоит её передать дальше.

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


Передавая таким образом некий запрос *Button*, произойдет проверка, а сможет ли он обработать этот запрос, если нет, то эту попытается сделать *Widget* и так далее. Нам как клиенту, совершенно без разницы, кто из них ответит на наш запрос, главное получить на него ответ!

![](https://i.imgur.com/D4SclSn.jpg)

***
[Command](#command)  
--------------      
![](https://refactoring.guru/images/patterns/content/command/command-2x.png)   
  
**Команда** — это поведенческий паттерн проектирования, который

