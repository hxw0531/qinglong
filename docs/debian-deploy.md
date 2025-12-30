# 青龙面板 Debian 部署指南

## 1. 安装系统依赖

```bash
# 更新包列表
apt-get update

# 安装基础依赖
apt-get install -y \
  bash \
  coreutils \
  git \
  curl \
  wget \
  tzdata \
  perl \
  openssl \
  jq \
  openssh-client \
  procps \
  netcat-openbsd \
  unzip \
  python3 \
  python3-pip
```

## 2. 安装 Node.js 20

```bash
# 安装 NodeSource 仓库
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs
```

## 3. 安装 Node.js 全局包

```bash
npm i -g pnpm@8.3.1 pm2 ts-node
```

## 4. 安装 Python 依赖

```bash
pip3 install requests
```

升级pip
pip3 install --upgrade pip
或者使用阿里镜像
pip3 install --upgrade pip -i https://mirrors.aliyun.com/pypi/simple/

## 5. 配置 Git

```bash
git config --global user.email "qinglong@users.noreply.github.com"
git config --global user.name "qinglong"
git config --global http.postBuffer 524288000
```

## 6. 设置时区

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo "Asia/Shanghai" > /etc/timezone
```

## 7. 部署青龙

```bash
# 创建目录
mkdir -p /opt/qinglong
cd /opt/qinglong

# 解压编译产物 (从 GitHub Actions 下载的 qinglong-*.tar.gz)
tar -xzvf qinglong-*.tar.gz

# 创建必要目录
mkdir -p .tmp data/log data/config data/scripts data/repo data/deps

# 设置权限
chmod +x shell/*.sh
chmod 777 .tmp

# 创建命令软链接
ln -sf /opt/qinglong/shell/task.sh /usr/local/bin/task
ln -sf /opt/qinglong/shell/update.sh /usr/local/bin/ql
```

## 8. 配置环境变量

```bash
cat >> /etc/environment << 'EOF'
QL_DIR=/opt/qinglong
PYTHONUNBUFFERED=1
EOF

# 加载环境变量
source /etc/environment
export QL_DIR=/opt/qinglong
```

## 9. 启动服务

```bash
cd /opt/qinglong

# 使用 PM2 启动 (推荐)
pm2 start static/build/app.js --name qinglong
pm2 save
pm2 startup

# 或直接启动 (前台运行)
# node static/build/app.js
```

## 10. 访问面板

浏览器访问: `http://服务器IP:5700`

## 常用命令

```bash
# 查看日志
pm2 logs qinglong

# 重启服务
pm2 restart qinglong

# 停止服务
pm2 stop qinglong

# 查看状态
pm2 status
```

## 国内镜像配置 (可选)

```bash
# npm 镜像
npm config set registry https://registry.npmmirror.com

# pip 镜像
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
