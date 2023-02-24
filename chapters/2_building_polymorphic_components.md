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



[<< prev](./1_introduction.md) | [next >>]()