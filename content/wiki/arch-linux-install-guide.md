---
title: "Arch linux 설치 가이드"
summary: 'Arch linux를 설치해보자.'
layout: wiki
parent: "Linux"
tags: ["Arch", "Guide"]
toc: true
latex: false
date: 2020-05-30T00:59:56+09:00
lastmod: 2020-05-30T22:55+09:00
---
# 설치하기 전에
이 글에 있는 코드 중 `#`는 root 사용자의 프롬프트, `$`는 일반 사용자 계정의 프롬프트를 나타낸다. 즉 `#` 뒤에 오는 명령어는 root 권한으로 실행시켜야 하고 `$` 뒤에 오는 명령어는 일반 사용자 계정으로 실행시켜도 된다.

진행하다 어려움이 생기면 [ArchWiki](https://wiki.archlinux.org/index.php/Installation_guide)를 참고하라.

# 설치 준비
## 설치 USB 만들기
### Windows
[Rufus](https://rufus.ie/)를 이용한다.

### Linux
USB를 꽂고 아래 명령을 실행해 장치명을 확인한다.
```
$ lsblk
```

이제 usb를 포맷하고 iso파일을 구워준다.


```
# wipefs --all /dev/sdb
# dd if=/path/to/iso/file of=/dev/sdb bs=4M && sync
```

`/dev/sdb`는 위에서 확인한 장치명으로 바꿔주어야한다.

# 설치
이제 USB를 꼽고 재부팅을 한다. 재부팅 중 바이오스/UEFI 진입키를 이용해 USB로 부팅한다.

여기서는 UEFI/GPT로 설치한다고 하자.
```
# ls /sys/firmware/efi
```
결과에 `efivars`가 있으면 UEFI로 설치할 수 있다.

## 인터넷 연결 확인
Arch linux는 설치할 때 패키지를 인터넷에서 가져오기 때문에 인터넷에 연결되어있어야 한다. 다음 명령으로 인터넷 연결을 확인한다.

```
# ping -c 1 www.google.com
```

### Wifi로 연결
만약 이더넷이 아니라 wifi로 연결할 때는 다음 명령으로 `$ wiki-menu`로 wifi를 연결하자.

## 파티션 나누기
`lsblk`로 설치할 디스크 이름을 확인해둔다. 이제 파티션 계획을 세워야 한다. 여기서는 다음과 같이 파티션을 만들기로 한다.

| 파티션 명 | 용도       | 파일 시스템 | 용량   | 코드 |
|:---------:|------------|-------------|--------|------|
| /dev/sda1 | EFI 파티션 | FAT32       | 200MB  | EF00 |
| /dev/sda2 | /          | EXT4        | 40GB   | 8304 |
| /dev/sda3 | /home      | EXT4        | 나머지 | 8302 |

`gdisk`를 이용해 파티션을 나누자.
```
# gdisk /dev/sda
```

```
`o` 입력: 디스크를 모두 지우고 새로 만든다.
`n`: 파티션 만들기 (new)
(default가 1로 되어있는지 확인/아닐 경우 ‘1’을 입력)

첫 섹터는 알아서 계산된다. 따라서 `엔터`
라스트 섹터는 용량으로 입력할 수 있다. `+200mb`입력
파일 시스템 코드는 `EF00`(EFI system partition) 입력

`p`를 입력해 제대로 됐는지 확인할 수 있다.

다른 파티션도 반복해서 만든다.

`w`로 저장
```

## 포맷
이제 포맷을 시작한다.
```
# mkfs.vfat -F32 /dev/sda1
# mkfs.ext4 /dev/sda2
# mkfs.ext4 /dev/sda3
```

## 마운트
```
# mount /dev/sda2 /mnt
# mkdir /mnt/boot /mnt/home
# mount /dev/sda1 /mnt/boot
# mount /dev/sda3 /mnt/home

```

# 패키지 설치
### 미러 수정
패키지를 받아오는 미러를 변경해야 속도가 빠르다. `/etc/pacman.d/mirrorlist` 파일을 수정하자.


[아치 리눅스 미러리스트 페이지](https://www.archlinux.org/mirrorlist/)에서 참고하거나 다음 미러를 사용한다.

```
##
## Arch Linux repository mirrorlist
## Filtered by mirror score from mirror status page
## Generated on 2020-05-21
##

## South Korea
#Server = http://mirror.premi.st/archlinux/$repo/os/$arch
## South Korea
#Server = https://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
## South Korea
#Server = http://ftp.harukasan.org/archlinux/$repo/os/$arch
## South Korea
#Server = https://ftp.harukasan.org/archlinux/$repo/os/$arch
## South Korea
#Server = http://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
```

## 기본 패키지 설치
```
pacstrap /mnt base linux linux-firmware base-devel
```

설치 완료되면 마운트 정보를 만들고 시스템에 진입한다.

```
# genfstab -U /mnt >> /mnt/etc/fstab
# arch-chroot /mnt
```

# 설정
먼저 필요한 패키지들을 설치한다.
```
# pacman -Syu # 패키지 업데이트
# pacman -S vim ntp networkmanager grub efibootmgr git xorg-server xorg-apps
```
* vim: 텍스트 에디터
* ntp: 시간 설정
* networkmanager: 이더넷만 사용할 것이라면 dhcpd도 고려해봐라.
* grub: 부트로더
* efibootmfr: UEFI 부팅시 필요하다.
* git
* xorg-server, xorg-apps: X11 server

## 타임존 설정
한국 시간대로 설정한다.
```
# ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```
> ln: 링크를 만드는 명령어.
> -s: symbolic link
> -f: force; replace if already exists

## 시간동기화
시간을 서버에서 가져와 동기화하자.
```
# ntpdate time.kriss.re.kr  # 실패하면 time2.kriss.re.kr 를 이용.
# hwclock -w				# 하드웨어 시간 동기화
```

## 로케일 설정
### 로케일 활성화
로케일을 시스템에서 사용하려면 활성화해야한다. `/etc/locale.gen` 파일에서 필요한 로케일의 주석을 해제한다. 여기서는 en_US.UTF-8 UTF-8과 ko_KR.UTF-8 UTF-8 사용한다. 그 후 다음 명령어로 갱신한다.

```
# locale-gen
```

### 시스템 로케일 설정
 `/etc/locale.conf` 작성한다.

```
# echo LANG=en_US.UTF-8 > /etc/locale.conf
```

## 사용자 설정
### root 패스워드 수정
root 계정을 패스워드 없이 사용하는 것은 보안상 위험하다. 따라서 root 계정의 패스워드를 변경하자:
```
# passwd
```

### 사용자 계정 추가
사용자 계정을 추가한다:
```
# useradd -m -g users -G wheel <사용자_이름>
# passwd <사용자_이름>
```
> -m: home폴더 만들기
> -g: group
> -G secondary group
> wheel은 관리자 그룹이다. `sudo`를 사용하기 위해 주가한다. [ArchWiki 그룹 리스트](https://wiki.archlinux.org/index.php/Users_and_groups#Group_list) 참고

### sudo 설정
이제 `/etc/sudoers`를 수정해 `wheel`그룹이 `sudo`를 사용할 수 있게 설정한다. 그런데 직접 수정하기보다 `visudo`를 이용해 수정하기를 권장한다.

```
# visudo
```

```
# %wheel ALL=	//주석을 해제해준다:
%wheel ALL=
```
## 네트워크 설정
### 호스트 이름 및 hosts설정
컴퓨터의 이름을 설정해준다. 기호에 맞게 바꿔라.
```
# echo "helloarch" > /etc/hostname
```

`/etc/hosts` 파일을 다음과 같이 수정한다.
```
127.0.0.1 localhost
::1 localhost
127.0.1.1 helloarch.localdomain helloarch
```

### 네트워크 설정
[NetworkManager](https://wiki.archlinux.org/index.php/NetworkManager)를 활성화한다.
```
# systemctl enable NetworkManager.service
```

## 부트로더 설치
`grub`을 사용한다. UEFI 부팅시 `efibootmgr`도 설치해야한다.
```
# grub-install /dev/sda
# grub-mkconfig -o /boot/grub/grub.cfg
```

### Windows dual booting
`grub-mkconfig`를 하기 전에 `os-prober`를 설치하면 windows도 capture한다.

```
# pacman -S os-prober
# grub-mkconfig -o /boot/grub/grub.cfg
```

윈도우는 localtime, linux는 UTC를 사용하기 때문에 둘 중 하나를 바꿔줘야 한다. 여기서는 linux가 localtime을 사용하도록 바꾼다.
```
# timedatectl set-local-rtc 1 --adjust-system-clock
```

## 재부팅
만약 위에서 networkmanager나 dhcpcd를 설정하지 않았으면 재부팅 했을 때 네트워크가 작동하지 않는다.
```
# exit # chroot 나오기
# umount -R /mnt
# reboot
```

# 설치 후에 할 일들
## microcode
[Arch Wiki-Microcode](https://wiki.archlinux.org/index.php/Microcode)

* `inter-ucode` (pacman)

```
# grub-mkconfig -o /boot/grub/grub.cfg
```
## yay 설치
yay는 AUR을 사용하기 편하게 해주는 wrapper이다.
```
# git clone https://aur.archlinux.org/yay.git
# cd yay
# makepkg -si
```

### 간단한 사용법
* package 동기화 :`yay`
* 검색: `yay -Ss <package name>`
* 설치: `yay -S <package name>`

## 앞으로 설치할 패키지들
한꺼번에 설치하기 위해 적어놓는다. 알아서 취사선택

```
# pacman -S xf-86-video-intel xorg-server xorg-apps compton lightdm lightdm-gtk-greeter uim intel-ucode xautomation neovim rxvt-unicode ranger xcape feh zsh als-utils pulseaudio pavucontrol qbittorent vlc cups
```
* xf86-video-intel: intel graphics driver
* lightdm: display manager
* compton: compositer
* uim: 한글 입력기
* intel-ucode: microcode
* xautomation: xte를 사용하기 위해. xte는 fake input generator
* neovim: improved vim
* rxvt-unicode: terminal emulator
* ranger: terminal based file explorer
* xcape
* feh: image viewer & background setter
* zsh: shell
* pulseaudio: audio server
* pavucontrol: pulseaudio front end
* qbittorent
* vlc
* cups: printer server

```
$ yay -S i3-gaps rofi polybar siji ttf-nanum xbindkeys brave xidlehook visual-studio-code-bin
```
* i3-gaps: window manager
* rofi: app launcher
* polybar: status bar
* sifi: icon font for polybar
* ttf-nanum: 한글 폰트
* xbindkeys
* brave: internet browser
* xidlehook: for auto suspend
* visual-studio-code-bin: VS Code


## 그래픽 환경 설치
### Graphic Driver
그래픽 카드 종류를 확인한다:
```
$ lspci | grep -e VGA -e 3D
```

인텔의 경우 `xf86-video-intel`을 설치한다. [ArchWiki 그래픽 드라이버](https://wiki.archlinux.org/index.php/Xorg#Driver_installation) 참고.

### X11 Server 설치
```
# pacman -S xorg-server xorg-apps
```

이제 desktop environment/window manager를 선택해야한다. 여기서는 window manager로 i3-gaps, display manager로 lightdm을 사용하기로 한다.

### lightdm 설치
lightdm은 greeter를 따로 설치해줘야한다.

* `lightdm` (pacman)
* `lightdm-gtk-greeter` (pacman)

`/etc/lightdm/lightdm.conf`에서 greeter를 설정한다:
```
[SeatDefaults]
greeter-session=lightdm-gtk-greeter
```
위의 `lightdm-gtk-greeter`를 알맞게 변경해준다. `/usr/share/xgreeters/*.desktop`에서 확인할 수 있다.

이제 lightdm을 활성화 시켜준다. enable을 start로 바꾸면 바로 실행할 수 있다.
```
# systemctl enable lightdm
```

### 한글화
한글화를 위해 글꼴과 입력기를 설치한다.
* `ttf-nanum` (AUR)
* `uim` (pacman)

`~/.xprofile`을 수정한다.
```
export GTK_IM_MODULE='uim'
export QT_IM_MODULE='uim'
uim-xim &
export XMODIFIERS='@im=uim'
```

## 기타

### 마우스 키 바인딩
* `xbindkeys` (AUR)
* `xautomation` (pacman)
 
### pip 설치
```
curl https://bootstrap.pypa.io/get-python -o get-pip.py
sudo python get-pip.py
```

### 오디오 설정
#### Change default sink
`/etc/pulse/default.pa`
```
load-module module-stream-resotre restore_devide=false
```

```
$ pacmd list-sinks
$ pacmd set-default-sink <number or name>
```
### 프린터
`cups` 설치 후 
```
# systemctl start org.cups.cupsd.service
```

#### 프린터 드라이브
yay -S brother-hll2360d

http://localhost:631/에서 add printer

참고
[블로그](https://whjeon.com/arch-install/)

## 설치할 것
* dunst: notification
* xclip: clipboard
* flameshot: screenshot
* thunar
* zathura: pdf viewer
* zathura-pdf-poppler: pdf viewer
* okular
