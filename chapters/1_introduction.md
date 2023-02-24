# Introduction to Polymorphic Components

In software engineering polymorphism is a common term used throughout Object Oriented Programming to define a single interface which is used to represent entities of differing types. This is borrowed from the biological process in which an organism may have many different forms.

Thus, it logically follows that polymorphic components are components which may also be used to represent differing types. But what does this mean? you may ask. For instance in Object Oriented Programming we may have a polymorphic class which is used by other classes via inheritance to inherit various capabilities or properties. In modern React however it is best practice for us to use functional components, therefore when we are talking about polymorphic components we are not talking about class inheritance but a method by which a component may change it's base properties dynamically.

You can think of this as a Component which can change its type from a user prop, for example we may have a Component called DOMElement which itself can be many components (span, h1, a).

Rather than creating a separate DOMElement for each of these components it is far better if we can define it dynamically:

<div align="center">

```
<DOMElement as="h1">
<DOMElement as="a">
```
</div>

We can take a look at real examples of Polymorphic Components in the MUI and Formik libraries. An example of this would be declaring a particular MUI Button component:

<div align="center">

```
import Button from '@mui/material/Button';

<Button color="success" variant="text">
<Button color="danger" variant="outlined">
```
</div>

Here we can pass properties which completely change the button of our Button component. We can handle various Button types, all without having to declare more than a single Button component. You can likely imagine how much space this saves in our codebase as we can reuse the same component over and over again for each variant of our component. So how exactly do we do this?

___

<div align="right">

[next >>](./2_building_polymorphic_components.md)

</div>