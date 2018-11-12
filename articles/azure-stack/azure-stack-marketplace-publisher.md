---
title: Marketplace 도구 키트를 사용 하 여 만들고 marketplace 항목 게시 | Microsoft Docs
description: 신속 하 게 게시 도구 키트를 사용 하 여 마켓플레이스 항목을 만드는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4758c29d1cdb7e09c397524574ba1ab6701efeeb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238030"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Marketplace 항목 게시 도구를 사용 하 여 추가

콘텐츠를 추가 합니다 [Azure Stack Marketplace](azure-stack-marketplace.md) 하 고 배포에 대 한 테 넌 트를 솔루션을 사용할 수 있게 합니다. Marketplace 도구 키트에 IaaS Azure Resource Manager 템플릿 또는 VM 확장에 따라 Azure Marketplace (.azpkg) 패키지 파일을 만듭니다. 이 도구를 사용 하 여 만든 또는 사용 하 여.azpkg 파일을 게시 하려면 Marketplace 도구 키트를 사용할 수도 있습니다 [수동](azure-stack-create-and-publish-marketplace-item.md) 단계입니다. 이 항목에서는 도구를 다운로드, VM 템플릿을 기반으로 marketplace 항목 만들기 및 Azure Stack Marketplace에 해당 항목을 게시 한 다음에 대해 설명 합니다.     

## <a name="prerequisites"></a>필수 조건

 - 가지 거 나 Azure Stack 호스트에서 도구 키트를 실행 합니다 [VPN](.\asdk\asdk-connect.md#connect-with-vpn) 도구를 실행 하는 컴퓨터에서 ASDK 호스트에 연결 합니다.

 - 다운로드 합니다 [Azure Stack 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) 압축을 풉니다.

 - 다운로드 합니다 [Azure 갤러리 패키징 도구](https://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Marketplace 게시 아이콘 및 미리 보기 파일에 필요합니다. 직접 사용 하거나 저장 합니다 [샘플](azure-stack-marketplace-publisher.md#support-files) 이 예제에 대 한 로컬 파일입니다.

## <a name="download-the-tool"></a>도구 다운로드

Marketplace 도구 키트를 다운로드할 수 있습니다 [Azure Stack 도구 리포지토리에서](azure-stack-powershell-download.md)합니다.

##  <a name="create-marketplace-items"></a>Marketplace 항목 만들기

이 섹션에서는 Marketplace 도구 키트를 사용 하 여.azpkg 형식에서 marketplace 항목 패키지를 만듭니다.  

### <a name="provide-marketplace-information-with-wizard"></a>마법사를 사용 하 여 marketplace 정보를 제공 합니다.

1. PowerShell 세션에서 Marketplace 도구 키트를 실행 합니다.
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. 클릭 합니다 **솔루션** 탭 합니다. 이 화면에 마켓플레이스 항목에 대 한 정보를 허용합니다. Marketplace에 표시 하려는 대로 항목에 대 한 정보를 입력 합니다. 지정할 수도 있습니다는 [매개 변수 파일](azure-stack-marketplace-publisher.md#use-a-parameters-file) 폼을 미리 채웁니다.  
    
    ![Marketplace Toolkit 첫 번째 화면의 스크린 샷](./media/azure-stack-marketplace-publisher/image7.png)
3. 클릭 **찾아보기** 각 아이콘과 스크린 샷 필드에 대 한 이미지 파일을 선택 합니다. 사용자 지정 아이콘 또는 샘플 아이콘을 사용할 수 있습니다는 [지원 파일이](azure-stack-marketplace-publisher.md#support-files) 섹션입니다.
4. 에 모든 필드는 채워진 후 Marketplace 내에 솔루션의 미리 보기에 대 한 "미리 보기 솔루션"을 선택 합니다. 수정 하 고 텍스트, 이미지 및 스크린 샷 클릭 하기 전에 편집할 수 있습니다 **다음**합니다.  

### <a name="import-template-and-create-package"></a>패키지를 만들고 템플릿 가져오기

이 섹션에서는 템플릿을 가져오고 사용자 솔루션에 대 한 입력을 사용 하 여 작동 합니다.

1.  클릭 **찾아보기** 선택 합니다 *azuredeploy.json* 다운로드 한 템플릿에 101-간단한-Windows-VM 폴더에서.

    ![두 번째 화면 Marketplace 도구 키트의 스크린샷](./media/azure-stack-marketplace-publisher/image8.png)
2.  배포 마법사를 사용 하 여 채워집니다를 *기본* 템플릿에 지정 된 각 매개 변수에 대 한 단계 및 입력 항목입니다. 추가 단계를 추가 하 고 입력 단계 간에 이동할 수 있습니다. 예를 들어 솔루션에 대 한 "프런트 엔드 구성" 및 "백 엔드 구성" 단계 수도 있습니다.
3.  AzureGalleryPackager.exe에 대 한 경로 지정 합니다.  
4.  **만들기**를 클릭합니다. Marketplace toolkit.azpkg 파일로 솔루션을 패키징합니다. 완료 되 면 마법사 솔루션 파일에 경로 표시 하 고 Azure Stack에 패키지를 게시 하려면 옵션을 제공 합니다.

## <a name="publish-marketplace-items"></a>Marketplace 항목 게시

이 섹션에서는 Azure Stack marketplace 마켓플레이스 항목을 게시합니다.

![Marketplace Toolkit 첫 번째 화면의 스크린 샷](./media/azure-stack-marketplace-publisher/image9.png)

1.  마법사에는 솔루션을 게시 하는 정보가 필요 합니다.
    
    |필드|설명|
    |-----|-----|
    | 서비스 관리자 이름 | 서비스 관리자 계정입니다.  예제: ServiceAdmin@mydomain.onmicrosoft.com |
    | 암호 | 서비스 관리자 계정의 암호입니다. |
    | API 엔드포인트 | Azure Stack Azure Resource Manager 끝점입니다. 예제: management.local.azurestack.external |
2.  클릭 **게시** 게시 로그가 표시 됩니다.
3.  Azure Stack 포털을 통해 게시 된 항목을 배포할 수 있게 됩니다.

## <a name="use-a-parameters-file"></a>매개 변수 파일을 사용 하 여

또한 marketplace 항목 정보를 작성 하려면 매개 변수 파일을 사용할 수 있습니다.  

Marketplace 도구 키트에 포함 되어는 *solution.parameters.ps1* 고유한 매개 변수 파일을 만드는 데 사용할 수 있습니다.

## <a name="support-files"></a>지원 파일

| 설명 | 샘플 |
| ----- | ----- |
| (40x40.png 아이콘 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90x90.png 아이콘 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115x115.png 아이콘 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255 x 115.png 아이콘 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324.png 미리 보기 | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>다음 단계

[Marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)  
[Marketplace 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)