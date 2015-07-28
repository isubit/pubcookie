# Updating Drupal 6 Pubcookie to Shibboleth

Here is a quick and easy way to update from Pubcookie to Shibboleth authentication on a Drupal 6 site.

The approach is to replace the pubcookie module with a version that has been modified to use Shibboleth.

##Prequisites

You must have Shibboleth installed and working on your server before proceeding.

##Start with Pubcookie 6.x-1.2

If you are on a previous version, upgrade. You can download stock pubcookie 6.x-1.2 from [https://drupal.org/project/pubcookie](https://drupal.org/project/pubcookie)

## Modify login/.htaccess
Locate the login directory at the root of your site. Inside is an .htaccess file that looks like this:

    Authname "Drupal"
    PubcookieAppID drupal
    Authtype NetID
    Require valid-user

Change this file to instead contain:

    AuthType shibboleth
    ShibRequestSetting requireSession 1
    Require shibboleth
    ShibRedirectToSSL 443

Test that Shibboleth (outside of Drupal) is working by placing a file in login/test.txt.

Going to https://example.edu/login/test.txt should prompt you for login credentials via Shibboleth and then show you the contents of test.txt. If this test does not work you do not have Shibboleth set up correctly and should fix that before proceeding.

##Modify Drupal's .htaccess

Modify Drupal's .htaccess file at the root of your Drupal installation to add the following line at line 110:

      # Rewrite URLs of the form 'x' to the form 'index.php?q=x'.
      RewriteCond %{REQUEST_URI} !^/Shibboleth

This means that Drupal will not try to intercept requests that should go to Shibboleth.

##Back Up Pubcookie Module

Move sites/all/modules/pubcookie to a safe place.

##Get the Drop-In Replacement for Pubcookie

    cd sites/all/modules
    git clone https://github.com/isubit/pubcookie.git

##Test

Everything should work the same as before. Even the login URL is the same:

https://example.edu/login/pc

Only now authentication is being done by Shibboleth.