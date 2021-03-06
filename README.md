# Features
- All features of nginx(latest release) are inherited, i.e., it is 100% compatible with nginx.
- Web development using python, c++, lua, php7, java , javascript and jsr-223 JVM language

# Dependency
- linux
- gcc and g++(c++11) or clang and clang++
- python-devel,if `--enable-http-hi-python=YES` and `with-http-hi-python-version=python2`
- python3.x-devel if `--enable-http-hi-python=YES` and `with-http-hi-python-version=python3`
- lua-devel(lua5.x),if `--enable-http-hi-lua=YES`  and `--with-http-hi-lua-version=lua5.x`
- luajit-devel,if `--enable-http-hi-lua=YES` and `--with-http-hi-lua-version=luajit`
- jdk 1.8,9,10,11,12,if `--enable-http-hi-java=YES`
- PHP 7.0,7.1, 7.2,7.3(--enable-embed=shared),if `--enable-http-hi-php=YES`



# Installation
`./configure --help` or see `install_demo.sh` or 

```shell
                --enable-http-hi-cpp=YES                           \
                --enable-http-hi-python=NO                         \
                --enable-http-hi-lua=NO                            \
                --enable-http-hi-qjs=NO                            \
                --enable-http-hi-java=NO                           \
                --enable-http-hi-php=NO                            \
                --with-http-hi-python-version=python3              \
                --with-http-hi-lua-version=lua5.3                  \
                --add-module=module/ngx_http_hi_module             \
                --add-module=module/ngx_autoblacklist_module               

```


# python, lua api
## hi_req
- uri
- method
- client
- param
- user_agent
- has_header
- get_header
- has_form
- get_form
- has_session
- get_session
- has_cookie
- get_cookie
- has_cache
- get_cache
## hi_res
- status
- content
- header
- session
- cache

# quickjs api
## hi
- uri
- method
- client
- param
- user_agent
- has_header
- get_header
- has_form
- get_form
- has_session
- get_session
- has_cookie
- get_cookie
- has_cache
- get_cache
- status
- content
- header
- session
- cache


# hello,world

## cpp servlet class

```cpp
#include "servlet.hpp"
namespace hi{
class hello : public servlet {
    public:

        void handler(request& req, response& res) {
            res.headers.find("Content-Type")->second = "text/plain;charset=UTF-8";
            res.content = "hello,world";
            res.status = 200;
        }

    };
}

extern "C" hi::servlet* create() {
    return new hi::hello();
}

extern "C" void destroy(hi::servlet* p) {
    delete p;
}

```

### cpp compile

```
g++ -std=c++11 -I/usr/local/nginx/include  -shared -fPIC hello.cpp -o hello.so
install hello.so /usr/local/nginx/cpp

```

## java servlet class

```java
package hi;

public class jhello implements hi.servlet {

    public jhello() {

    }

    public void handler(hi.request req, hi.response res) {
        res.status = 200;
        res.content = "hello,world";

    }
}

```

### java compile

```
${JAVA_HOME}/bin/javac -classpath .:${NGINX_INSTALL_DIR}/java/hi-nginx-java.jar hi/jhello.java

```


## php servlet class

see `php/hi/request.php`,`php/hi/response.php` and `php/hi/servlet.php`

```php

<?php

require_once 'hi/servlet.php';
require_once 'hi/route.php';

class index implements \hi\servlet {

    public function handler(\hi\request $req, \hi\response $res) {
        $app = \hi\route::get_instance();
        $app->add('{^/$}', array('GET'), function ($rq, $rs, &$param) {
            $rs->content = 'hello,world';
            $rs->status = 200;
        });
        
        $app->add('{^/who/(?P<name>\w+)/?$}', array('GET'), function ($rq, $rs, &$param) {
            $rs->content = 'hello,'.$param['name'];
            $rs->status = 200;
        });
        $app->run($req, $res);
    }

}

```

## javascript hello world

