# Building Polymorphic Components

We can build Polymorphic components very simplistically in React.
There are pros and cons in this approach which we will discuss, however, if we want a very basic implementation of a Polymorphic component we must merely consider how our polymorphic component will be built. In this example we will build a basic ```Text``` component, this component will have a prop ```as``` which will determine the element used to render the component.

What do we mean by this? lets define it with an example. If I provide a component with ```as="h2"\``` I would expect the content wrapped with the component to be rendered within a ```h2``` component. In doing so we can make a base component which is able to hold all common functionality whilst still being dynamic enough to change the rendered element as we please. This means that rather than having multiple components for each type of component, we can simply have a dynamic component and define all of our common functionality only once.

The end goal of such a component which is used as such:

<div align="center">

```
<Text as="h1">Should be a h1 element.</Text>
```

</div>


In order to achieve this we need our component to render wrapped in our passed elements. For instance, in this example we would expect to see in the DOM an element as such:

<div align="center">

```
<h1>Should be a h1 element.</h1>
```

</div>

To achieve this we will need to pass our ```as``` prop and render it. The problem we have is that React will not allow us to do this directly as props cannot be directly used as JSX elements, instead we will need to create a capitalised variable in order to use this:

```
const Text = ({
    as,
    children
}: TextProps) => {
    const Component = as || 'span';
    return <Component>{children}</Component>;
};
```

This enables us to use the variable as a component. We then simply return our ```children``` passed in between our components open and closing tags and return them wrapped with our variable name. You can find a full example of this code [here](https://github.com/LukeMcCann/FirstPolymorphicComponent/blob/first-polymorphic-component/src/App/Text/Text.tsx).

So, we have built our basic component, congratulations! however, there are still some problems. In programming we always start off with the most simplistic implementation, we write our tests, then build the implementation to pass the tests and achieve the functionality we require. We should only ever do the minimum to get the tests to pass. As such, there are many ways in which we are able to improve this naive implementation.

First and foremost, our current implementation will accept any value for our ```as``` prop. What if another developer passes a non-existent tag? you might expect an error, but you would be wrong, it instead will simply render with the nonsensical tag. You may think: "Well, it doesn't break, what's the problem?" but if we have many different types of tags that are none-standard, this could lead to problems later down the line and doesn't keep our application consistent. This could even be a typo, for instance, if our user passes ```hl``` instead of ```h1```, this could be rather difficult to spot depending on the font you are using in your text editor.

The second issue is that we currently have no support for attributes in our Component. For instance, if we wanted to pass an anchor tag we would also need to pass a ```href``` attribute, currently we are not passing this to our component tags and it would therefore not be applied, as such we would need to add support for attributes in order to expand the usefulness of our Component. This ties into our third main issue, if we enable attribute support we will then have the problem of invalid attributes being passed to elements. What do we mean by this? well, consider the following: we enable our Component to pass a href attribute, what happens if we pass this attribute but put ```as="h1"```? this should not be allowed as href is not available for a h1 yet currently there is nothing to stop us from doing this.

# Improving Our Component

Previously we defined three key ways in which our polymorphic component could be better. In this section we will implement this into our actual component and test it to ensure it adheres to our ideal outcome. In order to do this it is important we use TypeScript. JavaScript is a loosely typed language, this is very flexible and can be great when we want to write quick prototypes in our code, however, it can lead to mistakes and bugs further down the line. As someone who started programming in Java, PHP, C++ I much prefer strongly typed languages as too much flexibility makes it very easy to write sloppy code that is difficult to maintain. TypeScript fixes this by giving us a Transpiler (a form of compiler which translates one language to another) the transpilers output is often a human-readable language, compilation is a different form where we compile a language into another which is more machine-readable, usually increasing efficiency or runtime, each of these operate at differing levels of abstraction.
We will be using TypeScript so that we can set limits as to the types our props will accept, this will make our component far more robust and set "rules" that future developers must follow in order to use our component.

Another key part of TypeScript we will use is the concept of ```Generics```. If you have ever coded in an object oriented language such as Java it is likely you have come across the term ```Generics``` before. Java's generics are very similar to ```TypeScripts``` and the functionality and syntax will appear very familiar to seasoned Java devs. However, they do so differ in the way they handle type erasure, ```TypeScripts``` system being structural rather than nominal, which can affect how generics are used in certain situations.

In a structural type system types are defined by their structure and composition rather than explicit declarations or names. This means that two types are considered equivalent if their structures match, regardless of whether or not they have the same name or were defined in the same way. In TypeScript we can see an example of this if we have two objects with the same properties and methods, these two objects will be considered to have the same type even if they were defined separately or with different names.

Contrasting this is a nominal type system as you would find in a language like Java. In this instance types are defined by explicit declarations and names, two types are only considered equivalent if they have the same name or were declared in the same way. In Java two classes with the same structure but different names are considered to have different types and cannot be used interchangeably.

That was a lot of information, but I hope you now see what it is we are using Generics for a little clearer. In our case we want our component to be as flexible as possible, thus we will be using Generics within our implementation to allow for our types to be decided at runtime. However, we will also wish to constrain these generics to types which are valid to use with our generic component.

Note: I say component a lot here for clarity but do remember that in React a component is simply a JS function.

It may help to think of generics in terms of a function. If we think about when we want to write a JS function and make it more generic what do we do? we parameterise it, we use variables, similarly when we want to parameterise a TypeScript type we use Generics.

```
const echo = value => {
    console.log(value);

    return value;
};

