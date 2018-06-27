---
title: Azure AD 암호 보호 미리 보기에서 문제 해결 및 로깅
description: Azure AD 암호 보호 미리 보기 로깅 및 일반 문제 해결 이해
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292018"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>미리 보기: Azure AD 암호 보호 모니터링, 보고 및 문제 해결

|     |
| --- |
| Azure AD 암호 보호 및 사용자 지정 금지된 암호 목록은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 참조|
|     |

Azure AD 배포 후 암호 보호 모니터링 및 보고는 필수 작업입니다. 이 문서에서는 각 서비스에서 정보를 기록하는 위치 및 Azure AD 암호 보호 사용에 대한 보고 방법을 파악하도록 자세히 도와줍니다.

## <a name="on-premises-logs-and-events"></a>온-프레미스 로그 및 이벤트

### <a name="dc-agent-service"></a>DC 에이전트 서비스

각 도메인 컨트롤러에서 DC 에이전트 서비스 소프트웨어는 암호 유효성 검사(및 기타 상태) 결과를 로컬 이벤트 로그(\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin)에 기록

이벤트는 다음 범위를 사용하여 다양한 DC 에이전트 구성 요소에 의해 기록됩니다.

|구성 요소 |이벤트 ID 범위|
| --- | --- |
|DC 에이전트 암호 필터 dll| 10000-19999|
|DC 에이전트 서비스 호스팅 프로세스| 20000-29999|
|DC 에이전트 서비스 정책 유효성 검사 논리| 30000-39999|

성공적인 암호 유효성 검사 작업의 경우 일반적으로 DC 에이전트 암호 필터 dll에서 기록된 이벤트가 있습니다. 실패한 암호 유효성 검사 작업의 경우 일반적으로 기록된 두 개의 이벤트 즉, DC 에이전트 서비스 이벤트 및 DC 에이전트 암호 필터 dll 이벤트가 있습니다.

이러한 상황을 캡처하려면 불연속 이벤트가 다음 요인을 기준으로 기록됩니다.

* 기존 암호가 설정되거나 변경됐는지 여부.
* 기존 암호의 유효성 검사 통과 또는 실패 여부.
* Microsoft 전역 정책 vs 조직 정책으로 인한 유효성 검사의 실패 여부.
* 감사 전용 모드가 현재 암호 정책에 대해 켜져 있는지 또는 꺼져 있는지 여부.

키 암호-유효성 검사-관련 이벤트는 다음과 같습니다.

|   |암호 변경 |암호 설정|
| --- | :---: | :---: |
|합격 |10014 |10015|
|실패(고객 암호 정책 통과하지 못함)| 10016, 30002| 10017, 30003|
|실패(Microsoft 암호 정책 통과하지 못함)| 10016, 30004| 10017, 30005|
|감사 전용 합격(고객 암호 정책 실패)| 10024, 30008| 10025, 30007|
|감사 전용 합격(Microsoft 암호 정책 실패)| 10024, 30010| 10025, 30009|

> [!TIP]
> 들어오는 암호는 먼저 Microsoft 전역 암호 목록에 대해 유효성 검사를 수행합니다. 검사를 통과하지 못할 경우 더이상 처리를 수행하지 않습니다. Azure에서 암호 변경에 수행된 것과 동일한 동작입니다.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>이벤트 ID 10014 성공적인 암호 집합에 대한 샘플 이벤트 로그 메시지

지정된 사용자에 대해 변경된 암호는 현재 Azure 암호 정책을 준수하는 것으로 확인되었습니다.

 사용자 이름: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>이벤트 ID 10017 및 30003 실패한 암호 집합에 대한 샘플 이벤트 로그 메시지

10017:

지정된 사용자에 대한 암호 재설정은 현재 Azure 암호 정책을 준수하지 않으므로 거부되었습니다. 자세한 내용은 상호 관련된 이벤트 로그 메시지를 참조하십시오.

 사용자 이름: BPL_03283841185 FullName:

30003:

지정된 사용자에 대한 암호 재설정은 현재 Azure 암호 정책의 테넌트당 금지된 암호 목록에 있는 토큰 중 하나 이상과 일치하므로 거부되었습니다.

 사용자 이름: BPL_03283841185 FullName:

알아두어야 할 몇 가지 다른 키 이벤트 로그 메시지는 다음과 같습니다.

#### <a name="sample-event-log-message-for-event-id-30001"></a>이벤트 ID 30001에 대한 샘플 이벤트 로그 메시지

지정된 사용자에 대한 암호는 Azure 암호 정책을 아직 사용할 수 없으므로 수락되었습니다.

사용자 이름: <user> FullName: <user>

이 조건은 다음 이유(%n) 중 하나 이상에 의해 발생될 수 있습니다.

1. 포리스트는 아직 Azure에 등록되지 않았습니다.

   해결 단계: 관리자가 Register-AzureADPasswordProtectionForest cmdlet을 사용하여 포리스트를 등록해야 합니다.

