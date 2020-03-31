---
title: Windows 가상 데스크톱 로드 밸런싱 구성 - Azure
description: Windows 가상 데스크톱 환경에 대한 로드 균형 조정 방법을 구성하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128297"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows Virtual Desktop 부하 분산 방법 구성

호스트 풀에 대한 로드 밸런싱 방법을 구성하면 필요에 맞게 Windows 가상 데스크톱 환경을 조정할 수 있습니다.

>[!NOTE]
> 사용자가 항상 호스트 풀 내의 세션 호스트에 1:1 매핑을 갖기 때문에 영구 데스크톱 호스트 풀에는 적용되지 않습니다.

## <a name="configure-breadth-first-load-balancing"></a>폭 우선 부하 분산 구성

너비 우선 로드 분산은 새 비영구 호스트 풀의 기본 구성입니다. 너비 우선 로드 분산은 호스트 풀에서 사용 가능한 모든 세션 호스트에 새 사용자 세션을 배포합니다. 너비 우선 로드 밸런싱을 구성할 때 호스트 풀에서 세션 호스트당 최대 세션 제한을 설정할 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

최대 세션 제한을 조정하지 않고 광범위한 첫 번째 부하 분산을 수행하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

광범위한 첫 번째 로드 균형 조정을 수행하고 새 최대 세션 제한을 사용하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>깊이 우선 부하 분산 구성

깊이 우선 로드 분산은 연결 수가 가장 많은 사용 가능한 세션 호스트에 새 사용자 세션을 배포하지만 최대 세션 제한 임계값에 도달하지 않았습니다. 깊이 우선 로드 밸런싱을 구성할 때호스트 풀에서 세션 호스트당 최대 세션 제한을 **설정해야 합니다.**

깊이 우선 부하 분산을 수행하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
