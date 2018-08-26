# First Step Yocto Project


## 目標
minimalをbuildする


## はじめる
### 環境
* macOS 10.13.1
* docker ubuntu
* Git 1.8.3.1 or greater
* tar 1.27 or greater
* Python 3.4.0 or greater

## docker立ち上げる
` $ docker run ubuntu`

## docker上でyocto環境を整える
以下のドキュメントに沿ってyocto projectの環境をととのえる
https://www.yoctoproject.org/docs/2.5.1/brief-yoctoprojectqs/brief-yoctoprojectqs.html

#### 必須パッケージをインストール
```
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
     build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
     xz-utils debianutils iputils-ping
$ wget http://downloads.yoctoproject.org/releases/yocto/yocto-2.5.1/buildtools/x86_64-buildtools-nativesdk-standalone-2.5.1.sh
$ .x86_64-buildtools-nativesdk-standalone-2.5.1.sh
$ git clone git://git.yoctoproject.org/poky -b sumo
$ . poky/source oe-init-build-env
$ export LANG=en_US.UTF8
$ bitbake -k core-image-minimal
```

## はじめてレシピをかく
### meta-exampleで練習
Adding new recipes to the build system のあたりを参考にする
https://wiki.yoctoproject.org/wiki/Building_your_own_recipes_from_first_principles


### bitbake complains if run as root
root@eb4b9143265d:/work/build-test01# bitbake -k core-image-sato
ERROR:  OE-core's config sanity checker detected a potential misconfiguration.
    Either fix the cause of this error or at your own risk disable the checker (see sanity.conf).
    Following is the list of potential problems / advisories:

    Do not use Bitbake as root.
ERROR: Execution of event handler 'check_sanity_eventhandler' failed
ERROR: Command execution failed: Exited with 1

Summary: There were 3 ERROR messages shown, returning a non-zero exit code.
root@eb4b9143265d:/work/build-test01#
Workaround:

`$ touch conf/sanity.conf`

### runqemu qemux86しようとすると、UTF8のエラー
pythonをつかうとき、en_US.UTF-8にしないといろいろエラーになる。
ja_JP.UTF-8でも動かないことがある。
```
runqemu qemux86
runqemu - INFO - Running MACHINE=qemux86 bitbake -e...
Please use a locale setting which supports UTF-8 (such as LANG=en_US.UTF-8).
Python can't change the filesystem locale after loading so we need a UTF-8 when Python starts or things won't work.
runqemu - ERROR - bitbake -e
Cleanup
```
https://wiki.yoctoproject.org/wiki/TipsAndTricks/ResolvingLocaleIssues

```
sudo locale-gen en_US.UTF-8
apt-get install locales
dpkg-reconfigure locales
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
```
