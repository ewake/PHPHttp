PHP Class for Handling .htpasswd and .htgroup (Member Login & User Management)
============

Original HtPasswd and HtGroup classes by [Yang Yang](http://www.kavoir.com/2012/04/php-class-for-handling-htpasswd-and-htgroup-member-login-user-management.html)

Apache is a marvelous web server that offers .htpasswd and .htgroup for controlling restricted access to your website. By help of .htaccess, they work as a member login & user management system that is so simple and easy to deploy. You can even define user groups / roles with it.

## Basically

 * .htpasswd defines pairs of username & password that are user accounts.
 * .htgroup defines groups / roles of user accounts that can be access-controlled as a whole.
 * .htaccess then applies .htpasswd and .htgroup to the current directory, and specifies which groups in .htgroup has access to the current directory.

For example, we have

/home/myuser/.htpasswd
```txt
user1:{SHA}kGPaD671VNU0OU5lqLiN/h6Q6ac=
user2:{SHA}npMqPEX3kPQTo+x/+ZckHDrIcQI=
user3:{SHA}q1Fh2LTUjjkncp11m0M9WUH5Zrw=
```

/home/myuser/.htgroup
```txt
admin: user2
editor: user1 user3
writer: user3
```

/home/myuser/public_html/example.com/member/.htaccess
```txt
AuthName "Members Area"
AuthType Basic
AuthUserFile /home/myuser/.htpasswd
AuthGroupFile /home/myuser/.htgroup
<Limit GET POST>
require group admin
require group writer
</Limit>
```

What they do is only let users in the admin and writer group, that is user2 and user3, to access example.com/member. When someone tries to access example.com/member, Apache would prompt him or her for user name and password, and he or she must be either user2 or user3 to access it – they must enter the correct password set out in .htpasswd for user2 or user3.

user1 isn’t allowed to access example.com/member even if the password is correct. You get the idea.

You can place .htaccess anywhere in your website, and it will control access to the directory it’s in by the defined rules (which groups / roles are allowed to access). Just make sure it is pointing to the right .htpasswd and .htgroup by AuthUserFile and AuthGroupFile.

And you can have multiple .htaccess in different directories of your website, using the same .htpasswd and .htgroup.

This is so simple yet so very handy in creating & managing different users and user roles (.htpasswd, .htgroup) and giving them permissions (.htaccess) in accessing different website assets.

## PHP Class

Now that you are familiar with the basic authentication and native user management system in Apache, you can use this two simple PHP classes to automate tasks such as user creation, user deletion, adding user to group, and removing user from group.

## Usage

First, you should use your own $salt. Change the value of $salt in the Htpasswd class to something else for your own application.

To instantiate the classes:

```php
$passwdHandler = new Htpasswd('/home/myuser/.htpasswd');
$groupHandler = new Htgroup('/home/myuser/.htgroup');
```

To create and delete a user:

```php
// Add a user with name 'user1' and password 'I prefer to use passphrase rather than password.' if it doesn't exist in .htpasswd.
$passwdHandler->addUser('user1', 'I prefer to use passphrase rather than password.');
// Delete the user 'user1' if it exists in .htpasswd.
$passwdHandler->deleteUser('user1');
```

To check if a particular user exists in .htpasswd:

```php
// Check if user 'user1' exists in .htpasswd.
if ($passwdHandler->doesUserExist('user1')) {
    // User 'user1' exists.
}
```

To get the clear text password for a particular user (Apache stores passwords in .htpasswd as encoded strings):

```php
// Get the clear password for user 'user1'.
echo $passwdHandler->getClearPassword('user1');
```

To add a user to a group:

```php
// Add user 'user1' to group 'admin' in .htgroup. Group will be automatically created if it doesn't exist.
$groupHandler->addUserToGroup('user1', 'admin');
```

To delete a user from a group (user still exists in .htpasswd, just not associated with the group any more):

```php
// Delete user 'user1' from group 'admin' in .htgroup. Group will be automatically removed if it doesn't contain any users.
$groupHandler->deleteUserFromGroup('user1', 'admin');
```

To get a list of groups a particular user is assigned to:

```php
/* Get an array of groups that 'user1' is a member of. */
$user_groups = $groupHandler->getGroupsByUser('user1');
```

## Conclusion

This ain’t concluded. It’s just an END notice. Feel free to let me know your thoughts and how my classes work for you.

## Links

 * http://www.kavoir.com/2012/04/php-class-for-handling-htpasswd-and-htgroup-member-login-user-management.html
 * https://github.com/rafaelgou/php-apache2-basic-auth-manager