```javascript

if (typeof (Mustache) == 'undefined') {
    load('https://cdn.bootcss.com/mustache.js/2.3.0/mustache.min.js')
}


var list = java.util.Arrays.asList(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
var template = "{{#list}}* {{.}}\n{{/list}}"
var key = 'test', output
if (hi_req.cache.containsKey(key)) {
    output = hi_req.cache.get(key)
} else {
    output = Mustache.render(template, {'list': JSON.parse(list.toString())})
    hi_res.cache.put(key, output)
}
hi_res.headers.get('Content-Type').set(0, 'text/plain;charset=UTF-8')
hi_res.content = output
hi_res.status = 200;


```

## quickjs

```javascript
import * as hi from "hi";
hi.header('Content-Type','text/plain;charset=UTF-8')
hi.content('hello,world')
hi.status(200)

```


# 3rd party module

  -  nginx-rtmp-module
  -  nchan

# ngx_http_hi_module directives

- directives : content: loc,if in loc
    - **hi**,default: ""

    example:
    
```nginx

            location = /hello {
                hi cpp/hello.so ;
            }

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_need_tokens**,default: on

    example:

```nginx

        hi_need_tokens on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_cache_method**,default: GET

    example:

```nginx

        hi_cache_method GET|POST|PUT|HEAD;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_need_cache**,default: on

    example:

```nginx

        hi_need_cache on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_cache_size**,default: 10

    example:

```nginx

        hi_cache_size 10;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_cache_expires**,default: 300s

    example:

```nginx

        hi_cache_expires 300s;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_need_kvdb**,default: off

    example:

```nginx

        hi_need_kvdb on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_kvdb_size**,default: 10

    example:

```nginx

        hi_kvdb_size 10;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_kvdb_expires**,default: 300s

    example:

```nginx

        hi_kvdb_expires 300s;

```



- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_need_headers**,default: off

        example:

```nginx

        hi_need_headers on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_need_cookies**,default: off

        example:

```nginx

        hi_need_cookies on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_need_session**,default: off

        example:

```nginx

        hi_need_session on|off;

```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_session_expires**,default: 300s

    example:

```nginx

        hi_session_expires 300s;

```
     

- directives : content: loc,if in loc
    - **hi_python_content**,default: ""

    example:
    
```nginx

            location = /pyecho {
                hi_python_content "hi_res.status(200)\nhi_res.content('hello,world')" ;
            }

```

- directives : content: loc,if in loc
    - **hi_python_script**,default: ""

    example:
    
```nginx

            location ~ \.py$  {
                hi_python_script python;
            }

```

or

```nginx

            location / {
                hi_python_script python/index.py;
            }

```


- directives : content:  http,srv,if in srv
    - **hi_lua_package_path**,default: ""

    example:
    
```nginx


            hi_lua_package_path '/usr/local/nginx/lua/?.lua;';


```

- directives : content:  http,srv,if in srv
    - **hi_lua_package_cpath**,default: ""

    example:
    
```nginx


            hi_lua_package_cpath '/usr/local/nginx/lua/?.so;';


```



- directives : content: loc,if in loc
    - **hi_lua_content**,default: ""

    example:
    
```nginx

            location = /luaecho {
                hi_lua_content "hi_res:status(200)\nhi_res:content('hello,world')" ;
            }

```

- directives : content: loc,if in loc
    - **hi_lua_script**,default: ""

    example:
    
```nginx

            location ~ \.lua$  {
                hi_lua_script lua;
            }

```

or

```nginx

            location / {
                hi_lua_script lua/index.lua;
            }

```

- directives : content: http,srv,if in srv
    - **hi_java_classpath**,default:"-Djava.class.path=."

    example:

```nginx

hi_java_classpath "-Djava.class.path=.:/usr/local/nginx/java:/usr/local/nginx/java/hi-nginx-java.jar";


```

- directives : content: http,srv,if in srv
    - **hi_java_options**,default:"-server -d64 -Xmx1G -Xms1G -Xmn256m"

    example:

