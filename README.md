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
To define an entity, we use `entity`.

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
Rules use the entities, permits and conditionals to define the recomendations. Rules are defined with this syntax:

```
$user:User {
  can <post:edit> $post:Post {
    if ($user.uid === $post.uid)
  } 
}
```

This construct will create a rule where a user can edit his own posts (if the post's id is the same as the user's is).
