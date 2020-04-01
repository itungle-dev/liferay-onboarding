# Set Up Process

#### Installation List:

- Java 7, 8, 11
- Ant 1.9
- Maven
- Git and Github cli (hub.github.com)
- Docker
- DXP Cloud (lcp cli)
- MySQL/MariaDB
- Postman

#### DXP 6 Process

##### Prerequisites

- Java version 7
  1. Create an account in oracle and download the tar.gz file for version 7
  2. Then follow this:
     https://askubuntu.com/questions/1034387/how-can-i-install-jdk7-on-ubuntu-18-04-lts-64bit?newreg=c29ea3a31dfa4419b5ce9a650326e8f1
- MySQL version 5.7
- Ant version 1.9
- Put below \*\_OPTS in bash/zsh rc file

```
export ANT_OPTS="-server -verbose:gc -Xloggc:/tmp/ant-gc.log -Xms2g -Xmx4g -Xss2m -Xverify:none -XX:+DisableExplicitGC -XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCCause -XX:+PrintGCDetails -XX:+UseParallelOldGC -XX:HeapDumpPath=/tmp/ant-oom-heap-dump.bin -XX:MaxNewSize=512m -XX:MaxPermSize=512m -XX:MaxTenuringThreshold=0 -XX:NewSize=256m -XX:PermSize=100m -XX:SurvivorRatio=65536 -XX:TargetSurvivorRatio=0"
export GRADLE_OPTS="-Xms2g -Xmx4g"
```

##### Steps: (Get tomcat/6.1.x up and running with 'magic' bundle)

1. Create a folder to house bundles, liferay-plugins-ee, liferay-portal-ee
2. Set up on IntelliJ IDEA by importing existing project
3. Go through the set up as is.
4. In terminal, go to the folder directory from #1. Run these command:
   - `ant setup-eclipse`
5. Go into bundle/tomcat-7.0.40/ (you can create a symlink of tomcat-7.0.40 to a folder called tomcat)
6. Run `./catalina.sh jpda run` to start tomcat server
7. On another terminal, go into the folder of plugins/portal on thing you want to change. - `ant clean deploy` # Run this everytime you change something.\
    Examples:
   - I changed something in portlets/osb-portlets\
   - I would go into porlets/osb-portlets\
   - Then run `ant clean deploy`\
     NOTE: `ant clean deploy` might not build in portlets but it might failed. Not sure how to fix this

#### DXP 7+ Process

- Java version 8+
  1. Install using openjdk-8-jdk might have the java path as JRE.\
     - You want JDK instead so use to install in update-alternatives
     ```
     sudo update-alternatives --install "/usr/bin/java" "java" "/usr/$JAVA8_FOLDER/bin/java" 1
     sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/$JAVA8_FOLDER/bin/javac" 1
     ```
     - Then use `sudo update-alternatives` for both `java` and `javac`
- `./gradlew clean deploy` doesn't matter if build failed, as long as `/bundles/osgi/modules` has all the `.jar` files
- Get version 7 bundles
- Remove all files in `bundles_ver7/osgi/state` and `bundles_ver/osgi/modules`
- Copy `.jar` files after `./gradlew clean deploy` from `build` folder in project.\
   Example for lfris-tma: copy `.jar` files from `lfris-tma/build/osgi/modules` to `bundles_ver7/deploy`
- Copy `.jar` dependency from `lcp/liferay/deploy/common/` (assuming using lfris-tma project) to `bundles_ver7/osgi/modules`
- If it is your first time, get a license Account setting in www.liferay.com
- Copy the `activation_license.xml` to `bundle_ver7/deploy`
- Go into `tomcat` folder and run `./catalina.sh jpda run` to start tomcat server

#### MySQL Set up Process:

##### Set up Database (6.1.x):

1. Download one of the backup .sql on files.liferay.com
2. Set up MySQL with user: root and no password
3. example command: `mysql -u root lrdcom_web < '/home/tungle-liferay/Downloads/developer_lportal-2020-02-18_11-00-PST.sql'`

##### Set up Database (7.0.+):

1. Create new database for every new project if configure with MySQL
2. Drop JDBC driver into Liferay bundle/tomcat/lib/ext folder
3. Put this in portal-ext.properties
4. `$NAME` will be the database empty/filled database. As long as you create a database name `$NAME` in your local machine, when you run tomcat bundle, it should automatically fill database with tables/schemas.

```
jdbc.default.driverClassName=com.mysql.jdbc.Driver
jdbc.default.url=jdbc:mysql://localhost/$NAME?useUnicode=true&characterEncoding=UTF-8&useFastDateParsing=false&serverTimezone=PST
jdbc.default.username=root
jdbc.default.password=
```

- If run into "You must upgrade to Liferay 7210". Maybe the jbdc.default.url is point toward lrdcom_web

## Step Process for solving ticket.

1. Assign the ticket to yourself
2. Move the ticket to In Progress column of JIRA board
3. Checkout a new branch name: $TicketName_$SmallDescription; For example: LRIS1234-Update-Email
4. Solve your problem. And clean up and debug/sysout.
5. When you ready to do a commit. Run `ant format-source` for 6.1.x stuff or `./gradlew formatSource` for the 7.0+
   - If you are changing anything in language/properties file, then `ant build-lang` for 6.1.x or `./gradlew buildLang` for 7.0+
6. Make a commit. with message, "\$TicketName; Description of the solution or outcome"
7. Create a pull request to Ryan or your manager instead of liferay repo. if use, github cli, gh pr create --web will show a web UI to send the right pull request to the right person and repo