2. Azure AD 암호 보호 프록시는 현재 포리스트에서 적어도 하나 이상의 컴퓨터에서 아직 사용할 수 없습니다.

   해결 단계: 관리자가 Register-AzureADPasswordProtectionProxy cmdlet을 사용하여 프록시를 설치하고 등록해야 합니다.

3. 이 DC에는 모든 Azure AD 암호 보호 프록시 인스턴스에 네트워크 연결이 없습니다.

   해결 단계: 네트워크 연결이 적어도 하나 이상의 Azure AD 암호 보호 프록시 인스턴스에 존재하는지 확인합니다.

4. 이 DC는 도메인에서 다른 도메인 컨트롤러에 연결이 없습니다.

   해결 단계: 네트워크 연결이 도메인에 있는지 확인합니다.

#### <a name="sample-event-log-message-for-event-id-30006"></a>이벤트 ID 30006에 대한 샘플 이벤트 로그 메시지

이제 서비스는 다음과 같은 Azure 암호 정책을 적용합니다.

 AuditOnly: 1

 전역 정책 날짜: 2018-05-15T00:00:00.000000000Z

 테넌트 정책 날짜: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z

 테넌트 정책 적용: 1

#### <a name="dc-agent-log-locations"></a>DC 에이전트 로그 위치

DC 에이전트 서비스는 다음 로그(\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational)에 작업 관련 이벤트를 기록하게 됩니다.

DC 에이전트 서비스는 다음 로그(\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace)에 자세한 디버그 수준 추적 이벤트를 기록할 수 있습니다.

> [!WARNING]
> 추적 로그는 기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정되면 이 로그는 많은 양의 이벤트를 받고 도메인 컨트롤러 성능에 영향을 줄 수 있습니다. 따라서 이 향상된 로그는 문제가 더 세부적인 조사를 필요로 할 때 및 최소한의 시간 동안만 사용하도록 설정돼야 합니다.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 암호 보호 프록시 서비스

암호 보호 프록시 서비스는 다음 이벤트 로그(\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational)에 최소한의 이벤트 집합을 내보냅니다.

암호 보호 프록시 서비스는 다음 로그(\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace)에 자세한 디버그 수준 추적 이벤트를 기록할 수 있습니다.

> [!WARNING]
> 추적 로그는 기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정되면 이 로그는 많은 양의 이벤트를 받고 도메인 프록시 호스트의 성능에 영향을 줄 수 있습니다. 따라서 이 로그는 문제가 더 세부적인 조사를 필요로 할 때 및 최소한의 시간 동안만 사용하도록 설정돼야 합니다.

### <a name="dc-agent-discovery"></a>DC 에이전트 검색

`Get-AzureADPasswordProtectionDCAgent` cmdlet은 도메인이나 포리스트에서 실행되는 다양한 DC 에이전트에 대한 기본 정보를 표시하는 데 사용될 수 있습니다. 이 정보는 DC 에이전트 서비스를 실행하여 등록된 serviceConnectionPoint 개체에서 검색됩니다. 이 cmdlet의 출력 예는 다음과 같습니다.

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

다양한 속성이 대략적인 시간 단위로 각 DC 에이전트 서비스에 의해 업데이트됩니다. 데이터는 여전히 Active Directory 복제 대기 시간의 적용을 받습니다.

Cmdlet의 쿼리 범위는 포리스트 또는 도메인 매개 변수 중 하나를 사용하여 영향을 받을 수 있습니다.

### <a name="emergency-remediation"></a>응급 업데이트 관리

DC 에이전트 서비스가 문제를 일으키는 경우 불행한 상황이 발생하면 DC 에이전트 서비스는 즉시 종료될 수 있습니다. DC 에이전트 암호 필터 dll은 실행 중이 아닌 서비스를 호출하고 경고 이벤트(10012, 10013)를 기록하지만 해당 시간 동안 들어오는 모든 암호는 허용됩니다. DC 에이전트 서비스는 필요에 따라 "Disabled" 시작 유형을 사용하여 Windows 서비스 제어 관리자를 통해 구성될 수 있습니다.

### <a name="performance-monitoring"></a>성능 모니터링

DC 에이전트 서비스 소프트웨어는 **Azure AD 암호 보호**라는 성능 카운터 개체를 설치합니다. 다음 성능 카운터는 현재 사용할 수 있습니다.

