---
title: Azure에서 marketplace 항목 다운로드 | Microsoft Docs
description: 클라우드 운영자 Azure Stack 배포 내에 Azure에서 마켓플레이스 항목을 다운로드할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/27/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 418b2f6b156853c1a2820271808bdba922d41a87
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412902"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure에서 Azure Stack marketplace 항목 다운로드

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

클라우드 운영자로 서 Azure Marketplace에서 항목을 다운로드 하 고 Azure Stack에서 사용할 수 있도록 합니다. 사전 테스트 및 Azure Stack과 함께 작동 하는 지원 되는 Azure Marketplace 항목의 엄선된 된 목록에서 항목이 선택할 수 있습니다. 따라서 새로운 내용을 확인 하려면 계속, 추가 항목은 자주이 목록에 추가 됩니다. 

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

    ![저장소 공간을 검토](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Azure Stack Marketplace를 열고 Azure에 연결 합니다. 이 위해 선택 **Marketplace management**를 선택한 후 **Azure에서 추가**합니다.

    ![Azure에서 추가](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    포털에는 Azure Marketplace에서 다운로드할 수 있는 항목 목록을 표시합니다. 다운로드 크기를 포함 하 여,에 대 한 추가 정보 및 해당 설명을 보려면 각 항목을 클릭할 수 있습니다. 

    ![Marketplace 목록](media/azure-stack-download-azure-marketplace-item/image03.png)

4. 항목을 선택한 다음 선택 **다운로드**합니다. 다운로드 시간 달라 집니다.

    ![다운로드 메시지](media/azure-stack-download-azure-marketplace-item/image04.png)

    다운로드가 완료 되 면 Azure Stack 연산자 또는 사용자로 새 마켓플레이스 항목을 배포할 수 있습니다.

5. 다운로드 한 항목을 배포 하려면 선택 **+ 새로 만들기**, 한 다음 새 마켓플레이스 항목에 대 한 범주 중에서 검색 합니다. 다음 배포 프로세스를 시작 하려면 항목을 선택 합니다. 다른 marketplace 항목에 대 한 프로세스 달라 집니다. 

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

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>마켓플레이스 배포 도구를 사용 하 여 marketplace 항목 다운로드

1. 인터넷에 연결 된 컴퓨터에서 관리자 권한으로 PowerShell 콘솔을 엽니다.

2. Azure Stack 등록을 사용한 Azure 계정을 추가 합니다. Powershell 실행 계정에 추가할 `Add-AzureRmAccount` 매개 변수 없이 합니다. Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다 하 고 계정 구성에 따라 2 단계 인증을 사용 해야 할 수 있습니다.

3. 여러 구독이 있는 경우 등록에 사용한 것을 선택 하려면 다음 명령을 실행 합니다.  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 다음 스크립트를 사용 하 여 마켓플레이스 배포 도구의 최신 버전을 다운로드 합니다.  

   ```PowerShell
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

5. 배포 모듈을 가져올 다음 다음 스크립트를 실행 하 여 도구를 시작 합니다. 대체는 *대상 폴더 경로* Azure Marketplace에서 다운로드 한 파일을 저장할 위치를 사용 하 여 합니다.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 도구를 실행 하는 경우 Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다. Azure Stack 등록을 사용 하면 Azure 계정에 로그인 합니다. 로그인에 성공 하면 다음 이미지에서는 사용할 수 있는 marketplace 항목의 목록과 같은 화면이 표시 됩니다.  

   ![Azure Marketplace 항목 팝업](media/azure-stack-download-azure-marketplace-item/image05.png)

7. 다운로드 하 고 기록 하려는 항목을 선택 합니다 *버전*합니다. (보유할 수는 *Ctrl* 키를 여러 이미지를 선택 합니다.) 참조 하 게 됩니다 합니다 *버전* 다음 절차에서 항목을 가져올 때입니다. 
   
   사용 하 여 이미지 목록을 필터링 할 수도 있습니다는 **조건 추가** 옵션입니다.

8. 선택 **확인**, 한 다음 확인 하 고 약관에 동의 합니다. 

9. 다운로드 하는 경우 항목의 크기에 따라 달라 집니다. 다운로드가 완료 되 면 항목을 스크립트에 지정 된 폴더에서 제공 됩니다. (가상 머신) 용 VHD 파일을 포함 하는 다운로드 또는 합니다. ZIP 파일 (가상 머신 확장)입니다. 또한 갤러리 패키지를 *.azpkg* 형식. (A *.azpkg* 패키지를 *.zip* 파일입니다.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>다운로드를 가져오고 Azure Stack Marketplace에 게시
1. 가상 머신 이미지에 있는 솔루션 템플릿 파일 [이전에 다운로드 한](#use-the-marketplace-syndication-tool-to-download-marketplace-items) Azure Stack 환경에 로컬로 사용할 수 있어야 합니다.  

2. Marketplace 항목 패키지 (.azpkg 파일) Azure Stack Blob storage에 업로드 하는 관리 포털을 사용 합니다. 패키지의 업로드가 사용할 수 있도록 Azure Stack에 항목을 게시할 수 있도록 Azure Stack Marketplace.

   업로드 해야 공개적으로 액세스 가능한 컨테이너를 사용 하 여 저장소 계정 (이 시나리오에 대 한 필수 조건 참조)   
   1. Azure Stack 관리 포털에서로 이동 **더 많은 서비스** > **저장소 계정**합니다.  
   
   2. 선택한 저장소 계정 구독에서 다음 아래 **BLOB SERVICE**를 선택 **컨테이너**합니다.  
      ![Blob 서비스](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. 사용 하 여 선택한 컨테이너를 선택 **업로드** 열려는 합니다 **blob 업로드** 창입니다.  
      ![컨테이너](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. 저장소에 로드 하 고 클릭 하려는 파일 업로드 blob 창에서 이동할 **업로드**합니다.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. 업로드 된 파일 컨테이너 창에 나타납니다. 파일을 선택 하 고 다음에서 URL을 복사 합니다 **Blob 속성** 창입니다. Azure Stack에 마켓플레이스 항목을 가져올 때 다음 단계에서이 URL을 사용 합니다.  다음 이미지는 컨테이너는 *blob-테스트-저장소* 있고 파일이 *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*합니다.  파일 URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*합니다.  
      ![Blob 속성](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

3. VHD 이미지를 사용 하 여 Azure Stack 가져올는 **추가 AzsPlatformimage** cmdlet. 이 cmdlet을 사용 하는 경우 대체는 *게시자*, *제공*, 및가 가져오는 이미지의 값을 사용 하 여 다른 매개 변수 값입니다. 

   가져올 수 있습니다 합니다 *게시자*를 *제공*, 및 *sku* AZPKG 파일을 다운로드 하는 텍스트 파일에서 이미지의 값입니다. 텍스트 파일의 대상 위치에 저장 됩니다. 합니다 *버전* 값은 Azure에서 이전 절차에서 항목을 다운로드 하는 경우 명시 버전입니다. 
 
   다음 예제 스크립트에서는 서버 코어 가상 머신에서 Windows Server 2016 Datacenter-에 대 한 값이 사용 됩니다. 바꿉니다 *URI_path* 항목에 대 한 blob 저장소 위치에 경로 사용 하 여 합니다.

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "URI_path"  
   ```
   **솔루션 템플릿에 대 한:** 일부 템플릿에서 작은 3MB를 포함할 수 있습니다. VHD 파일 이름의 **fixed3.vhd**합니다. Azure Stack에 해당 파일을 가져올 필요가 없습니다. Fixed3.vhd 합니다.  이 파일은 Azure Marketplace에 대 한 게시 요구 사항에 맞게 일부 솔루션 템플릿을 사용 하 여 포함 합니다.

   템플릿 설명을 검토 하 고 다운로드 가져와서 솔루션 템플릿을 사용 하 여 작동 하는 데 필요한 Vhd와 같은 추가 요구 사항입니다.  
   
   **확장에 대 한:** 가상 머신 이미지 확장을 사용 하 여 작업할 때 다음 매개 변수를 사용 합니다.
   - *게시자*
   - *형식*
   - *버전*  

   사용 하지 않는 *제공* 확장에 대 한 합니다.   


4.  PowerShell을 사용 하 여 사용 하 여 Azure Stack의 마켓플레이스 항목 게시 하는 **추가 AzsGalleryItem** cmdlet. 예:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. 갤러리 항목을 게시 한 후에서 볼 수 있습니다 **더 많은 서비스** > **Marketplace**합니다.  다운로드를 솔루션 템플릿, 하는 경우 해당 솔루션 템플릿에 대 한 모든 종속 VHD 이미지를 추가 해야 합니다.  
  ![보기 marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Azure Stack PowerShell 1.3.0의 릴리스를 사용 하 여 이제 가상 머신 확장을 추가할 수 있습니다.

예: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>다음 단계
[Marketplace 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)