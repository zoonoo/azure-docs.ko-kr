---
title: Azure AD 암호 보호 미리 보기에서 문제 해결
description: Azure AD 암호 보호 미리 보기 일반 문제 해결 이해
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
ms.openlocfilehash: 5727965373752d40e3ce508c1bc79046c2b3b70b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177754"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>미리 보기: Azure AD 암호 보호 문제 해결

|     |
| --- |
| Azure AD 암호 보호는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

Azure AD 암호 보호를 배포한 후 문제를 해결해야 할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 해결 단계를 이해하는 데 유용한 세부 정보를 제공합니다.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>취약한 암호가 예상대로 거부되지 않음

다음과 같은 몇 가지 원인이 있을 수 있습니다.

1. DC 에이전트에서 아직 정책을 다운로드하지 않았습니다. 이 현상은 DC 에이전트 관리 이벤트 로그에서 30001개 이벤트입니다.

    이 문제의 잠재적 원인에는 다음이 포함됩니다.

    1. 포리스트가 아직 등록되지 않음
    2. 프록시가 아직 등록되지 않음
    3. 네트워크 연결 문제로 인해 프록시 서비스가 Azure와 통신할 수 없음(HTTP 프록시 요구 사항 확인)

2. 암호 정책 강제 적용 모드가 여전히 감사로 설정되어 있습니다. 이 경우 Azure AD 암호 보호 포털을 사용하여 적용하도록 다시 구성합니다. [암호 보호 사용](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)을 참조하세요.

3. 암호 정책이 사용하지 않도록 설정되어 있습니다. 이 경우 Azure AD 암호 보호 포털을 사용하여 사용하도록 다시 구성합니다. [암호 보호 사용](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)을 참조하세요.

4. 암호 유효성 검사 알고리즘이 예상대로 작동할 수도 있습니다. [암호 평가 방법](concept-password-ban-bad.md#how-are-passwords-evaluated)을 참조하세요.

## <a name="directory-services-repair-mode"></a>디렉터리 서비스 복구 모드

도메인 컨트롤러가 디렉터리 서비스 복구 모드로 부팅되면 DC 에이전트 서비스는 이를 감지하여 현재 활성 정책 구성에 관계 없이 모든 암호 유효성 검사 또는 적용 활동을 사용할 수 없도록 설정하게 합니다.

## <a name="emergency-remediation"></a>응급 업데이트 관리

DC 에이전트 서비스가 문제를 일으키는 상황이 발생하면 DC 에이전트 서비스는 즉시 종료될 수 있습니다. DC 에이전트 암호 필터 dll은 여전히 실행 중이 아닌 서비스를 호출하고 경고 이벤트(10012, 10013)를 기록하지만 해당 시간 동안 들어오는 모든 암호는 허용됩니다. DC 에이전트 서비스는 필요에 따라 "Disabled" 시작 유형을 사용하여 Windows 서비스 제어 관리자를 통해 구성될 수 있습니다.

다른 수정 측정값을 Azure AD 암호 보호 포털에서 사용 모드를 안 함으로 설정할 수 있습니다. 업데이트된 정책이 다운로드되면, 각 DC 에이전트 서비스는 모든 암호가 있는 그대로 허용되는 정지 모드가 됩니다. 자세한 내용은 [적용 모드](howto-password-ban-bad-on-premises-operations.md#enforce-mode)를 참조하세요.

## <a name="domain-controller-demotion"></a>도메인 컨트롤러 수준 내리기

여전히 DC 에이전트 소프트웨어를 실행하는 도메인 컨트롤러의 수준을 내리기 위해 지원됩니다. 하지만 관리자는 DC 에이전트 소프트웨어가 수준 내리기 절차 중 현재 암호 정책을 계속 실행하고 적용한다는 것을 인식해야 합니다. 새 로컬 관리자 계정 암호(수준 내리기 작업의 일부로 지정됨)는 다른 모든 암호와 같이 유효성을 검사합니다. Microsoft는 DC 수준 내리기 절차의 일부로 로컬 관리자 계정에 대해 보안 암호를 선택하라고 권장하지만 DC 에이전트 소프트웨어에 의한 새 로컬 관리자 계정 암호의 유효성 검사는 기존 수준 내리기 작업 절차를 중단시킬 수 있습니다.

수준 내리기가 성공하고 도메인 컨트롤러가 다시 부팅되고 일반 멤버 서버로 다시 실행되면 DC 에이전트 소프트웨어는 수동 모드 실행으로 되돌아갑니다. 언제든 제거될 수 있습니다.

## <a name="removal"></a>제거

공개 미리 보기 소프트웨어를 제거하고 도메인 및 포리스트에서 모든 관련 상태를 정리하기로 결정된 경우 다음 단계를 사용하여 이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이러한 단계를 순서대로 수행하는 것이 중요합니다. 프록시 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체를 다시 만듭니다. DC 에이전트 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체 및 sysvol 상태를 다시 만듭니다.

1. 모든 머신에서 프록시 소프트웨어를 제거합니다. 이 단계는 재부팅이 필요하지 **않습니다**.
2. 모든 도메인 컨트롤러에서 DC 에이전트 소프트웨어를 제거합니다. 이 단계는 재부팅이 **필요합니다**.
3. 각 도메인 명명 컨텍스트에서 모든 프록시 서비스 연결점을 수동으로 제거합니다. 다음 Active Directory PowerShell 명령을 사용하여 이러한 개체의 위치를 검색할 수 있습니다.

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다. 

4. 각 도메인 명명 컨텍스트에서 모든 DC 에이전트 연결점을 수동으로 제거합니다. 공개 미리 보기 소프트웨어가 얼마나 넓게 배포됐는가에 따라 포리스트에 도메인 컨트롤러당 이러한 개체 중 하나가 있을 수 있습니다. 다음 Active Directory PowerShell 명령을 사용하여 해당 개체의 위치를 검색할 수 있습니다.

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

5. 포리스트 수준의 구성 상태를 수동으로 제거합니다. 포리스트 구성 상태는 Active Directory 구성 명명 컨텍스트의 컨테이너에 유지됩니다. 다음과 같이 검색 및 삭제될 수 있습니다.

   ```PowerShell
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
