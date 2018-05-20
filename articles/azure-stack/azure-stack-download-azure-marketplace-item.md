---
title: Azure에서 마켓플레이스 항목을 다운로드 | Microsoft Docs
description: 내 Azure 스택 배포에서 Azure 마켓플레이스 항목을 다운로드할 수 있습니다.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 2e92dc96a69400f689e49b70d1b855c955084362
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure에서 Azure 스택에 마켓플레이스 항목을 다운로드

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*


Azure 스택 marketplace에 포함할 콘텐츠를 결정 하는 대로 Azure 마켓플레이스에서 사용할 수 있는 콘텐츠를 고려해 야 합니다. Azure 스택에서 실행 되도록 검증을 거친 되었던 Azure 마켓플레이스 항목의 큐 레이트 목록에서 다운로드할 수 있습니다. 이 목록에 새 항목 자리 자주, 새 콘텐츠를 확인 해야 합니다.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>(인터넷 연결)에 연결 된 시나리오에서 마켓플레이스 항목을 다운로드 합니다.

1. 마켓플레이스 항목을 다운로드 하려면 먼저 [Azure를 사용한 Azure 스택 등록](azure-stack-register.md)합니다.
2. Azure 스택 관리자 포털에 로그인 (ASDK에 대 한 사용 https://portal.local.azurestack.external)합니다.
3. 일부 마켓플레이스 항목 클 수 있습니다. 확인을 클릭 하 여 시스템에 있는 충분 한 공간이 있는지 확인 하십시오 **리소스 공급자** > **저장소**합니다.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. 클릭 **더 많은 서비스** > **마켓플레이스 관리**합니다.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 클릭 **Azure에서 추가** 다운로드할 수 있는 항목의 목록을 볼 수 있습니다. 다운로드 크기 및 설명을 보려면 목록에서 각 항목을 클릭할 수 있습니다.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 클릭 한 다음 목록에서 원하는 항목을 선택 **다운로드**합니다. 다운로드를 시작할 선택한 항목에 대 한 VM 이미지입니다. 다운로드 시간 달라 집니다.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 다운로드가 완료 되는 Azure 스택 연산자 또는 사용자로 프로그램 새 마켓플레이스 항목을 배포할 수 있습니다. 클릭 **+ 새로 만들기**새 마켓플레이스 항목에 대 한 범주를 검색 하 고 다음 항목을 선택 합니다.
7. 클릭 **만들기** 새로 다운로드 한 항목에 대 한 생성 환경을 엽니다. 에 항목을 배포 하려면 단계별 지침을 따릅니다.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>마켓플레이스 항목에는 연결이 끊어진 또는 부분적으로 연결 된 시나리오 (제한 된 인터넷에 연결 된) 다운로드

모든 인터넷 연결) (없이 연결이 끊어진된 모드에서 Azure 스택을 배포할 때는 Azure 스택 포털을 사용 하 여 마켓플레이스 항목을 다운로드할 수 없습니다. 그러나 마켓플레이스 배포 도구를 사용 하 여 인터넷에 연결 하는 컴퓨터로 마켓플레이스 항목을 다운로드 하 수 있으며 Azure 스택 환경에 전송할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
마켓플레이스 배포 도구를 사용 하려면 먼저 확인 했는지 [Azure 구독과 Azure 스택 등록](azure-stack-register.md)합니다.  

인터넷에 연결 된 컴퓨터에서 필요한 마켓플레이스 항목을 다운로드 하려면 다음 단계를 따르십시오.

1. 관리자 권한으로 PowerShell 콘솔을 열고 및 [Azure 스택 특정 PowerShell 모듈 설치](azure-stack-powershell-install.md)합니다. 설치 하 고 있는지 확인 **Azure 스택 PowerShell 모듈 버전 1.2.11 이상**합니다.  

2. Azure 스택 등록를 사용 하 여 Azure 계정을 추가 합니다. 실행 계정을 추가 하는 **추가 AzureRmAccount** 매개 변수 없이 cmdlet. Azure 계정 자격 증명을 입력 하는 메시지가 및 사용자 계정 구성에 따라 2 단계 인증을 사용 하 여 할 수 있습니다.  

3. 여러 구독이 있는 경우 등록에 사용 되는 항목을 선택 하려면 다음 명령을 실행 합니다.  

   ```powershell
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
   cd \AzureStack-Tools-master

   ```

5. 배포 모듈을 가져올 하 고 다음 명령을 실행 하 여 도구를 시작 합니다.  

   ```powershell
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 이 도구를 실행 하는 경우 Azure 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다. Azure 스택 등록을 사용 하는 Azure 계정에 로그인 합니다. 로그인에 성공 하면 다음 화면이 마켓플레이스를 사용할 수 있는 항목의 목록으로 나타납니다.  

   ![Azure 마켓플레이스 항목 팝업](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. 다운로드 하 여 이미지 버전을 기록 하려는 이미지를 선택 합니다. Ctrl 키를 누르고 있으면 여러 이미지를 선택할 수 있습니다. 다음 섹션에서 이미지를 가져올 이미지 버전을 사용 합니다.  그런 다음 클릭 **확인**, 다음을 클릭 하 여 약관에 동의 하 고 **예**합니다. 사용 하 여 이미지 목록을 필터링 할 수도 있습니다는 **조건을 추가** 옵션입니다. 

   다운로드는 이미지의 크기에 따라 시간이 걸립니다. 한 번 이미지 다운로드, 이전 버전에서 제공 하는 대상 경로가 제공 됩니다. (가상 컴퓨터)에 대 한 VHD 파일을 포함 하는 다운로드 또는 합니다. ZIP 파일 (가상 컴퓨터 확장) 및 Azpkg 형식에서 갤러리 항목입니다.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>이미지를 가져오고 Azure 스택 마켓플레이스에 게시
시장에 있는 항목의 세 가지 종류가 있습니다: 템플릿과 가상 컴퓨터, 가상 컴퓨터 확장 솔루션입니다. 솔루션 템플릿은 아래 설명 되어 있습니다.
> [!NOTE]
> 지금은 Azure 스택에 가상 컴퓨터 확장을 추가할 수 없습니다.

1. 이미지 및 갤러리 패키지를 다운로드 한 후 및 내용을 AzureStack 도구 마스터 폴더는 이동식 디스크 드라이브에 저장 하 고 Azure 스택 환경에 복사 (복사할 수 있습니다 로컬로 모든 위치에 같은: "C:\MarketplaceImages").     

2. 이미지를 가져오기 전에 연결 해야 Azure 스택 운영자의 환경에 설명 된 단계를 사용 하 여 [Azure 스택 운영자의 PowerShell 환경을 구성](azure-stack-powershell-configure-admin.md)합니다.  

3. 다운로드가 fixed3.vhd 라는 작은 3MB VHD 파일을 포함 하는 경우는 솔루션 템플릿입니다. 이 파일은 필요 하지 않습니다. 5 단계로 건너뜁니다. 다운로드에 대 한 설명에 표시 된 대로 모든 종속 항목을 다운로드 하 고 있는지 확인 합니다.

4. 추가 AzsVMImage cmdlet을 사용 하 여 Azure 스택에 이미지를 가져옵니다. 이 cmdlet을 사용할 때는 확인 바꿔야는 *게시자*, *제공*, 및 다른 매개 변수 값을 가져올 이미지의 값입니다. 가져올 수는 *게시자*, *제공*, 및 *sku* 이전에 다운로드 한 Azpkg 파일의 imageReference 개체에서 이미지의 값 및  *버전* 이전 단원의 6 단계에서 값입니다.

imageReference를 찾기 위해 사용 하 여 AZPKG 파일 이름을 바꿀 해야 합니다는 합니다. 확장 ZIP을 임시 위치에 추출 하 고, 텍스트 편집기로 DeploymentTemplates\CreateUiDefinition.json 파일을 엽니다. 이 섹션을 찾습니다.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   매개 변수 값을 대체 하 고 다음 명령을 실행 합니다.

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

5. 마켓플레이스 항목을 업로드를 위해 사용 하 여 포털 (합니다. Azpkg) 스택 Azure Blob 저장소에 있습니다. 로컬 스택 Azure 저장소에 업로드 하거나 Azure 저장소에 업로드할 수 있습니다. (이 패키지에 대 한 임시 위치입니다.) Blob 공개적으로 액세스할 수 있는지 확인 하 고 URI를 확인 합니다.  

6. 마켓플레이스 항목을 사용 하 여 Azure 스택에 게시는 **추가 AzsGalleryItem**합니다. 예: 

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

7. 갤러리 항목을 게시 한 후에서 볼 수 있습니다는 **새로** > **마켓플레이스** 창. 다운로드가 솔루션 템플릿이 되었으면도 종속 VHD 이미지를 다운로드 해야 합니다.

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>다음 단계

[만들기 및 마켓플레이스 항목을 게시](azure-stack-create-and-publish-marketplace-item.md)
