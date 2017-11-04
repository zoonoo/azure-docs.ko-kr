---
title: "Azure 스택 등록 | Microsoft Docs"
description: "Azure 스택 Azure 구독에 등록 합니다."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Azure 스택 Azure 구독에 등록

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

등록할 수 있는 [Azure 스택](azure-stack-poc.md) azure에서 Azure 마켓플레이스 항목을 다운로드 하 고 상용 데이터를 Microsoft에 다시 보고를 설정 하려면. 

> [!NOTE]
>마켓플레이스 배포 및 사용 보고와 같은 중요 한 Azure 스택 기능을 테스트 하면 있기 때문에 등록을 사용 하는 것이 좋습니다. Azure 스택 등록 한 후 사용 Azure 상거래에 보고 됩니다. 등록에 사용 되는 구독에서 볼 수 있습니다. Azure 스택 개발 키트 사용자는 보고 사용에 대 한 요금이 청구 되지 않습니다.
>


## <a name="get-azure-subscription"></a>Azure 구독 가져오기

Azure 스택 Azure를 등록 하기 전에 다음이 필요 합니다.

- Azure 구독에 대 한 구독 ID입니다. ID를 가져오려면 Azure에 로그인을 클릭 **더 많은 서비스** > **구독**를 사용 하려는 구독을 클릭 하 고 아래에서 **Essentials** 찾을 수 있습니다는 **구독 ID**합니다. 중국, 독일에과 미국 정부 클라우드 구독 현재 지원 되지 않습니다.
- 사용자 이름 및 구독에 대 한 소유자가 있는 계정에 대 한 암호 (MSA/2FA 계정이 지원 됨).
- Azure 구독에 대 한 Azure Active Directory입니다. Azure 포털의 오른쪽 위 모서리에서 아바타 위로 이동 하 여 Azure에서이 디렉터리를 찾을 수 있습니다. 
- [Azure 스택 리소스 공급자 등록](#register-azure-stack-resource-provider-in-azure)합니다.

이러한 요구 사항을 충족 하는 Azure 구독에 없으면 다음을 할 수 있습니다 [여기 무료 Azure 계정을 만들](https://azure.microsoft.com/en-us/free/?b=17.06)합니다. Azure 스택 등록 무료로 Azure 구독에서 발생 합니다.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Azure에서 Azure 스택 리소스 공급자 등록
> [!NOTE] 
> 이 단계는만 Azure 스택 환경에서 한 번만 완료 해야 합니다.
>

1. 관리자 권한으로 Powershell ISE를 시작 합니다.
2. -EnvironmentName 매개 변수 "azure"클라우드로 설정 된 Azure 구독의 소유자가 있는 Azure 계정에 로그인 합니다.
3. "Microsoft.AzureStack" Azure 리소스 공급자를 등록 합니다.

예제: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록

> [!NOTE]
>호스트 컴퓨터에서 이러한 모든 단계를 완료 해야 합니다.
>

1. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다. 
2. 복사는 [RegisterWithAzure.psm1 스크립트](https://go.microsoft.com/fwlink/?linkid=842959) (예: C:\Temp) 폴더에 있습니다.
3. 관리자 권한으로 PowerShell ISE를 시작 하 고 RegisterWithAzure 모듈을 가져옵니다.    
4. RegisterWithAzure.psm1 스크립트에서 추가 AzsRegistration 모듈을 실행 합니다. 다음 자리 표시자를 바꿉니다. 
    - *YourCloudAdminCredential* 는 domain\cloudadmin에 대 한 로컬 도메인 자격 증명을 포함 하는 PowerShell 개체 (개발 키트용 이것이 azurestack\cloudadmin).
    - *YourAzureSubscriptionID* Azure 스택을 등록 하는 데 사용할 Azure 구독 ID입니다.
    - *YourAzureDirectoryTenantName* Azure 구독에 연결 된 Azure 테 넌 트 디렉터리의 이름입니다. 이 디렉터리 테 넌 트에 등록 리소스 만들어질 수 있습니다. 
    - *YourPrivilegedEndpoint* 의 이름인는 [권한 있는 끝점](azure-stack-privileged-endpoint.md)합니다.

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. 로그인 팝업 창에서 Azure 구독 자격 증명을 입력 합니다.

## <a name="verify-the-registration"></a>등록 확인

1. 관리자 포털 (https://adminportal.local.azurestack.external)에 로그인 합니다.
2. 클릭 **더 많은 서비스** > **마켓플레이스 관리** > **Azure에서 추가**합니다.
3. (예: WordPress) Azure에서 사용할 수 있는 항목 목록이 표시 되 면 인증 과정에 성공 했습니다.

> [!NOTE]
> 등록이 완료 되 면 등록 하 고 있지에 대 한 활성 경고 더 이상 나타나지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack에 연결](azure-stack-connect-azure-stack.md)

