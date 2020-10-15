---
title: Windows Virtual Desktop(클래식) 호스트 풀 서비스 업데이트 - Azure
description: 프로덕션에 업데이트를 출시하기 전에 서비스 업데이트를 모니터링하는 Windows Virtual Desktop(클래식)에서 유효성 검사 호스트 풀을 만드는 방법을 알아봅니다.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 291f1e8b8870257c233dc32894ff49b26c0a3501
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323532"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>자습서: Windows Virtual Desktop(클래식)에서 서비스 업데이트의 유효성을 검사하는 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../create-validation-host-pool.md)를 참조하세요.

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 서비스 업데이트가 먼저 적용되는 유효성 검사 호스트 풀을 만드는 것이 좋습니다. 이렇게 하면 서비스에서 표준 또는 비유효성 검사 환경에 서비스 업데이트를 적용하기 전에 서비스 업데이트를 모니터링할 수 있습니다. 유효성 검사 호스트 풀이 없으면 오류를 유발하는 변경을 감지하지 못하여 프로덕션 환경의 사용자에게 가동 중지 시간이 발생할 수 있습니다.

앱에서 최신 업데이트가 문제 없이 작동되려면 유효성 검사 호스트 풀이 비유효성 검사 환경의 호스트 풀과 최대한 유사해야 합니다. 사용자는 표준 호스트 풀에 연결하는 것만큼 유효성 검사 호스트 풀에도 자주 연결해야 합니다. 호스트 풀에 대한 테스트를 자동화한 경우 유효성 검사 호스트 풀에 자동화된 테스트를 포함해야 합니다.

[진단 기능](diagnostics-role-service-2019.md) 또는 [Windows Virtual Desktop 문제 해결 문서](troubleshoot-set-up-overview-2019.md) 중 하나를 사용하여 유효성 검사 호스트 풀의 문제를 디버그할 수 있습니다.

>[!NOTE]
> 모든 후속 업데이트를 테스트하기 위해 유효성 검사 호스트 풀을 그대로 두는 것이 좋습니다.

시작하기 전에 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 없는 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>호스트 풀 만들기

다음 문서 중 하나에 제공된 지침에 따라 호스트 풀을 만들 수 있습니다.
- [자습서: Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace-2019.md)
- [Azure Resource Manager 템플릿으로 호스트 풀 만들기](create-host-pools-arm-template.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>호스트 풀을 유효성 검사 호스트 풀로 정의

다음 PowerShell cmdlet을 실행하여 새 호스트 풀을 유효성 검사 호스트 풀로 정의합니다. 따옴표 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

다음 PowerShell cmdlet을 실행하여 유효성 검사 속성이 설정되어 있는지 확인합니다. 따옴표 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

cmdlet의 결과는 다음 출력과 비슷해야 합니다.

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>업데이트 일정

매월 서비스 업데이트가 수행됩니다. 주요 이슈가 있는 경우 중요 업데이트가 좀 더 자주 제공됩니다.

## <a name="next-steps"></a>다음 단계

이제 유효성 검사 호스트 풀을 만들었으므로 Azure Service Health를 사용하여 Windows Virtual Desktop 배포를 모니터링하는 방법을 배울 수 있습니다.

> [!div class="nextstepaction"]
> [서비스 경고 설정](set-up-service-alerts-2019.md)
