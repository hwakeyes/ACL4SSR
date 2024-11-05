# 折腾cloudflare [【全网首发】永不被盗的订阅转换方法！使用worker搭建永久免费的私人反代订阅转换服务，新手小白必备，建议人手一个](https://www.youtube.com/watch?app=desktop&v=X7CC5jrgazo)
  - 结果：可以成功转换，但是无法跟openclash的在线订阅转换对接，一直下载失败
  - 地址：https://subconvertion.eagleqilian.workers.dev/sub?target=clash&url=https%3A%2F%2Fs.trojanflare.com%2Fclashx%2Fdc4751fe-0af3-49e5-a3c9-d4897661f7e6&insert=false&emoji=true&list=false&tfo=false&scv=true&fdn=false&sort=false&new_name=true

# 折腾自建订阅转换服务器subconverter: [全网唯一】定制OpenClash在线分流规则模板、在线订阅转换模板 自定义规则、策略组、分流策略、配置文件、clash规则、搭建订阅转换后端 一并解决很多问题，BT下载、广告拦截、家庭设备指定节点](https://www.youtube.com/watch?v=D841V_xgykg)
  - 分流规则和规则集自己制作了一份，就是当前这个fork过来的仓库
  - 在软路由上安装转换服务 https://github.com/tindy2013/subconverter/blob/master/README-cn.md ， 直接安装其二进制文件并配置自启动即可：
    ```
      # 下载release
      wget https://github.com/tindy2013/subconverter/releases/latest/download/subconverter_linux64.tar.gz
      # 解压并授权
      tar -xvzf subconverter_linux64.tar.gz subconverter && chmod +x subconverter/subconverter
      # 运行即可
      subconverter/subconverter
    ```
    - 配置启动：
      - 创建 /etc/init.d/subconverter
        ```
        #!/bin/sh /etc/rc.common
  
        START=80
        STOP=15
        
        CONFIG=subconverter
        APP_FILE=/root/${CONFIG}/subconverter
        LOCK_FILE_DIR=/var/lock
        LOCK_FILE=${LOCK_FILE_DIR}/${CONFIG}.lock
        
        set_lock() {
                [ ! -d "$LOCK_FILE_DIR" ] && mkdir -p $LOCK_FILE_DIR
                exec 999>"$LOCK_FILE"
                flock -xn 999
        }
        
        unset_lock() {
                flock -u 999
                rm -rf "$LOCK_FILE"
        }
        
        unlock() {
                failcount=1
                while [ "$failcount" -le 10 ]; do
                        if [ -f "$LOCK_FILE" ]; then
                                let "failcount++"
                                sleep 1s
                                [ "$failcount" -ge 10 ] && unset_lock
                        else
                                break
                        fi
                done
        }
        
        boot() {
                local delay=$(uci -q get ${CONFIG}.@global_delay[0].start_delay || echo 1)
                if [ "$delay" -gt 0 ]; then
                        $APP_FILE echolog "执行启动延时 $delay 秒后再启动!"
                        sleep $delay
                fi
                restart
                touch ${LOCK_FILE_DIR}/${CONFIG}_ready.lock
        }
        
        start() {
                set_lock
                [ $? == 1 ] && $APP_FILE echolog "脚本已经在运行，不重复运行，退出." && exit 0
                $APP_FILE start &
                unset_lock
        }
        
        stop() {
                unlock
                set_lock
                [ $? == 1 ] && $APP_FILE echolog "停止脚本等待超时，不重复运行，退出." && exit 0
                $APP_FILE stop
                unset_lock
        }
        
        restart() {
                set_lock
                [ $? == 1 ] && $APP_FILE echolog "脚本已经在运行，不重复运行，退出." && exit 0
                $APP_FILE stop
                $APP_FILE start
                unset_lock
        }
        ```
      - chmod +x /etc/init.d/subconverter
      - /etc/init.d/subconverter start
      - /etc/init.d/subconverter enabled (或者enable, 都试试)
    - 尝试修改配置文件，配置完/etc/init.d/subconverter reload或者restart即可。配置文件修改参考：https://mybj.org/2021/09/subconverter/index.html ， https://github.com/tindy2013/subconverter/blob/master/README-cn.md#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6
  - 结果：软路由的subconverter服务时好时坏，不知道是配置原因还是网络原因（不知道其是否需要从网络上抓取我在openclash配置的在线github 订阅转换模版而耗时）。而且有一次整个配置链条生效了之后，得到的最终代理配置始终是软路由上subconverter服务的离线模版配置结果，在openclash中配置的
    在线模版没有被应用上去，无论怎么改subconverter的配置始终不生效。最终把在线订阅转换关了，甚至把当前在配置的订阅删除仍然还是之前subconverter给我的配置。最后把所有订阅和离线配置文件删除了，上传一个从我电脑上导出的有效配置文件获得网络之后，重新配置了一个干净的订阅，最终显示的代理配置才
    恢复原样。
  - 浏览器访问地址：http://192.168.5.1:25500/sub?target=clash&url=https://s.trojanflare.com/clashx/dc4751fe-0af3-49e5-a3c9-d4897661f7e6
  - 注： openclash的配置中只需要这样配置地址 http://127.0.0.1:25500/sub
