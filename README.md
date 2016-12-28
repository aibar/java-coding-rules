# What the fuck is it?

It's easy to write a bullshit code in Java. This is list of _Must-s_, _Should-s_, appropriate _Not-s_, _Arguments_ and some _Good Ideas_ to code like a Jedi

# OOP

Quick list:
- Encapsulation. Always
- Inheritance. Never, except extending abstract classes
- Polymorphism. It's necessary or not. Simple

## Must Not

**Don't use Getters and Setters except in Builder and state-only classes**

Arguments:

It breaks encapsulation. With setters we change the state.
With getters we get the state. With this full information about the object,
we no longer need it. We can start implementing the logic which must be the
responsibility of the object. And in the end we will end up with the "Spaghetti code".
It's first class Evil.

**BUT** we have objects that contain only state. In this case state is the logic and
we cannot avoid getters and setters. But I suggest you to not name it:

    public String getSomething();
    public void setSomething(String something);

Instead:

    String something();
    void something(String something);

**Don't use static except in Factory classes**

Arguments:

Static is the procedural way of programming (PP). I can't say nothing else. If you understand
what is difference between PP and OOP, you must understand why it's bad in most cases.
Just answer to this question: Does private static methods are useful?

**Don't inherit from anything except from very super abstract classes**
TODO

## Good Ideas

**Use getters and setters only in Builder classes**

    public interface SomethingBuilder {
        String something();
        SomethingBuilder something(String something);
        Something build();
    }

Arguments:

It's about mutability. We all know that it's root of many headaches. When we have a little
state we can immediately pass the state when creating an object. But when we have a lot of state
we create a State objects.

**Hide implementation**. Use Factors EVERYWHERE. Without compromise.

In Smart OOP world we should not see any Implementation.

    // Not so good as you might thinked
    Pipeline pipeline = new PipelineImpl();

You can say what a problem we are working with Pipeline interface. True OOP!
But I see that we are forced to know how to get this Interface. Creating interface
with new() says nothing to me. Example. You want a cake. In the world "new"
you will have to create it youself. We have CakeImpl. It has constructor arguments.

    Cake myCake = new CakeImpl(someSugar, someEggs, someChocolate, etc.);

You will not **make** a good cake with this poor information. The key word is **make**.
With new you can only **create**.

    Cake myCake = Cake.mkSuperCake(someSugar, someEggs, someChocolate, etc.);

"mk" means **make**. Dispatch making a tasty cake to the "Master" interface. I like to use
word Master instead of Factory. Because factories are dirty and polluting. Master is master.
He knows his job better than robots in factories.
But, again you can say I can "insert" making a tasty cake into Impl class. 
But this logic is broken. How cake can make itself?
TODO: more arguments and code examples, people will not understand

So, when creating classes with _new_ we know the
implementation. I _think_ that hiding implementation is a **Good Idea**

# Exceptions

## Must

**"Throw and fuck away". Only if you are not able to handle the exception**

    try {
        // The exceptional code
    } catch (Exception fail) {
        throw new RuntimeException("We are failed because of...", fail);
    }
    // Or
    try {
        // The exceptional code
    } catch (Exception fail) {
        // Of course, SomeBusinessFailed is a subclass of RuntimeException
        throw new SomeBusinessFailed(fail);
    }

Arguments:

It's about returning result, because can you able or can't able to handle exception,
it does not care, you must return result. But what result I must return?
Probably you want to return "null" or "void". No.
You must throw an exception with meaningful message, because "null" or "void"
means we forgot the result and we don't know why we got "null" or "void"
We don't want to forget the result. Exception is the result

## Must Not

**Only if you are not forced to do so because of Java**

    public void doSomething() throws Exception {
        // The exceptional code
    }

Arguments:

    // Business logic
    public char payMoneyInDollars(Employee employee) throws FileNotFoundException {
        // The exceptional code
    }
    ...
    try {
        char payed = payMoneyInDollars(someEmployee);
    } catch (FileNotFoundException e) {
        // We didn't find some file when paying money for this employee
        // Tell me what should I do here?
        // Just WTF stuff
    }

**The code of very "fat Developer"**

    try {
        // The exceptional code
    } catch (Exception e) {
        // Who cares
        e.printStackTrace();
    }

It does not need arguments

**The code of very "fat Developer" eating pop-corn**

    try {
        // The exceptional code
    } catch (Exception e) {
        // Who cares, even I don't
    }

It does not need arguments

## Should

**You should throw an exception if you are not able to continue the logic,
because in any case it will lead to exception**

    public String doSomething(String text) {
        if (text == null || text.length() < 1) {
            throw new IllegalArgumentException("Text length < 1");
        }
        return text.substring(1);
    }

Arguments:

    public String doSomething(String text) {
        return text.substring(1);
    }
    ...
    // It's not fair! Certainly we will be fucked
    // Of course NullPointerException will tell us that we passed the null
    // But why don't we just say that we don't work with nulls?
    // It would be politically correct
    String result = doSomething(null);

## Ideas

- Most of all you are trying to say is about exception chaining.
When to stop exception chaining? In general you should never stop exception chaining,
except when you are in front of the client. In these case you should stop exception
chaining (anyway, you can no longer throw exceptions!) and notify the user about the
error. Handling exception before the client is always because of hack