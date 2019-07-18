---
title: PowerShell-Azure 사용 하 여 RDP 속성을 사용자 지정
description: PowerShell cmdlet을 사용 하 여 RDP 속성에 대 한 Windows 가상 데스크톱을 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: ce14f990272fa1e70d07c0f4a1f18025b536eccc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618875"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀에 대 한 원격 데스크톱 프로토콜 속성을 사용자 지정

다중 모니터 환경 및 오디오 리디렉션을 같은 호스트 풀의 원격 데스크톱 프로토콜 (RDP) 속성을 사용자 지정 필요에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다. 사용 하 여 Windows 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다 합니다 **-CustomRdpProperty** 에 매개 변수를 **집합 RdsHostPool** cmdlet.

참조 [원격 데스크톱 RDP 파일 설정](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) 지원 되는 속성 및 기본 값의 전체 목록입니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

## <a name="add-or-edit-a-single-custom-rdp-property"></a>추가 하거나 단일 사용자 지정 RDP 속성 편집

를 추가 하거나 단일 사용자 지정 RDP 속성을 편집 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![PowerShell cmdlet의 스크린샷 Get-RDSRemoteApp 이름과 FriendlyName를 사용 하 여 강조 표시 합니다.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>여러 사용자 지정 RDP 속성 추가 또는 편집

를 추가 하거나 여러 사용자 지정 RDP 속성을 편집 하려면 사용자 지정 RDP 속성을 세미콜론으로 구분 된 문자열로 제공 하 여 다음 PowerShell cmdlet을 실행 합니다.

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![PowerShell cmdlet의 스크린샷 Get-RDSRemoteApp 이름과 FriendlyName를 사용 하 여 강조 표시 합니다.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>모든 사용자 지정 RDP 속성 다시 설정

개별 사용자 지정 RDP 속성의 지침에 따라 해당 기본값으로 다시 설정할 수 [추가 또는 편집 단일 사용자 지정 RDP 속성](#add-or-edit-a-single-custom-rdp-property), 다음 실행 하 여 호스트 풀에 대 한 모든 사용자 지정 RDP 속성을 다시 설정할 수 있습니다 또는 PowerShell cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![PowerShell cmdlet의 스크린샷 Get-RDSRemoteApp 이름과 FriendlyName를 사용 하 여 강조 표시 합니다.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>다음 단계

지정 된 호스트 풀에 대 한 RDP 속성을 사용자 지정한 했으므로 로그인 할 수 있습니다 Windows 가상 데스크톱 클라이언트에 사용자 세션의 일부로 테스트할 합니다. 이렇게 하려면 Windows 가상 데스크톱 방법 연결할 계속 합니다.

- [Windows 10 및 Windows 7에서 연결](connect-windows-7-and-10.md)
- [웹 브라우저에서 연결](connect-web.md)
