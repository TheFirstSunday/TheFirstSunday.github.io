---
title: design patterns
date: 2020-01-28 12:08:36
categories: 'Diary'
tags: design patterns
---

## 设计模式

设计模式是软件设计中常见问题的典型解决方案。 每个模式就像一张蓝图， 你可以通过对其进行定制来解决代码中的特定设计问题。

## 设计模式分类

不同设计模式的复杂程度、 细节层次以及在整个系统中的应用范围等方面各不相同。 我喜欢将其类比于道路的建造： 如果你希望让十字路口更加安全， 那么可以安装一些交通信号灯， 或者修建包含行人地下通道在内的多层互通式立交桥。
最基础的、 底层的模式通常被称为惯用技巧。 这类模式一般只能在一种编程语言中使用。
最通用的、 高层的模式是构架模式。 开发者可以在任何编程语言中使用这类模式。 与其他模式不同， 它们可用于整个应用程序的架构设计。
此外， 所有模式可以根据其意图或目的来分类。 三种主要的模式类别：

- 创建型模式提供创建对象的机制， 增加已有代码的灵活性和可复用性。
- 结构型模式介绍如何将对象和类组装成较大的结构， 并同时保持结构的灵活和高效。
- 行为模式负责对象间的高效沟通和职责委派。

### 创建型模式

- 工厂模式: 工厂方法模式是一种创建型设计模式， 其在父类中提供一个创建对象的方法， 允许子类决定实例化对象的类型。

 工厂方法模式优缺点:
    优点:
      1. 你可以避免创建者和具体产品之间的紧密耦合。
      2. 单一职责原则。你可以将产品创建代码放在程序的单一位置， 从而使得代码更容易维护。
      3. 开闭原则。 无需更改现有客户端代码， 你就可以在程序中引入新的产品类型。
    缺点:
      1.应用工厂方法模式需要引入许多新的子类， 代码可能会因此变得更复杂。 最好的情况是将该模式引入创建者类的现有层次结构中。

``` typescript

    /**
     * The Creator class declares the factory method that is supposed to return an
     * object of a Product class. The Creator's subclasses usually provide the
     * implementation of this method.
     */
    abstract class Creator {
        /**
         * Note that the Creator may also provide some default implementation of the
         * factory method.
         */
        public abstract factoryMethod(): Product;

        /**
         * Also note that, despite its name, the Creator's primary responsibility is
         * not creating products. Usually, it contains some core business logic that
         * relies on Product objects, returned by the factory method. Subclasses can
         * indirectly change that business logic by overriding the factory method
         * and returning a different type of product from it.
         */
        public someOperation(): string {
            // Call the factory method to create a Product object.
            const product = this.factoryMethod();
            // Now, use the product.
            return `Creator: The same creator's code has just worked with ${product.operation()}`;
        }
    }

    /**
     * Concrete Creators override the factory method in order to change the
     * resulting product's type.
     */
    class ConcreteCreator1 extends Creator {
        /**
         * Note that the signature of the method still uses the abstract product
         * type, even though the concrete product is actually returned from the
         * method. This way the Creator can stay independent of concrete product
         * classes.
         */
        public factoryMethod(): Product {
            return new ConcreteProduct1();
        }
    }

    class ConcreteCreator2 extends Creator {
        public factoryMethod(): Product {
            return new ConcreteProduct2();
        }
    }

    /**
     * The Product interface declares the operations that all concrete products must
     * implement.
     */
    interface Product {
        operation(): string;
    }

    /**
     * Concrete Products provide various implementations of the Product interface.
     */
    class ConcreteProduct1 implements Product {
        public operation(): string {
            return '{Result of the ConcreteProduct1}';
        }
    }

    class ConcreteProduct2 implements Product {
        public operation(): string {
            return '{Result of the ConcreteProduct2}';
        }
    }

    /**
     * The client code works with an instance of a concrete creator, albeit through
     * its base interface. As long as the client keeps working with the creator via
     * the base interface, you can pass it any creator's subclass.
     */
    function clientCode(creator: Creator) {
        // ...
        console.log('Client: I\'m not aware of the creator\'s class, but it still works.');
        console.log(creator.someOperation());
        // ...
    }

    /**
     * The Application picks a creator's type depending on the configuration or
     * environment.
     */
    console.log('App: Launched with the ConcreteCreator1.');
    clientCode(new ConcreteCreator1());
    console.log('');

    console.log('App: Launched with the ConcreteCreator2.');
    clientCode(new ConcreteCreator2());


    OUTPUT: 执行结果
    App: Launched with the ConcreteCreator1.
    Client: I'm not aware of the creator's class, but it still works.
    Creator: The same creator's code has just worked with {Result of the ConcreteProduct1}

    App: Launched with the ConcreteCreator2.
    Client: I'm not aware of the creator's class, but it still works.
    Creator: The same creator's code has just worked with {Result of the ConcreteProduct2}

```

- 抽象工厂模式: 抽象工厂模式是一种创建型设计模式， 它能创建一系列相关的对象， 而无需指定其具体类。

抽象工厂模式优缺点:
    优点:
        1.你可以确保同一工厂生成的产品相互匹配。
        2.你可以避免客户端和具体产品代码的耦合。
        3.单一职责原则。 你可以将产品生成代码抽取到同一位置， 使得代码易于维护。
        4.开闭原则。 向应用程序中引入新产品变体时， 你无需修改客户端代码。
    缺点:
        1.由于采用该模式需要向应用中引入众多接口和类， 代码可能会比之前更加复杂。

``` typescript
    /**
     * The Abstract Factory interface declares a set of methods that return
     * different abstract products. These products are called a family and are
     * related by a high-level theme or concept. Products of one family are usually
     * able to collaborate among themselves. A family of products may have several
     * variants, but the products of one variant are incompatible with products of
     * another.
     */
    interface AbstractFactory {
        createProductA(): AbstractProductA;

        createProductB(): AbstractProductB;
    }

    /**
     * Concrete Factories produce a family of products that belong to a single
     * variant. The factory guarantees that resulting products are compatible. Note
     * that signatures of the Concrete Factory's methods return an abstract product,
     * while inside the method a concrete product is instantiated.
     */
    class ConcreteFactory1 implements AbstractFactory {
        public createProductA(): AbstractProductA {
            return new ConcreteProductA1();
        }

        public createProductB(): AbstractProductB {
            return new ConcreteProductB1();
        }
    }

    /**
     * Each Concrete Factory has a corresponding product variant.
     */
    class ConcreteFactory2 implements AbstractFactory {
        public createProductA(): AbstractProductA {
            return new ConcreteProductA2();
        }

        public createProductB(): AbstractProductB {
            return new ConcreteProductB2();
        }
    }
    
    /**
     * Each distinct product of a product family should have a base interface. All
     * variants of the product must implement this interface.
     */
    interface AbstractProductA {
        usefulFunctionA(): string;
    }

    /**
     * These Concrete Products are created by corresponding Concrete Factories.
     */
    class ConcreteProductA1 implements AbstractProductA {
        public usefulFunctionA(): string {
            return 'The result of the product A1.';
        }
    }

    class ConcreteProductA2 implements AbstractProductA {
        public usefulFunctionA(): string {
            return 'The result of the product A2.';
        }
    }

    /**
     * Here's the the base interface of another product. All products can interact
     * with each other, but proper interaction is possible only between products of
     * the same concrete variant.
     */
    interface AbstractProductB {
        /**
         * Product B is able to do its own thing...
         */
        usefulFunctionB(): string;

        /**
         * ...but it also can collaborate with the ProductA.
         *
         * The Abstract Factory makes sure that all products it creates are of the
         * same variant and thus, compatible.
         */
        anotherUsefulFunctionB(collaborator: AbstractProductA): string;
    }

    /**
     * These Concrete Products are created by corresponding Concrete Factories.
     */
    class ConcreteProductB1 implements AbstractProductB {

        public usefulFunctionB(): string {
            return 'The result of the product B1.';
        }

        /**
         * The variant, Product B1, is only able to work correctly with the variant,
         * Product A1. Nevertheless, it accepts any instance of AbstractProductA as
         * an argument.
         */
        public anotherUsefulFunctionB(collaborator: AbstractProductA): string {
            const result = collaborator.usefulFunctionA();
            return `The result of the B1 collaborating with the (${result})`;
        }
    }

    class ConcreteProductB2 implements AbstractProductB {

        public usefulFunctionB(): string {
            return 'The result of the product B2.';
        }

        /**
         * The variant, Product B2, is only able to work correctly with the variant,
         * Product A2. Nevertheless, it accepts any instance of AbstractProductA as
         * an argument.
         */
        public anotherUsefulFunctionB(collaborator: AbstractProductA): string {
            const result = collaborator.usefulFunctionA();
            return `The result of the B2 collaborating with the (${result})`;
        }
    }
    /**
     * The client code works with factories and products only through abstract
     * types: AbstractFactory and AbstractProduct. This lets you pass any factory or
     * product subclass to the client code without breaking it.
     */
    function clientCode(factory: AbstractFactory) {
        const productA = factory.createProductA();
        const productB = factory.createProductB();

        console.log(productB.usefulFunctionB());
        console.log(productB.anotherUsefulFunctionB(productA));
    }

    /**
     * The client code can work with any concrete factory class.
     */
    console.log('Client: Testing client code with the first factory type...');
    clientCode(new ConcreteFactory1());

    console.log('');

    console.log('Client: Testing the same client code with the second factory type...');
    clientCode(new ConcreteFactory2());

    Output: 执行结果
    Client: Testing client code with the first factory type...
    The result of the product B1.
    The result of the B1 collaborating with the (The result of the product A1.)

    Client: Testing the same client code with the second factory type...
    The result of the product B2.
    The result of the B2 collaborating with the (The result of the product A2.)
