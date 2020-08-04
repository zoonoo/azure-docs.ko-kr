---
title: Windows Virtual Desktop 호스트 풀 서비스 업데이트 - Azure
description: 프로덕션에 업데이트를 출시하기 전에 서비스 업데이트를 모니터링하는 유효성 검사 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 177763e7939de082faa0c83d2ab661292f0758b2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292650"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>자습서: 서비스 업데이트의 유효성을 검사하기 위한 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)를 참조하세요.

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 호스트 풀을 프로덕션 환경에 배포하기 전에 유효성 검사 호스트 풀을 만드는 것이 좋습니다. 서비스 업데이트는 프로덕션 환경에 배포되기 전에 모니터링할 수 있도록 유효성 검사 호스트 풀에 먼저 적용됩니다. 유효성 검사 호스트 풀이 없으면 오류를 유발하는 변경을 감지하지 못하여 프로덕션 환경의 사용자에게 가동 중지 시간이 발생할 수 있습니다.

앱에서 최신 업데이트가 문제 없이 작동되려면 유효성 검사 호스트 풀이 프로덕션 환경의 호스트 풀과 최대한 유사해야 합니다. 사용자는 프로덕션 호스트 풀과 마찬가지로, 유효성 검사 호스트 풀에도 자주 연결해야 합니다. 호스트 풀에 대한 테스트를 자동화한 경우 유효성 검사 호스트 풀에 자동화된 테스트를 포함해야 합니다.

[진단 기능](diagnostics-role-service.md) 또는 [Windows Virtual Desktop 문제 해결 문서](troubleshoot-set-up-overview.md) 중 하나를 사용하여 유효성 검사 호스트 풀의 문제를 디버그할 수 있습니다.

>[!NOTE]
> 모든 후속 업데이트를 테스트하기 위해 유효성 검사 호스트 풀을 그대로 두는 것이 좋습니다.

>[!IMPORTANT]
>Azure Resource Management가 통합된 Windows Virtual Desktop에 현재 유효성 검사 환경을 설정하고 해제하는 데 문제가 있습니다. 문제가 해결되면 이 문서를 업데이트할 예정입니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 PowerShell 모듈을 설정하고 Azure에 로그인하려면 [Windows Virtual Desktop PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="create-your-host-pool"></a>호스트 풀 만들기

다음 문서 중 하나에 제공된 지침에 따라 호스트 풀을 만들 수 있습니다.
- [자습서: Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>호스트 풀을 유효성 검사 호스트 풀로 정의

다음 PowerShell cmdlet을 실행하여 새 호스트 풀을 유효성 검사 호스트 풀로 정의합니다. 괄호 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

다음 PowerShell cmdlet을 실행하여 유효성 검사 속성이 설정되어 있는지 확인합니다. 괄호 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

cmdlet의 결과는 다음 출력과 비슷해야 합니다.

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>업데이트 일정

매월 서비스 업데이트가 수행됩니다. 주요 이슈가 있는 경우 중요 업데이트가 좀 더 자주 제공됩니다.

서비스 업데이트가 있는 경우 환경의 유효성을 검사하려면 매일 로그인하는 작은 사용자 그룹이 있어야 합니다. [TechCommunity 사이트](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true)를 정기적으로 방문하여 서비스 업데이트에 대한 최신 정보를 제공하는 WVDUPdate에 대한 게시물을 따르는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 유효성 검사 호스트 풀을 만들었으므로 Azure Service Health를 사용하여 Windows Virtual Desktop 배포를 모니터링하는 방법을 배울 수 있습니다. 

> [!div class="nextstepaction"]
> [서비스 경고 설정](./set-up-service-alerts.md)
