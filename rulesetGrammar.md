```
(* Primary rule *)

Ruleset = { Entity } , { Rule }

(* Entity definitions *)

Entity = "entity" , ws , Ident, [ ws , PrimaryGrouping , { ws , "," , ws , Grouping } ]
PrimaryGrouping = ("group" | "tag") , ws , "by" , ws , Variable , ws , "primarily"
Grouping = ("group" | "tag") , ws , "by" , ws , Variable , ws , "as" , ws , Ident

(* Rules *)
Rule = Variable , ":" , Ident , [ Filters ] , ws , "{" , ws , { Permission } , ws , "}"
Filters = "[" , ws , StrList , ws , "]" , { "[" , ws , Ident , ws , ":" , ws , StrList , ws , "]" }
StrList = '"' , NotQuote , '"' , { ws , "," , ws , '"' , NotQuote , '"' }
Permission = "can" , ws , ["not"] , ws , PermissionIdent , 
             [ ws , Variable , ":" , Ident , { Variable , ":" , Ident } ] , ws , "{" , Conditionals , "}"
PermissionIdent = "<" , Ident , { ":" , ("*" | Ident) } , ">"
Conditionals = [ Conditional , { ws , ("and" | "or" | "but") , Conditional } ]
Conditional = ["not" , ws] , "if" , ws , "(" , ws , Expr , ws , ")"

(* Expr *)
Expr = BoolExpr_Not
     | BoolExpr_Not , ws , ("&&" | "||") , ws, BoolExpr_Not
BoolExpr_Not = "!" , CompExpr
CompExpr = MathExpr_Add , ws , ("===" | "==" | ">=" | "<=" | ">" | "<") , ws , MathExpr_Add
         | MathExpr_Add
MathExpr_Add = MathExpr_Mult , ws , ("+" | "-") , ws , MathExpr_Mult
             | MathExpr_Mult
MathExpr_Mult = Factor , ws , ("*" | "/") , ws , Factor
              | Factor
Factor = "(" , ws , Expr , ws , ")"
       | Literal | Variable

(* Literals *)

Literal = LitString | LitBool | LitFloat | LitInteger
LitString = '"' , NotQuote , '"'
LitBool = "TRUE" | "FALSE"
LitFloat = [0] ,  { Digit } , "." , Digit , { Digit }
LitInteger = Digit , { Digit }

Variable = "$" , Ident , { "." , Ident }
Ident = Char , { Char }

(* Primitives *)
NotQuote Char - '"'
Char = "a" | ... "z" | "0" | ... "9" | "-" | "_"
ws = { " " }
```
