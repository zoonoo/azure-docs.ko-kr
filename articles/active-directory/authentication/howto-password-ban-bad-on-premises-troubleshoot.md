---
title: Azure AD 암호 보호-Azure Active Directory의에서 연결 문제 해결
description: Azure AD 암호 보호 일반적인 문제 해결 방법을 이해 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414769"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 암호 보호 문제 해결

Azure AD 암호 보호를 배포한 후 문제를 해결해야 할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 해결 단계를 이해하는 데 유용한 세부 정보를 제공합니다.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>디렉터리에 DC 에이전트 프록시를 찾을 수 없습니다.

이 문제의 주요 증상 30017 이벤트 DC 에이전트 관리 이벤트 로그의 경우

이 문제의 일반적인 원인은 프록시를 아직 등록 되지 않은 경우 프록시를 등록 하는 경우 있을 약간의 지연이 AD 복제 대기 시간으로 인해 특정 DC 에이전트는 프록시를 볼 때까지.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 에이전트는 프록시를 사용 하 여 통신할 수 없습니다.

이 문제의 주요 증상 30018 이벤트 DC 에이전트 관리 이벤트 로그의 경우 다음과 같은 몇 가지 원인이 있을 수 있습니다.

1. DC 에이전트 등록된 proxy(s)에 대 한 네트워크 연결을 허용 하지 않는 네트워크는 격리 된 부분에 있습니다. 따라서이 문제는 다른 DC 에이전트는 sysvol 공유의 정책 파일 복제를 통해 격리 된 DC에서 가져올 수 있는 Azure의 암호 정책을 다운로드 하기 위해는 proxy(s)와 통신할 수 있다면 expected\benign을 수 있습니다.

1. 프록시 호스트 컴퓨터에서 RPC 끝점 매퍼 끝점 (포트 135)에 액세스를 차단 합니다.

   Azure AD 암호 보호 프록시 설치 관리자는 자동으로 포트 135에 대 한 액세스를 허용 하는 Windows 방화벽 인바운드 규칙을 만듭니다. 이 규칙은 나중에 삭제, 사용 하지 않도록 설정 하는 경우 DC 에이전트 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품 대신 기본 제공 Windows 방화벽을 비활성화 하는 경우 포트 135에 대 한 액세스를 허용 하도록 방화벽을 구성 해야 합니다.

