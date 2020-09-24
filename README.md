

<!-- toc -->

- [CakePHP Sample App on OpenShift](#cakephp-sample-app-on-openshift)
  * [OpenShift Considerations](#openshift-considerations)
    + [Security](#security)
    + [Installation:](#installation)
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

1. Deploy the PHP application + MySQL from the provided template  

		$ oc process -f https://raw.githubusercontent.com/nmalvankar/cakephp-ex/master/openshift/templates/cakephp-mysql-template.yaml | oc apply -f -
		
2. Once the build is running, watch your build progress  

		$ oc logs -f bc/cakephp-example

3. Wait for cakephp-example and mysql pods to start up (this can take a few minutes):  

		$ oc get pods -w


	Sample output:  

	       NAME                               READY   STATUS      RESTARTS   AGE
			cakephp-mysql-example-1-build      0/1     Completed   0          48m
			cakephp-mysql-example-1-cdj8j      1/1     Running     0          47m
			cakephp-mysql-example-1-deploy     0/1     Completed   0          47m
			cakephp-mysql-example-1-hook-pre   0/1     Completed   0          47m
			mysql-1-2xbvk                      1/1     Running     0          48m
			mysql-1-deploy                     0/1     Completed   0          48m

4. Check the IP and port the cakephp-example & mysql services are running on:  

		$ oc get svc

	Sample output:  

	       NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
			cakephp-mysql-example   ClusterIP   172.21.235.170   <none>        8080/TCP   50m
			mysql                   ClusterIP   172.21.39.200    <none>        3306/TCP   50m

In this case, the IP for cakephp-example is 172.21.235.170 and it is on port 8080.  
*Note*: you can also get this information from the web console.


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
