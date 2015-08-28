#Ngnix ModX配置指南

ModX Revolution是一个简单易用，一站式内容管理系统，同时，也是一个应用程序开发框架，可以用他来替换WordPress和Joomla。ModX是基于GPL发布的自由软件，百分之百兼容Nginx和PHP5.1.1及更高版本。

ModX包含了一个所见即所得的富文本编辑器，并且你可以完全控制元数据和友站链接来提高你的内容在搜索引擎中的排名。

从开发者的角度来看，ModX是面向对象的，并且兼容MVC设计模式。他支持诸如ExtJS, jQuery, Prototype和MooTools这样的AJAX库，并且支持MySQL和MSSQL数据库。

现在，你可以了解到如何在你现有的Nginx系统之上配置ModX。

#如何在Nginx上配置ModX？
下面是一个在Nginx上安装ModX的配置文件示例（依赖于php-fpm）：

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


记得把配置文件示例中的‘nginxtips.com’和路径替换成你服务器的域名和路径哦，亲。

如果你的友站链接没有起效，可以试试看这个配置。当然，你可以从官方文档中找到更多帮助信息和ModX的安装配置方法。

正如你所见，ModX在Nginx上的安装配置相当简单，出了本文示例中的配置之外，你不需要任何其他额外的配置。

#动手试试吧

如果你想试试看，访问[ModX的网站](http://modx.com/)吧。

如果你有任何问题，去社区提问吧。很多人会乐意回答你的问题，并且帮你解决问题的。

Ngnix可以为你的ModX提供很好的性能，当然，你仍然可以使用像MaxCDN这样的厂商提供的CDN服务来进一步提高访问速度。

你可以查看本文来发现如何在ModX上实现CDN。

分享你的想法哦

你以前用过ModX么？和其他诸如WordPress和Joomla之类的内容管理系统相比，你觉得ModX的性能和功能怎么样呢？

请在下面留言哦，亲～


//亲你大爷，哈哈哈，这一句就不要了