1. 프록시 호스트 컴퓨터 액세스를 차단 하는 RPC 끝점 (동적 또는 정적) 프록시 서비스에서 수신

   Azure AD 암호 보호 프록시 설치 관리자는 Windows 방화벽을 자동으로 만듭니다 인바운드 포트에 대 한 액세스를 허용 하는 인바운드 규칙이 Azure AD 암호 보호 프록시 서비스를 수신 합니다. 이 규칙은 나중에 삭제, 사용 하지 않도록 설정 하는 경우 DC 에이전트 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품 대신 기본 제공 Windows 방화벽을 사용 하지 않도록 설정 된, 구성 해야 하는 Azure AD 암호 보호 프록시 서비스에서 인바운드 포트에 대 한 액세스를 허용 하도록 방화벽 수신 대기 합니다. 이 구성은 프록시 서비스를 특정 정적 RPC 포트에서 수신 하도록 구성 된 경우에 보다 구체적인 만들 수 있습니다 (사용 하는 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>프록시 서비스를 도메인의 DC 에이전트에서 호출을 받을 수는 있지만 Azure와 통신할 수 없는

1. 프록시 컴퓨터에 나열 된 끝점에 연결 되어 있는지 확인 합니다 [배포 요구 사항](howto-password-ban-bad-on-premises-deploy.md)합니다.

1. 포리스트와 서버는 동일한 Azure 테 넌 트에 등록 된 모든 프록시 있는지를 확인 합니다.

   실행 하 여이 확인할 수 있습니다는 `Get-AzureADPasswordProtectionProxy` 및 `Get-AzureADPasswordProtectionDCAgent` 와 비교 하 여 PowerShell cmdlet는 `AzureTenant` 각 속성 항목 반환 합니다. 올바른 작업에 대 한 이러한 같아야 포리스트 내의 모든 DC 에이전트 및 프록시 서버.

   실행 하 여 복구할 수는 Azure 테 넌 트 등록 일치 하지 않습니다 조건이 없으면이 `Register-AzureADPasswordProtectionProxy` 및/또는 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet을 필요에 따라 모든 등록에 대해 동일한 Azure 테 넌 트에서 자격 증명을 사용 해야 합니다.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>DC 에이전트 암호화 하거나 암호 정책 파일 및 기타 상태를 해독할 수 아닙니다.

이 문제는 다양 한 증상이 나타날 수 있습니다 되었지만 일반적으로 일반적인 근본 원인입니다.

Azure AD 암호 보호는 Windows Server 2012를 실행 하는 도메인 컨트롤러에서 사용할 수 있으며 이후 Microsoft 키 배포 서비스에서 제공 하는 암호화 및 암호 해독 기능에 중요 한 종속성을 갖습니다. KDS 서비스를 사용 하도록 설정 하 고 모든 Windows Server 2012 및 도메인의 도메인 컨트롤러 이상에서 작동 해야 합니다.

기본적으로 KDS 서비스의 서비스 시작 모드는 수동 (트리거 시작)으로 구성 됩니다. 이 구성은 클라이언트가 서비스를 사용 하려고 처음 시작 시를 의미 합니다. 이 기본 서비스 시작 모드는 작동 하려면 Azure AD 암호 보호에 적합 합니다.

KDS 서비스 시작 모드 사용 안 함으로 구성 된 경우에 Azure AD 암호 보호 제대로 작동 하려면 먼저이 구성은 고정 되어야 합니다.

이 문제에 대 한 간단한 테스트는 수동으로 서비스 관리 MMC 콘솔을 통해 KDS 서비스를 시작 하거나 다른 서비스 관리 도구를 사용 하 여 (예: 명령 프롬프트 콘솔에서 "net start kdssvc" 실행). KDS 서비스는 성공적으로 시작 하 고 실행 상태를 유지 해야 합니다.

시작할 수 없게 KDS 서비스에 대 한 가장 일반적인 근본 원인을 점 Active Directory 도메인 컨트롤러 개체의 기본 도메인 컨트롤러 OU 외부에 있는입니다. 이 구성은 KDS 서비스에서 지원 되지 않습니다 및 Azure AD 암호 보호에 따른 제한 사항이 아닙니다. 이 조건에 대 한 해결 도메인 컨트롤러 개체의 기본 도메인 컨트롤러 OU 위치로 이동 하는 것입니다.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>취약 한 암호 허용 되 고 있지만

이 문제는 여러 가지 원인이 있을 수 있습니다.

1. DC 에이전트에 정책을 다운로드할 수 없는 되었거나 기존 정책을 해독할 수 없습니다. 위의 항목의 가능한 원인을 확인 합니다.

1. 암호 정책 강제 적용 모드가 여전히 감사로 설정되어 있습니다. 이 구성이 적용 되는 경우 Azure AD 암호 보호 포털을 사용 하 여 적용 하려면 다시 구성 합니다. 참조 [을 사용 하도록 설정 하는 암호 보호](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)합니다.

1. 암호 정책이 사용하지 않도록 설정되어 있습니다. 이 구성이 적용 되 면 Azure AD 암호 보호 포털을 사용 하 여 활성화 되도록 다시 구성 합니다. 참조 [을 사용 하도록 설정 하는 암호 보호](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)합니다.

1. 도메인의 모든 도메인 컨트롤러에서 DC 에이전트 소프트웨어를 설치 되지 않았습니다. 이런 경우 원격 Windows 클라이언트를 특정 도메인 컨트롤러 암호 변경 작업 중 대상 확인 하기가 어렵습니다. DC 에이전트 소프트웨어가 설치 되어 있는 특정 DC를 성공적으로 대상이 생각 하는 경우 DC 에이전트 관리 이벤트 로그를 두 번 클릭 하 여 확인할 수 있습니다: 결과 관계 없이 됩니다 암호의 결과 문서화 하는 하나 이상의 이벤트 유효성 검사 합니다. 암호 변경할 사용자를 위한 이벤트 인 경우 다른 도메인 컨트롤러에서 암호 변경이 처리 가능성이 않았습니다.

   다른 테스트로 setting\changing 암호 DC 에이전트 소프트웨어를 설치한 DC에 직접 로그인을 시도 합니다. 이 방법은 프로덕션 Active Directory 도메인에 대 한 권장 되지 않습니다.

   이러한 제한 사항에 따라 DC 에이전트 소프트웨어의 증분 배포 지원 되지만, DC 에이전트 소프트웨어가 도메인의 모든 도메인 컨트롤러에서 가능한 한 빨리 설치 되어 있는지는 것이 좋습니다.

1. 예상 대로 실제로 암호 유효성 검사 알고리즘 작동 될 수 있습니다. 참조 [암호 평가 되는 방식을](concept-password-ban-bad.md#how-are-passwords-evaluated)입니다.

## <a name="directory-services-repair-mode"></a>디렉터리 서비스 복구 모드

도메인 컨트롤러는 디렉터리 서비스 복구 모드로 부팅 되 면, DC 에이전트 서비스를이 조건을 감지 하 고 모든 암호 유효성 검사 또는 적용 작업을 비활성화할 수는 현재 활성 정책 구성에 관계 없이 발생 합니다.

## <a name="emergency-remediation"></a>응급 업데이트 관리

DC 에이전트 서비스가 문제를 일으키는 상황이 발생하면 DC 에이전트 서비스는 즉시 종료될 수 있습니다. DC 에이전트 암호 필터 dll은 여전히 실행 중이 아닌 서비스를 호출하고 경고 이벤트(10012, 10013)를 기록하지만 해당 시간 동안 들어오는 모든 암호는 허용됩니다. DC 에이전트 서비스는 필요에 따라 "Disabled" 시작 유형을 사용하여 Windows 서비스 제어 관리자를 통해 구성될 수 있습니다.

다른 수정 측정값을 Azure AD 암호 보호 포털에서 사용 모드를 안 함으로 설정할 수 있습니다. 업데이트된 정책이 다운로드되면, 각 DC 에이전트 서비스는 모든 암호가 있는 그대로 허용되는 정지 모드가 됩니다. 자세한 내용은 [적용 모드](howto-password-ban-bad-on-premises-operations.md#enforce-mode)를 참조하세요.

## <a name="domain-controller-demotion"></a>도메인 컨트롤러 수준 내리기

여전히 DC 에이전트 소프트웨어를 실행하는 도메인 컨트롤러의 수준을 내리기 위해 지원됩니다. 하지만 관리자는 DC 에이전트 소프트웨어가 수준 내리기 절차 중 현재 암호 정책을 계속 실행하고 적용한다는 것을 인식해야 합니다. 새 로컬 관리자 계정 암호(수준 내리기 작업의 일부로 지정됨)는 다른 모든 암호와 같이 유효성을 검사합니다. Microsoft는 DC 수준 내리기 절차의 일부로 로컬 관리자 계정에 대해 보안 암호를 선택하라고 권장하지만 DC 에이전트 소프트웨어에 의한 새 로컬 관리자 계정 암호의 유효성 검사는 기존 수준 내리기 작업 절차를 중단시킬 수 있습니다.

수준 내리기가 성공하고 도메인 컨트롤러가 다시 부팅되고 일반 멤버 서버로 다시 실행되면 DC 에이전트 소프트웨어는 수동 모드 실행으로 되돌아갑니다. 언제든 제거될 수 있습니다.

## <a name="removal"></a>제거

Azure AD 암호 보호 소프트웨어 및 정리를 모든 관련 된 상태에서 제거할 도메인 및 포리스트를 결정 한 경우에 다음 단계를 사용 하 여이 작업을 수행할 수 있습니다.

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

4. 각 도메인 명명 컨텍스트에서 모든 DC 에이전트 연결점을 수동으로 제거합니다. 있을 수 있습니다 하나 소프트웨어를 배포 하는 정도 따라 포리스트의 도메인 컨트롤러 마다 이러한 개체. 다음 Active Directory PowerShell 명령을 사용하여 해당 개체의 위치를 검색할 수 있습니다.

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
