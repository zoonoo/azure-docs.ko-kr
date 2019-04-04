---
title: Azure에서 marketplace 항목 다운로드 | Microsoft Docs
description: 클라우드 운영자 Azure Stack 배포 내에 Azure에서 마켓플레이스 항목을 다운로드할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 9bf261918bdbdf3ee06ad28a037d5bb8a3631a20
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487637"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure에서 Azure Stack marketplace 항목 다운로드

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

클라우드 운영자로 서 Azure Marketplace에서 항목을 다운로드할 수 있으며 Azure Stack에서 사용할 수 있도록 키를 누릅니다. 사전 테스트 및 Azure Stack과 함께 작동 하는 지원 되는 Azure Marketplace 항목의 엄선된 된 목록에서 항목이 선택할 수 있습니다. 따라서 새로운 내용을 확인 하려면 계속, 추가 항목은 자주이 목록에 추가 됩니다. 

Azure Marketplace에 연결 하기 위한 두 가지 시나리오가 있습니다. 

- **연결 된 경우** -인터넷에 연결할 Azure Stack 환경과 필요 합니다. Azure Stack 포털을 사용 하 여를 찾아서 항목을 다운로드 합니다. 
- **연결 되어 있지 않거나 부분적으로 연결 된 경우** -마켓플레이스 배포 도구를 사용 하 여 마켓플레이스 항목을 다운로드 하려면 인터넷에 액세스 해야 합니다. 그런 다음 연결이 끊긴 Azure Stack 설치 다운로드를 전송합니다. 이 시나리오에서는 PowerShell을 사용 합니다.

참조 [Azure Stack에 대 한 Azure Marketplace 항목](azure-stack-marketplace-azure-items.md) 목록은 마켓플레이스 항목을 다운로드할 수 있습니다.


## <a name="connected-scenario"></a>연결 된 시나리오
Azure Stack에 인터넷에 연결 된 경우에 마켓플레이스 항목을 다운로드 하려면 관리 포털을 사용할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
Azure Stack 배포 인터넷 연결을가지고 있어야 [Azure에 등록 된](azure-stack-register.md)합니다.

### <a name="use-the-portal-to-download-marketplace-items"></a>포털을 사용 하 여 마켓플레이스 항목 다운로드  
1. Azure Stack 관리자 포털에 로그인 합니다.

