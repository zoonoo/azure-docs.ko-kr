---
title: Windows 가상 데스크톱 부하 분산 방법 구성 (미리 보기)-Azure
description: Windows 가상 데스크톱 환경에 대 한 부하 분산 방법을 구성 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318346"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows 가상 데스크톱 부하 분산 방법 구성

호스트 풀에 대 한 부하 분산 방법을 구성할 Windows Virtual Desktop (미리 보기) 환경을 더 잘 맞게 요구 사항을 조정할 수 있습니다.

>[!NOTE]
> 있으므로 사용자가 항상 1:1 매핑이 세션 호스트에 호스트 풀 내의 영구 데스크톱 호스트 풀에 적용 되지 않습니다.

## <a name="configure-breadth-first-load-balancing"></a>너비 우선 부하 분산 구성

너비 우선 부하 분산은 새 비 영구적인 호스트 풀에 대 한 기본 구성입니다. 새 사용자 세션을 호스트 풀의 모든 사용 가능한 세션 호스트 분산 너비 우선 부하 분산. 너비 우선 부하 분산을 구성할 때 호스트 풀의 최대 세션 제한 세션 호스트당를 설정할 수 있습니다.

먼저 [다운로드 하 여 Windows 가상 데스크톱 PowerShell 모듈을 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 아직 없는 경우 PowerShell 세션에서 사용 하도록 합니다.

최대 세션 제한을 조정 하지 않고 너비 우선을 분산 하는 데 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

너비 우선 부하 분산을 수행 하 고 새 최대 세션 제한을 사용 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>깊이 우선 부하 분산 구성

깊이 우선 부하 분산에서 가장 많은 연결을 사용 하 여 사용 가능한 세션 호스트에 새 사용자 세션을 분산은 해당 최대 세션 제한 임계값에 도달 하지. 깊이 우선 부하 분산을 구성 하는 경우 있습니다 **해야** 호스트 풀의 최대 세션 제한 세션 호스트당를 설정 합니다.

깊이 우선 부하 분산을 수행 하려면 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
