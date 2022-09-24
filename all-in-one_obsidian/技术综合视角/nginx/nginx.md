# SPA部署配置
## 标准SPA部署配置
这个配置可用于线上配置，每一个项目都有独立的配置规则，

```
    location /projectName/ {
      root path/to/projectName; # 这个规则可以根据实际情况变
      index index.html;
      try_files $uri $uri/ /projectName/index.html; # 这个主要是兼容history模式的路由，没有实际路径就走index.html 前端自行路由
    }

```

## 共享环境的多项目通用配置
这个配置可以适配公用同一个环境的多个项目，自动适配兼容不需要单独配置
```
    location ~* ^/([^/]*)\/? {
      root path/to/projects/root; # 这个规则可以根据实际情况变
      index index.html;
      try_files $uri $uri/ /$1/index.html; # 这个主要是兼容history模式的路由，没有实际路径就走index.html 前端自行路由
    }

```

## 多环境多项目通用配置
有时候我们只有一台机子，或者说只需要一台机子，就可以模拟出多套环境的应用场景，这样就需要一个区分的规则，在 nginx这里可以用目录做简单的区分即可实现
```
    location ~* ^/(envA|envB|envC)/([^/]*)\/? {
      root path/to/envs/root; # 这个规则可以根据实际情况变
      index index.html;
      try_files $uri $uri/ /$1/$2/index.html; # 这个主要是兼容history模式的路由，没有实际路径就走index.html 前端自行路由
    }

```


## 多业务线多环境多项目通用配置
基本扩展规则是类似的，在外层不断地通过添加目录层级来进行静态资源的区分
```
    location ~* ^/(businessA|businessB)/(envA|envB|envC)/([^/]*)\/? {
      root path/to/businesses/root; # 这个规则可以根据实际情况变
      index index.html;
      try_files $uri $uri/ /$1/$2/$3/index.html; # 这个主要是兼容history模式的路由，没有实际路径就走index.html 前端自行路由
    }

```