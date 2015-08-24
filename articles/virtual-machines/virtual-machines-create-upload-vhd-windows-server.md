<properties
	pageTitle="Windows Server VHD를 만들어서 Azure에 업로드"
	description="Windows Server 운영 체제가 포함된 VHD(가상 하드 디스크)를 Azure에서 만들고 업로드하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="kathydav"/>


# Windows Server VHD를 만들어서 Azure에 업로드#

이 문서에서는 운영 체제가 설치된 VHD(가상 하드 디스크)를 업로드하여 이미지로 사용하고 해당 이미지를 기반으로 가상 컴퓨터를 만드는 방법을 보여 줍니다. Microsoft Azure의 디스크 및 VHD에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD에 대하여](virtual-machines-disks-vhds.md)를 참조하세요.

> [AZURE.NOTE]이미지를 기반으로 가상 컴퓨터를 만드는 경우 가상 컴퓨터에서 실행할 응용 프로그램에 적절하게 운영 체제 설정을 사용자 지정할 수 있습니다. 이 구성은 해당 가상 컴퓨터에 대해 저장되며, 이미지에 영향을 주지 않습니다.

## 필수 조건##

이 문서에서는 사용자에게 다음 항목이 있는 것으로 가정합니다.

1. **Azure 구독** - 없는 경우 [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있음: 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다. 또한 [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음: MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

2. **Microsoft Azure PowerShell** - Microsoft Azure PowerShell 모듈이 설치되고 구독을 사용하도록 구성되어 있어야 합니다. 모듈을 다운로드하려면 [Microsoft Azure 다운로드](http://azure.microsoft.com/downloads/)(영문)를 참조하십시오. 모듈 설치 및 구성에 대한 자습서는 [여기](../powershell-install-configure.md)에서 확인할 수 있습니다. [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet을 사용하여 VHD를 업로드합니다.

3. **.vhd 파일에 저장된 지원되는 Windows 운영 체제** - 지원되는 Windows Server 운영 체제를 가상 하드 디스크에 설치했습니다. .vhd 파일을 만드는 도구는 여러 가지가 있습니다. Hyper-V와 같은 가상화 솔루션을 사용하여 가상 컴퓨터를 만들고 운영 체제를 설치할 수 있습니다. 자세한 내용은 [Hyper-V 역할 설치 및 가상 시스템 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오.

> [AZURE.NOTE]VHDX 형식은 Microsoft Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 [Convert-VHD cmdlet](http://technet.microsoft.com/library/hh848454.aspx)을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. 이에 대한 자습서는 [여기](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)에서 찾을 수 있습니다.

 다음 Windows Server 버전이 지원됩니다.

OS|SKU|서비스 팩|아키텍처
---|---|---|---
Windows Server 2012 R2|모든 버전|해당 없음|x64
Windows Server 2012|모든 버전|해당 없음|x64
Windows Server 2008 R2|모든 버전|SP1|x64

## 1단계: 업로드할 이미지 준비 ##

Azure에 이미지를 업로드하려면, Sysprep 명령을 사용하여 일반화해야 합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

운영 체제를 설치한 가상 컴퓨터에서 다음 절차를 완료합니다.

1. 운영 체제에 로그인합니다.

2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\\system32\\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.

	![명령 프롬프트 창 열기](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	**시스템 준비 도구** 대화 상자가 나타납니다.

	![Sysprep 시작](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  **시스템 준비 도구**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다.

5.  **종료 옵션**에서 **종료**를 선택합니다.

6.  **확인**을 클릭합니다.

## 2단계: Azure에서 저장소 계정 만들기 ##

Azure에서 가상 컴퓨터를 만드는 데 사용할 수 있도록 .vhd 파일을 업로드하려면 Azure에 저장소 계정이 있어야 합니다. Azure 포털을 사용하여 저장소 계정을 만들 수 있습니다.

1. [포털](http://manage.windowsazure.com)에 로그인합니다.

2. 명령 모음에서 **New**를 클릭합니다.

3. **데이터 서비스** > **저장소** > **빠른 생성**을 클릭합니다.

	![저장소 계정 빠른 생성](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. 다음과 같이 필드를 채웁니다.

	- **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 이름은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다.

	- 저장소 계정의 **위치 또는 선호도 그룹**을 선택합니다. 선호도 그룹을 사용하면 클라우드 서비스와 저장소를 동일한 데이터 센터에 배치할 수 있습니다.

	- 저장소 계정에 **지역에서 복제**를 사용할지 여부를 결정합니다. 지역에서 복제는 기본적으로 설정되어 있습니다. 이 옵션을 사용하면 추가 비용 없이 보조 위치로 데이터를 복제하므로 기본 위치에서 심각한 장애가 발생하는 경우 저장소에서 보조 위치로 장애 조치(Failover)할 수 있습니다. 보조 위치는 자동으로 할당되며 변경될 수 없습니다. 법적 필요 또는 조직 정책에 따라 클라우드 기반 저장소의 위치를 더 엄격하게 제어해야 하는 경우 지역에서 복제를 해제할 수 있습니다. 그러나 나중에 지역에서 복제를 설정하는 경우 기존 데이터를 대체 위치로 복제하는 데 일회성 데이터 전송 요금이 청구됩니다. 지역에서 복제를 사용하지 않는 저장소 서비스는 할인하여 제공됩니다. 저장소 계정의 지역에서 복제를 관리하는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](../storage-create-storage-account/#replication-options)에서 확인할 수 있습니다.

	![저장소 계정 세부 정보 입력](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. **Create Storage Account**를 클릭합니다. 이제 계정이 **저장소** 아래에 나타납니다.

	![저장소 계정 만들기 성공](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. 그런 다음, 업로드된 VHD를 위한 컨테이너를 만듭니다. 저장소 계정 이름을 클릭한 다음 **컨테이너**를 클릭합니다.

	![저장소 계정 세부 정보](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. **컨테이너 만들기**를 클릭합니다.

	![저장소 계정 세부 정보](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. 컨테이너의 **이름**을 입력하고 **액세스** 정책을 선택합니다.

	![컨테이너 이름](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]기본적으로 컨테이너는 전용이며 계정 소유자만 액세스할 수 있습니다. 컨테이너 속성 및 메타데이터는 제외하고 컨테이너에 있는 Blob에 대한 공용 읽기 권한을 허용하려면 "공용 Blob" 옵션을 사용하십시오. 컨테이너 및 Blob에 대한 전체 공용 읽기 권한을 허용하려면 "공용 컨테이너" 옵션을 사용하십시오.

## 3단계: Microsoft Azure 연결 준비 ##

.vhd 파일을 업로드하려면 컴퓨터와 Azure의 구독 사이에 보안 연결을 설정해야 합니다. 이를 위해 Microsoft Azure Active Directory 방법이나 인증서 방법을 사용할 수 있습니다.

> [AZURE.TIP]Azure PowerShell로 시작하려면 [Microsoft Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)을 참조하세요. 자세한 내용은 [Microsoft Azure Cmdlets 시작](https://msdn.microsoft.com/library/azure/jj554332.aspx)을 참조하세요.

### Microsoft Azure AD 방법 사용

1. Azure PowerShell 콘솔을 엽니다.

2. 다음을 입력합니다. `Add-AzureAccount`

	이 명령은 회사 또는 학교 계정으로 로그인할 수 있는 로그인 창을 엽니다.

	![PowerShell 창](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

### 인증서 방법 사용

1. Azure PowerShell 콘솔을 엽니다.

2.	다음을 입력합니다. `Get-AzurePublishSettingsFile`

3. .publishsettings 파일을 다운로드할 것을 요구하는 브라우저 창이 열립니다. 여기에는 Microsoft Azure 구독을 위한 정보와 인증서가 포함되어 있습니다.

	![브라우저 다운로드 페이지](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. .publishsettings 파일을 저장합니다.

4. 다음을 입력합니다. `Import-AzurePublishSettingsFile <PathToFile>`

	여기서 `<PathToFile>`은 .publishsettings 파일의 전체 경로입니다.

## 4단계: .vhd 파일 업로드

.vhd 파일을 업로드하는 경우 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음 명령 예제에서 **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL이고, **YourImagesFolder**는 이미지를 저장할 Blob 저장소 내 컨테이너입니다. **VHDName**은 가상 하드 디스크를 식별하기 위해 관리 포털에 표시되는 레이블입니다. **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다.


1. 이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Add-AzureVhd cmdlet에 대한 자세한 내용은 [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx)(영문)를 참조하십시오.

## 5단계: 사용자 지정 이미지 목록에 이미지 추가 ##

.vhd를 업로드한 후 구독과 연결된 사용자 지정 이미지 목록에 이미지로 추가합니다.

1. 포털의 **모든 항목**에서 **가상 컴퓨터**를 클릭합니다.

2. 가상 컴퓨터에서 **이미지**를 클릭합니다.

3. **이미지 만들기**를 클릭합니다.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. **VHD에서 이미지 만들기**에서 다음을 수행합니다.

	- **이름**을 지정합니다.
	- **설명**을 지정합니다.
	- **VHD의 URL**을 지정하려면 폴더 단추를 클릭하여 다음 창을 엽니다.

	![VHD 선택](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- VHD가 들어 있는 저장소 계정을 선택하고 **열기**를 클릭합니다. 그러면 **VHD에서 이미지 만들기** 창으로 돌아갑니다.
	- **VHD에서 이미지 만들기** 창으로 돌아간 후에는 운영 체제 제품군을 선택합니다.
	- **이 VHD에 연결된 가상 컴퓨터에서 Sysprep를 실행했습니다.**를 선택하여 1단계에서 운영 체제를 범용화했음을 확인한 후 **확인**을 클릭합니다.

	![이미지 추가](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **선택 사항:** 포털 대신 Add-AzureVMImage cmdlet을 사용하여 VHD를 이미지로 추가할 수 있습니다. Azure PowerShell 콘솔에서 다음을 입력합니다.

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. 이전 단계를 완료한 후에는 **이미지** 탭을 선택할 때 새 이미지가 나열됩니다.


	![사용자 지정 이미지](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	이제 가상 컴퓨터를 만들 때 **내 이미지**에서 이 새 이미지를 사용할 수 있습니다. 자세한 내용은 [Windows를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법](virtual-machines-windows-create-custom.md)을 참조하세요.

	![사용자 지정 이미지에서 VM 만들기](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]VM을 만드려고 할 때 "VHD https://XXXXX..의 YYYY 바이트는 지원되지 않는 가상 크기입니다. 크기는 정수(MB)여야 합니다."라는 오류 메시지가 표시되는 경우 이는 VHD가 정수 MB가 아니며 고정 크기 VHD여야 함을 의미합니다. 관리 포털 대신 Add-AzureVMImage PowerShell cmdlet을 사용하여 이미지를 추가해 보세요(위 5단계 참조). Azure cmdlet을 사용하면 VHD가 Azure 요구 사항을 충족합니다.

## 다음 단계 ##

가상 컴퓨터를 만든 후에 SQL Server 가상 컴퓨터를 만들어 보십시오. 지침은 [Microsoft Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)(영문)을 참조하십시오.

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=August15_HO7-->