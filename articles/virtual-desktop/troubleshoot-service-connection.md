---
title: 서비스 연결 문제 해결 Windows 가상 데스크톱-Azure
description: Windows 가상 데스크톱 테 넌 트 환경에서 클라이언트 연결을 설정할 때 발생 하는 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612642"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 가상 데스크톱 서비스 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서를 사용 하 여 Windows 가상 데스크톱 클라이언트 연결 문제를 해결할 수 있습니다.

## <a name="provide-feedback"></a>피드백 제공

사용자 의견을 제공 하 고 windows 가상 [데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 제품 팀 및 기타 활성 커뮤니티 구성원에 게 Windows 가상 데스크톱 서비스에 대해 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결 되어 있지만 아무것도 표시 되지 않습니다 (피드 없음).

사용자가 원격 데스크톱 클라이언트를 시작 하 고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시 되지 않습니다.

다음 명령줄을 사용 하 여 문제를 보고 하는 사용자가 응용 프로그램 그룹에 할당 되었는지 확인 합니다.

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

사용자가 올바른 자격 증명을 사용 하 여 로그인 하 고 있는지 확인 합니다.

웹 클라이언트를 사용 하는 경우 캐시 된 자격 증명 문제가 없는지 확인 합니다.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 Enterprise 다중 세션 가상 컴퓨터가 응답 하지 않음

가상 컴퓨터가 응답 하지 않으며 RDP를 통해 액세스할 수 없는 경우 호스트 상태를 확인 하 여 진단 기능으로 문제를 해결 해야 합니다.

호스트 상태를 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

호스트 상태가 인 경우 VM `NoHeartBeat`이 응답 하지 않으며 에이전트가 Windows 가상 데스크톱 서비스와 통신할 수 없음을 의미 합니다.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

NoHeartBeat 비트 상태를 수정 하기 위해 수행할 수 있는 몇 가지 작업이 있습니다.

### <a name="update-fslogix"></a>FSLogix 업데이트

FSLogix가 최신이 아닌 경우, 특히 frxdrvvt의 버전인 2.9.7205.27375 경우 교착 상태가 발생할 수 있습니다. [FSLogix를 최신 버전으로 업데이트](https://go.microsoft.com/fwlink/?linkid=2084562)해야 합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경에서 Windows 가상 데스크톱 환경 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱과 함께 PowerShell을 사용할 때 발생 하는 문제를 해결 하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하세요.
- 문제 해결 자습서를 진행 하려면 [자습서: 템플릿 배포 리소스 관리자 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조 하세요.
