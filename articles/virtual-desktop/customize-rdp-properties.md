---
title: PowerShell - Azure를 통해 RDP 속성 사용자 지정
description: PowerShell cmdlet으로 Windows 가상 데스크톱용 RDP 속성을 사용자 지정하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128070"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀에 대한 원격 데스크톱 프로토콜 속성 사용자 지정

멀티 모니터 환경 및 오디오 리디렉션과 같은 호스트 풀의 RDP(원격 데스크톱 프로토콜) 속성을 사용자 지정하면 사용자의 필요에 따라 최적의 환경을 제공할 수 있습니다. **설정 RdsHostPool** cmdlet의 **-CustomRdpProperty** 매개 변수를 사용하여 Windows 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다.

지원되는 속성 및 해당 기본값의 전체 목록은 [지원되는 RDP 파일 설정을](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) 참조하십시오.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>기본 RDP 속성

기본적으로 게시된 RDP 파일에는 다음 속성이 포함됩니다.

|RDP 속성 | 데스크톱 | RemoteApp |
|---|---| --- |
| 멀티 모니터 모드 | 사용 | 해당 없음 |
| 드라이브 리디렉션 사용 가능 | 드라이브, 클립보드, 프린터, COM 포트, USB 장치 및 스마트 카드| 드라이브, 클립보드 및 프린터 |
| 원격 오디오 모드 | 로컬로 재생 | 로컬로 재생 |

호스트 풀에 대해 정의한 모든 사용자 지정 속성은 이러한 기본값을 재정의합니다.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>단일 사용자 지정 RDP 속성 추가 또는 편집

단일 사용자 지정 RDP 속성을 추가하거나 편집하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![이름과 친근한 이름이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린 샷.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>여러 사용자 지정 RDP 속성 추가 또는 편집

여러 사용자 지정 RDP 속성을 추가하거나 편집하려면 사용자 지정 RDP 속성을 세미콜론으로 구분된 문자열로 제공하여 다음 PowerShell cmdlet을 실행합니다.

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![이름과 친근한 이름이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린 샷.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>모든 사용자 지정 RDP 속성 재설정

단일 사용자 지정 RDP 속성 [추가 또는 편집의](#add-or-edit-a-single-custom-rdp-property)지침에 따라 개별 사용자 지정 RDP 속성을 기본값으로 재설정하거나 다음 PowerShell cmdlet을 실행하여 호스트 풀의 모든 사용자 지정 RDP 속성을 재설정할 수 있습니다.

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![이름과 친근한 이름이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린 샷.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>다음 단계

지정된 호스트 풀에 맞게 RDP 속성을 사용자 지정한 후 Windows 가상 데스크톱 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 가지 방법 설명은 선택한 클라이언트를 사용하여 세션에 연결하는 방법을 알려줍니다.

- [Windows Desktop 클라이언트와 연결](connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
