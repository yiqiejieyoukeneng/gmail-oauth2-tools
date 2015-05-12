# Quick Run-Through with `oauth2.py` #

`oauth2.py` is a script and library to help developers implement and debug OAuth2 support in Python. Even if you're not programming in Python, the script can be used as a standalone utility for generating and authorizing OAuth tokens, and for generating OAuth2 authentication strings from OAuth tokens.

You can [download](http://google-mail-oauth2-tools.googlecode.com/svn/trunk/python/oauth2.py) `oauth2.py` from the source repository on the web, or [checkout](http://code.google.com/p/google-mail-oauth2-tools/source/checkout) the repository with SVN. You will also need to have Python version 2.4 or greater installed on your system; it can be downloaded from the [python.org download site](http://python.org/download/).

Throughout the tutorial we'll use the hypothetical user `xoauth@gmail.com`. During testing you should substitute your own Gmail address, and in production this would be the Gmail address of your users.

## Registering An Application ##

To use OAuth2, you must have registered your application through the
[Google APIs Console](https://code.google.com/apis/console). Registration is explained in [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/accounts/docs/OAuth2).

After registering your application, go to the "API Access" tab and create a Client ID.  `oauth2.py` is designed to work with the "Installed application" application type. After creating a Client ID, the API Access tab should show a Client ID and Client secret. You will need to supply these values to `oauth2.py` in the next step.

## Creating and Authorizing an OAuth Token ##

Use `oauth2.py` to create and authorize an OAuth token.

```
$ python oauth2.py --generate_oauth2_token --client_id=364545978226.apps.googleusercontent.com --client_secret=zNrNsBzOOnQy8_O-8LkofeTR
To authorize token, visit this url and follow the directions:
  https://accounts.google.com/o/oauth2/auth?client_id=364545978226.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fmail.google.com%2F
Enter verification code:
```

Visit the URL in your browser. You may need to login to Google. Then you should see a request to grant access to your application:

![](https://github.com/google/gmail-oauth2-tools/blob/wiki/runthrough-access-grant.png)

Click the "Allow access" button, and you will be presented a verification code:

![http://google-mail-oauth2-tools.googlecode.com/files/runthrough-access-grant.png](http://google-mail-oauth2-tools.googlecode.com/files/runthrough-access-grant.png)

Copy that code and paste it in the `oauth2.py` script window, and hit `<Enter>`. `oauth2.py` will respond with a refresh token, temporary access token, and the temporary access token expiration time.

```
$ python oauth2.py --generate_oauth2_token --client_id=364545978226.apps.googleusercontent.com --client_secret=zNrNsBzOOnQy8_O-8LkofeTR
To authorize token, visit this url and follow the directions:
  https://accounts.google.com/o/oauth2/auth?client_id=364545978226.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fmail.google.com%2F
Enter verification code: 4/0YMdz2qZnlqgafVQTbw33HYh4_H-.Qi3vpYBlkokfuJJV
Refresh Token: 1/q4SaB2JMQB9I-an6F1rxJE9OkOMtfjaz1bPm1tfDpQM
Access Token: ya29.AHES6ZRKeUQwH2xhirksSUDSyjg9oPuRMMal05Ty0cfFIT_uRfESHw
Access Token Expiration Seconds: 3600
$
```

Copy the refresh token and access token somewhere, as you'll want to use them for your testing. The refresh token can be used over and over again until it is revoked. The access token expires in one hour. To view or revoke your OAuth tokens, visit this [Google Accounts page](https://www.google.com/accounts/IssuedAuthSubTokens). You can also generate a new set of tokens at any time by repeating the above steps.

## Generating an OAuth2 Authentication String ##

Next, you can use `oauth2.py` to generate a OAuth2 authentication string using the access token.

```
$ python oauth2.py --generate_oauth2_string --access_token=ya29.AHES6ZRKeUQwH2xhirksSUDSyjg9oPuRMMal05Ty0cfFIT_uRfESHw --user=xoauth@gmail.com
OAuth2 argument:
dXNlcj14b2F1dGhAZ21haWwuY29tAWF1dGg9QmVhcmVyIHlhMjkuQUhFUzZaUktlVVF3SDJ4aGlya3NTVURTeWpnOW9QdVJNTWFsMDVUeTBjZkZJVF91UmZFU0h3AQE=
```

The base64-encoded string `dXNlcj...` is what you would pass to the IMAP AUTHENTICATE command.

## Testing IMAP and SMTP authentication with OAuth2 ##

Finally, `oauth2.py` can go one step further and connect to Google Mail's IMAP server and attempt to authenticate.

```
$ python oauth2.py --test_imap_authentication --access_token=ya29.AHES6ZRKeUQwH2xhirksSUDSyjg9oPuRMMal05Ty0cfFIT_uRfESHw --user=xoauth@gmail.com

  46:20.42 > DFBN1 AUTHENTICATE XOAUTH2
  46:20.46 < +
  46:20.46 write literal size 128
  46:21.16 < * CAPABILITY IMAP4rev1 UNSELECT IDLE NAMESPACE QUOTA ID XLIST CHILDREN X-GM-EXT-1 UIDPLUS COMPRESS=DEFLATE
  46:21.16 < DFBN1 OK xoauth@gmail.com authenticated (Success)
  46:21.16 > DFBN2 SELECT INBOX
  46:21.31 < * FLAGS (\Answered \Flagged \Draft \Deleted \Seen)
  46:21.31 < * OK [PERMANENTFLAGS (\Answered \Flagged \Draft \Deleted \Seen \*)] Flags permitted.
  46:21.31 < * OK [UIDVALIDITY 634733457] UIDs valid.
  46:21.31 < * 4 EXISTS
  46:21.31 < * 0 RECENT
  46:21.31 < * OK [UIDNEXT 10] Predicted next UID.
  46:21.31 < DFBN2 OK [READ-WRITE] INBOX selected. (Success)
```

The debug protocol dump shows that `oauth2.py` successfully authenticated to IMAP (`OK xoauth@gmail.com authenticated (Success)`). The entry `write literal size 428` is where `oauth2.py` is sending the base64-encoded OAauth2 string to the server.

Similarly, `oauth2.py` can be used to login to SMTP:

```
$ python oauth2.py --test_smtp_authentication --access_token=ya29.AHES6ZRKeUQwH2xhirksSUDSyjg9oPuRMMal05Ty0cfFIT_uRfESHw --user=xoauth@gmail.com

send: 'ehlo test\r\n'
reply: '250-mx.google.com at your service, [2620:0:1002:1005:be30:5bff:fed3:17fe]\r\n'
reply: '250-SIZE 35882577\r\n'
reply: '250-8BITMIME\r\n'
reply: '250-STARTTLS\r\n'
reply: '250-ENHANCEDSTATUSCODES\r\n'
reply: '250 PIPELINING\r\n'
reply: retcode (250); Msg: mx.google.com at your service, [2620:0:1002:1005:be30:5bff:fed3:17fe]
SIZE 35882577
8BITMIME
STARTTLS
ENHANCEDSTATUSCODES
PIPELINING
send: 'STARTTLS\r\n'
reply: '220 2.0.0 Ready to start TLS\r\n'
reply: retcode (220); Msg: 2.0.0 Ready to start TLS
send: 'AUTH XOAUTH2 dXNlcj14b2F1dGhAZ21haWwuY29tAWF1dGg9QmVhcmVyIHlhMjkuQUhFUzZaUktlVVF3SDJ4aGlya3NTVURTeWpnOW9QdVJNTWFsMDVUeTBjZkZJVF91UmZFU0h3AQE=\r\n'
reply: '235 2.7.0 Accepted\r\n'
reply: retcode (235); Msg: 2.7.0 Accepted
```

## Refreshing the access token ##

Access tokens expire after one hour. Typically an application will save the refresh token permanently, and generate new access tokens as needed. `oauth2.py` can issue a new access token from a valid refresh token.

```
$ python oauth2.py --client_id=364545978226.apps.googleusercontent.com --client_secret=zNrNsBzOOnQy8_O-8LkofeTR --refresh_token=1/q4SaB2JMQB9I-an6F1rxJE9OkOMtfjaz1bPm1tfDpQM
Access Token: ya29.AHES6ZRK7YBQrD8Gq2QgbBT0fo2ogAqJ5qTzUpU3CA5957QPOvjpIA
Access Token Expiration Seconds: 3600
```
