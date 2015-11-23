<properties
   pageTitle="Azure 마켓플레이스를 위해 온-프레미스에 가상 컴퓨터 이미지 만들기 | Microsoft Azure"
   description="VM 이미지 온-프레미스를 만들어 Azure 마켓플레이스에 배포하여 다른 사용자가 구입할 수 있도록 하는 단계를 이해하고 실행합니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="10/08/2015"
  ms.author="hascipio; v-divte"/>

# Azure 마켓플레이스를 위해 온-프레미스에 가상 컴퓨터 이미지 개발
RDP를 사용하여 클라우드에서 직접 Azure VHD를 개발하는 것이 좋습니다. 그러나 꼭 필요한 경우 VHD를 다운로드하고 온-프레미스 인프라를 사용하여 개발할 수 있습니다.

온-프레미스 개발을 위해서는 만든 VM의 OS VHD를 다운로드해야 합니다. 다음 단계는 위의 3.3단계의 일부로 수행됩니다.

## VHD 이미지 다운로드
### Blob URL 찾기
VHD를 다운로드하려면 먼저 OS 디스크에 대한 Blob URL을 찾습니다.

새 [Microsoft Azure 포털](https://ms.portal.azure.com)에서 Blob URL을 찾습니다.

1.	**찾아보기 > VM**으로 이동하고 배포된 VM을 선택합니다.
2.	**구성** 아래에서 **디스크** 타일을 선택하면 디스크 블레이드가 열립니다.

  ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)

3.	**OS 디스크**를 선택합니다. 그러면 VHD 위치를 포함한 디스크 속성을 표시하는 다른 블레이드가 열립니다.
4.	이 Blob URL을 복사합니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)

5.	이제 백업 디스크를 삭제하지 않고 배포된 VM을 삭제합니다. 또한 VM을 삭제하는 대신 중지하도록 선택할 수도 있습니다. VM이 실행 중인 경우 OS VHD를 다운로드하지 마십시오.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### VHD 다운로드
Blob URL을 알고 있는 경우 [Azure 관리 포털](http://manage.windowsazure.com/) 또는 PowerShell을 사용하여 VHD를 다운로드할 수 있습니다.
> [AZURE.NOTE]이 가이드를 작성한 시점에는 VHD를 다운로드하는 기능이 새 Microsoft Azure 포털에 아직 없습니다.

**현재 [Azure 관리 포털](http://manage.windowsazure.com/)을 통해 OS VHD 다운로드**

1.	아직 로그인하지 않은 경우 Azure 관리 포털에 로그인합니다.
2.	**저장소** 탭을 클릭합니다.
3.	VHD가 저장된 **저장소 계정**을 선택합니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)

4.	저장소 계정 속성이 열립니다. 컨테이너 탭을 선택합니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)

5.	VHD가 저장되는 컨테이너를 선택합니다. 기본적으로 포털에서 만들 때 VHD는 vhd 컨테이너에 저장됩니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)

6.	저장된 URL과 비교하여 올바른 OS VHD를 선택합니다.
7.	**다운로드**를 클릭합니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### PowerShell을 사용하여 VHD 다운로드
Azure 관리 포털 외에도 [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) cmdlet을 사용하여 OS VHD를 다운로드할 수 있습니다.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
예: Save-AzureVhd -Source "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" -LocalFilePath "C:\\Users\\Administrator\\Desktop\\baseimagevm.vhd" -StorageKey <String>

> [AZURE.NOTE]*Save-AzureVhd*에는 다운로드에 사용 가능한 대역폭을 가능한 효과적으로 사용할 수 있도록 병렬 처리를 늘리는 데 사용할 수 있는 NumberOfThreads 옵션도 있습니다.

## Azure 저장소 계정에 VHD 업로드
온-프레미스에서 VHD를 준비하는 경우 Azure의 저장소 계정에 업로드해야 합니다. 이 단계는 온-프레미스에서 VHD를 만든 후, VM 이미지에 대한 인증을 가져오기 전에 수행됩니다.

### 저장소 계정 및 컨테이너 만들기
이전에 설명한 것처럼 VHD를 미국의 지역에 있는 저장소 계정에 업로드하는 것이 좋습니다. 단일 SKU에 대한 모든 VHD는 단일 저장소 계정 내에서 단일 컨테이너에 배치되어야 합니다.

저장소 계정을 만들려면 [Microsoft Azure 포털](https://portal.azure.com/), PowerShell 또는 Linux 명령줄 도구를 사용할 수 있습니다.

**Microsoft Azure 포털에서 저장소 계정 만들기**

1.	**새로 만들기**를 클릭합니다.
2.	**저장소**를 선택합니다.
3.	저장소 계정 이름을 입력하고 위치를 선택합니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)

4.	**만들기**를 클릭합니다.
5.	만든 저장소 계정에 대한 블레이드가 열립니다. 그렇지 않은 경우 **찾아보기 > 저장소 계정**을 선택합니다. **저장소 계정** 블레이드에서 만든 저장소 계정을 선택합니다.
6.	**컨테이너**를 선택합니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img09.png)

7.	**컨테이너** 블레이드에서 **추가**를 선택하고 컨테이너 이름 및 컨테이너 권한을 입력합니다. 컨테이너 권한에 대해 **개인**을 선택합니다.

> [AZURE.TIP]게시를 계획 중인 SKU당 하나의 컨테이너를 만드는 것이 좋습니다.

  ![그리기](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### PowerShell을 사용하여 저장소 계정 만들기
PowerShell을 사용하여 [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) cmdlet으로 저장소 계정을 만듭니다.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

그런 다음 [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) cmdlet을 사용하여 해당 저장소 계정 내에 컨테이너를 만들 수 있습니다.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [AZURE.NOTE]이러한 명령은 현재 저장소 계정 컨텍스트가 PowerShell에 이미 설정되어 있다고 가정합니다. PowerShell 설정에 대한 자세한 내용은 [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)을 참조하세요.
### Mac 및 Linux용 명령줄 도구를 사용하여 저장소 계정 만들기
[Linux 명령줄 도구](../virtual-machines/command-line-tools/)에서 다음과 같이 저장소 계정을 만듭니다.

        azure storage account create mystorageaccount --location "West US"

다음과 같이 컨테이너를 만듭니다.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## VHD 업로드
저장소 계정 및 컨테이너를 만든 후에는 준비된 VHD를 업로드할 수 있습니다. PowerShell, Linux 명령줄 도구 또는 기타 Azure 저장소 관리 도구를 사용할 수 있습니다.

### PowerShell 통해 VHD 업로드
[Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) cmdlet을 사용합니다.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### Mac 및 Linux용 명령줄 도구를 사용하여 VHD 업로드
[Linux 명령줄 도구](../virtual-machines/command-line-tools/)에서 azure vm image create <image name> --location <Location of the data center> --OS Linux<LocationOfLocalVHD>를 사용합니다.

## 참고 항목
- [마켓플레이스에 대한 가상 컴퓨터 이미지 만들기](marketplace-publishing-vm-image-creation.md)
- [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)

<!---HONumber=Nov15_HO3-->