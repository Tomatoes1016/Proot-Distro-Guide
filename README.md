## Termux
1. 获取存储权限
```
termux-setup-storage
```
2. 更换镜像
```
termux-change-repo
# 或更换为清华源
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-main stable main@' $PREFIX/etc/apt/sources.list
```
3. 更新
```
apt update && apt upgrade
```
4. 设置ssh
```
apt install openssh
passwd
sshd
```
## 安装 PRoot-Distro
### 在线
```
apt install proot-distro
pd install debian
```
### 离线
1. 下载rootfs文件
2. 创建缓存目录
```
mkdir -p $PREFIX/var/lib/proot-distro/dlcache
```
3. 将rootfs文件放入缓存目录
```
mv rootfs.tar.xz $PREFIX/var/lib/proot-distro/dlcache
```
4. 安装
```
pd install debian
``` 
## Proot-Distro
### 启动命令
```
nano ~/.bashrc
```
```
alias deb="proot-distro login debian --user user"
```
```
source ~/.bashrc
```
### 配置镜像
```
nano /etc/apt/sources.list.d/debian.sources
```
```
Type: deb
URIs: http://mirrors.tuna.tsinghua.edu.cn/debian/
Suite: trixie trixie-updates trixie-backports
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Type: deb
URIs: http://mirrors.tuna.tsinghua.edu.cn/debian-security/
Suite: trixie-security
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```
### 更新
```
apt update && apt upgrade
```
- 解决无法使用https源的问题
```
apt install apt-transport-https ca-certificates
```
### 安装软件
```
apt install sudo locales fonts-noto tigervnc-standalone-server dbus-x11 x11-xserver-utils xfce4 xfce4-goodies ibus ibus-rime rime-data-double-pinyin git
```
### 配置sudo
```
adduser user
usermod -aG sudo user
echo "user ALL=(ALL:ALL) ALL" > /etc/sudoers.d/user
chmod 0440 /etc/sudoers.d/user
```
### 配置vncserver
```
su - user
nano ~/.config/tigervnc/xstartup
```
```
#!/bin/sh
export XKL_XMODMAP_DISABLE=1

unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

dbus-launch --exit-with-session
startxfce4
```
```
chmod +x ~/.config/tigervnc/xstartup
vncserver :1 -geometry 1920x1080 -depth 24
```
- 解决端口占用问题
```
nano ~/.bashrc
```
```
if [ -f /tmp/.X1-lock ] || [ -S /tmp/.X11-unix/X1 ]; then
    rm -f /tmp/.X1-lock /tmp/.X11-unix/X1
fi
```
### 语言，时区
- 语言
```
nano /etc/locale.gen
```
```
取消注释zh_CN.UTF-8 UTF-8
```
```
locale-gen
```
```
nano /etc/profile.d/locale.sh
```
```
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```
```
chmod +x /etc/profile.d/locale.sh
```
- 时区
```
dpkg-reconfigure tzdata
```
## 软件
### Vistual Studio Code
```
code --no-sandbox
```
- 桌面快捷方式
```
nano ~/Desktop/code.desktop
```
```
[Desktop Entry]
Name=Visual Studio Code
Comment=Code Editing. Redefined.
GenericName=Text Editor
Exec=/usr/share/code/code %F --no-sandbox
Icon=vscode
Type=Application
StartupNotify=false
StartupWMClass=Code
Categories=TextEditor;Development;IDE;
MimeType=application/x-code-workspace;
Actions=new-empty-window;
Keywords=vscode;

[Desktop Action new-empty-window]
Name=New Empty Window
Name[cs]=Nové prázdné okno
Name[de]=Neues leeres Fenster
Name[es]=Nueva ventana vacía
Name[fr]=Nouvelle fenêtre vide
Name[it]=Nuova finestra vuota
Name[ja]=新しい空のウィンドウ
Name[ko]=새 빈 창
Name[ru]=Новое пустое окно
Name[zh_CN]=新建空窗口
Name[zh_TW]=開新空視窗
Exec=/usr/share/code/code --new-window %F --no-sandbox
Icon=vscode
```
```
chmod +x ~/Desktop/code.desktop
```
### WebStorm
```
nano ~/.bashrc
```
```
export DISPLAY=:1
export XDG_RUNTIME_DIR=/tmp/runtime-user
```
```
source ~/.bashrc
```
- 桌面快捷方式
```
nano ~/Desktop/webstorm.desktop
```
```
[Desktop Entry]
Version=1.0
Type=Application
Name=WebStorm
Icon=/home/user/Programs/WebStorm-262.8665.341/bin/webstorm.svg
Exec="/home/user/Programs/WebStorm-262.8665.341/bin/webstorm" %f
Comment=The smartest JavaScript IDE
Categories=Development;IDE;
Terminal=false
StartupWMClass=jetbrains-webstorm
StartupNotify=true
```
```
chmod +x ~/Desktop/webstorm.desktop
```
### IBus
```
nano ~/.bashrc
```
```
export GTK_IM_MODULE=ibus
export QT_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
```
```
source ~/.bashrc
ibus-daemon -drx
ibus-setup
```
```
设置开机自启
```
```
nano ~/.config/ibus/rime/default.custom.yaml
```
```
patch:
  schema_list:
    - schema: double_pinyin_mspy
    - schema: luna_pinyin
```
```
rm -rf ~/.config/ibus/rime/build
ibus restart
```