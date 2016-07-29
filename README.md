auth-ruleset
============
Authorization Ruleset. Domain specific language to create language oriented authorization rules for your projects.

## Motivation
Handling permissions is hard. The more complex an application is, the harder is to avoid hardcoding permissions or mixing authorization logic with business logic. And this rules sometimes change and mutate to addapt to the natural and constant changes of the business, sometimes creating these complex sets of verifications where you have a role called "Editor" that can edit other's content, but only if the category of the post is the same as the category he's assigned to and if the post is not locked by a "SuperEditor" and the sun is in scorpio and the height of the author is below the average height of New Jersey's population in 1967. You know you've been there...

That's when the authorization rulesets come in handy. We separate the rules from the logic and we query that ruleset as the authority to allow or deny certain action.

The main goal of this project is to make easy the implementation of complex permission systems for the developers of any kind of apps.

## How does it work

