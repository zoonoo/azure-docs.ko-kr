---
title: 온-프레미스 Azure AD 암호 보호 문제 해결
description: 온-프레미스 Active Directory 도메인 서비스 환경에 대한 Azure AD 암호 보호 문제를 해결하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263648"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>문제 해결: 온-프레미스 Azure AD 암호 보호

Azure AD 암호 보호를 배포한 후 문제를 해결해야 할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 해결 단계를 이해하는 데 유용한 세부 정보를 제공합니다.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 에이전트가 디렉터리에서 프록시를 찾을 수 없습니다.

이 문제의 주요 증상은 DC 에이전트 Admin 이벤트 로그의 30017 이벤트입니다.

이 문제의 일반적인 원인은 프록시가 아직 등록되지 않았기 때문에 발생합니다. 프록시가 등록된 경우 특정 DC 에이전트가 해당 프록시를 볼 수 있을 때까지 AD 복제 대기 시간으로 인해 약간의 지연이 있을 수 있습니다.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 에이전트가 프록시와 통신할 수 없습니다.

이 문제의 주요 증상은 DC 에이전트 Admin 이벤트 로그의 30018 이벤트입니다. 이 문제에는 다음과 같은 몇 가지 원인이 있을 수 있습니다.

1. DC 에이전트는 등록된 프록시에 대한 네트워크 연결을 허용하지 않는 네트워크의 격리된 부분에 있습니다. 이 문제는 다른 DC 에이전트가 Azure에서 암호 정책을 다운로드하기 위해 프록시와 통신할 수 있는 한 문제가 적용되지 않을 수 있습니다. 일단 다운로드되면 이러한 정책은 sysvol 공유의 정책 파일 복제를 통해 격리된 DC에서 가져옵니다.

1. 프록시 호스트 컴퓨터가 RPC 끝점 매퍼 끝점(포트 135)에 대한 액세스를 차단하고 있습니다.

   Azure AD 암호 보호 프록시 설치 관리자는 포트 135에 대한 액세스를 허용하는 Windows 방화벽 인바운드 규칙을 자동으로 만듭니다. 이 규칙이 나중에 삭제되거나 비활성화되면 DC 에이전트가 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품 대신 기본 제공 Windows 방화벽이 비활성화된 경우 포트 135에 대한 액세스를 허용하도록 해당 방화벽을 구성해야 합니다.

1. 프록시 호스트 컴퓨터가 프록시 서비스에서 수신 대기하는 RPC 끝점(동적 또는 정적)에 대한 액세스를 차단하고 있습니다.

   Azure AD 암호 보호 프록시 설치 관리자는 Azure AD 암호 보호 프록시 서비스에서 수신 대기하는 모든 인바운드 포트에 액세스할 수 있는 Windows 방화벽 인바운드 규칙을 자동으로 만듭니다. 이 규칙이 나중에 삭제되거나 비활성화되면 DC 에이전트가 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품 대신 기본 제공 Windows 방화벽을 사용하지 않도록 설정한 경우 Azure AD 암호 보호 프록시 서비스에서 수신 대기하는 모든 인바운드 포트에 대한 액세스를 허용하도록 해당 방화벽을 구성해야 합니다. 프록시 서비스가 cmdlet을 사용하여 특정 정적 RPC 포트에서 수신하도록 구성된 경우 `Set-AzureADPasswordProtectionProxyConfiguration` 이 구성을 보다 구체적으로 지정할 수 있습니다.

1. 프록시 호스트 컴퓨터는 도메인 컨트롤러가 컴퓨터에 로그온할 수 있도록 구성되지 않았습니다. 이 동작은 "네트워크에서 이 컴퓨터에 액세스" 사용자 권한 할당을 통해 제어됩니다. 포리스트의 모든 도메인에 있는 모든 도메인 컨트롤러에는 이 권한이 부여되어야 합니다. 이 설정은 대규모 네트워크 강화 작업의 일부로 제한되는 경우가 많습니다.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>프록시 서비스가 Azure와 통신할 수 없습니다.

