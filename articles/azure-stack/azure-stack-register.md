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
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
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

이러한 요구 사항을 충족 하는 Azure 구독에 없으면 다음을 할 수 있습니다 [여기 무료 Azure 계정을 만들](https://azure.microsoft.com/en-us/free/?b=17.06)합니다. Azure 스택 등록 무료로 Azure 구독에서 발생 합니다.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Azure에서 Azure 스택 리소스 공급자 등록
> [!NOTE] 
> 이 단계는 Azure 스택 환경에서 한 번만 완료 해야 합니다.
>

1. 관리자 권한으로 Powershell 세션을 시작 합니다.
2. Azure 구독 (로그인 하 고 로그인 할 때는-EnvironmentName 매개 변수 "azure 클라우드"를 설정 했는지 확인 하려면 로그인 AzureRmAccount cmdlet 사용 가능)의 소유자가 있는 Azure 계정에 로그인 합니다.
3. "Microsoft.AzureStack" Azure 리소스 공급자 등록

**예제:** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Azure 스택 Azure 등록

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 실행 되어야 합니다. Azure 스택 개발 키트에 대 한 호스트 컴퓨터 됩니다. 통합된 시스템을 사용 하는 경우 Azure 스택 운영자를 게 문의 하십시오.
>

1. 관리자 권한으로 PowerShell 콘솔을 열고 및 [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.  

2. Azure 스택을 등록 하는 데 사용할 Azure 계정을 추가 합니다. 이 작업을 수행 하려면 실행의 `Add-AzureRmAccount` 매개 변수 없이 cmdlet. Azure 계정 자격 증명을 입력 하는 메시지가 및 사용자 계정 구성에 따라 2 단계 인증을 사용 하 여 할 수 있습니다.  

3. 여러 구독이 있는 경우 사용 하려는 목록을 선택 하려면 다음 명령을 실행 합니다.  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 등록에 해당 하는 Powershell 모듈의 기존 버전을 모두 삭제 하 고 [GitHub에서 최신 버전을 다운로드](azure-stack-powershell-download.md)합니다.  

5. 이전 단계에서 만든 "AzureStack 도구 마스터" 디렉터리에서 "등록" 폴더로 이동 하 고 ".\RegisterWithAzure.psm1" 모듈을 가져옵니다.  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. 동일한 PowerShell 세션에서 다음 스크립트를 실행 합니다. 자격 증명을 묻는 메시지가 나타나면 지정 `azurestack\cloudadmin` 사용자 및 암호 기능 중에 사용한 로컬 관리자에 대 한 배포 하는 것과 같습니다.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | 매개 변수 | 설명 |
   | -------- | ------------- |
   | CloudAdminCredential | 하는 데 사용 되는 클라우드 도메인 자격 증명 [권한 있는 끝점에 액세스할](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 형식의 사용자 이름이 "\<Azure 스택 도메인\>\cloudadmin"입니다. 개발 키트에 대 한 사용자 이름 "azurestack\cloudadmin"로 설정 됩니다. 통합된 시스템을 사용 하는 경우이 값을 가져오기 귀하가 Azure 스택 운영자를 게 문의 하십시오.|
   | AzureSubscriptionId | Azure 스택을 등록 하는 데 사용 하는 Azure 구독입니다.|
   | AzureDirectoryTenantName | Azure 구독과 연결 된 Azure 테 넌 트 디렉터리의 이름입니다. 이 디렉터리 테 넌 트에 등록 리소스 만들어질 수 있습니다. |
   | PrivilegedEndpoint | 미리 구성 된 원격 PowerShell 콘솔을 제공 하는 로그 수집 및 기타 post와 같은 기능은 배포 작업입니다. 개발 키트에 대 한 권한 있는 끝점 "AzS ERCS01" 가상 컴퓨터에서 호스팅됩니다. 통합된 시스템을 사용 하는 경우이 값을 가져오기 귀하가 Azure 스택 운영자를 게 문의 하십시오. 자세한 내용은를 참조는 [권한 있는 끝점을 사용 하 여](azure-stack-privileged-endpoint.md) 항목입니다.|
   | BillingModel | 구독을 사용 하는 청구 모델입니다. 이 매개 변수는 "Capacity" 차원, "PayAsYouUse" 및 "개발"에 대 한 값을 허용 합니다. 개발 키트에 대 한이 값은 "개발"로 설정 됩니다. 통합된 시스템을 사용 하는 경우이 값을 가져오기 귀하가 Azure 스택 운영자를 게 문의 하십시오. |

7. 스크립트가 완료 되는 메시지가 나타납니다. "활성 중 Azure 스택 (이 단계를 완료 하려면 최대 10 분까지 걸릴 수 있음)." 

## <a name="verify-the-registration"></a>등록 확인

1. 관리자 포털 (https://adminportal.local.azurestack.external)에 로그인 합니다.
2. 클릭 **더 많은 서비스** > **마켓플레이스 관리** > **Azure에서 추가**합니다.
3. (예: WordPress) Azure에서 사용할 수 있는 항목 목록이 표시 되 면 인증 과정에 성공 했습니다.

> [!NOTE]
> 등록이 완료 되 면 등록 하 고 있지에 대 한 활성 경고 더 이상 나타나지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack에 연결](azure-stack-connect-azure-stack.md)

