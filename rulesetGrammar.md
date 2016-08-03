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
Conditional = ["not" , ws] , "if" , ws , "(" , Expr , ")"

(* Expr *)
... TODO

Variable = "$" , Ident , { "." , Ident }
Ident = Char , { Char }
Char = "a" | ... "z" | "0" | ... "9" | "-" | "_"
ws = { " " }
```