1. 프록시 컴퓨터가 [배포 요구 사항에](howto-password-ban-bad-on-premises-deploy.md)나열된 끝점에 연결되어 있는지 확인합니다.

1. 포리스트와 모든 프록시 서버가 동일한 Azure 테넌트에 대해 등록되어 있는지 확인합니다.

   `Get-AzureADPasswordProtectionProxy` PowerShell cmdlet을 `Get-AzureADPasswordProtectionDCAgent` 실행하여 이 요구 사항을 확인한 다음 반환된 각 항목의 `AzureTenant` 속성을 비교할 수 있습니다. 올바른 작업을 위해 보고된 테넌트 이름은 모든 DC 에이전트 및 프록시 서버에서 동일해야 합니다.

   Azure 테넌트 등록 불일치 조건이 있는 경우 필요에 따라 `Register-AzureADPasswordProtectionProxy` 및/또는 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet을 실행하여 모든 등록에 대해 동일한 Azure 테넌트의 자격 증명을 사용해야 합니다.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 에이전트가 암호 정책 파일을 암호화하거나 해독할 수 없습니다.

Azure AD 암호 보호는 Microsoft 키 배포 서비스에서 제공하는 암호화 및 암호 해독 기능에 중요한 종속성을 가집니다. 암호화 또는 암호 해독 실패는 다양한 증상으로 나타날 수 있으며 몇 가지 잠재적인 원인이 있을 수 있습니다.

1. 도메인의 모든 Windows Server 2012 및 이후 도메인 컨트롤러에서 KDS 서비스가 활성화되고 작동해야 합니다.

   기본적으로 KDS 서비스의 서비스 시작 모드는 수동(트리거 시작)으로 구성됩니다. 이 구성은 클라이언트가 서비스를 처음 사용할 때 온디맨드로 시작된다는 것을 의미합니다. 이 기본 서비스 시작 모드는 Azure AD 암호 보호가 작동하도록 허용됩니다.

   KDS 서비스 시작 모드가 비활성화하도록 구성된 경우 Azure AD 암호 보호가 제대로 작동하기 전에 이 구성을 수정해야 합니다.

   이 문제에 대한 간단한 테스트는 서비스 관리 MMC 콘솔을 통해 또는 다른 관리 도구(예: 명령 프롬프트 콘솔에서 "net start kdssvc"실행)를 사용하여 KDS 서비스를 수동으로 시작하는 것입니다. KDS 서비스는 성공적으로 시작되어 계속 실행될 것으로 예상됩니다.

   KDS 서비스를 시작할 수 없는 가장 일반적인 근본 원인은 Active Directory 도메인 컨트롤러 개체가 기본 도메인 컨트롤러 OU 외부에 위치하기 때문에 입니다. 이 구성은 KDS 서비스에서 지원되지 않으며 Azure AD 암호 보호에 의해 부과되는 제한사항이 아닙니다. 이 조건에 대 한 수정 프로그램은 기본 도메인 컨트롤러 OU 아래 위치로 도메인 컨트롤러 개체를 이동 하는 것입니다.

1. 호환되지 않는 KDS 암호화된 버퍼 형식은 Windows 서버 2012 R2에서 Windows 서버 2016으로 변경되었습니다.

   KDS 암호화 버퍼의 형식을 수정하는 Windows Server 2016에 KDS 보안 수정 프로그램이 도입되었습니다. 이러한 버퍼는 Windows Server 2012 및 Windows Server 2012 R2에서 해독하지 못하는 경우가 있습니다. 반대 방향은 괜찮습니다 - Windows Server 2012 및 Windows Server 2012 R2에서 KDS 암호화 된 버퍼는 항상 Windows Server 2016 이상에서 성공적으로 해독됩니다. Active Directory 도메인의 도메인 컨트롤러가 이러한 운영 체제를 혼합하여 실행하는 경우 가끔 Azure AD 암호 보호 암호 해독 오류가 보고될 수 있습니다. 보안 수정의 특성을 고려할 때 이러한 오류의 타이밍이나 증상을 정확하게 예측할 수 없으며 지정된 시간에 데이터를 암호화하는 Azure AD 암호 보호 DC 에이전트가 결정적이지 않다는 점을 감안할 때 이러한 오류의 타이밍이나 증상을 정확하게 예측할 수 없습니다.

   Microsoft는 이 문제에 대한 수정 프로그램을 조사 중이지만 아직 ETA를 사용할 수 없습니다. 그 동안 Active Directory 도메인에서 이러한 호환되지 않는 운영 체제를 혼합하여 실행하지 않는 것 외에는 이 문제에 대한 해결 방법이 없습니다. 즉, Windows Server 2012 및 Windows Server 2012 R2 도메인 컨트롤러만 실행하거나 Windows Server 2016 이상 도메인 컨트롤러만 실행해야 합니다.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>약한 암호는 수락되고 있지만

