## Fedora25

#### hostname
- <http://www.linuxidc.com/Linux/2015-11/125200.htm>
```
[zlikun@zlikun ~]$ hostnamectl status
   Static hostname: zlikun.com
         Icon name: computer-laptop
           Chassis: laptop
        Machine ID: 39e7c30a1b1f4e9f8ad61abd450b3e60
           Boot ID: 8e6705751f0b46c29be346b0539b5987
  Operating System: Fedora 25 (Twenty Five)
       CPE OS Name: cpe:/o:fedoraproject:fedora:25
            Kernel: Linux 4.9.13-201.fc25.x86_64
      Architecture: x86-64

[zlikun@zlikun ~]$ hostnamectl set-hostname os.zlikun.com
[zlikun@zlikun ~]$ hostnamectl status
   Static hostname: os.zlikun.com
         Icon name: computer-laptop
           Chassis: laptop
        Machine ID: 39e7c30a1b1f4e9f8ad61abd450b3e60
           Boot ID: 8e6705751f0b46c29be346b0539b5987
  Operating System: Fedora 25 (Twenty Five)
       CPE OS Name: cpe:/o:fedoraproject:fedora:25
            Kernel: Linux 4.9.13-201.fc25.x86_64
      Architecture: x86-64
```

#### dnf source
- <https://repo.fdzh.org/>
- <https://github.com/FZUG/repo/wiki/%E6%B7%BB%E5%8A%A0-FZUG-%E6%BA%90>
```
# 添加`FZUG`源
$ dnf config-manager --add-repo=http://repo.fdzh.org/FZUG/FZUG.repo
```

#### dnf commands
- <http://www.linuxidc.com/Linux/2015-06/118751.htm>
```
$ dnf --version
$ dnf install [package-name]
$ dnf repolist
$ dnf repolist all
$ dnf list
$ dnf list installed
$ dnf list avaliable
$ dnf search [package-name]
$ dnf provides [package-name]
$ dnf info [package-name]
$ ...
```

#### 安装`Sogou Pinyin`
- <https://github.com/FZUG/repo/wiki/Sogou-Pinyin-%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98>
```
$ sudo dnf install sogoupinyin
```

#### 安装`Chrome`
- <https://github.com/FZUG/repo/wiki/%E5%AE%89%E8%A3%85-Chrome>
```
# Fedora/RHEL
$ wget https://repo.fdzh.org/chrome/google-chrome-mirrors.repo -P /etc/yum.repos.d/
$ dnf update
$ dnf install google-chrome-stable
```

#### 安装`Oracle JDK`
- <https://github.com/FZUG/repo/wiki/%E5%AE%89%E8%A3%85-Oracle-JDK>
```
$ sudo dnf install oracle-jdk8 oracle-jdk7
```

#### 安装`Atom`
- <https://github.com/FZUG/repo/wiki/%E5%AE%89%E8%A3%85-Atom>
```

```

#### 安装`Sublime Text3`
```
# 安装

# package controller [Ctrl + `]
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```