echo(123);
```

In the above snippet we have a parameterised function, this means we can reuse the function to print any value we pass to it, this works great, but what if we wanted to limit this particular function to only work with a single type? this is where TypeScript would come in:

```
const echo = (value: number) : number => {
    console.log(value)

    return value;
};

echo(123);
```

But what if we wanted multiple types? well, we could use ```any``` however that is not recommended as it essentially disables TypeScript effectively losing all of the advantages using TypeScript provides (such as type safety). It is far better if we can explicitly define the types we are going to want to use with this function, or if we don't know what types we are going to want to use to use the ```unknown``` type. In this case we want to restrict our types to a number, string, or object.

```
const echo = (value: number | string | object) : number | string | object => {
    console.log(value)

    return value;
};

echo(123);
```

Now, this works but it means we have to know exactly what we want, it also means we have to define every type as a union type, and it is not very dynamic at all. It would be far better in this case to use Generics:

```
const echo = <T> (value: T) : T => {
    console.log(value)

    return value;
};

echo(123);
```

We can represent our value as a kind of "variable". This means we do not need to know exactly what type we are going to be handed at runtime. In this example our generic is ```T```, we declare our generic type ```T``` in angled brackets then use it as the type for both our parameter and the return value. The actual type here will be determined at runtime depending on the type which is passed to the function. This means we can effectively handle any type by inferring the type from the argument passed. Alternatively, TypeScript allows us to explicitly declare our generic type when calling the function as such:

```
echo<string>("123");
```

Now lets say we have another function:

```
const echoLength = <T extends {length: number}> (value: T) : number => {
    console.log(value.length)

    return value.length;
};

echo(123);
```

In this case we have a Generic, however, the function needs access to a property of that Generic value which may not exist on every possible type, in this case we need to constrain the type of our generic to only allow the types which have a length property. We can do this by extending a type which has the properties we are looking for.

Lets now apply what we have learned here to our Component.

# Applying Our Knowledge of Generics

First of all we want to constrain our component to only allow valid HTML element strings in our ```as``` prop:

```
const Text = <T,> ({
    as,
    children
}: TextProps) => {
    const Component = as || 'span';
    return <Component>{children}</Component>;
};
```

We do so by applying our Generic within our Component, we will also need to pass this in our interface:

```
interface TextProps {
    as?: T,
    children: React.ReactNode,
};
```

We also need to either define our generic with a comma, or by extending another element straight after. This is due to a quirk with JSX explained best in [this](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics?) StackOverflow post.

We can then constrain our generic, in this case we want it to be a valid ```ElementType```:

```
interface TextProps<T> {
    as?: T,
    children: React.ReactNode,
};

const Text = <T extends React.ElementType> ({
    as,
    children
}: TextProps<T>) => {
    const Component = as || 'span';
    return <Component>{children}</Component>;
};
```

With this restriction in place all of our valid tests should still work. However, if we have an invalid value we expect to see an error:

```
    <Text as="nonesense">Hello World</Text>

    Type '"nonesense"' is not assignable to type 'ElementType<any> | undefined'
```

Now, you might think we would need to update our tests for this, however, we have an issue. Since this is a syntax error which is being assessed at TypeScripts "compile" time we have no means of testing for this. Instead, we can be sure that nobody will use this component in this manner as the transpiler itself will throw an error disallowing misuse of the component in the first place.

The element we have extended here is the React.ElementType, if you inspect this you will notice an array of elements all which have tags matching HTMLElements. By leveraging these standard types we cut down on the amount of declarations we would need if we did this as a union type instead. You can find a full example of this
[here](https://github.com/LukeMcCann/FirstPolymorphicComponent/tree/constrained-generics).

[<< prev](./1_introduction.md) | [next >>]()