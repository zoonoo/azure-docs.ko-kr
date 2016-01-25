<properties
   pageTitle="FreeBSD VM 이미지 만들기 및 업로드 | Microsoft Azure"
   description="FreeBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하여 Azure 가상 컴퓨터를 만드는 방법을 알아봅니다."
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/12/2016"
   ms.author="kyliel"/>

# FreeBSD VHD를 만들어서 Azure에 업로드

이 문서에서는 FreeBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 생성 및 업로드하고 이를 Azure에서 가상 컴퓨터(VM)를 만들기 위한 고유한 이미지로 사용하는 방법을 소개합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


##필수 조건##
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

- **Azure 구독** - 없는 경우 몇 분 만에 계정을 만들 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요. 그렇지 않으면 [무료 평가판 계정 만들기](http://azure.microsoft.com/pricing/free-trial/)를 참조하세요.  

- **Azure PowerShell 도구** - Microsoft Azure PowerShell 모듈이 설치되고 구독을 사용하도록 구성되어 있어야 합니다. 모듈을 다운로드하려면 [Azure 다운로드](http://azure.microsoft.com/downloads/)를 참조하십시오. 모듈 설치 및 구성에 대한 자습서는 여기에서 확인할 수 있습니다. [Azure Downloads](http://azure.microsoft.com/downloads/) cmdlet을 사용하여 VHD를 업로드합니다.

- **.vhd 파일에 설치된 FreeBSD 운영 체제** - 가상 하드 디스크에 지원되는 FreeBSD 운영 체제를 설치했습니다. 다양한 도구를 사용하여 .vhd 파일을 만들 수 있습니다. 예를 들어 Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. 자세한 내용은 [Hyper-V 역할 설치 및 가상 시스템 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오.

> [AZURE.NOTE]새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx) cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

이 작업에는 다음 5단계가 포함됩니다.

## 1단계: 업로드할 이미지 준비 ##

Hyper-v에 FreeBSD를 설치하는 방법에 대한 자습서는 [여기서](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx) 확인할 수 있습니다.

FreeBSD 운영 체제를 설치한 가상 컴퓨터에서 다음 절차를 완료합니다.

1. **DHCP 사용**

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. **SSH 사용**

    SSH는 디스크에서 설치 후 기본적으로 사용됩니다. 그렇지 않은 경우 또는 FreeBSD VHD를 직접 사용하는 경우 다음을 입력합니다.

		# echo 'sshd_enable="YES"' >> /etc/rc.conf
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
		# service sshd restart

3. **직렬 콘솔 설치**

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **sudo 설치**

    루트 계정은 Azure에서 사용할 수 없으므로 상승된 권한으로 명령을 실행하려면 권한 없는 사용자로 sudo를 사용해야 합니다.

		# pkg install sudo

5. Azure 에이전트에 대한 필수 조건

    5\.1 **python 설치**

		# pkg install python27
		# ln -s /usr/local/bin/python2.7 /usr/bin/python

    5\.2 **wget 설치**

		# pkg install wget

6. **Azure 에이전트 설치**

    언제든지 최신 버전의 Azure 에이전트를 [github](https://github.com/Azure/WALinuxAgent/releases)에서 확인할 수 있습니다. 버전 2.0.10 이상에서는 공식적으로 FreeBSD 10 이상 버전을 지원합니다. FreeBSD에 대한 최신 Azure 에이전트 버전은 2.0.16입니다.

		# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
		# mv waagent /usr/sbin
		# chmod 755 /usr/sbin/waagent
		# /usr/sbin/waagent -install

    **중요**: 설치 후 실행 중인지 다시 확인하세요.

		# service –e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

    이제 VM을 **종료**할 수 있습니다. 또한 종료하기 전에 7단계를 실행할 수 있지만 이는 선택 사항입니다.

7. 프로비전 해제는 선택 사항입니다. 시스템을 정리하여 다시 프로비전하기에 적합하도록 만듭니다.

    또한 다음 명령은 마지막으로 프로비전된 사용자 계정 및 관련 데이터를 삭제합니다.

		# waagent –deprovision+user

## 2단계: Azure에서 저장소 계정 만들기 ##

Azure에서 가상 컴퓨터를 만드는 데 사용할 수 있도록 .vhd 파일을 업로드하려면 Azure에 저장소 계정이 있어야 합니다. Azure 클래식 포털을 사용하여 저장소 계정을 만들 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 명령 모음에서 **New**를 클릭합니다.

3. **데이터 서비스** > **저장소** > **빠른 생성**을 클릭합니다.

	![저장소 계정 빠른 생성](./media/virtual-machines-freebsd-create-upload-vhd/Storage-quick-create.png)

4. 다음과 같이 필드를 채웁니다.

	- **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 이름은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다.

	- 저장소 계정의 **위치 또는 선호도 그룹**을 선택합니다. 선호도 그룹을 사용하면 클라우드 서비스와 저장소를 동일한 데이터 센터에 배치할 수 있습니다.

	- 저장소 계정에 **지역에서 복제**를 사용할지 여부를 결정합니다. 지역에서 복제는 기본적으로 설정되어 있습니다. 이 옵션을 사용하면 추가 비용 없이 보조 위치로 데이터를 복제하므로 기본 위치에서 심각한 장애가 발생하는 경우 저장소에서 보조 위치로 장애 조치(Failover)할 수 있습니다. 보조 위치는 자동으로 할당되며 변경될 수 없습니다. 법적 필요 또는 조직 정책에 따라 클라우드 기반 저장소의 위치를 더 엄격하게 제어해야 하는 경우 지역에서 복제를 해제할 수 있습니다. 그러나 나중에 지역에서 복제를 설정하는 경우 기존 데이터를 대체 위치로 복제하는 데 일회성 데이터 전송 요금이 청구됩니다. 지역에서 복제를 사용하지 않는 저장소 서비스는 할인하여 제공됩니다. 저장소 계정의 지역에서 복제를 관리하는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](../storage-create-storage-account/#replication-options)에서 확인할 수 있습니다.

	![저장소 계정 세부 정보 입력](./media/virtual-machines-freebsd-create-upload-vhd/Storage-create-account.png)


5. **Create Storage Account**를 클릭합니다. 이제 계정이 **저장소** 아래에 나타납니다.

	![저장소 계정 만들기 성공](./media/virtual-machines-freebsd-create-upload-vhd/Storagenewaccount.png)

6. 그런 다음, 업로드된 VHD를 위한 컨테이너를 만듭니다. 저장소 계정 이름을 클릭한 다음 **컨테이너**를 클릭합니다.

	![저장소 계정 세부 정보](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_detail.png)

7. **컨테이너 만들기**를 클릭합니다.

	![저장소 계정 세부 정보](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_container.png)

8. 컨테이너의 **이름**을 입력하고 **액세스** 정책을 선택합니다.

	![컨테이너 이름](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE]기본적으로 컨테이너는 전용이며 계정 소유자만 액세스할 수 있습니다. 컨테이너 속성 및 메타데이터는 제외하고 컨테이너에 있는 Blob에 대한 공용 읽기 권한을 허용하려면 "공용 Blob" 옵션을 사용하세요. 컨테이너 및 Blob에 대한 전체 공용 읽기 권한을 허용하려면 "공용 컨테이너" 옵션을 사용하세요.

## 3단계: Microsoft Azure 연결 준비 ##

.vhd 파일을 업로드하려면 컴퓨터와 Azure의 구독 사이에 보안 연결을 설정해야 합니다. 이를 위해 Microsoft Azure Active Directory 방법이나 인증서 방법을 사용할 수 있습니다.

###Microsoft Azure AD 방법 사용

1. Azure PowerShell 콘솔을 엽니다.

2. 다음 명령을 입력합니다. `Add-AzureAccount`

	이 명령은 직장 또는 학교 계정으로 로그인할 수 있는 로그인 창을 엽니다.

	![PowerShell 창](./media/virtual-machines-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

###인증서 방법 사용

1. Azure PowerShell 콘솔을 엽니다.

2. 다음을 입력합니다. `Get-AzurePublishSettingsFile`

3. .publishsettings 파일을 다운로드할 것을 요구하는 브라우저 창이 열립니다. 여기에는 Microsoft Azure 구독을 위한 정보와 인증서가 포함되어 있습니다.

	![브라우저 다운로드 페이지](./media/virtual-machines-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. .publishsettings 파일을 저장합니다.

4. 다음을 입력합니다. `Import-AzurePublishSettingsFile <PathToFile>`

	여기서 `<PathToFile>`은 .publishsettings 파일의 전체 경로입니다.

   자세한 내용은 [Microsoft Azure Cmdlets 시작](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)(영문)을 참조하세요.

   PowerShell을 설치하고 구성하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)을 참조하세요.

## 4단계: .vhd 파일 업로드 ##

.vhd 파일을 업로드하는 경우 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음 명령 예제에서 **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL이고, **YourImagesFolder**는 이미지를 저장할 Blob 저장소 내 컨테이너입니다. **VHDName**은 가상 하드 디스크를 식별하기 위해 Azure 클래식 포털에 표시되는 레이블입니다. **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다.


1. 이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## 5단계: 업로드한 VHD로 VM 만들기 ##
.vhd를 업로드한 후 구독과 연결된 사용자 지정 이미지 목록에 이미지로 추가하고 이 사용자 지정 이미지를 사용하여 가상 컴퓨터를 만들 수 있습니다.

1. 이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **중요**: 현재 Azure PowerShell 버전에서는 “Linux” 또는 “Windows”만 매개 변수로 사용할 수 있으므로 지금은 Linux를 OS 유형으로 사용하세요.

2. 이전 단계를 완료한 후 Azure 클래식 포털에서 **이미지** 탭을 선택하면 새 이미지가 나열됩니다.

    ![이미지 추가](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. 갤러리에서 가상 컴퓨터를 만듭니다. 이제 **내 이미지**에서 이 새 이미지를 사용할 수 있습니다. 새 이미지를 선택하고 메시지에 따라 호스트 이름, 암호/SSH 키 등을 설정합니다.

	![사용자 지정 이미지](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. 프로비전이 완료되면 FreeBSD VM이 Azure에서 실행됩니다.

	![azure의 freebsd 이미지](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!---HONumber=AcomDC_0114_2016-->