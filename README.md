# Birds - Spring Boot and Java Modules

"Birds" is the repository I made as I was following
Bruno Leite's tutorial,
"Build a Spring Boot Application Using Java Modules".
His post is
[here](https://developer.okta.com/blog/2020/07/27/spring-boot-using-java-modules#create-the-persistence-module).

The following are my notes as I work my way through the tutorial.

## Directory Structure

The first step in Bruno's tutorial is to
(manually) set up the directory structure and Maven pom files.

I expected to find rather long,
reverse domain name format, module names.
But not so.  The modules are named simply application,
and persistence.  (I wonder if this would cause trouble if we tried to integrate this project with other projects.)

## Pom Files

The pom file in the top level directory
contains a "modules" section.
This is a list of other "modules" to build.
Those modules have separate pom files that control them.
The text between the "module" tags is the
relative path of the
module directory or pom file.
Bruno chose to reference the directories
rather than the pom files.

The pom files also contain "parent" tags.
They look like they could almost be back-links
for module tags, but actually, they serve a different purpose.
The "parent" tags specify where a pom file gets default settings from.

## Directory and Pom File Checkpoint

At this point, with the directory structure in place,
and the pom files made, we can compile, with `mvn compile`.

(There is nothing to compile, but that just makes the compile cycle all the faster!)

## First pass at persistence module

The persistence module isn't really a module after just the first steps of Bruno's tutorial.
The plan is to create the persistence module as just another package,
then later, move that package into a module of its own.

In the persistence "module", Bruno creates three interfaces or classes.
- Bird, which represents an item that is dealt with by the database.
  - Bird has getters and setters through which it is persisted.
  - Bird has one private member annotated @Id.
    It's interesting that either the example is poor,
    or Spring is clever enough to infer what setter and getter matches the member marked with @Id.
- BirdRepository, an interface to classes that can store Bird objects.
  - It is a very simply declared interface.
    It just extends MongoRepository by specifying the class to store (Bird) and the key type (String).
- BirdPersistence, which handles persisting and restoring Birds.
  It also populates our database with an initial sample entry.
  - The class is annotated with @Component so that it can be
    auto-detected in annotation based configuration,
    becoming controlled by an ApplicationContext container.
    This makes the BirdPersistence class a "Spring Bean".
  - The class constructor is annotated with @Autowired
    so that the member field birdRepository
    will get set with a nicely instantiated BirdRepository.
    (Note that per [this Spring documentation](https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/htmlsingle/#beans-autowired-annotation),
    @Autowired is not really necessary in this case
    as there is only one constructor).
  - One of the methods is annotated with @PostConstruct,
    signifying that it is to be called
    after the object has had its dependency injection done,
    but before it is put into service.
    In this case, the method auto-populates the repository
    with a sample bird.
    That is why that method can't run
    until after dependency injection completes.

In the application "module", Bruno creates two interfaces or classes.
- SprintBootModulesApplication,
  which launches our application.
  - It is annotated with @SpringBootApplication,
    which arranges for auto-configuration.
- BirdController,
  which exposes our app through a REST API.
  - BirdController is annotated with @RestController,
    making it a the business logic part of a REST server.
  - To identify what method handles GET requests,
    one method is annotated with GetMapping.
  - Another method is annotated with @PostMapping
    for the comparable purpose.

## Functional App Checkpoint

And that's it.
That makes a functional app,
though not yet a module using one.

Let's check its functionality.

Per Bruno, we launch a docker image of Mongo,
then install and run our app.
Test it minimally by pointing Safari at it.

Wow!  It works!

## Okta integration

I'm skipping that part of the tutorial,
as my interest is in modularizing the code,
and in getting familiar with Spring Boot.

## Modularizing the Code

The tutorial provides a module-info file for the persistence module.
What happens if we take out the `requires` declarations?

We get errors of the form: "package X is not visible (X is declared in the unnamed module, but this module does not read it)" related to the `import` statements.  What does this mean?
- It means that X cannot be used (is not visible) because it is not in a `requires` clause (not read).
  And the error message tells us where it is so we can `require` it from.

### Modular Structure

The module names chosen are reverse domain name prefixe,
but do not correspond to the directory structure.

The file structure is:
- big project, containing several Maven modules
  - Maven module 1
    - src/java/main - this is the root of the Java module, where the module-info.java file is.
      - Note that in the module-info.java file, the module is named.
        The module name has nothing to do with its location in the directory tree.
        Reverse domain name format is used for the module name,
        but that has nothing to do with the package names, or I repeat, with the module location.
      - Note that the packages in the module are in their usual "." -> "/" package format
        under the module root.
      - Note that what are exported and imported in a module-info.java file are modules.
        In the case of requiring packages that end up in the unnamed module,
        they are required by their package name.
  - Maven module 2
    - src/java/main - this is the root of the Java module, where the module-info.java file is.

## After Modularizing

It still works!