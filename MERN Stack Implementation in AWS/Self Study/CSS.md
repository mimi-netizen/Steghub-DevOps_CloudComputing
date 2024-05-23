# Cascading Style Sheets (CSS)

Cascading Style Sheets (CSS) is a styling language used to control the layout and appearance of web pages written in a markup language, such as HTML or XML.  
It is used to separate the presentation of a web page from its content, allowing developers to create visually appealing and consistent designs across multiple pages.

CSS allows developers to define styles for various elements on a web page, such as fonts, colors, spacing, and layout. It also provides a way to create responsive designs that adapt to different screen sizes and devices.

Below are some basic syntax and properties used in CSS:

1. Selectors are used to target specific elements on a web page. They are followed by a declaration block that defines the styles for the selected elements.

```css
h1 {
  color: blue;
  font-size: 36px;
}
```

This code selects all elements with the tag name "h1" and applies the defined styles to them.

2. Properties are used to define the styles for the selected elements. Each property has a name, a colon, and a value.

```css
h1 {
  color: blue;
  font-size: 36px;
}
```

In this example, "color" and "font-size" are properties, and "blue" and "36px" are their respective values.

3. Values are the values assigned to properties. They can be numbers, colors, fonts, or other types of values, depending on the property.

```css
h1 {
  color: blue;
  font-size: 36px;
}
```

In this example, "blue" is a color value, and "36px" is a numerical value.

4. Units are used to define the size of values. They can be pixels (px), ems (em), rems (rem), percentages (%), or other units, depending on the property.

```css
h1 {
  font-size: 36px;
}
```

In this example, "36px" is a numerical value with the unit "px".

5. Selector-property-value syntax: This syntax is used to define a style for a specific selector.

```css
h1 {
  color: blue;
}
```

In this example, "h1" is the selector, "color" is the property, and "blue" is the value.

6. ID and class selectors: ID selectors are used to target a specific element with a unique ID, while class selectors are used to target elements with a specific class.

```css
#header {
  background-color: green;
}

.nav-link {
  color: red;
}
```

In this example, "#header" is an ID selector, and ".nav-link" is a class selector.

7. Pseudo-classes and pseudo-elements: Pseudo-classes and pseudo-elements are used to target specific parts of an element, such as the first line of a paragraph or the last child of a container.

```css
p:first-line {
  font-size: 24px;
}

ul:last-child {
  margin-bottom: 0;
}
```

In this example, "p:first-line" is a pseudo-class that targets the first line of a paragraph, and "ul:last-child" is a pseudo-element that targets the last child of a list.

8. Media queries: Media queries are used to apply different styles based on the size of the screen or device.

```css
@media (max-width: 768px) {
  h1 {
    font-size: 24px;
  }
}
```

In this example, the styles for h1 elements are applied only when the screen width is less than or equal to 768px.

These are just a few of the basic syntax and properties used in CSS. There are many more advanced features and techniques that can be used to create complex and responsive designs.
