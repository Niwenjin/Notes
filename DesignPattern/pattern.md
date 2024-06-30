# 设计模式

## 目录

[设计模式的六大原则](#设计模式的六大原则)  
**创建型模式**  
[工厂模式](#工厂模式)  
[单例模式](#单例模式)  
[建造者模式](#建造者模式)  
[原型模式](#原型模式)  
**结构型模式**  
[适配器模式](#适配器模式)  
[桥接模式](#桥接模式)  
**行为模式**  
[观察者模式](#观察者模式)

## 设计模式的六大原则

**总原则：开闭原则（Open Close Principle）**

开闭原则就是说对扩展开放，对修改关闭。在程序需要进行拓展的时候，不能去修改原有的代码，而是要扩展原有代码，实现一个热插拔的效果。所以一句话概括就是：为了使程序的扩展性好，易于维护和升级。想要达到这样的效果，我们需要使用接口和抽象类等，后面的具体设计中我们会提到这点。

**单一职责原则**

不要存在多于一个导致类变更的原因，也就是说每个类应该实现单一的职责，如若不然，就应该把类拆分。

**里氏替换原则（Liskov Substitution Principle）**

里氏代换原则(Liskov Substitution Principle LSP)面向对象设计的基本原则之一。里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。LSP 是继承复用的基石，只有当衍生类可以替换掉基类，软件单位的功能不受到影响时，基类才能真正被复用，而衍生类也能够在基类的基础上增加新的行为。里氏代换原则是对“开-闭”原则的补充。实现“开-闭”原则的关键步骤就是抽象化。而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。

历史替换原则中，子类对父类的方法尽量不要重写和重载。因为父类代表了定义好的结构，通过这个规范的接口与外界交互，子类不应该随便破坏它。

**依赖倒转原则（Dependence Inversion Principle）**

这个是开闭原则的基础，具体内容：面向接口编程，依赖于抽象而不依赖于具体。写代码时用到具体类时，不与具体类交互，而与具体类的上层接口交互。

**接口隔离原则（Interface Segregation Principle）**

这个原则的意思是：每个接口中不存在子类用不到却必须实现的方法，如果不然，就要将接口拆分。使用多个隔离的接口，比使用单个接口（多个接口方法集合到一个的接口）要好。

**迪米特法则（最少知道原则）（Demeter Principle）**

一个类对自己依赖的类知道的越少越好。也就是说无论被依赖的类多么复杂，都应该将逻辑封装在方法的内部，通过 public 方法提供给外部。这样当被依赖的类变化时，才能最小的影响该类。

最少知道原则的另一个表达方式是：只与直接的朋友通信。类之间只要有耦合关系，就叫朋友关系。耦合分为依赖、关联、聚合、组合等。我们称出现为成员变量、方法参数、方法返回值中的类为直接朋友。局部变量、临时变量则不是直接的朋友。我们要求陌生的类不要作为局部变量出现在类中。

**合成复用原则（Composite Reuse Principle）**

原则是尽量首先使用合成/聚合的方式，而不是使用继承。

## 创建型模式

创建型模式抽象了实例化过程。它们帮助一个系统独立于如何创建、组合和表示它的那些对象。

### 工厂模式

对“对象的创建”进行了一个封装。使用了 C++ **多态**的特性，将存在**继承**关系的类，通过一个工厂类创建对应的子类（派生类）对象，让其子类自己决定实例化哪一个工厂类。工厂模式使其创建过程延迟到子类进行，主要解决接口选择的问题。

#### 简单工厂模式

简单工厂模式的结构组成：

1. 工厂类：工厂模式的核心类，会定义一个用于创建指定的具体实例对象的接口。
2. 抽象产品类：是具体产品类的继承的父类或实现的接口。
3. 具体产品类：工厂类所创建的对象就是此具体产品实例。

```cpp
class Shoes;
class NikeShoes : Shoes;
class AdidasShoes : Shoes;
class LiNingShoes : Shoes;

enum SHOES_TYPE
{
    NIKE,
    LINING,
    ADIDAS
};

class ShoesFactory
{
public:
    Shoes *CreateShoes(SHOES_TYPE type)
    {
        switch (type)
        {
        case NIKE:
            return new NiKeShoes();
            break;
        case LINING:
            return new LiNingShoes();
            break;
        case ADIDAS:
            return new AdidasShoes();
            break;
        default:
            return NULL;
            break;
        }
    }
};
```

#### 工厂方法模式

工厂方法模式的结构组成：

1. 抽象工厂类：工厂方法模式的核心类，提供创建具体产品的接口，由具体工厂类实现。
2. 具体工厂类：继承于抽象工厂，实现创建对应具体产品对象的方式。
3. 抽象产品类：它是具体产品继承的父类（基类）。
4. 具体产品类：具体工厂所创建的对象，就是此类。

工厂方法模式的特点：

1. 工厂方法模式抽象出了工厂类，提供创建具体产品的接口，交由子类去实现。
2. 工厂方法模式的应用并不只是为了封装具体产品对象的创建，而是要把具体产品对象的创建放到具体工厂类实现。

工厂方法模式的缺陷：

1. 每新增一个产品，就需要增加一个对应的产品的具体工厂类。相比简单工厂模式而言，工厂方法模式需要更多的类定义。
2. 一条生产线只能一个产品。

```cpp
class ShoesFactory
{
public:
    virtual Shoes *CreateShoes() = 0;
    virtual ~ShoesFactory() {}
};

// 耐克生产者/生产链
class NiKeProducer : public ShoesFactory
{
public:
    Shoes *CreateShoes()
    {
        return new NiKeShoes();
    }
};

// 阿迪达斯生产者/生产链
class AdidasProducer : public ShoesFactory
{
public:
    Shoes *CreateShoes()
    {
        return new AdidasShoes();
    }
};

// 李宁生产者/生产链
class LiNingProducer : public ShoesFactory
{
public:
    Shoes *CreateShoes()
    {
        return new LiNingShoes();
    }
};
```

#### 抽象工厂模式

抽象工厂模式的结构组成（和工厂方法模式一样）：

1. 抽象工厂类：工厂方法模式的核心类，提供创建具体产品的接口，由具体工厂类实现。
2. 具体工厂类：继承于抽象工厂，实现创建对应具体产品对象的方式。
3. 抽象产品类：它是具体产品继承的父类（基类）。
4. 具体产品类：具体工厂所创建的对象，就是此类。

抽象工厂模式的特点：

- 提供一个接口，可以创建多个产品族中的产品对象。如创建耐克工厂，则可以创建耐克鞋子产品、衣服产品、裤子产品等。

抽象工厂模式的缺陷：

- 同工厂方法模式一样，新增产品时，都需要增加一个对应的产品的具体工厂类。

```cpp
// 基类 衣服
class Clothe
{
public:
    virtual void Show() = 0;
    virtual ~Clothe() {}
};

// 耐克衣服
class NiKeClothe : public Clothe;

// 基类 鞋子
class Shoes
{
public:
    virtual void Show() = 0;
    virtual ~Shoes() {}
};

// 耐克鞋子
class NiKeShoes : public Shoes;

// 总厂
class Factory
{
public:
    virtual Shoes *CreateShoes() = 0;
    virtual Clothe *CreateClothe() = 0;
    virtual ~Factory() {}
};

// 耐克生产者/生产链
class NiKeProducer : public Factory
{
public:
    Shoes *CreateShoes()
    {
        return new NiKeShoes();
    }

    Clothe *CreateClothe()
    {
        return new NiKeClothe();
    }
};
```

### 单例模式

单例模式是指在整个系统生命周期内，保证一个类只能产生一个实例，确保该类的唯一性。

单例类的特点：

1. 构造函数和析构函数为私有类型，目的是禁止外部构造和析构。
2. 拷贝构造函数和赋值构造函数是私有类型，目的是禁止外部拷贝和赋值，确保实例的唯一性。
3. 类中有一个获取实例的静态方法，可以全局访问。

```cpp
class Single
{

public:
    // 获取单实例对象
    static Single& GetInstance();

private:
    // 禁止外部构造
    Single();

    // 禁止外部析构
    ~Single();

    // 禁止外部拷贝构造
    Single(const Single &single) = delete;

    // 禁止外部赋值操作
    const Single &operator=(const Single &single) = delete;
};

Single& Single::GetInstance()
{
    /**
     * 局部静态特性的方式实现单实例。
     * 静态局部变量只在当前函数内有效，其他函数无法访问。
     * 静态局部变量只在第一次被调用的时候初始化，也存储在静态存储区，生命周期从第一次被初始化起至程序结束止。
     * 静态局部变量是线程安全的
     */
    static Single single;
    return single;
}
```

### 建造者模式

将一个复杂对象的构造与它的表示分离，使同样的构建过程可以创建不同的表示。

实现要点：

1. 抽象建造者类: 为创建产品各个部分，统一抽象接口；
2. 具体建造者类：具体实现抽象建造者各个部件的接口；
3. 抽象产品类（可选）：为产品提供统一接口；
4. 具体产品类:实现抽象产品类的接口；
5. **指挥类**：负责安排和调度复杂对象的各个建造过程。

```cpp
/*
1. 抽象产品类
*/
class AbstractProduct
{
  public:
	virtual ~AbstractProduct() {}
	virtual void setDisplay(string display) = 0;
	virtual void setHost(string host) = 0;
	virtual void setKeyBoard(string KeyBoard) = 0;
	virtual void setMouse(string mouse) = 0;
	virtual void show() = 0;
};

/*
2. 具体产品类
*/
class Computer:public AbstractProduct
{
  public:
	~Computer() {}
	void setDisplay(string display)
	{
		m_vec.emplace_back(display);
	}
	void setHost(string host)
	{
		m_vec.emplace_back(host);
	}
	void setKeyBoard(string KeyBoard)
	{
		m_vec.emplace_back(KeyBoard);
	}
	void setMouse(string mouse)
	{
		m_vec.emplace_back(mouse);
	}
	void show()
	{
		cout << "----------组装电脑---------" << endl;
		for (auto& x : m_vec)
		{
			cout << x << endl;
		}
	}
  private:
	vector<string> m_vec;
};

/*
3. 抽象建造者
*/
class AbstractBuilder
{
  public:
	//创建电脑产品
	AbstractBuilder()
		:product(new Computer) {}
	virtual ~AbstractBuilder() {}
	//抽象电脑产品创建的统一抽象接口
	virtual void BuildDisplay(string display) = 0;
	virtual void BuildHost(string host) = 0;
	virtual void BuildKeyBoard(string KeyBoard) = 0;
	virtual void BuildMouse(string mouse) = 0;
	AbstractProduct* getProduct()
	{
		return product;
	}
  protected:
	AbstractProduct* product;
};

/*
4. 具体建造者：具体实现抽象建造者各个部件的接口
*/
class ComputerBuilder :public AbstractBuilder
{
  public:
	~ComputerBuilder() {}
	void BuildDisplay(string display)
	{
		product->setDisplay(display);
	}
	void BuildHost(string host)
	{
		product->setHost(host);
	}
	void BuildKeyBoard(string KeyBoard)
	{
		product->setKeyBoard(KeyBoard);
	}
	void BuildMouse(string mouse)
	{
		product->setMouse(mouse);
	}
};

/*
5. 指挥者：安排和调度复杂对象的创建过程
*/
class Director
{
  public:
	Director(AbstractBuilder* builder)
		:m_builder(builder) {}
	~Director() {}
	AbstractProduct* createComputer(string display, string host, string KeyBoard, string mouse)
	{
		m_builder->BuildDisplay(display);
		m_builder->BuildHost(host);
		m_builder->BuildKeyBoard(KeyBoard);
		m_builder->BuildMouse(mouse);
		return m_builder->getProduct();
	}
  private:
	AbstractBuilder* m_builder;
};
```

### 原型模式

用原型实例指定创建对象的种类，并通过拷贝这些原型创建新的对象，简单理解就是“克隆指定对象”。

实现要点：

1. 抽象原型类：规定了具体原型对象必须实现的接口；
2. 具体原型类：实现抽象原型类的 clone() 方法，它是可被复制的对象；
3. 访问类：使用具体原型类中的 clone() 方法来复制新的对象。

```cpp
//1. 抽象原型类
class Shape
{
  public:
	virtual ~Shape() {}
	virtual Shape* clone() = 0;
	virtual int getid() = 0;
	virtual string getType() = 0;
  protected:
	string Type;
  private:
	int id;
};

//2. 三个形状具体原型
class Circle :public Shape
{
  public:
	Circle(string Type, int id) :Type(Type), id(id) {}
	~Circle() {}
	//Circle(const Circle& lhs) { Type = lhs.Type, id = lhs.id; }
	Shape* clone() { return new Circle{ *this }; }
	int getid() { return id; }
	string getType() { return Type; }
  protected:
	string Type;
  private:
	int id;
};

class Rectangle :public Shape
{
  public:
	Rectangle(string Type, int id) :Type(Type), id(id) {}
	~Rectangle() {}
	Rectangle(const Rectangle& lhs) { Type = lhs.Type, id = lhs.id; }
	Shape* clone() { return new Rectangle{ *this }; }
	int getid() { return id; }
	string getType() { return Type; }
  protected:
	string Type;
  private:
	int id;
};

class Square :public Shape
{
  public:
	Square(string Type, int id) :Type(Type), id(id) {}
	~Square() {}
	Square(const Square& lhs) { Type = lhs.Type, id = lhs.id; }
	Shape* clone() { return new Square{ *this }; }
	int getid() { return id; }
	string getType() { return Type; }
  protected:
	string Type;
  private:
	int id;
};

//3. 存储对象种类的数据库
class ShapeType
{
  public:
	~ShapeType()
	{
		for (auto& x : ShapeMap)
		{
			delete x.second;
			x.second = nullptr;
		}
	}
	//构造原始对象
	ShapeType()
	{
		Circle* circle = new Circle{ "圆形",1 };
		Square* square = new Square{"正方形",2};
		Rectangle* rectangle = new Rectangle{"矩形",3};
		ShapeMap.emplace(circle->getType(), circle);
		ShapeMap.emplace(square->getType(), square);
		ShapeMap.emplace(rectangle->getType(), rectangle);
	}
	//根据你所需要的种类来获得克隆对象
	Shape* getShape(string Type)
	{
		return ShapeMap[Type]->clone();
	}
  private:
	unordered_map<string, Shape*> ShapeMap;
};
```

## 结构型模式

结构型模式涉及如何组合类和对象以获得更大的结构。

### 适配器模式

适配器模式用于将已有接口转换为调用者所期望的另一种接口，让特定的 API 接口可以适配多种场景。

主要组件：

1. 目标接口(Target)：提供给外部程序的统一接口，是外部调用者(client)期望使用的接口。
2. 源接口(Adaptee)：已经具备一定的功能，但是与 Target 不兼容的接口。它包含了 client 所需要的功能，但是不能被 client 所使用。
3. 适配器(Adapter)：对源接口进行适配，使得源接口可以像目标接口一样被公共调用的类。该类提供了 Target 的接口实现，并通过继承或组合的方式调用了 Adaptee 的接口。

适配器的分类：

1. 类适配器：同时继承了目标接口和源接口，从而使得源接口的函数可以被目标接口所调用。
2. 对象适配器：以对象组合的方式适配不兼容的源接口。所谓的对象组合，是指在一个对象内部调用另一个对象的成员函数。

代码实现（对象适配器）：

```cpp
class Target
{
  public:
    virtual void request() = 0;
};

class Adaptee
{
  public:
    void specificRequest()
    {
        std::cout << "Adaptee specific request" << std::endl;
    }
};

class Adapter : public Target
{
  public:
    Adapter(Adaptee* adaptee) : m_adaptee(adaptee) {}
    void request() override
    {
        m_adaptee->specificRequest();
    }
  private:
    Adaptee* m_adaptee;
};
```

### 装饰器模式

### 代理模式

### 外观模式

### 桥接模式

桥接模式的核心思想是分离抽象和实现。通过提供一个桥接结构，使得抽象层和实现层不直接交互，而是通过桥接接口来进行通信，从而实现解耦。

主要组件：

1. 抽象接口；
2. 具体实现。

代码实现：

```cpp
// Implementor
class Device {
  public:
    virtual bool isEnabled() = 0;
    virtual void enable() = 0;
    virtual void disable() = 0;
    virtual ~Device() {}
};

// Concrete Implementor 1
class TV : public Device {
    bool on = false;
  public:
    bool isEnabled() override {
        return on;
    }

    void enable() override {
        on = true;
        std::cout << "TV is turned on.\n";
    }

    void disable() override {
        on = false;
        std::cout << "TV is turned off.\n";
    }
};

// Concrete Implementor 2
class Radio : public Device {
    bool on = false;
  public:
    bool isEnabled() override {
        return on;
    }

    void enable() override {
        on = true;
        std::cout << "Radio is turned on.\n";
    }

    void disable() override {
        on = false;
        std::cout << "Radio is turned off.\n";
    }
};
```

### 组合模式

它将对象组合成树状结构来表示“部分-整体”的层次关系。

主要组件：

1. 组件（Component）：组合模式的“根节点”，定义组合中所有对象的通用接口，可以是抽象类或接口。该类中定义了子类的共性内容。
2. 叶子（Leaf）：实现了 Component 接口的叶子节点，表示组合中的叶子对象。
3. 合成（Composite）：作用是存储子部件，并且在 Composite 中实现了对子部件的相关操作，⽐如添加、删除、获取子组件等。

### 享元模式

## 行为模式

行为模式涉及到算法和对象之间职责的分配。行为模式不仅描述类和对象的模式，还描述它们之间的通信模式。

### 策略模式

### 模板方法模式

### 观察者模式

观察者模式（Observer Pattern）提供了一种一对多的模式，多个观察者对象同时监听一个主题对象，一旦主题对象发生变化，能够自动通知所有的观察者对象。它提供了一种关联对象之间的同步机制，他们之间通过通信来保持状态同步。

有两大类（主题和观察者）一共四个角色：

- Subject：抽象主题角色，被观察的对象，当被观察的状态发生变化时，会通知所有的观察者，Subject 一般包含了所有观察者对象的引用（集合）；
- ConcreteSubject：具体主题，被观察者的具体实现，当状态发生改变时，向所有观察者发出通知；
- Observer：抽象观察者，提供统一的接口，在得到通知时执行某些操作；
- ConcreteObserver：具体观察者，实现抽象观察者提供的接口；

![observer](img/observer.png)

实现要点：

1. 被观察者提供注册和注销的接口，用指针数组存储注册的观察者；
2. 被观察者提供通知接口（Notify），当发生变化时循环遍历注册的观察者，进行通知；
3. 观察者实现收到通知的行为。

```cpp
//
//观察者模式
//

class Observer;
//抽象被观察者
class Subject {
public:
    Subject() : m_nState(0) {}

    virtual ~Subject() = default;

    virtual void Attach(const std::shared_ptr<Observer> pObserver) = 0;

    virtual void Detach(const std::shared_ptr<Observer> pObserver) = 0;

    virtual void Notify() = 0;

    virtual int GetState() { return m_nState; }

    void SetState(int state) {
        std::cout << "Subject updated !" << std::endl;
        m_nState = state;
    }

protected:
    std::list<std::shared_ptr<Observer>> m_pObserver_list;
    int m_nState;
};

//抽象观察者
class Observer {
public:
    virtual ~Observer() = default;

    Observer(const std::shared_ptr<Subject> pSubject, const std::string &name = "unknown")
            : m_pSubject(pSubject), m_strName(name) {}

    virtual void Update() = 0;

    virtual const std::string &name() { return m_strName; }

protected:
    std::shared_ptr<Subject> m_pSubject;
    std::string m_strName;
};

//具体被观察者
class ConcreteSubject : public Subject {
public:
    void Attach(const std::shared_ptr<Observer> pObserver) override {
        auto iter = std::find(m_pObserver_list.begin(), m_pObserver_list.end(), pObserver);
        if (iter == m_pObserver_list.end()) {
            std::cout << "Attach observer" << pObserver->name() << std::endl;
            m_pObserver_list.emplace_back(pObserver);
        }

    }

    void Detach(const std::shared_ptr<Observer> pObserver) override {
        std::cout << "Detach observer" << pObserver->name() << std::endl;
        m_pObserver_list.remove(pObserver);
    }

    //循环通知所有观察者
    void Notify() override {
        auto it = m_pObserver_list.begin();
        while (it != m_pObserver_list.end()) {
            (*it++)->Update();
        }
    }
};


//具体观察者1
class Observer1 : public Observer {
public:
    Observer1(const std::shared_ptr<Subject> pSubject, const std::string &name = "unknown")
            : Observer(pSubject, name) {}

    void Update() override {
        std::cout << "Observer1_" << m_strName << " get the update.New state is: "
                  << m_pSubject->GetState() << std::endl;
    }
};

//具体观察者2
class Observer2 : public Observer {
public:
    Observer2(const std::shared_ptr<Subject> pSubject, const std::string &name = "unknown")
            : Observer(pSubject, name) {}

    void Update() override {
        std::cout << "Observer2_" << m_strName << " get the update.New state is: "
                  << m_pSubject->GetState() << std::endl;
    }
};
```

### 迭代子模式

### 责任链模式

### 命令模式

### 备忘录模式

### 状态模式

### 访问者模式

### 中介者模式

### 解释器模式