```

- 生成器模式: 生成器模式是一种创建型设计模式， 使你能够分步骤创建复杂对象。 该模式允许你使用相同的创建代码生成不同类型和形式的对象。
  
 生成器模式优缺点:
    优点:
        1.你可以分步创建对象， 暂缓创建步骤或递归运行创建步骤。
        2.生成不同形式的产品时， 你可以复用相同的制造代码。
        3.单一职责原则。 你可以将复杂构造代码从产品的业务逻辑中分离出来。
    缺点:
        由于该模式需要新增多个类， 因此代码整体复杂程度会有所增加。

``` typescript
    /**
     * The Builder interface specifies methods for creating the different parts of
     * the Product objects.
     */
    interface Builder {
        producePartA(): void;
        producePartB(): void;
        producePartC(): void;
    }

    /**
     * The Concrete Builder classes follow the Builder interface and provide
     * specific implementations of the building steps. Your program may have several
     * variations of Builders, implemented differently.
     */

    class ConcreteBuilder1 implements Builder {
        private product: Product1;

        /**
         * A fresh builder instance should contain a blank product object, which is
         * used in further assembly.
         */
        constructor() {
            this.reset();
        }

        public reset(): void {
            this.product = new Product1();
        }

        /**
         * All production steps work with the same product instance.
         */
        public producePartA(): void {
            this.product.parts.push('PartA1');
        }

        public producePartB(): void {
            this.product.parts.push('PartB1');
        }

        public producePartC(): void {
            this.product.parts.push('PartC1');
        }
        /**
         * Concrete Builders are supposed to provide their own methods for
         * retrieving results. That's because various types of builders may create
         * entirely different products that don't follow the same interface.
         * Therefore, such methods cannot be declared in the base Builder interface
         * (at least in a statically typed programming language).
         *
         * Usually, after returning the end result to the client, a builder instance
         * is expected to be ready to start producing another product. That's why
         * it's a usual practice to call the reset method at the end of the
         * `getProduct` method body. However, this behavior is not mandatory, and
         * you can make your builders wait for an explicit reset call from the
         * client code before disposing of the previous result.
         */
        public getProduct(): Product1 {
            const result = this.product;
            this.reset();
            return result;
        }
    }

    /**
     * It makes sense to use the Builder pattern only when your products are quite
     * complex and require extensive configuration.
     *
     * Unlike in other creational patterns, different concrete builders can produce
     * unrelated products. In other words, results of various builders may not
     * always follow the same interface.
     */
    class Product1 {
        public parts: string[] = [];

        public listParts(): void {
            console.log(`Product parts: ${this.parts.join(', ')}\n`);
        }
    }

    /**
     * The Director is only responsible for executing the building steps in a
     * particular sequence. It is helpful when producing products according to a
     * specific order or configuration. Strictly speaking, the Director class is
     * optional, since the client can control builders directly.
     */
    class Director {
        private builder: Builder;

        /**
         * The Director works with any builder instance that the client code passes
         * to it. This way, the client code may alter the final type of the newly
         * assembled product.
         */
        public setBuilder(builder: Builder): void {
            this.builder = builder;
        }

        /**
         * The Director can construct several product variations using the same
         * building steps.
         */
        public buildMinimalViableProduct(): void {
            this.builder.producePartA();
        }

        public buildFullFeaturedProduct(): void {
            this.builder.producePartA();
            this.builder.producePartB();
            this.builder.producePartC();
        }
    }

    /**
     * The client code creates a builder object, passes it to the director and then
     * initiates the construction process. The end result is retrieved from the
     * builder object.
     */
    function clientCode(director: Director) {
        const builder = new ConcreteBuilder1();
        director.setBuilder(builder);

        console.log('Standard basic product:');
        director.buildMinimalViableProduct();
        builder.getProduct().listParts();

        console.log('Standard full featured product:');
        director.buildFullFeaturedProduct();
        builder.getProduct().listParts();

        // Remember, the Builder pattern can be used without a Director class.
        console.log('Custom product:');
        builder.producePartA();
        builder.producePartC();
        builder.getProduct().listParts();
    }

    const director = new Director();
    clientCode(director);

    Output: 执行结果
    Standard basic product:
    Product parts: PartA1

    Standard full featured product:
    Product parts: PartA1, PartB1, PartC1

    Custom product:
    Product parts: PartA1, PartC1

```

- 原型模式: 原型模式是一种创建型设计模式， 使你能够复制已有对象， 而又无需使代码依赖它们所属的类。

原型模式优缺点:
    优点:
        1.你可以克隆对象， 而无需与它们所属的具体类相耦合。
        2.你可以克隆预生成原型， 避免反复运行初始化代码。
        3.你可以更方便地生成复杂对象。
        4.你可以用继承以外的方式来处理复杂对象的不同配置。
    缺点:
        1.克隆包含循环引用的复杂对象可能会非常麻烦。

``` typescript
    /**
     * The example class that has cloning ability. We'll see how the values of field
     * with different types will be cloned.
     */
    class Prototype {
        public primitive: any;
        public component: object;
        public circularReference: ComponentWithBackReference;

        public clone(): this {
            const clone = Object.create(this);

            clone.component = Object.create(this.component);

            // Cloning an object that has a nested object with backreference
            // requires special treatment. After the cloning is completed, the
            // nested object should point to the cloned object, instead of the
            // original object. Spread operator can be handy for this case.
            clone.circularReference = {
                ...this.circularReference,
                prototype: { ...this },
            };

            return clone;
        }
    }

    class ComponentWithBackReference {
        public prototype;

        constructor(prototype: Prototype) {
            this.prototype = prototype;
        }
    }
    /**
    * The client code.
    */
    function clientCode() {
        const p1 = new Prototype();
        p1.primitive = 245;
        p1.component = new Date();
        p1.circularReference = new ComponentWithBackReference(p1);

        const p2 = p1.clone();
        if (p1.primitive === p2.primitive) {
            console.log('Primitive field values have been carried over to a clone. Yay!');
        } else {
            console.log('Primitive field values have not been copied. Booo!');
        }
        if (p1.component === p2.component) {
            console.log('Simple component has not been cloned. Booo!');
        } else {
            console.log('Simple component has been cloned. Yay!');
        }

        if (p1.circularReference === p2.circularReference) {
            console.log('Component with back reference has not been cloned. Booo!');
        } else {
            console.log('Component with back reference has been cloned. Yay!');
        }

        if (p1.circularReference.prototype === p2.circularReference.prototype) {
            console.log('Component with back reference is linked to original object. Booo!');
        } else {
            console.log('Component with back reference is linked to the clone. Yay!');
        }
    }

    clientCode();

    Output: 执行结果
    Primitive field values have been carried over to a clone. Yay!
    Simple component has been cloned. Yay!
    Component with back reference has been cloned. Yay!
    Component with back reference is linked to the clone. Yay!
```

- 单例模式: 单例模式是一种创建型设计模式， 让你能够保证一个类只有一个实例， 并提供一个访问该实例的全局节点。
  
  单例模式的优缺点:
    优点:  
        1.你可以保证一个类只有一个实例。
        2.你获得了一个指向该实例的全局访问节点。
        3.仅在首次请求单例对象时对其进行初始化。
    缺点:
        1.违反了_单一职责原则_。 该模式同时解决了两个问题。
        2.单例模式可能掩盖不良设计， 比如程序各组件之间相互了解过多等。
        3.该模式在多线程环境下需要进行特殊处理， 避免多个线程多次创建单例对象。
        4.单例的客户端代码单元测试可能会比较困难， 因为许多测试框架以基于继承的方式创建模拟对象。 由于单例类的构造函数是私有的， 而且绝大部分语言无法重写静态方法， 所以你需要想出仔细考虑模拟单例的方法。 要么干脆不编写测试代码， 或者不使用单例模式。

``` typescript

    /**
     * The Singleton class defines the `getInstance` method that lets clients access
     * the unique singleton instance.
     */
    class Singleton {
        private static instance: Singleton;

        /**
         * The Singleton's constructor should always be private to prevent direct
         * construction calls with the `new` operator.
         */
        private constructor() { }

        /**
         * The static method that controls the access to the singleton instance.
         *
         * This implementation let you subclass the Singleton class while keeping
         * just one instance of each subclass around.
         */
        public static getInstance(): Singleton {
            if (!Singleton.instance) {
                Singleton.instance = new Singleton();
            }

            return Singleton.instance;
        }

        /**
         * Finally, any singleton should define some business logic, which can be
         * executed on its instance.
         */
        public someBusinessLogic() {
            // ...
        }
    }

    /**
     * The client code.
     */
    function clientCode() {
        const s1 = Singleton.getInstance();
        const s2 = Singleton.getInstance();

        if (s1 === s2) {
            console.log('Singleton works, both variables contain the same instance.');
        } else {
            console.log('Singleton failed, variables contain different instances.');
        }
    }

    clientCode();

    Output.txt: 执行结果
    Singleton works, both variables contain the same instance.
```

### 结构性模式

- 适配器模式: 适配器模式是一种结构型设计模式， 它能使接口不兼容的对象能够相互合作。

 适配器模式优缺点:
    优点:
        1._单一职责原则_你可以将接口或数据转换代码从程序主要业务逻辑中分离。
        2.开闭原则。 只要客户端代码通过客户端接口与适配器进行交互， 你就能在不修改现有客户端代码的情况下在程序中添加新类型的适配器。
    缺点:
        1.代码整体复杂度增加， 因为你需要新增一系列接口和类。 有时直接更改服务类使其与其他代码兼容会更简单。

``` typescript

    /**
    * The Target defines the domain-specific interface used by the client code.
    */
    class Target {
        public request(): string {
            return 'Target: The default target\'s behavior.';
        }
    }

    /**
    * The Adaptee contains some useful behavior, but its interface is incompatible
    * with the existing client code. The Adaptee needs some adaptation before the
    * client code can use it.
    */
    class Adaptee {
        public specificRequest(): string {
            return '.eetpadA eht fo roivaheb laicepS';
        }
    }

    /**
    * The Adapter makes the Adaptee's interface compatible with the Target's
    * interface.
    */
    class Adapter extends Target {
        private adaptee: Adaptee;

        constructor(adaptee: Adaptee) {
            super();
            this.adaptee = adaptee;
        }

        public request(): string {
            const result = this.adaptee.specificRequest().split('').reverse().join('');
            return `Adapter: (TRANSLATED) ${result}`;
        }
    }

    /**
    * The client code supports all classes that follow the Target interface.
    */
    function clientCode(target: Target) {
        console.log(target.request());
    }

    console.log('Client: I can work just fine with the Target objects:');
    const target = new Target();
    clientCode(target);

    console.log('');

    const adaptee = new Adaptee();
    console.log('Client: The Adaptee class has a weird interface. See, I don\'t understand it:');
    console.log(`Adaptee: ${adaptee.specificRequest()}`);

    console.log('');

    console.log('Client: But I can work with it via the Adapter:');
    const adapter = new Adapter(adaptee);
    clientCode(adapter);

    Output: 执行结果
    Client: I can work just fine with the Target objects:
    Target: The default target's behavior.

    Client: The Adaptee class has a weird interface. See, I don't understand it:
    Adaptee: .eetpadA eht fo roivaheb laicepS

    Client: But I can work with it via the Adapter:
    Adapter: (TRANSLATED) Special behavior of the Adaptee.
