

<!-- toc -->

- [CakePHP Sample App on OpenShift](#cakephp-sample-app-on-openshift)
  * [OpenShift Considerations](#openshift-considerations)
    + [Security](#security)
    + [Installation:](#installation)
    + [Debugging Unexpected Failures](#debugging-unexpected-failures)
    + [Installation: With MySQL](#installation-with-mysql)
    + [Adding Webhooks and Making Code Changes](#adding-webhooks-and-making-code-changes)
    + [Enabling the Database example](#enabling-the-database-example)
    + [Hot Deploy](#hot-deploy)
    + [Source repository layout](#source-repository-layout)
    + [Compatibility](#compatibility)
    + [License](#license)

<!-- tocstop -->

CakePHP Sample App on OpenShift
===============================

This is a quickstart CakePHP application for OpenShift v3 that you can use as a starting point to develop your own application and deploy it on an [OpenShift](https://github.com/openshift/origin) cluster.

If you'd like to install it, follow [these directions](https://github.com/sclorg/cakephp-ex/blob/master/README.md#installation).  

The steps in this document assume that you have access to an OpenShift deployment that you can deploy applications on.

OpenShift Considerations
------------------------
These are some special considerations you may need to keep in mind when running your application on OpenShift.

### Security
Since the quickstarts are shared code, we had to take special consideration to ensure that security related configuration variable values are unique across applications. To accomplish this, we modified some of the configuration files. Namely we changed Security.salt and Security.cipherSeed values in the app/Config/core.php config file. Those values are now generated from the application template as CAKEPHP_SECURITY_SALT and CAKEPHP_SECURITY_CIPHER_SEED. Also the secret token is generated in the template as CAKEPHP_SECRET_TOKEN. From these values the session hashes are generated. Now instead of using the same default values, OpenShift can generate these values using the generate from logic defined within the instant application's template.

### Installation:
These steps assume your OpenShift deployment has the default set of ImageStreams defined.  Instructions for installing the default ImageStreams are available [here](https://docs.okd.io/latest/install_config/imagestreams_templates.html#creating-image-streams-for-openshift-images).  If you are defining the set of ImageStreams now, remember to pass in the proper cluster-admin credentials and to create the ImageStreams in the 'openshift' namespace.

1. Add a PHP application from the provided template  

		$ oc process -f https://raw.githubusercontent.com/nmalvankar/cakephp-ex/master/openshift/templates/cakephp-mysql-template.yaml | oc apply -f -
		
2. Once the build is running, watch your build progress  

		$ oc logs -f bc/cakephp-example

3. Wait for cakephp-example pods to start up (this can take a few minutes):  

		$ oc get pods -w


	Sample output:  

	       NAME                      READY     REASON         RESTARTS   AGE
	       cakephp-example-1-build   0/1       ExitCode:0     0          8m
	       cakephp-example-1-pytud   1/1       Running        0          2m


4. Check the IP and port the cakephp-example service is running on:  

		$ oc get svc

	Sample output:  

	       NAME              LABELS                     SELECTOR               IP(S)           PORT(S)
	       cakephp-example   template=cakephp-example   name=cakephp-example   172.30.97.123   8080/TCP

In this case, the IP for cakephp-example is 172.30.97.123 and it is on port 8080.  
*Note*: you can also get this information from the web console.


### Enabling the Database example
In order to access the example CakePHP home page, which contains application stats including database connectivity, you have to go into the app/View/Layouts/ directory, remove the default.ctp and after that rename default.ctp.default into default.ctp`.

It will also be necessary to update your application to talk to your database back-end. The app/Config/database.php file used by CakePHP was set up in such a way that it will accept environment variables for your connection information that you pass to it. Once an administrator has created a MySQL database service for you to connect with you can add the following environment variables to your deploymentConfig to ensure all your cakephp-example pods have access to these environment variables. Note: the cakephp-mysql.json template creates the DB service and environment variables for you.

You will then need to rebuild the application.  This is done via either a `oc start-build` command, or through the web console, or a webhook trigger in github initiating a build after the code changes are pushed.


### Source repository layout

You do not need to change anything in your existing PHP project's repository.
However, if these files exist they will affect the behavior of the build process:

* **composer.json**

  List of dependencies to be installed with `composer`. The format is documented
  [here](https://getcomposer.org/doc/04-schema.md).

### Compatibility

This repository is compatible with PHP 5.6 and higher, excluding any alpha or beta versions.

### License
This code is dedicated to the public domain to the maximum extent permitted by applicable law, pursuant to [CC0](http://creativecommons.org/publicdomain/zero/1.0/).
