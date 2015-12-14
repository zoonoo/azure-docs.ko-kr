<properties 
	pageTitle="Red Hat Enterprise Linux VHD 만들기 및 Azure에서 사용하도록 업로드" 
	description="RedHat Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="SuperScottz" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="mingzhan"/>


# Azure용 RedHat 기반 가상 컴퓨터 준비
이 문서에서는 Azure용 Red Hat Enterprise Linux(RHEL) 가상 컴퓨터를 준비하는 방법을 알아봅니다. 이 문서에서 다루는 RHEL 버전은 6.7 및 7.1이며 이 문서에서 다루는 준비에 대한 하이퍼바이저는 Hyper-V, KVM, VMWare입니다. Red Hat 클라우드 액세스 프로그램에 참여하기 위한 자격 요구 사항에 대한 자세한 내용은 [Red Hat 클라우드 액세스 웹 사이트](http://www.redhat.com/en/technologies/cloud-computing/cloud-access)를 참조하세요.




##Hyper-V 관리자에서 이미지 준비 
###필수 조건
이 섹션은 RedHat 웹 사이트에서 확보한 ISO 파일로 가상 하드 디스크(VHD)에 RHEL 이미지를 이미 설치한 것으로 가정합니다. Hyper-V 관리자를 사용하여 운영 체제 이미지를 설치하는 방법에 대한 내용은 [Hyper-V 역할 설치 및 가상 컴퓨터 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하세요.

**RHEL 설치 참고 사항**

- 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd powershell cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

- Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 RAID를 사용할 수 있습니다.

- OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.

- 모든 VHD 크기는 1MB의 배수여야 합니다.

- qemu-img를 사용하여 디스크 이미지를 VHD 형식으로 변환할 때는 qemu-img 버전 >=2.2.1로 인해 VHD 형식이 잘못 지정됩니다. 이는 알려진 버그입니다. 이 문제는 향후 qemu-img 릴리스에서 수정될 예정입니다. 현재에는 qemu-img 2.2.0 이하 버전을 이용하는 것이 좋습니다.


###RHEL 6.7

1.	Hyper-V 관리자에서 가상 컴퓨터를 선택합니다.

2.	**연결**을 클릭하여 가상 컴퓨터의 콘솔 창을 엽니다.

3.	다음 명령을 실행하여 NetworkManager를 제거합니다.

        # sudo rpm -e --nodeps NetworkManager

    **참고:** 패키지가 아직 설치되어 있지 않은 경우 이 명령이 실패하고 오류 메시지가 표시됩니다. 예상된 동작입니다.

4.	다음 텍스트가 포함된 **network** 파일을 `/etc/sysconfig/` 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	다음 텍스트가 포함된 **ifcfg-eth0** 파일을 `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.
            
        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo chkconfig network on

8.	RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	WALinuxAgent 패키지 `WALinuxAgent-<version>`가 Fedora EPEL 6 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 EPEL 리포지토리를 사용하도록 설정합니다.

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 `/boot/grub/menu.lst`를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 또한 RHEL 6에서 사용하는 커널 버전의 버그로 인해 NUMA를 사용하지 않도록 설정합니다.

    위의 작업을 수행하는 동시에 다음 매개 변수도 제거하는 것이 좋습니다.

        rhgb quiet crashkernel=auto

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.

    원하는 경우에는 crashkernel 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

11.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다. 다음 줄을 포함하도록 /etc/ssh/sshd\_config를 수정합니다.

        ClientAliveInterval 180

12.	다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **참고:** WALinuxAgent 패키지를 설치하면 NetworkManager 및 NetworkManager-gnome 패키지가 2단계에서 설명된 대로 아직 제거되지 않은 경우 이러한 패키지를 제거합니다.

13.	OS 디스크에 스왑 공간을 만들지 마세요. Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # sudo subscription-manager unregister

15.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Hyper-V 관리자에서 **작업 -> 종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

###RHEL 7.1

1. Hyper-V 관리자에서 가상 컴퓨터를 선택합니다.

2.	연결을 클릭하여 가상 컴퓨터의 콘솔 창을 엽니다.

3.	다음 텍스트가 포함된 **network** 파일을 `/etc/sysconfig/` 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	다음 텍스트가 포함된 **ifcfg-eth0** 파일을 `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo chkconfig network on

6.	RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 `/etc/default/grub`을 열고 **GRUB\_CMDLINE\_LINUX** 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 위의 작업을 수행하는 동시에 다음 매개 변수도 제거하는 것이 좋습니다.

        rhgb quiet crashkernel=auto
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원하는 경우에는 crashkernel 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

8.	위의 설명에 따라 `/etc/default/grub` 편집을 완료한 후에는 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config`를 수정합니다.

        ClientAliveInterval 180

10.	WALinuxAgent 패키지 `WALinuxAgent-<version>`가 Fedora EPEL 6 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 EPEL 리포지토리를 사용하도록 설정합니다.

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12.	OS 디스크에 스왑 공간을 만들지 마십시오. Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	구독에 대한 등록을 해제하려면 다음 명령을 실행합니다.

        # sudo subscription-manager unregister

14.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.
        
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Hyper-V 관리자에서 **작업 -> 종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.


##KVM에서 이미지 준비 
###RHEL 6.7

1.	Red Hat 웹 사이트에서 RHEL 6.7의 KVM 이미지를 다운로드합니다.

2.	루트 암호를 설정합니다.

    암호화된 암호를 생성하고 명령 출력을 복사합니다.

        # openssl passwd -1 changeme
    guestfish 루트 암호를 설정합니다.
       
        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    루트 사용자의 두 번째 필드를 “!!”에서 암호화된 암호로 변경합니다.

3.	qcow2 이미지에서 KVM으로 가상 컴퓨터를 만들고, 디스크 형식을 **qcow2**로 설정하고, 가상 네트워크 인터페이스 장치 모델을 **virtio**로 설정합니다. 그 후 가상 컴퓨터를 시작하고 루트로 로그인합니다.

4.	다음 텍스트가 포함된 **network** 파일을 `/etc/sysconfig/` 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	다음 텍스트가 포함된 **ifcfg-eth0** 파일을 `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # chkconfig network on

8.	RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # subscription-manager register –auto-attach --username=XXX --password=XXX

9.	Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 `/boot/grub/menu.lst`를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 또한 RHEL 6에서 사용하는 커널 버전의 버그로 인해 NUMA를 사용하지 않도록 설정합니다.

    위의 작업을 수행하는 동시에 다음 매개 변수도 제거하는 것이 좋습니다.

        rhgb quiet crashkernel=auto

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원하는 경우에는 crashkernel 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

10.	Cloud-Init을 제거합니다.

        # yum remove cloud-init

11.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.
 
        # chkconfig sshd on

    다음 줄을 포함하도록 /etc/ssh/sshd\_config를 수정합니다.

        PasswordAuthentication yes
        ClientAliveInterval 180

    sshd를 다시 시작합니다.

		# service sshd restart

12.	WALinuxAgent 패키지 `WALinuxAgent-<version>`가 Fedora EPEL 6 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 EPEL 리포지토리를 사용하도록 설정합니다.

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) **/etc/waagent.conf**에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.
        
        # subscription-manager unregister

16.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	KVM에서 VM을 종료합니다.

18.	qcow2 이미지를 vhd 형식으로 변환합니다. 우선 이미지를 원시 형식으로 변환합니다.
         
         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    원시 이미지의 크기를 1MB에 맞게 조정해야 합니다. 1MB에 정확하게 맞출 수 없는 경우 1MB에 맞게 크기를 반올림합니다. # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    원시 디스크를 고정 크기 vhd로 변환합니다.

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


###RHEL 7.1

1.	Red Hat 웹 사이트에서 RHEL 7.1의 KVM 이미지를 다운로드합니다.

2.	루트 암호를 설정합니다.

    암호화된 암호를 생성하고 명령 출력을 복사합니다.

        # openssl passwd -1 changeme

    guestfish 루트 암호를 설정합니다.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    루트 사용자의 두 번째 필드를 “!!”에서 암호화된 암호로 변경합니다.

3.	qcow2 이미지에서 KVM으로 가상 컴퓨터를 만들고, 디스크 형식을 **qcow2**로 설정하고, 가상 네트워크 인터페이스 장치 모델을 **virtio**로 설정합니다. 그 후 가상 컴퓨터를 시작하고 루트로 로그인합니다.

4.	다음 텍스트가 포함된 **network** 파일을 `/etc/sysconfig/` 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	다음 텍스트가 포함된 **ifcfg-eth0** 파일을 `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # chkconfig network on

7.	RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # subscription-manager register –auto-attach --username=XXX --password=XXX

8.	Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 `/etc/default/grub`을 열고 **GRUB\_CMDLINE\_LINUX** 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 위의 작업을 수행하는 동시에 다음 매개 변수도 제거하는 것이 좋습니다.

        rhgb quiet crashkernel=auto

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원하는 경우에는 crashkernel 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

9.	위의 설명에 따라 `/etc/default/grub` 편집을 완료한 후에는 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Cloud-Init을 제거합니다.

        # yum remove cloud-init

11.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

        # systemctl enable sshd

    다음 줄을 포함하도록 /etc/ssh/sshd\_config를 수정합니다.

        PasswordAuthentication yes
        ClientAliveInterval 180

    sshd를 다시 시작합니다.

        systemctl restart sshd	

12.	WALinuxAgent 패키지 `WALinuxAgent-<version>`가 Fedora EPEL 6 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 EPEL 리포지토리를 사용하도록 설정합니다.

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

13.	다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # yum install WALinuxAgent

    waagent 서비스를 사용하도록 설정합니다.

        # systemctl enable waagent.service

14.	OS 디스크에 스왑 공간을 만들지 마십시오. Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # subscription-manager unregister

16.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	KVM의 가상 컴퓨터를 종료합니다.

18.	qcow2 이미지를 vhd 형식으로 변환합니다.

    우선 이미지를 원시 형식으로 변환합니다.

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    원시 이미지의 크기를 1MB에 맞추어 조정해야 합니다. 그렇지 않으면 1MB에 맞추어 크기를 올림합니다.

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    원시 디스크를 고정 크기 vhd로 변환합니다.

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##VMWare에서 이미지 준비
###필수 조건
이 섹션은 VMWare에 RHEL 가상 컴퓨터가 이미 설치되어 있다고 가정합니다. VMWare에서 운영 체제를 설치하는 방법은 [VMWare 게스트 운영 체제 설치 가이드](http://partnerweb.vmware.com/GOSIG/home.html)를 참조하세요.
 
- Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 RAID를 사용할 수 있습니다.

- OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.

- 가상 하드 디스크를 만들 때 **Store virtual disk as a single file**(가상 디스크를 단일 파일로 저장)을 선택합니다.

###RHEL 6.7
1.	다음 명령을 실행하여 NetworkManager를 제거합니다.

         # sudo rpm -e --nodeps NetworkManager

    **참고:** 패키지가 아직 설치되어 있지 않은 경우 이 명령이 실패하고 오류 메시지가 표시됩니다. 예상된 동작입니다.

2.	다음 텍스트가 포함된 **network**라는 파일을 /etc/sysconfig/ 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	다음 텍스트가 포함된 **ifcfg-eth0**이라는 파일을 /etc/sysconfig/network-scripts/ 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo chkconfig network on

6.	RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	WALinuxAgent 패키지 `WALinuxAgent-<version>`가 Fedora EPEL 6 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 EPEL 리포지토리를 사용하도록 설정합니다.

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 "/boot/grub/menu.lst"를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 또한 RHEL 6에서 사용하는 커널 버전의 버그로 인해 NUMA를 사용하지 않도록 설정합니다. 위의 작업을 수행하는 동시에 다음 매개 변수도 제거하는 것이 좋습니다.

        rhgb quiet crashkernel=auto

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원하는 경우에는 crashkernel 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

9.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config`를 수정합니다.

        ClientAliveInterval 180

10.	다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	OS 디스크에 스왑 공간을 만들지 마세요.
    
    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # sudo subscription-manager unregister

13.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14.	VM을 종료하고 VMDK 파일을 VHD 파일로 변환합니다.

    우선 이미지를 원시 형식으로 변환합니다.

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    원시 이미지의 크기를 1MB에 맞추어 조정해야 합니다. 그렇지 않으면 1MB에 맞추어 크기를 올림합니다.

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    원시 디스크를 고정 크기 vhd로 변환합니다.

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

###RHEL 7.1

1.	다음 텍스트가 포함된 **network**라는 파일을 /etc/sysconfig/ 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	다음 텍스트가 포함된 **ifcfg-eth0**이라는 파일을 /etc/sysconfig/network-scripts/ 디렉터리에 만듭니다.

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo chkconfig network on

4.	RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 `/etc/default/grub`을 열고 **GRUB\_CMDLINE\_LINUX** 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 위의 작업을 수행하는 동시에 다음 매개 변수도 제거하는 것이 좋습니다.

        rhgb quiet crashkernel=auto

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원하는 경우에는 crashkernel 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

6.	위의 설명에 따라 `/etc/default/grub` 편집을 완료한 후에는 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Hyper-V 모듈을 initramfs에 추가합니다.

    `/etc/dracut.conf`를 편집하고 다음 콘텐츠를 추가합니다.

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    initramfs를 다시 빌드합니다.

        # dracut –f -v

8.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config`를 수정합니다.

        ClientAliveInterval 180

9.	WALinuxAgent 패키지 `WALinuxAgent-<version>`가 Fedora EPEL 6 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 EPEL 리포지토리를 사용하도록 설정합니다.


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	OS 디스크에 스왑 공간을 만들지 마십시오. Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	구독에 대한 등록을 해제하려면 다음 명령을 실행합니다.

        # sudo subscription-manager unregister

13.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	VM을 종료하고 VMDK 파일을 VHD 형식으로 변환합니다.

    우선 이미지를 원시 형식으로 변환합니다.

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    원시 이미지의 크기를 1MB에 맞추어 조정해야 합니다. 그렇지 않으면 1MB에 맞추어 크기를 올림합니다.

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    원시 디스크를 고정 크기 vhd로 변환합니다.

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##kickstart 파일을 사용하여 ISO에서 자동으로 준비
###RHEL 7.1

1.	아래 콘텐츠를 사용하여 kickstart 파일을 만들고 저장합니다. Kickstart 설치에 대한 내용은 [Kickstart 설치 가이드](https://access.redhat.com/documentation/ko-KR/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)를 참조하세요.


        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

 

2.	kickstart 파일을 설치 시스템에서 읽을 수 있는 위치에 보관합니다.
 
3.	Hyper-V 관리자에서 새 VM을 만듭니다. **가상 하드 디스크 연결** 페이지에서 **나중에 가상 하드 디스크 연결**을 선택하고 새 가상 컴퓨터 마법사를 완료합니다.

4.	VM 설정을 엽니다.

    a. 새 가상 하드 디스크를 VM에 연결하고 **VHD 형식** 및 **고정 크기**를 선택합니다.
    
    b. 설치 ISO를 DVD 드라이브에 연결합니다.

    c. CD에서 부팅하도록 BIOS를 설정합니다.

5.	VM을 시작하고, 설치 가이드가 나타나면 **Tab** 키를 눌러서 부팅 옵션을 구성합니다.

6.	부팅 옵션 마지막에 `inst.ks=<the location of the Kickstart file>`을 입력하고 **Enter** 키를 누릅니다.

7.	설치를 마칠 때까지 대기합니다. 완료되면 VM이 자동으로 종료됩니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

##알려진 문제
Hyper-V 및 Azure에서 RHEL 7.1 사용 시 알려진 문제가 있습니다.

###문제: 디스크 I/O 중지 

이 문제는 Hyper-V 및 Azure에서 RHEL 7.1을 사용하여 저장소 디스크 I/O 작업이 빈번하게 이뤄지는 동안 발생할 수 있습니다.

발생 비율:

일시적인 문제이지만 Hyper-V 및 Azure에서 디스크 I/O 작업이 빈번하게 이뤄지는 동안 더 자주 발생할 수 있습니다.

    
[AZURE.NOTE]알려진 이 문제는 Red Hat에서 이미 해결했습니다. 연결된 수정 프로그램을 설치하려면 다음 명령을 실행합니다.

    # sudo yum update

<!---HONumber=AcomDC_1203_2015-->