```

- 桥接模式: 桥接模式是一种结构型设计模式， 可将一个大类或一系列紧密相关的类拆分为抽象和实现两个独立的层次结构， 从而能在开发时分别使用。

 桥接模式优缺点:
    优点:
        1.你可以创建与平台无关的类和程序。
        2.客户端代码仅与高层抽象部分进行互动， 不会接触到平台的详细信息。
        3.开闭原则。 你可以新增抽象部分和实现部分， 且它们之间不会相互影响。
        4.单一职责原则。 抽象部分专注于处理高层逻辑， 实现部分处理平台细节。
    缺点:
        1.对高内聚的类使用该模式可能会让代码更加复杂。

``` typescript
    /**
     * The Abstraction defines the interface for the "control" part of the two class
     * hierarchies. It maintains a reference to an object of the Implementation
     * hierarchy and delegates all of the real work to this object.
     */
    class Abstraction {
        protected implementation: Implementation;

        constructor(implementation: Implementation) {
            this.implementation = implementation;
        }

        public operation(): string {
            const result = this.implementation.operationImplementation();
            return `Abstraction: Base operation with:\n${result}`;
        }
    }

    /**
     * You can extend the Abstraction without changing the Implementation classes.
     */
    class ExtendedAbstraction extends Abstraction {
        public operation(): string {
            const result = this.implementation.operationImplementation();
            return `ExtendedAbstraction: Extended operation with:\n${result}`;
        }
    }

    /**
     * The Implementation defines the interface for all implementation classes. It
     * doesn't have to match the Abstraction's interface. In fact, the two
     * interfaces can be entirely different. Typically the Implementation interface
     * provides only primitive operations, while the Abstraction defines higher-
     * level operations based on those primitives.
     */
    interface Implementation {
        operationImplementation(): string;
    }

    /**
     * Each Concrete Implementation corresponds to a specific platform and
     * implements the Implementation interface using that platform's API.
     */
    class ConcreteImplementationA implements Implementation {
        public operationImplementation(): string {
            return 'ConcreteImplementationA: Here\'s the result on the platform A.';
        }
    }

    class ConcreteImplementationB implements Implementation {
        public operationImplementation(): string {
            return 'ConcreteImplementationB: Here\'s the result on the platform B.';
        }
    }

    /**
     * Except for the initialization phase, where an Abstraction object gets linked
     * with a specific Implementation object, the client code should only depend on
     * the Abstraction class. This way the client code can support any abstraction-
     * implementation combination.
     */
    function clientCode(abstraction: Abstraction) {
        // ..

        console.log(abstraction.operation());

        // ..
    }

    /**
     * The client code should be able to work with any pre-configured abstraction-
     * implementation combination.
     */
    let implementation = new ConcreteImplementationA();
    let abstraction = new Abstraction(implementation);
    clientCode(abstraction);

    console.log('');

    implementation = new ConcreteImplementationB();
    abstraction = new ExtendedAbstraction(implementation);
    clientCode(abstraction);

    Output: 执行结果
    Abstraction: Base operation with:
    ConcreteImplementationA: Here's the result on the platform A.

    ExtendedAbstraction: Extended operation with:
    ConcreteImplementationB: Here's the result on the platform B.
```

- 组合模式: 组合模式是一种结构型设计模式， 你可以使用它将对象组合成树状结构， 并且能像使用独立对象一样使用它们。

 组合模式优缺点
    优点:
        1.你可以利用多态和递归机制更方便地使用复杂树结构。
        2.开闭原则。 无需更改现有代码， 你就可以在应用中添加新元素， 使其成为对象树的一部分。
    缺点:
        1.对于功能差异较大的类， 提供公共接口或许会有困难。 在特定情况下， 你需要过度一般化组件接口， 使其变得令人难以理解。
  
``` typescript

/**
 * The base Component class declares common operations for both simple and
 * complex objects of a composition.
 */
abstract class Component {
    protected parent: Component;

    /**
     * Optionally, the base Component can declare an interface for setting and
     * accessing a parent of the component in a tree structure. It can also
     * provide some default implementation for these methods.
     */
    public setParent(parent: Component) {
        this.parent = parent;
    }

    public getParent(): Component {
        return this.parent;
    }

    /**
     * In some cases, it would be beneficial to define the child-management
     * operations right in the base Component class. This way, you won't need to
     * expose any concrete component classes to the client code, even during the
     * object tree assembly. The downside is that these methods will be empty
     * for the leaf-level components.
     */
    public add(component: Component): void { }

    public remove(component: Component): void { }

    /**
     * You can provide a method that lets the client code figure out whether a
     * component can bear children.
     */
    public isComposite(): boolean {
        return false;
    }
    /**
    * The base Component may implement some default behavior or leave it to
    * concrete classes (by declaring the method containing the behavior as
    * "abstract").
    */
    public abstract operation(): string;
}

    /**
     * The Leaf class represents the end objects of a composition. A leaf can't have
     * any children.
     *
     * Usually, it's the Leaf objects that do the actual work, whereas Composite
     * objects only delegate to their sub-components.
     */
    class Leaf extends Component {
        public operation(): string {
            return 'Leaf';
        }
    }

    /**
     * The Composite class represents the complex components that may have children.
     * Usually, the Composite objects delegate the actual work to their children and
     * then "sum-up" the result.
     */
    class Composite extends Component {
        protected children: Component[] = [];

        /**
         * A composite object can add or remove other components (both simple or
         * complex) to or from its child list.
         */
        public add(component: Component): void {
            this.children.push(component);
            component.setParent(this);
        }

        public remove(component: Component): void {
            const componentIndex = this.children.indexOf(component);
            this.children.splice(componentIndex, 1);

            component.setParent(null);
        }

        public isComposite(): boolean {
            return true;
        }

        /**
         * The Composite executes its primary logic in a particular way. It
         * traverses recursively through all its children, collecting and summing
         * their results. Since the composite's children pass these calls to their
         * children and so forth, the whole object tree is traversed as a result.
         */
        public operation(): string {
            const results = [];
            for (const child of this.children) {
                results.push(child.operation());
            }

            return `Branch(${results.join('+')})`;
        }
    }

    /**
     * The client code works with all of the components via the base interface.
     */
    function clientCode(component: Component) {
        // ...

        console.log(`RESULT: ${component.operation()}`);

        // ...
    }

    /**
     * This way the client code can support the simple leaf components...
     */
    const simple = new Leaf();
    console.log('Client: I\'ve got a simple component:');
    clientCode(simple);
    console.log('');

    /**
     * ...as well as the complex composites.
     */
    const tree = new Composite();
    const branch1 = new Composite();
    branch1.add(new Leaf());
    branch1.add(new Leaf());
    const branch2 = new Composite();
    branch2.add(new Leaf());
    tree.add(branch1);
    tree.add(branch2);
    console.log('Client: Now I\'ve got a composite tree:');
    clientCode(tree);
    console.log('');

    /**
     * Thanks to the fact that the child-management operations are declared in the
     * base Component class, the client code can work with any component, simple or
     * complex, without depending on their concrete classes.
     */
    function clientCode2(component1: Component, component2: Component) {
        // ...

        if (component1.isComposite()) {
            component1.add(component2);
        }
        console.log(`RESULT: ${component1.operation()}`);

        // ...
    }

    console.log('Client: I don\'t need to check the components classes even when managing the tree:');
    clientCode2(tree, simple);

    Output: 执行结果
    Client: I've got a simple component:
    RESULT: Leaf

    Client: Now I've got a composite tree:
    RESULT: Branch(Branch(Leaf+Leaf)+Branch(Leaf))

    Client: I don't need to check the components classes even when managing the tree:
    RESULT: Branch(Branch(Leaf+Leaf)+Branch(Leaf)+Leaf)

```

- 装饰模式: 装饰模式是一种结构型设计模式， 允许你通过将对象放入包含行为的特殊封装对象中来为原对象绑定新的行为。

 装饰模式优缺点:
    优点:
        1.你无需创建新子类即可扩展对象的行为。
        2.你可以在运行时添加或删除对象的功能。
        3.你可以用多个装饰封装对象来组合几种行为。
        4.单一职责原则。 你可以将实现了许多不同行为的一个大类拆分为多个较小的类。
    缺点:
        1.在封装器栈中删除特定封装器比较困难。
        2.实现行为不受装饰栈顺序影响的装饰比较困难。
        3.各层的初始化配置代码看上去可能会很糟糕。

``` typescript

    /**
     * The base Component interface defines operations that can be altered by
     * decorators.
     */
    interface Component {
        operation(): string;
    }

    /**
     * Concrete Components provide default implementations of the operations. There
     * might be several variations of these classes.
     */
    class ConcreteComponent implements Component {
        public operation(): string {
            return 'ConcreteComponent';
        }
    }

    /**
     * The base Decorator class follows the same interface as the other components.
     * The primary purpose of this class is to define the wrapping interface for all
     * concrete decorators. The default implementation of the wrapping code might
     * include a field for storing a wrapped component and the means to initialize
     * it.
     */
    class Decorator implements Component {
        protected component: Component;

        constructor(component: Component) {
            this.component = component;
        }

        /**
         * The Decorator delegates all work to the wrapped component.
         */
        public operation(): string {
            return this.component.operation();
        }
    }

    /**
     * Concrete Decorators call the wrapped object and alter its result in some way.
     */
    class ConcreteDecoratorA extends Decorator {
        /**
         * Decorators may call parent implementation of the operation, instead of
         * calling the wrapped object directly. This approach simplifies extension
         * of decorator classes.
         */
        public operation(): string {
            return `ConcreteDecoratorA(${super.operation()})`;
        }
    }

    /**
     * Decorators can execute their behavior either before or after the call to a
     * wrapped object.
     */
    class ConcreteDecoratorB extends Decorator {
        public operation(): string {
            return `ConcreteDecoratorB(${super.operation()})`;
        }
    }

    /**
     * The client code works with all objects using the Component interface. This
     * way it can stay independent of the concrete classes of components it works
     * with.
     */
    function clientCode(component: Component) {
        // ...

        console.log(`RESULT: ${component.operation()}`);

        // ...
    }

    /**
     * This way the client code can support both simple components...
     */
    const simple = new ConcreteComponent();
    console.log('Client: I\'ve got a simple component:');
    clientCode(simple);
    console.log('');

    /**
     * ...as well as decorated ones.
     *
     * Note how decorators can wrap not only simple components but the other
     * decorators as well.
     */
    const decorator1 = new ConcreteDecoratorA(simple);
    const decorator2 = new ConcreteDecoratorB(decorator1);
    console.log('Client: Now I\'ve got a decorated component:');
    clientCode(decorator2);
    Output: 执行结果
    Client: I've got a simple component:
    RESULT: ConcreteComponent

    Client: Now I've got a decorated component:
    RESULT: ConcreteDecoratorB(ConcreteDecoratorA(ConcreteComponent))
