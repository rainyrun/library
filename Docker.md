# Docker

基本命令

```sh
# 启动docker
systemctl docker start
# 开机自动启动
systemctl docker enable
# 修改守护进程监听端口 或套接字
docker daemon -H tcp://0.0.0.0:2375 [...] # 可绑定多个
docker daemon -H unix://home/docker/docker.sock
# 环境变量 明确docker监听的端口
export DOCKER_HOST="tcp://0.0.0.0:2375"
# 守护进程的调试模式
docker daemon -D
```