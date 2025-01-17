---
layout: default
title: Combining generation and custom code
sitemap:
priority: 0.1
lastmod: 2021-09-09T21:22:00-00:00
---

__Tip submitted by [@tcharl](https://github.com/tcharl)__

# Combining generation and custom code


_Goal:_ Jhipster is very good for managing your model entities, thanks to its powerful Domain Specific language.
But getting the best both custom code and generative world is  always a hard task.
Here are the different pattern you can adopt in order to make it real. 

## Pattern 1 - Generate once

This approach is the simplest one, and used in most of the use case.
It consists in modeling your entities once, generate the first model, then override what you want after this first shot.
If one day you want to resync, you can always regen in another branch, the compare both codes through your IDE.
However, that later process is always painful and you can spend days for a major upgrade.

### Pro

 - Do what you want

### Con
 
 - You'll tend do not benefit from JHipster new features

## Pattern 2 - Split generated code and custom code

With this one, you'll try to avoid modifying generated class and to host your custom code in dedicated ones.
Here, you can use the --with-generated-flag jhipster cli option in order to easily differentiate the generated classes from your custom ones.
Finally, you'll only modify the main router on frontend part in order to route to your custom home page instead of the generated one.

In order to avoid your router file being overriden at each generation, you can create a `.yo-resolve` file at the root of your project and tell to yeoman the expected behavior.

Example:
```
src/main/resources/swagger/api.yml skip
src/main/webapp/app/modules/home/home.tsx skip
```

### Pro

- Can combine generation and custom code without so much hassle

### Con

- Dead code
- Custom classes that have different names or package than your model (can be considered as a DDD best practice but still).

## Pattern 3 - Side by side

Here, the goal is to use classes extensions and beans precedence in order to inject your custom code instead of the generated one.

Let's take an example with a `Customer` jhipster entity.

### Repository

At the repository level, you'll annotate jhipster generated repository using `NoRepositoryBean` annotation in order to disable discovery.
You can then create your custom repository class
```
@Repository
@Primary
MemberRepositoryPrimary extends MemberRepository
```

### Service

Here, you'll use the `serviceImpl` option in order to be able to inject your custom Bean in your Controller.
Then, you can simply extends the generated service and annotate your bean with `@Primary` in order to get precedence.

### Controller

You'll use another API prefix for your custom endpoints (for example `/api/v2`).

### Angular

Same extension applies on the frontend side, you can then configure your beans precedence in the `app.module.ts` file:
```
providers: [
// keep other entries
{ provide: MemberDomainService, useExisting: MemberDomainServicePrimary },
]
```

### Pro

- Can override generated code behavior
- Easy to find custom code
- Keep the Jhipster best layout even for custom code

### Con

- File duplication
