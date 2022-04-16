
build.gradle

```shell
plugins {
    id 'org.springframework.boot' version '2.5.5'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'com.google.cloud.tools.jib' version '3.1.4'
    id 'java'
}

jib.to.image = 'chrislomeli/api-gatewy'
```

the just 

```shell
 gradle jib
```

will copy it all the way to the repo with no local image

