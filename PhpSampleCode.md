# Using the PHP sample code #

The PHP sample code is available for browsing in the [repository](http://code.google.com/p/google-mail-oauth2-tools/source/browse/trunk/php/), and can also be downloaded in a [ZIP file](http://google-mail-oauth2-tools.googlecode.com/files/oauth2-php-samples-20120905.zip).

Using this sample code requires that you already have an OAuth 2.0 access token. To authorize an access token from a PHP application, use the [Google APIs Client Library for PHP](http://code.google.com/p/google-api-php-client/wiki/OAuth2). For testing, you can acquire an access token with [oauth2.py](OAuth2DotPyRunThrough.md).

## Libraries ##
The download ZIP file contains a copy of the Zend framework libraries. Alternatively, you can download the Zend framework from
[zend.com](http://framework.zend.com/download/latest), or install it with your system's software package manager.

## Downloading ##
Download the sample code package from
[here](http://google-mail-oauth2-tools.googlecode.com/files/oauth2-php-samples-20120905.zip), unzip it, and make sure the oauth2-php-sample is in a web-accessible directory.

## Running the sample app ##
Visit http://`<yourserver>`/oauth2-php-samples/oauth2.php to run the OAuth 2.0 sample. Enter a Gmail email address and an access token that is authorized to access that account, then click Submit. You should see an output of the count of messages in your inbox along with the subject lines of the emails.

## Exploring the sample code ##

The high-level sample code is in [SVN](http://code.google.com/p/google-mail-oauth2-tools/source/browse/#svn/trunk/php) along with plenty of comments.