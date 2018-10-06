---
title: Azure는 ASDK 등록 | Microsoft Docs
description: Azure Stack marketplace 배포 및 사용 보고를 사용 하도록 설정 하려면 Azure를 사용 하 여 등록 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d06ad47dc2962b249b4e7aef5667492e642be35e
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830126"
---
# <a name="azure-stack-registration"></a>Azure Stack 등록
Azure에서 마켓플레이스 항목을 다운로드 하 고 Microsoft에 다시 보고 하는 전자 상거래 데이터를 설정 하는 Azure를 사용 하 여 Azure Stack 개발 키트 ASDK () 설치를 등록할 수 있습니다. 마켓플레이스 배포를 포함 하 여 전체 Azure Stack 기능을 지원 하려면 등록이 필요 합니다. 마켓플레이스 배포 및 사용 보고와 같은 중요 한 Azure Stack 기능을 테스트할 수 있기 때문에 등록을 사용 하는 것이 좋습니다. Azure Stack 등록 한 후 Azure 상거래에 사용량이 보고 됩니다. 등록에 사용한 구독에서 볼 수 있습니다. 그러나 ASDK 사용자가 보고 하는 사용량에 대 한 요금이 청구 되지 않습니다.

ASDK 프로그램을 등록 하지 않은 경우 발생할 수 있습니다는 **활성화 필요** Azure Stack 개발 키트를 등록 해야 한다는 경고 합니다. 이는 정상적인 동작입니다.

## <a name="prerequisites"></a>필수 조건
이러한 지침을 ASDK Azure를 사용 하 여 등록을 사용 하기 전에 Azure Stack PowerShell 설치 되어 있고에 설명 된 대로 Azure Stack 도구를 다운로드를 확인 합니다 [사후 배포 구성](asdk-post-deploy.md) 문서.

또한 PowerShell 언어 모드를로 변경 해야 **FullLanguageMode** ASDK를 Azure에 등록 하는 데 사용 하는 컴퓨터입니다. 전체 관리자 권한 PowerShell 창을 열고 다음 PowerShell 명령을 실행 하려면 현재 언어 모드를 설정 되어 있는지 확인 합니다.

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

출력 반환 **FullLanguageMode**합니다. 다른 언어 모드 반환, 다른 컴퓨터에서 실행 해야 등록 되거나 언어 모드 설정 해야 합니다 **FullLanguageMode** 계속 하기 전에 합니다.

## <a name="register-azure-stack-with-azure"></a>Azure를 사용 하 여 Azure Stack 등록
Azure는 ASDK 등록 하려면 다음이 단계를 따릅니다.

> [!NOTE]
> 이러한 모든 단계는 권한 있는 끝점에 대 한 액세스 권한이 있는 컴퓨터에서 실행 되어야 합니다. ASDK에 대 한 개발 키트 호스트 컴퓨터입니다.

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.  

2. Azure를 사용 하 여 ASDK 설치를 등록 하려면 다음 PowerShell 명령을 실행 합니다. Azure 구독 및 로컬 ASDK 설치 모두에 로그인 해야 합니다. 아직 Azure 구독이 없는 할 수 있습니다 [여기서 무료 Azure 계정을 만들](https://azure.microsoft.com/free/?b=17.06)합니다. Azure Stack 등록 비용 없이 Azure 구독에서 발생 합니다.<br><br>실행 하는 경우 등록 스크립트를 Azure Stack의 둘 이상의 인스턴스에서 동일한 Azure 구독 ID를 사용 하 여를 실행 하는 경우 등록에 대 한 고유한 이름을 설정 합니다 **집합 AzsRegistration** cmdlet. 합니다 **RegistrationName** 매개 변수는 기본값인 **AzureStackRegistration**합니다. 그러나 Azure Stack의 둘 이상의 인스턴스에서 같은 이름을 사용할 경우 스크립트가 실패 합니다.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    $UsageReporting = True # Set to False if you using a Capacity Billing Model
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development `
        -RegistrationName $RegistrationName `
        -EnableUsageReporting $UsageReporting
    ```
3. 스크립트가 완료 되 면이 메시지가 표시 됩니다: **환경의 이제 등록 되 고 제공된 된 매개 변수를 사용 하 여 활성화 합니다.**

    ![환경의 지금 등록](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>등록이 성공 했는지 확인 합니다.
Azure 사용 하 여 ASDK 등록 되었는지 확인 하려면 다음이 단계를 수행 합니다.

1. 에 로그인 합니다 [Azure Stack 관리 포털](https://adminportal.local.azurestack.external)합니다.

2. 클릭 **Marketplace Management** > **Azure에서 추가**합니다.

    ![](media/asdk-register/2.PNG)

3. Azure에서 사용할 수 있는 항목의 목록을 표시를 활성화가 했습니다.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>등록 리소스 이동
동일한 구독에서 리소스 그룹을 사용 하 여 등록 리소스를 이동할 **는** 지원 합니다. 새 리소스 그룹에 리소스를 이동 하는 방법에 대 한 자세한 내용은 참조 하세요. [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)합니다.


## <a name="next-steps"></a>다음 단계
[Azure Stack marketplace 항목 추가](.\.\azure-stack-marketplace.md)
