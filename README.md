# CF example app: ping-pong matching server

This is an app to match ping-pong players with each other. It's currently an
API only, so you have to use `curl` to interact with it.

It has an [acceptance test suite][acceptance-test] you might like to look at.

**Note**: We highly recommend that you use the latest versions of any software required by this sample application. For example, make sure that you are using the most recent verion of maven.

## Running locally

The following assumes you have a working Java 1.8 SDK installed and Maven 3+.

Install(if you dont already have) and start mysql service. Login with an appropriate mysql client :

```bash
mysql -u root
```

Create a database user (you might want to add entries for the ip address reachable from your bosh-lite vm) and table in the MySQL REPL you just opened:

```sql
CREATE USER 'springpong'@'localhost' IDENTIFIED BY 'springpong';
CREATE DATABASE pong_matcher_spring_development;
GRANT ALL ON pong_matcher_spring_development.* TO 'springpong'@'localhost';
exit
```

Start the SpringBoot app from your IDE or the command line:

```bash
mvn spring-boot:run
```

Export the test host

```bash
export HOST=http://localhost:8080
```

Now follow the [interaction instructions][interaction].

## Running on [Your-Local-Boshlite][bosh-lite] `version 9000.103.0`

Log in.

```bash
cf login -a https://api.bosh-lite.com
```

Target your org / space.

```bash
cf target -o myorg -s myspace
```

Create your own mysql cups [cups] instance entering sensible values for the parameters.

```bash
cf cups mysql -p "host, port, dbname, username, password"
```

Build the app.

```bash
mvn package
```

Push the app using a route name e.g., "springpong". You can choose to use `--no-route` or `--random-route` instead. Its manifest assumes you called your MySqlDB instance 'mysql'.

```bash
cf push -n springpong
```

Export the test host

```bash
export HOST=http://springpong.bosh-lite.com
```

Now follow the [interaction instructions][interaction].

[acceptance-test]:https://github.com/cloudfoundry-samples/pong_matcher_acceptance
[bosh-lite]:https://github.com/cloudfoundry/bosh-lite
[interaction]:https://github.com/cloudfoundry-samples/pong_matcher_grails#interaction-instructions
