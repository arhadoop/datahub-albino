# Commerce with Datahub

This repository provides an example of how to run Model T Hybris Enterprise Commerce with Datahub. 

# Datahub Configuration

List your extensions in datahub/manifest.json. This can include 

## Custom Extensions

Follow the examples of datahub/bicycle-apparel and datahub/model-t-example. Create a folder for custom extensions in datahub/[extension-name]
Then add the folder name to datahub/manifest.json. 

## Datahub Configuration Properties

Configuration properties that you want to pass to datahub go in the `datahub-environment.conf` file. This file is in HOCON format which is slightly different than java properties format. See [Hocon Documentation](https://github.com/lightbend/config/blob/master/HOCON.md) for more information. 

In Model-T you can have several environments, for dev, stage and prod. If you want to override a small set of datahub configuration properties for a specific environment, you can create a configuration file named `datahub-environment-[environment name].conf` in the `datahub/config` directory. For example, if you have an environment named "stage" you would create a file named `datahub/config/datahub-environment-stage.conf`.

## Generating Encryption Key

Before you run datahub, you must generate your own encryption key and check it  into this git repository. On a Mac or Linux machine you can generate the key by running this command: 

```bash
openssl enc -aes-128-ecb -k secret -P -md sha1 | perl -n -e '/^key=(.*)$/ && print "$1\n"' >  encryption-key.txt
```

Then move the encryption-key.txt file to datahub/config/encryption-key.txt

Note that this file is referenced in datahub/config/datahub-environment.conf on this line: 

```conf
datahub.encryption.key.path="/opt/model-t/config/encryption-key.txt"
```

## Trusted SSL Certificates

Sometimes Datahub needs to publish data to systems via SSL to internal systems running with self-signed certificates. To configure Datahub to trust the SSL certificates used by your internal systems, put the certificate files from these servers into the datahub/config/trusted-certificates folder. 

You can load the cer file from the server by running this command. 

```bash 
openssl s_client -showcerts -servername www.example.com -connect www.google.com:443 </dev/null 
```

Then, copy each section starting with the "-----BEGIN CERTIFICATE-----" line and ending after the "-----END CERTIFICATE-----" line into a file ending in ".cer" in trusted-certificates folder.

# Using Datahub

## Configuring the Datahub BackOffice

To get datahubbackoffice working, you need to tell Backoffice exactly where the datahub service is. You only need to do this the first time you deploy datahub. 

1. Log into your Hybris Administration Console by navigationg to https://[your model-t backoffice url]/hac/ 
1. Navigate to "console/impex import"
1. Copy and paste the following into the "Import Content" field
```impex

# DataHub BackOffice Server Configurations
INSERT_UPDATE DataHubInstanceModel;instanceName[unique=true];instanceLocation
;Model T DataHub;http://datahub:8080/datahub-webapp/v1

```
4. Click the "import content" button
5. Log into the backoffice https://[your model-t backoffice url]/backoffice/ and use the backoffice administration as usual.

