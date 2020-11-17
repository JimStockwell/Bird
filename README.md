# Birds - Spring Boot and Java Modules

"Birds" is the repository I made as I was following
Bruno Leite's tutorial,
"Build a Spring Boot Application Using Java Modules".
His post is
[here](https://developer.okta.com/blog/2020/07/27/spring-boot-using-java-modules#create-the-persistence-module).

The first step in his tutorial is to
(manually) set up the directory structure and Maven pom files.

## Directory Structure

I expected to find rather long,
reverse domain name format, module names.
But not so.  The modules are named simply application,
and persistence.  (I wonder if this would cause trouble if we tried to integrate this project with other projects.)

## Pom Files

The pom file in the top level directory
contains a "modules" section.
This is a list of other "modules" to build.
Those modules have seperate pom files that control them.
The text between the "module" tags is the
relative path of the
module directory or pom file.
Bruno chose to reference the directories
rather than the pom files.

The pom files also contain "parent" tags.
They look like they could almost be back-links
for module tags, but actually, they serve a different purpose.
The "parent" tags specify where a pom file gets default settings from.

