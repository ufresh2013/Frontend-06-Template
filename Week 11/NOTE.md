### 重学CSS
CSS 2.1语法标准
https://www.w3.org/TR/CSS21/grammar.html#q25.0
https://www.w3.org/TR/css-syntax-3


CSS总体结构
- at-rules: @charset, @import, @media, @page等
@charset: https://www.w3.org/TR/css-syntax-3
@import: https://www.w3.org/TR/css-cascade-4
@media: https://www.w3.org/TR/css3-conditional
@page: https://www.w3.org/TR/css-page-3
@counter-style: https://www.w3.org/TR/css-counter-styles-3
@keyframes: https://www.w3.org/TR/css-animations-1
@fontface: https://www.w3.org/TR/css-fonts-3
@supports: https://www.w3.org/TR/css3-conditional
@namespace: https://www.w3.org/TR/css-namespaces-3

- rule
Selector 选择器: 
  https://www.w3.org/TR/selectors-3
  https://www.w3.org/TR/selectors-4
  类型
    selector_group, 
    selector: > <sp> + ~
    simple_selector: type * . # : :: :not
Key 属性+变量
  https://www.w3.org/TR/css-variables
Value 值+函数类型的值 calc, number, length ...
  https://www.w3.org/TR/css-values-4

```css
/* 选择器 声明key: value*/
div {
  background-color: blue
}
```
