---
title: Windows Virtual Desktop 부하 분산 구성 - Azure
description: Windows Virtual Desktop 환경에 대한 부하 분산 메서드를 구성하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0e742a046d43fef6e8263f73fc2ca8460848ad40
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448120"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows Virtual Desktop 부하 분산 방법 구성

호스트 풀에 대한 부하 분산 메서드를 구성하면 사용자의 요구에 맞게 Windows Virtual Desktop 환경을 조정할 수 있습니다.

>[!NOTE]
> 사용자에게는 항상 호스트 풀 내의 세션 호스트에 1:1 매핑이 있으므로 영구 데스크톱 호스트 풀에는 적용되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [Windows Virtual Desktop PowerShell 모듈 설정](powershell-module.md)의 지침에 따라 PowerShell 모듈을 다운로드 및 설치하고 Azure 계정에 로그인했다고 가정합니다.

## <a name="configure-breadth-first-load-balancing"></a>폭 우선 부하 분산 구성

폭 우선 부하 분산은 새로운 비영구 호스트 풀의 기본 구성입니다. 폭 우선 부하 분산은 호스트 풀에서 사용 가능한 모든 세션 호스트에 새 사용자 세션을 배포합니다. 폭 우선 부하 분산을 구성할 때 호스트 풀의 세션 호스트당 최대 세션 제한을 설정할 수 있습니다.

최대 세션 제한을 조정하지 않고 폭 우선 부하 분산을 수행하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

그런 다음, 폭 우선 부하 분산 방법을 설정했는지 확인하려면 다음 cmdlet을 실행합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

폭 우선 부하 분산을 수행하고 새로운 최대 세션 제한을 사용하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>깊이 우선 부하 분산 구성

깊이 우선 부하 분산은 연결 수가 가장 많지만 최대 세션 제한 임계값에 도달하지 않은 사용 가능한 세션 호스트에 새 사용자 세션을 배포합니다.

>[!IMPORTANT]
>깊이 우선 부하 분산을 구성할 때 호스트 풀의 세션 호스트당 최대 세션 제한을 설정해야 합니다.

깊이 우선 부하 분산을 수행하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> 깊이 우선 부하 분산 알고리즘은 최대 세션 호스트 제한(`-MaxSessionLimit`)에 따라 세션을 세션 호스트에 배포합니다. 이 매개 변수의 기본값은 `999999`입니다. 이 값은 이 변수를 설정할 수 있는 가장 높은 숫자이기도 합니다. 이 매개 변수는 깊이 우선 부하 분산 알고리즘을 사용하는 경우에 필요합니다. 가능한 최상의 사용자 환경을 위해 최대 세션 호스트 제한 매개 변수를 환경에 가장 적합한 숫자로 변경해야 합니다.

설정이 업데이트되었는지 확인하려면 다음 cmdlet을 실행합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Azure Portal을 사용하여 부하 분산 구성

Azure Portal을 사용하여 부하 분산을 구성할 수도 있습니다.

부하 분산을 구성하려면 다음을 수행합니다.

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. 서비스 아래에서 **Windows Virtual Desktop** 을 검색하여 선택합니다.
3. Windows Virtual Desktop 페이지에서 **호스트 풀** 을 선택합니다.
4. 편집하려는 풀의 이름을 선택합니다.
5. **속성** 을 선택합니다.
6. 필드에 **최대 세션 제한** 을 입력하고 드롭다운 메뉴에서 이 호스트 풀에 대해 원하는 **부하 분산 알고리즘** 을 선택합니다.
7. **저장** 을 선택합니다. 이렇게 하면 새 부하 분산 설정이 적용됩니다.