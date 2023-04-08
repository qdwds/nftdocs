# 部署项目到liunx

# 端口开放
在服务器里面需要对外开放端口，外部才能访问内部。
## 查看端口状态
```
sudo ufw status
```
## 开启端口
```
sudo ufw allow 端口号
```
## 开启防火墙
```
sudo ufw enable
```
## 重启端口
```
sudo ufw reload
```

# 直接部署
## 连接服务器 
```
ssh ubunut@192.168.0.101
```

## 上传代码
```
scp -r /Users/loucky/study/blockchain/nfts/allinnft/nft_app ubuntu@192.168.0.101:/home/ubuntu
```

## 部署项目
部署项目的时候需要想把项目`npm run build`出来，然后执行`npm run start`这时候项目就会启动，只要服务器对外开放了指定端口就能直接访问。

## pm2部署
使用pm2部署后会在后运行。关机命令行也不会影响项目运行。
## 部署
```
pm2 start npm --watch --name nft -- run start
```
## 安装pm2
```javascript
$ npm install -g pm2 命令行全局安装pm2

$ pm2 logs 显示所有进程日志
$ pm2 stop all 停止所有进程
$ pm2 restart all 重启所有进程
$ pm2 reload all 0秒停机重载进程 (用于 NETWORKED 进程)
$ pm2 stop 0 停止指定的进程
$ pm2 restart 0 重启指定的进程
$ pm2 startup 产生 init 脚本 保持进程活着
$ pm2 web 运行健壮的 computer API endpoint
$ pm2 delete 0 杀死指定的进程
$ pm2 delete all 杀死全部进程 



用法
$ npm install pm2 -g     # 命令行安装 pm2 
$ pm2 start app.js -i 4 #后台运行pm2，启动4个app.js 
                                # 也可以把'max' 参数传递给 start
                                # 正确的进程数目依赖于Cpu的核心数目
$ pm2 start app.js --name my-api # 命名进程
$ pm2 list               # 显示所有进程状态
$ pm2 monit              # 监视所有进程
$ pm2 logs               #  显示所有进程日志
$ pm2 stop all           # 停止所有进程
$ pm2 restart all        # 重启所有进程
$ pm2 reload all         # 0秒停机重载进程 (用于 NETWORKED 进程)
$ pm2 stop 0             # 停止指定的进程
$ pm2 restart 0          # 重启指定的进程
$ pm2 startup            # 产生 init 脚本 保持进程活着
$ pm2 web                # 运行健壮的 computer API endpoint (http://localhost:9615)
$ pm2 delete 0           # 杀死指定的进程
$ pm2 delete all         # 杀死全部进程

运行进程的不同方式：
$ pm2 start app.js -i max  # 根据有效CPU数目启动最大进程数目
$ pm2 start app.js -i 3      # 启动3个进程
$ pm2 start app.js -x        #用fork模式启动 app.js 而不是使用 cluster
$ pm2 start app.js -x -- -a 23   # 用fork模式启动 app.js 并且传递参数 (-a 23)
$ pm2 start app.js --name serverone  # 启动一个进程并把它命名为 serverone
$ pm2 stop serverone       # 停止 serverone 进程
$ pm2 start app.json        # 启动进程, 在 app.json里设置选项
$ pm2 start app.js -i max -- -a 23                   #在--之后给 app.js 传递参数
$ pm2 start app.js -i max -e err.log -o out.log  # 启动 并 生成一个配置文件
你也可以执行用其他语言编写的app  ( fork 模式):
$ pm2 start my-bash-script.sh    -x --interpreter bash
$ pm2 start my-python-script.py -x --interpreter python

0秒停机重载:
这项功能允许你重新载入代码而不用失去请求连接。
注意：
仅能用于web应用
运行于Node 0.11.x版本
运行于 cluster 模式（默认模式）
$ pm2 reload all

CoffeeScript:
$ pm2 start my_app.coffee  #这就是全部

PM2准备好为产品级服务了吗？
只需在你的服务器上测试
$ git clone https://github.com/Unitech/pm2.git
$ cd pm2
$ npm install  # 或者 npm install --dev ，如果devDependencies 没有安装
$ npm test



pm2 list
列出由pm2管理的所有进程信息，还会显示一个进程会被启动多少次，因为没处理的异常。


pm2 monit

监视每个node进程的CPU和内存的使用情况。

```