---
title: PowerShell을 사용 하 여 RDP 속성 사용자 지정-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱에 대 한 RDP 속성을 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: helohr
ms.openlocfilehash: 624edaea9a0fb56e34eb83f033dfdab64985bd5c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950719"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정

다중 모니터 환경 및 오디오 리디렉션과 같은 RDP (호스트 풀의 원격 데스크톱 프로토콜) 속성을 사용자 지정 하면 요구 사항에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다. **Set RdsHostPool** cmdlet의 **-customrdpproperty** 매개 변수를 사용 하 여 WINDOWS 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다.

지원 되는 속성 및 해당 기본값의 전체 목록은 [원격 데스크톱 RDP 파일 설정](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) 을 참조 하세요.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

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

이제 지정 된 호스트 풀의 RDP 속성을 사용자 지정 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 이렇게 하려면 Windows 가상 데스크톱 방법에 연결을 계속 진행 합니다.

- [Windows 10 및 Windows 7에서 연결](connect-windows-7-and-10.md)
- [웹 브라우저에서 연결](connect-web.md)
