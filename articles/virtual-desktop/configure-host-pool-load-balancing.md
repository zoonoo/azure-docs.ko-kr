---
title: Windows 가상 데스크톱 부하 분산 구성-Azure
description: Windows 가상 데스크톱 환경에 대 한 부하 분산 방법을 구성 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 61c11e342f3b4f906b453e0962018a8f8c34acd4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605869"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows Virtual Desktop 부하 분산 방법 구성

호스트 풀에 대해 부하 분산 방법을 구성 하면 사용자의 요구에 맞게 Windows 가상 데스크톱 환경을 조정할 수 있습니다.

>[!NOTE]
> 사용자에 게는 항상 호스트 풀 내의 세션 호스트에 대 한 1:1 매핑이 있으므로 영구 데스크톱 호스트 풀에는이 내용이 적용 되지 않습니다.

## <a name="configure-breadth-first-load-balancing"></a>너비 우선 부하 분산 구성

너비 우선 부하 분산은 새로운 비영구 호스트 풀의 기본 구성입니다. 너비 우선 부하 분산은 호스트 풀에서 사용 가능한 모든 세션 호스트에 새 사용자 세션을 배포 합니다. 너비 우선 부하 분산을 구성할 때 호스트 풀의 세션당 최대 세션 제한 수를 설정할 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

최대 세션 제한을 조정 하지 않고 너비 우선 부하 분산을 수행 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

너비 우선 부하 분산을 수행 하 고 새 최대 세션 제한을 사용 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>깊이 우선 부하 분산 구성

깊이 우선 부하 분산은 연결 수가 가장 많은 사용 가능한 세션 호스트에 새 사용자 세션을 배포 하지만 최대 세션 제한 임계값에 도달 하지 않았습니다. 깊이 우선 부하 분산을 구성 하는 경우 호스트 풀에서 세션당 최대 세션 제한을 설정 **해야 합니다** .

깊이 우선 부하 분산을 수행 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
