<properties 
	pageTitle="Azure에서 Linux VHD 만들기 및 업로드" 
	description="Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드

이 문서에서는 VHD(가상 하드 디스크)를 생성 및 업로드하고 이를 Azure에서 가상 컴퓨터를 만들기 위한 고유한 이미지로 사용하는 방법을 소개합니다. 또한 이 이미지를 기반으로 여러 개의 가상 컴퓨터를 만들 수 있도록 운영 체제를 준비하는 방법을 살펴봅니다.  

> [AZURE.NOTE] 이전에 Azure VM을 사용한 경험이 없어도 이 문서의 단계를 완료할 수 있습니다. 그러나 Azure 계정이 필요합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 계정 만들기](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/)(영문)를 참조하세요. 

Azure의 가상 컴퓨터는 가상 컴퓨터를 만들 때 선택한 이미지를 기반으로 하는 운영 체제를 실행합니다. 이미지는 VHD 형식인 .vhd 파일로 저장소 계정에 저장됩니다. Azure의 디스크 및 이미지에 대한 자세한 내용은 [디스크 및 이미지 관리](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx)를 참조하세요.

가상 컴퓨터를 만드는 경우 실행하려는 응용 프로그램에 적합하도록 일부 운영 체제 설정을 사용자 지정할 수 있습니다. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법](/ko-kr/manage/windows/how-to-guides/custom-create-a-vm/)을 참조하세요.

