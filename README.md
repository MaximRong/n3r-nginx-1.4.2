n3r-nginx-1.4.2
===============

此工程是火箭队为了升级现场的nginx版本创建的，会写上相信的nginx-1.4.2安装步骤。

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
[http://nginx.org/en/docs/http/ngx_http_geo_module.html](http://nginx.org/en/docs/http/ngx_http_geo_module.html)
中文翻译:
[http://nginx.org/cn/docs/http/ngx_http_geo_module.html](http://nginx.org/cn/docs/http/ngx_http_geo_module.html)