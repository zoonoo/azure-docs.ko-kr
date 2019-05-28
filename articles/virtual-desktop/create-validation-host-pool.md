---
title: Windows Virtual Desktop 미리 보기 호스트 풀을 만들어 서비스 업데이트 유효성 검사 - Azure
description: 프로덕션에 업데이트를 출시하기 전에 서비스 업데이트를 모니터링하는 유효성 검사 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 91dd716a595dd56c14b3be0bf11a7b69eeb470d6
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834116"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>자습서: 서비스 업데이트의 유효성을 검사하기 위한 호스트 풀 만들기

호스트 풀은 Windows Virtual Desktop 미리 보기 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 호스트 풀을 프로덕션 환경에 배포하기 전에 유효성 검사 호스트 풀을 만드는 것이 좋습니다. 서비스 업데이트는 프로덕션 환경에 배포되기 전에 모니터링할 수 있도록 유효성 검사 호스트 풀에 먼저 적용됩니다. 유효성 검사 호스트 풀이 없으면 오류를 유발하는 변경을 감지하지 못하여 프로덕션 환경의 사용자에게 가동 중지 시간이 발생할 수 있습니다.

앱에서 최신 업데이트가 문제 없이 작동되려면 유효성 검사 호스트 풀이 프로덕션 환경의 호스트 풀과 최대한 유사해야 합니다. 사용자는 프로덕션 호스트 풀과 마찬가지로, 유효성 검사 호스트 풀에도 자주 연결해야 합니다. 호스트 풀에 대한 테스트를 자동화한 경우 유효성 검사 호스트 풀에 자동화된 테스트를 포함해야 합니다.

[진단 기능](diagnostics-role-service.md) 또는 [Windows Virtual Desktop 문제 해결 문서](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview) 중 하나를 사용하여 유효성 검사 호스트 풀의 문제를 디버그할 수 있습니다.

>[!NOTE]
> 모든 후속 업데이트를 테스트하기 위해 유효성 검사 호스트 풀을 그대로 두는 것이 좋습니다.

시작하기 전에 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 없는 경우).

## <a name="create-your-host-pool"></a>호스트 풀 만들기

다음 문서 중 하나에 제공된 지침에 따라 호스트 풀을 만들 수 있습니다.
- [자습서: Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [Azure Resource Manager 템플릿으로 호스트 풀 만들기](create-host-pools-arm-template.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>호스트 풀을 유효성 검사 호스트 풀로 정의

다음 PowerShell cmdlet을 실행하여 새 호스트 풀을 유효성 검사 호스트 풀로 정의합니다. 따옴표 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

다음 PowerShell cmdlet을 실행하여 유효성 검사 속성이 설정되어 있는지 확인합니다. 따옴표 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

cmdlet의 결과는 다음 출력과 비슷해야 합니다.

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>업데이트 일정

미리 보기에서 서비스 업데이트는 대략 월별 주기로 발생합니다. 주요 이슈가 있는 경우 중요 업데이트가 좀 더 자주 제공됩니다.

## <a name="next-steps"></a>다음 단계

유효성 검사 호스트 풀을 만들었으므로 이제 RemoteApps로 유효성 검사 호스트 풀을 선택적으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
