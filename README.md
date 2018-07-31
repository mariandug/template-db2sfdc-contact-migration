
# Anypoint Template: Database to Salesforce Contact Migration

+ [License Agreement](#licenseagreement)
+ [Use Case](#usecase)
+ [Considerations](#considerations)
	* [DB Considerations](#dbconsiderations)
	* [Salesforce Considerations](#salesforceconsiderations)
+ [Run it!](#runit)
	* [Running on premise](#runonopremise)
	* [Running on Studio](#runonstudio)
	* [Running on Mule ESB stand alone](#runonmuleesbstandalone)
	* [Running on CloudHub](#runoncloudhub)
	* [Deploying your Anypoint Template on CloudHub](#deployingyouranypointtemplateoncloudhub)
	* [Properties to be configured (With examples)](#propertiestobeconfigured)
+ [API Calls](#apicalls)
+ [Customize It!](#customizeit)
	* [config.xml](#configxml)
	* [businessLogic.xml](#businesslogicxml)
	* [endpoints.xml](#endpointsxml)
	* [errorHandling.xml](#errorhandlingxml)


# License Agreement <a name="licenseagreement"/>
Note that using this template is subject to the conditions of this [License Agreement](AnypointTemplateLicense.pdf).
Please review the terms of the license before downloading and using this template. In short, you are allowed to use the template for free with Mule ESB Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case <a name="usecase"/>
As a Salesforce admin I want to synchronize Contacts from a database to Salesforce in a one-time manner.

This Template should serve as a foundation for setting an online migration of Contacts from a database to a Salesforce instance. Everytime the HTTP endpoint is triggered, the integration will migrate all the contacts in the database in manner of one time integration and it will be responsible for updating or inserting the Contact on the target Salesforce instance.

Requirements have been set not only to be used as examples, but also to establish a starting point to adapt your integration to your requirements.

As implemented, this Template leverages the [Batch Module](http://www.mulesoft.org/documentation/display/current/Batch+Processing).
The batch job is divided in Process Records and On Complete stages.
The integration is triggered by HTTP endpoint defined in the flow that is going to trigger the application, querying the database contacts matching a filter criteria and executing the batch job.
During the Process Records stage, each database Contact will be filtered depending on if it has an existing matching Contact in the Salesforce instance.
The last step of the Process stage will group the Contacts and insert/update them in Salesforce.
Finally during the On Complete stage the Template will log output statistics data into the console and send an email with the results.

In this template you may choose whether Account for Contact should be created as well during the process.

# Considerations <a name="considerations"/>

To make this Anypoint Template run, there are certain preconditions that must be considered. All of them deal with the preparations in both source (Database) and destination (Salesforce) systems, that must be made in order for all to run smoothly. 
**Failing to do so could lead to unexpected behavior of the template.**

This particular Anypoint Template illustrates the migration use case between a Database and Salesforce, thus it requires a Database instance to work.
The Anypoint Template comes packaged with a SQL script to create the Database table that uses. It is the user responsibility to use that script to create the table in an available schema and change the configuration accordingly. The SQL script file can be found in [src/main/resources/contact.sql](../master/src/main/resources/contact.sql)

## DB Considerations <a name="dbconsiderations"/>

There may be a few things that you need to know regarding DB, in order for this template to work.

This Anypoint Template may be using date time/timestamp fields from the DB in order to do comparisons and take further actions.
While the template handles the time zone by sending all such fields in a neutral time zone, it can not handle **time offsets**.
We define as **time offsets** the time difference that may surface between date time/timestamp fields from different systems due to a differences in the system's internal clock.
The user of this template should take this in consideration and take the actions needed to avoid the time offset.

### As source of data

There are no particular considerations for this Anypoint Template regarding DB as data origin.


## Salesforce Considerations <a name="salesforceconsiderations"/>

There may be a few things that you need to know regarding Salesforce, in order for this template to work.

In order to have this template working as expected, you should be aware of your own Salesforce field configuration.

### FAQ

 - Where can I check that the field configuration for my Salesforce instance is the right one?

    [Salesforce: Checking Field Accessibility for a Particular Field][1]

- Can I modify the Field Access Settings? How?

    [Salesforce: Modifying Field Access Settings][2]


[1]: https://help.salesforce.com/HTViewHelpDoc?id=checking_field_accessibility_for_a_particular_field.htm&language=en_US
[2]: https://help.salesforce.com/HTViewHelpDoc?id=modifying_field_access_settings.htm&language=en_US


### As destination of data

There are no particular considerations for this Anypoint Template regarding Salesforce as data destination.









# Run it! <a name="runit"/>
Simple steps to get Database to Salesforce Contact Migration running.
**Note:** This particular Anypoint Template illustrates the migration use case between Salesforce and a Database, thus it requires a Database instance to work.
The Anypoint Template comes packaged with a SQL script to create the database table that uses. 
It is the user responsibility to use that script to create the table in an available schema and change the configuration accordingly. The SQL script file can be found in [src/main/resources/contact.sql](../master/src/main/resources/contact.sql).

This template is customized for MySQL. To use it with different SQL implementation, some changes are necessary:

* update SQL script dialect to desired one
* replace MySQL driver library (or add another) dependency to desired one in [POM](pom.xml)
* update Database Config to suitable connection instead of `db:my-sql-connection` in global elements (config.xml)
* update connection configurations in `mule.*.properties` file

In any of the ways you would like to run this Template this is an example of the output you'll see after hitting the HTTP endpoint:

	{
	  "Message": "Batch Process initiated",
	  "ID": "7fc674b0-e4b7-11e7-9627-100ba905a441",
	  "RecordCount": 32,
	  "StartExecutionOn": "2017-12-19T13:24:03Z"
	}

## Running on premise <a name="runonopremise"/>
In this section we detail the way you should run your Anypoint Template on your computer.


### Where to Download Mule Studio and Mule ESB
First thing to know if you are a newcomer to Mule is where to get the tools.

+ You can download Mule Studio from this [Location](http://www.mulesoft.com/platform/mule-studio)
+ You can download Mule ESB from this [Location](http://www.mulesoft.com/platform/soa/mule-esb-open-source-esb)


### Importing an Anypoint Template into Studio
Mule Studio offers several ways to import a project into the workspace, for instance: 

+ Anypoint Studio Project from File System
+ Packaged mule application (.jar)

You can find a detailed description on how to do so in this [Documentation Page](http://www.mulesoft.org/documentation/display/current/Importing+and+Exporting+in+Studio).


### Running on Studio <a name="runonstudio"/>
Once you have imported you Anypoint Template into Anypoint Studio you need to follow these steps to run it:

+ Locate the properties file `mule.dev.properties`, in src/main/resources
+ Complete all the properties required as per the examples in the section [Properties to be configured](#propertiestobeconfigured)
+ Once that is done, right click on you Anypoint Template project folder 
+ Hover you mouse over `"Run as"`
+ Click on  `"Mule Application (configure)"`
+ Inside the dialog, select Environment and set the variable `"mule.env"` to the value `"dev"`
+ Click `"Run"`


### Running on Mule ESB stand alone <a name="runonmuleesbstandalone"/>
Complete all properties in one of the property files, for example in [mule.prod.properties] (../master/src/main/resources/mule.prod.properties) and run your app with the corresponding environment variable to use it. To follow the example, this will be `mule.env=prod`. 
After this, to trigger the use case you just need to hit the local HTTP connector with the port you configured in your file. If this is, for instance, `9090` then you should hit: `http://localhost:9090/migratecontacts` and this will output a summary report and send it in the e-mail.

## Running on CloudHub <a name="runoncloudhub"/>
While [creating your application on CloudHub](http://www.mulesoft.org/documentation/display/current/Hello+World+on+CloudHub) (Or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in **Properties to be configured** as well as the **mule.env**.
While [creating your application on CloudHub](http://www.mulesoft.org/documentation/display/current/Hello+World+on+CloudHub) (Or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in **Properties to be configured** as well as the **mule.env**. 
Follow other steps defined [here](#runonpremise) and once your app is all set and started, there is no need to do anything else. Once your app is all set up and started, supposing you choose contactsmigration as domain name to trigger the use case, you just need to hit http://contactsmigration.cloudhub.io/migratecontacts and report will be sent to the e-mail configured.

### Deploying your Anypoint Template on CloudHub <a name="deployingyouranypointtemplateoncloudhub"/>
Mule Studio provides you with really easy way to deploy your Template directly to CloudHub, for the specific steps to do so please check this [link](http://www.mulesoft.org/documentation/display/current/Deploying+Mule+Applications#DeployingMuleApplications-DeploytoCloudHub)


## Properties to be configured (With examples) <a name="propertiestobeconfigured"/>
In order to use this Mule Anypoint Template you need to configure properties (Credentials, configurations, etc.) either in properties file or in CloudHub as Environment Variables. Detail list with examples:
### Application configuration
**Application configuration**

+ http.port `9090`
+ page.size `200`
    
**Syncing policy for accounts**

+ account.sync.policy `syncAccount`

**Note:** the property **account.sync.policy** can take any of the two following values: 

+ **doNotCreateAccount**: if the propety has no value assigned to it then application will do nothing in what respect to the account and it'll just move the contact over.
+ **syncAccount**: it will try to create the contact's account if this is not pressent in the Salesforce instance.

**Database Connector configuration**

+ db.host `localhost`
+ db.port `3306`
+ db.user `user-name`
+ db.password `user-password`
+ db.databasename `dbname`

**Note:** If it is required to connect to a different Database there should be provided the jar for the library and changed the value of that field in the connector.

**Salesforce Connector configuration**

+ sfdc.username `joan.baez@orgb`
+ sfdc.password `JoanBaez456`
+ sfdc.securityToken `ces56arl7apQs56XTddf34X`

**SMTP Services configuration**

+ smtp.host `smtp.gmail.com`
+ smtp.port `587`
+ smtp.user `johndoe%40gmail.com`
+ smtp.password `password`

**E-mail Details**

+ mail.from `batch.contact.migration%40mulesoft.com`
+ mail.to `user@example.com`
+ mail.subject `Batch Job Finished Report`

# API Calls <a name="apicalls"/>
Salesforce imposes limits on the number of API Calls that can be made. Therefore calculating this amount may be an important factor to consider. Contact Migration Template calls to the API can be calculated using the formula:

*** 1 + X + X + X / 200*** 

Being ***X*** the number of Contacts to be synchronized on each run. 

The division by ***200*** is because, by default, Contacts are gathered in groups of 200 for each Upsert API Call in the commit step.	

For instance if 10 records are fetched from origin instance, then 31 api calls will be made (1 + 10 + 10 + 10) - considering sync Account policy enabled.


# Customize It!<a name="customizeit"/>
This brief guide intends to give a high level idea of how this Anypoint Template is built and how you can change it according to your needs.
As mule applications are based on XML files, this page will be organized by describing all the XML that conform the Anypoint Template.
Of course more files will be found such as Test Classes and [Mule Application Files](http://www.mulesoft.org/documentation/display/current/Application+Format), but to keep it simple we will focus on the XMLs.

Here is a list of the main XML files you'll find in this application:

* [config.xml](#configxml)
* [endpoints.xml](#endpointsxml)
* [businessLogic.xml](#businesslogicxml)
* [errorHandling.xml](#errorhandlingxml)


## config.xml<a name="configxml"/>
Configuration for Connectors and [Configuration Properties](http://www.mulesoft.org/documentation/display/current/Configuring+Properties) are set in this file. **Even you can change the configuration here, all parameters that can be modified here are in properties file, and this is the recommended place to do it so.** Of course if you want to do core changes to the logic you will probably need to modify this file.

In the visual editor they can be found on the *Global Element* tab.


## businessLogic.xml<a name="businesslogicxml"/>
Functional aspect of the Template is implemented on this XML, directed by one flow that will check for Salesforce creations/updates. The several message processors constitute four high level actions that fully implement the logic of this Template:

1. During the Input stage the Template will go to the Database and query all the existing Contacts that match the filter criteria.
2. During the Process Records stage, each Database Contact will be checked by name against Salesforce, if it has an existing matching objects in database.
3. Account associated with Database Contact is migrated to Account associated with Contact in Salesforce. The matching is performed by querying a Salesforce instance for an entry with name same as the given Database Account name.
4. Then the upsert of Contact in Salesforce is performed.
5. Finally during the On Complete stage the Template will logoutput statistics data into the console and send email.



## endpoints.xml<a name="endpointsxml"/>
This is the file where you will found the inbound and outbound sides of your integration app.
This Template has only an [HTTP Listener Connector](http://www.mulesoft.org/documentation/display/current/HTTP+Listener+Connector) as the way to trigger the use case.
### Inbound Flow
**HTTP Listener Connector** - Start Report Generation

+ `${http.port}` is set as a property to be defined either on a property file or in CloudHub environment variables.
+ The path configured by default is `migratecontacts` and you are free to change for the one you prefer.
+ The host name for all endpoints in your CloudHub configuration should be defined as `localhost`. CloudHub will then route requests from your application domain URL to the endpoint.
+ The endpoint is a *request-response* and a result of calling it is the response with the total records fetched by the criteria specified.



## errorHandling.xml<a name="errorhandlingxml"/>
This is the right place to handle how your integration will react depending on the different exceptions. 
This file holds a [Error Handling](http://www.mulesoft.org/documentation/display/current/Error+Handling) that is referenced by the main flow in the business logic.