```nginx

hi_java_options "-server -d64 -Xmx3G -Xms3G -Xmn768m -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+UseNUMA -XX:+CMSParallelRemarkEnabled -XX:MaxTenuringThreshold=15 -XX:MaxGCPauseMillis=30 -XX:GCPauseIntervalMillis=150 -XX:+UseAdaptiveGCBoundary -XX:-UseGCOverheadLimit -XX:+UseBiasedLocking -XX:SurvivorRatio=8 -XX:TargetSurvivorRatio=90 -XX:MaxTenuringThreshold=15 -Dfml.ignorePatchDiscrepancies=true -Dfml.ignoreInvalidMinecraftCertificates=true -XX:+UseFastAccessorMethods -XX:+UseCompressedOops -XX:+OptimizeStringConcat -XX:+AggressiveOpts -XX:ReservedCodeCacheSize=2048m -XX:+UseCodeCacheFlushing -XX:SoftRefLRUPolicyMSPerMB=10000 -XX:ParallelGCThreads=10";


```

- directives : content: loc,if in loc
    - **hi_java_servlet**,default:""

    example:

```nginx

hi_java_servlet hi/jhello;



```

- directives : content : http,srv,loc,if in loc ,if in srv
    - **hi_java_servlet_cache_expires**,default:300s

    example:

```nginx

hi_java_servlet_cache_expires 300s;


```

- directives : content : http,srv,loc,if in loc ,if in srv
    - **hi_java_servlet_cache_size**,default:10

    example:

```nginx

hi_java_servlet_cache_size 10;


```

- directives : content: http,srv,if in srv
    - hi_java_version,default:8

    example:

```nginx

hi_java_version 8;


```


- directives : content: loc,if in loc
    - **hi_javascript_lang**,default:javascript

    example:

```nginx

hi_javascript_lang javascript;


```


- directives : content: loc,if in loc
    - **hi_javascript_extension**,default:js

    example:

```nginx

hi_javascript_extension js;


```



- directives : content: loc,if in loc
    - **hi_javascript_content**,default:""

    example:

```nginx

hi_javascript_content "hi_res.content='hello,world';hi_res.status=200;";


```

- directives : content: loc,if in loc
    - **hi_javascript_script**,default:""

    example:

```nginx

hi_javascript_script javascript/index.js;


```

or

```nginx


hi_javascript_script javascript;


```

- directives : content: http,srv,loc,if in loc ,if in srv
    - **hi_javascript_compiledscript_expires**,default:300s

    example:

```nginx

hi_javascript_compiledscript_expires 300s;

```

- directives : content: loc,if in loc
    - **hi_php_script**,default: ""

    example:
    
```nginx
            location ~ \.php$  {
                hi_php_script php;
            }
```

or

```nginx
            location / {
                hi_php_script php/index.php;
            }
```

- directives : content: loc,if in loc
    - **hi_qjs_script**,default: ""

    example:
    
```nginx
            location ~ \.js$  {
                hi_qjs_script qjs;
            }
```
or

```nginx
            location / {
                hi_qjs_script qjs/index.js;
            }
```


- directives : content:  http,srv,if in srv
    - **hi_qjs_memory_limit** ,default: 1024m

    example:
    
```nginx


            hi_qjs_memory_limit 1024m;


```


- directives : content:  http,srv,if in srv
    - **hi_qjs_ctx_called_limit**,default: 10240

    example:
    
```nginx


            hi_qjs_ctx_called_limit 10240;


```

- directives : content: loc if in loc
    - **hi_subrequest**, default : ""

    example:

```nginx

        location ^~ /sub {
                hi_subrequest '/query';
                hi_lua_content 'hi_res:header("Content-Type",hi_req:get_form("__subrequest_content_type__"))\nhi_res:status(tonumber(hi_req:get_form("__subrequest_status__")))\nhi_res:content(hi_req:get_form("__subrequest_body__"))';
        }

        location ^~ /query {
                internal;
                proxy_pass http://http://hq.sinajs.cn/;
                proxy_set_header Accept-Encoding '';
        }
#  curl -i http://localhost/sub?list=sh601006

```

# ngx_http_autoblacklist_module directives

- directives : content: http,srv,loc,if in loc ,if in srv
    - **autoblacklist**, default : "off"
    - **autoblacklist_size**, default : 1024
    - **autoblacklist_limit**, default : 30
    - **autoblacklist_expires**, default : 86400s 

    example:

```nginx

	autoblacklist on|off;
	autoblacklist_size 1024;
	autoblacklist_expires 86400s;
	autoblacklist_limit 30;

```

## nginx.conf

[hi_demo_conf](https://github.com/webcpp/hi_demo/blob/master/demo.conf)
