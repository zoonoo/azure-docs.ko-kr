---
title: PowerShell을 사용 하 여 Azure Security Center에 등록
description: 이 문서에서는 PowerShell cmdlet을 사용하여 Azure Security Center 온보딩 프로세스를 단계별로 안내합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: e367851c4d709acbc0eb94a47d7e20d4d3c1cc46
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904807"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>PowerShell을 사용하여 Azure Security Center 온보딩 자동화

Azure Security Center PowerShell 모듈을 사용하여 Azure 워크로드를 프로그래밍 방식으로 보호할 수 있습니다.
PowerShell을 사용하면 작업을 자동화하고 수동 작업에 내재된 사용자 오류를 피할 수 있습니다. 이는 특히 모두 처음부터 보호해야 하는 수십만 개의 리소스가 있는 수십 개의 구독이 포함된 대규모 배포에 유용합니다.

PowerShell을 사용하여 Azure Security Center를 온보딩하면 Azure 리소스의 온보딩 및 관리를 프로그래밍 방식으로 자동화하고 필요한 보안 컨트롤을 추가할 수 있습니다.

이 문서에서는 전체 구독에서 Security Center를 배포하기 위해 환경에서 수정 및 사용될 수 있는 샘플 PowerShell 스크립트를 제공합니다. 

이 예제에서는 ID: d07c0080-170c-4c24-861d-9c817742786c를 사용 하 여 구독에 대 한 Security Center를 사용 하도록 설정 하 고, 고급 위협 방지 및 검색 기능을 제공 하는 Azure Defender를 사용 하 여 높은 수준의 보호를 제공 하는 권장 설정을 적용 합니다.

1. [Azure Defender](azure-defender.md)를 사용 하도록 설정 합니다. 
 
2. 구독과 연결 된 Vm에서 수집한 데이터를 Log Analytics 에이전트가 전송 하는 Log Analytics 작업 영역을 설정 합니다 .이 예제에서는 기존 사용자 정의 작업 영역 (myWorkspace)입니다.

3. [Log Analytics 에이전트를 배포](security-center-enable-data-collection.md#auto-provision-mma)하는 Security Center의 자동 에이전트 프로 비전을 활성화 합니다.

5. [Security Center 경고 및 주목할 만한 이벤트에 대 한 보안 연락처로 조직의 ciso](security-center-provide-security-contact-details.md)을 설정 합니다.

6. Azure Security Center의 [기본 보안 정책](tutorial-security-policy.md)을 할당합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 단계는 Security Center cmdlet을 실행하기 전에 수행해야 합니다.

1. 관리자 권한으로 PowerShell을 실행합니다.

1. PowerShell에서 다음 명령을 실행합니다.
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>PowerShell을 사용하여 Security Center 온보딩

1. Security Center 리소스 공급자에 구독을 등록합니다.

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. 선택 사항: 구독의 적용 범위 수준 (Azure Defender on/off)을 설정 합니다. 정의 되지 않은 경우 Defender가 해제 됩니다.

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. 에이전트가 보고할 Log Analytics 작업 영역을 구성합니다. 구독의 VM이 보고할 기존에 만든 Log Analytics 작업 영역이 있어야 합니다. 동일한 작업 영역에 보고할 여러 구독을 정의할 수 있습니다. 정의되지 않은 경우 기본 작업 영역이 사용됩니다.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Azure Vm에서 Log Analytics 에이전트의 자동 프로 비전:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > 자동 프로비저닝을 사용하도록 설정하여 Azure Virtual Machines가 Azure Security Center를 통해 자동으로 보호되도록 하는 것이 좋습니다.
    >

1. 선택 사항: Security Center에서 생성한 경고 및 알림의 수신자로 사용될, 온보딩하는 구독의 보안 연락처 세부 정보를 정의하는 것이 좋습니다.

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. 기본 Security Center 정책 이니셔티브를 할당합니다.

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Enable Monitoring in Azure Security Center'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

PowerShell을 사용 하 여 Azure Security Center 성공적으로 등록 했습니다.

이제 이러한 PowerShell cmdlet을 자동화 스크립트와 함께 사용하여 구독 및 리소스를 프로그래밍 방식으로 반복할 수 있습니다. 이렇게 하면 시간이 절약되고 사용자 오류 가능성이 줄어듭니다. 이 [샘플 스크립트](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)를 참조로 사용할 수 있습니다.




## <a name="see-also"></a>참조
PowerShell을 사용하여 Security Center에 대한 온보딩을 자동화하는 방법을 자세히 알아보려면 다음 문서를 참조하세요.

* [Az. Security](https://docs.microsoft.com/powershell/module/az.security)

Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) --Azure 구독 및 리소스 그룹에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
* [Azure Security Center의 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) --보안 경고를 관리 하 고 대응 하는 방법을 알아봅니다.