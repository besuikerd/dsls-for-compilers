% dsls for compilers
% Nick ten Veen
%

# Syntax

## Syntax definition

```
Exp.Add = <Exp> + <Exp> { left }
Exp.Mul = <Exp> * <Exp> { left }
Exp.Pow = <Exp> ^ <Exp> { right }
Exp.Int = \d+
```

## Associations

```
Exp.Add = <Exp> + <Exp> { left }

((2 + 3) + 4)
```

```
Exp.Not = not <Exp> { right } 

not (not true))
```


## Ambiguities

```
2 + 3 * 4
```


```
Add(                       Mul(
  Int(2),                    Add(
  Mul(                         Int(2),
    Int(3),                    Int(3)
    Int(4)                   ),
  )                          Int(4)
)                          )
```

## Disambiguation

```
  {
    Exp.Add
    Exp.Sub
  } >
  {
    Exp.Mul
    Exp.Div
  }
```

## Tools

* Syntax highlighting
* Code formatter / pretty printer
* Code outline
* Code folding
* Content assist
* (meta) Concrete object syntax

## Code formatter

```
Exp.IfThenElse = <
  if(<Exp>) {
    <{Statement "\n"}*>
  } else {
    <{Statement "\n"}*>
  }
>
```

# Transformations
## 

* Rule: `Term => Term`

```
  eval : Add(e1, e2) -> <add> (i, j)  
    where
      Int(i) := <eval> e1
    ; Int(j) := <eval> e2
```

* Strategy: `(Seq[Rule], Seq[Term]) => Rule`
```
  fold-left(do-fold | acc): [] -> acc
  fold-left(do-fold | acc): [x | xs] -> <fold-left(do-fold | acc')> xs 
    where
      acc' := <do-fold> (acc, x)  
```

## Desugaring

```
RenderElement.RenderElement = [
  <[ID] [{ElementAttribute " "}*]>
    [{RenderChild "\n"}*]
  </[ID]>
]
RenderElement.RenderElement2 = [
  <[ID] [{ElementAttribute " "}*]/>
]
```

```
<div></div>
<div/>
```

```
RenderElement("div", [], [], "div")
RenderElement2("div", [])
```

## Desugaring (pattern matching)
```
desugar-element: RenderElement2(tag, attributes) -> RenderElement(tag, attributes, [], tag)
```


# Code generation

##
`Source Language => Target Language`

## How to generate?
* String concatenation
* Target AST
* Concrete Object Syntax

## String concatenation

```
generate-expression: Add(e1, e2) -> $[[e1'] + [e2']]
  with
    e1' := <generate-expression> e1
  ; e2' := <generate-expression> e2
```

## Target AST

```
generate-expression: Add(e1, e2) -> JsAdd(e1', e2')
  with
    e1' := <generate-expression> e1
  ; e2' := <generate-expression> e2
    
javascript-pp = ...
```

## Concrete Object Syntax
```
generate-expression: Add(e1, e2) -> js-exp |[
  exp_e1 + exp_e2
]|
  with
    exp_e1 := <generate-expression> e1
  ; exp_e2 := <generate-expression> e2
    

```