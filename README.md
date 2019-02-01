**Demo project for slow gradle and springboot-gradle-plugin dependency resolution**

## Background 
We have a framework (marvincore, marvin) and the project modules itself (base, documents, stamm, rezept, thb,
abrechnung.....), everyone is taken from an seperate git repository.
In the legacy structure every module was a single gradle project, that produced a lot of artifacts (jars, wars, ears).
We used custom plugins which provided a mechanism to create ivy artifacts with complicated definition files,
which resource is added to which artifact.
For different reasons we want to go another, more mavenized way, which means that every module is migrated
to a gradle multi project module (stamm-server, stamm-client, stamm-shared......),
whereas every content of a previous artifact is migrated to another submodule.
We also provide bom modules to define which version of which module should be used only once and not to have to
define this information per submodule. This project is only a part of our hole project (50%),
so the performance values shown afterwards will be even getting worse.
Currently this is a show stopper for our intended migration.

## How to prepare with local artifactory server
I have added a docker-compose to setup a local artifactory OSS instance. To reproduce do the following:
- Call {{docker-compose up}} to setup this server (in ~/artifactory)
- Step to http://localhost/artifactory/webapp/#/home in your browser
- Step through the wizard and add a maven repository
- Configure anonymous to be able to upload any local repo via (Admin... / Users -> Anything -> Users -> Enable "Deploy/Cache")
- Call {{./gradlew prepareUpload}} in the root project to build and upload the subprojects marincore, marvin, base, documents, stamm, rezept, thb, abrechnung to local artifactory

## How to prepare with mavenLocal
When you call {{./gradlew prepareUploadLocal}} in the root project the subprojects marvincore, marvin, base, documents,
stamm, rezept, thb, abrechnung are built and published to maven local repository to have this bunch of dependencies be
usable. Afterwards take a look at ~/.m2/repository/org/moley/performance and see all the artifacts.
These dependencies (including the boms) are needed for simulating performance issues.


## Performance issue resolving boms
Call 'gradle -Dorg.gradle.debug=true --no-daemon dependencies' in integration