```

- 外观模式: 外观模式是一种结构型设计模式， 能为程序库、 框架或其他复杂类提供一个简单的接口。

 外观模式的优缺点:
    优点:
        1. 你可以让自己的代码独立于复杂子系统。
    缺点:
         2.外观可能成为与程序中所有类都耦合的上帝对象。

``` typescript

    /**
     * The Facade class provides a simple interface to the complex logic of one or
     * several subsystems. The Facade delegates the client requests to the
     * appropriate objects within the subsystem. The Facade is also responsible for
     * managing their lifecycle. All of this shields the client from the undesired
     * complexity of the subsystem.
     */
    class Facade {
        protected subsystem1: Subsystem1;

        protected subsystem2: Subsystem2;

        /**
         * Depending on your application's needs, you can provide the Facade with
         * existing subsystem objects or force the Facade to create them on its own.
         */
        constructor(subsystem1: Subsystem1 = null, subsystem2: Subsystem2 = null) {
            this.subsystem1 = subsystem1 || new Subsystem1();
            this.subsystem2 = subsystem2 || new Subsystem2();
        }

        /**
         * The Facade's methods are convenient shortcuts to the sophisticated
         * functionality of the subsystems. However, clients get only to a fraction
         * of a subsystem's capabilities.
         */
        public operation(): string {
            let result = 'Facade initializes subsystems:\n';
            result += this.subsystem1.operation1();
            result += this.subsystem2.operation1();
            result += 'Facade orders subsystems to perform the action:\n';
            result += this.subsystem1.operationN();
            result += this.subsystem2.operationZ();

            return result;
        }
    }

    /**
     * The Subsystem can accept requests either from the facade or client directly.
     * In any case, to the Subsystem, the Facade is yet another client, and it's not
     * a part of the Subsystem.
     */
    class Subsystem1 {
        public operation1(): string {
            return 'Subsystem1: Ready!\n';
        }

        // ...

        public operationN(): string {
            return 'Subsystem1: Go!\n';
        }
    }

    /**
     * Some facades can work with multiple subsystems at the same time.
     */
    class Subsystem2 {
        public operation1(): string {
            return 'Subsystem2: Get ready!\n';
        }

        // ...

        public operationZ(): string {
            return 'Subsystem2: Fire!';
        }
    }

    /**
     * The client code works with complex subsystems through a simple interface
     * provided by the Facade. When a facade manages the lifecycle of the subsystem,
     * the client might not even know about the existence of the subsystem. This
     * approach lets you keep the complexity under control.
     */
    function clientCode(facade: Facade) {
        // ...

        console.log(facade.operation());

        // ...
    }

    /**
     * The client code may have some of the subsystem's objects already created. In
     * this case, it might be worthwhile to initialize the Facade with these objects
     * instead of letting the Facade create new instances.
     */
    const subsystem1 = new Subsystem1();
    const subsystem2 = new Subsystem2();
    const facade = new Facade(subsystem1, subsystem2);
    clientCode(facade);
    Output: 执行结果
    Facade initializes subsystems:
    Subsystem1: Ready!
    Subsystem2: Get ready!
    Facade orders subsystems to perform the action:
    Subsystem1: Go!
    Subsystem2: Fire!

```

- 享元模式: 享元模式是一种结构型设计模式， 它摒弃了在每个对象中保存所有数据的方式， 通过共享多个对象所共有的相同状态， 让你能在有限的内存容量中载入更多对象。

 享元模式的优缺点:
    优点:
        1. 如果程序中有很多相似对象， 那么你将可以节省大量内存。
    缺点:
        1. 你可能需要牺牲执行速度来换取内存， 因为他人每次调用享元方法时都需要重新计算部分情景数据。
        2. 代码会变得更加复杂。 团队中的新成员总是会问： ​ “为什么要像这样拆分一个实体的状态？”。

``` typescript
    /**
     * The Flyweight stores a common portion of the state (also called intrinsic
     * state) that belongs to multiple real business entities. The Flyweight accepts
     * the rest of the state (extrinsic state, unique for each entity) via its
     * method parameters.
     */
    class Flyweight {
        private sharedState: any;

        constructor(sharedState: any) {
            this.sharedState = sharedState;
        }

        public operation(uniqueState): void {
            const s = JSON.stringify(this.sharedState);
            const u = JSON.stringify(uniqueState);
            console.log(`Flyweight: Displaying shared (${s}) and unique (${u}) state.`);
        }
    }

    /**
     * The Flyweight Factory creates and manages the Flyweight objects. It ensures
     * that flyweights are shared correctly. When the client requests a flyweight,
     * the factory either returns an existing instance or creates a new one, if it
     * doesn't exist yet.
     */
    class FlyweightFactory {
        private flyweights: {[key: string]: Flyweight} = <any>{};

        constructor(initialFlyweights: string[][]) {
            for (const state of initialFlyweights) {
                this.flyweights[this.getKey(state)] = new Flyweight(state);
            }
        }

        /**
         * Returns a Flyweight's string hash for a given state.
         */
        private getKey(state: string[]): string {
            return state.join('_');
        }

        /**
         * Returns an existing Flyweight with a given state or creates a new one.
         */
        public getFlyweight(sharedState: string[]): Flyweight {
            const key = this.getKey(sharedState);

            if (!(key in this.flyweights)) {
                console.log('FlyweightFactory: Can\'t find a flyweight, creating new one.');
                this.flyweights[key] = new Flyweight(sharedState);
            } else {
                console.log('FlyweightFactory: Reusing existing flyweight.');
            }

            return this.flyweights[key];
        }

        public listFlyweights(): void {
            const count = Object.keys(this.flyweights).length;
            console.log(`\nFlyweightFactory: I have ${count} flyweights:`);
            for (const key in this.flyweights) {
                console.log(key);
            }
        }
    }

    /**
    * The client code usually creates a bunch of pre-populated flyweights in the
    * initialization stage of the application.
    */
    const factory = new FlyweightFactory([
        ['Chevrolet', 'Camaro2018', 'pink'],
        ['Mercedes Benz', 'C300', 'black'],
        ['Mercedes Benz', 'C500', 'red'],
        ['BMW', 'M5', 'red'],
        ['BMW', 'X6', 'white'],
        // ...
    ]);
    factory.listFlyweights();

    // ...

    function addCarToPoliceDatabase(
        ff: FlyweightFactory, plates: string, owner: string,
        brand: string, model: string, color: string,
    ) {
        console.log('\nClient: Adding a car to database.');
        const flyweight = ff.getFlyweight([brand, model, color]);

        // The client code either stores or calculates extrinsic state and passes it
        // to the flyweight's methods.
        flyweight.operation([plates, owner]);
    }

    addCarToPoliceDatabase(factory, 'CL234IR', 'James Doe', 'BMW', 'M5', 'red');

    addCarToPoliceDatabase(factory, 'CL234IR', 'James Doe', 'BMW', 'X1', 'red');

    factory.listFlyweights();
    Output: 执行结果
    FlyweightFactory: I have 5 flyweights:
    Chevrolet_Camaro2018_pink
    Mercedes Benz_C300_black
    Mercedes Benz_C500_red
    BMW_M5_red
    BMW_X6_white

    Client: Adding a car to database.
    FlyweightFactory: Reusing existing flyweight.
    Flyweight: Displaying shared (["BMW","M5","red"]) and unique (["CL234IR","James Doe"]) state.

    Client: Adding a car to database.
    FlyweightFactory: Can't find a flyweight, creating new one.
    Flyweight: Displaying shared (["BMW","X1","red"]) and unique (["CL234IR","James Doe"]) state.

    FlyweightFactory: I have 6 flyweights:
    Chevrolet_Camaro2018_pink
    Mercedes Benz_C300_black
    Mercedes Benz_C500_red
    BMW_M5_red
    BMW_X6_white
    BMW_X1_red
