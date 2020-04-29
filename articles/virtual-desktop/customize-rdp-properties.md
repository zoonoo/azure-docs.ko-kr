---
title: PowerShell을 사용 하 여 RDP 속성 사용자 지정-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱에 대 한 RDP 속성을 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128070"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정

다중 모니터 환경 및 오디오 리디렉션과 같은 RDP (호스트 풀의 원격 데스크톱 프로토콜) 속성을 사용자 지정 하면 요구 사항에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다. **Set RdsHostPool** cmdlet의 **-customrdpproperty** 매개 변수를 사용 하 여 WINDOWS 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다.

지원 되는 속성 및 해당 기본값의 전체 목록은 [지원 되는 RDP 파일 설정](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) 을 참조 하세요.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>기본 RDP 속성

기본적으로 게시 된 RDP 파일에는 다음 속성이 포함 됩니다.

|RDP 속성 | 데스크톱 | RemoteApp |
|---|---| --- |
| 다중 모니터 모드 | 사용 | 해당 없음 |
| 드라이브 리디렉션 사용 | 드라이브, 클립보드, 프린터, COM 포트, USB 장치 및 스마트 카드| 드라이브, 클립보드 및 프린터 |
| 원격 오디오 모드 | 로컬로 재생 | 로컬로 재생 |

호스트 풀에 대해 정의 하는 모든 사용자 지정 속성은 이러한 기본값을 재정의 합니다.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>단일 사용자 지정 RDP 속성 추가 또는 편집

단일 사용자 지정 RDP 속성을 추가 하거나 편집 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Name 및 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>여러 사용자 지정 RDP 속성 추가 또는 편집

여러 사용자 지정 RDP 속성을 추가 하거나 편집 하려면 사용자 지정 RDP 속성을 세미콜론으로 구분 된 문자열로 제공 하 여 다음 PowerShell cmdlet을 실행 합니다.

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Name 및 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>모든 사용자 지정 RDP 속성 다시 설정

[단일 사용자 지정 rdp 속성 추가 또는 편집](#add-or-edit-a-single-custom-rdp-property)의 지침에 따라 개별 사용자 지정 rdp 속성을 기본값으로 다시 설정 하거나, 다음 PowerShell cmdlet을 실행 하 여 호스트 풀의 모든 사용자 지정 rdp 속성을 다시 설정할 수 있습니다.

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Name 및 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>다음 단계

이제 지정 된 호스트 풀의 RDP 속성을 사용자 지정 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 가지 방법으로 사용자가 선택한 클라이언트를 사용 하 여 세션에 연결 하는 방법을 알 수 있습니다.

- [Windows Desktop 클라이언트와 연결](connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
