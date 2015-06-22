Preparing a target with docker
==================================

I modified the docker WordPress image of `Tutum Cloud <https://github.com/tutumcloud/tutum-docker-wordpress>`_ a little
to install the old version of WordPress.
The old version has more vulneranability so it is not difficut to find weaknesses.
I tested the version of 1.2.1 and 4.2 and both are working well.
You should refer `here <https://github.com/ymkim92/tutum-docker-wordpress>`_ to install the old version.

Sometimes the new start of container changes its IP address for example, from 172.17.0.7 to 172.17.0.8.

.. note:: To start and attach your ready-made instance, you can run this command: ::
    
      $ sudo docker start -a <CONTAINER ID>
      
      For exameple, ::

        $ sudo docker start -a e98a5273f9bf

At this time, if you want to keep working with your wordpress site,
you may add the ``WP_HOME`` and ``WP_SITEURL`` variables in ``wp-config.php``: ::

    define('WP_HOME','http://172.17.0.8');
    define('WP_SITEURL','http://172.17.0.8');

You can study more about this in https://codex.wordpress.org/Changing_The_Site_URL.



