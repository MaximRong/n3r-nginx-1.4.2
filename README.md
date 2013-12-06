n3r-nginx-1.4.2
===============

此工程是火箭队为了升级现场的nginx版本创建的，会写上相信的nginx-1.4.2安装步骤。

全局nginx配置:
```nginx
--prefix=/home/maxim/App/nginx --add-module=/home/maxim/App/ngx_devel_kit --add-module=/home/maxim/App/lua-nginx-module --add-module=/home/maxim/App/nginx-3rd-module/echo-nginx-module --add-module=/home/maxim/App/nginx-3rd-module/nginx-http-concat --with-http_stub_status_module --add-module=/home/maxim/App/nginx-3rd-module/set-misc-nginx-module
```

准备阶段
----------
安装git命令 (for linux系统)

增加lua支撑
-----------
1. 下载ngx_devel_kit 地址[https://github.com/simpl/ngx_devel_kit](https://github.com/simpl/ngx_devel_kit) 或者使用git命令 git clone https://github.com/simpl/ngx_devel_kit.git

2. 安装LuaJIT 地址[http://luajit.org/download.html](http://luajit.org/download.html)
安装直接make -perfix=path && make install

3. 下载lua-nginx-module 地址[https://github.com/chaoslawful/lua-nginx-module](https://github.com/chaoslawful/lua-nginx-module) 或者使用git命令 git clone https://github.com/chaoslawful/lua-nginx-module

4. 增加两个环境变量
```shell
export LUAJIT_LIB=/path/to/luajit/lib
export LUAJIT_INC=/path/to/luajit/include/luajit-2.0
```

5. 增加lua-nginx对应模块，修改nginx的configura命令：
```nginx
./configure --prefix=/opt/nginx \
        --add-module=/path/to/ngx_devel_kit \
        --add-module=/path/to/lua-nginx-module
```
make && make install

6. 启动nginx 如果报如下错误：
```shell
./nginx: error while loading shared libraries: libluajit-5.1.so.2: cannot open shared object file: No such file or directory
```
解决办法:
```shell
shell> echo "/path/to/luajit/lib" > /etc/ld.so.conf.d/usr_local_lib.conf
shell> ldconfig
```

7. 再次启动，OK!

8. 验证，修改nginx.conf
```nginx
location /test_lua {
              default_type text/html;
              content_by_lua '
                    ngx.say("hello!");
              ';
          }
```
访问http://ip:port/test_lua 出现hello!则成功！

参考文献:
[https://github.com/chaoslawful/lua-nginx-module#installation](https://github.com/chaoslawful/lua-nginx-module#installation)
[http://huoding.com/2012/08/31/156](http://huoding.com/2012/08/31/156)


安装echo模块(此模块用于调试)
-----------------------------
echo用于显示简单的文字，用于调试。

1. 下载源模块包[https://github.com/agentzh/echo-nginx-module](https://github.com/agentzh/echo-nginx-module) 或者使用git命令 git clone https://github.com/agentzh/echo-nginx-module.git

2. 增加对应模块
```nginx
./configura --add-module=path/to/echo-nginx-module
```

3. 重启服务

参考文献：
[https://github.com/agentzh/echo-nginx-module](https://github.com/agentzh/echo-nginx-module)
[http://wiki.nginx.org/HttpEchoModule](http://wiki.nginx.org/HttpEchoModule)


geo模块
--------
geo模块为自带模块，无需安装，在源码中包含sina对应的ip映射文件(geo_sina.conf)。
geo模块用于映射ip地址。
geo模块配置示例:
```nginx
geo $mallcity {
        ranges;
        default 11|110;
        include geo_sina.conf;
      }

```

参考文献:
[http://nginx.org/en/docs/http/ngx_http_geo_module.html](http://nginx.org/en/docs/http/ngx_http_geo_module.html)<br/>
中文翻译:
[http://nginx.org/cn/docs/http/ngx_http_geo_module.html](http://nginx.org/cn/docs/http/ngx_http_geo_module.html)


map 模块
----------
映射模块，无需安装。源码中包含了school.conf对应页面。
map模块配置示例:
```nginx
map $provCode $xy_url {
        include school.conf;
    }

 server {
     listen       8080;
     server_name  localhost;

     set $mallcity $mallcity;

     if ($mallcity ~ ^(\d+)\|\d+$){
             set $provCode $1;
     }

     location /test_lua {
         default_type text/html;
         echo $xy_url;
	}
}
```
参考文献:
[http://nginx.org/en/docs/http/ngx_http_map_module.html](http://nginx.org/en/docs/http/ngx_http_map_module.html)


nginx-http-concat 模块
------------------------
nginx-http-concat 模块是淘宝开发的基于Nginx减少HTTP请求数量的扩展模块,主要是用于合并减少前端用户Request的HTTP请求的数量。
比如你可以这样合并请求
```script
http://example.com/??style1.css,style2.css,foo/style3.css
```

1. 下载模块，网址[https://github.com/alibaba/nginx-http-concat](https://github.com/alibaba/nginx-http-concat) 或者使用git命令 git clone https://github.com/alibaba/nginx-http-concat.git

2. --add-module=/path/to/nginx-http-concat && make -j2 && make install

参考文献：
[https://github.com/alibaba/nginx-http-concat](https://github.com/alibaba/nginx-http-concat)

stub_status_module 模块
------------------------
nginx自带监控状态模块

```shell
--with-http_stub_status_module
```

```nginx
	location /nginx_status {
	  # copied from http://blog.kovyrin.net/2006/04/29/monitoring-nginx-with-rrdtool/
	  stub_status on;
	  access_log   off;
	  allow SOME.IP.ADD.RESS;
	  deny all;
	}
```

参考文献:
[http://wiki.nginx.org/HttpStubStatusModule](http://wiki.nginx.org/HttpStubStatusModule)


set-misc-nginx-module
----------------------

这个模块是由张奕春开发的nginx扩展功能，支持多种nginx set指令 (md5/sha1, sql/json quoting, and many more)

1. 下载set-misc-nginx-module源代码[https://github.com/agentzh/set-misc-nginx-module](https://github.com/agentzh/set-misc-nginx-module) 或者使用git命令 git clone https://github.com/agentzh/set-misc-nginx-module.git

2. 安装--add-module=/path/to/set-misc-nginx-module && make -j2 && make install

参考文献：
[https://github.com/agentzh/set-misc-nginx-module](https://github.com/agentzh/set-misc-nginx-module)




