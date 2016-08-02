auth-ruleset
============
Authorization Ruleset. Domain specific language to create language oriented authorization rules for your projects.

## Motivation
Handling permissions is hard. The more complex an application is, the harder is to avoid hardcoding permissions or mixing authorization logic with business logic. And this rules sometimes change and mutate to addapt to the natural and constant changes of the business, sometimes creating these complex sets of verifications where you have a role called "Editor" that can edit other's content, but only if the category of the post is the same as the category he's assigned to and if the post is not locked by a "SuperEditor" and the sun is in scorpio and the height of the author is below the average height of New Jersey's population in 1967. You know you've been there...

That's when the authorization rulesets come in handy. We separate the rules from the logic and we query that ruleset as the authority to allow or deny certain action.

The main goal of this project is to make easy the implementation of complex permission systems for the developers of any kind of apps.

## How does it work
There are two main elements involved in this schema: the ruleset and the query. The ruleset defines entities, permissions and conditions; the queries return an allow or deny recomendation.

### Authorization Ruleset
This is the set of rules the system uses as a base. It defines entities and rules. Entities are structures, a definition of how to interpret data in the context of the application. Rules define who can do what to what given a specific context.

#### Entities
To define an *entity*, we use `entity`.

```
entity IDENTIFIER
  (tag | group) by VARIABLE (primarily | as IDENTIFIER) [, more groups...]
```

The difference between **tag** and **group** is the relationship between elements.

A **tag** is a **many to many** relationship. For example, you can have an entity User tagged by role. The user can have multiple roles.

A **group** is a **one to many relationship**. For example, you can have an entity User categorized by area. The user can only have one area assigned.

The `primarily` keyword can only be used once for each entity definition, always being the first group defined.

```
entity User
  tag by roles primarily,
  group by area as area;
```

This construction will create an entity called User, and it will have two groups assigned: roles and area. This means that whatever you pass as an argument in a query using this entity must contain those variables or an exception will be thrown.

#### Rules
Usually, an action is described as a subject doing something, maybe to an object. An authorization takes into accoint this different parts: the *subject* (being the active element of the action), the *verb* (the action itself) and maybe an *object* (or *objects*, depending on the *verb*).

Rules use *entities*, *verbs* and *conditionals* to describe different scenarios given different kinds of *subjects*, *verbs* and *objects*.

Let's see an example:

```
$user:User["BASIC"][area: "Marketing"] {
  can <post:edit> $post:Post {
    if ($user.uid === $post.uid)
  } 
}
```

This construct will create a rule where a user with the role BASIC and part of the Marketing area (the *subject*) can edit (the *verb*) his own posts (the *object*), described as the post's uid being the same as the user's uid.

This first part is called the *subject selector*. The first element is setting a variable called `$user` to hold the data for the *subject* in a subsequent query. This is usefull if we want to access the subject's properties in the body of the rule. After the column `:`, it assigns a previously defined entity `User` to the *subject*. Then, it has the group filter. It accepts a list of values. The *subject* used in the query is tested against this filtering system to pick which rules apply to him. In this case, it's filtering the primary group and the `area` group for `User`: "it contains the role 'BASIC' and it's assigned to the area 'Marketing'".

The next part, between the first set of brackets, is the *permission list*. It contains a set of *permissions* starting with `can` or `can not` followed by a *verb*. The first part defines if the given *verb* will return an *allow* or *deny* recomendation if the rule applies. Please note that this rules are applied in cascade and we might override previous permissions if not used carefully. Here we are setting the *verb* `<post:edit>` as an allow.

It's important to note that this column `:` separator is used to allow wildcards in *verbs*. Eg: `<post:*>` will be aplied for `<post:edit>` and `<post:create>`.

The next part is the *object*. We set a variable `$post` and assign the entity `Post` to it. It also accepts group filtering so we can be more granular with different kinds of *objects*. We can have multiple arguments for each rule. If a required *object* is not passed as an argument in the query, it will throw an error.

The last part is the conditional list. This is a list of statements that will be resolved when querying the rule as a more granular way to filter rules. It starts with `if` or `if not`, then an expression that evaluates to truthy or falsey. Chains of ifs can be created with boolean operators (and some syntactic aliases like `but`):

```
if ($foo.bar === $bar || $foo.bar === '10')
and if (($foo.fu + 20) < 50)
but not if ($foo.biz has 'fii')
```


