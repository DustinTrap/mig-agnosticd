# Migration Tutorial

In this tutorial, we are walked through how to successfully migrate an
application workload (selected via namespace) from OCP 3.x to OCP 4.x using the
Application Migration Tool.

First, open up the migration UI. To get the route, run the following command on
the destination cluster:
```bash
$ oc get routes migration -n mig -o jsonpath='{.spec.host}'
migration-mig.apps.cluster-dymurray-ocp4.dymurray-ocp4.mg.example.com
```

The screen should look something like:

![1](./screenshots/1.png?raw=true "1")

## Add a cluster

First thing we want to do is add the source OCP cluster we wish to migrate the
application from. Click `Add cluster`:

![1](./screenshots/1.png?raw=true "1")

Fill out the neccessary information. Be sure to see the [Setup
Document](./Setup.md#source-cluster) to get the service account token and URL.

![3-a](./screenshots/3-a.png?raw=true "3a")

When done, click `Check connection`. You should see a `Success!` message. Click
`Add`.

![3](./screenshots/3.png?raw=true "2")

## Setup an AWS S3 bucket as a replication repository

Next we want to add a replication repository. Click `Add Repository`:

![4](./screenshots/4.png?raw=true "4")

Fill out the AWS S3 bucket credential information.

![5](./screenshots/5.png?raw=true "5")

Click `Check connection` and you should see a `Success!` message appear if
everything looks good.

![5.a](./screenshots/5-a.png?raw=true "5a")

Click `Add` and view your repository on the main menu. Now that we have a
replication repository specified and both the source and destination clusters
defined, we can create a migration plan. Click `Add Plan`:

![6](./screenshots/6.png?raw=true "6")

## (Optional) View the application you wish to migrate

* Mediawiki
![mw3](./screenshots/mw3.png?raw=true "mw3")

* MSSQL
![mssql3](./screenshots/mssql3.png?raw=true "mssql3")

## Create a migration plan

Now that we have a replication repository specified and both the source and
destination clusters defined, we can create a migration plan. Click `Add Plan`:

![7](./screenshots/7.png?raw=true "7")

Fill out a plan name:

![8](./screenshots/8.png?raw=true "8")

Select the source cluster name:

![9](./screenshots/9.png?raw=true "9")

Select the namespace(s) you wish to migrate over. This will be either
`mssql-persistent` or `mediawiki`:

![10](./screenshots/10.png?raw=true "10")

Now we are displayed a list of persistent volumes associated with our
application workload. Select which type of action you would like to perform on
the PV. For this example, let's select `copy`:

![11](./screenshots/11.png?raw=true "11")

Select your migration targets. Select the previously created replication
repository and leave the target cluster field as `host`. Optionally change the
name of the namespace you wish to have on the destination cluster.

![12](./screenshots/12.png?raw=true "12")

After validating the migration plan, you will see a `Ready` message and you can
click `Close`:

![13](./screenshots/13.png?raw=true "13")

## Migrate the application workload

Now we can select `Migrate` or `Stage` on the application. Since we don't care
about downtime for this example, let's select `Migrate`:

![14](./screenshots/14.png?raw=true "14")

Optionally choose to *not* terminate the application on the source cluster.
Leave it unchecked and select `Migrate`.

![15](./screenshots/15.png?raw=true "15")

Once done, you should see `Migration Succeeded` on the migration plan:

![16](./screenshots/16.png?raw=true "16")


## Verify application is functioning on destination cluster

Let's first open the OCP 4.1 web console:

![console](./screenshots/dest.png?raw=true "console")

Click on the `mssql-persistent` or `mediawiki` namespace:

![ns](./screenshots/dest-project.png?raw=true "ns")

Click on the `mssql-app-deployment` or `mediawiki` deployment object to
retrieve the route:

![route](./screenshots/dest-route.png?raw=true "route")

Open the route and verify the application is functional:

* MSSQL
![app](./screenshots/dest-app.png?raw=true "app")

* Mediawiki
![mw4](./screenshots/mw4.png?raw=true "mw4")

## Bonus: Check out copied PV

To verify the application actually copied the PV data over to a new volume,
let's confirm we are no longer using an NFS volume. If everything worked as
expected, our OCP 4.1 cluster will use it's default storage class (gp2) to
provision an AWS EBS volume.

Click on the `Storage` tab in the web console:

![pv](./screenshots/pv1.png?raw=true "pv")

Click on the persistent volume and verify that it is using Amazon Elastic Block
Storage as the provisioner:

![pv2](./screenshots/pv2.png?raw=true "pv2")

