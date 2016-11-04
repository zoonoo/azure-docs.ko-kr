---
title: FreeBSD VM 이미지 만들기 및 업로드 | Microsoft Docs
description: FreeBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하여 Azure 가상 컴퓨터를 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: kyliel

---
# FreeBSD VHD를 만들어서 Azure에 업로드
이 문서에서는 FreeBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다. VHD를 업로드한 후에는 VHD를 사용자 고유의 이미지로 사용하여 Azure에서 VM(가상 컴퓨터)을 만들 수 있습니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## 필수 조건
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

* **Azure 구독** - 계정이 없는 경우 몇 분 만에 계정을 만들 수 있습니다. MSDN 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요. 그렇지 않으면 [무료 평가판 계정 만들기](https://azure.microsoft.com/pricing/free-trial/)를 참조하세요.
* **Azure PowerShell 도구** - Azure PowerShell 모듈이 설치되어 있고 Azure 구독을 사용하도록 구성되어 있어야 합니다. 모듈을 다운로드하려면 [Azure 다운로드](https://azure.microsoft.com/downloads/)를 참조하세요. 모듈 설치 및 구성에 대한 자습서는 여기에서 확인할 수 있습니다. [Azure Downloads](https://azure.microsoft.com/downloads/) cmdlet을 사용하여 VHD를 업로드합니다.
* **.vhd 파일에 설치된 FreeBSD 운영 체제** - 가상 하드 디스크에 지원되는 FreeBSD 운영 체제를 설치해야 합니다. .vhd 파일을 만드는 도구는 여러 가지가 있습니다. 예를 들어 Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. Hyper-V를 설치하고 사용하는 방법에 대한 자세한 내용은 [Hyper-V 설치 및 가상 컴퓨터 만들기](http://technet.microsoft.com/library/hh846766.aspx)를 참조하세요.

> [!NOTE]
> 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx) cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. 뿐만 아니라 [Hyper-V에 FreeBSD를 사용하는 방법에 대한 MSDN 자습서](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)가 있습니다.
> 
> 

이 작업에는 다음 5단계가 포함됩니다.

## 1단계: 업로드할 이미지 준비
FreeBSD 운영 체제를 설치한 가상 컴퓨터에서 다음 절차를 완료합니다.

1. DHCP를 활성화합니다.
   
        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. SSH를 활성화합니다.
   
    SSH는 디스크에서 설치 후 기본적으로 사용됩니다. 어떤 이유로 활성화하지 않았거나 FreeBSD VHD를 직접 사용하는 경우 다음을 입력합니다.
   
        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart
3. 직렬 콘솔을 설치합니다.
   
        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. sudo를 설치합니다.
   
    Azure에서 root 계정이 비활성화됩니다. 다시 말해서 상승된 권한으로 명령을 실행하려면 권한 없는 사용자로 sudo를 사용해야 합니다.
   
        # pkg install sudo
   ;
5. Azure 에이전트에 대한 필수 조건.
   
        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Azure 에이전트를 설치합니다.
   
    언제든지 최신 버전의 Azure 에이전트를 [github](https://github.com/Azure/WALinuxAgent/releases)에서 확인할 수 있습니다. 버전 2.0.10+는 FreeBSD 10 및 10.1을 공식적으로 지원하며 버전 2.1.4는 FreeBSD 10.2 이상 릴리스를 공식적으로 지원합니다.
   
        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1
   
    2\.0의 경우 2.0.16을 예로 사용하겠습니다.
   
        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
   
    2\.1의 경우 2.1.4를 예로 사용하겠습니다.
   
        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0
   
   > [!IMPORTANT]
   > Azure 에이전트를 설치한 후 에이전트가 실행 중인지 확인하는 것이 좋습니다.
   > 
   > 
   
        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log
7. 시스템을 프로비전 해제합니다.
   
    시스템을 프로비전 해제하여 다시 프로비전하기에 적합한 상태로 정리합니다. 다음 명령은 마지막으로 프로비전된 사용자 계정 및 관련 데이터를 삭제합니다.
   
        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf
   
    이제 VM을 종료할 수 있습니다.

## 2단계: Azure에서 저장소 계정 만들기
가상 컴퓨터를 만드는 데 사용할 수 있도록 .vhd 파일을 업로드하려면 Azure에 저장소 계정이 있어야 합니다. Azure 클래식 포털을 사용하여 저장소 계정을 만들 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 명령 모음에서 **새로 만들기**를 선택합니다.
3. **데이터 서비스** > **저장소** > **빠른 생성**을 선택합니다.
   
    ![저장소 계정 빠른 생성](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)
4. 다음과 같이 필드를 채웁니다.
   
   * **URL** 필드에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 3-24자의 숫자와 소문자를 사용할 수 있습니다. 이 이름은 구독에 대한 Azure Blob 저장소, Azure 큐 저장소 또는 Azure 테이블 저장소 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다.
   * **위치/선호도 그룹** 드롭다운 메뉴에서 저장소 계정의 **위치 또는 선호도 그룹**을 선택합니다. 선호도 그룹을 사용하면 클라우드 서비스와 저장소를 동일한 데이터 센터에 배치할 수 있습니다.
   * **복제** 필드에서, 저장소 계정에 **지역에서 복제**를 사용할지 여부를 결정합니다. 지역에서 복제는 기본적으로 설정되어 있습니다. 이 옵션을 사용하면 추가 비용 없이 보조 위치로 데이터를 복제하므로 기본 위치에서 심각한 장애가 발생하는 경우 저장소에서 보조 위치로 장애 조치(Failover)할 수 있습니다. 보조 위치는 자동으로 할당되며 변경될 수 없습니다. 법적 필요 또는 조직 정책에 따라 클라우드 기반 저장소의 위치를 더 엄격하게 제어해야 하는 경우 지역에서 복제를 해제할 수 있습니다. 그러나 나중에 지역에서 복제를 설정하는 경우 기존 데이터를 대체 위치로 복제하는 데 일회성 데이터 전송 요금이 청구됩니다. 지역에서 복제를 사용하지 않는 저장소 서비스는 할인하여 제공됩니다. 저장소 계정의 지역에서 복제를 관리하는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](../storage/storage-create-storage-account.md#replication-options)에서 확인할 수 있습니다.
     
     ![저장소 계정 세부 정보 입력](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)
5. **저장소 계정 만들기**를 선택합니다. 이제 계정이 **저장소** 아래에 나타납니다.
   
    ![저장소 계정을 성공적으로 만들었음](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)
6. 다음으로 업로드된 .vhd 파일의 컨테이너를 만듭니다. 저장소 계정 이름을 선택하고 **컨테이너**를 선택합니다.
   
    ![저장소 계정 세부 정보](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)
7. **컨테이너 만들기**를 선택합니다.
   
    ![저장소 계정 세부 정보](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)
8. **이름** 필드에 컨테이너의 이름을 입력합니다. 그런 다음 **액세스** 드롭 다운 메뉴에서 원하는 액세스 정책 유형을 선택합니다.
   
    ![컨테이너 이름](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)
   
   > [!NOTE]
   > 기본적으로 컨테이너는 전용이며 해당 계정 소유자만 액세스할 수 있습니다. 컨테이너 속성 및 메타데이터는 제외하고 컨테이너에 있는 Blob에 대한 공용 읽기 권한을 허용하려면 **공용 Blob** 옵션을 사용하세요. 컨테이너 및 Blob에 대한 전체 공용 읽기 권한을 허용하려면 **공용 컨테이너** 옵션을 사용하세요.
   > 
   > 

## 3단계: Azure 연결 준비
.vhd 파일을 업로드하려면 컴퓨터와 Azure 구독 간에 보안 연결을 설정해야 합니다. 이를 위해 Azure AD(Azure Active Directory) 방법이나 인증서 방법을 사용할 수 있습니다.

### Azure AD 방법을 사용하여 .vhd 파일 업로드
1. Azure PowerShell 콘솔을 엽니다.
2. 다음 명령을 입력합니다. `Add-AzureAccount`
   
    이 명령은 직장 또는 학교 계정으로 로그인할 수 있는 로그인 창을 엽니다.
   
    ![PowerShell 창](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure가 자격 증명 정보를 인증한고 저장합니다. 그런 다음 창을 닫습니다.

### 인증서 방법을 사용하여 .vhd 파일 업로드
1. Azure PowerShell 콘솔을 엽니다.
2. 다음을 입력합니다. `Get-AzurePublishSettingsFile`
3. .publishsettings 파일을 다운로드할 것을 요구하는 브라우저 창이 열립니다. 이 파일에는 Azure 구독에 대한 정보와 인증서가 포함되어 있습니다.
   
    ![브라우저 다운로드 페이지](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. .publishsettings 파일을 저장합니다.
5. 입력: `Import-AzurePublishSettingsFile <PathToFile>`, 여기서 `<PathToFile>`은 .publishsettings 파일의 전체 경로입니다.
   
   자세한 내용은 [Azure Cmdlets 시작](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)을 참조하세요.
   
   PowerShell 설치 및 구성에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 4단계: .vhd 파일 업로드
.vhd 파일을 업로드할 때 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음은 파일을 업로드할 때 사용되는 몇 가지 용어입니다.

* **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL입니다.
* **YourImagesFolder**는 이미지를 저장하려는 Blob 저장소 내 컨테이너입니다.
* **VHDName**은 가상 하드 디스크를 식별하기 위해 Azure 클래식 포털에 표시되는 레이블입니다.
* **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다.

이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## 5단계: 업로드한 .vhd 파일로 VM 만들기
.vhd 파일을 업로드한 후에는 구독과 연결된 사용자 지정 이미지 목록에 .vhd 파일을 이미지로 추가하고 이 사용자 지정 이미지를 사용하여 가상 컴퓨터를 만들 수 있습니다.

1. 이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.
   
        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>
   
   > [!NOTE]
   > OS 유형으로 Linux를 사용합니다. Azure PowerShell 최신 버전은 매개 변수로 "Linux" 또는 "Windows"만 허용합니다.
   > 
   > 
2. 이전 단계를 완료한 후 Azure 클래식 포털에서 **이미지** 탭을 선택하면 새 이미지가 나열됩니다.
   
    ![이미지 선택](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)
3. 갤러리에서 가상 컴퓨터를 만듭니다. 이제 **내 이미지**에서 이 새 이미지를 사용할 수 있습니다.
4. 새 이미지를 선택합니다. 다음으로 메시지에 따라 호스트 이름, 암호, SSH 키 등을 설정합니다.
   
    ![사용자 지정 이미지](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. 프로비전이 완료되면 FreeBSD VM이 Azure에서 실행됩니다.
   
    ![Azure의 FreeBSD 이미지](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!---HONumber=AcomDC_0831_2016-->