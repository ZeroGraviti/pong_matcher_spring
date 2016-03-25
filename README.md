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

## Running on [Your__Local__Boshlite__VM (I used the virtualbox provider)][bosh-lite] `version 9000.103.0`

Log in.

```bash
cf login -a api.bosh-lite.com -u admin -p admin --skip-ssl-validation
```

Target your org / space.

```bash
cf target -o myorg -s myspace
```

Create your own mysql [service instance][cups] entering sensible values for the parameters.

```bash
cf cups mysql -p "host, port, dbname, username, password"
```

Build the app.

```bash
mvn package
```

Push the app using a route name e.g., "springpong". You can choose to use `--no-route` or `--random-route` instead. Its manifest assumes you called your MySqlDB instance 'mysql'.

```bash
cf push -n springpong -m 512M
```
If you havent specified the route name as above or if your deployment is crashing into a redeployment loop, you can also try to disable the route and then re-enable it after successful deployment.
```bash
cf push --no-route
cf map-route springpong bosh-lite.com -n springpong
```

You might need to scale up or down depending on the load on your env for first time/re deployments

```bash
cf scale -m 256M springpong
```
Export the test host

```bash
export HOST=http://springpong.bosh-lite.com
```
If you wish, during deployment/restart/restage you can tail the logs using `cf logs springpong`
Since this version of bosh-lite is DEA based warden (and not Diego garden), disabling the health-check i.e., `cf set-health-check springpone none` AFAIK has no effect.


Now follow the [interaction instructions][interaction].

[bosh-lite]:https://github.com/cloudfoundry/bosh-lite
[cups]:http://docs.cloudfoundry.org/devguide/services/user-provided.html#create
[acceptance-test]:https://github.com/cloudfoundry-samples/pong_matcher_acceptance
[interaction]:https://github.com/cloudfoundry-samples/pong_matcher_grails#interaction-instructions
