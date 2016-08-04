```
Query           = "can" , ws
                , Entity , ws
                , PermissionIdent , ws
                , [ Entity , ws , { "," , ws , Entity , ws } ]
                , [ ws , "in the context of" , ws , JSON];

Entity          = JSON , ws , ":" , ws , Ident;

PermissionIdent = "<" , Ident , { ":" , ("*" | Ident) } , ">";

JSON            = (* SEE rfc7159 : http://rfc7159.net/rfc7159 *);
```