2.  Marketplace 항목 다운로드 하기 전에 사용 가능한 저장소 공간을 검토 합니다. 나중에 다운로드에 대 한 항목을 선택 하면 사용 가능한 저장소 용량을 다운로드 크기를 비교할 수 있습니다. 용량이 제한 된 경우에 대 한 옵션을 고려 [사용 가능한 공간을 관리](azure-stack-manage-storage-shares.md#manage-available-space)합니다. 

    사용 가능한 공간을 검토 **하위 지역 관리** 탐색 하 고 이동 하려는 지역을 선택 **리소스 공급자** > **저장소**합니다.

    [![저장소 공간을 검토](media/azure-stack-download-azure-marketplace-item/storagesm.png "저장소 공간을 검토")](media/azure-stack-download-azure-marketplace-item/storage.png#lightbox)

    
3. Azure Stack Marketplace를 열고 Azure에 연결 합니다. 이 위해 선택 **Marketplace management**를 선택한 후 **Azure에서 추가**합니다.

    [![Azure에서 추가](media/azure-stack-download-azure-marketplace-item/marketplacesm.png "Azure에서 추가")](media/azure-stack-download-azure-marketplace-item/marketplace.png#lightbox)

    포털에는 Azure Marketplace에서 다운로드할 수 있는 항목 목록을 표시합니다. 다운로드 크기를 포함 하 여,에 대 한 추가 정보 및 해당 설명을 보려면 각 항목을 클릭할 수 있습니다. 

    [![Marketplace 목록](media/azure-stack-download-azure-marketplace-item/image03sm.png "Marketplace 목록")](media/azure-stack-download-azure-marketplace-item/image03.png#lightbox)

4. 항목을 선택한 다음 선택 **다운로드**합니다. 다운로드 시간 달라 집니다.

    [![메시지를 다운로드](media/azure-stack-download-azure-marketplace-item/image04.png "다운로드 메시지")](media/azure-stack-download-azure-marketplace-item/image04.png#lightbox)

    다운로드가 완료 되 면 Azure Stack 연산자 또는 사용자로 새 마켓플레이스 항목을 배포할 수 있습니다.

5. 다운로드 한 항목을 배포 하려면 선택 **+ 리소스 만들기**, 한 다음 새 마켓플레이스 항목에 대 한 범주 중에서 검색 합니다. 다음 배포 프로세스를 시작 하려면 항목을 선택 합니다. 다른 marketplace 항목에 대 한 프로세스 달라 집니다. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>연결 끊김 또는 부분적으로 연결 된 경우

PowerShell을 사용 하 여 Azure Stack 인터넷에 연결 하지 않고 오프 라인된 모드의 경우와 *마켓플레이스 배포 도구* 인터넷에 연결 된 컴퓨터에 마켓플레이스 항목을 다운로드 하려면. 그런 다음 Azure Stack 환경에 항목을 전송 합니다. 연결이 끊어진된 환경에서 Azure Stack 포털을 사용 하 여 마켓플레이스 항목을 다운로드할 수 없습니다. 

마켓플레이스 배포 도구를 연결 된 경우에도 사용할 수 있습니다. 

이 시나리오는 두 부분으로 분류됩니다.
- **1 부:** Azure Marketplace에서 다운로드 합니다. PowerShell 구성 인터넷에 연결 된 컴퓨터의 배포 도구 다운로드 및 다음 Azure Marketplace 항목 폼을 다운로드 합니다.  
- **2 부:** 업로드 하 고 Azure Stack Marketplace에 게시 합니다. Azure Stack에 가져와야 하 고 Azure Stack Marketplace에 게시할 Azure Stack 환경에 다운로드 한 파일을 이동 합니다.  


### <a name="prerequisites"></a>필수 조건
- Azure Stack 배포 해야 [Azure에 등록 된](azure-stack-register.md)합니다.  

- 인터넷에 연결 된 컴퓨터에 있어야 **Azure Stack PowerShell 모듈 버전 1.2.11** 이상. 아직 없는 경우 [Azure Stack 특정 PowerShell 모듈을 설치](azure-stack-powershell-install.md)합니다.  

- 다운로드 한 마켓플레이스 항목을 가져오는 사용 하도록 설정 합니다 [Azure Stack 운영자에 대 한 PowerShell 환경](azure-stack-powershell-configure-admin.md) 구성 해야 합니다.  

- 해야 합니다는 [저장소 계정](azure-stack-manage-storage-accounts.md) (즉, storage blob) 컨테이너를 공개적으로 액세스할 수 있는 Azure Stack에서. Marketplace 항목 갤러리 파일에 대 한 임시 저장소와 컨테이너를 사용 합니다. 저장소 계정 및 컨테이너를 사용 하 여 잘 모르는 경우 [blob 사용-Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Azure 설명서에서.

- 마켓플레이스 배포 도구는 첫 번째 절차 중에 다운로드 됩니다. 

- 설치할 수 있습니다 [AzCopy](../storage/common/storage-use-azcopy.md) 최적의 다운로드 성능은 되지만이 필요 하지 않습니다.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>마켓플레이스 배포 도구를 사용 하 여 marketplace 항목 다운로드

1. 인터넷에 연결 된 컴퓨터에서 관리자 권한으로 PowerShell 콘솔을 엽니다.

2. Azure Stack 등록을 사용한 Azure 계정을 추가 합니다. Powershell 실행 계정에 추가할 `Add-AzureRmAccount` 매개 변수 없이 합니다. Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다 하 고 계정 구성에 따라 2 단계 인증을 사용 해야 할 수 있습니다.

3. 여러 구독이 있는 경우 등록에 사용한 것을 선택 하려면 다음 명령을 실행 합니다.  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 다음 스크립트를 사용 하 여 마켓플레이스 배포 도구의 최신 버전을 다운로드 합니다.  

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. 배포 모듈을 가져올 하 고 후 다음 명령을 실행 하 여 도구를 시작 합니다. 대체 `Destination folder path` Azure Marketplace에서 다운로드 한 파일을 저장할 위치를 사용 하 여 합니다.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

6. 도구를 실행 하는 경우 다음 이미지에서는 사용할 수 있는 marketplace 항목 목록 사용 하 여 비슷한 화면이 표시 됩니다.

   [![Azure Marketplace 항목 팝업](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace 항목")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. 다운로드 하 고 기록 하려는 항목을 선택 합니다 *버전*합니다. 보유할 수는 *Ctrl* 키를 여러 이미지를 선택 합니다. 참조 된 *버전* 다음 절차에서 항목을 가져올 때입니다. 
   
   사용 하 여 이미지 목록을 필터링 할 수도 있습니다는 **조건 추가** 옵션입니다.

8. 선택 **확인**, 한 다음 확인 하 고 약관에 동의 합니다. 

9. 다운로드 하는 경우 항목의 크기에 따라 달라 집니다. 다운로드가 완료 되 면 항목을 스크립트에 지정 된 폴더에서 제공 됩니다. 다운로드는 VHD 파일 (가상 머신) 또는 (가상 머신 확장)에 대 한.zip 파일을 포함합니다. 갤러리 패키지를 포함할 수도 있습니다는 *.azpkg* 단순한.zip 파일 형식입니다.

10. 다운로드가 실패 하는 경우 다음 PowerShell cmdlet을 다시 실행 하 여 다시 시도할 수 있습니다.

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes”
    ```

    다시 시도 하기 전에 다운로드 하지 못했습니다 제품 폴더를 제거 합니다. 예를 들어 다운로드 스크립트를 다운로드 하는 경우 실패 `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`를 제거 합니다 `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` 폴더에서 다음 cmdlet 다시 실행 합니다.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>다운로드를 가져오고 (1811 이상) Azure Stack Marketplace에 게시

1. 해야 하는 파일을 이동 해야 [이전에 다운로드 한](#use-the-marketplace-syndication-tool-to-download-marketplace-items) 로컬로 되도록 Azure Stack 환경에 사용할 수 있습니다. 마켓플레이스 배포 도구를 도구를 사용 하 여 가져오기 작업을 수행 해야 하기 때문에 Azure Stack 환경에 사용할 수 있는 수도 있어야 합니다.

   다음 이미지에는 폴더 구조 예제를 보여 줍니다. `D:\downloadfolder` 모든 다운로드 한 마켓플레이스 항목을 포함합니다. 각 하위 폴더는 marketplace 항목 (예를 들어 `microsoft.custom-script-linux-arm-2.0.3`) 명명 된 제품 id 각 하위 폴더 내에서 marketplace 항목의 다운로드 한 콘텐츠가입니다.

   [![Marketplace 다운로드 디렉터리 구조](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace 다운로드 디렉터리 구조")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. 지침을 따릅니다 [이 문서에서는](azure-stack-powershell-configure-admin.md) Azure Stack 연산자 PowerShell 세션을 구성 합니다. 

3. 배포 모듈을 가져올 다음 다음 스크립트를 실행 하 여 마켓플레이스 배포 도구를 시작 합니다.

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   합니다 `-origin` 매개 변수 다운로드 한 제품; 모두 포함 하는 최상위 수준 폴더를 지정 합니다. 예를 들어 `"D:\downloadfolder"`합니다.

   `-AzsCredential` 매개 변수는 선택 사항입니다. 만료 된 경우 액세스 토큰을 갱신에 사용 됩니다. 경우는 `-AzsCredential` 매개 변수가 지정 되지 토큰이 만료 되 고 연산자 자격 증명을 입력 하 라는 메시지가 나타납니다.

    > [!Note]  
    > AD FS 사용자 id 사용 하 여 대화형 인증만을 지원 합니다. 자격 증명 개체를 필요한 경우 서비스 주체 (SPN)을 사용 해야 합니다. Azure Stack 및 AD FS를 사용 하 여 서비스 주체에 id 관리 서비스 설정에 대 한 자세한 내용은 참조 하세요. [AD FS에 대 한 관리 서비스 주체](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)합니다.

4. 스크립트를 성공적으로 완료 된 후 항목 사용할지 Azure Stack Marketplace에서.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>다운로드를 가져오고 (1809 및 낮은) Azure Stack Marketplace에 게시

1. 가상 머신 이미지에 있는 솔루션 템플릿 파일 [이전에 다운로드 한](#use-the-marketplace-syndication-tool-to-download-marketplace-items) Azure Stack 환경에 로컬로 사용할 수 있어야 합니다.  

2. Marketplace 항목 패키지 (.azpkg 파일) 및 가상 하드 디스크 이미지 (.vhd 파일) Azure Stack Blob storage에 업로드 하는 관리 포털을 사용 합니다. 패키지의 업로드 하 고 디스크 파일 사용할 수 있게 Azure Stack에 Azure Stack Marketplace에 항목을 게시할 수 있도록 합니다.

   업로드 해야 공개적으로 액세스 가능한 컨테이너를 사용 하 여 저장소 계정 (이 시나리오에 대 한 필수 조건 참조).  
   1. Azure Stack 관리 포털에서로 이동 **모든 서비스** 한 다음는 **데이터 + 저장소** 범주를 선택한 **저장소 계정**합니다.  
   
   2. 선택한 저장소 계정 구독에서 다음 아래 **BLOB SERVICE**를 선택 **컨테이너**합니다.  
      [![Blob 서비스](media/azure-stack-download-azure-marketplace-item/blob-service.png "Blob service")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. 사용 하 여 선택한 컨테이너를 선택 **업로드** 열려는 합니다 **blob 업로드** 창입니다.  
      [![컨테이너](media/azure-stack-download-azure-marketplace-item/container.png "컨테이너")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. 패키지 및 디스크 파일을 저장소로 로드 하 고 선택한 업로드 blob 창에서 이동할 **업로드**: [![업로드할](media/azure-stack-download-azure-marketplace-item/uploadsm.png "업로드")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. 업로드 된 파일 컨테이너 창에 나타납니다. 파일을 선택 하 고 다음에서 URL을 복사 합니다 **Blob 속성** 창입니다. Azure Stack에 마켓플레이스 항목을 가져올 때 다음 단계에서이 URL을 사용 합니다.  다음 이미지는 컨테이너는 *blob-테스트-저장소* 있고 파일이 *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*합니다.  파일 URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*합니다.  
      [![Blob 속성](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Blob 속성")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. VHD 이미지를 사용 하 여 Azure Stack 가져올는 **추가 AzsPlatformimage** cmdlet. 이 cmdlet을 사용 하는 경우 대체는 *게시자*, *제공*, 및가 가져오는 이미지의 값을 사용 하 여 다른 매개 변수 값입니다. 

   가져올 수 있습니다 합니다 *게시자*를 *제공*, 및 *sku* AZPKG 파일을 다운로드 하는 텍스트 파일에서 이미지의 값입니다. 텍스트 파일의 대상 위치에 저장 됩니다. 합니다 *버전* 값은 Azure에서 이전 절차에서 항목을 다운로드 하는 경우 명시 버전입니다. 
 
   다음 예제 스크립트에서는 서버 코어 가상 머신에서 Windows Server 2016 Datacenter-에 대 한 값이 사용 됩니다. 에 대 한 값 *-Osuri* 항목에 대 한 blob 저장소 위치를 예제 경로입니다.

   이 스크립트를 대신 사용할 수 있습니다 합니다 [이 문서에 설명 된 절차](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) 가져오려는 합니다. Azure portal을 사용 하 여 VHD 이미지입니다.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **솔루션 템플릿에 대 한:** 일부 템플릿에서 작은 3MB를 포함할 수 있습니다. VHD 파일 이름의 **fixed3.vhd**합니다. Azure Stack에 해당 파일을 가져올 필요가 없습니다. Fixed3.vhd.  이 파일은 Azure Marketplace에 대 한 게시 요구 사항에 맞게 일부 솔루션 템플릿을 사용 하 여 포함 합니다.

   템플릿 설명을 검토 하 고 다운로드 가져와서 솔루션 템플릿을 사용 하 여 작동 하는 데 필요한 Vhd와 같은 추가 요구 사항입니다.  
   
   **확장에 대 한:** 가상 머신 이미지 확장을 사용 하 여 작업할 때 다음 매개 변수를 사용 합니다.
   - *게시자*
   - *형식*
   - *버전*  

   사용 하지 않는 *제공* 확장에 대 한 합니다.   


4.  PowerShell을 사용 하 여 사용 하 여 Azure Stack의 마켓플레이스 항목 게시 하는 **추가 AzsGalleryItem** cmdlet. 예를 들면 다음과 같습니다.  
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. 갤러리 항목을 게시 한 후 사용 하 여 출시 되었습니다. 갤러리 항목이 게시 된를 확인 하려면로 이동 **모든 서비스**를 선택한 다음는 **일반** 범주를 선택한 **Marketplace**합니다.  다운로드를 솔루션 템플릿, 하는 경우 해당 솔루션 템플릿에 대 한 모든 종속 VHD 이미지를 추가 해야 합니다.  
  [![보기 마켓플레이스](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "보기 marketplace")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Azure Stack PowerShell 1.3.0의 릴리스를 사용 하 여 이제 가상 머신 확장을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>다음 단계

[Marketplace 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)