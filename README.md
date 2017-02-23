# Reproducible builds issue with gradle 3.4 and the spring boot plugin 

Gradle 3.4's reproducible builds feature should build an identical jar file each time a given source tree is compiled. With the regular java plugin, this is happening, but when the spring-boot-plugin is used this is not happening.

## Steps to reproduce the issue.
(run all commands from the root project directory)
1. Clean and build the java-plugin project twice to generate two jar files. (The clean guarantees that a new jar will be built.)
    1. `./gradlew clean java-plugin:build`
    1. `cp java-plugin/build/libs/java-plugin-0.0.1-SNAPSHOT.jar /tmp/java-plugin-1.jar`
    1. `./gradlew clean java-plugin:build`
    1. `cp java-plugin/build/libs/java-plugin-0.0.1-SNAPSHOT.jar /tmp/java-plugin-2.jar`
1. The two jars should be the same. 
    1. `shasum -a 256 /tmp/java-plugin-*.jar`
    2. (in fact the hash for each file should be `e1358db781e755047d9a6c504481d078f15bf259df4b81c23c7260b5ecd4e6ef`. Please let me know if it isn't on your machine.)
1. Clean and build the spring-boot-plugin project twice to generate two uber jar files.
    1. `./gradlew clean spring-boot-plugin:build`
    1. `cp spring-boot-plugin/build/libs/spring-boot-plugin-0.0.1-SNAPSHOT.jar /tmp/spring-boot-plugin-1.jar`
    1. `./gradlew clean spring-boot-plugin:build`
    1. `cp spring-boot-plugin/build/libs/spring-boot-plugin-0.0.1-SNAPSHOT.jar /tmp/spring-boot-plugin-2.jar`
1. These two jars _should_ be the same, but are not.
    1. `shasum -a 256 /tmp/spring-boot-plugin-*.jar`
    
I investigated the differences with [diffoscope](https://diffoscope.org/), and for me they are related to timestamps on the files in the uberjar.