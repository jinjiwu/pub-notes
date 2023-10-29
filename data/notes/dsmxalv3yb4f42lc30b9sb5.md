
# Install




# SoftWare

## pacman 

**[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/archlinuxcn/)**

在/etc/pacman.conf文件末尾添加以下两行：
```
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```
之后安装 archlinuxcn-keyring 包导入 GPG key。

修改/etc/pacman.conf，增加32位软件源
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
执行 ```sudo pacman -Syy ```

软件管理
```bash
# 更新系统
sudo pacman -Syy && sudo pacman -Su
# 安装软件
sudo pacman -S <pkg name>
# 卸载软件
sudo pacman -R <pkg-name>
# 查询文件属于哪个包管理器
sudo pacman -Qo /path/to/file_name
# 
```

阻止软件升级
```
# /etc/pacman.conf,修改以下行
[options]
IgnorePkg = <pkg name>
```

## yay 



## kde

[Arch kde install](https://wiki.archlinux.org/title/KDE)

kde-plasma,plasma-desktop

系统监视器 system info

截图 spectacle

文件夹 Dolphin

网络管理工具 plasma-nm

## fcitx5

安装[fcitx5](https://wiki.archlinux.org/title/Fcitx5)
```bash
sudo pacman -S fcitx5 fcitx5-im fcitx5-qt fcitx5-gtk fcitx5-chinese-addons
```

编辑环境变量/etc/environment
```
INPUT_METHOD=fcitx5
GTK_IM_MOUDLE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS=@im=fcitx5
```

## ssh

ssh连接3090失败，No route to host

1. 重启NetworkManager
```bash
# sudo systemctl restart NetworkManager

ping -I <ip or interface name> <dest>
# 查看路由表,添加路由，编辑/etc/profile
route -n
sudo route add -net 10.184.0.0 netmask 255.255.0.0 gw 10.181.0.1
``` 

## nvidia

[cudatookits](https://developer.nvidia.com/cuda-downloads)

## python
### anaconda

安装地址 [Aur](https://aur.archlinux.org/packages/anaconda)


#### conda换[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

TUNA 还提供了 Anaconda 仓库与第三方源（conda-forge、msys2、pytorch等，查看完整列表，更多第三方源可以前往校园网联合镜像站查看）的镜像，各系统都可以通过修改用户目录下的 .condarc 文件来使用 TUNA 镜像源。Windows 用户无法直接创建名为 .condarc 的文件，可先执行 conda config --set show_channel_urls yes 生成该文件之后再修改。

注：由于更新过快难以同步，我们不同步pytorch-nightly, pytorch-nightly-cpu, ignite-nightly这三个包。

```yaml
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```
运行 conda clean -i 清除索引缓存，保证用的是镜像站提供的索引。

运行 conda create -n myenv numpy 测试一下吧。

#### pip换[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)

临时使用
```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

设为默认
```bash
python -m pip install --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
# 或者如果您到 pip 默认源的网络连接较差，临时使用本镜像站来升级 pip：
python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip
```
配置多个镜像源。如果您想配置多个镜像源平衡负载，可在已经替换 index-url 的情况下通过以下方式继续增加源站：
```bash
# 请自行替换引号内的内容，源地址之间需要有空格
pip config set global.extra-index-url "<url1> <url2>..."
```
可用的 pypi 源列表（校园网联合镜像站): (https://mirrors.cernet.edu.cn/list/pypi)

## cpp
### clang 

### cmake 

### g++

### cmake 


## htop

监视进程命令行工具

## fish

fish, the friendly interactive shell, is a commandline shell intended to be interactive and user-friendly.

[docs](https://fishshell.com/docs/current/index.html)

### 环境变量设置
第一种：修改~/.config/fish/config.fish
```bash
set -x PATH /opt/cuda/bin $PATH
```

第二种
```bash 
fish_add_path <path>
```

第三种：修改~/.config/fish/fish_variables

### 命令提示符添加git支持
```bash
function fish_prompt --description 'Write out the prompt'
    set -l last_pipestatus $pipestatus
    set -g __fish_git_prompt_showupstream verbose
    set -g __fish_git_prompt_showcolorhints
    set -g __fish_git_prompt_show_informative_status
    set -g fish_prompt_hg_show_informative_status
    set -lx __fish_last_status $status # Export for __fish_print_pipestatus.
    set -l normal (set_color normal)
    set -q fish_color_status
    or set -g fish_color_status --background=red white

    # Color the prompt differently when we're root
    set -l color_cwd $fish_color_cwd
    set -l suffix '>'
    if functions -q fish_is_root_user; and fish_is_root_user
        if set -q fish_color_cwd_root
            set color_cwd $fish_color_cwd_root
        end
        set suffix '#'
    end

    # Write pipestatus
    # If the status was carried over (if no command is issued or if `set` leaves the status untouched), don't bold it.
    set -l bold_flag --bold
    set -q __fish_prompt_status_generation; or set -g __fish_prompt_status_generation $status_generation
    if test $__fish_prompt_status_generation = $status_generation
        set bold_flag
    end
    set __fish_prompt_status_generation $status_generation
    set -l status_color (set_color $fish_color_status)
    set -l statusb_color (set_color $bold_flag $fish_color_status)
    set -l prompt_status (__fish_print_pipestatus "[" "]" "|" "$status_color" "$statusb_color" $last_pipestatus)

    echo -n -s (prompt_login)' ' (set_color $color_cwd) (prompt_pwd) $normal (fish_vcs_prompt) $normal " "$prompt_status $suffix " "
end
```

## v2raya 

版本选择<=4

阻止pacman更新v2ray-core

## Tencent

使用wine安装微信

```bash
# locale -a should have zh_CN.utf8
locale -a
# 修改/etc/locale.gen，去掉zh_CN.UTF-8 UTF-8前面的注释
sudo vim /etc/locale.gen
# 生成locale
sudo locale-gen

# 安装wine-for-wechat from archlinuxcn
sudo pacman -S wine-for-wechat
# 下载wechatsetup.exe
wine64 WechatSetup.exe
# 安装winetricks
sudo pacman -S winetricks
# 安装微信所需的依赖，处理没有输入框的情况
winetricks riched20

# 将msyh字体复制到/usr/share/fonts/msyh文件夹下，修复乱码
mkfontscale
mkfontdir
fc-cache -fv

# 微信窗口关闭在打开就不能输入中文了

```

## Microsoft

[onedriver github](https://github.com/jstaf/onedriver)

openai block bwg server
  
```bash 
# 安装cloudflare warp， https://pkg.cloudflareclient.com/install
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list

sudo apt-get update && sudo apt-get install cloudflare-warp

# set warp
sudo warp-cli register
sudo warp-cli set-mode proxy
sudo warp-cli connect
curl ifconfig.me --proxy socks5://127.0.0.1:40000
curl caht.openai.com --proxy socks5://127.0.0.1:40000

```

修改服务器trojan入站出战规则（/etc/v2ray/config.json）
```json
 "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  },{
    "tag": "chatGPT_proxy",
    "protocol": "socks",
    "settings": {
      "servers": [
        {
          "address": "127.0.0.1",
          "port": 40000
        }
      ]
    }
  }],
  "routing":{
    "rules":[
      {
        "type": "field",
        "outboundTag": "chatGPT_proxy",
        "domain": [
          "chat.openai.com",
          "ip138.com",
          "domain:openai.com",
          "domain:ai.com"
        ]
      }
    ]
  }
```

## wps

## zotero

**plugin**

- ZotFile <https://github.com/jlegewie/zotfile.git>
- Zotero Tag <https://github.com/windingwind/zotero-tag.git>
- Zutilo Utility for Zotero <https://github.com/wshanks/Zutilo.git>
- Zotero Scholar Citations <https://github.com/nico-zck/zotero-scholar-citations.git>
- Better Notes for Zotero <https://github.com/windingwind/zotero-better-notes.git>
- Zotero GPT <https://github.com/MuiseDestiny/zotero-gpt.git>
- Zotero Style <https://github.com/MuiseDestiny/zotero-style.git>
- Zotero Refrence <https://github.com/MuiseDestiny/zotero-reference.git>

## meshlab

## zeno

# Code

## word abbreviation

rules 
1. The first few or some letters of a word, such as addr (address) and asm (assembly);
1. Word syllable initials splicing, such as msg (message);
1. Multiple word initials splicing, such as NASA (National Aeronautics and Space Administration);
1. Remove the vowels aeiou and keep the consonants, such ad JPN (Japan), CHN (China) and ft (foot);
1. A common abbreviation, such as thx(thanks).