```

- 代理模式: 代理模式是一种结构型设计模式， 让你能够提供对象的替代品或其占位符。 代理控制着对于原对象的访问， 并允许在将请求提交给对象前后进行一些处理。
  
  代理模式优缺点:
    优点:
        1.你可以在客户端毫无察觉的情况下控制服务对象。
        2.如果客户端对服务对象的生命周期没有特殊要求， 你可以对生命周期进行管理。
        3.4.即使服务对象还未准备好或不存在， 代理也可以正常工作。
        开闭原则。 你可以在不对服务或客户端做出修改的情况下创建新代理。

    缺点:
        1.代码可能会变得复杂， 因为需要新建许多类。
        2.服务响应可能会延迟。

``` typescript
    /**
     * The Subject interface declares common operations for both RealSubject and the
     * Proxy. As long as the client works with RealSubject using this interface,
     * you'll be able to pass it a proxy instead of a real subject.
     */
    interface Subject {
        request(): void;
    }

    /**
     * The RealSubject contains some core business logic. Usually, RealSubjects are
     * capable of doing some useful work which may also be very slow or sensitive -
     * e.g. correcting input data. A Proxy can solve these issues without any
     * changes to the RealSubject's code.
     */
    class RealSubject implements Subject {
        public request(): void {
            console.log('RealSubject: Handling request.');
        }
    }

    /**
     * The Proxy has an interface identical to the RealSubject.
     */
    class Proxy implements Subject {
        private realSubject: RealSubject;

        /**
         * The Proxy maintains a reference to an object of the RealSubject class. It
         * can be either lazy-loaded or passed to the Proxy by the client.
         */
        constructor(realSubject: RealSubject) {
            this.realSubject = realSubject;
        }

        /**
         * The most common applications of the Proxy pattern are lazy loading,
         * caching, controlling the access, logging, etc. A Proxy can perform one of
         * these things and then, depending on the result, pass the execution to the
         * same method in a linked RealSubject object.
         */
        public request(): void {
            if (this.checkAccess()) {
                this.realSubject.request();
                this.logAccess();
            }
        }

        private checkAccess(): boolean {
            // Some real checks should go here.
            console.log('Proxy: Checking access prior to firing a real request.');

            return true;
        }

        private logAccess(): void {
            console.log('Proxy: Logging the time of request.');
        }
    }

    /**
     * The client code is supposed to work with all objects (both subjects and
     * proxies) via the Subject interface in order to support both real subjects and
     * proxies. In real life, however, clients mostly work with their real subjects
     * directly. In this case, to implement the pattern more easily, you can extend
     * your proxy from the real subject's class.
     */
    function clientCode(subject: Subject) {
        // ...

        subject.request();

        // ...
    }

    console.log('Client: Executing the client code with a real subject:');
    const realSubject = new RealSubject();
    clientCode(realSubject);

    console.log('');

    console.log('Client: Executing the same client code with a proxy:');
    const proxy = new Proxy(realSubject);
    clientCode(proxy);

    Output: 执行结果
    Client: Executing the client code with a real subject:
    RealSubject: Handling request.

    Client: Executing the same client code with a proxy:
    Proxy: Checking access prior to firing a real request.
    RealSubject: Handling request.
    Proxy: Logging the time of request.
```

### 行为模式

- 责任链模式: 责任链模式是一种行为设计模式， 允许你将请求沿着处理者链进行发送。 收到请求后， 每个处理者均可对请求进行处理， 或将其传递给链上的下个处理者。

 责任链模式优缺点:
    优点:
        1.你可以控制请求处理的顺序。
        2.单一职责原则。 你可对发起操作和执行操作的类进行解耦。
        3.开闭原则。 你可以在不更改现有代码的情况下在程序中新增处理者。
    缺点:
        1. 部分请求可能未被处理。

``` typescript

    /**
     * The Handler interface declares a method for building the chain of handlers.
     * It also declares a method for executing a request.
     */
    interface Handler {
        setNext(handler: Handler): Handler;

        handle(request: string): string;
    }

    /**
     * The default chaining behavior can be implemented inside a base handler class.
     */
    abstract class AbstractHandler implements Handler
    {
        private nextHandler: Handler;

        public setNext(handler: Handler): Handler {
            this.nextHandler = handler;
            // Returning a handler from here will let us link handlers in a
            // convenient way like this:
            // monkey.setNext(squirrel).setNext(dog);
            return handler;
        }

        public handle(request: string): string {
            if (this.nextHandler) {
                return this.nextHandler.handle(request);
            }

            return null;
        }
    }

    /**
     * All Concrete Handlers either handle a request or pass it to the next handler
     * in the chain.
     */
    class MonkeyHandler extends AbstractHandler {
        public handle(request: string): string {
            if (request === 'Banana') {
                return `Monkey: I'll eat the ${request}.`;
            }
            return super.handle(request);

        }
    }

    class SquirrelHandler extends AbstractHandler {
        public handle(request: string): string {
            if (request === 'Nut') {
                return `Squirrel: I'll eat the ${request}.`;
            }
            return super.handle(request);
        }
    }

    class DogHandler extends AbstractHandler {
        public handle(request: string): string {
            if (request === 'MeatBall') {
                return `Dog: I'll eat the ${request}.`;
            }
            return super.handle(request);
        }
    }

    /**
     * The client code is usually suited to work with a single handler. In most
     * cases, it is not even aware that the handler is part of a chain.
     */
    function clientCode(handler: Handler) {
        const foods = ['Nut', 'Banana', 'Cup of coffee'];

        for (const food of foods) {
            console.log(`Client: Who wants a ${food}?`);

            const result = handler.handle(food);
            if (result) {
                console.log(`  ${result}`);
            } else {
                console.log(`  ${food} was left untouched.`);
            }
        }
    }

    /**
     * The other part of the client code constructs the actual chain.
     */
    const monkey = new MonkeyHandler();
    const squirrel = new SquirrelHandler();
    const dog = new DogHandler();

    monkey.setNext(squirrel).setNext(dog);

    /**
     * The client should be able to send a request to any handler, not just the
     * first one in the chain.
     */
    console.log('Chain: Monkey > Squirrel > Dog\n');
    clientCode(monkey);
    console.log('');

    console.log('Subchain: Squirrel > Dog\n');
    clientCode(squirrel);

    Output.: 执行结果
    Chain: Monkey > Squirrel > Dog

    Client: Who wants a Nut?
    Squirrel: I'll eat the Nut.
    Client: Who wants a Banana?
    Monkey: I'll eat the Banana.
    Client: Who wants a Cup of coffee?
    Cup of coffee was left untouched.

    Subchain: Squirrel > Dog

    Client: Who wants a Nut?
    Squirrel: I'll eat the Nut.
    Client: Who wants a Banana?
    Banana was left untouched.
    Client: Who wants a Cup of coffee?
    Cup of coffee was left untouched.

```

- 命令模式: 命令模式是一种行为设计模式， 它可将请求转换为一个包含与请求相关的所有信息的独立对象。 该转换让你能根据不同的请求将方法参数化、 延迟请求执行或将其放入队列中， 且能实现可撤销操作。

  命令模式优缺点:
    优点:
      1. 单一职责原则。 你可以解耦触发和执行操作的类。
      2. 开闭原则。 你可以在不修改已有客户端代码的情况下在程序中创建新的命令。
      3. 你可以实现撤销和恢复功能。
      4. 你可以实现操作的延迟执行。
      5. 你可以将一组简单命令组合成一个复杂命令。
   缺点:
      1. 代码可能会变得更加复杂， 因为你在发送者和接收者之间增加了一个全新的层次。

``` typescript

    /**
     * The Command interface declares a method for executing a command.
     */
    interface Command {
        execute(): void;
    }

    /**
     * Some commands can implement simple operations on their own.
     */
    class SimpleCommand implements Command {
        private payload: string;

        constructor(payload: string) {
            this.payload = payload;
        }

        public execute(): void {
            console.log(`SimpleCommand: See, I can do simple things like printing (${this.payload})`);
        }
    }

    /**
     * However, some commands can delegate more complex operations to other objects,
     * called "receivers."
     */
    class ComplexCommand implements Command {
        private receiver: Receiver;

        /**
         * Context data, required for launching the receiver's methods.
         */
        private a: string;

        private b: string;

        /**
         * Complex commands can accept one or several receiver objects along with
         * any context data via the constructor.
         */
        constructor(receiver: Receiver, a: string, b: string) {
            this.receiver = receiver;
            this.a = a;
            this.b = b;
        }
        /**
         * Commands can delegate to any methods of a receiver.
         */
        public execute(): void {
            console.log('ComplexCommand: Complex stuff should be done by a receiver object.');
            this.receiver.doSomething(this.a);
            this.receiver.doSomethingElse(this.b);
        }
    }

    /**
     * The Receiver classes contain some important business logic. They know how to
     * perform all kinds of operations, associated with carrying out a request. In
     * fact, any class may serve as a Receiver.
     */
    class Receiver {
        public doSomething(a: string): void {
            console.log(`Receiver: Working on (${a}.)`);
        }

        public doSomethingElse(b: string): void {
            console.log(`Receiver: Also working on (${b}.)`);
        }
    }

    /**
     * The Invoker is associated with one or several commands. It sends a request to
     * the command.
     */
    class Invoker {
        private onStart: Command;

        private onFinish: Command;

        /**
         * Initialize commands.
         */
        public setOnStart(command: Command): void {
            this.onStart = command;
        }

        public setOnFinish(command: Command): void {
            this.onFinish = command;
        }

        /**
         * The Invoker does not depend on concrete command or receiver classes. The
         * Invoker passes a request to a receiver indirectly, by executing a
         * command.
         */
        public doSomethingImportant(): void {
            console.log('Invoker: Does anybody want something done before I begin?');
            if (this.isCommand(this.onStart)) {
                this.onStart.execute();
            }

            console.log('Invoker: ...doing something really important...');

            console.log('Invoker: Does anybody want something done after I finish?');
            if (this.isCommand(this.onFinish)) {
                this.onFinish.execute();
            }
        }

        private isCommand(object): object is Command {
            return object.execute !== undefined;
        }
    }

    /**
     * The client code can parameterize an invoker with any commands.
     */
    const invoker = new Invoker();
    invoker.setOnStart(new SimpleCommand('Say Hi!'));
    const receiver = new Receiver();
    invoker.setOnFinish(new ComplexCommand(receiver, 'Send email', 'Save report'));

    invoker.doSomethingImportant();

    Output: 执行结果
    Invoker: Does anybody want something done before I begin?
    SimpleCommand: See, I can do simple things like printing (Say Hi!)
    Invoker: ...doing something really important...
    Invoker: Does anybody want something done after I finish?
    ComplexCommand: Complex stuff should be done by a receiver object.
    Receiver: Working on (Send email.)
    Receiver: Also working on (Save report.)
