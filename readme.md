This shows a bug in SAM CLI, version 1.1.0

The issue is that maven artifacts are not prefixed with their groupId when collected as dependencies by sam.

To inspect the issue run:

```bash

mvn install
sam build
ls .aws-sam/build/function/lib/
```

The output looks like:

```
aws-lambda-java-core-1.2.1.jar  aws-lambda-java-log4j2-1.2.0.jar  log4j-api-2.13.2.jar  log4j-core-2.13.2.jar  x-1.0.0-SNAPSHOT.jar
```

so there is only one `x-1.0.0-SNAPSHOT.jar` and `unzip -l x-1.0.0-SNAPSHOT.jar` confirms that its only one of the dependencies and not a merge:

```
Archive:  .aws-sam/build/function/lib/x-1.0.0-SNAPSHOT.jar
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  08-31-2020 13:00   META-INF/
      126  08-31-2020 13:00   META-INF/MANIFEST.MF
        0  08-31-2020 13:00   c/
      424  08-31-2020 13:00   c/MyCClass.class
        0  08-31-2020 13:00   META-INF/maven/
        0  08-31-2020 13:00   META-INF/maven/a.c/
        0  08-31-2020 13:00   META-INF/maven/a.c/x/
      587  08-31-2020 12:59   META-INF/maven/a.c/x/pom.xml
       99  08-31-2020 12:44   META-INF/maven/a.c/x/pom.properties
---------                     -------
     1236                     9 files
```

The expected result would have been to have these two as jars present

```
a.bx:jar:1.0.0-SNAPSHOT:compile
a.c:x:jar:1.0.0-SNAPSHOT:compile
```