이 문제에는 여러 가지 원인이 있을 수 있습니다.

1. DC 에이전트가 만료된 공개 미리 보기 소프트웨어 버전을 실행하고 있습니다. [공개 미리 보기 DC 에이전트 소프트웨어가 만료되었습니다.](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)

1. DC 에이전트는 정책을 다운로드할 수 없거나 기존 정책을 해독할 수 없습니다. 위의 항목에서 가능한 원인을 확인합니다.

1. 암호 정책 강제 적용 모드가 여전히 감사로 설정되어 있습니다. 이 구성이 적용되는 경우 Azure AD 암호 보호 포털을 사용하여 적용하도록 다시 구성합니다. 자세한 내용은 [작업 모드를 참조하십시오.](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)

1. 암호 정책이 사용하지 않도록 설정되어 있습니다. 이 구성이 적용되는 경우 Azure AD 암호 보호 포털을 사용하여 사용하도록 다시 구성합니다. 자세한 내용은 [작업 모드를 참조하십시오.](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)

1. 도메인의 모든 도메인 컨트롤러에 DC 에이전트 소프트웨어를 설치하지 않았습니다. 이 경우 원격 Windows 클라이언트가 암호 변경 작업 중에 특정 도메인 컨트롤러를 대상으로 하는지 확인하기가 어렵습니다. DC 에이전트 소프트웨어가 설치된 특정 DC를 성공적으로 타겟팅했다고 생각되면 DC 에이전트 관리자 이벤트 로그를 다시 확인하여 확인할 수 있습니다. 유효성 검사. 암호가 변경된 사용자에 대한 이벤트가 없는 경우 암호 변경은 다른 도메인 컨트롤러에서 처리되었을 가능성이 높습니다.

   다른 테스트로 DC 에이전트 소프트웨어가 설치된 DC에 직접 로그인하는 동안 암호 변경을 설정해 보십시오. 이 기술은 프로덕션 용 Active Directory 도메인에는 권장되지 않습니다.

   DC 에이전트 소프트웨어의 증분 배포는 이러한 제한 사항에 따라 지원되지만 Microsoft는 DC 에이전트 소프트웨어가 가능한 한 빨리 도메인의 모든 도메인 컨트롤러에 설치되는 것이 좋습니다.