```

- 迭代器模式: 迭代器模式是一种行为设计模式， 让你能在不暴露集合底层表现形式 （列表、 栈和树等） 的情况下遍历集合中所有的元素。
  
  迭代器模式优缺点:
    优点:
      1. 单一职责原则。 通过将体积庞大的遍历算法代码抽取为独立的类， 你可对客户端代码和集合进行整理。
      2. 开闭原则。 你可实现新型的集合和迭代器并将其传递给现有代码， 无需修改现有代码。
      3. 你可以并行遍历同一集合， 因为每个迭代器对象都包含其自身的遍历状态。
      4. 相似的， 你可以暂停遍历并在需要时继续。
   缺点:
      1. 如果你的程序只与简单的集合进行交互， 应用该模式可能会矫枉过正。
      2. 对于某些特殊集合， 使用迭代器可能比直接遍历的效率低。

``` typescript

    /**
     * Iterator Design Pattern
     *
     * Intent: Lets you traverse elements of a collection without exposing its
     * underlying representation (list, stack, tree, etc.).
     */

    interface Iterator<T> {
        // Return the current element.
        current(): T;

        // Return the current element and move forward to next element.
        next(): T;

        // Return the key of the current element.
        key(): number;

        // Checks if current position is valid.
        valid(): boolean;

        // Rewind the Iterator to the first element.
        rewind(): void;
    }

    interface Aggregator {
        // Retrieve an external iterator.
        getIterator(): Iterator<string>;
    }

    /**
     * Concrete Iterators implement various traversal algorithms. These classes
     * store the current traversal position at all times.
     */

    class AlphabeticalOrderIterator implements Iterator<string> {
        private collection: WordsCollection;

        /**
         * Stores the current traversal position. An iterator may have a lot of
         * other fields for storing iteration state, especially when it is supposed
         * to work with a particular kind of collection.
         */
        private position: number = 0;

        /**
         * This variable indicates the traversal direction.
         */
        private reverse: boolean = false;

        constructor(collection: WordsCollection, reverse: boolean = false) {
            this.collection = collection;
            this.reverse = reverse;

            if (reverse) {
                this.position = collection.getCount() - 1;
            }
        }

        public rewind() {
            this.position = this.reverse ?
                this.collection.getCount() - 1 :
                0;
        }

        public current(): string {
            return this.collection.getItems()[this.position];
        }

        public key(): number {
            return this.position;
        }

        public next(): string {
            const item = this.collection.getItems()[this.position];
            this.position += this.reverse ? -1 : 1;
            return item;
        }

        public valid(): boolean {
            if (this.reverse) {
                return this.position >= 0;
            }

            return this.position < this.collection.getCount();
        }
    }

    /**
     * Concrete Collections provide one or several methods for retrieving fresh
     * iterator instances, compatible with the collection class.
     */
    class WordsCollection implements Aggregator {
        private items: string[] = [];

        public getItems(): string[] {
            return this.items;
        }

        public getCount(): number {
            return this.items.length;
        }

        public addItem(item: string): void {
            this.items.push(item);
        }

        public getIterator(): Iterator<string> {
            return new AlphabeticalOrderIterator(this);
        }

        public getReverseIterator(): Iterator<string> {
            return new AlphabeticalOrderIterator(this, true);
        }
    }

    /**
     * The client code may or may not know about the Concrete Iterator or Collection
     * classes, depending on the level of indirection you want to keep in your
     * program.
     */
    const collection = new WordsCollection();
    collection.addItem('First');
    collection.addItem('Second');
    collection.addItem('Third');

    const iterator = collection.getIterator();

    console.log('Straight traversal:');
    while (iterator.valid()) {
        console.log(iterator.next());
    }

    console.log('');
    console.log('Reverse traversal:');
    const reverseIterator = collection.getReverseIterator();
    while (reverseIterator.valid()) {
        console.log(reverseIterator.next());
    }

    Output: 执行结果
    Straight traversal:
    First
    Second
    Third

    Reverse traversal:
    Third
    Second
    First
```

- 中介者模式: 中介者模式是一种行为设计模式， 能让你减少对象之间混乱无序的依赖关系。 该模式会限制对象之间的直接交互， 迫使它们通过一个中介者对象进行合作。
  
  中介者模式优缺点:
    优点:
      1. 单一职责原则。 你可以将多个组件间的交流抽取到同一位置， 使其更易于理解和维护。
      2. 开闭原则。 你无需修改实际组件就能增加新的中介者。
      3. 你可以减轻应用中多个组件间的耦合情况。
      4. 你可以更方便地复用各个组件。
    缺点:
      1. 一段时间后， 中介者可能会演化成为上帝对象。

``` typescript

    /**
     * The Mediator interface declares a method used by components to notify the
     * mediator about various events. The Mediator may react to these events and
     * pass the execution to other components.
     */
    interface Mediator {
        notify(sender: object, event: string): void;
    }

    /**
     * Concrete Mediators implement cooperative behavior by coordinating several
     * components.
     */
    class ConcreteMediator implements Mediator {
        private component1: Component1;

        private component2: Component2;

        constructor(c1: Component1, c2: Component2) {
            this.component1 = c1;
            this.component1.setMediator(this);
            this.component2 = c2;
            this.component2.setMediator(this);
        }

        public notify(sender: object, event: string): void {
            if (event === 'A') {
                console.log('Mediator reacts on A and triggers following operations:');
                this.component2.doC();
            }

            if (event === 'D') {
                console.log('Mediator reacts on D and triggers following operations:');
                this.component1.doB();
                this.component2.doC();
            }
        }
    }

    /**
     * The Base Component provides the basic functionality of storing a mediator's
     * instance inside component objects.
     */
    class BaseComponent {
        protected mediator: Mediator;

        constructor(mediator: Mediator = null) {
            this.mediator = mediator;
        }

        public setMediator(mediator: Mediator): void {
            this.mediator = mediator;
        }
    }

    /**
     * Concrete Components implement various functionality. They don't depend on
     * other components. They also don't depend on any concrete mediator classes.
     */
    class Component1 extends BaseComponent {
        public doA(): void {
            console.log('Component 1 does A.');
            this.mediator.notify(this, 'A');
        }

        public doB(): void {
            console.log('Component 1 does B.');
            this.mediator.notify(this, 'B');
        }
    }

    class Component2 extends BaseComponent {
        public doC(): void {
            console.log('Component 2 does C.');
            this.mediator.notify(this, 'C');
        }

        public doD(): void {
            console.log('Component 2 does D.');
            this.mediator.notify(this, 'D');
        }
    }

    /**
     * The client code.
     */
    const c1 = new Component1();
    const c2 = new Component2();
    const mediator = new ConcreteMediator(c1, c2);

    console.log('Client triggers operation A.');
    c1.doA();

    console.log('');
    console.log('Client triggers operation D.');
    c2.doD();

    Output: 执行结果
    Client triggers operation A.
    Component 1 does A.
    Mediator reacts on A and triggers following operations:
    Component 2 does C.

    Client triggers operation D.
    Component 2 does D.
    Mediator reacts on D and triggers following operations:
    Component 1 does B.
    Component 2 does C.

```

- 备忘录模式: 备忘录模式是一种行为设计模式， 允许在不暴露对象实现细节的情况下保存和恢复对象之前的状态。

   备忘录模式优缺点
    优点:
      1. 你可以在不破坏对象封装情况的前提下创建对象状态快照。
      2. 你可以通过让负责人维护原发器状态历史记录来简化原发器代码。
   缺点:
      1. 如果客户端过于频繁地创建备忘录， 程序将消耗大量内存。
      2. 负责人必须完整跟踪原发器的生命周期， 这样才能销毁弃用的备忘录。
      3. 绝大部分动态编程语言 （例如 PHP、 Python 和 JavaScript） 不能确保备忘录中的状态不被修改。

``` typescript

    /**
     * The Originator holds some important state that may change over time. It also
     * defines a method for saving the state inside a memento and another method for
     * restoring the state from it.
     */
    class Originator {
        /**
         * For the sake of simplicity, the originator's state is stored inside a
         * single variable.
         */
        private state: string;

        constructor(state: string) {
            this.state = state;
            console.log(`Originator: My initial state is: ${state}`);
        }

        /**
         * The Originator's business logic may affect its internal state. Therefore,
         * the client should backup the state before launching methods of the
         * business logic via the save() method.
         */
        public doSomething(): void {
            console.log('Originator: I\'m doing something important.');
            this.state = this.generateRandomString(30);
            console.log(`Originator: and my state has changed to: ${this.state}`);
        }

        private generateRandomString(length: number = 10): string {
            const charSet = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';

            return Array
                .apply(null, { length })
                .map(() => charSet.charAt(Math.floor(Math.random() * charSet.length)))
                .join('');
        }

        /**
         * Saves the current state inside a memento.
         */
        public save(): Memento {
            return new ConcreteMemento(this.state);
        }

        /**
         * Restores the Originator's state from a memento object.
         */
        public restore(memento: Memento): void {
            this.state = memento.getState();
            console.log(`Originator: My state has changed to: ${this.state}`);
        }
    }

    /**
     * The Memento interface provides a way to retrieve the memento's metadata, such
     * as creation date or name. However, it doesn't expose the Originator's state.
     */
    interface Memento {
        getState(): string;

        getName(): string;

        getDate(): string;
    }

    /**
     * The Concrete Memento contains the infrastructure for storing the Originator's
     * state.
     */
    class ConcreteMemento implements Memento {
        private state: string;

        private date: string;

        constructor(state: string) {
            this.state = state;
            this.date = new Date().toISOString().slice(0, 19).replace('T', ' ');
        }

        /**
         * The Originator uses this method when restoring its state.
         */
        public getState(): string {
            return this.state;
        }

        /**
         * The rest of the methods are used by the Caretaker to display metadata.
         */
        public getName(): string {
            return `${this.date} / (${this.state.substr(0, 9)}...)`;
        }

        public getDate(): string {
            return this.date;
        }
    }

    /**
     * The Caretaker doesn't depend on the Concrete Memento class. Therefore, it
     * doesn't have access to the originator's state, stored inside the memento. It
     * works with all mementos via the base Memento interface.
     */
    class Caretaker {
        private mementos: Memento[] = [];

        private originator: Originator;

        constructor(originator: Originator) {
            this.originator = originator;
        }

        public backup(): void {
            console.log('\nCaretaker: Saving Originator\'s state...');
            this.mementos.push(this.originator.save());
        }

        public undo(): void {
            if (!this.mementos.length) {
                return;
            }
            const memento = this.mementos.pop();

            console.log(`Caretaker: Restoring state to: ${memento.getName()}`);
            this.originator.restore(memento);
        }

        public showHistory(): void {
            console.log('Caretaker: Here\'s the list of mementos:');
            for (const memento of this.mementos) {
                console.log(memento.getName());
            }
        }
    }

    /**
     * Client code.
     */
    const originator = new Originator('Super-duper-super-puper-super.');
    const caretaker = new Caretaker(originator);

    caretaker.backup();
    originator.doSomething();

    caretaker.backup();
    originator.doSomething();

    caretaker.backup();
    originator.doSomething();

    console.log('');
    caretaker.showHistory();

    console.log('\nClient: Now, let\'s rollback!\n');
    caretaker.undo();

    console.log('\nClient: Once more!\n');
    caretaker.undo();

    Output: 执行结果
    Originator: My initial state is: Super-duper-super-puper-super.

    Caretaker: Saving Originator's state...
    Originator: I'm doing something important.
    Originator: and my state has changed to: qXqxgTcLSCeLYdcgElOghOFhPGfMxo

    Caretaker: Saving Originator's state...
    Originator: I'm doing something important.
    Originator: and my state has changed to: iaVCJVryJwWwbipieensfodeMSWvUY

    Caretaker: Saving Originator's state...
    Originator: I'm doing something important.
    Originator: and my state has changed to: oSUxsOCiZEnohBMQEjwnPWJLGnwGmy

    Caretaker: Here's the list of mementos:
    2019-02-17 15:14:05 / (Super-dup...)
    2019-02-17 15:14:05 / (qXqxgTcLS...)
    2019-02-17 15:14:05 / (iaVCJVryJ...)

    Client: Now, let's rollback!

    Caretaker: Restoring state to: 2019-02-17 15:14:05 / (iaVCJVryJ...)
    Originator: My state has changed to: iaVCJVryJwWwbipieensfodeMSWvUY

    Client: Once more!

    Caretaker: Restoring state to: 2019-02-17 15:14:05 / (qXqxgTcLS...)
    Originator: My state has changed to: qXqxgTcLSCeLYdcgElOghOFhPGfMxo
