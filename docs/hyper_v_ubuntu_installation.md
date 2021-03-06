# 하이퍼 V 우분투 설치 

## 환경 Environments

* Windows-10-Pro-x64
* Ubuntu-14.04-x64

## 하이퍼 V 설치 Install Hyper-V

* 제어판 > 프로그램 및 기능 > Windows 기능 켜기 / 끄기 클릭
* Hyper-V 체크 후 확인 버튼 클릭
* 재부팅

## 우분투 다운로드 Download Ubuntu

* 다음 카카오 <http://ftp.daumkakao.com/ubuntu-releases/14.04/ubuntu-14.04.5-server-amd64.iso>
* 네오위즈 <http://ftp.neowiz.com/ubuntu-releases/14.04.5/ubuntu-14.04.5-server-amd64.iso>

## 하이퍼 V 가상 컴퓨터 생성 Create Hyper-V Virtual Machine

* Hyper-V 관리자 실행
* 작업 > 가상 스위치 관리자 > 가상 스위치 관리자 다이얼로그 
    * 유형: 내부 선택 > 가상 스위치 만들기 버튼 클릭
    * 새 가상 스위치 선택
        * 이름: `NAT`
        * 확인 버튼 클릭 
* 작업 > 새로 만들기 > 새 가상 컴퓨터 마법사 다이얼로그 
    * 이름: `Ubuntu-14.04.5-x64`
    * 세대: 1세대 선택
    * 메모리
        * 시작 메모리: 1024MB
        * 동적 메모리: 사용
    * 네트워킹: `NAT` 스위치 선택
    * 가상 하드 디스크 연결
        * 이름: `Ubuntu-14.04.5-x64.vhdx`
        * 위치: `C:\Users\Public\Documents\Hyper-V\Virtual hard disks\Ubuntu-14.04.5-x64.vhdx`
        * 크기: `10GB`
    * 설치 옵션 > 부팅 가능 CD/DVD-ROM 에서 운영 체제 설치: `ubunutu-14.04.5-server-amd64.iso 선택`
    * 마침
* 가상 컴퓨터 `Ubuntu-14.04.5-x64` 선택 > 설정 클릭
    * 프로세서 카테고리 선택 > 가상 프로세서 수: 2개 
    * 확인

## 인터넷 연결 공유 Share Internet Connection

* 제어판 > 네트워크 및 공유 센터 > 인터넷 이더넷 클릭
* 이더넷 상태 다이얼로그 > 속성 버튼 클릭
* 이더넷 속성 다이얼로그 > 공유 탭 선택
    * 인터넷 연결 공유
    * 다른 네트워크 사용자가 이 컴퓨터의 인터넷 연결을 통해 연결할 수 있도록 허용 체크
    * 홈 네트워킹 연결: `NAT`
    * 확인 버튼 클릭

## 우분투 설치 Install Ubuntu

* Hyper-V 가상 컴퓨터 `Ubuntu-14.04.5-x64` 선택 
    * 시작 클릭
    * 연결 클릭
* Lanauage: `English` 선택
* `Install Ubuntu Server` 선택 
* Select a language: `English - English` 선택 
* Select your location: `Unite States` 선택 
* Configure the keyboard
    * Detect keyboard layout? `No` 선택 
    * Country of origin for the keyboard: `English (US)` 선택 
    * Keyboard layout: `English (US)` 선택 
* Configure the network:
    * DHCP `Cancel` 선택 
    * Network autoconfiguration failed: `Continue` 선택 
    * Network configuration method: `Configure network manually` 선택 
        * IP Address: `192.168.137.100/24` 입력
        * Gateway: `192.168.137.1` 입력
        * Name server address: `8.8.8.8` 입력
        * Hostname: `ubuntu` 입력
        * Domain name: `` 입력
* Set up users and passwords:
    * Full name for the new user: `실제 이름` 입력
    * User name for your account: `계정 이름` 입력
    * Choose a password for the new user: `비밀번호` 입력
    * Re-enter password for verify: `비밀번호` 재입력
    * Encrypt your home directory?: `No` 선택
* Setting up the clock > Select your time zone: `Select from worldwide list` 선택 > `Asia/Seoul` 선택
* Partition disks:
    * Partitioning method: `Guided - use enitre disk and setup LVM` 선택udo 
    * Select disk to partition: `SCSI3 (0,0,0) (sda) - 10.7GB Msft Virtual Disk` 선택
    * Wrtie the changes to disks and configure LVM? `Yes` 선택
    * Amount of volume group to use for guided partioning: `Continue` 선택    
    * Write the changes to diskss? `Yes` 선택
* Configure the package manager >  HTTP proxy information: `Continue` 선택
* Configure tasksel: How do you want to manage upgrades on this system? `No automatic updates` 선택
* Software selection: `OpenSSH server` 체크
* Install the GRUD boot loader on a hard disk: `Yes` 선택

## 우분투 셋업 Setup Ubuntu

### 네트워크 확인

#### PING

적당한 네임 서버에 핑을 확인합니다.

```bash
$ ping 168.126.63.1
```

핑 실패시 **인터넷 연결 공유**을 재설정합니다.

#### SSH

bash 나 putty 로 <ssh://192.168.137.100> 에 접속합니다.

접속시 password 프롬프트가 늦게 표시된다면 네임 서버 주소를 확인합니다.

#### IP

```bash
$ sudo vim /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
        address 192.168.137.100
        netmask 255.255.255.0
        network 192.168.137.0
        broadcast 192.168.137.255
        gateway 192.168.137.1
        # dns-* options are implemented by the resolvconf package, if installed
        dns-nameservers 8.8.8.8
```

이더넷 설정을 재시작합니다.

```bash
$ sudo ifdown eth0 && sudo ifup eth0
```

네임 서버가 제대로 변경 되었는지 확인합니다.

```
$ sudo cat /etc/resolv.conf
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 8.8.8.8
```

### 패키지 업데이트

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
```

