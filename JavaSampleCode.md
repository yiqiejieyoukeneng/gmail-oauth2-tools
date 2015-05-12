# Using the Java sample code #

the java sample code is available for browsing in the [repository](https://github.com/google/gmail-oauth2-tools/tree/master/java), and can also be downloaded in a [zip file](http://google-mail-oauth2-tools.googlecode.com/files/oauth2-java-sample-20120904.zip).

## Prerequisites ##
You'll need the following packages installed on your system to run the sample code:

  * JDK version 1.5 or higher
  * The JavaMail API version 1.4.5 or higher. This is available [alone](http://java.sun.com/products/javamail/) or as part of [Java EE](http://java.sun.com/javaee/).
  * [Apache Ant](http://ant.apache.org/)
  * An OAuth Token and Token Secret for a Gmail account. You can use [oauth2.py](OAuth2DotPyRunThrough.md) to generate these for your own account.

## Downloading and configuring ##
Download the sample code package from
[here](http://google-mail-oauth2-tools.googlecode.com/files/oauth2-java-sample-20120904.zip), unzip it, and change into the `oauth2-java-sample-20120904` directory.

Open the file `build.properties` in a text editor and modify the following properties:

  * `javamail_jar`: If you are not using a Java EE jdk and need to specifically include the location of the JavaMail JAR file, put that here. Otherwise it's ok to leave this property blank.
  * `email`: The email address of a Gmail account to which you want to authenticate.
  * `oauthToken`: A valid OAuth2 access token for the Gmail account.

Save `build.properties` and close your editor

## Running the sample app ##

Execute `ant run` to run the sample application with the parameters you specified in build.properties. If everything worked properly, you should see some debug output including the lines:

```
[...]
[java] Successfully authenticated to IMAP.
[...]
[java] Successfully authenticated to SMTP.
```

## Exploring the sample code ##

The high-level sample code is in [OAuth2Authenticator.java](http://code.google.com/p/google-mail-oauth2-tools/source/browse/trunk/java/com/google/code/samples/oauth2/OAuth2Authenticator.java). The two important methods are `initialize` and `connectToImap`. `initialize` installs a SaslClient provider of the XOAUTH2 mechanism; it needs to be called once before any calls to `connectToImap`.  `connectToImap` can be called to open new IMAP connections and authenticate to them with XOAUTH2.