```

- 观察者模式: 观察者模式是一种行为设计模式， 允许你定义一种订阅机制， 可在对象事件发生时通知多个 “观察” 该对象的其他对象。

  观察者模式优缺点:
    优点:
      1. 开闭原则。 你无需修改发布者代码就能引入新的订阅者类 （如果是发布者接口则可轻松引入发布者类）。
      2. 你可以在运行时建立对象之间的联系。
    缺点:
      1. 订阅者的通知顺序是随机的。

``` typescript

    /**
     * The Subject interface declares a set of methods for managing subscribers.
     */
    interface Subject {
        // Attach an observer to the subject.
        attach(observer: Observer): void;

        // Detach an observer from the subject.
        detach(observer: Observer): void;

        // Notify all observers about an event.
        notify(): void;
    }

    /**
     * The Subject owns some important state and notifies observers when the state
     * changes.
     */
    class ConcreteSubject implements Subject {
        /**
         * @type {number} For the sake of simplicity, the Subject's state, essential
         * to all subscribers, is stored in this variable.
         */
        public state: number;

        /**
         * @type {Observer[]} List of subscribers. In real life, the list of
         * subscribers can be stored more comprehensively (categorized by event
         * type, etc.).
         */
        private observers: Observer[] = [];

        /**
         * The subscription management methods.
         */
        public attach(observer: Observer): void {
            const isExist = this.observers.includes(observer);
            if (isExist) {
                return console.log('Subject: Observer has been attached already.');
            }

            console.log('Subject: Attached an observer.');
            this.observers.push(observer);
        }

        public detach(observer: Observer): void {
            const observerIndex = this.observers.indexOf(observer);
            if (observerIndex === -1) {
                return console.log('Subject: Nonexistent observer.');
            }

            this.observers.splice(observerIndex, 1);
            console.log('Subject: Detached an observer.');
        }

        /**
         * Trigger an update in each subscriber.
         */
        public notify(): void {
            console.log('Subject: Notifying observers...');
            for (const observer of this.observers) {
                observer.update(this);
            }
        }

        /**
         * Usually, the subscription logic is only a fraction of what a Subject can
         * really do. Subjects commonly hold some important business logic, that
         * triggers a notification method whenever something important is about to
         * happen (or after it).
         */
        public someBusinessLogic(): void {
            console.log('\nSubject: I\'m doing something important.');
            this.state = Math.floor(Math.random() * (10 + 1));

            console.log(`Subject: My state has just changed to: ${this.state}`);
            this.notify();
        }
    }

    /**
     * The Observer interface declares the update method, used by subjects.
     */
    interface Observer {
        // Receive update from subject.
        update(subject: Subject): void;
    }

    /**
     * Concrete Observers react to the updates issued by the Subject they had been
     * attached to.
     */
    class ConcreteObserverA implements Observer {
        public update(subject: Subject): void {
            if (subject instanceof ConcreteSubject && subject.state < 3) {
                console.log('ConcreteObserverA: Reacted to the event.');
            }
        }
    }

    class ConcreteObserverB implements Observer {
        public update(subject: Subject): void {
            if (subject instanceof ConcreteSubject && (subject.state === 0 || subject.state >= 2)) {
                console.log('ConcreteObserverB: Reacted to the event.');
            }
        }
    }

    /**
     * The client code.
     */

    const subject = new ConcreteSubject();

    const observer1 = new ConcreteObserverA();
    subject.attach(observer1);

    const observer2 = new ConcreteObserverB();
    subject.attach(observer2);

    subject.someBusinessLogic();
    subject.someBusinessLogic();

    subject.detach(observer2);

    subject.someBusinessLogic();

    Output: 执行结果
    Subject: Attached an observer.
    Subject: Attached an observer.

    Subject: I'm doing something important.
    Subject: My state has just changed to: 6
    Subject: Notifying observers...
    ConcreteObserverB: Reacted to the event.

    Subject: I'm doing something important.
    Subject: My state has just changed to: 1
    Subject: Notifying observers...
    ConcreteObserverA: Reacted to the event.
    Subject: Detached an observer.

    Subject: I'm doing something important.
    Subject: My state has just changed to: 5
    Subject: Notifying observers...
```

- 状态模式: 状态模式是一种行为设计模式， 让你能在一个对象的内部状态变化时改变其行为， 使其看上去就像改变了自身所属的类一样。

 状态模式优缺点:
    优点:
        1. 单一职责原则。 将与特定状态相关的代码放在单独的类中。
        2. 开闭原则。 无需修改已有状态类和上下文就能引入新状态。
        3. 通过消除臃肿的状态机条件语句简化上下文代码。
    缺点:
        1. 如果状态机只有很少的几个状态， 或者很少发生改变， 那么应用该模式可能会显得小题大作。

``` typescript

    /**
     * The Context defines the interface of interest to clients. It also maintains a
     * reference to an instance of a State subclass, which represents the current
     * state of the Context.
     */
    class Context {
        /**
         * @type {State} A reference to the current state of the Context.
         */
        private state: State;

        constructor(state: State) {
            this.transitionTo(state);
        }

        /**
         * The Context allows changing the State object at runtime.
         */
        public transitionTo(state: State): void {
            console.log(`Context: Transition to ${(<any>state).constructor.name}.`);
            this.state = state;
            this.state.setContext(this);
        }

        /**
         * The Context delegates part of its behavior to the current State object.
         */
        public request1(): void {
            this.state.handle1();
        }

        public request2(): void {
            this.state.handle2();
        }
    }

    /**
     * The base State class declares methods that all Concrete State should
     * implement and also provides a backreference to the Context object, associated
     * with the State. This backreference can be used by States to transition the
     * Context to another State.
     */
    abstract class State {
        protected context: Context;

        public setContext(context: Context) {
            this.context = context;
        }

        public abstract handle1(): void;

        public abstract handle2(): void;
    }

    /**
     * Concrete States implement various behaviors, associated with a state of the
     * Context.
     */
    class ConcreteStateA extends State {
        public handle1(): void {
            console.log('ConcreteStateA handles request1.');
            console.log('ConcreteStateA wants to change the state of the context.');
            this.context.transitionTo(new ConcreteStateB());
        }

        public handle2(): void {
            console.log('ConcreteStateA handles request2.');
        }
    }

    class ConcreteStateB extends State {
        public handle1(): void {
            console.log('ConcreteStateB handles request1.');
        }

        public handle2(): void {
            console.log('ConcreteStateB handles request2.');
            console.log('ConcreteStateB wants to change the state of the context.');
            this.context.transitionTo(new ConcreteStateA());
        }
    }

    /**
     * The client code.
     */
    const context = new Context(new ConcreteStateA());
    context.request1();
    context.request2();

    Output: 执行结果
    Context: Transition to ConcreteStateA.
    ConcreteStateA handles request1.
    ConcreteStateA wants to change the state of the context.
    Context: Transition to ConcreteStateB.
    ConcreteStateB handles request2.
    ConcreteStateB wants to change the state of the context.
    Context: Transition to ConcreteStateA.
