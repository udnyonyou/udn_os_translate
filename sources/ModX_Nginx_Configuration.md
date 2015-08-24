# ModX Nginx Configuration

原文:http://www.nginxtips.com/modx-nginx-configuration/

ModX Revolution is an easy, all-in-one CMS and application framework, an alternative to other common CMS, like WordPress or Joomla. This is a free software, licensed under GPL, and it is 100% compatible with Nginx and PHP 5.1.1 and above.

ModX includes WYSIWYG rich-text editors and full control over all metadata and URL structures (friendly URLs) to improve search engine rankings.

In terms of dev features, it’s an object-oriented, MVC-compliant core code. It supports these ajax libraries: ExtJS, jQuery, Prototype and MooTools. It also supports MySQL and MSSQL databases.

Today, you will learn how to configure ModX on your existing Nginx web server.

# How Can You Configure ModX On Nginx?

View this example config for a ModX installation on an Nginx server (php-fpm is required for Nginx servers):


    server {
    listen 80;
    server_name nginxtips.com;
    root /var/www/nginxtips.com;
    index index.php index.html;
    access_log off;
    log_not_found off;
    error_log /etc/nginx/logs/nginxtips.com_error.log warn;

        # modx specific	config as seen on official docs
        location / {
                if (!-e $request_filename) {
                        rewrite ^/(.*)$ /index.php?q=$1 last;
                }
        }

	# php-fpm configuration
        location ~ \.php$ {
            try_files $uri =404;
            fastcgi_pass   unix:/tmp/php5-fpm.sock;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
            fastcgi_buffer_size 128k;
            fastcgi_buffers 256 4k;
            fastcgi_busy_buffers_size 256k;
            fastcgi_temp_file_write_size 256k;
        }

    }



Remember to change ‘nginxtips.com’ and the path of your domain to match your real domain details.

If your friendly URLs don’t work, try this alternative configuration. You can also find more help regarding the initial ModX setup in the official documentation.

As you see, ModX configuration for Nginx is pretty simple. It does not require any special configurations, besides the ones explained in this tutorial.

# Try It Yourself

If you want to give it a try, go to the [ModX website](http://modx.com/).

If you have questions or doubts about this great CMS, ask their community. They will be glad to help you with any troubles and questions you may have.

Nginx will give your ModX installation a great boost in terms of web performance. However, you can unlock more speed using a CDN service like the one MaxCDN offers.

Check out this cool tutorial to find out how to implement CDN on ModX.
Share Your Thoughts

Have you used ModX before? What do you think about the performance and features, when compared to other popular CMS platforms, like WordPress or Joomla?

Please share your thoughts in the comments below.

