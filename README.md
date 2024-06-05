# BaCon
Basic DI Container for Unity engine. Supports registrations as single, as transient and registration instances. Tags are supported as well.

## Installation
Just download and import unitypackage file [from this page](https://github.com/vavilichev/BaCon/releases/tag/v.1.0)



# What it can

## Scoping

When you use scoping the only thing you should understand: child container can resolve instances from registered in the parent container AND parent container CAN'T resolve instances from registered in the child container.

```
var projectContainer = new DIContainer();
var sceneContainer = new DIContainer(projectContainer);
```

## Types of registrations

### As Single
Or singleton, only one instance can be created by resolving, if you are resolving many times, the same instance returns each time:
```
projectContainer.RegisterFactory(_ => new ExampleProjectService()).AsSingle();  // Without tags
projectContainer.RegisterFactory("my_awesome_tag_1", _ =>new ExampleProjectService()).AsSingle();  // Using tags
projectContainer.RegisterFactory("my_awesome_tag_2", _ =>new ExampleProjectService()).AsSingle();  // Using tags
```

### As Transient
Each time creates a new instance of an object:
```
projectContainer.RegisterFactory(_ => new ExampleProjectTransient());  // Without tags
```



            // Registration as singleton with resolving
            projectContainer.RegisterFactory(c => new ExampleAnotherProjectService(c.Resolve<ExampleProjectService>())).AsSingle();
            

            // Registration in the scene container. This container can see project scope therefore we can use resolving project instances.
            sceneContainer.RegisterFactory(c => new ExampleSceneService(c.Resolve<ExampleProjectTransient>()));

            // Resolving instances from the project scope with sceneContainer. It works properly
            var exampleProjServiceWithoutTag = sceneContainer.Resolve<ExampleProjectService>();
            var exampleProjServiceWithTag1 = sceneContainer.Resolve<ExampleProjectService>("my_awesome_tag_1");
            var exampleProjServiceWithTag2 = sceneContainer.Resolve<ExampleProjectService>("my_awesome_tag_2");
            
            // Different values because of the transient, not singleton.
            var exampleProjectTransient1 = sceneContainer.Resolve<ExampleProjectTransient>();
            var exampleProjectTransient2 = sceneContainer.Resolve<ExampleProjectTransient>();

            // Register instances
            sceneContainer.RegisterInstance(new ExampleObject());
            sceneContainer.RegisterInstance("my_awesome_object_tag", new ExampleObject());

            // Call parent from child. Works correct
            var exampleProjectService = projectContainer.Resolve<ExampleProjectService>();  // Works fine
            var exampleProjectServiceCorrect = sceneContainer.Resolve<ExampleProjectService>(); // Works fine

            // Call child from parent. It's an error;
            var exampleSceneObject1 = sceneContainer.Resolve<ExampleObject>(); // Works fine
            var exampleSceneObject2 = projectContainer.Resolve<ExampleObject>();    // incorrect