```

- 策略模式: 策略模式是一种行为设计模式， 它能让你定义一系列算法， 并将每种算法分别放入独立的类中， 以使算法的对象能够相互替换。

 策略模式优缺点
    优点:
      1. 你可以在运行时切换对象内的算法。
      2.你可以将算法的实现和使用算法的代码隔离开来。
      3.你可以使用组合来代替继承。
      4.开闭原则。 你无需对上下文进行修改就能够引入新的策略。
    缺点:
      1.如果你的算法极少发生改变， 那么没有任何理由引入新的类和接口。 使用该模式只会让程序过于复杂。
      2.客户端必须知晓策略间的不同——它需要选择合适的策略。
      3.许多现代编程语言支持函数类型功能， 允许你在一组匿名函数中实现不同版本的算法。 这样， 你使用这些函数的方式就和使用策略对象时完全相同， 无需借助额外的类和接口来保持代码简洁。

``` typescript

    /**
     * The Context defines the interface of interest to clients.
     */
    class Context {
        /**
         * @type {Strategy} The Context maintains a reference to one of the Strategy
         * objects. The Context does not know the concrete class of a strategy. It
         * should work with all strategies via the Strategy interface.
         */
        private strategy: Strategy;

        /**
         * Usually, the Context accepts a strategy through the constructor, but also
         * provides a setter to change it at runtime.
         */
        constructor(strategy: Strategy) {
            this.strategy = strategy;
        }

        /**
         * Usually, the Context allows replacing a Strategy object at runtime.
         */
        public setStrategy(strategy: Strategy) {
            this.strategy = strategy;
        }

        /**
         * The Context delegates some work to the Strategy object instead of
         * implementing multiple versions of the algorithm on its own.
         */
        public doSomeBusinessLogic(): void {
            // ...

            console.log('Context: Sorting data using the strategy (not sure how it\'ll do it)');
            const result = this.strategy.doAlgorithm(['a', 'b', 'c', 'd', 'e']);
            console.log(result.join(','));

            // ...
        }
    }

    /**
     * The Strategy interface declares operations common to all supported versions
     * of some algorithm.
     *
     * The Context uses this interface to call the algorithm defined by Concrete
     * Strategies.
     */
    interface Strategy {
        doAlgorithm(data: string[]): string[];
    }

    /**
     * Concrete Strategies implement the algorithm while following the base Strategy
     * interface. The interface makes them interchangeable in the Context.
     */
    class ConcreteStrategyA implements Strategy {
        public doAlgorithm(data: string[]): string[] {
            return data.sort();
        }
    }

    class ConcreteStrategyB implements Strategy {
        public doAlgorithm(data: string[]): string[] {
            return data.reverse();
        }
    }

    /**
     * The client code picks a concrete strategy and passes it to the context. The
     * client should be aware of the differences between strategies in order to make
     * the right choice.
     */
    const context = new Context(new ConcreteStrategyA());
    console.log('Client: Strategy is set to normal sorting.');
    context.doSomeBusinessLogic();

    console.log('');

    console.log('Client: Strategy is set to reverse sorting.');
    context.setStrategy(new ConcreteStrategyB());
    context.doSomeBusinessLogic();

    Output: 执行结果
    Client: Strategy is set to normal sorting.
    Context: Sorting data using the strategy (not sure how it'll do it)
    a,b,c,d,e

    Client: Strategy is set to reverse sorting.
    Context: Sorting data using the strategy (not sure how it'll do it)
    e,d,c,b,a
```

- 模板方法模式
 模板方法模式优缺点
    优点:
        1.你可仅允许客户端重写一个大型算法中的特定部分， 使得算法其他部分修改对其所造成的影响减小。
        2.你可将重复代码提取到一个超类中。
    缺点:
        1. 部分客户端可能会受到算法框架的限制。
        2.通过子类抑制默认步骤实现可能会导致违反_里氏替换原则_。
        3.模板方法中的步骤越多， 其维护工作就可能会越困难。

``` typescript
    /**
     * The Abstract Class defines a template method that contains a skeleton of some
     * algorithm, composed of calls to (usually) abstract primitive operations.
     *
     * Concrete subclasses should implement these operations, but leave the template
     * method itself intact.
     */
    abstract class AbstractClass {
        /**
         * The template method defines the skeleton of an algorithm.
         */
        public templateMethod(): void {
            this.baseOperation1();
            this.requiredOperations1();
            this.baseOperation2();
            this.hook1();
            this.requiredOperation2();
            this.baseOperation3();
            this.hook2();
        }

        /**
         * These operations already have implementations.
         */
        protected baseOperation1(): void {
            console.log('AbstractClass says: I am doing the bulk of the work');
        }

        protected baseOperation2(): void {
            console.log('AbstractClass says: But I let subclasses override some operations');
        }

        protected baseOperation3(): void {
            console.log('AbstractClass says: But I am doing the bulk of the work anyway');
        }

        /**
         * These operations have to be implemented in subclasses.
         */
        protected abstract requiredOperations1(): void;

        protected abstract requiredOperation2(): void;

        /**
         * These are "hooks." Subclasses may override them, but it's not mandatory
         * since the hooks already have default (but empty) implementation. Hooks
         * provide additional extension points in some crucial places of the
         * algorithm.
         */
        protected hook1(): void { }

        protected hook2(): void { }
    }

    /**
     * Concrete classes have to implement all abstract operations of the base class.
     * They can also override some operations with a default implementation.
     */
    class ConcreteClass1 extends AbstractClass {
        protected requiredOperations1(): void {
            console.log('ConcreteClass1 says: Implemented Operation1');
        }

        protected requiredOperation2(): void {
            console.log('ConcreteClass1 says: Implemented Operation2');
        }
    }

    /**
     * Usually, concrete classes override only a fraction of base class' operations.
     */
    class ConcreteClass2 extends AbstractClass {
        protected requiredOperations1(): void {
            console.log('ConcreteClass2 says: Implemented Operation1');
        }

        protected requiredOperation2(): void {
            console.log('ConcreteClass2 says: Implemented Operation2');
        }

        protected hook1(): void {
            console.log('ConcreteClass2 says: Overridden Hook1');
        }
    }

    /**
     * The client code calls the template method to execute the algorithm. Client
     * code does not have to know the concrete class of an object it works with, as
     * long as it works with objects through the interface of their base class.
     */
    function clientCode(abstractClass: AbstractClass) {
        // ...
        abstractClass.templateMethod();
        // ...
    }

    console.log('Same client code can work with different subclasses:');
    clientCode(new ConcreteClass1());
    console.log('');

    console.log('Same client code can work with different subclasses:');
    clientCode(new ConcreteClass2());

    Output: 执行结果
    Same client code can work with different subclasses:
    AbstractClass says: I am doing the bulk of the work
    ConcreteClass1 says: Implemented Operation1
    AbstractClass says: But I let subclasses override some operations
    ConcreteClass1 says: Implemented Operation2
    AbstractClass says: But I am doing the bulk of the work anyway

    Same client code can work with different subclasses:
    AbstractClass says: I am doing the bulk of the work
    ConcreteClass2 says: Implemented Operation1
    AbstractClass says: But I let subclasses override some operations
    ConcreteClass2 says: Overridden Hook1
    ConcreteClass2 says: Implemented Operation2
    AbstractClass says: But I am doing the bulk of the work anyway
```

- 访问者模式: 访问者模式是一种行为设计模式， 它能将算法与其所作用的对象隔离开来。

  访问者模式优缺点:
    优点:
      1. 开闭原则。 你可以引入在不同类对象上执行的新行为， 且无需对这些类做出修改。
      2. 单一职责原则。 可将同一行为的不同版本移到同一个类中。
      3. 访问者对象可以在与各种对象交互时收集一些有用的信息。 当你想要遍历一些复杂的对象结构 （例如对象树）， 并在结构中的每个对象上应用访问者时， 这些信息可能会有所帮助。
    缺点:
      1. 每次在元素层次结构中添加或移除一个类时， 你都要更新所有的访问者。
      2. 在访问者同某个元素进行交互时， 它们可能没有访问元素私有成员变量和方法的必要权限。

``` typescript

    /**
     * The Component interface declares an `accept` method that should take the base
     * visitor interface as an argument.
     */
    interface Component {
        accept(visitor: Visitor): void;
    }

    /**
     * Each Concrete Component must implement the `accept` method in such a way that
     * it calls the visitor's method corresponding to the component's class.
     */
    class ConcreteComponentA implements Component {
        /**
         * Note that we're calling `visitConcreteComponentA`, which matches the
         * current class name. This way we let the visitor know the class of the
         * component it works with.
         */
        public accept(visitor: Visitor): void {
            visitor.visitConcreteComponentA(this);
        }

        /**
         * Concrete Components may have special methods that don't exist in their
         * base class or interface. The Visitor is still able to use these methods
         * since it's aware of the component's concrete class.
         */
        public exclusiveMethodOfConcreteComponentA(): string {
            return 'A';
        }
    }

    class ConcreteComponentB implements Component {
        /**
         * Same here: visitConcreteComponentB => ConcreteComponentB
         */
        public accept(visitor: Visitor): void {
            visitor.visitConcreteComponentB(this);
        }

        public specialMethodOfConcreteComponentB(): string {
            return 'B';
        }
    }

    /**
     * The Visitor Interface declares a set of visiting methods that correspond to
     * component classes. The signature of a visiting method allows the visitor to
     * identify the exact class of the component that it's dealing with.
     */
    interface Visitor {
        visitConcreteComponentA(element: ConcreteComponentA): void;

        visitConcreteComponentB(element: ConcreteComponentB): void;
    }

    /**
     * Concrete Visitors implement several versions of the same algorithm, which can
     * work with all concrete component classes.
     *
     * You can experience the biggest benefit of the Visitor pattern when using it
     * with a complex object structure, such as a Composite tree. In this case, it
     * might be helpful to store some intermediate state of the algorithm while
     * executing visitor's methods over various objects of the structure.
     */
    class ConcreteVisitor1 implements Visitor {
        public visitConcreteComponentA(element: ConcreteComponentA): void {
            console.log(`${element.exclusiveMethodOfConcreteComponentA()} + ConcreteVisitor1`);
        }

        public visitConcreteComponentB(element: ConcreteComponentB): void {
            console.log(`${element.specialMethodOfConcreteComponentB()} + ConcreteVisitor1`);
        }
    }

    class ConcreteVisitor2 implements Visitor {
        public visitConcreteComponentA(element: ConcreteComponentA): void {
            console.log(`${element.exclusiveMethodOfConcreteComponentA()} + ConcreteVisitor2`);
        }

        public visitConcreteComponentB(element: ConcreteComponentB): void {
            console.log(`${element.specialMethodOfConcreteComponentB()} + ConcreteVisitor2`);
        }
    }

    /**
     * The client code can run visitor operations over any set of elements without
     * figuring out their concrete classes. The accept operation directs a call to
     * the appropriate operation in the visitor object.
     */
    function clientCode(components: Component[], visitor: Visitor) {
        // ...
        for (const component of components) {
            component.accept(visitor);
        }
        // ...
    }

    const components = [
        new ConcreteComponentA(),
        new ConcreteComponentB(),
    ];

    console.log('The client code works with all visitors via the base Visitor interface:');
    const visitor1 = new ConcreteVisitor1();
    clientCode(components, visitor1);
    console.log('');

    console.log('It allows the same client code to work with different types of visitors:');
    const visitor2 = new ConcreteVisitor2();
    clientCode(components, visitor2);

    Output: 执行结果
    The client code works with all visitors via the base Visitor interface:
    A + ConcreteVisitor1
    B + ConcreteVisitor1

    It allows the same client code to work with different types of visitors:
    A + ConcreteVisitor2
    B + ConcreteVisitor2
```