1. 암호 유효성 검사 알고리즘이 실제로 예상대로 작동할 수 있습니다. [암호를 평가하는 방법을](concept-password-ban-bad.md#how-are-passwords-evaluated)참조하십시오.

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe 약한 DSRM 암호를 설정 하지 못합니다.

Active Directory는 항상 새 디렉터리 서비스 복구 모드 암호의 유효성을 검사하여 도메인의 암호 복잡성 요구 사항을 충족하는지 확인합니다. 이 유효성 검사는 Azure AD 암호 보호와 같은 암호 필터 dlls를 호출합니다. 새 DSRM 암호가 거부되면 다음과 같은 오류 메시지가 나타납니다.

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Azure AD 암호 보호가 Active Directory DSRM 암호에 대한 암호 유효성 검사 이벤트 로그 이벤트를 기록하면 이벤트 로그 메시지에 사용자 이름이 포함되지 않을 것으로 예상됩니다. 이 문제는 DSRM 계정이 실제 Active Directory 도메인의 일부가 아닌 로컬 계정이기 때문에 발생합니다.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>DSRM 암호가 약하여 도메인 컨트롤러 복제본 승격에 실패

DC 승격 프로세스 중에 유효성 검사를 위해 도메인의 기존 DC에 새 디렉터리 서비스 복구 모드 암호가 제출됩니다. 새 DSRM 암호가 거부되면 다음과 같은 오류 메시지가 나타납니다.

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

위의 문제와 마찬가지로 Azure AD 암호 보호 암호 유효성 검사 결과 이벤트에는 이 시나리오에 대한 빈 사용자 이름이 있습니다.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>약한 로컬 관리자 암호로 인해 도메인 컨트롤러 강등 실패

여전히 DC 에이전트 소프트웨어를 실행하는 도메인 컨트롤러의 수준을 내리기 위해 지원됩니다. 하지만 관리자는 DC 에이전트 소프트웨어가 수준 내리기 절차 중 현재 암호 정책을 계속 실행하고 적용한다는 것을 인식해야 합니다. 새 로컬 관리자 계정 암호(수준 내리기 작업의 일부로 지정됨)는 다른 모든 암호와 같이 유효성을 검사합니다. DC 강등 절차의 일부로 로컬 관리자 계정에 대해 보안 암호를 선택할 것을 권장합니다.

수준 내리기가 성공하고 도메인 컨트롤러가 다시 부팅되고 일반 멤버 서버로 다시 실행되면 DC 에이전트 소프트웨어는 수동 모드 실행으로 되돌아갑니다. 언제든 제거될 수 있습니다.

## <a name="booting-into-directory-services-repair-mode"></a>디렉터리 서비스 복구 모드로 부팅

도메인 컨트롤러가 디렉터리 서비스 복구 모드로 부팅되면 DC 에이전트 암호 필터 dll이 이 조건을 감지하고 현재 활성 정책에 관계없이 모든 암호 유효성 검사 또는 적용 활동을 사용하지 않도록 설정합니다. 구성. DC 에이전트 암호 필터 dll 은 10023 경고 이벤트를 Admin 이벤트 로그에 기록합니다.

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>공개 미리 보기 DC 에이전트 소프트웨어가 만료되었습니다.

Azure AD 암호 보호 공개 미리 보기 기간 동안 DC 에이전트 소프트웨어는 다음 날짜에 암호 유효성 검사 요청 처리를 중지하도록 하드 코딩되었습니다.

* 버전 1.2.65.0은 2019년 9월 1일에 암호 유효성 검사 요청 처리를 중지합니다.
* 버전 1.2.25.0 및 2019년 7월 1일에 암호 유효성 검사 요청 처리를 중지했습니다.

기한이 다가오면 모든 시간 제한 DC 에이전트 버전은 부팅 시 DC 에이전트 Admin 이벤트 로그에 다음과 같은 10021 이벤트를 내보올 것입니다.

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

기한이 지나면 모든 시간 제한 DC 에이전트 버전은 부팅 시 DC 에이전트 Admin 이벤트 로그에 다음과 같은 10022 이벤트를 내보올 수 있습니다.

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

기한은 초기 부팅 시에만 확인되므로 달력 기한이 지난 후까지 이러한 이벤트가 표시되지 않을 수 있습니다. 기한이 인식되면 모든 암호가 자동으로 승인되는 것 이외에는 도메인 컨트롤러 나 더 큰 환경에 부정적인 영향이 발생하지 않습니다.

> [!IMPORTANT]
> 만료된 공개 미리 보기 DC 에이전트를 즉시 최신 버전으로 업그레이드하는 것이 좋습니다.

업그레이드해야 하는 환경에서 DC 에이전트를 쉽게 검색할 수 `Get-AzureADPasswordProtectionDCAgent` 있는 방법은 cmdlet을 실행하는 것입니다.

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

이 항목의 경우 SoftwareVersion 필드는 분명히 살펴볼 핵심 속성입니다. PowerShell 필터링을 사용하여 필요한 기준 버전 이상에 이미 있는 DC 에이전트를 필터링할 수도 있습니다.

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD 암호 보호 프록시 소프트웨어는 모든 버전에서 시간 제한이 없습니다. Microsoft는 DC 에이전트와 프록시 에이전트가 릴리스될 때 모두 최신 버전으로 업그레이드하는 것이 좋습니다. cmdlet은 `Get-AzureADPasswordProtectionProxy` DC 에이전트의 경우 위의 예제와 유사하게 업그레이드가 필요한 프록시 에이전트를 찾는 데 사용할 수 있습니다.

특정 [업그레이드](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) 절차에 대한 자세한 내용은 DC 에이전트 업그레이드 및 [프록시 서비스 업그레이드를](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) 참조하십시오.

## <a name="emergency-remediation"></a>응급 업데이트 관리

DC 에이전트 서비스가 문제를 일으키는 상황이 발생하면 DC 에이전트 서비스는 즉시 종료될 수 있습니다. DC 에이전트 암호 필터 dll은 여전히 실행 중이 아닌 서비스를 호출하고 경고 이벤트(10012, 10013)를 기록하지만 해당 시간 동안 들어오는 모든 암호는 허용됩니다. DC 에이전트 서비스는 필요에 따라 "Disabled" 시작 유형을 사용하여 Windows 서비스 제어 관리자를 통해 구성될 수 있습니다.

다른 수정 측정값을 Azure AD 암호 보호 포털에서 사용 모드를 안 함으로 설정할 수 있습니다. 업데이트된 정책이 다운로드되면, 각 DC 에이전트 서비스는 모든 암호가 있는 그대로 허용되는 정지 모드가 됩니다. 자세한 내용은 [작업 모드를 참조하십시오.](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)

## <a name="removal"></a>제거

Azure AD 암호 보호 소프트웨어를 제거하고 도메인 및 포리스트에서 모든 관련 상태를 정리하기로 결정한 경우 이 작업은 다음 단계를 사용하여 수행할 수 있습니다.

> [!IMPORTANT]
> 이러한 단계를 순서대로 수행하는 것이 중요합니다. 프록시 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체를 다시 만듭니다. DC 에이전트 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체 및 sysvol 상태를 다시 만듭니다.

1. 모든 머신에서 프록시 소프트웨어를 제거합니다. 이 단계는 재부팅이 필요하지 **않습니다**.
2. 모든 도메인 컨트롤러에서 DC 에이전트 소프트웨어를 제거합니다. 이 단계는 재부팅이 **필요합니다**.
3. 각 도메인 명명 컨텍스트에서 모든 프록시 서비스 연결점을 수동으로 제거합니다. 다음 Active Directory PowerShell 명령을 사용하여 이러한 개체의 위치를 검색할 수 있습니다.

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

4. 각 도메인 명명 컨텍스트에서 모든 DC 에이전트 연결점을 수동으로 제거합니다. 소프트웨어가 배포된 빈도에 따라 포리스트에 도메인 컨트롤러당 이러한 개체가 하나 있을 수 있습니다. 다음 Active Directory PowerShell 명령을 사용하여 해당 개체의 위치를 검색할 수 있습니다.

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

5. 포리스트 수준의 구성 상태를 수동으로 제거합니다. 포리스트 구성 상태는 Active Directory 구성 명명 컨텍스트의 컨테이너에 유지됩니다. 다음과 같이 검색 및 삭제될 수 있습니다.

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 수동으로 다음 폴더와 해당 내용 모두를 삭제하여 모든 sysvol 관련 상태를 수동으로 제거합니다.

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   필요한 경우 이 경로는 기존 도메인 컨트롤러에서 로컬로 액세스될 수도 있으며, 기본 위치는 다음 경로와 같이 표시됩니다.

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Sysvol 공유가 기본이 아닌 위치에 구성된 경우 이 경로는 다릅니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호에 대한 질문과 대답](howto-password-ban-bad-on-premises-faq.md)

전역 및 사용자 지정 금지된 암호 목록에 대한 자세한 내용은 [잘못된 암호 금지](concept-password-ban-bad.md) 문서 참조