|성능 카운터 이름 | 설명|
| --- | --- |
|처리된 암호 |이 카운터는 마지막 다시 시작한 이후에 (승인 또는 거부) 처리된 암호의 총 수를 표시합니다.|
|허용된 암호 |이 카운터는 마지막 다시 시작한 이후에 허용된 암호의 총 수를 표시합니다.|
|거부된 암호 |이 카운터는 마지막 다시 시작한 이후에 거부된 암호의 총 수를 표시합니다.|
|진행 중인 암호 필터 요청 |이 카운터는 현재 진행 중인 암호 필터 요청 수를 표시합니다.|
|최대 암호 필터 요청 |이 카운터는 마지막 다시 시작한 이후에 동시 암호 필터 요청 최대 수를 표시합니다.|
|암호 필터 요청 오류 |이 카운터는 마지막 다시 시작한 이후에 오류로 인해 실패한 암호 필터 요청의 총 수를 표시합니다. Azure AD 암호 보호 DC 에이전트 서비스가 실행 중이 아닐 때 오류가 발생할 수 있습니다.|
|암호 필터 요청 수/초 |이 카운터는 암호를 처리하는 속도를 표시합니다.|
|암호 필터 요청 처리 시간 |이 카운터는 암호 필터 요청을 처리하는 데 필요한 평균 시간을 표시합니다.|
|최대 암호 필터 요청 처리 시간 |이 카운터는 마지막 다시 시작한 이후에 최대 암호 필터 요청 처리 시간을 표시합니다.|
|감사 모드로 인해 허용된 암호 |이 카운터는 보통 거부됐었으나 (마지막 다시 시작한 이후에) 암호 정책이 감사 모드로 구성됐기 때문에 허용된 암호의 총 수를 표시합니다.|

## <a name="directory-services-repair-mode"></a>디렉터리 서비스 복구 모드

도메인 컨트롤러가 디렉터리 서비스 복구 모드로 부팅되면 DC 에이전트 서비스는 이것을 검색하여 현재 활성 정책 구성에 관계 없이 모든 암호 유효성 검사 또는 적용 활동을 사용할 수 없도록 설정하게 합니다.

## <a name="domain-controller-demotion"></a>도메인 컨트롤러 수준 내리기

여전히 DC 에이전트 소프트웨어를 실행하는 도메인 컨트롤러의 수준을 내리기 위해 지원됩니다. 하지만 관리자는 DC 에이전트 소프트웨어가 수준 내리기 절차 중 현재 암호 정책을 계속 실행하고 적용한다는 것을 인식해야 합니다. 새 로컬 관리자 계정 암호(수준 내리기 작업의 일부로 지정됨)는 다른 모든 암호와 같이 유효성을 검사합니다. Microsoft는 DC 수준 내리기 절차의 일부로 로컬 관리자 계정에 대해 보안 암호를 선택하라고 권장하지만 DC 에이전트 소프트웨어에 의한 새 로컬 관리자 계정 암호의 유효성 검사는 기존 수준 내리기 작업 절차를 중단시킬 수 있습니다.
수준 내리기가 성공하고 도메인 컨트롤러가 다시 부팅되고 일반 멤버 서버로 다시 실행되면 DC 에이전트 소프트웨어는 수동 모드 실행으로 되돌아갑니다. 언제든 제거될 수 있습니다.

## <a name="removal"></a>제거

공개 미리 보기 소프트웨어를 제거하고 도메인 및 포리스트에서 모든 관련 상태를 정리하기로 결정된 경우 다음 단계를 사용하여 이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이러한 단계를 순서대로 수행하는 것이 중요합니다. 암호 보호 프록시 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체 및 sysvol 상태를 주기적으로 다시 만들게 됩니다.

1. 모든 컴퓨터에서 암호 보호 프록시 소프트웨어를 제거합니다. 이 단계는 재부팅이 필요하지 **않습니다**.
2. 모든 도메인 컨트롤러에서 DC 에이전트 소프트웨어를 제거합니다. 이 단계는 재부팅이 **필요합니다**.
3. 각 도메인 명명 컨텍스트에서 모든 프록시 서비스 연결점을 수동으로 제거합니다. 다음 Active Directory Powershell 명령을 사용하여 이러한 개체의 위치를 검색할 수 있습니다.
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다. 

4. 각 도메인 명명 컨텍스트에서 모든 DC 에이전트 연결점을 수동으로 제거합니다. 공개 미리 보기 소프트웨어가 얼마나 넓게 배포됐는가에 따라 포리스트에 도메인 컨트롤러당 이러한 개체 중 하나가 있을 수 있습니다. 다음 Active Directory Powershell 명령을 사용하여 해당 개체의 위치를 검색할 수 있습니다.

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

5. 포리스트 수준의 구성 상태를 수동으로 제거합니다. 포리스트 구성 상태는 Active Directory 구성 명명 컨텍스트의 컨테이너에 유지됩니다. 다음과 같이 검색 및 삭제될 수 있습니다.

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. 수동으로 다음 폴더와 해당 내용 모두를 삭제하여 모든 sysvol 관련 상태를 수동으로 제거합니다.

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   필요한 경우 이 경로는 기존 도메인 컨트롤러에서 로컬로 액세스될 수도 있으며, 기본 위치는 다음 경로와 같이 표시됩니다.

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Sysvol 공유가 기본이 아닌 위치에 구성된 경우 이 경로는 다릅니다.

## <a name="next-steps"></a>다음 단계

전역 및 사용자 지정 금지된 암호 목록에 대한 자세한 내용은 [잘못된 암호 금지](concept-password-ban-bad.md) 문서 참조
