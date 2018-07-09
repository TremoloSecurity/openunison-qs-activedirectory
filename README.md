# OpenUnison Active Directory Quickstart

This quickstart will deploy an OpenUnison integration with Active Directory.  Once deployed,
a user will be able to:

1. Register
2. Request access to groups managed by Active Directory
3. Use a self service password reset if they forget their password

In addition to the keystore created in the instructions from https://hub.docker.com/r/tremolosecurity/openunisons2idocker/ the Active Directory root certificate
should be added to the keystore.  

## Environment Variables

| Variable | Description | Example |
| -------- | ----------- | ------- |
| OU_HOST | The host name users will use to access the site | myapp.mycompany.lan |
| AD_BASE_DN | The root DN for Active Directory | cn=Users,dc=mydomain,dc=com |
| AD_HOST | The host name of the Active Directory Domain Controller | dc1.mydomain.com |
| AD_PORT | The port to communicate to Active Directory on, usually 636 |
| AD_BIND_DN | A DN with read and write access to the domain controller | cn=Administrator,cn=users,dc=domain,dc=com |
| AD_BIND_PASSWORD | The password for the AD adminsitrator service account | somesecret |
| OU_HIBERNATE_DIALECT | The hibernate dialect for your database (https://docs.jboss.org/hibernate/orm/4.2/javadocs/org/hibernate/dialect/package-summary.html) | org.hibernate.dialect.MySQL5Dialect |
| OU_JDBC_DRIVER | The JDBC driver used to talk to the database | JDBC driver for your database, make sure that the driver is a dependency in your POM file | com.mysql.jdbc.Driver |
| OU_JDBC_URL | The connection URL for the OpenUnison audit database | jdbc:mysql://mariadb:3306/unison?useSSL=true |
| OU_JDBC_USER | User used to connect to the audit database | root |
| OU_JDBC_PASSWORD | Password used to connect to the audit database | ***** |
| OU_JDBC_VALIDATION | A query for validating connections on checkout | SELECT 1 |
| SMTP_HOST | Host for the SMTP server | smtp.gmail.com |
| SMTP_PORT | Port for the SMTP Server | 587 |
| SMTP_FROM | The "From" subject of emails to approvers | You have approvals waiting |
| SMTP_USER | User name for accessing the email server | user@domain.com |
| SMTP_PASSWORD | Password for the user for the email server | ***** |
| SMTP_TLS | true/false if the SMTP server uses TLS | true |
| OU_AUDITOR_GROUP | The name (DN) of a group in Active Direct that provides access for auditors, up until the AD_BASE_DN | cn=auditors,cn=Users |
| JAVA_OPTS | List of Java system properties | -Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom  |
| OU_JDBC_PWD_URL | The JDBC URL for the password reset database | jdbc:mysql://mariadb:3306/passwordReset?useSSL=true |
| GOOGLE_CAPTCHA_SITE_KEY | Site Key from https://www.google.com/recaptcha |  XXXXXX |
| GOOGLE_CAPTCHA_SECRET | Secret from google recaptcha | XXXXXX |
| OU_SELF_REG_APPROVER_GROUP | The name (DN) of a group in Active Directory that lists approvers for self registration, up until the AD_BASE_DN | cn=self-registration-approvers,cn=Users |
| unisonKeystorePassword | Password for the keystore | secretPassword |


## Use

Once deployed, access this site by navigating to https://OU_HOST/ replacing
OU_HOST with the value of the OU_HOST environment variable.  For instance, if
OU_HOST is myapp.mycompany.lan use https://myapp.mycompany.lan/.  Once prompted
for a username and password, use a uid and password from Active Directory.  Once logged in, a page showing all headers,
request and session variables is shown.

## Production Deployment

Once you've moved past a proof of concept and wish to move into production there are some areas that should be updated:

1.  Fork the TS repo - Create a fork of this repo so you can make customizations
2.  ScaleJS pages - This repo proxies the pages directly our of their github repo.  This is great for a quick POC, but for a production deployment you should copy down the ScaleJS pages so you can make edits, update the logos, etc.
3.  Tweak the tuning parameters - All of the connection pools have parameters for the number of connections, timeouts, etc.
4.  Remove components you don't need - If you don't need user self registration, just password reset and ScaleJS go ahead and remove it.
5.  Create a database for the scheduler - See the OpenUnison documentation for specifics but you don't want scheduled tasks to be run on every OpenUnison instance, only one.
6.  Connect to a JMS Server - Just as with the scheduler, connecting to an external JMS scheduler will provide scalability and high availability across a cluster.
7.  Create separate static keys for workflows, queues, etc - The unison-session key is used throughout the configuration for simplicity.  Once you are ready for production separate keys should be used for different functions to decrease the impact of a change or if one of the keys is compromised.