**중요**: [이 문서](http://support.microsoft.com/kb/2805216)에 지정된 대로 보증 배포판 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux OS를 실행하는 가상 컴퓨터에 Azure 플랫폼 SLA가 적용됩니다. Azure 이미지 갤러리에 제공된 모든 Linux 배포는 필요한 구성이 포함된 보증 배포판입니다.


##필수 조건##
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

- **관리 인증서** - VHD를 업로드할 구독에 필요한 관리 인증서를 만들어 .cer 파일로 내보냈습니다. 인증서 만들기에 대한 자세한 내용은 [Azure용 관리 인증서 만들기](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)를 참조하세요. 

- **.vhd 파일에 설치된 Linux 운영 체제**   - 지원되는 Linux 운영 체제가 가상 하드 디스크에 설치되어 있습니다. 다양한 도구를 사용하여 .vhd 파일을 만들 수 있습니다. 예를 들어 Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. 자세한 내용은 [Hyper-V 역할 설치 및 가상 컴퓨터 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하세요. 

	**중요**: 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

	보증 배포판 목록은 [Azure의 Linux-보증 배포판](../linux-endorsed-distributions.md)을 참조하세요. 또한 [보증되지 않는 배포](virtual-machines-linux-create-upload-vhd-generic.md)에 대한 정보는 이 문서의 끝에 있는 섹션을 참조하세요.

- **Linux Azure 명령줄 도구** - Linux 운영 체제를 사용하여 이미지를 만드는 경우에는 [Linux 및 Mac용 Azure 명령줄 도구](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409)를 사용하여 VHD를 업로드합니다.

- **Azure Powershell 도구** - `Add-AzureVhd` cmdlet을 사용하여 VHD를 업로드할 수도 있습니다. Azure PowerShell cmdlet을 다운로드하려면 [Azure 다운로드](http://azure.microsoft.com/downloads/)를 방문하세요. 참조 정보는 [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx)를 참조하세요.


이 작업에는 다음 단계가 포함됩니다.

- [1단계: 업로드할 이미지 준비] []
- [2단계: Azure에서 저장소 계정 만들기] []
- [3단계: Azure 연결 준비] []
- [4단계: Azure에 이미지 업로드] []

## <a id="prepimage"> </a>1단계: 업로드할 이미지 준비 ##

Microsoft Azure에서는 다양한 Linux 배포를 지원합니다([보증 배포판](../linux-endorsed-distributions.md) 참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법을 안내합니다.

- **[CentOS 기반 배포](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES 및 openSUSE](virtual-machines-linux-create-upload-vhd-suse.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[기타 - 보증되지 않는 배포](virtual-machines-linux-create-upload-vhd-generic.md)**

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 추가 팁은 **[Linux 설치 참고 사항](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)**을 참조하세요.

위 가이드의 단계를 수행하면 Azure로 업로드할 수 있는 VHD 파일을 만들 수 있습니다.


## <a id="createstorage"> </a>2단계: Azure에서 저장소 계정 만들기 ##

저장소 계정은 저장소 서비스에 액세스하는 데 필요한 가장 높은 수준의 네임스페이스를 나타내며 Azure 구독과 관련이 있습니다. 가상 컴퓨터를 만드는 데 사용할 수 있는 .vhd 파일을 Azure에 업로드하려면 Azure에 저장소 계정이 있어야 합니다. Azure 관리 포털을 사용하여 저장소 계정을 만들 수 있습니다.

1. Azure 관리 포털에 로그인합니다.

2. 명령 모음에서 **새로 만들기**를 클릭합니다.

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. **저장소 계정**을 클릭한 후 **빠른 생성**을 클릭합니다.

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. 다음과 같이 필드를 채웁니다.

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 이름은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다.
	
- 저장소 계정의 위치 또는 선호도 그룹을 선택합니다. 선호도 그룹을 지정하여 저장소와 동일한 데이터 센터에 클라우드 서비스를 배치할 수 있습니다.
 
- 저장소 계정에 지역에서 복제를 사용할지 여부를 결정합니다. 지역에서 복제는 기본적으로 설정되어 있습니다. 이 옵션을 사용하면 추가 비용 없이 보조 위치로 데이터를 복제하므로 기본 위치에서 처리할 수 없는 심각한 장애가 발생하는 경우 저장소에서 보조 위치로 장애 조치(Failover)합니다. 보조 위치는 자동으로 할당되며 변경될 수 없습니다. 법적 필요 또는 조직 정책에 따라 클라우드 기반 저장소의 위치를 더 엄격하게 제어해야 하는 경우 지역에서 복제를 해제할 수 있습니다. 그러나 나중에 지역에서 복제를 설정하는 경우 기존 데이터를 대체 위치로 복제하는 데 일회성 데이터 전송 요금이 청구됩니다. 지역에서 복제를 사용하지 않는 저장소 서비스는 할인하여 제공됩니다.

5. **저장소 계정 만들기**를 클릭합니다.

	이제 계정이 **저장소 계정**에 나열되어 있습니다.

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>3단계: Azure 연결 준비 ##

.vhd 파일을 업로드하려면 컴퓨터와 Azure의 구독 사이에 보안 연결을 설정해야 합니다. 

1. Azure PowerShell 창을 엽니다.

2. 다음을 입력합니다. 

	`Get-AzurePublishSettingsFile`

	이 명령은 브라우저 창을 열고 Azure 구독에 대한 정보와 인증서가 포함된 .publishsettings 파일을 자동으로 다운로드합니다. 

3. .publishsettings 파일을 저장합니다. 

4. 다음을 입력합니다.

	`Import-AzurePublishSettingsFile <PathToFile>`

	여기서 `<PathToFile>`은 .publishsettings 파일의 전체 경로입니다. 

	자세한 내용은 [Azure Cmdlets 시작](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)을 참조하세요. 


## <a id="upload"> </a>4단계: Azure에 이미지 업로드 ##

.vhd 파일을 업로드하는 경우 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음 명령 예제에서 **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL이고, **YourImagesFolder**는 이미지를 저장할 Blob 저장소 내 컨테이너입니다. **VHDName**은 가상 하드 디스크를 식별하기 위해 관리 포털에 표시되는 레이블입니다. **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다. 

다음 중 하나를 수행합니다.

- 이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	자세한 내용은 [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx)를 참조하세요.

- Linux 명령줄 도구를 사용하여 이미지를 업로드합니다. 다음 명령을 사용하여 이미지를 업로드할 수 있습니다.

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[1단계: 업로드할 이미지 준비]: #prepimage
[2단계: Azure에서 저장소 계정 만들기]: #createstorage
[3단계: Azure 연결 준비]: #connect
[4단계: Azure에 이미지 업로드]: #upload


<!--HONumber=45--> 
 