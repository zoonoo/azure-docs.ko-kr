---
title: Azure Resource Manager 리소스 공급자 작업
description: Microsoft Azure Resource Manager 리소스 공급자에서 사용할 수 있는 작업을 나열합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c56e6729307a1cad998a060202f51cb8f29bf94a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548315"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 리소스 공급자 작업

이 문서에는 각 Azure Resource Manager 리소스 공급자에서 사용할 수 있는 작업 목록이 나열되어 있습니다. 이러한 작업은 Azure [사용자 지정 역할에서](custom-roles.md) Azure의 리소스에 대한 세부적인 액세스 제어를 제공하는 데 사용할 수 있습니다. 작업 문자열에는 다음과 같은 `{Company}.{ProviderName}/{resourceType}/{action}`형식이 있습니다. 리소스 공급자 네임스페이스가 Azure 서비스에 매핑하는 방법 목록은 [리소스 공급자를 서비스로 일치시다](../azure-resource-manager/management/azure-services-resource-providers.md)참조합니다.

리소스 공급자 작업은 계속 업데이트됩니다. 최신 작업을 가져오려면 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 또는 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)를 사용합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

Azure 서비스: [Azure Active 디렉터리 도메인 서비스](../active-directory-domain-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.AAD/unregister/action | 도메인 서비스를 등록 취소합니다. |
> | 작업 | Microsoft.AAD/register/action | 도메인 서비스를 등록합니다. |
> |  | **도메인 서비스** |  |
> | 작업 | Microsoft.AAD/domainServices/read | 도메인 서비스를 읽습니다. |
> | 작업 | Microsoft.AAD/domainServices/write | 도메인 서비스를 작성합니다. |
> | 작업 | Microsoft.AAD/domainServices/delete | 도메인 서비스를 삭제합니다. |
> |  | **도메인 서비스/오컨테이너** |  |
> | 작업 | Microsoft.AAD/domainServices/oucontainer/read | Ou 컨테이너 읽기 |
> | 작업 | Microsoft.AAD/domainServices/oucontainer/write | Ou 컨테이너 쓰기 |
> | 작업 | Microsoft.AAD/domainServices/oucontainer/delete | Ou 컨테이너 삭제 |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.AAD/locations/operationresults/read |  |
> |  | **작업** |  |
> | 작업 | Microsoft.AAD/Operations/read |  |

## <a name="microsoftaadiam"></a>microsoft.aadiam

마이크로소프트 모니터링 인사이트

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **진단 설정** |  |
> | 작업 | microsoft.aadiam/diagnosticsettings/write | 진단 설정을 작성합니다. |
> | 작업 | microsoft.aadiam/diagnosticsettings/read | 진단 설정을 읽습니다. |
> | 작업 | microsoft.aadiam/diagnosticsettings/delete | 진단 설정을 삭제합니다. |
> |  | **진단 설정범주** |  |
> | 작업 | microsoft.aadiam/diagnosticsettingscategories/read | 진단 설정 범주를 읽습니다. |
> |  | **메트릭정의** |  |
> | 작업 | 마이크로 소프트.aadiam / 메트릭정의 / 읽기 | 테넌트 수준 메트릭 정의 읽기 |
> |  | **메트릭** |  |
> | 작업 | 마이크로 소프트.aadiam / 메트릭 / 읽기 | 테넌트 수준 메트릭 읽기 |

## <a name="microsoftaddons"></a>Microsoft.Addons

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Addons/register/action | 지정된 구독을 Microsoft.Addons에 등록합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Addons/operations/read | 지원되는 RP 작업을 가져옵니다. |
> |  | **supportProviders** |  |
> | 작업 | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 지정한 구독에 대한 현재 지원 플랜 정보를 나열합니다. |
> |  | **지원 공급자/지원플랜유형** |  |
> | 작업 | Microsoft.Addons/supportProviders/supportPlanTypes/read | 지정된 Canonical 지원 플랜 상태를 가져옵니다. |
> | 작업 | Microsoft.Addons/supportProviders/supportPlanTypes/write | 지정된 Canonical 지원 플랜 유형을 추가합니다. |
> | 작업 | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 지정된 Canonical 지원 플랜을 제거합니다. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

Azure 서비스: [Azure Active 디렉터리](../active-directory/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ADHybridHealthService/configuration/action | 테넌트 구성을 업데이트합니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/action | 테넌트의 서비스 인스턴스를 업데이트합니다. |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/action | 테넌트에 대한 새 포리스트를 만듭니다. |
> | 작업 | Microsoft.ADHybridHealthService/register/action | ADHybrid Health Service 리소스 공급자를 등록하고 ADHybrid Health Service 리소스를 만들 수 있도록 합니다. |
> | 작업 | Microsoft.ADHybridHealthService/unregister/action | ADHybrid Health Service 리소스 공급자에 대한 구독을 등록 취소합니다. |
> |  | **addsservices** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/write | 테넌트에 대한 ADDomainService 인스턴스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 서버 인스턴스를 서비스에 추가합니다. |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/read | 지정된 서비스 이름에 대한 서비스 정보를 가져옵니다. |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/delete | 서비스 및 해당 서버(상태 데이터 포함)를 삭제합니다. |
> |  | **서비스/애드도메인 서비스 멤버 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 지정된 서비스 이름에 대한 모든 서버를 가져옵니다. |
> |  | **서비스/경고 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/alerts/read | 경고 ID, 경고 발생 날짜, 경고 마지막으로 검색된 경고, 경고 설명, 마지막 업데이트, 경고 수준, 경고 상태, 경고 문제 해결 링크 등과 같은 포리스트에 대한 경고 정보를 가져옵니다. |
> |  | **서비스/구성 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/configuration/read | 포리스트에 대한 서비스 구성을 가져옵니다. 예: 포리스트 이름, 기능 수준, 도메인 명명 마스터 FSMO 역할, 스키마 마스터 FSMO 역할 등 |
> |  | **서비스/차원 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 포리스트에 대한 도메인 및 사이트 정보를 가져옵니다. 예: 상태, 활성 경고, 해결된 경고, 속성(도메인 기능 수준, 포리스트, 인프라 마스터, PDC, RID 마스터 등)  |
> |  | **서비스/기능/사용자 기본 설정 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 포리스트에 대한 사용자 기본 설정을 가져옵니다.<br>예: MetricCounterName(ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches)<br>UI 차트 등에 대한 설정입니다. |
> |  | **추가 서비스/포리스트 요약** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 지정된 포리스트에 대한 포리스트 요약을 가져옵니다(포리스트 이름, 이 포리스트에 속한 도메인 수, 사이트 수 및 사이트 정보 등). |
> |  | **서비스/메트릭 메타데이터 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 지정된 서비스에 지원되는 메트릭 목록을 가져옵니다.<br>예: ADFS 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomainService의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>ADSync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/메트릭/그룹 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 서비스가 지정되면 이 API는 메트릭 정보를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/프리미엄 검사 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 이 API는 프리미엄 테넌트에 등록된 모든 ADDomainServices의 목록을 가져옵니다. |
> |  | **서비스/복제 세부 정보 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 지정된 서비스 이름의 모든 서버에 대한 복제 정보를 가져옵니다. |
> |  | **서비스/복제 상태 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 도메인 컨트롤러 수 및 해당 복제 오류가 있는 경우 이 오류를 가져옵니다. |
> |  | **추가 서비스/복제 요약** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 지정된 포리스트에 대한 복제 정보와 함께 전체 도메인 컨트롤러 목록을 가져옵니다. |
> |  | **서비스/서비스 회원 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 지정된 서비스와 테넌트에 대한 서버를 삭제합니다. |
> |  | **서비스/서비스 멤버/자격 증명 추가** |  |
> | 작업 | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | ADDomainService의 서버를 등록하는 동안 이 API가 호출되어 새 서버를 등록하기 위한 자격 증명을 가져옵니다. |
> |  | **구성** |  |
> | 작업 | Microsoft.ADHybridHealthService/configuration/write | 테넌트 구성을 만듭니다. |
> | 작업 | Microsoft.ADHybridHealthService/configuration/read | 테넌트 구성을 읽습니다. |
> |  | **로그** |  |
> | 작업 | Microsoft.ADHybridHealthService/logs/read | 테넌트에 대한 에이전트 설치 및 등록 로그를 가져옵니다. |
> |  | **로그/내용물** |  |
> | 작업 | Microsoft.ADHybridHealthService/logs/contents/read | Blob에 저장된 에이전트 설치 및 등록 로그의 내용을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ADHybridHealthService/operations/read | 시스템에서 지원하는 작업 목록을 가져옵니다. |
> |  | **보고서/사용 가능한 배포** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/availabledeployments/read | DevOps에서 고객 인시던트를 지원하는 데 사용할 수 있는 지역 목록을 가져옵니다. |
> |  | **보고서/잘못된 암호** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/badpassword/read | Active Directory Federation Service의 모든 사용자에 대한 잘못된 암호 시도 목록을 가져옵니다. |
> |  | **보고서/나쁜 암호 사용자디핑 주파수** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 지정된 테넌트에 대한 일별 IPAddress당 UserId당 잘못된 사용자 이름/암호 시도 빈도와 관련하여 큐에 새로 넣어진 보고 작업의 상태 및 최종 결과가 포함된 Blob SAS URI를 가져옵니다. |
> |  | **신고/동의토데브옵스 임차인** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | DevOps에서 동의한 테넌트 목록을 가져옵니다. 일반적으로 고객 지원에 사용됩니다. |
> |  | **보고서/이데브롭스** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/isdevops/read | DevOps에서 동의한 테넌트인지 여부를 나타내는 값을 가져옵니다. |
> |  | **보고서/셀렉트데브옵스테넌트** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 선택한 DevOps 테넌트에 대한 userid(objectid)를 업데이트합니다. |
> |  | **보고서/선택한 배포** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 지정된 테넌트에 대해 선택한 배포를 가져옵니다. |
> |  | **보고서/테넌트 할당배포** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 테넌트 ID가 지정되면 테넌트 스토리지 위치를 가져옵니다. |
> |  | **보고서/업데이트 선택배포** |  |
> | 작업 | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 액세스할 데이터가 있는 지리적 위치를 가져옵니다. |
> |  | **서비스** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/write | 테넌트의 서비스 인스턴스를 만듭니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/read | 테넌트의 서비스 인스턴스를 읽습니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/delete | 테넌트의 서비스 인스턴스를 삭제합니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/action | 서비스에 서버 인스턴스를 만듭니다. |
> |  | **서비스/경고** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/alerts/read | 서비스에 대한 경고를 읽습니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/alerts/read | 서비스에 대한 경고를 읽습니다. |
> |  | **서비스/체크서비스 특징성** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 기능 이름이 지정되면 해당 기능을 사용하는 데 필요한 모든 항목이 서비스에 있는지 확인합니다. |
> |  | **서비스/내보내기 오류** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/exporterrors/read | 지정된 동기화 서비스에 대한 내보내기 오류를 가져옵니다. |
> |  | **서비스/내보내기 상태** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/exportstatus/read | 지정된 서비스에 대한 내보내기 상태를 가져옵니다. |
> |  | **서비스/피드백 유형/피드백** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 지정된 서비스 및 서버에 대한 경고 피드백을 가져옵니다. |
> |  | **서비스/메트릭 메타데이터** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/metricmetadata/read | 지정된 서비스에 지원되는 메트릭 목록을 가져옵니다.<br>예: ADFS 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomainService의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>ADSync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/메트릭/그룹** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/metrics/groups/read | 서비스가 지정되면 이 API는 메트릭 정보를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/메트릭/그룹/평균** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 서비스가 지정되면 이 API는 지정된 서비스의 메트릭에 대한 평균을 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/메트릭/그룹/합계** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 서비스가 지정되면 이 API는 지정된 서비스의 메트릭에 대한 집계 보기를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/모니터링 구성** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 서비스에 대한 모니터링 구성을 추가하거나 업데이트합니다. |
> |  | **서비스/모니터링 구성** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 지정된 서비스에 대한 모니터링 구성을 가져옵니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 서비스에 대한 모니터링 구성을 추가하거나 업데이트합니다. |
> |  | **서비스/프리미엄 체크** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/premiumcheck/read | 이 API는 프리미엄 테넌트에 등록된 모든 서비스의 목록을 가져옵니다. |
> |  | **서비스/보고서** |  |
> | 작업 | 마이크로소프트.AD하이브리드 헬스 서비스/서비스/보고서/생성블로부리/액션 | 위험한 IP 보고서를 생성하고 이를 가리키는 URI를 반환합니다. |
> |  | **서비스/보고서/블로우리스** |  |
> | 작업 | 마이크로소프트.AD하이브리드 헬스 서비스/서비스/보고서/blobUris/읽기 | 지난 7일 동안 모든 위험한 IP 보고서 URI를 가져옵니다. |
> |  | **서비스/보고서/세부 정보** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/reports/details/read | 지난 7일간 잘못된 암호 오류가 있는 상위 50명의 사용자에 대한 보고서를 가져옵니다. |
> |  | **서비스/서비스 회원** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/read | 서비스의 서버 인스턴스를 읽습니다. |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/delete | 서비스의 서버 인스턴스를 삭제합니다. |
> |  | **서비스/서비스 회원/경고** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 서버에 대한 경고를 읽습니다. |
> |  | **서비스/서비스 회원/자격 증명** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 서버를 등록하는 동안 이 API가 호출되어 새 서버를 등록하기 위한 자격 증명을 가져옵니다. |
> |  | **서비스/서비스 회원/데이터 신선도** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 서버가 지정되면 이 API는 서버에서 업로드하는 데이터 형식 목록과 각 업로드에 대한 최신 시간을 가져옵니다. |
> |  | **서비스/서비스 회원/수출 현황** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 지정된 동기화 서비스에 대한 동기화 내보내기 오류 정보를 가져옵니다. |
> |  | **서비스/서비스 회원/메트릭** |  |
> | 작업 | 마이크로소프트.AD하이브리드 헬스 서비스/서비스/서비스 회원/메트릭/읽기 | 지정된 서비스 및 서비스 멤버에 대한 커넥터 및 실행 프로필 이름 목록을 가져옵니다. |
> |  | **서비스/서비스 회원/메트릭/그룹** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 서비스가 지정되면 이 API는 메트릭 정보를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> |  | **서비스/서비스 회원/서비스 구성** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 지정된 테넌트에 대한 서비스 구성을 가져옵니다. |
> |  | **서비스/테넌트 화이트리스팅** |  |
> | 작업 | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 지정된 테넌트에 대한 기능 허용 목록 상태를 가져옵니다. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

Azure 서비스: [Azure 관리자](../advisor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Advisor/generateRecommendations/action | 권장 사항 생성 상태를 가져옵니다. |
> | 작업 | Microsoft.Advisor/register/action | Microsoft Advisor에 대한 구독을 등록합니다. |
> | 작업 | Microsoft.Advisor/unregister/action | Microsoft Advisor에 대한 구독을 등록 취소합니다. |
> |  | **구성** |  |
> | 작업 | Microsoft.Advisor/configurations/read | 구성 가져오기 |
> | 작업 | Microsoft.Advisor/configurations/write | 구성을 만듭니다/업데이트합니다. |
> |  | **generateRecommendations** |  |
> | 작업 | Microsoft.Advisor/generateRecommendations/read | 권장 사항 생성 상태를 가져옵니다. |
> |  | **메타 데이터** |  |
> | 작업 | 마이크로소프트.어드바이저/메타데이터/읽기 | 메타데이터 가져오기 |
> |  | **작업** |  |
> | 작업 | Microsoft.Advisor/operations/read | Microsoft Advisor에 대한 작업을 가져옵니다. |
> |  | **권장 사항** |  |
> | 작업 | Microsoft.Advisor/recommendations/read | 권장 사항을 읽습니다. |
> | 작업 | Microsoft.Advisor/recommendations/available/action | 새로운 권장 사항은 Microsoft 어드바이저에서 확인할 수 있습니다. |
> |  | **권장 사항/억제** |  |
> | 작업 | Microsoft.Advisor/recommendations/suppressions/read | 비표시 오류(Suppression)를 가져옵니다. |
> | 작업 | Microsoft.Advisor/recommendations/suppressions/write | 비표시 오류(Suppression)를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Advisor/recommendations/suppressions/delete | 비표시 오류(Suppression)를 삭제합니다. |
> |  | **suppressions** |  |
> | 작업 | Microsoft.Advisor/suppressions/read | 비표시 오류(Suppression)를 가져옵니다. |
> | 작업 | Microsoft.Advisor/suppressions/write | 비표시 오류(Suppression)를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Advisor/suppressions/delete | 비표시 오류(Suppression)를 삭제합니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

Azure 서비스: [Azure 모니터](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.AlertsManagement/register/action | Microsoft 경고 관리에 대한 구독을 등록합니다. |
> |  | **actionRules** |  |
> | 작업 | Microsoft.AlertsManagement/actionRules/read | 입력 필터에 대한 모든 작업 규칙을 가져옵니다. |
> | 작업 | Microsoft.AlertsManagement/actionRules/write | 지정된 구독에서 작업 규칙을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.AlertsManagement/actionRules/delete | 지정된 구독에서 작업 규칙을 삭제합니다. |
> |  | **경고** |  |
> | 작업 | Microsoft.AlertsManagement/alerts/read | 입력 필터에 대한 모든 경고를 가져옵니다. |
> | 작업 | Microsoft.AlertsManagement/alerts/changestate/action | 경고 상태를 변경합니다. |
> |  | **경고/진단** |  |
> | 작업 | Microsoft.AlertsManagement/alerts/diagnostics/read | 경고에 대한 모든 진단을 가져옵니다. |
> |  | **경고/기록** |  |
> | 작업 | Microsoft.AlertsManagement/alerts/history/read | 경고 기록을 가져옵니다. |
> |  | **alertsList** |  |
> | 작업 | Microsoft.AlertsManagement/alertsList/read | 전체 구독에서 입력 필터에 대한 모든 경고를 가져옵니다. |
> |  | **알림메타데이터** |  |
> | 작업 | 마이크로 소프트.경고관리 / 경고메타 데이터 / 읽기 | 입력 매개 변수에 대한 알림 메타 데이터를 가져옵니다. |
> |  | **alertsSummary** |  |
> | 작업 | Microsoft.AlertsManagement/alertsSummary/read | 경고의 요약 정보를 가져옵니다. |
> |  | **alertsSummaryList** |  |
> | 작업 | Microsoft.AlertsManagement/alertsSummaryList/read | 구독 전체의 경고에 대한 요약을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.AlertsManagement/Operations/read | 제공된 작업을 읽습니다. |
> |  | **smartDetectorAlertRules** |  |
> | 작업 | 마이크로 소프트.경고관리 / 스마트 디텍터경고규칙 / 쓰기 | 지정된 구독에서 스마트 탐지기 경고 규칙 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.경고관리/스마트 디텍터 경고규칙/읽기 | 입력 필터에 대한 모든 스마트 검출기 경고 규칙 받기 |
> | 작업 | 마이크로 소프트.경고 관리 / 스마트 디텍터경고규칙 / 삭제 | 지정된 구독에서 스마트 탐지기 경고 규칙 삭제 |
> |  | **smartGroups** |  |
> | 작업 | Microsoft.AlertsManagement/smartGroups/read | 입력 필터에 대한 모든 스마트 그룹을 가져옵니다. |
> | 작업 | Microsoft.AlertsManagement/smartGroups/changestate/action | 스마트 그룹의 상태를 변경합니다. |
> |  | **스마트 그룹/기록** |  |
> | 작업 | Microsoft.AlertsManagement/smartGroups/history/read | 스마트 그룹 기록을 가져옵니다. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

Azure 서비스: [Azure 분석 서비스](../analysis-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.AnalysisServices/register/action | Analysis Services 리소스 공급자를 등록합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 지정된 Analysis Server 이름이 올바른지와 사용되고 있지 않은지 확인합니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.AnalysisServices/locations/operationresults/read | 지정된 작업 결과에 대한 정보를 검색합니다. |
> |  | **위치/작업 상태** |  |
> | 작업 | Microsoft.AnalysisServices/locations/operationstatuses/read | 지정된 작업 상태에 대한 정보를 검색합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.AnalysisServices/operations/read | 작업 정보를 검색합니다. |
> |  | **서버** |  |
> | 작업 | Microsoft.AnalysisServices/servers/read | 지정된 Analysis Server의 정보를 검색합니다. |
> | 작업 | Microsoft.AnalysisServices/servers/write | 지정된 Analysis Server를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.AnalysisServices/servers/delete | Analysis Server를 삭제합니다. |
> | 작업 | Microsoft.AnalysisServices/servers/suspend/action | Analysis Server를 일시 중단합니다. |
> | 작업 | Microsoft.AnalysisServices/servers/resume/action | Analysis Server를 계속합니다. |
> | 작업 | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 서버와 연결된 게이트웨이 상태를 나열합니다. |
> |  | **서버/스쿠스** |  |
> | 작업 | Microsoft.AnalysisServices/servers/skus/read | 서버에 사용 가능한 SKU 정보를 검색합니다. |
> |  | **Sku** |  |
> | 작업 | Microsoft.AnalysisServices/skus/read | SKU 정보를 검색합니다. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

Azure 서비스: [API 관리](../api-management/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ApiManagement/register/action | Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록합니다. |
> | 작업 | Microsoft.ApiManagement/unregister/action | Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록 취소합니다. |
> |  | **확인이름가용성** |  |
> | 작업 | Microsoft.ApiManagement/checkNameAvailability/read | 제공된 서비스 이름을 사용할 수 있는지 확인합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ApiManagement/operations/read | Microsoft.ApiManagement 리소스에 제공되는 모든 API 작업을 읽습니다. |
> |  | **보고서** |  |
> | 작업 | Microsoft.ApiManagement/reports/read | 기간, 지역, 개발자, 제품, API, 작업, 구독 및 byRequest별로 집계된 보고서를 가져옵니다. |
> |  | **서비스** |  |
> | 작업 | Microsoft.ApiManagement/service/write | API 관리 서비스 인스턴스 생성 또는 업데이트 |
> | 작업 | Microsoft.ApiManagement/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | 작업 | Microsoft.ApiManagement/service/delete | API Management 서비스 인스턴스를 삭제합니다. |
> | 작업 | Microsoft.ApiManagement/service/updatehostname/action | API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다. |
> | 작업 | Microsoft.ApiManagement/service/updatecertificate/action | API 관리 서비스에 대한 TLS/SSL 인증서 업로드 |
> | 작업 | Microsoft.ApiManagement/service/backup/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다. |
> | 작업 | Microsoft.ApiManagement/service/restore/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너에서 API Management 서비스 복원 |
> | 작업 | Microsoft.ApiManagement/service/managedeployments/action | SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다. |
> | 작업 | Microsoft.ApiManagement/service/getssotoken/action | API Management 서비스 레거시 포털에 관리자로 로그인하는 데 사용할 수 있는 SSO 토큰을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Virtual Network에서 실행되는 Microsoft.ApiManagement 리소스를 업데이트하여 업데이트된 네트워크 설정을 선택합니다. |
> | 작업 | Microsoft.ApiManagement/service/users/action | 새 사용자를 등록합니다. |
> | 작업 | Microsoft.ApiManagement/service/notifications/action | 지정된 사용자에게 알림을 보냅니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/게이트웨이/작업 | 게이트웨이 구성을 검색합니다. 또는 게이트웨이 하트비트를 업데이트합니다. |
> |  | **서비스/APIS** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/read | API 관리 서비스 인스턴스의 모든 API를 나열합니다. 또는 식별자가 지정한 API의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/write | API 관리 서비스 인스턴스의 기존 지정된 API를 새로 또는 업데이트합니다. 또는 API 관리 서비스 인스턴스의 지정된 API를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/delete | API 관리 서비스 인스턴스의 지정된 API를 삭제합니다. |
> |  | **서비스/APIS/진단** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/diagnostics/read | API의 모든 진단을 나열합니다. 또는 식별자가 지정한 API에 대한 진단의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/diagnostics/write | API에 대한 새 진단을 만들거나 기존 진단을 업데이트합니다. 또는 식별자가 지정한 API에 대한 진단의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/diagnostics/delete | API에서 지정된 진단을 삭제합니다. |
> |  | **서비스/APIs/문제** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/read | 지정된 API와 연결된 모든 문제를 나열합니다. 또는 식별자가 지정한 API에 대한 문제의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/write | API에 대한 새 문제를 만들거나 기존 문제를 업데이트합니다. 또는 API에 대한 기존 문제를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/delete | API에서 지정된 문제를 삭제합니다. |
> |  | **서비스/AP-APIs/문제/첨부 파일** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/attachments/read | 지정된 API와 연결된 문제에 대한 모든 첨부 파일을 나열합니다. 또는 식별자가 지정한 API에 대한 문제 첨부 파일의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/attachments/write | API에서 문제에 대한 새 첨부 파일을 만들거나 기존 첨부 파일을 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 문제에서 지정된 주석을 삭제합니다. |
> |  | **서비스/APIS/문제/코멘트** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/comments/read | 지정된 API와 연결된 문제에 대한 모든 주석을 나열합니다. 또는 식별자가 지정한 API에 대한 문제 주석의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/comments/write | API에서 문제에 대한 새 주석을 만들거나 기존 주석을 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/issues/comments/delete | 문제에서 지정된 주석을 삭제합니다. |
> |  | **서비스/APIS/운영** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/read | 지정된 API에 대한 작업 컬렉션을 나열합니다. 또는 식별자가 지정한 API 작업의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/write | API에서 새 작업을 만들거나 기존 작업을 업데이트합니다. 또는 식별자가 지정한 API에서 작업의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/delete | API에서 지정된 작업을 삭제합니다. |
> |  | **서비스/APIS/운영/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/policies/read | API 작업 수준에서 정책 구성 목록을 가져옵니다. 또는 API 작업 수준에서 정책 구성을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/policies/write | API 작업 수준에 대한 정책 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/policies/delete | Api 작업에서 정책 구성을 삭제합니다. |
> |  | **서비스/APIS/운영/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/policy/read | 작업 수준에서 정책 구성 받기 |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/policy/write | 작업 수준에서 정책 구성 만들기 |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/policy/delete | 작업 수준에서 정책 구성 삭제 |
> |  | **서비스/APIS/운영/태그** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/tags/read | 작업과 연결된 모든 태그를 나열합니다. 또는 작업과 연결된 태그를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/tags/write | 작업에 태그를 할당합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/operations/tags/delete | 작업에서 태그를 분리합니다. |
> |  | **서비스/APIS/운영ByTags** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/operationsByTags/read | 태그와 연결된 작업 컬렉션을 나열합니다. |
> |  | **서비스/APIS/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/policies/read | API 수준에서 정책 구성을 가져옵니다. 또는 API 수준에서 정책 구성을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/policies/write | API에 대한 정책 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/policies/delete | Api에서 정책 구성을 삭제합니다. |
> |  | **서비스/APIS/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/policy/read | API 수준에서 정책 구성 받기 |
> | 작업 | Microsoft.ApiManagement/service/apis/policy/write | API 수준에서 정책 구성 만들기 |
> | 작업 | Microsoft.ApiManagement/service/apis/policy/delete | API 수준에서 정책 구성 삭제 |
> |  | **서비스/APIS/제품** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/products/read | API가 속한 모든 제품을 나열합니다. |
> |  | **서비스/APIS/릴리스** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/releases/read | API의 모든 릴리스를 나열합니다.<br>API 릴리스는 API 개정을 최신 상태로 만들 때 만들어집니다.<br>릴리스는 이전 개정으로 롤백하는 데도 사용됩니다.<br>결과는 페이징되며 $top 및 $skip 매개 변수에 의해 제한될 수 있습니다.<br>또는 API 릴리스의 세부 정보를 반환합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/releases/delete | API의 모든 릴리스를 제거하거나 API에서 지정된 릴리스를 삭제합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/releases/write | API에 대한 새 릴리스를 만듭니다. 또는 식별자가 지정한 API 릴리스의 세부 정보를 업데이트합니다. |
> |  | **서비스/APIS/개정** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/revisions/read | API의 모든 개정을 나열합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/revisions/delete | API의 모든 수정 버전을 제거합니다. |
> |  | **서비스/APIS/스키마** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/schemas/read | API 수준에서 스키마 구성을 가져옵니다. 또는 API 수준에서 스키마 구성을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/schemas/write | API에 대한 스키마 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/schemas/delete | Api에서 스키마 구성을 삭제합니다. |
> |  | **서비스/APIS/태그 설명** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/tagDescriptions/read | API 범위에 있는 모든 태그 설명을 나열합니다. swagger와 유사한 모델 - 태그설명은 API 수준에서 정의되지만 API 범위에서 운영 또는 태그 받기 설명에 태그가 할당될 수 있습니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Api 의 범위에서 태그 설명을 생성/업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Api에 대한 태그 설명을 삭제합니다. |
> |  | **서비스/APIS/태그** |  |
> | 작업 | Microsoft.ApiManagement/service/apis/tags/read | API와 연결된 모든 태그를 나열합니다. 또는 API와 연결된 태그를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/tags/write | Api에 태그를 할당합니다. |
> | 작업 | Microsoft.ApiManagement/service/apis/tags/delete | Api에서 태그를 분리합니다. |
> |  | **서비스/apisByTags** |  |
> | 작업 | Microsoft.ApiManagement/service/apisByTags/read | 태그와 연결된 API 컬렉션을 나열합니다. |
> |  | **서비스/api버전세트** |  |
> | 작업 | Microsoft.ApiManagement/service/apiVersionSets/read | 지정된 서비스 인스턴스에 API 버전 집합 의 컬렉션을 나열합니다. 또는 식별자가 지정한 Api 버전 집합의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/apiVersionSets/write | Api 버전 집합을 만들거나 업데이트합니다. 또는 식별자가 지정한 Api VersionSet의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/apiVersionSets/delete | 특정 Api 버전 집합을 삭제합니다. |
> |  | **서비스/api버전세트/버전** |  |
> | 작업 | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 버전 엔터티 목록을 가져옵니다. |
> |  | **서비스/권한 부여서버** |  |
> | 작업 | Microsoft.ApiManagement/service/authorizationServers/read | 서비스 인스턴스 내에 정의된 권한 부여 서버 의 컬렉션을 나열합니다. 또는 비밀없이 권한 부여 서버의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/authorizationServers/write | 새 권한 부여 서버를 만들거나 기존 권한 부여 서버를 업데이트합니다. 또는 식별자가 지정한 권한 부여 서버의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/authorizationServers/delete | 특정 권한 부여 서버 인스턴스를 삭제합니다. |
> | 작업 | 마이크로 소프트.Api관리 / 서비스 / 권한 부여서버 / 목록비밀 / 작업 | 권한 부여 서버에 대한 비밀을 가져옵니다. |
> |  | **서비스/백엔드** |  |
> | 작업 | Microsoft.ApiManagement/service/backends/read | 지정된 서비스 인스턴스에 백 엔드 컬렉션을 나열합니다. 또는 식별자가 지정한 백 엔드의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/backends/write | 백 엔드를 만들거나 업데이트합니다. 또는 기존 백 엔드를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/backends/delete | 지정된 백 엔드를 삭제합니다. |
> | 작업 | Microsoft.ApiManagement/service/backends/reconnect/action | 지정된 시간 지정 후 백 엔드에 대한 새 연결을 만들도록 APIM 프록시에 대해 지정합니다. 시간 초과를 지정하지 않으면 2분의 시간 초과가 사용됩니다. |
> |  | **서비스/캐시** |  |
> | 작업 | 마이크로소프트.Api관리/서비스/캐시/읽기 | 지정된 서비스 인스턴스에 모든 외부 캐시 컬렉션을 나열합니다. 또는 식별자가 지정한 캐시의 세부 정보를 가져옵니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/캐시/쓰기 | Api 관리 인스턴스에서 사용할 외부 캐시를 만들거나 업데이트합니다. 또는 식별자가 지정한 캐시의 세부 정보를 업데이트합니다. |
> | 작업 | 마이크로소프트.Api관리/서비스/캐시/삭제 | 특정 캐시를 삭제합니다. |
> |  | **서비스/인증서** |  |
> | 작업 | Microsoft.ApiManagement/service/certificates/read | 지정된 서비스 인스턴스에 모든 인증서 컬렉션을 나열합니다. 또는 식별자가 지정한 인증서의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/certificates/write | 백 엔드를 사용하여 인증에 사용되는 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/certificates/delete | 특정 인증서를 삭제합니다. |
> |  | **서비스/콘텐츠유형** |  |
> | 작업 | Microsoft.ApiManagement/service/contentTypes/read | 콘텐츠 형식을 반환합니다. |
> |  | **서비스/콘텐츠유형/콘텐츠항목** |  |
> | 작업 | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 콘텐츠 항목 목록을 반환하거나 콘텐츠 항목 세부 정보를 반환합니다. |
> | 작업 | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 새 콘텐츠 항목을 만들거나 지정된 콘텐츠 항목을 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 지정된 콘텐츠 항목을 제거합니다. |
> |  | **서비스/진단** |  |
> | 작업 | Microsoft.ApiManagement/service/diagnostics/read | API 관리 서비스 인스턴스의 모든 진단을 나열합니다. 또는 식별자가 지정한 진단의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/diagnostics/write | 새 진단을 만들거나 기존 진단을 업데이트합니다. 또는 식별자가 지정한 진단의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/diagnostics/delete | 지정된 진단을 삭제합니다. |
> |  | **서비스/게이트웨이** |  |
> | 작업 | 마이크로소프트.ApiManagement/서비스/게이트웨이/읽기 | 서비스 인스턴스에 등록된 게이트웨이 컬렉션을 나열합니다. 또는 식별자가 지정한 게이트웨이의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/쓰기 | Api 관리 인스턴스에서 사용할 게이트웨이를 만들거나 업데이트합니다. 또는 식별자가 지정한 게이트웨이의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/삭제 | 특정 게이트웨이를 삭제합니다. |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/목록 키/작업 | 게이트웨이 키를 검색합니다. |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/다시 생성키/작업 | 지정된 게이트웨이 키 무효화를 통해 생성된 모든 토큰을 재생성합니다. |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/생성 토큰/작업 | 게이트웨이에 대한 공유 액세스 권한 부여 토큰을 가져옵니다. |
> |  | **서비스/게이트웨이/API** |  |
> | 작업 | 마이크로소프트.ApiManagement/서비스/게이트웨이/apis/읽기 | 게이트웨이와 연결된 API 컬렉션을 나열합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/게이트웨이/APIS/쓰기 | 지정된 게이트웨이에 API를 추가합니다. |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/APIS/삭제 | 지정된 게이트웨이에서 지정된 API를 삭제합니다. |
> |  | **서비스/게이트웨이/호스트 이름 구성** |  |
> | 작업 | Microsoft.ApiManagement/서비스/게이트웨이/호스트 이름 구성/읽기 | 지정된 게이트웨이에 대한 호스트 이름 구성 컬렉션을 나열합니다. |
> |  | **서비스/그룹** |  |
> | 작업 | Microsoft.ApiManagement/service/groups/read | 서비스 인스턴스 내에서 정의된 그룹의 컬렉션을 나열합니다. 또는 식별자가 지정한 그룹의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/groups/write | 그룹을 만들거나 업데이트합니다. 또는 식별자가 지정한 그룹의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/groups/delete | API 관리 서비스 인스턴스의 특정 그룹을 삭제합니다. |
> |  | **서비스/그룹/사용자** |  |
> | 작업 | Microsoft.ApiManagement/service/groups/users/read | 그룹과 연결된 사용자 엔터티의 컬렉션을 나열합니다. |
> | 작업 | Microsoft.ApiManagement/service/groups/users/write | 기존 그룹에 기존 사용자를 추가합니다. |
> | 작업 | Microsoft.ApiManagement/service/groups/users/delete | 기존 그룹에서 기존 사용자를 제거합니다. |
> |  | **서비스/ID공급자** |  |
> | 작업 | Microsoft.ApiManagement/service/identityProviders/read | 지정된 서비스 인스턴스에서 구성된 ID 공급자의 컬렉션을 나열합니다. 또는 비밀 없이 ID 공급자의 구성 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/identityProviders/write | IDProvider 구성을 만들거나 업데이트합니다. 또는 기존 IDProvider 구성을 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/identityProviders/delete | 지정된 ID 공급자 구성을 삭제합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/ID프로바이더/목록비밀/작업 | ID 공급자 비밀을 가져옵니다. |
> |  | **서비스/문제** |  |
> | 작업 | 마이크로소프트.Api관리/서비스/문제/읽기 | 지정된 서비스 인스턴스에 문제 컬렉션을 나열합니다. 또는 API 관리 문제 세부 정보 가져오기 |
> |  | **서비스/위치/네트워크 상태** |  |
> | 작업 | Microsoft.ApiManagement/service/locations/networkstatus/read | 해당 위치에는 서비스가 종속되는 리소스의 네트워크 액세스 상태를 가져옵니다. |
> |  | **서비스/로거** |  |
> | 작업 | Microsoft.ApiManagement/service/loggers/read | 지정된 서비스 인스턴스에 로거 컬렉션을 나열합니다. 또는 식별자가 지정한 로거의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/loggers/write | 로거를 만들거나 업데이트합니다. 또는 기존 로거를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/loggers/delete | 지정된 로거를 삭제합니다. |
> |  | **서비스/명명된값** |  |
> | 작업 | 마이크로소프트.ApiManagement/서비스/명명된 값/읽기 | 서비스 인스턴스 내에 정의된 명명된 값의 컬렉션을 나열합니다. 또는 식별자에서 지정한 명명된 값의 세부 정보를 가져옵니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/명명된 값/쓰기 | 명명된 값을 만들거나 업데이트합니다. 또는 특정 명명된 값을 업데이트합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/명명된 값/삭제 | API 관리 서비스 인스턴스에서 특정 명명된 값을 삭제합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/명명된 값/목록값/작업 | 식별자에서 지정한 명명된 값의 비밀을 가져옵니다. |
> |  | **서비스/네트워크 상태** |  |
> | 작업 | Microsoft.ApiManagement/service/networkstatus/read | 서비스가 종속되는 리소스의 네트워크 액세스 상태를 가져옵니다. |
> |  | **서비스/알림** |  |
> | 작업 | Microsoft.ApiManagement/service/notifications/read | 서비스 인스턴스 내에 정의된 속성 컬렉션을 나열합니다. 또는 식별자가 지정한 알림의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/notifications/write | API 관리 게시자 알림을 만들거나 업데이트합니다. |
> |  | **서비스/알림/수신자이메일** |  |
> | 작업 | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 알림 수신자 이메일 목록을 알림에 구독합니다. |
> | 작업 | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 알림 수신자 목록에 전자 메일 주소를 추가합니다. |
> | 작업 | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 알림 목록에서 전자 메일을 제거합니다. |
> |  | **서비스 /알림 / 수신자사용자** |  |
> | 작업 | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 알림을 구독한 알림 받는 사람의 목록을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/notifications/recipientUsers/write | API 관리 사용자를 알림수신자 목록에 추가합니다. |
> | 작업 | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 알림 목록에서 API 관리 사용자를 제거합니다. |
> |  | **서비스/오픈리드커더** |  |
> | 작업 | Microsoft.ApiManagement/service/openidConnectProviders/read | 모든 OpenId 연결 공급자 목록입니다. 또는 비밀없이 특정 OpenID 연결 공급자를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/openidConnectProviders/write | OpenID 연결 공급자를 만들거나 업데이트합니다. 또는 특정 OpenID 연결 공급자를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/openidConnectProviders/delete | API 관리 서비스 인스턴스의 특정 OpenID 연결 공급자를 삭제합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/오픈 idConnectProviders/목록비밀/작업 | 특정 OpenID 연결 공급자 비밀을 가져옵니다. |
> |  | **서비스/작업 결과** |  |
> | 작업 | Microsoft.ApiManagement/service/operationresults/read | 장기 실행 작업의 현재 상태를 가져옵니다. |
> |  | **서비스/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/policies/read | Api 관리 서비스의 모든 전역 정책 정의를 나열합니다. 또는 Api 관리 서비스의 전역 정책 정의를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/policies/write | Api 관리 서비스의 전역 정책 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/policies/delete | Api 관리 서비스의 전역 정책 구성을 삭제합니다. |
> |  | **서비스/정책** |  |
> | 작업 | 마이크로소프트.Api관리/서비스/정책/읽기 | 테넌트 수준에서 정책 구성 받기 |
> | 작업 | 마이크로소프트.ApiManagement/서비스/정책/쓰기 | 테넌트 수준에서 정책 구성 만들기 |
> | 작업 | Microsoft.Api관리/서비스/정책/삭제 | 테넌트 수준에서 정책 구성 삭제 |
> |  | **서비스/정책설명** |  |
> | 작업 | Microsoft.Api관리/서비스/정책 설명/읽기 | 모든 정책 설명을 나열합니다. |
> |  | **서비스/정책스니펫** |  |
> | 작업 | Microsoft.ApiManagement/service/policySnippets/read | 모든 정책 조각을 나열합니다. |
> |  | **서비스/포털 설정** |  |
> | 작업 | Microsoft.ApiManagement/service/portalsettings/read | 포털 설정의 컬렉션을 나열합니다. 또는 포털에 대한 로그인 설정 받기 또는 포털에 대한 등록 설정 받기 또는 포털에 대한 위임 설정 받기 |
> | 작업 | Microsoft.ApiManagement/service/portalsettings/write | 로그인 설정을 업데이트합니다. 또는 로그인 설정 만들기 또는 업데이트. 또는 등록 설정 업데이트 또는 등록 설정 업데이트 또는 위임 업데이트 설정. 또는 위임 설정 만들기 또는 업데이트 |
> | 작업 | Microsoft.ApiManagement/서비스/포털 설정/목록비밀/작업 | 포털 위임 설정의 유효성 검사 키를 가져옵니다. |
> |  | **서비스/제품** |  |
> | 작업 | Microsoft.ApiManagement/service/products/read | 지정된 서비스 인스턴스에 제품 컬렉션을 나열합니다. 또는 식별자가 지정한 제품의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/products/write | 제품을 만들거나 업데이트합니다. 또는 기존 제품 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/delete | 제품을 삭제합니다. |
> |  | **서비스/제품/APIS** |  |
> | 작업 | Microsoft.ApiManagement/service/products/apis/read | 제품과 연결된 API 컬렉션을 나열합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/apis/write | 지정된 제품에 API를 추가합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/apis/delete | 지정된 제품에서 지정된 API를 삭제합니다. |
> |  | **서비스/제품/그룹** |  |
> | 작업 | Microsoft.ApiManagement/service/products/groups/read | 지정된 제품과 연결된 개발자 그룹의 컬렉션을 나열합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/groups/write | 지정된 개발자 그룹과 지정된 제품 간의 연결을 추가합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/groups/delete | 지정된 그룹과 제품 간의 연결을 삭제합니다. |
> |  | **서비스/제품/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/products/policies/read | 제품 수준에서 정책 구성을 가져옵니다. 또는 제품 수준에서 정책 구성을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/products/policies/write | 제품에 대한 정책 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/policies/delete | 제품에서 정책 구성을 삭제합니다. |
> |  | **서비스/제품/정책** |  |
> | 작업 | Microsoft.ApiManagement/service/products/policy/read | 제품 수준에서 정책 구성 받기 |
> | 작업 | Microsoft.ApiManagement/service/products/policy/write | 제품 수준에서 정책 구성 만들기 |
> | 작업 | Microsoft.ApiManagement/service/products/policy/delete | 제품 수준에서 정책 구성 삭제 |
> |  | **서비스/제품/구독** |  |
> | 작업 | Microsoft.ApiManagement/service/products/subscriptions/read | 지정된 제품에 대한 구독 컬렉션을 나열합니다. |
> |  | **서비스/제품/태그** |  |
> | 작업 | Microsoft.ApiManagement/service/products/tags/read | 제품과 연결된 모든 태그를 나열합니다. 또는 제품과 연결된 태그를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/products/tags/write | 제품에 태그를 할당합니다. |
> | 작업 | Microsoft.ApiManagement/service/products/tags/delete | 제품에서 태그를 분리합니다. |
> |  | **서비스/제품ByTags** |  |
> | 작업 | Microsoft.ApiManagement/service/productsByTags/read | 태그와 연결된 제품의 컬렉션을 나열합니다. |
> |  | **서비스/속성** |  |
> | 작업 | Microsoft.ApiManagement/service/properties/read | 서비스 인스턴스 내에 정의된 속성 컬렉션을 나열합니다. 또는 식별자가 지정한 속성의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/properties/write | 속성을 만들거나 업데이트합니다. 또는 특정 속성을 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/properties/delete | API 관리 서비스 인스턴스에서 특정 속성을 삭제합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/속성/목록비밀/작업 | 식별자가 지정한 속성의 비밀을 가져옵니다. |
> |  | **서비스/할당량** |  |
> | 작업 | Microsoft.ApiManagement/service/quotas/read | 할당량에 대한 값을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/quotas/write | 할당량 카운터 현재 값을 설정합니다. |
> |  | **서비스/할당량/기간** |  |
> | 작업 | Microsoft.ApiManagement/service/quotas/periods/read | 기간에 대한 할당량 카운터 값을 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/quotas/periods/write | 할당량 카운터 현재 값을 설정합니다. |
> |  | **서비스/지역** |  |
> | 작업 | 마이크로소프트.ApiManagement/서비스/지역/읽기 | 서비스가 있는 모든 azure 영역을 나열합니다. |
> |  | **서비스/보고서** |  |
> | 작업 | Microsoft.ApiManagement/service/reports/read | 기간별로 집계된 보고서를 가져오거나 지역별로 집계된 보고서를 가져오거나 개발자별로 집계된 보고서를 가져옵니다.<br>또는 제품별로 집계된 보고서를 가져옵니다.<br>또는 API별로 집계된 보고서를 가져오거나 작업별로 집계된 보고서를 가져오거나 구독별로 집계된 보고서를 가져옵니다.<br>또는 데이터 보고 요청을 가져옵니다. |
> |  | **서비스/구독** |  |
> | 작업 | Microsoft.ApiManagement/service/subscriptions/read | API 관리 서비스 인스턴스의 모든 구독을 나열합니다. 또는 키 없이 지정된 구독 엔터티를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/subscriptions/write | 지정된 사용자의 구독을 지정된 제품에 만들거나 업데이트합니다. 또는 식별자가 지정한 구독의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/subscriptions/delete | 지정된 구독을 삭제합니다. |
> | 작업 | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | API 관리 서비스 인스턴스의 기존 구독의 기본 키를 다시 생성합니다. |
> | 작업 | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | API 관리 서비스 인스턴스의 기존 구독의 보조 키를 다시 생성합니다. |
> | 작업 | 마이크로소프트.ApiManagement/서비스/구독/목록비밀/작업 | 지정된 구독 키를 가져옵니다. |
> |  | **서비스/태그리소스** |  |
> | 작업 | Microsoft.ApiManagement/service/tagResources/read | 태그와 연결된 리소스 컬렉션을 나열합니다. |
> |  | **서비스/태그** |  |
> | 작업 | Microsoft.ApiManagement/service/tags/read | 서비스 인스턴스 내에 정의된 태그 컬렉션을 나열합니다. 또는 식별자가 지정한 태그의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/tags/write | 태그를 만듭니다. 또는 식별자가 지정한 태그의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/tags/delete | API 관리 서비스 인스턴스의 특정 태그를 삭제합니다. |
> |  | **서비스/템플릿** |  |
> | 작업 | Microsoft.ApiManagement/service/templates/read | 모든 이메일 템플릿을 가져오거나 API Management 이메일 템플릿 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/templates/write | API Management 이메일 템플릿을 만들거나/업데이트하거나 API Management 이메일 템플릿을 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/templates/delete | 기본 API Management 이메일 템플릿을 다시 설정합니다. |
> |  | **서비스/테넌트** |  |
> | 작업 | Microsoft.ApiManagement/service/tenant/read | Api 관리 서비스의 전역 정책 정의를 가져옵니다. 또는 테넌트 액세스 정보 세부 정보 가져오기 |
> | 작업 | Microsoft.ApiManagement/service/tenant/write | 테넌트에 대한 정책 구성을 설정하거나 테넌트 액세스 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/tenant/delete | 테넌트에 대한 정책 구성을 제거합니다. |
> | 작업 | Microsoft.ApiManagement/서비스/테넌트/목록비밀/작업 | 테넌트 액세스 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 기본 액세스 키를 다시 생성합니다. |
> | 작업 | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 보조 액세스 키를 다시 생성합니다. |
> | 작업 | Microsoft.ApiManagement/service/tenant/deploy/action | 배포 작업을 실행하여 지정된 git 분기에서 데이터베이스의 구성에 대해 변경된 내용을 적용합니다. |
> | 작업 | Microsoft.ApiManagement/service/tenant/save/action | 리포지토리의 지정된 분기에 대한 구성 스냅샷을 사용하여 커밋을 만듭니다. |
> | 작업 | Microsoft.ApiManagement/service/tenant/validate/action | 지정된 git 분기에서 변경된 내용이 유효한지 검사합니다. |
> |  | **서비스/테넌트/운영결과** |  |
> | 작업 | Microsoft.ApiManagement/service/tenant/operationResults/read | 작업 결과 목록을 가져오거나 특정 작업의 결과를 가져옵니다. |
> |  | **서비스/테넌트/동기화상태** |  |
> | 작업 | Microsoft.ApiManagement/service/tenant/syncState/read | 마지막 git 동기화의 상태를 가져옵니다. |
> |  | **서비스/사용자** |  |
> | 작업 | Microsoft.ApiManagement/service/users/read | 지정된 서비스 인스턴스에 등록된 사용자 컬렉션을 나열합니다. 또는 식별자가 지정한 사용자의 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.ApiManagement/service/users/write | 사용자를 만들거나 업데이트합니다. 또는 식별자가 지정한 사용자의 세부 정보를 업데이트합니다. |
> | 작업 | Microsoft.ApiManagement/service/users/delete | 특정 사용자를 삭제합니다. |
> | 작업 | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 지정된 사용자를 개발자 포털에 로그인하기 위한 인증 토큰이 포함된 리디렉션 URL을 검색합니다. |
> | 작업 | Microsoft.ApiManagement/service/users/token/action | 사용자에 대한 공유 액세스 권한 부여 토큰을 가져옵니다. |
> |  | **서비스/사용자/확인** |  |
> | 작업 | Microsoft.ApiManagement/service/users/confirmations/send/action | 확인을 보냅니다. |
> |  | **서비스/사용자/그룹** |  |
> | 작업 | Microsoft.ApiManagement/service/users/groups/read | 모든 사용자 그룹을 나열합니다. |
> |  | **서비스/사용자/ID** |  |
> | 작업 | Microsoft.ApiManagement/서비스/사용자/ID/읽기 | 모든 사용자 ID 목록입니다. |
> |  | **서비스/사용자/키** |  |
> | 작업 | Microsoft.ApiManagement/service/users/keys/read | 사용자와 연결된 키 받기 |
> |  | **서비스/사용자/구독** |  |
> | 작업 | Microsoft.ApiManagement/service/users/subscriptions/read | 지정된 사용자의 구독 컬렉션을 나열합니다. |

## <a name="microsoftappconfiguration"></a>마이크로소프트.앱구성

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.앱구성/등록/작업 | Microsoft 앱 구성을 사용하도록 구독을 등록합니다. |
> |  | **확인이름가용성** |  |
> | 작업 | 마이크로소프트.App구성/체크네임가용성/읽기 | 리소스 이름을 사용할 수 있는지 확인합니다. |
> |  | **구성 스토어** |  |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/읽기 | 지정된 구성 저장소의 속성을 가져옵니다 또는 지정된 리소스 그룹 또는 구독 아래 모든 구성 저장소를 나열합니다. |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/쓰기 | 지정된 매개 변수를 사용하여 구성 저장소를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.App구성/구성스토어/삭제 | 구성 저장소를 삭제합니다. |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/목록 키/작업 | 지정된 구성 저장소의 API 키를 나열합니다. |
> | 작업 | Microsoft.AppConfiguration/구성스토어/재생성키/액션 | 지정된 구성 저장소에 대한 API 키를 재생성합니다. |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/리스트키밸류/액션 | 지정된 구성 저장소에 대한 키 값을 나열합니다. |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/프라이빗엔드포인트연결승인/액션 | 지정된 구성 저장소에서 개인 끝점 연결을 자동으로 승인합니다. |
> |  | **구성스토어/이벤트그리드필터** |  |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/이벤트그리드필터/읽기 | 지정된 구성 저장소 이벤트 그리드 필터의 속성을 가져옵니다 또는 지정된 구성 저장소 아래에 모든 구성 저장소 이벤트 그리드 필터를 나열합니다. |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/이벤트그리드필터/쓰기 | 지정된 매개 변수를 사용하여 구성 저장소 이벤트 그리드 필터를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.App구성/구성스토어/이벤트그리드필터/삭제 | 구성 저장소 이벤트 그리드 필터를 삭제합니다. |
> |  | **구성상점/프라이빗엔드포인트커넥션** |  |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/프라이빗엔드포인트커넥션프록시/유효성 검사/조치 | 지정된 구성 저장소에서 개인 끝점 연결 프록시의 유효성을 검사합니다. |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/사설엔드포인트커넥션프록시스/읽기 | 지정된 구성 저장소에서 개인 끝점 연결 프록시를 가져옵니다. |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/사설엔드포인트커넥션프록시/쓰기 | 지정된 구성 저장소에서 개인 끝점 연결 프록시를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/프라이빗엔드포인트커넥션프록시스/삭제 | 지정된 구성 저장소에서 개인 끝점 연결 프록시를 삭제합니다. |
> |  | **구성스토어/프라이빗엔드포인트커넥션** |  |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/프라이빗엔드포인트연결/읽기 | 개인 끝점 연결을 얻거나 지정된 구성 저장소 에서 개인 끝점 연결을 나열합니다. |
> | 작업 | 마이크로소프트.앱 구성/구성스토어/프라이빗엔드포인트연결/쓰기 | 지정된 구성 저장소에서 개인 끝점 연결을 승인하거나 거부합니다. |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/프라이빗엔드포인트연결/삭제 | 지정된 구성 저장소에서 개인 끝점 연결을 삭제합니다. |
> |  | **구성스토어/프라이빗링크리소스** |  |
> | 작업 | 마이크로소프트.App구성/구성스토어/프라이빗링크리소시스/읽기 | 지정된 구성 저장소 아래에 모든 개인 링크 리소스를 나열합니다. |
> |  | **구성스토어/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/공급자/마이크로소프트.인사이트/진단설정/읽기 | 구성 저장소에 대한 모든 진단 설정 값을 읽습니다. |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/공급자/마이크로소프트.인사이트/진단설정/쓰기 | Microsoft 앱 구성에 대한 진단 설정을 작성/덮어씁니다. |
> |  | **구성스토어/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.AppConfiguration/구성스토어/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | Microsoft 앱 구성에 대한 모든 메트릭 정의를 검색합니다. |
> |  | **구성스토어/동기화작업** |  |
> | 작업 | 마이크로소프트.App구성/구성스토어/동기화작업/읽기 | 지정된 구성 저장소 동기화 작업의 속성을 가져옵니다 또는 지정된 구성 저장소 에서 모든 구성 저장소 동기화 작업을 나열 합니다. |
> | 작업 | 마이크로소프트.App구성/구성스토어/동기화작업/쓰기 | 지정된 매개 변수를 사용하여 구성 저장소 동기화 작업을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.AppConfiguration/구성스토어/동기화 작업/삭제 | 구성 저장소 동기화 작업을 삭제합니다. |
> |  | **위치/작업상태** |  |
> | 작업 | 마이크로소프트.앱 구성/위치/작업상태/읽기 | 작업 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.앱 구성/작업/읽기 | Microsoft 앱 구성에서 지원하는 모든 작업을 나열합니다. |
> |  | **구성스토어/키값** |  |
> | DataAction | 마이크로소프트.App구성/구성스토어/키값/읽기 | 구성 저장소에서 키-값을 읽습니다. |
> | DataAction | 마이크로소프트.App구성/구성스토어/키값/쓰기 | 구성 저장소에서 키-값을 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.App구성/구성스토어/키값/삭제 | 구성 저장소에서 기존 키-값을 삭제합니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

Azure 서비스: [Azure 리소스 관리자](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Authorization/elevateAccess/Action | 테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한을 부여합니다. |
> |  | **classicAdministrators** |  |
> | 작업 | Microsoft.Authorization/classicAdministrators/read | 구독에 대한 관리자를 읽습니다. |
> | 작업 | Microsoft.Authorization/classicAdministrators/write | 구독에 대한 관리자를 추가 또는 수정합니다. |
> | 작업 | Microsoft.Authorization/classicAdministrators/delete | 구독에서 관리자를 제거합니다. |
> |  | **클래식관리자/작업 상태** |  |
> | 작업 | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 구독의 관리자 작업 상태를 가져옵니다. |
> |  | **denyAssignments** |  |
> | 작업 | Microsoft.Authorization/denyAssignments/read | 거부 할당에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Authorization/denyAssignments/write | 지정된 범위에서 거부 할당을 만듭니다. |
> | 작업 | Microsoft.Authorization/denyAssignments/delete | 지정된 범위에서 거부 할당을 삭제합니다. |
> |  | **잠금** |  |
> | 작업 | Microsoft.Authorization/locks/read | 지정된 범위에서 잠금을 가져옵니다. |
> | 작업 | Microsoft.Authorization/locks/write | 지정된 범위에서 잠금을 추가합니다. |
> | 작업 | Microsoft.Authorization/locks/delete | 지정된 범위에서 잠금을 삭제합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Authorization/operations/read | 작업 목록을 가져옵니다. |
> |  | **권한을** |  |
> | 작업 | Microsoft.Authorization/permissions/read | 호출자가 지정된 범위에서 갖는 모든 사용 권한을 나열합니다. |
> |  | **정책** |  |
> | 작업 | Microsoft.권한 부여/정책/감사/작업 | '감사' 효과가 있는 Azure 정책 평가 의 결과로 수행된 작업 |
> | 작업 | Microsoft.권한 부여/정책/감사IfNot존재/작업 | 'auditIfNotExists' 효과를 가진 Azure 정책 평가의 결과로 취해진 조치 |
> | 작업 | Microsoft.권한 부여/정책/거부/작업 | '거부' 효과가 있는 Azure 정책 평가 결과로 수행된 작업 |
> | 작업 | Microsoft.권한 부여/정책/배포IfNot존재/작업 | 'deployIfNotExists' 효과를 가진 Azure 정책 평가의 결과로 취해진 작업 |
> |  | **정책할당** |  |
> | 작업 | Microsoft.Authorization/policyAssignments/read | 정책 할당에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Authorization/policyAssignments/write | 지정된 범위에서 정책 할당을 만듭니다. |
> | 작업 | Microsoft.Authorization/policyAssignments/delete | 지정된 범위에서 정책 할당을 삭제합니다. |
> |  | **policyDefinitions** |  |
> | 작업 | Microsoft.Authorization/policyDefinitions/read | 정책 정의에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Authorization/policyDefinitions/write | 사용자 지정 정책 정의를 만듭니다. |
> | 작업 | Microsoft.Authorization/policyDefinitions/delete | 정책 정의를 삭제합니다. |
> |  | **policySetDefinitions** |  |
> | 작업 | Microsoft.Authorization/policySetDefinitions/read | 정책 집합 정의에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Authorization/policySetDefinitions/write | 사용자 지정 정책 집합 정의를 만듭니다. |
> | 작업 | Microsoft.Authorization/policySetDefinitions/delete | 정책 집합 정의를 삭제합니다. |
> |  | **providerOperations** |  |
> | 작업 | Microsoft.Authorization/providerOperations/read | 역할 정의에 사용할 수 있는 모든 리소스 공급자에 대한 작업을 가져옵니다. |
> |  | **역할할당** |  |
> | 작업 | Microsoft.Authorization/roleAssignments/read | 역할 할당에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Authorization/roleAssignments/write | 지정된 범위에서 역할 할당을 만듭니다. |
> | 작업 | Microsoft.Authorization/roleAssignments/delete | 지정된 범위에서 역할 할당을 삭제합니다. |
> |  | **roleDefinitions** |  |
> | 작업 | Microsoft.Authorization/roleDefinitions/read | 역할 정의에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Authorization/roleDefinitions/write | 지정된 사용 권한 및 할당 가능 범위를 사용하여 사용자 지정 역할 정의를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Authorization/roleDefinitions/delete | 지정된 사용자 지정 역할 정의를 삭제합니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

Azure 서비스: [자동화](../automation/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Automation/register/action | 구독을 Azure Automation에 등록합니다. |
> |  | **자동화계정** |  |
> | 작업 | Microsoft.Automation/automationAccounts/webhooks/action | Azure Automation 웹후크에 대한 URI를 생성합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/read | Azure Automation 계정을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/write | Azure Automation 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/listKeys/action | 자동화 계정에 대한 키를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/delete | Azure Automation 계정을 삭제합니다. |
> |  | **자동화계정/에이전트등록정보** |  |
> | 작업 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Azure Automation DSC의 등록 정보를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC 키를 다시 생성하는 요청을 작성합니다. |
> |  | **자동화계정/인증서** |  |
> | 작업 | Microsoft.Automation/automationAccounts/certificates/getCount/action | 인증서 수를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/certificates/read | Azure Automation 인증서 자산을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/certificates/write | Azure Automation 인증서 자산을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/certificates/delete | Azure Automation 인증서 자산을 삭제합니다. |
> |  | **자동화계정/컴파일작업** |  |
> | 작업 | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC의 컴파일을 작성합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC의 컴파일을 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC의 컴파일을 작성합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC의 컴파일을 읽습니다. |
> |  | **자동화계정/구성** |  |
> | 작업 | Microsoft.Automation/automationAccounts/configurations/read | Azure Automation DSC의 콘텐츠를 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/configurations/getCount/action | Azure Automation DSC의 콘텐츠 수를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/configurations/write | Azure Automation DSC의 콘텐츠를 작성합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/configurations/delete | Azure Automation DSC의 콘텐츠를 삭제합니다. |
> |  | **자동화계정/구성/콘텐츠** |  |
> | 작업 | Microsoft.Automation/automationAccounts/configurations/content/read | 구성 미디어 콘텐츠를 읽습니다. |
> |  | **자동화계정/연결** |  |
> | 작업 | Microsoft.Automation/automationAccounts/connections/read | Azure Automation 연결 자산을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/connections/getCount/action | 연결 수를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/connections/write | Azure Automation 연결 자산을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation 연결 자산을 삭제합니다. |
> |  | **자동화계정계정/연결유형** |  |
> | 작업 | Microsoft.Automation/automationAccounts/connectionTypes/read | Azure Automation 연결 형식 자산을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/connectionTypes/write | Azure Automation 연결 형식 자산을 만듭니다. |
> | 작업 | Microsoft.Automation/automationAccounts/connectionTypes/delete | Azure Automation 연결 형식 자산을 삭제합니다. |
> |  | **자동화계정/자격 증명** |  |
> | 작업 | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation 자격 증명 자산을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/credentials/getCount/action | 자격 증명 수를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/credentials/write | Azure Automation 자격 증명 자산을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation 자격 증명 자산을 삭제합니다. |
> |  | **자동화계정/하이브리드Run북워커그룹** |  |
> | 작업 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hybrid Runbook Worker 리소스를 삭제합니다. |
> |  | **automationAccounts/jobs** |  |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 작업 실행 시 Azure Automation runbook의 콘텐츠를 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
> |  | **자동화계정/작업/출력** |  |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/output/read | 작업의 출력을 가져옵니다. |
> |  | **자동화계정/작업/스트림** |  |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> |  | **자동화계정/작업일정** |  |
> | 작업 | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation 작업 일정을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation 작업 일정을 만듭니다. |
> | 작업 | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation 작업 일정을 삭제합니다. |
> |  | **자동화계정/링크된워크스페이스** |  |
> | 작업 | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 자동화 계정에 연결된 작업 영역 가져오기 |
> |  | **자동화계정/모듈** |  |
> | 작업 | Microsoft.Automation/automationAccounts/modules/read | Azure Automation Powershell 모듈을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/modules/getCount/action | Automation Account 내에서 Powershell 모듈 수를 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/modules/write | Azure Automation Powershell Powershell 모듈을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation Powershell 모듈을 삭제합니다. |
> |  | **자동화계정/모듈/활동** |  |
> | 작업 | Microsoft.Automation/automationAccounts/modules/activities/read | Azure Automation 작업을 가져옵니다. |
> |  | **자동화계정/노드구성** |  |
> | 작업 | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Azure Automation DSC의 노드 구성 콘텐츠를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Azure Automation DSC의 노드 구성을 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Azure Automation DSC의 노드 구성을 작성합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC의 노드 구성을 삭제합니다. |
> |  | **자동화계정/노드수** |  |
> | 작업 | Microsoft.Automation/automationAccounts/nodecounts/read | 지정된 유형에 대한 노드 수 요약을 읽습니다. |
> |  | **자동화계정/노드** |  |
> | 작업 | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC 노드를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/nodes/write | Azure Automation DSC 노드를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC 노드를 삭제합니다. |
> |  | **자동화계정/노드/보고서** |  |
> | 작업 | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC 보고서를 읽습니다. |
> |  | **자동화계정/노드/보고서/콘텐츠** |  |
> | 작업 | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC 보고서 콘텐츠를 읽습니다. |
> |  | **자동화계정계정/개체데이터유형/필드** |  |
> | 작업 | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation TypeFields를 가져옵니다. |
> |  | **자동화계정/사설엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.자동화 /자동화계정 / 개인엔드 포인트 연결 Proxies / 읽기 | Azure 자동화 개인 엔드포인트 연결 프록시 읽기 |
> | 작업 | 마이크로 소프트.자동화 /자동화계정 / 개인엔드 포인트 연결 Proxies / 쓰기 | Azure 자동화 개인 엔드포인트 연결 프록시 를 만듭니다. |
> | 작업 | 마이크로 소프트.자동화/자동화계정/사설엔드포인트연결프록시/유효성 검사/조치 | 개인 끝점 연결 요청(groupId 유효성 검사) 유효성 검사 |
> | 작업 | 마이크로 소프트.자동화 /자동화계정 / 개인엔드 포인트 연결 Proxies / 삭제 | Azure 자동화 개인 엔드포인트 연결 프록시 삭제 |
> |  | **자동화계정/사설엔드포인트커넥션프록시/운영결과** |  |
> | 작업 | 마이크로 소프트.자동화 /자동화계정 / 개인엔드 포인트 연결Proxies / 작업결과 / 읽기 | Azure 자동화 개인 엔드포인트 프록시 작업 결과를 가져옵니다. |
> |  | **자동화계정/프라이빗엔드포인트연결** |  |
> | 작업 | 마이크로소프트.자동화/자동화계정/프라이빗엔드포인트연결/읽기 | Azure 자동화 개인 엔드포인트 연결 상태 얻기 |
> | 작업 | 마이크로소프트.자동화/자동화계정/사설엔드포인트연결/쓰기 | Azure 자동화 개인 엔드포인트 연결 승인 또는 거부 |
> |  | **자동화계정/프라이빗링크자원** |  |
> | 작업 | 마이크로소프트.자동화/자동화계정/개인링크자원/읽기 | 개인 끝점에 대한 그룹 정보 읽기 |
> |  | **자동화계정/파이썬2패키지** |  |
> | 작업 | Microsoft.Automation/automationAccounts/python2Packages/read | Azure Automation Python 2 패키지를 가져오기 |
> | 작업 | Microsoft.Automation/automationAccounts/python2Packages/write | Azure Automation Python 2 패키지를 만들거나 업데이트 |
> | 작업 | Microsoft.Automation/automationAccounts/python2Packages/delete | Azure Automation Python 2 패키지를 삭제 |
> |  | **자동화계정/파이썬3패키지** |  |
> | 작업 | Microsoft.Automation/automationAccounts/python3Packages/read | Azure Automation Python 3 패키지를 가져오기 |
> | 작업 | Microsoft.Automation/automationAccounts/python3Packages/write | Azure Automation Python 3 패키지를 만들거나 업데이트 |
> | 작업 | Microsoft.Automation/automationAccounts/python3Packages/delete | Azure Automation Python 3 패키지를 삭제 |
> |  | **자동화계정/런북** |  |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Azure Automation Runbook 수를 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/write | Azure Automation Runbook을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation Runbook을 삭제합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation Runbook 초안을 게시합니다. |
> |  | **자동화계정/런북/콘텐츠** |  |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/content/read | Azure Automation runbook의 콘텐츠를 가져옵니다. |
> |  | **자동화계정/런북/초안** |  |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation Runbook 초안을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Azure Automation Runbook 초안에 대한 편집을 실행 취소합니다. |
> |  | **자동화계정/런북/초안/콘텐츠** |  |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Azure Automation Runbook 초안의 콘텐츠를 만듭니다. |
> |  | **자동화계정/런북/초안/작업결과** |  |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation Runbook 초안 작업 결과를 가져옵니다. |
> |  | **자동화계정/런북/초안/테스트작업** |  |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Azure Automation Runbook 초안 테스트 작업을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Azure Automation Runbook 초안 테스트 작업을 만듭니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Azure Automation Runbook 초안 테스트 작업을 중지합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Azure Automation Runbook 초안 테스트 작업을 일시 중단합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Azure Automation Runbook 초안 테스트 작업을 계속합니다. |
> |  | **자동화계정/일정** |  |
> | 작업 | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation 일정 자산을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/schedules/getCount/action | Azure Automation 일정 수를 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation 일정 자산을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/schedules/delete | Azure Automation 일정 자산을 삭제합니다. |
> |  | **자동화계정/소프트웨어UpdateConfigurationMachineRuns** |  |
> | 작업 | 마이크로소프트.자동화/자동화계정/소프트웨어UpdateConfigurationMachineRuns/읽기 | Azure 자동화 소프트웨어 업데이트 구성 컴퓨터 실행 |
> |  | **자동화계정/소프트웨어UpdateConfigurationRuns** |  |
> | 작업 | 마이크로소프트.자동화/자동화계정/소프트웨어UpdateConfigurationRuns/읽기 | Azure 자동화 소프트웨어 업데이트 구성 실행 |
> |  | **automationAccounts/softwareUpdateConfigurations** |  |
> | 작업 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation 소프트웨어 업데이트 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation 소프트웨어 업데이트 구성을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation 소프트웨어 업데이트 구성을 삭제합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation 소프트웨어 업데이트 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation 소프트웨어 업데이트 구성을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation 소프트웨어 업데이트 구성을 삭제합니다. |
> |  | **자동화계정/통계** |  |
> | 작업 | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation 통계를 가져옵니다. |
> |  | **자동화계정/업데이트배포컴퓨터실행** |  |
> | 작업 | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Azure Automation 업데이트 배포 머신을 가져옵니다. |
> |  | **자동화계정/업데이트관리패치작업** |  |
> | 작업 | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Azure Automation 업데이트 관리 패치 작업을 가져옵니다. |
> |  | **자동화계정/사용법** |  |
> | 작업 | Microsoft.Automation/automationAccounts/usages/read | Azure Automation 사용량을 가져옵니다. |
> |  | **자동화계정/변수** |  |
> | 작업 | Microsoft.Automation/automationAccounts/variables/read | Azure Automation 변수 자산을 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/variables/write | Azure Automation 변수 자산을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation 변수 자산을 삭제합니다. |
> |  | **자동화계정/감시자** |  |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/write | Azure Automation Watcher 작업을 만듭니다. |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/read | Azure Automation Watcher 작업을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation Watcher 작업을 삭제합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation Watcher 작업을 시작합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation Watcher 작업을 중지합니다. |
> |  | **자동화계정/감시자/스트림** |  |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/streams/read | Azure Automation Watcher 작업 스트림을 가져옵니다. |
> |  | **자동화계정/감시자/감시자행동** |  |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Azure Automation Watcher 작업 동작을 만듭니다. |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Azure Automation Watcher 작업 동작을 가져옵니다. |
> | 작업 | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Azure Automation Watcher 작업 동작을 삭제합니다. |
> |  | **automationAccounts/webhooks** |  |
> | 작업 | Microsoft.Automation/automationAccounts/webhooks/read | Azure Automation 웹후크를 읽습니다. |
> | 작업 | Microsoft.Automation/automationAccounts/webhooks/write | Azure Automation 웹후크를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation 웹후크를 삭제합니다.  |
> |  | **작업** |  |
> | 작업 | Microsoft.Automation/operations/read | Azure Automation 리소스에 사용 가능한 작업을 가져옵니다. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Azure 서비스: [Azure Active Directory B2C](../active-directory-b2c/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.AzureActiveDirectory/register/action | Microsoft.AzureActiveDirectory 리소스 공급자 구독을 등록합니다. |
> |  | **b2c디렉터** |  |
> | 작업 | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C 디렉터리 리소스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C 디렉터리 리소스를 봅니다. |
> | 작업 | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C 디렉터리 리소스를 삭제합니다. |
> |  | **b2ctenants** |  |
> | 작업 | 마이크로소프트.AzureActiveDirector/b2ctenants/읽기 | 사용자가 구성원인 모든 B2C 테넌을 나열합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.AzureActiveDirectory/operations/read | Microsoft.AzureActiveDirectory 리소스 공급자에 사용할 수 있는 모든 API 작업을 읽습니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.AzureStack/register/action | Microsoft.AzureStack 리소스 공급자에 구독을 등록합니다. |
> |  | **클라우드 매니페스트 파일** |  |
> | 작업 | 마이크로소프트.AzureStack/클라우드 매니페스트 파일/읽기 | 클라우드 매니페스트 파일 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.AzureStack/Operations/read | 리소스 공급자 작업의 속성을 가져옵니다. |
> |  | **등록** |  |
> | 작업 | Microsoft.AzureStack/registrations/read | Azure Stack 등록의 속성 가져오기 |
> | 작업 | Microsoft.AzureStack/registrations/write | Azure Stack 등록을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.AzureStack/registrations/delete | Azure Stack 등록을 삭제합니다. |
> | 작업 | Microsoft.AzureStack/registrations/getActivationKey/action | 최신 Azure Stack 활성화 키를 가져옵니다. |
> |  | **registrations/customerSubscriptions** |  |
> | 작업 | Microsoft.AzureStack/registrations/customerSubscriptions/read | Azure Stack 고객 구독의 속성을 가져옵니다. |
> | 작업 | Microsoft.AzureStack/registrations/customerSubscriptions/write | Azure Stack 고객 구독을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Azure Stack 고객 구독을 삭제합니다. |
> |  | **registrations/products** |  |
> | 작업 | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace 제품의 속성 가져오기 |
> | 작업 | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace 제품에 대한 세부 정보 검색 |
> | 작업 | 마이크로소프트.AzureStack/등록/제품/getProducts/작업 | Azure 스택 마켓플레이스 제품 목록을 검색합니다. |
> | 작업 | 마이크로소프트.AzureStack/등록/제품/getProduct/작업 | Azure 스택 마켓플레이스 제품 검색 |
> | 작업 | Microsoft.AzureStack/등록/제품/업로드제품 로그/작업 | 레코드 Azure 스택 마켓플레이스 제품 작업 상태 및 타임스탬프 |

## <a name="microsoftbatch"></a>Microsoft.Batch

Azure 서비스: [일괄 처리](../batch/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Batch/register/action | Batch 리소스 공급자에 대한 구독을 등록하고 Batch 계정을 만들도록 설정합니다. |
> | 작업 | Microsoft.Batch/unregister/action | Batch 리소스 공급자에 대한 구독을 등록 취소하여 Batch 계정 만들기를 차단합니다. |
> |  | **일괄 처리 계정** |  |
> | 작업 | Microsoft.Batch/batchAccounts/read | Batch 계정을 나열하거나 Batch 계정의 속성을 가져옵니다. |
> | 작업 | Microsoft.Batch/batchAccounts/write | 새 Batch 계정을 만들거나 기존 Batch 계정을 업데이트합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/delete | Batch 계정을 삭제합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/listkeys/action | Batch 계정에 대한 액세스 키를 나열합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/regeneratekeys/action | Batch 계정에 대한 액세스 키를 다시 생성합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Batch 계정에 대해 구성된 자동 스토리지 계정의 액세스 키를 동기화합니다. |
> |  | **일괄 계정/응용 프로그램** |  |
> | 작업 | Microsoft.Batch/batchAccounts/applications/read | 애플리케이션을 나열하거나 애플리케이션의 속성을 가져옵니다. |
> | 작업 | Microsoft.Batch/batchAccounts/applications/write | 새 애플리케이션을 만들거나 기존 애플리케이션을 업데이트합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/applications/delete | 애플리케이션을 삭제합니다. |
> |  | **일괄 계정/응용 프로그램/버전** |  |
> | 작업 | Microsoft.Batch/batchAccounts/applications/versions/read | 애플리케이션 패키지의 속성을 가져옵니다. |
> | 작업 | Microsoft.Batch/batchAccounts/applications/versions/write | 새 애플리케이션 패키지를 만들거나 기존 애플리케이션 패키지를 업데이트합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/applications/versions/delete | 애플리케이션 패키지를 삭제합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 애플리케이션 패키지를 활성화합니다. |
> |  | **일괄 계정/인증서작업 결과** |  |
> | 작업 | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 배치 계정에 대한 장기 실행 인증서 작업의 결과를 가져옵니다. |
> |  | **일괄 계정/인증서** |  |
> | 작업 | Microsoft.Batch/batchAccounts/certificates/read | 배치 계정의 인증서를 나열하거나 인증서 속성을 가져옵니다. |
> | 작업 | Microsoft.Batch/batchAccounts/certificates/write | 배치 계정에 새 인증서를 만들거나 기존 인증서를 업데이트합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/certificates/delete | 배치 계정에서 인증서를 삭제합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 배치 계정에서 실패한 인증서 삭제를 취소합니다. |
> |  | **일괄 계정/작업결과** |  |
> | 작업 | Microsoft.Batch/batchAccounts/operationResults/read | 장기 실행 배치 계정 작업의 결과를 가져옵니다. |
> |  | **일괄 처리계정/풀작업결과** |  |
> | 작업 | Microsoft.Batch/batchAccounts/poolOperationResults/read | 배치 계정에 대한 장기 실행 풀 작업의 결과를 가져옵니다. |
> |  | **일괄 계정/풀** |  |
> | 작업 | Microsoft.Batch/batchAccounts/pools/read | 배치 계정의 풀을 나열하거나 풀 속성을 가져옵니다. |
> | 작업 | Microsoft.Batch/batchAccounts/pools/write | 배치 계정에 새 풀을 만들거나 기존 풀을 업데이트합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/pools/delete | 배치 계정에서 풀을 삭제합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/pools/stopResize/action | 배치 계정 풀에서 진행 중인 크기 조정 작업을 중지합니다. |
> | 작업 | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 배치 계정 풀에 대한 자동 크기 조정을 해제합니다. |
> |  | **일괄 계정/개인엔드포인트연결결과** |  |
> | 작업 | 마이크로 소프트.배치 / 배치 계정 / 개인엔드 포인트 연결 결과 / 읽기 | 장기 실행 Batch 계정 개인 엔드포인트 연결 작업의 결과를 가져옵니다. |
> |  | **일괄 계정/개인엔드포인트연결** |  |
> | 작업 | 마이크로 소프트.배치 / 배치 계정 / 개인엔드 포인트 연결 / 쓰기 | Batch 계정에서 기존 비공개 끝점 연결 업데이트 |
> | 작업 | 마이크로 소프트.배치 / 배치 계정 / 개인엔드 포인트 연결 / 읽기 | 개인 끝점 연결 또는 일괄 처리 계정에 개인 끝점 연결을 나열 합니다. |
> |  | **일괄 계정/개인링크리소스** |  |
> | 작업 | 마이크로 소프트.배치 / 배치 계정 / 개인링크 자원 / 읽기 | 개인 링크 리소스의 속성을 가져옵니다 또는 일괄 처리 계정에 개인 링크 리소스 목록 |
> |  | **위치** |  |
> | 작업 | Microsoft.Batch/locations/checkNameAvailability/action | 계정 이름이 올바르고 사용되고 있지 않은지 확인합니다. |
> |  | **위치/계정작업결과** |  |
> | 작업 | Microsoft.Batch/locations/accountOperationResults/read | 장기 실행 배치 계정 작업의 결과를 가져옵니다. |
> |  | **위치/할당량** |  |
> | 작업 | Microsoft.Batch/locations/quotas/read | 지정된 Azure 지역에서 지정된 구독의 Batch 할당량을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Batch/operations/read | Microsoft.Batch 리소스 공급자에서 사용할 수 있는 작업을 나열합니다. |
> |  | **일괄 계정/작업** |  |
> | DataAction | 마이크로 소프트.Batch/배치 계정 / 작업 / 읽기 | Batch 계정의 작업을 나열하거나 작업의 속성을 가져옵니다. |
> | DataAction | 마이크로 소프트.Batch/배치 계정 / 작업 / 쓰기 | Batch 계정에 새 작업을 만들거나 기존 작업을 업데이트합니다. |
> | DataAction | Microsoft.Batch/일괄 처리계정/작업/삭제 | Batch 계정에서 작업 삭제 |
> |  | **일괄 계정/작업일정** |  |
> | DataAction | 마이크로 소프트.Batch/배치 계정 / 작업 일정 / 읽기 | Batch 계정에 작업 일정을 나열하거나 작업 일정의 속성을 가져옵니다. |
> | DataAction | Microsoft.Batch/일괄 처리 계정/작업일정/쓰기 | Batch 계정에 새 작업 일정을 만들거나 기존 작업 일정을 업데이트합니다. |
> | DataAction | Microsoft.Batch/일괄 처리 계정/작업일정/삭제 | Batch 계정에서 작업 일정 삭제 |

## <a name="microsoftbilling"></a>Microsoft.Billing

Azure 서비스: [비용 관리 + 청구](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.청구/유효성 검사주소/작업 |  |
> | 작업 | Microsoft.Billing/register/action |  |
> |  | **billingAccounts** |  |
> | 작업 | Microsoft.Billing/billingAccounts/read |  |
> | 작업 | 마이크로소프트.청구/청구계정/목록Invoice섹션Create구독권한/조치 |  |
> | 작업 | 마이크로소프트.청구/청구 계정/쓰기 |  |
> |  | **청구계정/계약** |  |
> | 작업 | Microsoft.청구/청구 계정/계약/읽기 |  |
> |  | **청구계정/청구권한** |  |
> | 작업 | Microsoft.Billing/청구계정/청구권한/읽기 |  |
> |  | **billingAccounts/billingProfiles** |  |
> | 작업 | 마이크로소프트.청구/청구계정/청구프로필/쓰기 |  |
> | 작업 | 마이크로소프트.청구/청구계정/청구프로필/읽기 |  |
> | 작업 | 마이크로소프트.청구/청구계정/청구프로필/쓰기 |  |
> | 작업 | 마이크로소프트.청구/청구계정/청구프로필/쓰기 |  |
> |  | **청구계정/청구계정/청구권한** |  |
> | 작업 | 마이크로소프트.청구/청구계정/청구프로필/청구권한/읽기 |  |
> |  | **청구계정/청구계정/고객** |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/고객/읽기 |  |
> |  | **billingAccounts/billingProfiles/invoices/pricesheet** |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/송장/가격표/다운로드/작업 |  |
> |  | **청구계정/청구계정/청구서섹션** |  |
> | 작업 | Microsoft.Billing/청구 계정/청구 프로필/송장 섹션/쓰기 |  |
> | 작업 | Microsoft.Billing/청구 계정/청구 프로필/송장 섹션/읽기 |  |
> | 작업 | Microsoft.Billing/청구 계정/청구 프로필/송장 섹션/쓰기 |  |
> |  | **청구계정/청구계정/청구프로필/인보이스섹션/청구권한** |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/인보이스섹션/청구권한/읽기 |  |
> |  | **청구계정/청구계정/청구프로필/인보이스섹션/청구구독서** |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/송장섹션/청구구독/이전/작업 |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/송장섹션/청구구독/이동/작업 |  |
> | 작업 | Microsoft.Billing/billing계정/청구프로필/송장 섹션/청구 구독/유효성 검사Move 자격/작업 |  |
> |  | **청구계정/청구계정/청구서섹션/제품** |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/송장섹션/제품/이전/작업 |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/송장섹션/제품/이동/작업 |  |
> | 작업 | Microsoft.Billing/billing계정/청구프로필/송장섹션/제품/유효성 검사Move 적격성/작업 |  |
> |  | **billingAccounts/billingProfiles/pricesheet** |  |
> | 작업 | Microsoft.Billing/청구계정/청구프로필/가격표/다운로드/작업 |  |
> |  | **billingAccounts/billingSubscriptions** |  |
> | 작업 | Microsoft.청구/청구계정/청구 구독/읽기 |  |
> |  | **청구계정/고객** |  |
> | 작업 | Microsoft.청구/청구 계정/고객/읽기 |  |
> |  | **청구계정/고객/청구권한** |  |
> | 작업 | Microsoft.Billing/청구계정/고객/청구권한/읽기 |  |
> |  | **billingAccounts/departments** |  |
> | 작업 | Microsoft.Billing/billingAccounts/departments/read |  |
> |  | **billingAccounts/enrollmentAccounts** |  |
> | 작업 | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> |  | **청구계정/등록계정/청구권한** |  |
> | 작업 | Microsoft.Billing/청구계정/등록계정/청구권한/읽기 |  |
> |  | **청구계정/등록부서/청구권한** |  |
> | 작업 | Microsoft.Billing/청구 계정/등록부서/청구권한/읽기 |  |
> |  | **billingAccounts/products** |  |
> | 작업 | Microsoft.청구/청구 계정/제품/읽기 |  |
> |  | **부서** |  |
> | 작업 | Microsoft.Billing/departments/read |  |
> |  | **송장** |  |
> | 작업 | Microsoft.청구/송장/다운로드/작업 | 목록에서 다운로드 링크를 사용하여 송장 다운로드 |
> | 작업 | Microsoft.청구/송장/다운로드/작업 | 목록에서 다운로드 링크를 사용하여 송장 다운로드 |
> | 작업 | Microsoft.Billing/invoices/read |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

Azure 서비스: [빙 맵](https://docs.microsoft.com/BingMaps/)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.빙지도/업데이트통신 환경 설정/작업 | Microsoft.BingMaps 소유자에 대 한 통신 환경 설정을 업데이트 합니다. |
> | 작업 | 마이크로소프트.빙지도/목록통신 환경 설정/작업 | Microsoft.BingMaps 소유자에 대 한 통신 환경 설정을 가져옵니다. |
> |  | **맵아피** |  |
> | 작업 | Microsoft.BingMaps/mapApis/Read | 마이크로소프트.빙지도/mapApis에 대 한 리소스를 가져옵니다. |
> | 작업 | Microsoft.BingMaps/mapApis/Write | Microsoft.BingMaps/mapApis의 리소스를 업데이트합니다. |
> | 작업 | Microsoft.BingMaps/mapApis/Delete | Microsoft.BingMaps/mapApis의 리소스를 삭제합니다. |
> | 작업 | Microsoft.BingMaps/mapApis/regenerateKey/action | Microsoft.BingMaps/mapApis에 대한 키 재생성 |
> | 작업 | Microsoft.BingMaps/mapApis/listSecrets/action | 마이크로소프트.빙지도/mapApis에 대 한 비밀을 나열 |
> | 작업 | 마이크로소프트.빙지도/mapApis/목록사용메트릭/액션 | Microsoft.BingMaps/mapApis에 대한 메트릭 을 나열합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.BingMaps/Operations/read | Microsoft.BingMaps에 대한 작업 나열 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

Azure 서비스: [Azure 블록 체인 서비스](../blockchain/workbench/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.블록체인/등록/액션 | 블록 체인 리소스 공급자에 대한 구독을 등록합니다. |
> |  | **블록체인 회원** |  |
> | 작업 | 마이크로소프트.블록체인/블록체인회원/읽기 | 기존 블록체인 회원을 얻거나 나열합니다. |
> | 작업 | 마이크로소프트.블록체인/블록체인 회원/쓰기 | 블록체인 회원을 생성하거나 업데이트합니다. |
> | 작업 | 마이크로소프트.블록체인/블록체인회원/삭제 | 기존 블록체인 멤버를 삭제합니다. |
> | 작업 | 마이크로소프트.블록체인/블록체인회원/리스트Api키/액션 | 기존 블록체인 회원 API 키를 얻거나 나열합니다. |
> |  | **블록체인 회원/트랜잭션노드** |  |
> | 작업 | 마이크로소프트.블록체인/블록체인 회원/트랜잭션노드/읽기 | 기존 블록 체인 회원 트랜잭션 노드를 얻거나 나열합니다. |
> | 작업 | 마이크로소프트.블록체인/블록체인 회원/트랜잭션노드/쓰기 | 블록체인 멤버 트랜잭션 노드를 생성하거나 업데이트합니다. |
> | 작업 | 마이크로소프트.블록체인/블록체인 회원/트랜잭션노드/삭제 | 기존 블록체인 회원 거래 노드를 삭제합니다. |
> | 작업 | 마이크로소프트.블록체인/블록체인 회원/트랜잭션노드/리스트Api키/액션 | 기존 블록체인 회원 거래 노드 API 키를 얻거나 나열합니다. |
> |  | **코다 회원** |  |
> | 작업 | 마이크로소프트.블록체인/코다회원/읽기 | 기존 블록체인 코르다 회원을 얻거나 나열합니다. |
> | 작업 | 마이크로소프트.블록체인/코다회원/글쓰기 | 블록체인 코르다 회원을 생성하거나 업데이트합니다. |
> | 작업 | 마이크로소프트.블록체인/코다회원/삭제 | 기존 블록체인 코르다 회원을 삭제합니다. |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.블록체인/위치/체크네임가용성/액션 | 리소스 이름이 유효하고 사용되지 않는지 확인합니다. |
> |  | **위치/블록체인멤버운영결과** |  |
> | 작업 | 마이크로소프트.블록체인/위치/블록체인멤버운영결과/읽기 | 블록 체인 회원의 운영 결과를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.블록체인/운영/읽기 | Microsoft 블록 체인 리소스 공급자의 모든 작업을 나열합니다. |
> |  | **블록체인 회원/트랜잭션노드** |  |
> | DataAction | 마이크로소프트.블록체인/블록체인 회원/트랜잭션노드/연결/액션 | 블록체인 회원 거래 노드에 연결합니다. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

Azure 서비스: [Azure 블루프린트](../governance/blueprints/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Blueprint/register/action | Azure 청사진 리소스 공급자 등록 |
> |  | **청사진할당** |  |
> | 작업 | Microsoft.Blueprint/blueprintAssignments/read | 모든 청사진 아티팩트 읽기 |
> | 작업 | Microsoft.Blueprint/blueprintAssignments/write | 청사진 아티팩트 만들기 또는 업데이트 |
> | 작업 | Microsoft.Blueprint/blueprintAssignments/delete | 청사진 아티팩트 삭제 |
> | 작업 | 마이크로소프트.블루프린트/블루프린트할당스/후이스블루프린트/액션 | Azure Blueprints 서비스 주체 개체 ID를 가져옵니다. |
> |  | **blueprintAssignments/assignmentOperations** |  |
> | 작업 | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 모든 청사진 아티팩트 읽기 |
> |  | **청사진** |  |
> | 작업 | Microsoft.Blueprint/blueprints/read | 청사진 읽기 |
> | 작업 | Microsoft.Blueprint/blueprints/write | 청사진 만들기 또는 업데이트 |
> | 작업 | Microsoft.Blueprint/blueprints/delete | 청사진 삭제 |
> |  | **blueprints/artifacts** |  |
> | 작업 | Microsoft.Blueprint/blueprints/artifacts/read | 모든 청사진 아티팩트 읽기 |
> | 작업 | Microsoft.Blueprint/blueprints/artifacts/write | 청사진 아티팩트 만들기 또는 업데이트 |
> | 작업 | Microsoft.Blueprint/blueprints/artifacts/delete | 청사진 아티팩트 삭제 |
> |  | **blueprints/versions** |  |
> | 작업 | Microsoft.Blueprint/blueprints/versions/read | 청사진 읽기 |
> | 작업 | Microsoft.Blueprint/blueprints/versions/write | 청사진 만들기 또는 업데이트 |
> | 작업 | Microsoft.Blueprint/blueprints/versions/delete | 청사진 삭제 |
> |  | **blueprints/versions/artifacts** |  |
> | 작업 | Microsoft.Blueprint/blueprints/versions/artifacts/read | 모든 청사진 아티팩트 읽기 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

Azure 서비스: [Azure 봇 서비스](https://docs.microsoft.com/azure/bot-service/)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.봇서비스/체크네임 가용성/작업 | 봇의 이름 가용성 확인 |
> | 작업 | 마이크로소프트.봇서비스/리스타우트서비스 제공자/조치 | 인증 서비스 제공업체 목록 |
> |  | **봇 서비스** |  |
> | 작업 | Microsoft.BotService/botServices/read | Bot Service를 읽습니다. |
> | 작업 | Microsoft.BotService/botServices/write | Bot Service를 작성합니다. |
> | 작업 | Microsoft.BotService/botServices/delete | Bot Service를 삭제합니다. |
> |  | **botServices/channels** |  |
> | 작업 | Microsoft.BotService/botServices/channels/read | 봇 서비스 채널 읽기 |
> | 작업 | Microsoft.BotService/botServices/channels/write | 봇 서비스 채널 작성 |
> | 작업 | Microsoft.BotService/botServices/channels/delete | 봇 서비스 채널 삭제 |
> | 작업 | 마이크로소프트.봇서비스/봇서비스/채널/리스트채널위드키/액션 | 비밀이 있는 봇 서비스 채널 목록 |
> |  | **botServices/connections** |  |
> | 작업 | Microsoft.BotService/botServices/connections/read | 봇 서비스 연결 읽기 |
> | 작업 | Microsoft.BotService/botServices/connections/write | 봇 서비스 연결 작성 |
> | 작업 | Microsoft.BotService/botServices/connections/delete | 봇 서비스 연결 삭제 |
> |  | **봇 서비스/연결/목록비밀** |  |
> | 작업 | 마이크로소프트.봇서비스/봇서비스/연결/목록비밀/쓰기 | 봇 서비스 연결 목록 작성  |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.BotService/locations/operationresults/read | 비동기 작업의 상태를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.BotService/Operations/read | 모든 리소스 종류에 대한 작업을 읽습니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

Azure 서비스: [Redis에 대 한 Azure 캐시](../azure-cache-for-redis/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Cache/checknameavailability/action | 이름을 새 Redis Cache에서 사용할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Cache/register/action | ‘Microsoft.Cache’ 리소스 공급자를 구독에 등록합니다. |
> | 작업 | Microsoft.Cache/unregister/action | ‘Microsoft.Cache’ 리소스 공급자를 구독에서 등록 취소합니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.Cache/locations/operationresults/read | 이전에 'Location' 헤더가 클라이언트에 반환된 장기 실행 작업의 결과를 가져옵니다 |
> |  | **작업** |  |
> | 작업 | Microsoft.Cache/operations/read | 'Microsoft.Cache' 공급자가 지원하는 작업을 나열합니다. |
> |  | **Redis** |  |
> | 작업 | Microsoft.Cache/redis/write | 관리 콘솔에서 Redis Cache의 설정 및 구성을 수정합니다. |
> | 작업 | Microsoft.Cache/redis/read | 관리 콘솔에서 Redis Cache의 설정 및 구성을 봅니다. |
> | 작업 | Microsoft.Cache/redis/delete | 전체 Redis Cache를 삭제합니다. |
> | 작업 | Microsoft.Cache/redis/listKeys/action | 관리 포털에서 Redis Cache 액세스 키의 값을 봅니다. |
> | 작업 | Microsoft.Cache/redis/regenerateKey/action | 관리 포털에서 Redis Cache 액세스 키의 값을 변경합니다. |
> | 작업 | Microsoft.Cache/redis/import/action | 여러 blob에서 지정된 형식의 데이터를 Redis로 가져옵니다. |
> | 작업 | Microsoft.Cache/redis/export/action | Redis 데이터를 지정된 형식의 접두사 지정 스토리지 blob으로 내보냅니다. |
> | 작업 | Microsoft.Cache/redis/forceReboot/action | 캐시 인스턴스를 강제로 다시 부팅합니다. 데이터가 손실될 수 있습니다. |
> | 작업 | Microsoft.Cache/redis/stop/action | 캐시 인스턴스를 중지합니다. |
> | 작업 | Microsoft.Cache/redis/start/action | 캐시 인스턴스를 시작합니다. |
> |  | **재배포/방화벽규칙** |  |
> | 작업 | Microsoft.Cache/redis/firewallRules/read | Redis Cache의 IP 방화벽 규칙을 가져옵니다. |
> | 작업 | Microsoft.Cache/redis/firewallRules/write | Redis Cache의 IP 방화벽 규칙을 편집합니다. |
> | 작업 | Microsoft.Cache/redis/firewallRules/delete | Redis Cache의 IP 방화벽 규칙을 삭제합니다. |
> |  | **재배포/링크된 서버** |  |
> | 작업 | Microsoft.Cache/redis/linkedservers/read | Redis Cache와 관련된 연결된 서버를 가져옵니다. |
> | 작업 | Microsoft.Cache/redis/linkedservers/write | Redis Cache에 연결된 서버를 추가합니다. |
> | 작업 | Microsoft.Cache/redis/linkedservers/delete | Redis Cache에서 연결된 서버를 삭제합니다. |
> |  | **재배포/메트릭정의** |  |
> | 작업 | Microsoft.Cache/redis/metricDefinitions/read | Redis Cache에 대해 사용 가능한 메트릭을 가져옵니다. |
> |  | **재배포/패치일정** |  |
> | 작업 | Microsoft.Cache/redis/patchSchedules/read | Redis Cache의 패치 일정을 가져옵니다. |
> | 작업 | Microsoft.Cache/redis/patchSchedules/write | Redis Cache의 패치 일정을 수정합니다. |
> | 작업 | Microsoft.Cache/redis/patchSchedules/delete | Redis Cache의 패치 일정을 삭제합니다. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Capacity/calculateprice/action | 예약 가격을 계산합니다. |
> | 작업 | Microsoft.Capacity/checkoffers/action | 구독 제안을 확인합니다. |
> | 작업 | Microsoft.Capacity/checkscopes/action | 구독을 확인합니다. |
> | 작업 | Microsoft.Capacity/validatereservationorder/action | 예약의 유효성을 검사합니다. |
> | 작업 | Microsoft.Capacity/reservationorders/action | 예약을 업데이트합니다. |
> | 작업 | Microsoft.Capacity/register/action | 용량 리소스 공급자를 등록하고 용량 리소스 만들기를 사용하도록 설정합니다. |
> | 작업 | Microsoft.Capacity/unregister/action | 테넌트를 등록 취소합니다. |
> | 작업 | 마이크로 소프트.용량 / 계산 교환 / 작업 | 새 구매의 교환 금액과 가격을 계산하고 정책 오류를 반환합니다. |
> | 작업 | 마이크로소프트.용량/교환/작업 | 모든 예약 교환 |
> |  | **응용 예약** |  |
> | 작업 | Microsoft.Capacity/appliedreservations/read | 모든 예약을 읽습니다. |
> |  | **catalogs** |  |
> | 작업 | Microsoft.Capacity/catalogs/read | 예약 카탈로그를 읽습니다. |
> |  | **상업 예약 주문** |  |
> | 작업 | Microsoft.Capacity/commercialreservationorders/read | 테넌트에서 작성된 예약 주문을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Capacity/operations/read | 작업을 읽습니다. |
> |  | **예약 주문** |  |
> | 작업 | Microsoft.Capacity/reservationorders/availablescopes/action | 사용 가능한 범위를 찾습니다. |
> | 작업 | Microsoft.Capacity/reservationorders/read | 모든 예약을 읽습니다. |
> | 작업 | Microsoft.Capacity/reservationorders/write | 예약을 만듭니다. |
> | 작업 | Microsoft.Capacity/reservationorders/delete | 예약을 삭제합니다. |
> | 작업 | Microsoft.Capacity/reservationorders/reservations/action | 예약을 업데이트합니다. |
> | 작업 | Microsoft.Capacity/reservationorders/return/action | 예약을 반환합니다. |
> | 작업 | Microsoft.Capacity/reservationorders/swap/action | 예약을 교환합니다. |
> | 작업 | Microsoft.Capacity/reservationorders/split/action | 예약을 분할합니다. |
> | 작업 | Microsoft.Capacity/reservationorders/merge/action | 예약을 병합합니다. |
> | 작업 | Microsoft.용량/예약 주문/계산 환불/조치 | 환불 금액 및 새 구매 의 가격을 계산 하 고 정책 오류를 반환 합니다. |
> |  | **예약 주문/병합 작업 결과** |  |
> | 작업 | Microsoft.용량/예약 주문/병합 작업 결과/읽기 | 병합 작업 폴링 |
> |  | **예약 주문/예약** |  |
> | 작업 | Microsoft.용량/예약 주문/예약/사용 가능한 스코프/작업 | 사용 가능한 범위를 찾습니다. |
> | 작업 | Microsoft.Capacity/reservationorders/reservations/read | 모든 예약을 읽습니다. |
> | 작업 | Microsoft.Capacity/reservationorders/reservations/write | 예약을 만듭니다. |
> | 작업 | Microsoft.Capacity/reservationorders/reservations/delete | 예약을 삭제합니다. |
> |  | **예약 주문/예약/수정** |  |
> | 작업 | Microsoft.Capacity/reservationorders/reservations/revisions/read | 모든 예약을 읽습니다. |
> |  | **예약 주문/분할 작업 결과** |  |
> | 작업 | Microsoft.용량/예약 주문/분할 작업 결과/읽기 | 분할 작업 폴링 |
> |  | **거주자** |  |
> | 작업 | Microsoft.Capacity/tenants/register/action | 테넌트를 등록합니다. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

Azure 서비스: [콘텐츠 전송 네트워크](../cdn/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Cdn/register/action | CDN 리소스 공급자에 대한 구독을 등록하고 CDN 프로필 생성을 활성화합니다. |
> | 작업 | Microsoft.Cdn/CheckNameAvailability/action |  |
> | 작업 | Microsoft.Cdn/ValidateProbe/action |  |
> | 작업 | Microsoft.Cdn/CheckResourceUsage/action |  |
> |  | **edgenodes** |  |
> | 작업 | Microsoft.Cdn/edgenodes/read |  |
> | 작업 | Microsoft.Cdn/edgenodes/write |  |
> | 작업 | Microsoft.Cdn/edgenodes/delete |  |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.Cdn/operationresults/read |  |
> | 작업 | Microsoft.Cdn/operationresults/write |  |
> | 작업 | Microsoft.Cdn/operationresults/delete |  |
> |  | **작업 결과/프로필 결과** |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/read |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/write |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> |  | **작업 결과/프로파일 결과/끝점 결과** |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> |  | **작업 결과/프로파일 결과/끝점 결과/사용자 지정 도메인 결과** |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> |  | **작업 결과/프로파일 결과/끝점 결과/원점 그룹 결과** |  |
> | 작업 | Microsoft.Cdn/작업 결과/프로필 결과/끝점 결과/원본 그룹 결과/읽기 |  |
> | 작업 | Microsoft.Cdn/작업 결과/프로필 결과/끝점 결과/원본 그룹 결과/쓰기 |  |
> | 작업 | Microsoft.Cdn/작업 결과/프로필 결과/끝점 결과/원본 그룹 결과/삭제 |  |
> |  | **작업 결과/프로파일 결과/끝점 결과/원본 결과** |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | 작업 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> |  | **작업** |  |
> | 작업 | Microsoft.Cdn/operations/read |  |
> |  | **프로필** |  |
> | 작업 | Microsoft.Cdn/profiles/read |  |
> | 작업 | Microsoft.Cdn/profiles/write |  |
> | 작업 | Microsoft.Cdn/profiles/delete |  |
> | 작업 | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | 작업 | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | 작업 | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> |  | **profiles/endpoints** |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/read |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/write |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/delete |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> |  | **profiles/endpoints/customdomains** |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> |  | **프로파일/끝점/원점 그룹** |  |
> | 작업 | Microsoft.Cdn/프로필/끝점/원본 그룹/읽기 |  |
> | 작업 | Microsoft.Cdn/프로필/끝점/원본 그룹/쓰기 |  |
> | 작업 | Microsoft.Cdn/프로필/끝점/원본 그룹/삭제 |  |
> |  | **profiles/endpoints/origins** |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | 작업 | Microsoft.Cdn/profiles/endpoints/origins/delete |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

Azure 서비스: [앱 서비스 인증서](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 서비스 앱 사용자에 대한 서비스 주체를 프로비전합니다. |
> | 작업 | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 인증서 구매 개체를 제출하지 않고 유효성을 검사합니다. |
> | 작업 | Microsoft.CertificateRegistration/register/action | 구독에 대한 Microsoft 인증서 리소스 공급자를 등록합니다. |
> |  | **인증서 주문** |  |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/Write | 새 certificateOrder를 추가하거나 기존 certificateOrder를 업데이트합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/Delete | 기존 AppServiceCertificate를 삭제합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/Read | CertificateOrders의 목록을 가져옵니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 기존 certificateorder를 재발급합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 기존 certificateorder를 갱신합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 인증서 작업의 목록을 검색합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 인증서 전자 메일 기록을 검색합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 인증서 전자 메일을 다시 보냅니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 도메인 소유권 확인 |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 요청 전자 메일을 다른 전자 메일 주소로 다시 보냅니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 발급된 App Service Certificate에 대한 사이트 봉인을 검색합니다. |
> |  | **certificateOrders/certificates** |  |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 새 인증서를 추가하거나 기존 인증서를 업데이트합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 기존 인증서를 삭제합니다. |
> | 작업 | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 인증서 목록을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.CertificateRegistration/operations/Read | 앱 서비스 인증서 등록의 모든 작업을 나열합니다. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

Azure 서비스: 클래식 배포 모델 가상 머신

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ClassicCompute/register/action | 클래식 Compute에 등록 |
> | 작업 | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 지정된 도메인 이름의 가용성을 확인합니다. |
> | 작업 | Microsoft.ClassicCompute/moveSubscriptionResources/action | 모든 클래식 리소스를 다른 구독으로 이동합니다. |
> | 작업 | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 클래식 이동 작업에 대한 구독의 가용성이 유효한지 확인합니다. |
> |  | **기능** |  |
> | 작업 | Microsoft.ClassicCompute/capabilities/read | 기능을 표시합니다. |
> |  | **체크도메인 이름 가용성** |  |
> | 작업 | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 지정된 도메인 이름의 가용성을 가져옵니다. |
> |  | **도메인 이름** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/read | 리소스에 대한 도메인 이름을 반환합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/write | 리소스에 대한 도메인 이름을 추가 또는 수정합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/delete | 리소스에 대한 도메인 이름을 제거합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/swap/action | 스테이징 슬롯을 프로덕션 슬롯으로 바꿉니다. |
> |  | **도메인 이름/활성** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/active/write | 활성 도메인 이름을 설정합니다. |
> |  | **도메인 이름/가용성세트** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 리소스에 대한 가용성 집합을 표시합니다. |
> |  | **domainNames/capabilities** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/capabilities/read | 도메인 이름 기능을 표시합니다. |
> |  | **도메인 이름/배포 슬롯** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 배포 슬롯을 표시합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 배포를 만들거나 업데이트합니다. |
> |  | **도메인 이름/배포 슬롯/역할** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 도메인 이름의 배포 슬롯의 역할 가져오기 |
> |  | **도메인 이름/배포 슬롯/역할/역할 인스턴스** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 도메인 이름의 배포 슬롯의 역할에 대한 역할 인스턴스 가져오기 |
> |  | **도메인 이름/배포 슬롯/상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 배포 슬롯 상태를 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 배포 슬롯 상태를 추가합니다. |
> |  | **도메인 이름/배포 슬롯/업그레이드도메인** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 도메인 이름의 배포 슬롯에 대한 업그레이드 도메인 가져오기 |
> | 작업 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 도메인 이름의 배포 슬롯에 대한 업그레이드 도메인 업데이트 |
> |  | **도메인 이름/확장** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/extensions/read | 도메인 이름 확장을 반환합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/extensions/write | 도메인 이름 확장을 추가합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/extensions/delete | 도메인 이름 확장을 제거합니다. |
> |  | **도메인 이름/확장/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 도메인 이름 확장에 대한 작업 상태를 읽습니다. |
> |  | **domainNames/internalLoadBalancers** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 내부 부하 분산 장치를 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 새 내부 부하 분산 장치를 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 새 내부 부하 분산 장치를 제거합니다. |
> |  | **도메인 이름/내부로드밸슬러/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 도메인 이름 내부 부하 분산 장치에 대한 작업 상태를 읽습니다. |
> |  | **도메인 이름/로드밸런스엔드포인트세트** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 부하가 분산된 엔드포인트 집합을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 부하가 분산된 엔드포인트 집합을 추가합니다. |
> |  | **도메인 이름/로드밸런스EndpointSet/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 도메인 이름 부하 분산 엔드포인트 집합에 대한 작업 상태를 읽습니다. |
> |  | **도메인 이름/작업 상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 도메인 이름의 작업 상태를 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 도메인 이름 확장에 대한 작업 상태를 읽습니다. |
> |  | **domainNames/serviceCertificates** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 사용된 서비스 인증서를 반환합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 사용된 서비스 인증서를 추가 또는 수정합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 사용된 서비스 인증서를 삭제합니다. |
> |  | **도메인 이름/서비스인증서/운영상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 도메인 이름 서비스 인증서에 대한 작업 상태를 읽습니다. |
> |  | **domainNames/slots** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/read | 배포 슬롯을 표시합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/write | 배포를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/delete | 지정된 배포 슬롯을 삭제합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/start/action | 배포 슬롯을 시작합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/stop/action | 배포 슬롯을 일시 중단합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 배포 슬롯의 마이그레이션 유효성을 검사합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 배포 슬롯의 마이그레이션을 준비합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 배포 슬롯의 마이그레이션을 커밋합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 배포 슬롯의 마이그레이션을 중단합니다. |
> |  | **도메인 이름/슬롯/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 도메인 이름 슬롯에 대한 작업 상태를 읽습니다. |
> |  | **domainNames/slots/roles** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/read | 배포 슬롯에 대한 역할을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/write | 배포 슬롯의 역할을 추가합니다. |
> |  | **도메인 이름/슬롯/역할/확장 참조** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 배포 슬롯 역할에 대한 확장 참조를 반환합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 배포 슬롯 역할에 대한 확장 참조를 추가하거나 수정합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 배포 슬롯 역할에 대한 확장 참조를 제거합니다. |
> |  | **도메인 이름/슬롯/역할/확장 참조/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 도메인 이름 슬롯 확장 참조에 대한 작업 상태를 읽습니다. |
> |  | **도메인 이름/슬롯/역할/메트릭 정의** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 도메인 이름의 역할 메트릭 정의를 가져옵니다. |
> |  | **도메인 이름/슬롯/역할/메트릭** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 도메인 이름의 역할 메트릭을 가져옵니다. |
> |  | **도메인 이름/슬롯/역할/작업 상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 도메인 이름 슬롯 역할의 작업 상태를 가져옵니다. |
> |  | **도메인 이름/슬롯/역할/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> |  | **도메인 이름/슬롯/역할/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> |  | **도메인 이름/슬롯/역할/역할인스턴스** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 도메인 이름 슬롯 역할의 역할 인스턴스에 대한 원격 데스크톱 연결 파일을 다운로드합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 역할 인스턴스를 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 역할 인스턴스를 다시 시작합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 역할 인스턴스를 이미지로 다시 설치합니다. |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 역할 인스턴스를 다시 빌드합니다. |
> |  | **도메인 이름/슬롯/역할/역할인스턴스/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 도메인 이름 슬롯 역할의 역할 인스턴스의 작업 상태를 가져옵니다. |
> |  | **도메인 이름/슬롯/역할/스쿠스** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 배포 슬롯의 역할 SKU를 가져옵니다. |
> |  | **도메인 이름/슬롯/상태/시작** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 배포 슬롯 상태를 중지됨으로 변경합니다. |
> |  | **도메인 이름/슬롯/상태/중지** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 배포 슬롯 상태를 시작됨으로 변경합니다. |
> |  | **도메인 이름/슬롯/업그레이드도메인** |  |
> | 작업 | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 도메인 업그레이드를 탐색합니다. |
> |  | **operatingSystemFamilies** |  |
> | 작업 | Microsoft.ClassicCompute/operatingSystemFamilies/read | Microsoft Azure에서 사용 가능한 게스트 운영 체제 제품군을 나열하고 각 제품군에서 사용 가능한 운영 체제 버전도 나열합니다. |
> |  | **operatingSystems** |  |
> | 작업 | Microsoft.ClassicCompute/operatingSystems/read | Microsoft Azure에서 현재 사용할 수 있는 게스트 운영 체제의 버전을 나열합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ClassicCompute/operations/read | 작업 목록을 가져옵니다. |
> |  | **작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> |  | **quotas** |  |
> | 작업 | Microsoft.ClassicCompute/quotas/read | 구독에 대한 할당량을 가져옵니다. |
> |  | **리소스유형/스쿠스** |  |
> | 작업 | Microsoft.ClassicCompute/resourceTypes/skus/read | 지원되는 리소스 형식에 대한 SKU 목록을 가져옵니다. |
> |  | **가상 머신** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/read | 가상 머신 목록을 검색합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/write | 가상 머신을 추가 또는 수정합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/delete | 가상 머신을 제거합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/capture/action | 가상 머신을 캡처합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/start/action | 가상 머신을 시작합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 가상 머신을 다시 배포합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 가상 머신에서 유지 관리를 수행합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/restart/action | 가상 머신을 다시 시작합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/stop/action | 가상 머신을 중지합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 가상 머신을 종료합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 데이터 디스크를 가상 머신에 연결합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 데이터 디스크를 가상 머신에서 분리합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 가상 머신에 대한 RDP 파일을 다운로드합니다. |
> |  | **가상 머신/관련 네트워크 보안 그룹** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 가상 컴퓨터와 연결된 네트워크 보안 그룹을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 가상 머신과 연결된 네트워크 보안 그룹을 추가합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 가상 머신과 연결된 네트워크 보안 그룹을 삭제합니다. |
> |  | **가상 머신/관련네트워크 보안 그룹/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹에 연결된 가상 머신에 대한 작업 상태를 읽습니다. |
> |  | **가상 머신/비동기운영** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 가능한 비동기 작업을 가져옵니다. |
> |  | **가상 시스템/진단 설정** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 가상 머신 진단 설정을 가져옵니다. |
> |  | **가상 머신/디스크** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/disks/read | 데이터 디스크의 목록을 검색합니다. |
> |  | **가상 머신/확장** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/extensions/read | 가상 머신 확장을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/extensions/write | 가상 머신 확장을 배치합니다. |
> |  | **가상 머신/확장/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 가상 머신 확장의 작업 상태를 읽습니다. |
> |  | **가상 시스템/메트릭 정의** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 가상 머신 메트릭 정의를 가져옵니다. |
> |  | **virtualMachines/metrics** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/metrics/read | 메트릭을 가져옵니다. |
> |  | **가상 머신/네트워크인터페이스/관련 네트워크 보안 그룹** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 추가합니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 삭제합니다. |
> |  | **가상 머신/네트워크인터페이스/관련네트워크보안그룹/운영상태** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹에 연결된 가상 머신에 대한 작업 상태를 읽습니다. |
> |  | **가상 머신/작업상태** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 가상 머신의 작업 상태를 읽습니다. |
> |  | **가상 머신 / 공급자 / 마이크로 소프트.인사이트 / 진단설정** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> |  | **가상 머신/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 메트릭 정의를 가져옵니다. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

Azure 서비스: 클래식 배포 모델 가상 네트워크

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ClassicNetwork/register/action | 클래식 네트워크에 등록합니다. |
> |  | **expressroutecrossconnections** |  |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Express 경로 교차 연결을 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Express 경로 교차 연결을 추가합니다. |
> |  | **익스프레스 경로 교차 연결/작동 상태** |  |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Express 경로 교차 연결 작업 상태를 가져옵니다. |
> |  | **익스프레스 경로 교차 연결/피어링** |  |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Express 경로 교차 연결 피어링을 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Express 경로 교차 연결 피어링을 추가합니다. |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Express 경로 교차 연결 피어링을 삭제합니다. |
> |  | **익스프레스 경로 교차 연결/피어링/운영 상태** |  |
> | 작업 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Express 경로 교차 연결 피어링 작업 상태를 가져옵니다. |
> |  | **gatewaySupportedDevices** |  |
> | 작업 | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 지원되는 디바이스 목록을 검색합니다. |
> |  | **networkSecurityGroups** |  |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/read | 네트워크 보안 그룹을 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/write | 새 네트워크 보안 그룹을 추가합니다. |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 네트워크 보안 그룹을 삭제합니다. |
> |  | **네트워크 보안그룹/운영상태** |  |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹의 작업 상태를 읽습니다. |
> |  | **네트워크 보안 그룹/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 네트워크 보안 그룹 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 네트워크 보안 그룹 진단 설정을 만들거나 업데이트합니다. 이 작업은 인사이트 리소스 공급자를 통해 보완됩니다. |
> |  | **네트워크 보안 그룹/공급자/Microsoft.Insights/logDEFINITIONs** |  |
> | 작업 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 네트워크 보안 그룹에 대한 이벤트를 가져옵니다. |
> |  | **네트워크 보안 그룹/보안규칙** |  |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 보안 규칙을 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 보안 규칙을 추가하거나 업데이트합니다. |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 보안 규칙을 삭제합니다. |
> |  | **네트워크 보안 그룹/보안규칙/운영상태** |  |
> | 작업 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 네트워크 보안 그룹 보안 규칙의 작업 상태를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ClassicNetwork/operations/read | 클래식 네트워크 작업을 가져옵니다. |
> |  | **quotas** |  |
> | 작업 | Microsoft.ClassicNetwork/quotas/read | 구독에 대한 할당량을 가져옵니다. |
> |  | **예약된 입면** |  |
> | 작업 | Microsoft.ClassicNetwork/reservedIps/read | 예약된 IP를 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/reservedIps/write | 예약된 IP를 새로 추가합니다. |
> | 작업 | Microsoft.ClassicNetwork/reservedIps/delete | 예약된 IP를 삭제합니다. |
> | 작업 | Microsoft.ClassicNetwork/reservedIps/link/action | 예약된 IP를 연결합니다. |
> | 작업 | Microsoft.ClassicNetwork/reservedIps/join/action | 예약된 IP를 조인합니다. |
> |  | **예약Ips/작업상태** |  |
> | 작업 | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 예약된 IP의 작업 상태를 읽습니다. |
> |  | **가상 네트워크** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/read | 가상 네트워크를 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/write | 새 가상 네트워크를 추가합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/delete | 가상 네트워크를 삭제합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 가상 네트워크를 다른 가상 네트워크와 피어링합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/join/action | 가상 네트워크를 조인합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 가상 네트워크에서 지정된 IP 주소의 가용성을 확인합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Virtual Network의 마이그레이션 유효성을 검사합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Virtual Network의 마이그레이션을 준비합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Virtual Network의 마이그레이션을 커밋합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Virtual Network의 마이그레이션을 중단합니다. |
> |  | **가상 네트워크/기능** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 기능을 표시합니다. |
> |  | **가상 네트워크/게이트웨이** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 가상 네트워크 게이트웨이를 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 가상 네트워크 게이트웨이를 추가합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 가상 네트워크 게이트웨이를 삭제합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 가상 네트워크 게이트웨이에 대한 진단을 시작합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 가상 네트워크 게이트웨이에 대한 진단을 중지합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 게이트웨이 진단을 다운로드합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 회로 서비스 키를 검색합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 디바이스 구성 스크립트를 다운로드합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 가상 네트워크 게이트웨이 패키지를 나열합니다. |
> |  | **가상 네트워크/게이트웨이/클라이언트Revoked인증서** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 해지된 클라이언트 인증서를 읽습니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 클라이언트 인증서를 해지합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 클라이언트 인증서의 해지를 취소합니다. |
> |  | **가상 네트워크/게이트웨이/클라이언트루트 인증서** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 클라이언트 루트 인증서를 찾습니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 새 클라이언트 루트 인증서를 업로드합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 가상 네트워크 게이트웨이 클라이언트 인증서를 삭제합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 지문으로 인증서를 다운로드합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 가상 네트워크 게이트웨이 인증서 패키지를 나열합니다. |
> |  | **가상 네트워크/게이트웨이/연결** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 연결 목록을 검색합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 사이트 간 게이트웨이 연결을 연결합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 사이트 간 게이트웨이 연결을 끊습니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 사이트 간 게이트웨이 연결을 테스트합니다. |
> |  | **가상 네트워크/게이트웨이/운영상태** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 가상 네트워크 게이트웨이의 작업 상태를 읽습니다. |
> |  | **가상 네트워크/게이트웨이/패키지** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 가상 네트워크 게이트웨이 패키지를 가져옵니다. |
> |  | **가상 네트워크/운영상태** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 가상 네트워크의 작업 상태를 읽습니다. |
> |  | **virtualNetworks/remoteVirtualNetworkPeeringProxies** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 원격 가상 네트워크 피어링 프록시를 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 원격 가상 네트워크 피어링 프록시를 추가하거나 업데이트합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 원격 가상 네트워크 피어링 프록시를 삭제합니다. |
> |  | **가상 네트워크/서브넷/관련 네트워크 보안 그룹** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 서브넷과 연결된 네트워크 보안 그룹을 가져옵니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 서브넷과 연결된 네트워크 보안 그룹을 추가합니다. |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 서브넷과 연결된 네트워크 보안 그룹을 삭제합니다. |
> |  | **가상 네트워크/서브넷/관련네트워크보안그룹/운영상태** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹과 연결된 가상 네트워크 서브넷에 대한 작업 상태를 읽습니다. |
> |  | **virtualNetworks/virtualNetworkPeerings** |  |
> | 작업 | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 가상 네트워크 피어링을 가져옵니다. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

Azure 서비스: 클래식 배포 모델 저장소

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ClassicStorage/register/action | 클래식 스토리지에 등록합니다. |
> | 작업 | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 스토리지 계정의 가용성을 확인합니다. |
> |  | **기능** |  |
> | 작업 | Microsoft.ClassicStorage/capabilities/read | 기능을 표시합니다. |
> |  | **체크스토리지계정가용성** |  |
> | 작업 | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 스토리지 계정의 가용성을 가져옵니다. |
> |  | **디스크** |  |
> | 작업 | Microsoft.ClassicStorage/disks/read | 스토리지 계정 디스크를 반환합니다. |
> |  | **이미지** |  |
> | 작업 | Microsoft.ClassicStorage/images/read | 이미지를 반환합니다. |
> |  | **이미지/작업 상태** |  |
> | 작업 | Microsoft.ClassicStorage/images/operationstatuses/read | 이미지 작업 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ClassicStorage/operations/read | 클래식 스토리지 작업을 가져옵니다. |
> |  | **osImages** |  |
> | 작업 | Microsoft.ClassicStorage/osImages/read | 운영 체제 이미지를 반환합니다. |
> |  | **osPlatformImages** |  |
> | 작업 | Microsoft.ClassicStorage/osPlatformImages/read | 운영 체제 플랫폼 이미지를 가져옵니다. |
> |  | **publicImages** |  |
> | 작업 | Microsoft.ClassicStorage/publicImages/read | 공용 가상 컴퓨터 이미지를 가져옵니다. |
> |  | **quotas** |  |
> | 작업 | Microsoft.ClassicStorage/quotas/read | 구독에 대한 할당량을 가져옵니다. |
> |  | **저장소 계정** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/read | 지정된 계정의 스토리지 계정을 반환합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/write | 새 스토리지 계정을 추가합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/delete | 스토리지 계정을 삭제합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 스토리지 계정에 대한 기존 액세스 키를 다시 생성합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 스토리지 계정의 마이그레이션 유효성을 검사합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 스토리지 계정의 마이그레이션을 준비합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 스토리지 계정의 마이그레이션을 커밋합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 스토리지 계정의 마이그레이션을 중단합니다. |
> |  | **저장소계정/blobServices/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/blobServices/공급자/마이크로소프트.인사이트/진단설정/읽기 | 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/blobServices/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 진단 설정을 추가하거나 수정합니다. |
> |  | **저장소계정/blobServices/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/blobServices/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | 메트릭 정의를 가져옵니다. |
> |  | **저장소계정/디스크** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/disks/read | 스토리지 계정 디스크를 반환합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/disks/write | 스토리지 계정 디스크를 추가합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/disks/delete | 지정된 스토리지 계정 디스크를 삭제합니다. |
> |  | **저장소계정/디스크/작업상태** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> |  | **저장소계정/파일서비스/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로소프트.클래식 스토리지/스토리지계정/파일서비스/공급자/마이크로소프트.인사이트/진단설정/읽기 | 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.클래식 스토리지/스토리지계정/파일서비스/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 진단 설정을 추가하거나 수정합니다. |
> |  | **저장소계정/파일서비스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.클래식 스토리지/스토리지계정/파일서비스/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | 메트릭 정의를 가져옵니다. |
> |  | **저장소계정/이미지** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/images/read | 스토리지 계정 이미지를 반환합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/images/delete | 지정된 장소 계정 이미지를 삭제합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> |  | **저장소계정/이미지/운영 상태** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 스토리지 계정 이미지 작업 상태를 반환합니다. |
> |  | **저장소계정/운영상태** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> |  | **스토리지계정/오스이미지** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/osImages/read | 스토리지 계정 운영 체제 이미지를 반환합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/osImages/write | 지정된 스토리지 계정 운영 체제 이미지를 추가합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 지정된 스토리지 계정 운영 체제 이미지를 삭제합니다. |
> |  | **스토리지계정/제공업체/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/제공업체/마이크로소프트.인사이트/진단설정/읽기 | 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/제공업체/마이크로소프트.인사이트/진단설정/쓰기 | 진단 설정을 추가하거나 수정합니다. |
> |  | **저장소계정/공급자/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/제공업체/마이크로소프트.인사이트/메트릭정의/읽기 | 메트릭 정의를 가져옵니다. |
> |  | **저장소계정/큐서비스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | 마이크로소프트.클래식 스토리지/스토리지계정/큐서비스/공급자/마이크로소프트.인사이트/진단설정/읽기 | 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.클래식 스토리지/스토리지계정/큐서비스/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 진단 설정을 추가하거나 수정합니다. |
> |  | **저장소계정/큐서비스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/큐서비스/제공업체/마이크로소프트.인사이트/메트릭정의/읽기 | 메트릭 정의를 가져옵니다. |
> |  | **storageAccounts/services** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/services/read | 사용 가능한 서비스를 가져옵니다. |
> |  | **storageAccounts/services/diagnosticSettings** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> |  | **storageAccounts/services/metricDefinitions** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> |  | **스토리지계정/서비스/메트릭** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 메트릭을 가져옵니다. |
> |  | **저장소계정/테이블서비스/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/테이블서비스/제공업체/마이크로소프트.인사이트/진단설정/읽기 | 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/테이블서비스/제공업체/마이크로소프트.인사이트/진단설정/쓰기 | 진단 설정을 추가하거나 수정합니다. |
> |  | **저장소계정/테이블서비스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.클래식스토리지/스토리지계정/테이블서비스/제공업체/마이크로소프트.인사이트/메트릭정의/읽기 | 메트릭 정의를 가져옵니다. |
> |  | **storageAccounts/vmImages** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 가상 머신 이미지를 반환합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 지정된 가상 머신 이미지를 추가합니다. |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 지정된 가상 머신 이미지를 삭제합니다. |
> |  | **스토리지계정/vmImages/운영 현황** |  |
> | 작업 | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 지정된 가상 머신 이미지 작업 상태를 가져옵니다. |
> |  | **vmImages** |  |
> | 작업 | Microsoft.ClassicStorage/vmImages/read | 가상 머신 이미지를 나열합니다. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

Azure 서비스: [코그너티브 서비스](../cognitive-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.CognitiveServices/register/action | 구독 등록 작업 |
> | 작업 | Microsoft.CognitiveServices/register/action | Cognitive Services에 대한 구독을 등록합니다. |
> | 작업 | 마이크로소프트.코그너티브 서비스/체크도메인가용성/액션 | 구독에 사용할 수 있는 SCO를 읽습니다. |
> | 작업 | Microsoft.CognitiveServices/register/action | Cognitive Services에 대한 구독을 등록합니다. |
> |  | **계정** |  |
> | 작업 | Microsoft.CognitiveServices/accounts/read | API 계정을 읽습니다. |
> | 작업 | Microsoft.CognitiveServices/accounts/write | API 계정을 씁니다. |
> | 작업 | Microsoft.CognitiveServices/accounts/delete | API 계정을 삭제합니다. |
> | 작업 | Microsoft.CognitiveServices/accounts/listKeys/action | 키를 나열합니다. |
> | 작업 | Microsoft.CognitiveServices/accounts/regenerateKey/action | 키를 다시 생성합니다. |
> |  | **계정/스쿠스** |  |
> | 작업 | Microsoft.CognitiveServices/accounts/skus/read | 기존 리소스에 대한 사용 가능한 SKU를 읽습니다. |
> |  | **계정/사용** |  |
> | 작업 | Microsoft.CognitiveServices/accounts/usages/read | 기존 리소스에 대한 할당량 사용량을 가져옵니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 구독에 사용할 수 있는 SCO를 읽습니다. |
> | 작업 | 마이크로 소프트.코그너티브 서비스 / 위치 / 삭제가상 네트워크 또는 서브 넷 / 작업 | Microsoft.Network에서 가상 네트워크 또는 서브넷 삭제 알림. |
> | 작업 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 구독에 사용할 수 있는 SCO를 읽습니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | 마이크로소프트.코그너티브 서비스/위치/작업 결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.CognitiveServices/Operations/read | 사용 가능한 모든 작업을 나열합니다. |
> |  | **계정/변칙 검출기/타임시리즈/전체** |  |
> | DataAction | Microsoft.CognitiveServices/계정/변칙 탐지기/타임시리즈/전체/감지/작업 | 이 작업은 전체 계열을 사용하여 모델을 생성하며 각 점은 동일한 모델로 검색됩니다.<br>이 방법을 사용하면 특정 지점 전후의 점을 사용하여 이상인지 여부를 확인합니다.<br>전체 감지는 사용자에게 시간열의 전반적인 상태를 제공할 수 있습니다. |
> |  | **계정/변칙 검출기/타임시리즈/마지막** |  |
> | DataAction | Microsoft.CognitiveServices/계정/변칙 탐지기/타임시리즈/마지막/감지/작업 | 이 작업은 최신 점 보다 앞서 점을 사용하여 모델을 생성합니다. 이 방법을 사용하면 기록 점만 사용하여 대상 점이 변칙인지 여부를 확인합니다. 최신 지점 감지는 비즈니스 메트릭의 실시간 모니터링 시나리오와 일치합니다. |
> |  | **계정/자동 제안** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/자동 제안/검색/작업 | 이 작업은 지정된 쿼리 또는 부분 쿼리에 대한 제안을 제공합니다. |
> |  | **계정/컴퓨터 비전** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/분석/작업 | 이 작업은 이미지 콘텐츠를 기반으로 다양한 시각적 기능을 추출합니다.  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/설명/작업 | 이 작업은 완전한 문장으로 사람이 읽을 수있는 언어로 이미지에 대한 설명을 생성합니다.<br> 설명은 작업에서 반환되는 콘텐츠 태그 컬렉션을 기반으로 합니다.<br>각 이미지에 대해 두 개 이상의 설명을 생성할 수 있습니다.<br> 설명은 신뢰 점수에 따라 정렬됩니다.<br>모든 설명은 영어로 되어 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/생성 섬모네일/액션 | 이 작업은 사용자가 지정한 너비와 높이가 있는 썸네일 이미지를 생성합니다.<br> 기본적으로 서비스는 이미지를 분석하고 ROI(관심 영역)를 식별하며 ROI를 기반으로 스마트 자르기 좌표를 생성합니다.<br> 스마트 자르기는 입력 이미지와 다른 가로 세로 비율을 지정할 때 도움이 됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/ocr/작업 | 광학 문자 인식(OCR)은 이미지의 텍스트를 감지하고 인식된 문자를 기계에서 사용할 수 있는 문자 스트림으로 추출합니다.    |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/인식 텍스트/작업 | 이 인터페이스를 사용하여 텍스트 인식 작업의 결과를 가져옵니다. 텍스트 인식 인터페이스를 사용하는 경우 응답에 "작업 위치"라는 필드가 포함됩니다. "작업 위치" 필드에는 텍스트 인식 작업 결과 작업에 사용해야 하는 URL이 포함되어 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/태그/작업 | 이 작업은 제공된 이미지의 내용과 관련된 단어 또는 태그 목록을 생성합니다.<br>컴퓨터 비전 API는 이미지에서 발견되는 개체, 살아있는 존재, 풍경 또는 동작을 기반으로 태그를 반환할 수 있습니다.<br>범주와 달리 태그는 계층적 분류 시스템에 따라 구성되지 않고 이미지 콘텐츠에 해당합니다.<br>태그는 모호성을 피하거나 컨텍스트를 제공하기 위한 힌트를 포함할 수 있으며, 예를 들어 태그 "첼로"는 힌트 "악기"를 수반할 수 있다.<br>모든 태그는 영어로 되어 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/관심 영역/작업 | 이 작업은 이미지의 가장 중요한 영역 주위에 경계 상자를 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/감지/작업 | 이 작업은 지정된 이미지에서 개체 검색을 수행합니다.  |
> |  | **계정/컴퓨터 비전/모델** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/모델/읽기 | 이 작업은 컴퓨터 비전 API에서 지원하는 도메인별 모델 목록을 반환합니다.  현재 API는 유명 인사 인식기, 랜드마크 인식기 와 같은 도메인 별 모델을 지원합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/모델/분석/작업 | 이 작업은 도메인 별 모델을 적용하여 이미지 내의 콘텐츠를 인식합니다.<br> 컴퓨터 비전 API에서 지원하는 도메인별 모델 목록은 /models GET 요청을 사용하여 검색할 수 있습니다.<br> 현재 API는 유명 인사, 랜드마크 와 같은 도메인별 모델을 제공합니다. |
> |  | **계정/컴퓨터 비전/읽기** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/읽기/분석/작업 | 이 인터페이스를 사용하여 텍스트가 많은 문서에 최적화된 OCR(최첨단 광학 문자 인식) 알고리즘을 사용하여 읽기 작업을 수행합니다.<br>그것은 손으로 작성, 인쇄 또는 혼합 문서를 처리 할 수 있습니다.<br>읽기 인터페이스를 사용하는 경우 응답에는 '작업 위치'라는 헤더가 포함됩니다.<br>'작업 위치' 헤더에는 OCR 결과에 액세스하기 위해 결과 읽기 작업에 사용해야 하는 URL이 포함되어 있습니다. |
> |  | **계정/컴퓨터 비전/읽기/분석 결과** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/읽기/분석 결과/읽기 | 이 인터페이스를 사용하여 Read 작업의 상태 및 OCR 결과를 검색합니다.  'operationId'를 포함하는 URL은 읽기 작업 '작업-위치' 응답 헤더에 반환됩니다. |
> |  | **계정/컴퓨터 비전/읽기/코어** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/읽기/코어/비동기 분석/작업 | 이 인터페이스를 사용하여 배치 읽기 파일 작업의 결과를 얻을 수 있으며 최첨단 광학 문자를 사용합니다. |
> |  | **계정/컴퓨터 비전/읽기/작업** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/읽기/작업/읽기 | 이 인터페이스는 읽기 작업의 OCR 결과를 가져오는 데 사용됩니다. 이 인터페이스에 대한 URL은 일괄 처리 읽기 파일 인터페이스에서 반환된 <b>"작업 위치"</b> 필드에서 검색해야 합니다. |
> |  | **계정/컴퓨터 비전/텍스트 작업** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컴퓨터 비전/텍스트 작업/읽기 | 이 인터페이스는 텍스트 작업 결과를 인식하는 데 사용됩니다. 이 인터페이스에 대한 URL은 텍스트 인식 인터페이스에서 반환된 <b>"작업 위치"</b> 필드에서 검색해야 합니다. |
> |  | **계정/콘텐츠 중재자** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지리스트/작업 | 이미지 목록을 만듭니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/작업 | 용어 목록을 만듭니다. |
> |  | **계정/콘텐츠 중재자/이미지 리스트** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지리스트/읽기 | 이미지 목록 - 세부 정보 가져오기 - 이미지 목록 - 모두 가져오기 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지리스트/삭제 | 이미지 목록 - 삭제 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지리스트/새로 고침 인덱스/작업 | 이미지 목록 - 검색 인덱스 새로 고침 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지리스트/쓰기 | 이미지 목록 - 업데이트 세부 정보 |
> |  | **계정/콘텐츠 중재자/이미지 리스트/이미지** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지 리스트/이미지/쓰기 | 이미지 목록에 이미지를 추가합니다. 이미지/일치 API를 사용할 때 이미지 목록을 사용하여 다른 이미지와 퍼지 일치를 수행할 수 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지 리스트/이미지/삭제 | 이미지 목록에서 이미지를 삭제합니다. 이미지/일치 API를 사용할 때 이미지 목록을 사용하여 다른 이미지와 퍼지 일치를 수행할 수 있습니다. 목록에서 모든 이미지를 삭제합니다. 이미지/일치 API를 사용할 때 이미지 목록을 사용하여 다른 이미지와 퍼지 일치를 수행할 수 있습니다.* |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/이미지 리스트/이미지/읽기 | 이미지 - 모든 이미지 ID 받기 |
> |  | **계정/콘텐츠 중재자/프로세스 이미지** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/프로세스 이미지/평가/작업 | 외설또는 성인용 콘텐츠가 포함된 이미지의 확률을 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/프로세스 이미지/찾기/작업 | 이미지에서 얼굴을 찾습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/프로세스 이미지/일치/작업 | 사용자 지정 이미지 목록 중 하나에 대해 이미지를 일치시면 됩니다. 이 API를 사용하여 사용자 지정 이미지 목록을 만들고 관리할 수 있습니다.  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/프로세스 이미지/ocr/작업 | 지정된 언어에 대해 이미지에 있는 텍스트를 반환합니다. 입력에 언어가 지정되지 않은 경우 검색이 영어로 기본설정됩니다. |
> |  | **계정/콘텐츠 중재자/프로세스 텍스트** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/컨텐더/프로세스 텍스트/검색언어/작업 | 이 작업은 지정된 입력 콘텐츠의 언어를 검색합니다. 제출된 텍스트를 포함하는 주요 언어에 대한 ISO 639-3 코드를 반환합니다. 110개 이상의 언어가 지원되었습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/프로세스 텍스트/화면/작업 | 이 작업은 100개 이상의 언어로 욕설을 감지하고 사용자 지정 및 공유 블랙리스트와 일치합니다. |
> |  | **계정/콘텐츠 중재자/팀** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/팀/작업/작업 | 이 끝점에 게시된 이미지 콘텐츠에 대한 작업 ID가 반환됩니다.  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/팀/검토/작업 | 생성된 리뷰는 팀의 검토자에게 표시됩니다. 검토자가 검토를 완료하면 검토 결과가 지정된 CallBackEndpoint에 게시됩니다(예: HTTP POST). |
> |  | **계정/콘텐츠 중재자/팀/작업** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/팀/작업/읽기 | 작업 ID에 대한 작업 세부 정보를 가져옵니다. |
> |  | **계정/콘텐츠 중재자/팀/리뷰** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/팀/리뷰/읽기 | 통과된 검토 ID에 대한 검토 세부 정보를 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/팀/리뷰/게시/작업 | 동영상 리뷰는 처음에 게시되지 않은 상태로 만들어지므로 팀의 검토자가 아직 검토할 수 없습니다. |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/팀/리뷰/성적 증명서/작업 | 이 API는 비디오 검토에 성적 증명서 파일(비디오에서 말한 모든 단어의 텍스트 버전)을 추가합니다. 파일은 유효한 WebVTT 형식이어야 합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/팀/리뷰/성적 평가 결과/작업 | 이 API는 비디오 검토를 위해 성적 증명서 화면 텍스트 결과 파일을 추가합니다. 성적 증명서 화면 텍스트 결과 파일은 화면 텍스트 API의 결과입니다. 성적 증명서 화면 텍스트 결과 파일을 생성하기 위해, 성적 증명서 파일은 화면 텍스트 API를 사용하여 욕설에 대한 스크리프를해야합니다. |
> |  | **계정/콘텐츠 중재자/팀/리뷰/액세스 키** |  |
> | DataAction | 마이크로 소프트.코그너티브 서비스 / 계정 / 콘텐츠 중재자 / 팀 / 리뷰 / 액세스 키 / 읽기 | 팀의 검토 콘텐츠 액세스 키를 가져옵니다. |
> |  | **계정/콘텐츠 중재자/팀/리뷰/프레임** |  |
> | DataAction | 마이크로 소프트.코그너티브 서비스 / 계정 / 콘텐츠 중재자 / 팀 / 리뷰 / 프레임 / 쓰기 | 이 방법을 사용하여 비디오 검토를 위한 프레임을 추가합니다. |
> | DataAction | 마이크로 소프트.코그너티브 서비스 / 계정 / 콘텐츠 중재자 / 팀 / 리뷰 / 프레임 / 읽기 | *정의되지 않음* |
> |  | **계정/콘텐츠 중재자/팀/설정/템플릿** |  |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/팀/설정/템플릿/쓰기 | 지정된 템플릿을 만들거나 업데이트합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/팀/설정/템플릿/삭제 | 팀에서 템플릿 삭제 |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/팀/설정/템플릿/읽기 | 이 팀에서 프로비전된 검토 템플릿 배열을 반환합니다. |
> |  | **계정/콘텐츠 중재자/팀/워크플로** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/팀/워크플로/쓰기 | 새 워크플로를 만들거나 기존 워크플로를 업데이트합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/팀/워크플로/읽기 | 팀에서 특정 워크플로에 대한 세부 정보 가져오기 팀에서 사용할 수 있는 모든 워크플로를 가져옵니다* |
> |  | **계정/콘텐츠 중재자/용어 목록** |  |
> | DataAction | Microsoft.CognitiveServices/계정/콘텐츠 중재자/용어 목록/대량 업데이트/작업 | 용어 목록 - 대량 업데이트 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/삭제 | 용어 목록 - 삭제 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/읽기 | 용어 목록 - 모두 가져오기 - 용어 목록 - 세부 정보 가져오기 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/새로 고침 인덱스/작업 | 용어 목록 - 검색 인덱스 새로 고침 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/쓰기 | 용어 목록 - 업데이트 세부 정보 |
> |  | **계정/콘텐츠 중재자/용어 목록/용어** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/용어/쓰기 | 용어 - 용어 추가 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/용어 삭제 | 기간 - 삭제 - 기간 - 모든 약관 삭제 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/콘텐츠 중재자/용어 목록/읽기 | 기간 - 모든 약관 받기 |
> |  | **계정/사용자 정의 비전.예측** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/작업 | 프로젝트를 만듭니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의.예측/사용자/작업 | *정의되지 않음* |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/할당량/작업 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/분류/반복** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/분류/반복/이미지/작업 | 이미지를 분류하고 결과를 저장합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/분류/반복/URL/작업 | 이미지 URL을 분류하고 결과를 저장합니다. |
> |  | **계정/사용자 정의 비전.예측/분류/반복/이미지** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/분류/반복/이미지/노스토어/작업 | 결과를 저장하지 않고 이미지를 분류합니다. |
> |  | **계정/사용자 정의 비전.예측/분류/반복/URL** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/분류/반복/url/노스토어/작업 | 결과를 저장하지 않고 이미지 URL을 분류합니다. |
> |  | **계정/사용자 정의 비전.예측/감지/반복** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/감지/반복/이미지/작업 | 이미지에서 개체를 감지하고 결과를 저장합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/검색/반복/URL/작업 | 이미지 URL에서 개체를 감지하고 결과를 저장합니다. |
> |  | **계정/사용자 정의 비전.예측/감지/반복/이미지** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/감지/반복/이미지/노스토어/작업 | 결과를 저장하지 않고 이미지의 개체를 감지합니다. |
> |  | **계정/사용자 정의 비전.예측/감지/반복/URL** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/검색/반복/url/노스토어/작업 | 결과를 저장하지 않고 이미지 URL에서 개체를 감지합니다. |
> |  | **계정/사용자 정의 비전.예측/도메인** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/도메인/읽기 | 특정 도메인에 대한 정보를 가져옵니다. 사용 가능한 도메인 목록을 가져옵니다.* |
> |  | **계정/사용자 정의 비전.예측/라벨 제안** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/라벨 제안/설정/작업 | 레이블 제안의 풀 크기를 설정합니다. |
> |  | **계정/사용자 정의 비전.예측/라벨 제안/설정** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/라벨 제안/설정/읽기 | 이 프로젝트에 대한 레이블 제안의 풀 크기를 가져옵니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/마이그레이션/작업 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/프로젝트** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/작업 | 이 API는 바디 콘텐츠를 다중 파트/양식 데이터 및 응용 프로그램/옥텟 스트림으로 허용합니다. 다중 파트를 사용하는 경우 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/태그/작업 | 프로젝트에 대한 태그를 만듭니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/삭제 | 특정 프로젝트를 삭제합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/읽기 | 특정 프로젝트를 가져옵니다. 프로젝트를 받으세요.* |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/기차/작업 | 교육을 위한 큐 프로젝트입니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/쓰기 | 특정 프로젝트를 업데이트합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/가져오기/작업 | 프로젝트를 가져오습니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/내보내기** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/내보내기/읽기 | 프로젝트를 내보전합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/지역/작업 | 이 API는 이미지 영역의 일괄 처리를 허용하고 선택적으로 태그를 사용하여 기존 이미지를 지역 정보로 업데이트합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/파일/작업 | 이 API는 파일 일괄 처리와 선택적으로 태그를 사용하여 이미지를 만듭니다. 최대 64개의 이미지와 20개의 태그를 만들 수 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/예측/작업 | 이 API는 지정된 예측 된 이미지에서 이미지의 일괄 처리를 만듭니다. 최대 64개의 이미지와 20개의 태그를 만들 수 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/URL/작업 | 이 API는 이미지를 만들기 위해 URL 일괄 처리및 선택적으로 태그를 허용합니다. 최대 64개의 이미지와 20개의 태그를 만들 수 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/태그/작업 | 이미지 집합을 태그 집합과 연결합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/삭제 | 교육 이미지 집합에서 이미지를 삭제합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/지역 제안/작업 | 이 API는 해당 지역에 대한 신뢰와 함께 이미지에 대한 지역 제안을 가져옵니다. 제안서가 없는 경우 빈 배열을 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/제안/작업 | 이 API는 제안된 태그 ID로 필터링된 태그가 지정되지 않은 이미지를 가져옵니다. 이미지가 없는 경우 빈 배열을 반환합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/ID** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/ID/읽기 | 이 API는 지정된 태그 및 선택적 반복에 대한 이미지 집합을 반환합니다. 이터레이션이 지정되지 않은 경우 |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/지역** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/지역/삭제 | 이미지 영역 세트를 삭제합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/제안** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의.예측/프로젝트/이미지/제안/개수/작업 | 이 API는 tagId를 사용하여 지정된 임계값에 대해 제안된 태그당 태그가 지정되지 않은 이미지수를 가져옵니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/태그가 지정** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/태그/읽기 | 이 API는 일괄 처리 및 범위 선택을 지원합니다. 기본적으로 이미지와 일치하는 처음 50개의 이미지만 반환됩니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/태그/개수** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/태그/개량/읽기 | 필터링은 및/또는 관계에 있습니다. 예를 들어 제공된 태그 ID가 "Dog"와 |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/태그** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/이미지/태그/삭제 | 이미지 집합에서 태그 집합을 제거합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/태그 지정되지 않은** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/이미지/태그 지정 되지 않은/읽기 | 이 API는 일괄 처리 및 범위 선택을 지원합니다. 기본적으로 이미지와 일치하는 처음 50개의 이미지만 반환됩니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/이미지/태그가 지정되지 않은/개수** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/이미지/태그되지 않은/개구/읽기 | 이 API는 지정된 프로젝트및 선택적으로 반복에 대한 태그가 없는 이미지를 반환합니다. 이터레이션이 지정되지 않은 경우 |
> |  | **계정/사용자 정의 비전.예측/프로젝트/반복** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의.예측/프로젝트/반복/삭제 | 프로젝트의 특정 이터레이션을 삭제합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의.예측/프로젝트/반복/내보내기/작업 | 학습된 반복을 내보냅니다. |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/읽기 | 특정 반복을 가져옵니다. 프로젝트에 대한 반복을 가져옵니다.* |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/게시/작업 | 특정 이터레이션을 게시합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/쓰기 | 특정 이터레이션을 업데이트합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/반복/내보내기** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/내보내기/읽기 | 특정 반복에 대한 내보내기 목록을 가져옵니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/반복/성능** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/성능/읽기 | 반복에 대한 자세한 성능 정보를 가져옵니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/반복/성능/이미지** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/성능/이미지/읽기 | 이 API는 일괄 처리 및 범위 선택을 지원합니다. 기본적으로 이미지와 일치하는 처음 50개의 이미지만 반환됩니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/반복/성능/이미지/개수** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/반복/성능/이미지/개량 | 필터링은 및/또는 관계에 있습니다. 예를 들어 제공된 태그 ID가 "Dog"와 |
> |  | **계정/사용자 정의 비전.예측/프로젝트/반복/게시** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의.예측/프로젝트/반복/게시/삭제 | 특정 반복을 게시 취소합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/예측** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/예측/삭제 | 예측된 이미지 세트와 관련 예측 결과를 삭제합니다. |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/프로젝트/예측/쿼리/작업 | 예측 끝점으로 전송된 이미지를 가져옵니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/빠른 테스트** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/빠른 테스트/이미지/작업 | 이미지를 빠르게 테스트합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/빠른 테스트/URL/작업 | 이미지 URL을 빠르게 테스트합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/태그** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/태그/삭제 | 프로젝트에서 태그를 삭제합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/태그/읽기 | 특정 태그에 대한 정보를 가져옵니다. 지정된 프로젝트 및 반복에 대한 태그를 가져옵니다.* |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/태그/쓰기 | 태그를 업데이트합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/태그샌드리전** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/태그샌드 지역/제안/작업 | 이 API는 태그가 지정되지 않은 이미지의 배열/배치에 대한 권장 태그 및 영역과 태그에 대한 신뢰도를 얻습니다. 태그가 없는 경우 빈 배열을 반환합니다. |
> |  | **계정/사용자 정의 비전.예측/프로젝트/기차** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/프로젝트/기차/고급/작업 | 파이프라인구성 및 교육 유형을 통해 교육을 위한 큐 프로젝트. |
> |  | **계정/사용자 정의 비전.예측/할당량** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/할당량/삭제 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/할당량/새로 고침** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/할당량/새로 고침/쓰기 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/사용/예측/사용자** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/사용/예측/사용자/읽기 | 옥스포드 사용자에 대한 예측 리소스에 대한 사용 받기 |
> |  | **계정/사용자 정의 비전.예측/사용/교육/리소스/계층** |  |
> | DataAction | Microsoft.CognitiveServices/계정/사용자 정의 비전.예측/사용/교육/리소스/계층/읽기 | Azure 사용자에 대한 교육 리소스사용 |
> |  | **계정/사용자 정의 비전.예측/사용/교육/사용자** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/사용/교육/사용자/읽기 | 옥스포드 사용자를 위한 교육 리소스 사용 |
> |  | **계정/사용자 정의 비전.예측/사용자** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의.예측/사용자/삭제 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/사용자/상태** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의.예측/사용자/상태/쓰기 | 사용자 상태 업데이트 |
> |  | **계정/사용자 정의 비전.예측/사용자/계층** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의.예측/사용자/계층/쓰기 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/사용자** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의.예측/사용자/읽기 | *정의되지 않음* |
> |  | **계정/사용자 정의 비전.예측/화이트리스트** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/화이트리스트/삭제 | 특정 기능을 가진 화이트리스트 사용자 삭제 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/화이트리스트/읽기 | 특정 기능을 가진 화이트리스트 사용자 목록 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/사용자 정의 비전.예측/화이트리스트/쓰기 | 특정 기능을 가진 화이트리스트에서 사용자를 업데이트하거나 만듭니다. |
> |  | **계정/엔터티 검색** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/엔티티서치/검색/액션 | 지정된 쿼리에 대한 엔터티 및 배치 결과를 가져옵니다. |
> |  | **계정/얼굴** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/감지/작업 | 이미지에서 사람의 얼굴을 감지하고, 얼굴 사각형을 반환하며, 선택적으로 faceId, 랜드마크 및 속성을 사용합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/찾기 유사/작업 | 쿼리 얼굴의 faceId를 지정하여 faceId 배열, 얼굴 목록 또는 큰 얼굴 목록에서 비슷한 얼굴을 검색합니다. faceId |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/그룹/작업 | 얼굴 유사성에 따라 후보 면을 그룹으로 나눕니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/식별/작업 | 1 대 다 식별은 사람 그룹 또는 대규모 사람 그룹에서 특정 쿼리 사람 얼굴의 가장 가까운 일치를 찾을 수 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/확인/작업 | 두 얼굴이 같은 사람에 속하는지 또는 한 얼굴이 사람에게 속하는지 여부를 확인합니다. |
> |  | **계정/얼굴/얼굴 목록** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/얼굴 목록/쓰기 | 사용자 지정 faceListId, 이름 및 선택적 userData를 사용하여 빈 얼굴 목록을 만듭니다. 이름과 userData를 포함하여 최대 64개의 얼굴 목록이 얼굴 목록의 업데이트 정보를 허용합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/얼굴 목록/삭제 | 지정된 면 목록을 삭제합니다. 얼굴 목록의 관련 얼굴 이미지도 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/얼굴 목록/읽기 | 얼굴 목록에서 얼굴ListId, 이름, userData 및 얼굴 목록에서 얼굴을 검색합니다. 얼굴 목록의 얼굴ListId, 이름 및 사용자 데이터를 나열합니다. |
> |  | **계정/얼굴/얼굴 목록/지속된 얼굴** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/얼굴 목록/지속적인 얼굴/쓰기 | 지정된 면 리스트에 최대 1,000개의 면을 추가합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/얼굴 목록/지속얼굴/삭제 | 지정된 얼굴ListId 및 persisitedFaceId로 얼굴 목록에서 면을 삭제합니다. 관련 얼굴 이미지도 삭제됩니다. |
> |  | **계정/얼굴/대형 얼굴 목록** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대입 글/쓰기 | 사용자 지정 largeFaceListId, 이름 및 선택적 userData를 사용하여 빈 큰 얼굴 목록을 만듭니다. 이름 및 userData를 포함하여 큰 얼굴 목록의 정보를 업데이트합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대얼굴 리스트/삭제 | 지정된 큰 면 목록을 삭제합니다. 큰 얼굴 목록의 관련 얼굴 이미지도 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대얼굴 목록/읽기 | 큰 얼굴 목록의 큰FaceListId, 이름, userData를 검색합니다. 큰 얼굴 목록의 정보를 나열큰FaceListId, 이름 및 사용자데이터. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대형 얼굴 리스트/기차/작업 | 큰 얼굴 목록 교육 작업을 제출합니다. 훈련은 훈련된 대형 얼굴 목록만 사용할 수 있는 중요한 단계입니다. |
> |  | **계정/얼굴/대얼굴/지속얼굴** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/큰 얼굴/지속적인 얼굴/쓰기 | 지정된 큰 면 리스트에 면을 최대 1,000,000개의 면에 추가합니다. 지정된 얼굴의 userData 필드를 지속성FaceId로 큰 면 목록에서 업데이트합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/큰 얼굴 목록/지속적인 얼굴/삭제 | 지정된 큰 얼굴 목록에서 얼굴을 삭제얼굴리스트Id 및 persisitedFaceId. 관련 얼굴 이미지도 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/큰 얼굴/지속적인 얼굴/읽기 | 큰 얼굴 목록에서 큰 얼굴 목록에서 지속된 얼굴을 검색하여 FaceListId 및 지속얼굴Id를 검색합니다. 지정된 큰 얼굴 목록에서 얼굴의 지속FaceId 및 userData를 나열합니다. |
> |  | **계정/얼굴/대형 얼굴 목록/교육** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대세리스트/교육/읽기 | 완료된 대형 얼굴 목록 교육 상태를 확인하거나 진행 중인 상태를 확인합니다. 대규모 페이스리스트 교육은 비동기 작업입니다. |
> |  | **계정/얼굴/대규모 그룹** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대형 그룹/쓰기 | 사용자 지정 큰 PersonGroupId, 이름 및 선택적 userData를 사용 하 여 새 큰 사람 그룹을 만듭니다. 기존 대규모 사용자 그룹의 이름과 userData를 업데이트합니다. 속성이 요청 본문에 없는 경우 변경 되지 않습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대규모 그룹/삭제 | 지정된 personGroupId를 가진 기존 큰 사람 그룹을 삭제합니다. 이 대규모 사용자 그룹의 지속된 데이터는 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대규모 그룹/읽기 | 이름과 userData를 포함하여 대규모 그룹의 정보를 검색합니다. 이 API는 대용량 그룹 정보 목록을 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대형 그룹/기차/작업 | 대규모 그룹 교육 작업을 제출합니다. 교육은 훈련된 대규모 그룹만이 사용할 수 있는 중요한 단계입니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대규모 그룹/사람/작업 | 지정된 대규모 그룹에서 새 사람을 만듭니다. 이 사람에게 얼굴을 추가하려면 |
> |  | **계정/얼굴/대규모 그룹/사람** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대규모 그룹/사람/삭제 | 대규모 그룹에서 기존 사람을 삭제합니다. 저장된 모든 사람 데이터 및 개인 항목의 얼굴 이미지는 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대규모 그룹/사람/읽기 | 사용자의 이름과 userData를 검색하고 등록된 사람 얼굴 이미지를 나타내는 지속된 faceId를 검색합니다. personId, 이름, userData 및 persistedFaceIds를 포함하여 지정된 대인 그룹에 모든 사람의 정보를 나열합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대규모 그룹/사람/쓰기 | 업데이트 이름 또는 사용자데이터. |
> |  | **계정/얼굴/대형 그룹/사람/지속적인 얼굴** |  |
> | DataAction | Microsoft.CognitiveServices/계정/얼굴/대형 사용자 그룹/사람/지속적인 얼굴/쓰기 | 얼굴 식별 또는 확인을 위해 개인그룹에 얼굴 이미지를 추가합니다. 업데이트 이미지를 처리하기 위해 사람이 얼굴의 userData 필드를 유지했습니다. |
> | DataAction | Microsoft.CognitiveServices/계정/얼굴/대규모 그룹/사람/지속적인 얼굴/삭제 | 큰 사람 그룹의 사람에서 얼굴을 삭제합니다. 이 얼굴 항목과 관련된 얼굴 데이터 및 이미지도 삭제됩니다. |
> | DataAction | Microsoft.CognitiveServices/계정/얼굴/대형 사용자 그룹/사람/지속적인 얼굴/읽기 | 사람 얼굴 정보를 검색합니다. 지속된 사람의 얼굴은 큰 사람에 의해 지정됩니다PersonGroupId, personId 및 지속얼굴Id. |
> |  | **계정/얼굴/대규모 그룹/교육** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/대형 그룹/교육/읽기 | 대규모 그룹 교육 상태가 완료또는 진행 중인지 확인합니다. LargePersonGroup 교육은 비동기 작업입니다. |
> |  | **계정/얼굴/개인 그룹** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/쓰기 | 지정된 personGroupId, 이름 및 사용자 제공 userData를 사용하여 새 사용자 그룹을 만듭니다. 기존 사용자 그룹의 이름과 userData를 업데이트합니다. 속성이 요청 본문에 없는 경우 변경 되지 않습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/삭제 | 지정된 personGroupId를 가진 기존 사람 그룹을 삭제합니다. 이 사용자 그룹의 유지된 데이터는 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/읽기 | 사람 그룹 이름 및 userData를 검색합니다. 이 사용자 그룹 에서 사람 정보를 얻으려면 사용자 그룹의 pesonGroupId, 이름 및 userData를 사용합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/기차/작업 | 사람 그룹 교육 작업을 제출합니다. 교육은 숙련된 사람 그룹만이 사용할 수 있는 중요한 단계입니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/사람/작업 | 지정된 사람 그룹에 새 사람을 만듭니다. 이 사람에게 얼굴을 추가하려면 |
> |  | **계정/얼굴/개인 그룹/사람** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/사람/삭제 | 사람 그룹에서 기존 사람을 삭제합니다. 저장된 모든 사람 데이터 및 개인 항목의 얼굴 이미지는 삭제됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/사람/읽기 | 사용자의 이름과 userData를 검색하고 등록된 사람 얼굴 이미지를 나타내는 지속된 faceId를 검색합니다. personId, 이름, userData 및 등록된 FaceIds를 포함하여 지정된 사용자 그룹에 모든 사람의 정보를 나열합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/사람/쓰기 | 업데이트 이름 또는 사용자데이터. |
> |  | **계정/얼굴/개인 그룹/사람/지속적인 얼굴** |  |
> | DataAction | Microsoft.CognitiveServices/계정/얼굴/사람 그룹/사람/지속적인 얼굴/쓰기 | 얼굴 식별 또는 확인을 위해 사람 그룹에 얼굴 이미지를 추가합니다. 여러 업데이트의 이미지를 처리하기 위해 사람이 얼굴의 userData 필드를 유지했습니다. |
> | DataAction | Microsoft.CognitiveServices/계정/얼굴/사람 그룹/사람/지속적인 얼굴/삭제 | 사람 그룹의 사람에서 얼굴을 삭제합니다. 이 얼굴 항목과 관련된 얼굴 데이터 및 이미지도 삭제됩니다. |
> | DataAction | Microsoft.CognitiveServices/계정/얼굴/사람 그룹/사람/지속적인 얼굴/읽기 | 사람 얼굴 정보를 검색합니다. 지속된 사람 얼굴은 해당 personGroupId, personId 및 persistedFaceId에 의해 지정됩니다. |
> |  | **계정/얼굴/개인 그룹/교육** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/사람 그룹/교육/읽기 | 완료된 사람 그룹 교육 상태를 확인하거나 진행 중인 상태를 확인합니다. PersonGroup 교육이 트리거된 비동기 작업입니다. |
> |  | **계정/얼굴/스냅샷** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/스냅샷/테이크/액션 | 개체에 대한 스냅숏을 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/스냅샷/읽기 | 스냅숏 작업의 상태를 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/스냅샷/적용/작업 | 사용자 지정 개체 ID를 제공하는 스냅숏을 적용합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/스냅샷/삭제 | 스냅샷을 삭제합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/스냅샷/쓰기 | 스냅숏의 속성을 업데이트합니다. |
> |  | **계정/얼굴/스냅샷** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/얼굴/스냅샷/읽기 | 사용자의 액세스 가능한 모든 스냅샷을 정보와 함께 나열합니다.* |
> |  | **계정/양식 인식기/사용자 지정** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/FormRecognizer/사용자 지정/기차/작업 | 사용자 지정 모델을 만들고 학습합니다.<br>train 요청에는 외부에서 액세스할 수 있는 Azure Storage Blob 컨테이너 Uri(가급적 공유 액세스 시그니처 Uri) 또는 로컬로 마운트된 드라이브의 데이터 폴더에 대한 유효한 경로인 소스 매개 변수가 포함되어야 합니다.<br>로컬 경로가 지정되면 Linux/Unix 경로 형식을 따르고 입력 마운트 구성에 기반한 절대 경로여야 합니다. |
> |  | **계정/양식 인식기/사용자 지정/모델** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/FormRecognizer/사용자 지정/모델/분석/작업 | 지정된 문서에서 키-값 쌍을 추출합니다. 입력 문서는 '응용 프로그램/pdf', '이미지/jpeg' 또는 '이미지/png'와 같은 지원되는 콘텐츠 유형 중 하나여야 합니다. 성공 응답이 JSON으로 반환됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/FormRecognizer/사용자 지정/모델/삭제 | 모델 아티팩트를 삭제합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/폼 인식기/사용자 지정/모델/읽기 | 모델에 대한 정보를 가져옵니다. 모든 학습된 사용자 지정 모델에 대한 정보 얻기* |
> |  | **계정/FormRecognizer/사용자 지정/모델/키** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/FormRecognizer/사용자 지정/모델/키/읽기 | 모델의 키를 검색합니다. |
> |  | **계정/양식 인식기/사전 빌드/영수증** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/FormRecognizer/미리 빌드/영수증/비동기 분석/작업 | 지정된 수신 문서에서 필드 텍스트 및 의미 체계 값을 추출합니다. 입력 이미지 문서는 지원되는 콘텐츠 유형 중 하나여야 합니다- JPEG, PNG, BMP, PDF 또는 TIFF. 성공 응답은 결과를 비동기적으로 검색하기 위해 수신 결과 작업URL을 포함하는 '작업 위치'라는 필드를 포함하는 JSON입니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/FormRecognizer/미리 빌드/영수증/작업/작업 | 상태를 쿼리하고 확인 분석 작업의 결과를 검색합니다. 이 인터페이스의 URL은 영수증 분석 응답의 '작업 위치' 헤더에서 가져올 수 있습니다. |
> |  | **계정/이미지 검색** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/이미지 서치/세부 정보/작업 | 이미지가 포함된 웹 페이지와 같은 이미지에 대한 인사이트를 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/이미지 검색/검색/작업 | 지정된 쿼리에 대한 관련 이미지를 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/이미지 서치/추세/작업 | 현재 인기 있는 이미지를 가져옵니다. |
> |  | **계정/몰입리더** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/몰입리더/getcontentmodelfor/action | 몰입형 리더 세션 생성 |
> |  | **계정/잉크 인식기** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/잉크 인식기/인식/작업 | 스트로크 데이터 집합이 콘텐츠를 분석하고 인식된 텍스트를 포함하여 인식된 엔터티 목록을 생성합니다. |
> |  | **계정/LUIS** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/LUIS/예측/작업 | 지정된 쿼리에 대해 게시된 끝점 예측을 가져옵니다. |
> |  | **계정/뉴스검색** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/뉴스 검색/범주 검색/작업 | 제공된 범주에 대한 뉴스를 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/뉴스 검색/검색/작업 | 지정된 쿼리와 관련된 뉴스 기사를 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/뉴스검색/트렌드토픽/액션 | Bing에서 식별된 인기 토픽을 가져옵니다. 다음은 Bing 홈 페이지 하단의 배너에 표시된 것과 동일한 항목입니다. |
> |  | **계정/QnAMaker** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/루트/작업 | QnA Maker |
> |  | **계정/QnAMaker/변경** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/변경/읽기 | 런타임에서 변경 내용을 다운로드합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/변경/쓰기 | 변경 데이터를 바꿉습니다. |
> |  | **계정/QnAMaker/끝점키** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/끝점키/읽기 | 끝점에 대한 끝점 키를 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/끝점키/새로 고침/작업 | 끝점 키를 다시 생성합니다. |
> |  | **계정/QnAMaker/엔드포인트 설정** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/엔드포인트 설정/읽기 | 끝점에 대한 끝점 설정 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/엔드포인트 설정/쓰기 | 끝점에 대한 끝점 표시를 업데이트합니다. |
> |  | **계정/QnAMaker/지식 기반** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 자료/게시/작업 | 기술 자료의 테스트 인덱스의 모든 변경 내용을 prod 인덱스에 게시합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 기반/삭제 | 기술 자료 및 모든 데이터를 삭제합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 기반/읽기 | 특정 기술 자료의 기술 자료 목록 또는 세부 정보를 가져옵니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 기반/쓰기 | 기술 자료 수정 또는 기술 자료 내용 바꾸기를 위한 비동기 작업입니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 자료/생성 답변/작업 | 생성응답 호출을 사용하여 기술 자료 쿼리합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 기반/기차/작업 | 기술 자료에 제안을 추가하기 위해 호출을 교육합니다. |
> |  | **계정/QnAMaker/지식 자료/생성** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 자료/만들기/쓰기 | 새 기술 사이트를 만드는 비동기 작업입니다. |
> |  | **계정/QnAMaker/지식 기반/다운로드** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/지식 자료/다운로드/읽기 | 기술 사이트를 다운로드합니다. |
> |  | **계정/QnAMaker/운영** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/QnAMaker/운영/읽기 | 특정 장기 실행 작업에 대한 세부 정보를 가져옵니다. |
> |  | **계정/맞춤법 검사** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/맞춤법 검사/맞춤법 검사/작업 | GET 또는 POST를 통해 맞춤법 검사 쿼리 결과를 가져옵니다. |
> |  | **계정/텍스트 분석** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/텍스트 분석/언어/작업 | API는 검색된 언어와 0에서 1 사이의 숫자 점수를 반환합니다. 점수가 1에 가까울수록 식별된 언어가 true라는 100% 확실성을 나타냅니다. 총 120개의 언어가 지원됩니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/텍스트 분석/엔터티/작업 | API는 지정된 문서에서 알려진 엔터티 및\"일반\" \"명명된 \"\" 엔터티(사람, 위치,\"조직 등)의 목록을 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/텍스트 분석/키프레이즈/액션 | 이 API는 입력 텍스트의 핵심 요지를 나타내는 문자열 목록을 반환합니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/텍스트 분석/감정/작업 | 이 API는 0~1 사이의 숫자 점수를 반환합니다.<br>1에 가까운 점수는 긍정적인 정서를 나타내고, 0에 가까운 점수는 부정적인 정서를 나타냅니다.<br>점수가 0.5이면 감정이 부족했음을 나타냅니다(예:<br>사실진술). |
> |  | **계정/비디오 검색** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/비디오 검색/추세/작업 | 현재 인기 동영상보기 |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/비디오 검색/세부 정보/작업 | 관련 동영상과 같은 동영상에 대한 인사이트를 얻을 수 있습니다. |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/비디오 검색/검색/작업 | 특정 검색어와 관련된 동영상을 가져옵니다. |
> |  | **계정/비주얼 서치** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/비주얼서치/검색/액션 | 제공된 이미지와 관련된 태그 목록을 반환합니다. |
> |  | **계정/웹 서치** |  |
> | DataAction | 마이크로소프트.코그너티브 서비스/계정/웹 검색/검색/작업 | 특정 쿼리에 대한 웹, 이미지, 뉴스, & 비디오 결과를 가져옵니다. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.커머스/레지스터/액션 | Microsoft 상거래 사용 집계에 대한 구독 등록 |
> | 작업 | Microsoft.상거래/등록 취소/작업 | Microsoft 상거래 사용 집계에 대한 등록 취소 구독 |
> |  | **RateCard** |  |
> | 작업 | Microsoft.Commerce/RateCard/read | 지정된 구독에 대한 제안 데이터, 리소스/미터 메타데이터 및 요율을 제공합니다. |
> |  | **사용량 집계** |  |
> | 작업 | Microsoft.Commerce/UsageAggregates/read | 구독을 통해 Microsoft Azure의 사용량을 검색합니다. 결과에는 특정 시간 범위의 집계 사용량 현황 데이터, 구독 및 리소스 관련 정보가 포함됩니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

Azure 서비스: [가상 컴퓨터,](../virtual-machines/index.yml) [가상 컴퓨터 규모 집합](../virtual-machine-scale-sets/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Compute/register/action | Microsoft.Compute 리소스 공급자에 구독을 등록합니다. |
> | 작업 | 마이크로소프트.계산/등록 취소/작업 | Microsoft.Compute 리소스 공급자를 사용 하 여 구독을 등록 취소 합니다. |
> |  | **가용성세트** |  |
> | 작업 | Microsoft.Compute/availabilitySets/read | 가용성 집합의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/availabilitySets/write | 새 가용성 집합을 만들거나 기존 가용성 집합을 업데이트합니다. |
> | 작업 | Microsoft.Compute/availabilitySets/delete | 가용성 집합을 삭제합니다. |
> |  | **가용성세트/vmSizes** |  |
> | 작업 | Microsoft.Compute/availabilitySets/vmSizes/read | 가용성 집합에서 가상 머신을 만들거나 업데이트하기 위한 사용 가능 크기를 나열합니다. |
> |  | **디스크 암호화 세트** |  |
> | 작업 | 마이크로소프트.계산/디스크암호화세트/읽기 | 디스크 암호화 세트의 속성 받기 |
> | 작업 | 마이크로소프트.계산/디스크암호화세트/쓰기 | 새 디스크 암호화 세트를 만들거나 기존 디스크 암호화 집합을 업데이트합니다. |
> | 작업 | 마이크로소프트.계산/디스크암호화세트/삭제 | 디스크 암호화 세트 삭제 |
> |  | **디스크** |  |
> | 작업 | Microsoft.Compute/disks/read | 디스크의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/disks/write | 새 디스크를 만들거나 기존 디스크를 업데이트합니다. |
> | 작업 | Microsoft.Compute/disks/delete | 디스크를 삭제합니다. |
> | 작업 | Microsoft.Compute/disks/beginGetAccess/action | Blob 액세스에 대한 디스크의 SAS URI를 가져옵니다. |
> | 작업 | Microsoft.Compute/disks/endGetAccess/action | 디스크의 SAS URI를 취소합니다. |
> |  | **galleries** |  |
> | 작업 | Microsoft.Compute/galleries/read | 갤러리의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/galleries/write | 새 갤러리를 만들거나 기존 갤러리를 업데이트합니다. |
> | 작업 | Microsoft.Compute/galleries/delete | 갤러리를 삭제합니다. |
> |  | **갤러리/ 응용 프로그램** |  |
> | 작업 | 마이크로소프트.계산/갤러리/응용 프로그램/읽기 | 갤러리 응용 프로그램의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.컴퓨트/갤러리/응용 프로그램/쓰기 | 새 갤러리 응용 프로그램을 만들거나 기존 갤러리 응용 프로그램을 업데이트합니다. |
> | 작업 | 마이크로소프트.컴퓨트/갤러리/응용 프로그램/삭제 | 갤러리 응용 프로그램 삭제 |
> |  | **갤러리 / 응용 프로그램 / 버전** |  |
> | 작업 | 마이크로소프트.계산/갤러리/응용 프로그램/버전/읽기 | 갤러리 응용 프로그램 버전의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.컴퓨트/갤러리/응용 프로그램/버전/쓰기 | 새 갤러리 응용 프로그램 버전을 만들거나 기존 응용 프로그램 버전을 업데이트합니다. |
> | 작업 | Microsoft.Compute/갤러리/응용 프로그램/버전/삭제 | 갤러리 응용 프로그램 버전 삭제 |
> |  | **galleries/images** |  |
> | 작업 | Microsoft.Compute/galleries/images/read | 갤러리 이미지의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/galleries/images/write | 새 갤러리 이미지를 만들거나 기존 갤러리 이미지를 업데이트합니다. |
> | 작업 | Microsoft.Compute/galleries/images/delete | 갤러리 이미지를 삭제합니다. |
> |  | **galleries/images/versions** |  |
> | 작업 | Microsoft.Compute/galleries/images/versions/read | 갤러리 이미지 버전의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/galleries/images/versions/write | 새 갤러리 이미지 버전을 만들거나 기존 갤러리 이미지 버전을 업데이트합니다. |
> | 작업 | Microsoft.Compute/galleries/images/versions/delete | 갤러리 이미지 버전을 삭제합니다. |
> |  | **호스트 그룹** |  |
> | 작업 | 마이크로소프트.컴퓨트/호스트 그룹/읽기 | 호스트 그룹의 속성 받기 |
> | 작업 | 마이크로소프트.컴퓨트/호스트 그룹/쓰기 | 새 호스트 그룹을 만들거나 기존 호스트 그룹을 업데이트합니다. |
> | 작업 | 마이크로소프트.컴퓨트/호스트 그룹/삭제 | 호스트 그룹 삭제 |
> |  | **호스트 그룹/호스트** |  |
> | 작업 | Microsoft.계산/호스트 그룹/호스트/읽기 | 호스트의 속성 받기 |
> | 작업 | 마이크로소프트.컴퓨트/호스트 그룹/호스트/쓰기 | 새 호스트를 만들거나 기존 호스트를 업데이트합니다. |
> | 작업 | Microsoft.Compute/호스트 그룹/호스트/삭제 | 호스트 삭제 |
> |  | **이미지** |  |
> | 작업 | Microsoft.Compute/images/read | 이미지의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/images/write | 새 이미지를 만들거나 기존 이미지를 업데이트합니다. |
> | 작업 | Microsoft.Compute/images/delete | 이미지를 삭제합니다. |
> |  | **위치/캡운영** |  |
> | 작업 | Microsoft.Compute/locations/capsOperations/read | 비동기 Caps 작업의 상태를 가져옵니다. |
> |  | **위치/디스크운영** |  |
> | 작업 | Microsoft.Compute/locations/diskOperations/read | 비동기 디스크 작업의 상태를 가져옵니다. |
> |  | **위치/로그애널리틱스** |  |
> | 작업 | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 제한 진단을 지원하기 위해 시간 간격별로 총 요청을 표시할 로그를 만듭니다. |
> | 작업 | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | ResourceName, OperationName 또는 적용된 제한 정책에서 그룹화한 제한된 요청의 집계를 표시할 로그를 만듭니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.Compute/locations/operations/read | 비동기 작업의 상태를 가져옵니다. |
> |  | **위치/게시자** |  |
> | 작업 | Microsoft.Compute/locations/publishers/read | 게시자의 속성을 가져옵니다. |
> |  | **위치/게시자/아티팩트 유형/제안** |  |
> | 작업 | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 플랫폼 이미지 제품의 속성을 가져옵니다. |
> |  | **위치/게시자/아티팩트 유형/제안/스쿠스** |  |
> | 작업 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 플랫폼 이미지 Sku의 속성을 가져옵니다. |
> |  | **위치/게시자/아티팩트 유형/제안/skus/버전** |  |
> | 작업 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 플랫폼 이미지 버전의 속성을 가져옵니다. |
> |  | **위치/게시자/아티팩트 유형/유형** |  |
> | 작업 | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VMExtension 형식의 속성을 가져옵니다. |
> |  | **위치/게시자/아티팩트 유형/유형/버전** |  |
> | 작업 | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VMExtension 버전의 속성을 가져옵니다. |
> |  | **위치/실행 명령** |  |
> | 작업 | Microsoft.Compute/locations/runCommands/read | 위치에 사용할 수 있는 실행 명령을 나열합니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.Compute/locations/usages/read | 한 위치에 있는 구독의 Compute 리소스에 대한 서비스 제한 및 현재 사용 수량을 가져옵니다. |
> |  | **위치/vmSizes** |  |
> | 작업 | Microsoft.Compute/locations/vmSizes/read | 한 위치에서 사용 가능한 가상 머신 크기를 나열합니다. |
> |  | **위치/vsm운영** |  |
> | 작업 | 마이크로소프트.계산/위치/vsm운영/읽기 | 가상 컴퓨터 런타임 서비스 확장으로 가상 시스템 규모 집합에 대한 비동기 작업 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Compute/operations/read | Microsoft.Compute 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> |  | **근접배치그룹** |  |
> | 작업 | 마이크로소프트.계산/근접배치그룹/읽기 | 근접 배치 그룹의 속성 받기 |
> | 작업 | 마이크로소프트.계산/근접배치그룹/쓰기 | 새 근접 배치 그룹을 만들거나 기존 위치 그룹을 업데이트합니다. |
> | 작업 | 마이크로소프트.계산/근접배치그룹/삭제 | 근접 배치 그룹 삭제 |
> |  | **복원포인트컬렉션** |  |
> | 작업 | Microsoft.Compute/restorePointCollections/read | 복원 지점 컬렉션의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/restorePointCollections/write | 새 복원 지점 컬렉션을 만들거나 기존 복원 지점 컬렉션을 업데이트합니다. |
> | 작업 | Microsoft.Compute/restorePointCollections/delete | 복원 지점 컬렉션 및 포함된 복원 지점을 삭제합니다. |
> |  | **restorePointCollections/restorePoints** |  |
> | 작업 | Microsoft.Compute/restorePointCollections/restorePoints/read | 복원 지점의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/restorePointCollections/restorePoints/write | 새 복원 지점을 만듭니다. |
> | 작업 | Microsoft.Compute/restorePointCollections/restorePoints/delete | 복원 지점을 삭제합니다. |
> | 작업 | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Blob SAS URI와 함께 복원 지점의 속성을 가져옵니다. |
> |  | **공유VM이미지** |  |
> | 작업 | Microsoft.Compute/sharedVMImages/read | SharedVMImage의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/sharedVMImages/write | 새 SharedVMImage를 만들거나 기존 SharedVMImage를 업데이트합니다. |
> | 작업 | Microsoft.Compute/sharedVMImages/delete | SharedVMImage를 삭제합니다. |
> |  | **공유VM이미지/버전** |  |
> | 작업 | Microsoft.Compute/sharedVMImages/versions/read | SharedVMImageVersion의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/sharedVMImages/versions/write | 새 SharedVMImageVersion을 만들거나 기존 SharedVMImageVersion을 업데이트합니다. |
> | 작업 | Microsoft.Compute/sharedVMImages/versions/delete | SharedVMImageVersion을 삭제합니다. |
> | 작업 | Microsoft.Compute/sharedVMImages/versions/replicate/action | SharedVMImageVersion을 대상 지역에 복제합니다. |
> |  | **Sku** |  |
> | 작업 | Microsoft.Compute/skus/read | 구독에 사용할 수 있는 Microsoft.Compute SKU 목록을 가져옵니다. |
> |  | **스냅샷** |  |
> | 작업 | Microsoft.Compute/snapshots/read | 스냅샷의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/snapshots/write | 새 스냅샷을 만들거나 기존 스냅샷을 업데이트합니다. |
> | 작업 | Microsoft.Compute/snapshots/delete | 스냅샷을 삭제합니다. |
> | 작업 | Microsoft.Compute/snapshots/beginGetAccess/action | Blob 액세스에 대한 스냅샷의 SAS URI를 가져옵니다. |
> | 작업 | Microsoft.Compute/snapshots/endGetAccess/action | 스냅샷의 SAS URI를 취소합니다. |
> |  | **가상 머신** |  |
> | 작업 | Microsoft.Compute/virtualMachines/read | 가상 머신의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/virtualMachines/write | 새 가상 머신을 만들거나 기존 가상 머신을 업데이트합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/delete | 가상 머신을 삭제합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/start/action | 가상 머신을 시작합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/powerOff/action | 가상 머신을 끕니다. 가상 컴퓨터에는 요금이 계속 청구됩니다. |
> | 작업 | Microsoft.Compute/virtualMachines/redeploy/action | 가상 머신을 다싯 배포합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/restart/action | 가상 머신을 다시 시작합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/deallocate/action | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/generalize/action | 가상 컴퓨터 상태를 일반화됨으로 설정하고 가상 컴퓨터 캡처를 준비합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/capture/action | 가상 하드 디스크를 복사하여 가상 머신을 캡처하고 유사한 가상 머신을 만드는 데 사용할 수 있는 템플릿을 생성합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/runCommand/action | 가상 머신에서 미리 정의된 스크립트를 실행합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 가상 머신의 blob 기반 디스크를 관리되는 디스크로 변환합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/performMaintenance/action | VM에서 유지 관리 작업을 수행합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/reimage/action | 차이점 보관용 디스크를 사용하는 가상 머신을 이미지로 다시 설치합니다. |
> | 작업 | 마이크로소프트.컴퓨트/가상 머신/설치패치/작업 | 사용자가 제공한 매개 변수를 기반으로 가상 시스템에 사용 가능한 OS 업데이트 패치를 설치합니다. 사용 가능한 패치 목록이 포함된 평가 결과도 이 의 일부로 새로 고쳐집니다. |
> | 작업 | 마이크로소프트.컴퓨트/가상 머신/평가패치/작업 | 가상 컴퓨터를 평가하고 사용 가능한 OS 업데이트 패치 목록을 찾습니다. |
> | 작업 | 마이크로소프트.컴퓨트/가상 머신/취소패설치/작업 | 가상 컴퓨터에서 진행 중인 설치 OS 업데이트 패치 작업을 취소합니다. |
> |  | **가상 머신/확장** |  |
> | 작업 | Microsoft.Compute/virtualMachines/extensions/read | 가상 머신 확장의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/virtualMachines/extensions/write | 새 가상 머신 확장을 만들거나 기존 가상 머신 확장을 업데이트합니다. |
> | 작업 | Microsoft.Compute/virtualMachines/extensions/delete | 가상 머신 확장을 삭제합니다. |
> |  | **가상 머신/인스턴스보기** |  |
> | 작업 | Microsoft.Compute/virtualMachines/instanceView/read | 가상 머신 및 해당 리소스의 자세한 런타임 상태를 가져옵니다. |
> |  | **가상 머신/vmSizes** |  |
> | 작업 | Microsoft.Compute/virtualMachines/vmSizes/read | 가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다. |
> |  | **가상머신스케일세트** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/Read | 가상 머신 확장 집합의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/Write | 새 가상 머신 확장 집합을 만들거나 기존 가상 머신 확장 집합을 업데이트합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/delete | 가상 머신 확장 집합을 삭제합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/delete/action | 가상 머신 확장 집합의 인스턴스를 삭제합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/start/action | 가상 머신 확장 집합의 인스턴스를 시작합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 가상 머신 확장 집합의 인스턴스를 끕니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/restart/action | 가상 머신 확장 집합의 인스턴스를 다시 시작합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 가상 머신 확장 집합의 인스턴스에 대한 Compute 리소스를 끄고 해제합니다.  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 수동으로 인스턴스를 최신 모델의 가상 머신 확장 집합으로 업데이트합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 가상 머신 확장 집합의 인스턴스를 이미지로 다시 설치합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 가상 머신 확장 집합 인스턴스의 모든 디스크(OS 디스크 및 데이터 디스크)를 이미지로 다시 설치합니다.  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 가상 머신 확장 집합의 인스턴스를 다시 배포합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 가상 머신 확장 집합의 인스턴스에서 계획된 유지 관리를 수행합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/scale/action | 기존 가상 머신 확장 집합이 지정된 인스턴스 개수로 규모 감축/ 규모 확장할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 서비스 패브릭 가상 머신 확장 집합의 플랫폼 업데이트 도메인을 수동으로 탐색하여 중단된 보류 중인 업데이트를 마칩니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 모든 가상 머신 확장 집합 인스턴스를 사용 가능한 최신 플랫폼 이미지 OS 버전으로 이동하도록 롤링 업그레이드를 시작합니다. |
> | 작업 | 마이크로소프트.컴퓨트/가상머신스케일셋/롤링업그레이드/액션 | 가상 머신 확장 집합의 롤링 업그레이드를 취소합니다. |
> |  | **virtualMachineScaleSets/extensions** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 가상 머신 확장 집합 확장의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 새 가상 머신 확장 집합 확장을 만들거나 기존 가상 머신 확장 집합 확장을 업데이트합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 가상 머신 확장 집합 확장을 삭제합니다. |
> |  | **가상머신스케일세트/확장/역할** |  |
> | 작업 | 마이크로소프트.계산/가상기계스케일세트/확장/역할/읽기 | 가상 컴퓨터 런타임 서비스 확장을 통해 가상 시스템 확장 집합에서 역할의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.계산/가상기계스케일세트/확장/역할/쓰기 | 가상 컴퓨터 런타임 서비스 확장으로 가상 시스템 확장 집합에서 기존 역할의 속성을 업데이트합니다. |
> |  | **가상머신스케일세트/인스턴스뷰** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 가상 머신 확장 집합의 인스턴스 보기를 가져옵니다. |
> |  | **virtualMachineScaleSets/networkInterfaces** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 가상 머신 확장 집합의 모든 네트워크 인터페이스 속성을 가져옵니다. |
> |  | **가상머신스케일세트/오스업그레이드히스토리** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 가상 머신 확장 집합에 대한 OS 업그레이드의 기록을 가져옵니다. |
> |  | **virtualMachineScaleSets/publicIPAddresses** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 가상 머신 확장 집합의 모든 공용 IP 주소 속성을 가져옵니다. |
> |  | **가상머신스케일세트/롤링업그레이드** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 가상 머신 확장 집합의 최신 롤링 업그레이드 상태를 가져옵니다. |
> |  | **가상머신스케일세트/스쿠스** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/skus/read | 기존 가상 머신 확장 집합에 대한 유효한 SKU를 나열합니다. |
> |  | **virtualMachineScaleSets/virtualMachines** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | VM 확장 집합에서 Virtual Machine의 속성을 가져옵니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | VM 확장 집합에 있는 가상 머신의 속성을 업데이트합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | VM 확장 집합에서 특정 Virtual Machine을 삭제합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | VM 확장 집합에서 Virtual Machine 인스턴스를 시작합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | VM 확장 집합에서 Virtual Machine 인스턴스를 끕니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | VM 확장 집합에서 Virtual Machine 인스턴스를 다시 시작합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | VM 확장 집합의 Virtual Machine에 대한 Compute 리소스를 끄고 해제합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 가상 머신 확장 집합의 가상 머신 인스턴스를 이미지로 다시 설치합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 가상 머신 확장 집합의 가상 머신 인스턴스에 대한 모든 디스크(OS 디스크 및 데이터 디스크)를 이미지로 다시 설치합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 가상 머신 확장 집합의 가상 머신 인스턴스를 이미지로 다시 배포합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 가상 머신 확장 집합의 가상 머신 인스턴스에서 계획된 유지 관리를 수행합니다. |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 가상 머신 확장 집합의 가상 머신 인스턴스에서 미리 정의된 스크립트를 실행합니다. |
> |  | **가상머신스케일세트/가상머신/인스턴스뷰** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | VM 크기 집합에 있는 Virtual Machine의 인스턴스 보기를 검색합니다. |
> |  | **virtualMachineScaleSets/virtualMachines/networkInterfaces** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 가상 머신 확장 집합을 사용하여 만든 가상 머신의 한 네트워크 인터페이스 또는 모든 네트워크 인터페이스의 속성을 가져옵니다. |
> |  | **가상머신스케일세트/가상머신/네트워크인터페이스/ipConfigurations** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 가상 머신 확장 집합을 사용하여 만든 네트워크 인터페이스의 한 IP 구성 또는 모든 IP 구성의 속성을 가져옵니다. IP 구성은 프라이빗 IP를 나타냅니다. |
> |  | **가상머신스케일세트/가상머신/네트워크인터페이스/ipConfigurations/publicIP주소** |  |
> | 작업 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 가상 머신 확장 집합을 사용하여 만든 공용 IP 주소의 속성을 가져옵니다. 가상 머신 확장 집합은 ipconfiguration(프라이빗 IP)당 공용 IP를 하나만 만들 수 있습니다. |
> |  | **가상머신스케일세트/vmSizes** |  |
> | 작업 | 마이크로소프트.컴퓨트/가상머신스케일셋/vmSizes/읽기 | 가상 시스템 규모 집합에서 가상 컴퓨터를 만들거나 업데이트하는 데 사용할 수 있는 크기 나열 |
> |  | **가상머신스케일셋가상머신/익스텐션** |  |
> | 작업 | 마이크로소프트.컴퓨팅/가상머신스케일가상머신/확장/읽기 | 가상 시스템 스케일 집합에서 가상 시스템에 대한 확장 속성 받기 |
> | 작업 | 마이크로소프트.컴퓨팅/가상머신스케일가상머신/확장/쓰기 | 가상 머신 스케일 세트에서 가상 시스템에 대한 새 확장을 만들거나 기존 확장을 업데이트합니다. |
> | 작업 | 마이크로소프트.컴퓨팅/가상머신스케일가상머신/확장/삭제 | 가상 컴퓨터 배율 집합에서 가상 컴퓨터의 확장 을 삭제합니다. |
> |  | **가상 머신** |  |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 가상 머신에 일반 사용자로 로그인합니다. |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 가상 머신에 Windows 관리자 또는 Linux 루트 사용자 권한으로 로그인합니다. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

Azure 서비스: [비용 관리](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Consumption/register/action | 소비 RP에 등록합니다. |
> |  | **집계 비용** |  |
> | 작업 | 마이크로 소프트.소비 / 집계 비용 / 읽기 | 관리 그룹에 대한 집계 비용 목록입니다. |
> |  | **균형** |  |
> | 작업 | Microsoft.Consumption/balances/read | 관리 그룹의 청구 기간에 대한 사용률 요약 정보를 나열합니다. |
> |  | **예산** |  |
> | 작업 | Microsoft.Consumption/budgets/read | 구독 또는 관리 그룹별로 예산을 나열합니다. |
> | 작업 | Microsoft.Consumption/budgets/write | 구독 또는 관리 그룹별로 예산을 만들고 업데이트합니다. |
> | 작업 | Microsoft.Consumption/budgets/delete | 구독 또는 관리 그룹별로 예산을 삭제합니다. |
> |  | **요금** |  |
> | 작업 | Microsoft.Consumption/charges/read | 요금을 나열합니다. |
> |  | **크레딧** |  |
> | 작업 | Microsoft.Consumption/credits/read | 크레딧을 나열합니다. |
> |  | **이벤트** |  |
> | 작업 | Microsoft.Consumption/events/read | 이벤트를 나열합니다. |
> |  | **외부 결제 계정/태그** |  |
> | 작업 | 마이크로소프트.소비/외부 결제 계정/태그/읽기 | EA 및 구독에 대한 태그를 나열합니다. |
> |  | **외부 구독/태그** |  |
> | 작업 | 마이크로 소프트.소비 /외부 구독 / 태그 / 읽기 | EA 및 구독에 대한 태그를 나열합니다. |
> |  | **예측** |  |
> | 작업 | Microsoft.Consumption/forecasts/read | 예측을 나열합니다. |
> |  | **lots** |  |
> | 작업 | Microsoft.Consumption/lots/read | lots를 나열합니다. |
> |  | **시장** |  |
> | 작업 | Microsoft.Consumption/marketplaces/read | EA 및 WebDirect 구독의 범위에 대한 마켓플레이스 리소스 사용 정보를 나열합니다. |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.Consumption/operations/read | 작업 결과를 나열합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Consumption/operations/read | Microsoft.Consumption 리소스 공급자가 지원하는 모든 작업을 나열합니다. |
> |  | **작업 상태** |  |
> | 작업 | Microsoft.Consumption/operationstatus/read | 작업 상태를 나열합니다. |
> |  | **가격표** |  |
> | 작업 | Microsoft.Consumption/pricesheets/read | 구독 또는 관리 그룹의 가격표 데이터를 나열합니다. |
> |  | **예약 세부 정보** |  |
> | 작업 | Microsoft.Consumption/reservationDetails/read | 예약된 인스턴스의 사용률 세부 정보를 예약 순서 또는 관리 그룹별로 나열합니다. 세부 정보 데이터는 일별 인스턴스 수준입니다. |
> |  | **예약 권장 사항** |  |
> | 작업 | Microsoft.Consumption/reservationRecommendations/read | 구독의 예약 인스턴스에 대한 단일 또는 공유 권장 사항을 나열합니다. |
> |  | **예약요약** |  |
> | 작업 | Microsoft.Consumption/reservationSummaries/read | 예약된 인스턴스의 사용률 요약 정보를 예약 순서 또는 관리 그룹별로 나열합니다. 요약 데이터는 월별 또는 일별 수준입니다. |
> |  | **예약거래** |  |
> | 작업 | Microsoft.Consumption/reservationTransactions/read | 관리 그룹에서 예약한 인스턴스의 트랜잭션 기록을 나열합니다. |
> |  | **태그** |  |
> | 작업 | Microsoft.Consumption/tags/read | EA 및 구독에 대한 태그를 나열합니다. |
> |  | **거주자** |  |
> | 작업 | Microsoft.Consumption/tenants/register/action | 테넌트에 의한 Microsoft.Consumption 범위에 대한 작업을 등록합니다. |
> | 작업 | 마이크로소프트.소비/테넌타/읽기 | 테넌자 목록 |
> |  | **용어** |  |
> | 작업 | Microsoft.Consumption/terms/read | 구독 또는 관리 그룹의 사용 약관을 나열합니다. |
> |  | **usageDetails** |  |
> | 작업 | Microsoft.Consumption/usageDetails/read | EA 및 WebDirect 구독의 범위에 대한 사용량 세부 정보를 나열합니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

Azure 서비스: [컨테이너 인스턴스](../container-instances/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ContainerInstance/register/action | 컨테이너 인스턴스 리소스 공급자에 대한 구독을 등록하고 컨테이너 그룹을 만들 수 있도록 합니다. |
> |  | **containerGroups** |  |
> | 작업 | Microsoft.ContainerInstance/containerGroups/read | 모든 컨테이너 그룹을 가져옵니다. |
> | 작업 | Microsoft.ContainerInstance/containerGroups/write | 특정 컨테이너 그룹을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerInstance/containerGroups/delete | 특정 컨테이너 그룹을 삭제합니다. |
> | 작업 | Microsoft.ContainerInstance/containerGroups/restart/action | 특정 컨테이너 그룹을 다시 시작합니다. |
> | 작업 | Microsoft.ContainerInstance/containerGroups/stop/action | 특정 컨테이너 그룹을 중지합니다. 컴퓨팅 리소스 할당이 취소되고 청구가 중지됩니다. |
> | 작업 | Microsoft.ContainerInstance/containerGroups/start/action | 특정 컨테이너 그룹을 시작합니다. |
> |  | **컨테이너그룹/컨테이너** |  |
> | 작업 | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 특정 컨테이너에 실행합니다. |
> |  | **컨테이너그룹/컨테이너/빌드로그** |  |
> | 작업 | Microsoft.컨테이너 인스턴스/컨테이너 그룹/컨테이너/빌드로그/읽기 | 특정 컨테이너에 대한 빌드 로그를 가져옵니다. |
> |  | **컨테이너그룹/컨테이너/로그** |  |
> | 작업 | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 특정 컨테이너에 대한 로그를 가져옵니다. |
> |  | **컨테이너그룹/작업결과** |  |
> | 작업 | Microsoft.컨테이너 인스턴스/컨테이너 그룹/작업결과/읽기 | 비동기 작업 결과 얻기 |
> |  | **컨테이너그룹/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 컨테이너 그룹에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 컨테이너 그룹에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **컨테이너그룹/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 컨테이너 그룹에 사용 가능한 메트릭을 가져옵니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷이 삭제됨을 Microsoft.ContainerInstance에 알립니다. |
> |  | **위치/캐시이미지** |  |
> | 작업 | Microsoft.ContainerInstance/locations/cachedImages/read | Azure 지역의 구독에 대해 캐시된 이미지를 가져옵니다. |
> |  | **위치/기능** |  |
> | 작업 | Microsoft.ContainerInstance/locations/capabilities/read | Azure 지역에 대한 기능을 가져옵니다. |
> |  | **위치/작업** |  |
> | 작업 | 마이크로소프트.컨테이너 인스턴스/위치/작업/읽기 | Azure 컨테이너 인스턴스 서비스에 대한 작업을 나열합니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.ContainerInstance/locations/usages/read | 특정 Azure 지역의 사용량을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.컨테이너인스턴스/작업/읽기 | Azure 컨테이너 인스턴스 서비스에 대한 작업을 나열합니다. |
> |  | **서비스 연결 링크** |  |
> | 작업 | Microsoft.컨테이너 인스턴스/서비스 연결 링크/삭제 | 서브넷에서 Azure 컨테이너 인스턴스 리소스 공급자가 만든 서비스 연결 링크를 삭제합니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

Azure 서비스: [컨테이너 레지스트리](../container-registry/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ContainerRegistry/register/action | 컨테이너 레지스트리 리소스 공급자에 대한 구독을 등록하고 컨테이너 레지스트리를 만들도록 설정합니다. |
> |  | **확인이름가용성** |  |
> | 작업 | Microsoft.ContainerRegistry/checkNameAvailability/read | 컨테이너 레지스트리 이름을 사용할 수 있는지 확인합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷을 삭제 중임을 Microsoft.ContainerRegistry에 알립니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.ContainerRegistry/locations/operationResults/read | 비동기 작업 결과를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ContainerRegistry/operations/read | 사용 가능한 모든 Azure Container Registry REST API 작업을 나열합니다. |
> |  | **registries** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/read | 지정된 컨테이너 레지스트리의 속성을 가져오거나 지정된 리소스 그룹 또는 구독에 속한 모든 컨테이너 레지스트리를 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/delete | 컨테이너 레지스트리를 삭제합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/listCredentials/action | 지정된 컨테이너 레지스트리에 대한 로그인 자격 증명을 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 지정된 컨테이너 레지스트리에 대한 로그인 자격 증명 중 하나를 다시 생성합니다. |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/생성 자격 증명/작업 | 지정된 컨테이너 레지스트리의 토큰에 대한 키를 생성합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/importImage/action | 지정된 매개 변수를 사용하여 이미지를 컨테이너 레지스트리에 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 사용자가 원본을 업로드할 수 있는 업로드 위치를 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/queueBuild/action | 요청 매개 변수를 기반으로 새 빌드를 만들고 빌드 큐에 추가합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 컨테이너 레지스트리에 대한 원본 업로드 URL 위치를 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/scheduleRun/action | 컨테이너 레지스트리에 대한 실행을 예약합니다. |
> |  | **레지스트리/에이전트 풀** |  |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/에이전트 풀/목록큐 상태/작업 | 컨테이너 레지스트리에 대한 에이전트풀의 모든 큐 상태를 나열합니다. |
> |  | **레지스트리/아티팩트** |  |
> | 작업 | Microsoft.Container레지스트리/레지스트리/아티팩트/삭제 | 컨테이너 레지스트리에서 아티팩트를 삭제합니다. |
> |  | **registries/builds** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/builds/read | 지정된 빌드 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 빌드를 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/builds/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 빌드를 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 빌드 로그를 다운로드할 링크를 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/builds/cancel/action | 기존 빌드를 취소합니다. |
> |  | **레지스트리/빌드 작업** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/read | 지정된 빌드 작업의 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 빌드 작업을 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 빌드 작업을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/delete | 컨테이너 레지스트리에서 빌드 작업을 삭제합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 빌드 작업에 대한 원본 제어 속성을 나열합니다. |
> |  | **registries/buildTasks/steps** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 지정된 빌드 단계의 속성을 가져오거나 지정된 빌드 작업의 모든 빌드 단계를 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 지정된 매개 변수를 사용하여 빌드 작업에 대한 빌드 단계를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 빌드 작업에서 빌드 단계를 삭제합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 비밀 인수를 포함하여 빌드 단계에 대한 빌드 인수를 나열합니다. |
> |  | **registries/eventGridFilters** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 지정된 Event Grid 필터의 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 Event Grid 필터를 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 Event Grid 필터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 컨테이너 레지스트리에서 Event Grid 필터를 삭제합니다. |
> |  | **레지스트리/목록정책** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/listPolicies/read | 지정된 컨테이너 레지스트리에 대한 정책을 나열합니다. |
> |  | **레지스트리/목록사용** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/listUsages/read | 지정된 컨테이너 레지스트리에 대한 할당량 사용량을 나열합니다. |
> |  | **레지스트리/메타데이터** |  |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/메타데이터/읽기 | 컨테이너 레지스트리에 대한 특정 리포지토리의 메타데이터 가져옵니다. |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/메타데이터/쓰기 | 컨테이너 레지스트리에 대한 리포지토리의 메타데이터 업데이트 |
> |  | **레지스트리/작업상태** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/operationStatuses/read | 레지스트리 비동기 작업 상태를 가져옵니다. |
> |  | **레지스트리/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/개인Endpoint연결프록시/유효성 검사/작업 | 개인 엔드포인트 연결 프록시 유효성 검사(NRP만) |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/개인엔드포인트연결프록시/읽기 | 개인 엔드포인트 연결 프록시(NRP만) |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/개인Endpoint연결 프록시/쓰기 | 개인 끝점 연결 프록시 만들기(NRP만) |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/개인Endpoint연결 프록시/삭제 | 개인 엔드포인트 연결 프록시 삭제(NRP만) |
> |  | **레지스트리/개인엔드포인트커넥션프록시스/운영상태** |  |
> | 작업 | 마이크로 소프트.컨테이너 레지스트리 / 레지스트리 / 개인엔드 포인트 연결 Proxies / 작업상태 / 읽기 | 개인 엔드포인트 연결 프록시 비동기 작업 상태 가져옵니다. |
> |  | **레지스트리/풀** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> |  | **레지스트리/푸시** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/push/write | 컨테이너 레지스트리에 이미지를 푸시하거나 작성합니다. |
> |  | **레지스트리/검역** |  |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/격리/읽기 | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/격리/쓰기 | 격리된 이미지의 격리 상태를 작성/수정합니다. |
> |  | **registries/replications** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/replications/read | 지정된 복제 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 복제를 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/replications/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 복제를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/replications/delete | 컨테이너 레지스트리에서 복제를 삭제합니다. |
> |  | **레지스트리/복제/작업상태** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 복제 비동기 작업 상태를 가져옵니다. |
> |  | **registries/runs** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/runs/read | 컨테이너 레지스트리 또는 목록 실행에 대한 실행 속성을 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/runs/write | 실행을 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 실행을 위한 로그 SAS URL을 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/runs/cancel/action | 기존 실행을 취소합니다. |
> |  | **레지스트리/범위 맵** |  |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/범위 맵/읽기 | 지정된 범위 맵의 속성을 가져옵니다 또는 지정된 컨테이너 레지스트리에 대 한 모든 범위 맵을 나열 합니다. |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/범위 맵/쓰기 | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 범위 맵을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/스코프맵/삭제 | 컨테이너 레지스트리에서 범위 맵을 삭제합니다. |
> |  | **레지스트리/범위맵/작업상태** |  |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/스코프맵/작업상태/읽기 | 범위 맵 비동기 작업 상태를 가져옵니다. |
> |  | **레지스트리/서명** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/sign/write | 컨테이너 레지스트리에 대한 콘텐츠 신뢰 메타데이터를 푸시/풀합니다. |
> |  | **레지스트리/작업 실행** |  |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/작업 실행/목록 세부 정보/작업 | 컨테이너 레지스트리에 대한 작업 실행의 모든 세부 정보를 나열합니다. |
> |  | **registries/tasks** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/tasks/read | 컨테이너 레지스트리에 대한 작업을 가져오거나 모든 작업을 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/tasks/write | 컨테이너 레지스트리에 대한 작업을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/tasks/delete | 컨테이너 레지스트리에 대한 작업을 삭제합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 컨테이너 레지스트리에 대한 작업의 모든 세부 정보를 나열합니다. |
> |  | **레지스트리/토큰** |  |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/토큰/읽기 | 지정된 토큰의 속성을 가져옵니다 또는 지정된 컨테이너 레지스트리에 대 한 모든 토큰을 나열 합니다. |
> | 작업 | 마이크로소프트.컨테이너 레지스트리/레지스트리/토큰/쓰기 | 지정된 매개 변수를 사용 하 고 컨테이너 레지스트리에 대 한 토큰을 만듭니다 또는 업데이트 합니다. |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/토큰/삭제 | 컨테이너 레지스트리에서 토큰을 삭제합니다. |
> |  | **레지스트리/토큰/작업상태** |  |
> | 작업 | Microsoft.컨테이너 레지스트리/레지스트리/토큰/작업상태/읽기 | 토큰 비동기 작업 상태를 가져옵니다. |
> |  | **registries/updatePolicies** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/updatePolicies/write | 지정된 컨테이너 레지스트리에 대한 정책을 업데이트합니다. |
> |  | **registries/webhooks** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/read | 지정된 웹후크 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 웹후크를 나열합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 웹후크를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/delete | 컨테이너 레지스트리에서 웹후크를 삭제합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 웹후크에 대한 서비스 URI 및 사용자 지정 헤더의 구성을 가져옵니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 웹후크로 보낼 ping 이벤트를 트리거합니다. |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 지정된 웹후크에 대한 최신 이벤트를 나열합니다. |
> |  | **레지스트리/웹후크/운영상태** |  |
> | 작업 | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 웹후크 비동기 작업 상태를 가져옵니다. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

Azure 서비스: [Azure Kubernetes 서비스(AKS)](../aks/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ContainerService/register/action | Microsoft.ContainerService 리소스 공급자에 구독을 등록합니다. |
> | 작업 | Microsoft.ContainerService/unregister/action | Microsoft.ContainerService 리소스 공급자에 구독을 등록 취소합니다. |
> |  | **컨테이너 서비스** |  |
> | 작업 | Microsoft.ContainerService/containerServices/read | 컨테이너 서비스를 가져옵니다. |
> | 작업 | Microsoft.ContainerService/containerServices/write | 새 컨테이너 서비스를 만들거나 기존 컨테이너 서비스를 업데이트합니다. |
> | 작업 | Microsoft.ContainerService/containerServices/delete | 컨테이너 서비스를 삭제합니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.ContainerService/locations/operationresults/read | 비동기 작업 결과의 상태를 가져옵니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.ContainerService/locations/operations/read | 비동기 작업의 상태를 가져옵니다. |
> |  | **위치/오케스트레이터** |  |
> | 작업 | Microsoft.ContainerService/locations/orchestrators/read | 지원되는 오케스트레이터를 나열합니다. |
> |  | **관리되는 클러스터** |  |
> | 작업 | Microsoft.ContainerService/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | 작업 | Microsoft.ContainerService/managedClusters/write | 새 관리되는 클러스터를 만들거나 기존 관리되는 클러스터를 업데이트합니다. |
> | 작업 | Microsoft.ContainerService/managedClusters/delete | 관리되는 클러스터를 삭제합니다. |
> | 작업 | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 관리되는 클러스터의 clusterAdmin 자격 증명을 나열합니다. |
> | 작업 | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/목록클러스터 모니터링사용자 자격 증명/작업 | 관리되는 클러스터의 클러스터모니터링사용자 자격 증명 나열 |
> | 작업 | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 관리형 클러스터의 서비스 주체 프로필을 다시 설정합니다. |
> | 작업 | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 관리형 클러스터의 AAD 프로필을 다시 설정합니다. |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/회전클러스터 인증서/작업 | 관리되는 클러스터의 인증서 회전 |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/개인Endpoint연결승인/작업 | 사용자가 개인 끝점 연결을 승인할 수 있는지 확인합니다. |
> |  | **관리되는 클러스터/액세스 프로필** |  |
> | 작업 | Microsoft.ContainerService/managedClusters/accessProfiles/read | 역할 이름별로 관리되는 클러스터 액세스 프로필을 가져옵니다. |
> | 작업 | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 자격 증명 나열을 사용하여 역할 이름별로 관리되는 클러스터 액세스 프로필을 가져옵니다. |
> |  | **관리되는 클러스터/에이전트풀** |  |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/에이전트풀/읽기 | 에이전트 풀을 가져옵니다. |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/에이전트풀/쓰기 | 새 에이전트 풀을 만들거나 기존 에이전트 풀을 업데이트합니다. |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/에이전트풀/삭제 | 에이전트 풀 삭제 |
> |  | **관리클러스터/에이전트풀/업그레이드프로필** |  |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/에이전트풀/업그레이드프로필/읽기 | 에이전트 풀의 업그레이드 프로필 을 가져옵니다. |
> |  | **관리클러스터/사용 가능한에이전트풀버전** |  |
> | 작업 | 마이크로소프트.컨테이너 서비스/관리 클러스터/사용 가능한 에이전트풀버전/읽기 | 클러스터의 사용 가능한 에이전트 풀 버전을 가져옵니다. |
> |  | **관리되는 클러스터/검출기** |  |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/감지기/읽기 | 관리되는 클러스터 검출기 받기 |
> |  | **관리클러스터/진단상태** |  |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/진단상태/읽기 | 클러스터의 진단 상태를 가져옵니다. |
> |  | **관리되는 클러스터/업그레이드프로필** |  |
> | 작업 | Microsoft.컨테이너 서비스/관리 클러스터/업그레이드프로필/읽기 | 클러스터의 업그레이드 프로필을 가져옵니다. |
> |  | **오픈 시프트 클러스터** |  |
> | 작업 | Microsoft.ContainerService/openShiftClusters/read | 오픈 시프트 클러스터 받기 |
> | 작업 | Microsoft.ContainerService/openShiftClusters/write | 새 Open Shift 클러스터를 만들거나 기존 Open Shift 클러스터를 업데이트합니다. |
> | 작업 | Microsoft.ContainerService/openShiftClusters/delete | 오픈 시프트 클러스터 삭제 |
> |  | **오픈 시프트관리 클러스터** |  |
> | 작업 | Microsoft.ContainerService/openShiftManagedClusters/read | 오픈 시프트 관리 클러스터 받기 |
> | 작업 | Microsoft.ContainerService/openShiftManagedClusters/write | 새 Open Shift 관리형 클러스터를 만들거나 기존 Open Shift 관리 클러스터를 업데이트합니다. |
> | 작업 | Microsoft.ContainerService/openShiftManagedClusters/delete | 오픈 시프트 관리 클러스터 삭제 |
> |  | **작업** |  |
> | 작업 | Microsoft.ContainerService/operations/read | Microsoft.ContainerService 리소스 공급자에서 사용 가능한 작업을 나열합니다. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

Azure 서비스: [비용 관리](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.CostManagement/query/action | 범위별 사용량 현황 데이터를 쿼리합니다. |
> | 작업 | Microsoft.CostManagement/reports/action | 범위별 사용량 현황 데이터에 관한 보고서를 예약합니다. |
> | 작업 | Microsoft.CostManagement/exports/action | 지정된 내보내기를 실행합니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/레지스터/액션 | 구독을 통해 Microsoft.CostManagement의 범위에 대한 작업을 등록합니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/뷰/액션 | 뷰를 만듭니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/예측/작업 | 범위별로 사용 데이터를 예측합니다. |
> |  | **경고** |  |
> | 작업 | 마이크로 소프트.CostManagement / 경고 / 쓰기 | 경고를 업데이트합니다. |
> |  | **클라우드 커넥터** |  |
> | 작업 | 마이크로소프트.코스트 매니지먼트/클라우드 커넥터/읽기 | 인증된 사용자를 위한 클라우드 커넥터를 나열합니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/클라우드 커넥터/쓰기 | 지정된 클라우드커넥터를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/클라우드 커넥터/삭제 | 지정된 클라우드커넥터를 삭제합니다. |
> |  | **차원** |  |
> | 작업 | Microsoft.CostManagement/dimensions/read | 범위별 지원되는 모든 차원을 나열합니다. |
> |  | **수출** |  |
> | 작업 | Microsoft.CostManagement/exports/read | 범위에 따라 내보내기를 나열합니다. |
> | 작업 | Microsoft.CostManagement/exports/write | 지정된 내보내기를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.CostManagement/exports/delete | 지정된 내보내기를 삭제합니다. |
> | 작업 | Microsoft.CostManagement/내보내기/실행/작업 | 내보내기를 실행합니다. |
> |  | **외부 결제 계정** |  |
> | 작업 | 마이크로소프트.비용 관리/외부 청구 계정/읽기 | 인증된 사용자에 대한 외부 결제 계정을 나열합니다. |
> | 작업 | Microsoft.CostManagement/외부 결제 계정/쿼리/작업 | 외부 BillingAccounts에 대한 사용 데이터를 쿼리합니다. |
> | 작업 | Microsoft.CostManagement/외부 결제 계정/예측/작업 | 외부 결제 계정에 대한 사용 데이터를 예측합니다. |
> |  | **외부 결제 계정/차원** |  |
> | 작업 | Microsoft.CostManagement/외부 결제 계정/차원/읽기 | 외부 BillingAccounts에 대해 지원되는 모든 차원을 나열합니다. |
> |  | **외부 결제 계정/외부 구독** |  |
> | 작업 | Microsoft.CostManagement/외부 결제 계정/외부 구독/읽기 | 인증된 사용자에 대한 외부 결제 계정 내에 외부 구독을 나열합니다. |
> |  | **외부 결제 계정/예측** |  |
> | 작업 | Microsoft.CostManagement/외부 결제 계정/예측/읽기 | 외부 결제 계정에 대한 사용 데이터를 예측합니다. |
> |  | **외부 결제 계정/쿼리** |  |
> | 작업 | Microsoft.CostManagement/외부 결제 계정/쿼리/읽기 | 외부 BillingAccounts에 대한 사용 데이터를 쿼리합니다. |
> |  | **외부 구독** |  |
> | 작업 | 마이크로 소프트.CostManagement / 외부 구독 / 읽기 | 인증된 사용자에 대한 외부 구독을 나열합니다. |
> | 작업 | 마이크로 소프트.CostManagement / 외부 구독 / 쓰기 | 외부 구독의 관련 관리 그룹 업데이트 |
> | 작업 | Microsoft.CostManagement/외부 구독/쿼리/작업 | 외부 구독에 대한 사용 데이터를 쿼리합니다. |
> | 작업 | Microsoft.CostManagement/외부 구독/예측/작업 | 외부 결제 계정에 대한 사용 데이터를 예측합니다. |
> |  | **외부 구독/치수** |  |
> | 작업 | Microsoft.CostManagement/외부 구독/차원/읽기 | 외부 구독에 대해 지원되는 모든 차원을 나열합니다. |
> |  | **외부 구독/예측** |  |
> | 작업 | Microsoft.CostManagement/외부 구독/예측/읽기 | 외부 결제 계정에 대한 사용 데이터를 예측합니다. |
> |  | **외부 구독/쿼리** |  |
> | 작업 | Microsoft.CostManagement/외부 구독/쿼리/읽기 | 외부 구독에 대한 사용 데이터를 쿼리합니다. |
> |  | **예측** |  |
> | 작업 | 마이크로소프트.코스트 매니지먼트/예측/읽기 | 범위별로 사용 데이터를 예측합니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.비용 관리/운영/읽기 | Microsoft.CostManagement 리소스 공급자가 지원하는 모든 작업을 나열합니다. |
> |  | **쿼리** |  |
> | 작업 | Microsoft.CostManagement/query/read | 범위별 사용량 현황 데이터를 쿼리합니다. |
> |  | **보고서** |  |
> | 작업 | Microsoft.CostManagement/reports/read | 범위별 사용량 현황 데이터에 관한 보고서를 예약합니다. |
> |  | **거주자** |  |
> | 작업 | Microsoft.CostManagement/테넌자/레지스터/작업 | 테넌트에 의한 Microsoft.CostManagement의 범위에 대한 작업을 등록합니다. |
> |  | **레이아웃** |  |
> | 작업 | 마이크로소프트.코스트 매니지먼트/뷰/읽기 | 저장된 모든 뷰를 나열합니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/뷰/삭제 | 저장된 뷰를 삭제합니다. |
> | 작업 | 마이크로소프트.코스트 매니지먼트/뷰/쓰기 | 뷰를 업데이트합니다. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

Azure 서비스: [Azure 데이터 상자](../databox-family/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DataBox/register/action | 공급자 Microsoft.Databox를 등록합니다. |
> | 작업 | 마이크로소프트.데이터 박스/등록 취소/작업 | 등록 취소 공급자 Microsoft.Databox |
> |  | **작업** |  |
> | 작업 | Microsoft.DataBox/jobs/cancel/action | 진행 중인 주문을 취소합니다. |
> | 작업 | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 반송 배송을 위한 픽업을 예약할 수 있습니다. |
> | 작업 | Microsoft.DataBox/jobs/read | 주문을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.DataBox/jobs/delete | 주문을 삭제합니다. |
> | 작업 | Microsoft.DataBox/jobs/write | 주문을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataBox/jobs/listCredentials/action | 주문과 관련된 암호화되지 않은 자격 증명을 나열합니다. |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.데이터 박스/위치/유효성 검사입력/작업 | 이 메서드는 모든 유형의 유효성 검사를 수행합니다. |
> | 작업 | Microsoft.DataBox/locations/validateAddress/action | 배송 주소의 유효성을 검사하고, 있는 경우, 대체 주소를 제공합니다. |
> | 작업 | Microsoft.DataBox/locations/availableSkus/action | 이 메서드는 사용할 수 있는 SKU 목록을 반환합니다. |
> | 작업 | 마이크로소프트.데이터 박스/위치/지역구성/작업 | 이 메서드는 지역에 대 한 구성을 반환 합니다. |
> |  | **위치/사용 가능스쿠스** |  |
> | 작업 | 마이크로소프트.데이터 박스/위치/사용 가능한Skus/읽기 | 목록 또는 사용 가능한 스쿠스를 얻을 |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.DataBox/locations/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.데이터 박스/작업/읽기 | 작업 목록 또는 받기 |
> |  | **subscriptions/resourceGroups** |  |
> | 작업 | 마이크로소프트.데이터 박스/구독/리소스 그룹/이동 리소스/작업 | 이 메서드는 리소스 이동을 수행합니다. |
> | 작업 | 마이크로소프트.데이터 박스/구독/리소스 그룹/유효성 검사MoveResources/작업 | 이 메서드는 리소스 이동이 허용되는지 여부를 확인합니다. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

Azure 서비스: [Azure 데이터 상자 가장자리](../databox-online/data-box-edge-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **데이터박스에지디바이스** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 디바이스 등록에 대한 인증서를 업로드 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Data Box Edge 디바이스를 만들거나 업데이트 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge 디바이스를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Data Box Edge 디바이스를 삭제 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge 디바이스를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge 디바이스를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Data Box Edge 디바이스를 만들거나 업데이트 |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/getExtended정보/액션 | 리소스 확장 정보를 검색 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 업데이트를 검색 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 디바이스에서 업데이트를 다운로드 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 디바이스에 업데이트를 설치 |
> |  | **데이터박스에지디바이스/알림** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 경고를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 경고를 나열하거나 가져오기 |
> |  | **데이터박스에지디바이스/대역폭일정** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 대역폭 일정을 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 대역폭 일정을 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 대역폭 일정을 만들거나 업데이트 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 대역폭 일정을 삭제 |
> |  | **데이터박스에지디바이스/대역폭일정/운영결과** |  |
> | 작업 | 마이크로 소프트.DataBoxEdge / 데이터박스에지디바이스/대역폭일정/작업결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/채용정보** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 작업을 나열하거나 가져오기 |
> |  | **데이터박스에지디바이스/네트워크설정** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 디바이스 네트워크 설정을 나열하거나 가져오기 |
> |  | **데이터박스에지디바이스/노드** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/노드/읽기 | 노드를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/운영결과** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스엣지디바이스/운영결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/운영상태** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/운영상태/읽기 | 작업 상태를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/주문** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/주문/읽기 | 목록 또는 주문을 가져옵니다. |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/주문/읽기 | 목록 또는 주문을 가져옵니다. |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/주문/쓰기 | 주문을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/주문/삭제 | 주문 삭제 |
> |  | **데이터박스에지디바이스/주문/운영결과** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/주문/작업결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/역할** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 역할 목록 또는 가져옵니다. |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 역할 목록 또는 가져옵니다. |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 역할을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 역할 삭제 |
> |  | **데이터박스에지디바이스/역할/운영결과** |  |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 역할 / 작업결과 / 읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/보안설정** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 보안 설정을 업데이트 |
> |  | **데이터박스에지디바이스/보안설정/운영결과** |  |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 보안설정 / 운영결과 / 읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/공유** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 공유를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 공유를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 공유를 만들거나 업데이트 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 클라우드의 데이터로 공유 메타데이터 새로 고침 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 공유를 삭제 |
> |  | **데이터박스에지디바이스/공유/운영결과** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/공유/운영결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/스토리지계정자격증명** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 스토리지 계정 자격 증명을 만들거나 업데이트 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 스토리지 계정 자격 증명을 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 스토리지 계정 자격 증명을 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 스토리지 계정 자격 증명을 삭제 |
> |  | **데이터박스에지디바이스/스토리지계정자격증명/운영결과** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/스토리지계정자격증명/운영결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/스토리지계정** |  |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 스토리지 계정 / 읽기 | 저장소 계정을 나열하거나 가져옵니다. |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 스토리지 계정 / 읽기 | 저장소 계정을 나열하거나 가져옵니다. |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/스토리지계정/쓰기 | 저장소 계정을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/스토리지계정/삭제 | 저장소 계정 삭제 |
> |  | **데이터박스에지디바이스/스토리지계정/컨테이너** |  |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 스토리지계정 / 컨테이너 / 읽기 | 컨테이너 를 나열하거나 가져옵니다. |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 스토리지계정 / 컨테이너 / 읽기 | 컨테이너 를 나열하거나 가져옵니다. |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 스토리지계정 / 컨테이너 / 쓰기 | 컨테이너를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/스토리지계정/컨테이너/삭제 | 컨테이너 삭제 |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/스토리지계정/컨테이너/새로 고침/액션 | 클라우드의 데이터로 컨테이너 메타데이터 새로 고침 |
> |  | **데이터박스에지디바이스/스토리지계정/컨테이너/운영결과** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스엣지디바이스/스토리지계정/컨테이너/작업결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/스토리지계정/운영결과** |  |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/스토리지계정/운영결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/트리거** |  |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 트리거 / 읽기 | 트리거 목록 또는 트리거 가져옵니다. |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 트리거 / 읽기 | 트리거 목록 또는 트리거 가져옵니다. |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 트리거 / 쓰기 | 트리거 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.데이터박스에지/데이터박스에지디바이스/트리거/삭제 | 트리거 삭제 |
> |  | **데이터박스에지디바이스/트리거/작동결과** |  |
> | 작업 | 마이크로 소프트.데이터 박스 에지 / 데이터박스에지장치 / 트리거 / 작업결과 / 읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **데이터박스에지디바이스/업데이트요약** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 업데이트 요약을 나열하거나 가져오기 |
> |  | **데이터박스에지디바이스/사용자** |  |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 공유 사용자를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 공유 사용자를 나열하거나 가져오기 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 공유 사용자를 만들거나 업데이트 |
> | 작업 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 공유 사용자를 삭제 |
> |  | **데이터박스에지디바이스/사용자/작업결과** |  |
> | 작업 | 마이크로 소프트.DataBoxEdge / 데이터박스에지디바이스/사용자/작업결과/읽기 | 작업 결과를 나열하거나 가져옵니다. |
> |  | **Sku** |  |
> | 작업 | 마이크로소프트.데이터 박스 에지 / 스쿠스 / 읽기 | SUS 를 나열하거나 가져옵니다. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

Azure 서비스: [Azure 데이터 브릭](../azure-databricks/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Databricks/register/action | Databricks에 등록합니다. |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.데이터브릭/위치/getNetwork정책/작업 | NRP에서 사용하는 위치에 따라 서브넷에 대한 네트워크 의도 경찰 받기 |
> |  | **위치/작업 상태** |  |
> | 작업 | 마이크로 소프트.데이터 브릭 / 위치 / 작업 상태 / 읽기 | 리소스의 작업 상태를 읽습니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.데이터브릭/운영/읽기 | 작업 목록을 가져옵니다. |
> |  | **workspaces** |  |
> | 작업 | Microsoft.Databricks/workspaces/read | Databricks 작업 영역의 목록을 검색합니다. |
> | 작업 | Microsoft.Databricks/workspaces/write | Databricks 작업 영역을 만듭니다. |
> | 작업 | Microsoft.Databricks/workspaces/delete | Databricks 작업 영역을 제거합니다. |
> | 작업 | Microsoft.Databricks/작업 영역/새로 고침권한/작업 | 작업 영역에 대한 권한 새로 고침 |
> | 작업 | 마이크로소프트.데이터브릭/작업 영역/업데이트Deny할당/작업 | 작업 영역의 관리되는 리소스 그룹에 대한 작업이 아닌 거부 할당 업데이트 |
> | 작업 | Microsoft.데이터브릭/작업 영역/새로 고침작업 영역/작업 | URL과 같은 새로운 세부 정보로 작업 영역 새로 고침 |
> |  | **작업 영역/db워크 스페이스** |  |
> | 작업 | 마이크로 소프트.데이터 브릭 / 작업 공간 / dbWorkspaces / 쓰기 | 데이터 브릭 작업 영역 초기화(내부 전용) |
> |  | **작업 영역/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Databricks 작업 영역에 사용할 수 있는 진단 설정을 구성합니다. |
> | 작업 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> |  | **작업 영역/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Databricks 작업 영역에 사용할 수 있는 로그 정의를 가져옵니다. |
> |  | **작업 영역/저장소암호화** |  |
> | 작업 | 마이크로 소프트.Databricks / 작업 영역 / 스토리지암호화 / 쓰기 | 고객이 Databricks 작업 영역의 관리 되는 저장소 계정에 키 암호화를 관리 할 수 있습니다. |
> | 작업 | 마이크로 소프트.Databricks / 작업 영역 / 스토리지암호화 / 삭제 | Databricks 작업 영역의 관리되는 저장소 계정에서 고객 관리 키 암호화를 사용하지 않도록 설정합니다. |
> |  | **workspaces/virtualNetworkPeerings** |  |
> | 작업 | 마이크로 소프트.데이터 브릭 / 작업 영역 / 가상 네트워크 피어링 / 읽기 | 가상 네트워크 피어링을 가져옵니다. |
> | 작업 | 마이크로 소프트.데이터 브릭 / 작업 영역 / 가상 네트워크 피어링 / 쓰기 | 가상 네트워크 피어링 추가 또는 수정 |
> | 작업 | 마이크로 소프트.Databricks / 작업 영역 / 가상 네트워크 피어링 / 삭제 | 가상 네트워크 피어링을 삭제합니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

Azure 서비스: [데이터 카탈로그](../data-catalog/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DataCatalog/register/action | 데이터 카탈로그 리소스 공급자에 대한 구독 등록 |
> | 작업 | Microsoft.DataCatalog/unregister/action | 데이터 카탈로그 리소스 공급자에 대한 구독 등록 취소 |
> |  | **catalogs** |  |
> | 작업 | Microsoft.DataCatalog/catalogs/read | 데이터 카탈로그 리소스 공급자를 위한 카탈로그 리소스를 읽습니다. |
> | 작업 | Microsoft.DataCatalog/catalogs/write | 데이터 카탈로그 리소스 공급자를 위한 카탈로그 리소스를 작성합니다. |
> | 작업 | Microsoft.DataCatalog/catalogs/delete | 데이터 카탈로그 리소스 공급자에 대한 카탈로그 리소스를 삭제합니다. |
> |  | **확인이름가용성** |  |
> | 작업 | 마이크로소프트.데이터 카탈로그/체크네임가용성/읽기 | 데이터 카탈로그 리소스 공급자의 카탈로그 이름 가용성을 확인합니다. |
> |  | **데이터 카탈로그** |  |
> | 작업 | Microsoft.데이터 카탈로그/데이터 카탈로그/읽기 | 데이터 카탈로그 리소스 공급자에 대한 데이터 카탈로그 리소스를 읽습니다. |
> | 작업 | Microsoft.데이터 카탈로그/데이터 카탈로그/쓰기 | 데이터 카탈로그 리소스 공급자를 위한 DataCatalog 리소스를 작성합니다. |
> | 작업 | Microsoft.DataCatalog/데이터 카탈로그/삭제 | 데이터 카탈로그 리소스 공급자에 대한 데이터 카탈로그 리소스 를 삭제합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.DataCatalog/operations/read | 데이터 카탈로그 리소스 공급자에서 사용 가능한 모든 작업을 읽습니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

Azure 서비스: [데이터 팩터리](../data-factory/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DataFactory/register/action | Data Factory 리소스 공급자에 대한 구독을 등록합니다. |
> | 작업 | Microsoft.DataFactory/unregister/action | Data Factory 리소스 공급자에 대한 구독을 등록 취소합니다. |
> |  | **체크azuredatafactoryname가용성** |  |
> | 작업 | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Data Factory 이름을 사용할 수 있는지 확인합니다. |
> |  | **datafactories** |  |
> | 작업 | Microsoft.DataFactory/datafactories/read | Data Factory를 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/write | Data Factory를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/delete | Data Factory를 삭제합니다. |
> |  | **데이터 팩토리/활동창** |  |
> | 작업 | Microsoft.DataFactory/datafactories/activitywindows/read | 지정된 매개 변수를 사용하여 Data Factory의 활동 창을 읽습니다. |
> |  | **데이터 팩토리/데이터 파이프라인** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/read | 모든 파이프라인을 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/delete | 모든 파이프라인을 삭제합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 모든 파이프라인을 일시 중지합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 모든 파이프라인을 다시 시작합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/update/action | 모든 파이프라인을 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/write | 모든 파이프라인을 만들거나 업데이트합니다. |
> |  | **데이터 팩토리/데이터 파이프라인/활동/활동창** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 지정된 매개 변수를 사용하여 파이프라인 활동에 대한 활동 창을 읽습니다. |
> |  | **데이터 팩토리/데이터 파이프라인/활동창** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 지정된 매개 변수를 사용하여 파이프라인에 대한 활동 창을 읽습니다. |
> |  | **데이터 팩토리/데이터 세트** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/read | 모든 데이터 세트를 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/delete | 모든 데이터 세트를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/write | 모든 데이터 세트를 만들거나 업데이트합니다. |
> |  | **데이터 팩토리/데이터 세트/활동창** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 지정된 매개 변수를 사용하여 데이터 세트에 대한 활동 창을 읽습니다. |
> |  | **데이터 팩토리/데이터 세트/슬라이스런** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 지정된 시작 시간을 사용하여 지정된 데이터 세트에 대한 데이터 조각 실행을 읽습니다. |
> |  | **데이터 팩토리/데이터 세트/슬라이스** |  |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/slices/read | 지정된 기간의 데이터 조각을 가져옵니다. |
> | 작업 | Microsoft.DataFactory/datafactories/datasets/slices/write | 데이터 조각의 상태를 업데이트합니다. |
> |  | **데이터 팩토리/게이트웨이** |  |
> | 작업 | Microsoft.DataFactory/datafactories/gateways/read | 모든 게이트웨이를 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/gateways/write | 모든 게이트웨이를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/gateways/delete | 모든 게이트웨이를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 모든 게이트웨이에 대한 연결 정보를 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 모든 게이트웨이에 대한 인증 키를 나열합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 모든 게이트웨이에 대한 인증 키를 다시 생성합니다. |
> |  | **데이터 팩토리/링크된 서비스** |  |
> | 작업 | Microsoft.DataFactory/datafactories/linkedServices/read | 모든 연결된 서비스를 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/linkedServices/delete | 모든 연결된 서비스를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/linkedServices/write | 모든 연결된 서비스를 만들거나 업데이트합니다. |
> |  | **데이터 팩토리/실행/로그정보** |  |
> | 작업 | Microsoft.DataFactory/datafactories/runs/loginfo/read | 로그가 포함된 Blob 컨테이너에 대한 SAS URI를 읽습니다. |
> |  | **데이터 팩토리/테이블** |  |
> | 작업 | Microsoft.DataFactory/datafactories/tables/read | 모든 데이터 세트를 읽습니다. |
> | 작업 | Microsoft.DataFactory/datafactories/tables/delete | 모든 데이터 세트를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/datafactories/tables/write | 모든 데이터 세트를 만들거나 업데이트합니다. |
> |  | **factories** |  |
> | 작업 | Microsoft.DataFactory/factories/read | 데이터 팩터리를 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/write | 데이터 팩터리를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/factories/delete | 데이터 팩터리를 삭제합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/생성데이터 흐름디버그세션/액션 | 데이터 흐름 디버그 세션을 만듭니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/스타트데이터플로우디버그세션/액션 | 데이터 흐름 디버그 세션을 시작합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/애드데이터흐름토디버그세션/액션 | 미리 보기를 위해 디버그 세션에 데이터 흐름을 추가합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/실행데이터흐름디버그명령/액션 | 데이터 흐름 디버그 명령을 실행합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/삭제 데이터 흐름디버그세션/액션 | 데이터 흐름 디버그 세션을 삭제합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/쿼리 데이터 흐름디버그세션/액션 | 데이터 흐름 디버그 세션을 쿼리합니다. |
> | 작업 | Microsoft.DataFactory/factories/cancelpipelinerun/action | 실행 ID로 지정된 파이프라인 실행을 취소합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/캔슬샌드박스파이프라인런/액션 | 파이프라인에 대한 디버그 실행을 취소합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/샌드박스파이프라인/액션 | 디버그 파이프라인 실행을 쿼리합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/쿼리 트리거/작업 | 트리거를 쿼리합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/getFeature값/작업 | 특정 위치에 대한 노출 제어 기능 값을 가져옵니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/겟데이터플레인액세스/액션 | ADF 데이터 평면 서비스에 대한 액세스를 가져옵니다. |
> | 작업 | Microsoft.DataFactory/factories/getGitHubAccessToken/action | GitHub 액세스를 토큰을 가져옵니다. |
> | 작업 | Microsoft.DataFactory/factories/querytriggerruns/action | 트리거 실행을 쿼리합니다. |
> | 작업 | Microsoft.DataFactory/factories/querypipelineruns/action | 파이프라인 실행을 쿼리합니다. |
> | 작업 | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 디버그 파이프라인 실행을 쿼리합니다. |
> |  | **공장/데이터 흐름** |  |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/데이터 흐름/읽기 | 데이터 흐름을 읽습니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/데이터 플로우/삭제 | 데이터 흐름을 삭제합니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/데이터 플로우/쓰기 | 데이터 흐름 생성 또는 업데이트 |
> |  | **팩토리/데이터 집합** |  |
> | 작업 | Microsoft.DataFactory/factories/datasets/read | 모든 데이터 세트를 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/datasets/delete | 모든 데이터 세트를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/factories/datasets/write | 모든 데이터 세트를 만들거나 업데이트합니다. |
> |  | **공장/디버그 파이프라인** |  |
> | 작업 | Microsoft.DataFactory/팩터리/디버그 파이프라인 실행/취소/작업 | 파이프라인에 대한 디버그 실행을 취소합니다. |
> |  | **팩토리/겟데이터플레인액세스** |  |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/겟데이터플레인액세스/읽기 | ADF 데이터 평면 서비스에 대한 액세스를 읽습니다. |
> |  | **공장/getFeature값** |  |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/getFeature값/읽기 | 특정 위치에 대한 노출 제어 기능 값을 읽습니다. |
> |  | **공장/통합 실행 시간** |  |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/read | 모든 Integration Runtime을 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/write | Integration Runtime을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/delete | 모든 Integration Runtime을 삭제합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/start/action | 모든 Integration Runtime을 시작합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 모든 Integration Runtime을 중지합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 지정된 Integration Runtime에 대한 자격 증명을 동기화합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 지정된 Integration Runtime을 업그레이드합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 지정된 Integration Runtime에 대한 인증 키를 다시 생성합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 지정된 Integration Runtime에서 연결된 Integration Runtime 참조를 제거합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 지정된 공유 Integration Runtime에 연결된 Integration Runtime 참조를 만듭니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 지정된 Integration Runtime에 대한 SSIS Integration Runtime 메타데이터를 가져옵니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 지정된 Integration Runtime에 대한 SSIS Integration Runtime 메타데이터를 새로 고칩니다. |
> |  | **공장/통합런타임/getconnectioninfo** |  |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Integration Runtime 연결 정보를 읽습니다. |
> |  | **공장/통합런타임/겟스테이션** |  |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integration Runtime 상태를 읽습니다. |
> |  | **공장/통합런타임/리스타우트키** |  |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 모든 Integration Runtime에 대한 인증 키를 나열합니다. |
> |  | **공장/통합런타임/모니터링데이터** |  |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 모든 Integration Runtime에 대한 모니터링 데이터를 가져옵니다. |
> |  | **공장/통합런타임/노드** |  |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 지정된 Integration Runtime에 대한 노드를 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 지정된 Integration Runtime에 대한 노드를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 자체 호스팅 Integration Runtime 노드를 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Integration Runtime의 지정된 노드에 대한 IP 주소를 반환합니다. |
> |  | **공장/링크된 서비스** |  |
> | 작업 | Microsoft.DataFactory/factories/linkedServices/read | 연결된 서비스를 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/linkedServices/delete | 연결된 서비스를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/factories/linkedServices/write | 연결된 서비스를 만들거나 업데이트합니다. |
> |  | **공장/운영결과** |  |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/운영결과/읽기 | 작업 결과를 가져옵니다. |
> |  | **공장/파이프라인** |  |
> | 작업 | Microsoft.DataFactory/factories/pipelineruns/read | 파이프라인 실행을 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 실행 ID로 지정된 파이프라인 실행을 취소합니다. |
> | 작업 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 지정된 파이프라인 실행 ID에 대한 활동 실행을 쿼리합니다. |
> |  | **공장/파이프라인/활동 실행** |  |
> | 작업 | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 지정된 파이프라인 실행 ID에 대한 활동 실행을 읽습니다. |
> |  | **팩터리/파이프라인 실행/쿼리 활동 실행** |  |
> | 작업 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 지정된 파이프라인 실행 ID에 대한 쿼리 활동 실행의 결과를 읽습니다. |
> |  | **공장/파이프라인** |  |
> | 작업 | Microsoft.DataFactory/factories/pipelines/read | 파이프라인을 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/pipelines/delete | 파이프라인을 삭제합니다. |
> | 작업 | Microsoft.DataFactory/factories/pipelines/write | 파이프라인을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/factories/pipelines/createrun/action | 파이프라인에 대한 실행을 만듭니다. |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/파이프라인/샌드박스/액션 | 파이프라인에 대한 디버그 실행 환경을 만듭니다. |
> |  | **공장/파이프라인/파이프라인 실행** |  |
> | 작업 | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 파이프라인 실행을 읽습니다. |
> |  | **공장/파이프라인/파이프라인/활동 실행/진행률** |  |
> | 작업 | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 활동 실행의 진행률을 가져옵니다. |
> |  | **공장/파이프라인/샌드박스** |  |
> | 작업 | Microsoft.DataFactory/공장/파이프라인/샌드박스/만들기/작업 | 파이프라인에 대한 디버그 실행 환경을 만듭니다. |
> | 작업 | Microsoft.DataFactory/팩토리/파이프라인/샌드박스/실행/작업 | 파이프라인에 대한 디버그 실행을 만듭니다. |
> |  | **팩터리/쿼리 파이프라인** |  |
> | 작업 | Microsoft.DataFactory/factories/querypipelineruns/read | 쿼리 파이프라인 실행의 결과를 읽습니다. |
> |  | **팩터리/쿼리 트리거런** |  |
> | 작업 | Microsoft.DataFactory/factories/querytriggerruns/read | 트리거 실행의 결과를 읽습니다. |
> |  | **공장/샌드박스파이프라인** |  |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/샌드박스파이프라인/읽기 | 파이프라인에 대한 디버그 실행 정보를 가져옵니다. |
> |  | **공장/샌드박스파이프라인/샌드박스액티비티런** |  |
> | 작업 | 마이크로소프트.데이터팩토리/팩토리/샌드박스파이프라인/샌드박스액티비티런스/읽기 | 활동에 대한 디버그 실행 정보를 가져옵니다. |
> |  | **공장/트리거런** |  |
> | 작업 | Microsoft.DataFactory/factories/triggerruns/read | 트리거 실행을 읽습니다. |
> |  | **공장/트리거** |  |
> | 작업 | Microsoft.DataFactory/factories/triggers/read | 모든 트리거를 읽습니다. |
> | 작업 | Microsoft.DataFactory/factories/triggers/write | 모든 트리거를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataFactory/factories/triggers/delete | 모든 트리거를 삭제합니다. |
> | 작업 | Microsoft.DataFactory/팩토리/트리거/구독 이벤트/작업 | 이벤트 구독. |
> | 작업 | Microsoft.DataFactory/팩토리/트리거/geteventsubscription상태/작업 | 이벤트 구독 상태입니다. |
> | 작업 | Microsoft.DataFactory/팩토리/트리거/수신 거부이벤트/작업 | 이벤트 구독 을 취소합니다. |
> | 작업 | Microsoft.DataFactory/factories/triggers/start/action | 모든 트리거를 시작합니다. |
> | 작업 | Microsoft.DataFactory/factories/triggers/stop/action | 모든 트리거를 중지합니다. |
> |  | **팩토리/트리거/트리거런** |  |
> | 작업 | Microsoft.DataFactory/factories/triggers/triggerruns/read | 트리거 실행을 읽습니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.DataFactory/locations/configureFactoryRepo/action | 팩터리에 대해 리포지토리를 구성합니다. |
> | 작업 | Microsoft.DataFactory/locations/getFeatureValue/action | 특정 위치에 대한 노출 제어 기능 값을 가져옵니다. |
> |  | **위치/getFeature값** |  |
> | 작업 | Microsoft.DataFactory/locations/getFeatureValue/read | 특정 위치에 대한 노출 제어 기능 값을 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.DataFactory/operations/read | Microsoft Data Factory 공급자의 모든 작업을 읽습니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

Azure 서비스: [데이터 레이크 분석](../data-lake-analytics/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DataLakeAnalytics/register/action | DataLakeAnalytics에 구독을 등록합니다. |
> |  | **계정** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/read | 기존 DataLakeAnalytics 계정에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics 계정을 삭제합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | DataLakeAnalytics 계정 간에 SystemMaxAnalyticsUnits를 전송합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다. |
> |  | **계정/계산 정책** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 컴퓨팅 정책에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | 컴퓨팅 정책을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | 컴퓨팅 정책을 삭제합니다. |
> |  | **accounts/dataLakeStoreAccounts** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다. |
> |  | **accounts/firewallRules** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 방화벽 규칙에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | 방화벽 규칙을 삭제합니다. |
> |  | **계정/운영결과** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/operationResults/read | DataLakeAnalytics 계정 작업의 결과를 가져옵니다. |
> |  | **accounts/storageAccounts** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | DataLakeAnalytics 계정과 연결된 Storage 계정에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다. |
> |  | **계정/저장소계정/컨테이너** |  |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | DataLakeAnalytics 계정과 연결된 Storage 계정의 컨테이너를 가져옵니다. |
> | 작업 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | DataLakeAnalytics 계정과 연결된 Storage 계정의 스토리지 컨테이너 SAS 토큰을 나열합니다. |
> |  | **계정/가상 네트워크규칙** |  |
> | 작업 | 마이크로소프트.데이터레이크애널리틱스/계정/가상네트워크규칙/읽기 | 가상 네트워크 규칙에 대한 정보를 가져옵니다. |
> | 작업 | 마이크로소프트.데이터레이크애널리틱스/계정/가상네트워크규칙/쓰기 | 가상 네트워크 규칙을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.DataLake분석/계정/가상네트워크규칙/삭제 | 가상 네트워크 규칙을 삭제합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | DataLakeAnalytics 계정 이름의 가용성을 확인합니다. |
> |  | **위치/기능** |  |
> | 작업 | Microsoft.DataLakeAnalytics/locations/capability/read | DataLakeAnalytics 사용과 관련된 구독의 기능 정보를 가져옵니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.DataLakeAnalytics/locations/operationResults/read | DataLakeAnalytics 계정 작업의 결과를 가져옵니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.DataLakeAnalytics/locations/usages/read | DataLakeAnalytics 사용과 관련된 구독의 할당량 사용량 정보를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.DataLakeAnalytics/operations/read | DataLakeAnalytics에서 사용 가능한 작업을 가져옵니다. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

Azure 서비스: [Azure 데이터 레이크 스토어](../storage/blobs/data-lake-storage-introduction.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DataLakeStore/register/action | DataLakeStore에 구독을 등록합니다. |
> |  | **계정** |  |
> | 작업 | Microsoft.DataLakeStore/accounts/read | 기존 DataLakeStore 계정에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/write | DataLakeStore 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/delete | DataLakeStore 계정을 삭제합니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/enableKeyVault/action | DataLakeStore 계정에 Keyvault를 사용하도록 설정합니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/Superuser/action | Microsoft.Authorization/roleAssignments/write가 부여되면 Data Lake Store에 대한 슈퍼 사용자 권한을 부여합니다. |
> |  | **accounts/eventGridFilters** |  |
> | 작업 | Microsoft.DataLakeStore/accounts/eventGridFilters/read | EventGrid 필터를 가져옵니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/eventGridFilters/write | EventGrid 필터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | EventGrid 필터를 삭제합니다. |
> |  | **accounts/firewallRules** |  |
> | 작업 | Microsoft.DataLakeStore/accounts/firewallRules/read | 방화벽 규칙에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/firewallRules/write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/firewallRules/delete | 방화벽 규칙을 삭제합니다. |
> |  | **계정/운영결과** |  |
> | 작업 | Microsoft.DataLakeStore/accounts/operationResults/read | DataLakeStore 계정 작업의 결과를 가져옵니다. |
> |  | **계정/공유** |  |
> | 작업 | 마이크로소프트.데이터레이크스토어/계정/공유/읽기 | 공유에 대한 정보를 가져옵니다. |
> | 작업 | 마이크로소프트.데이터레이크스토어/계정/공유/쓰기 | 공유를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.데이터레이크스토어/계정/공유/삭제 | 공유를 삭제합니다. |
> |  | **계정/신뢰할 수 있는 Id공급자** |  |
> | 작업 | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 신뢰할 수 있는 ID 공급자에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 신뢰할 수 있는 ID 공급자를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 신뢰할 수 있는 ID 공급자를 삭제합니다. |
> |  | **계정/가상 네트워크규칙** |  |
> | 작업 | 마이크로소프트.DataLake스토어/계정/가상네트워크규칙/읽기 | 가상 네트워크 규칙에 대한 정보를 가져옵니다. |
> | 작업 | 마이크로소프트.DataLakeStore/계정/가상네트워크규칙/쓰기 | 가상 네트워크 규칙을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.DataLakeStore/계정/가상네트워크규칙/삭제 | 가상 네트워크 규칙을 삭제합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.DataLakeStore/locations/checkNameAvailability/action | DataLakeStore 계정 이름의 가용성을 확인합니다. |
> |  | **위치/기능** |  |
> | 작업 | Microsoft.DataLakeStore/locations/capability/read | DataLakeStore 사용과 관련된 구독의 기능 정보를 가져옵니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.DataLakeStore/locations/operationResults/read | DataLakeStore 계정 작업의 결과를 가져옵니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.DataLakeStore/locations/usages/read | DataLakeStore 사용과 관련된 구독의 할당량 사용량 정보를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.DataLakeStore/operations/read | DataLakeStore에서 사용 가능한 작업을 가져옵니다. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

Azure 서비스: [Azure 데이터베이스 마이그레이션 서비스](../dms/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DataMigration/register/action | Azure Database Migration Service 공급자를 사용하여 구독을 등록합니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.DataMigration/locations/operationResults/read | 202 수락됨 응답과 관련된 장기 실행 작업의 상태를 가져옵니다. |
> |  | **위치/작업상태** |  |
> | 작업 | Microsoft.DataMigration/locations/operationStatuses/read | 202 수락됨 응답과 관련된 장기 실행 작업의 상태를 가져옵니다. |
> |  | **서비스** |  |
> | 작업 | Microsoft.DataMigration/services/read | 리소스에 대한 정보를 읽습니다. |
> | 작업 | Microsoft.DataMigration/services/write | 리소스 및 해당 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataMigration/services/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 작업 | Microsoft.DataMigration/services/stop/action | 비용을 최소화하기 위해 DMS 서비스를 중지합니다. |
> | 작업 | Microsoft.DataMigration/services/start/action | 마이그레이션을 다시 처리할 수 있도록 DMS 서비스를 시작합니다. |
> | 작업 | Microsoft.DataMigration/services/checkStatus/action | 서비스가 배포되어 실행되고 있는지 확인합니다. |
> | 작업 | Microsoft.DataMigration/서비스/구성 작업자/작업 | 서비스의 보편 작업자에 대한 DMS 작업자 구성 |
> | 작업 | 마이크로소프트.데이터 마이그레이션/서비스/추가 작업자/작업 | 서비스의 보편 근무자에 DMS 작업자 추가 |
> | 작업 | 마이크로 소프트.DataMigration/서비스/제거작업자/작업 | 서비스의 보편 근로자에게 DMS 작업자 제거 |
> | 작업 | 마이크로소프트.DataMigration/서비스/업데이트에이전트구성/액션 | 제공된 값으로 DMS 에이전트 구성을 업데이트합니다. |
> | 작업 | 마이크로소프트.데이터 마이그레이션/서비스/getHybridDownloadLink/작업 | RP Blob 저장소에서 DMS 작업자 패키지 다운로드 링크를 가져옵니다. |
> |  | **services/projects** |  |
> | 작업 | Microsoft.DataMigration/services/projects/read | 리소스에 대한 정보를 읽습니다. |
> | 작업 | Microsoft.DataMigration/services/projects/write | Azure Database Migration Service 작업을 실행합니다. |
> | 작업 | Microsoft.DataMigration/services/projects/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 작업 | Microsoft.DataMigration/services/projects/accessArtifacts/action | 프로젝트 아티팩트 GET 또는 PUT에 사용할 수 있는 URL을 생성합니다. |
> |  | **서비스/프로젝트/작업** |  |
> | 작업 | Microsoft.DataMigration/services/projects/tasks/read | 리소스에 대한 정보를 읽습니다. |
> | 작업 | Microsoft.DataMigration/services/projects/tasks/write | Azure Database Migration Service 작업을 실행합니다. |
> | 작업 | Microsoft.DataMigration/services/projects/tasks/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 작업 | Microsoft.DataMigration/services/projects/tasks/cancel/action | 작업이 현재 실행 중인 경우 취소합니다. |
> |  | **서비스/서비스 작업** |  |
> | 작업 | 마이크로 소프트.데이터 마이그레이션 / 서비스 / 서비스 작업 / 읽기 | 리소스에 대한 정보를 읽습니다. |
> | 작업 | 마이크로 소프트.DataMigration/서비스/서비스 작업/쓰기 | Azure Database Migration Service 작업을 실행합니다. |
> | 작업 | 마이크로 소프트.DataMigration/서비스/서비스 작업/삭제 | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 작업 | Microsoft.DataMigration/서비스/서비스 작업/취소/작업 | 작업이 현재 실행 중인 경우 취소합니다. |
> |  | **서비스/슬롯** |  |
> | 작업 | Microsoft.DataMigration/services/slots/read | 리소스에 대한 정보를 읽습니다. |
> | 작업 | Microsoft.DataMigration/services/slots/write | 리소스 및 해당 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DataMigration/services/slots/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> |  | **Sku** |  |
> | 작업 | Microsoft.DataMigration/skus/read | DMS 리소스에서 지원하는 SKU 목록을 가져옵니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

Azure 서비스: [MariaDB에 대 한 Azure 데이터베이스](../mariadb/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.DBforMariaDB/레지스터/작업 | MariaDB 리소스 공급자 등록 |
> | 작업 | 마이크로소프트.DBforMariaDB/체크네임가용성/액션 | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> |  | **위치/azureAsync작업** |  |
> | 작업 | 마이크로소프트.DBforMariaDB/위치/azureAsync작업/읽기 | MariaDB 서버 작업 결과 반환 |
> |  | **위치/작업결과** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 작업결과 / 읽기 | 리소스 그룹 기반 MariaDB 서버 작업 결과 반환 |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 작업결과 / 읽기 | MariaDB 서버 작업 결과 반환 |
> |  | **위치/퍼포먼스티어** |  |
> | 작업 | Microsoft.DBforMariaDB/locations/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> |  | **위치/프라이빗엔드포인트커넥스애싱크오Operation** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 개인엔드 포인트연결AzureAsync작업 / 읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/프라이빗엔드포인트연결작업결과** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 개인엔드 포인트 연결작업결과 / 읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/개인엔드포인트연결프록시A싱크오Operation** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 개인 엔드 포인트연결프록시Async작업 / 읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/프라이빗엔드포인트커넥션프록시작업결과** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 개인 엔드 포인트연결프록시작업결과/읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/보안경고정책AzureAsyncOperation** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 보안경고정책AzureAsync작업 /읽기 | 서버 위협 탐지 작업 결과 목록을 반환합니다. |
> |  | **위치/보안경고정책운영결과** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 위치 / 보안경고정책작업결과/읽기 | 서버 위협 탐지 작업 결과 목록을 반환합니다. |
> |  | **위치/서버키AzureAsync작업** |  |
> | 작업 | 마이크로소프트.DBforMariaDB/위치/서버키AzureAsync작업/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/서버키작업결과** |  |
> | 작업 | 마이크로소프트.DBforMariaDB/위치/서버키작업결과/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.DBforMariaDB/운영/읽기 | MariaDB 작업 목록을 반환합니다. |
> |  | **퍼포먼스티어** |  |
> | 작업 | Microsoft.DBforMariaDB/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> |  | **서버** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 쿼리텍스트 / 작업 | 쿼리 목록에 대한 텍스트 반환 |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 쿼리텍스트 / 작업 | 쿼리 텍스트를 반환합니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/delete | 기존 서버를 삭제합니다. |
> | 작업 | 마이크로소프트.DBforMariaDB/서버/다시 시작/작업 | 특정 서버를 다시 시작합니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> |  | **servers/administrators** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 관리자 / 읽기 | MariaDB 서버 관리자 목록을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 관리자 / 쓰기 | 지정된 매개 변수를 사용 하 고 MariaDB 서버 관리자를 만듭니다 또는 업데이트 합니다. |
> | 작업 | Microsoft.DBforMariaDB/서버/관리자/삭제 | MariaDB 서버의 기존 관리자를 삭제합니다. |
> |  | **servers/advisors** |  |
> | 작업 | 마이크로소프트.DBforMariaDB/서버/고문/읽기 | 관리자 목록을 반환합니다. |
> | 작업 | 마이크로소프트.DBforMariaDB/서버/고문/읽기 | 어드바이저 반환 |
> | 작업 | 마이크로소프트.DBforMariaDB/서버/고문/create권장 작업 세션/작업 | 새 추천 작업 세션 만들기 |
> |  | **서버/고문/권장 작업** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 고문 / 권장 작업 / 읽기 | 권장된 작업 목록을 반환 |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 고문 / 권장 작업 / 읽기 | 권장된 작업 목록을 반환 |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 고문 / 권장 작업 / 읽기 | 권장 작업 반환 |
> |  | **servers/configurations** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> |  | **servers/databases** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 데이터베이스 / 읽기 | MariaDB 데이터베이스 목록을 반환하거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 데이터베이스 / 쓰기 | 지정된 매개 변수를 사용하여 MariaDB 데이터베이스를 만들거나 지정된 데이터베이스에 대한 속성을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 데이터베이스 / 삭제 | 기존 MariaDB 데이터베이스를 삭제합니다. |
> |  | **servers/firewallRules** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> |  | **servers/keys** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 키 / 읽기 | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 키 / 쓰기 | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 키 / 삭제 | 기존 서버 키를 삭제합니다. |
> |  | **서버/로그파일** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 로그 파일 / 읽기 | MariaDB 로그파일 목록을 반환합니다. |
> |  | **서버/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 Proxies / 유효성 검사 / 작업 | NRP 측에서 개인 엔드포인트 연결 create 호출 유효성 검사 |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 Proxies / 읽기 | 개인 끝점 연결 프록시 목록을 반환하거나 지정된 개인 끝점 연결 프록시에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 Proxies / 쓰기 | 지정된 매개 변수를 사용 하 고 개인 끝점 연결 프록시를 만들거나 지정 된 개인 끝점 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 Proxies / 삭제 | 기존 개인 끝점 연결 프록시 삭제 |
> |  | **서버/개인엔드포인트연결** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 / 읽기 | 개인 끝점 연결 목록을 반환하거나 지정된 개인 끝점 연결에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 / 삭제 | 기존 개인 끝점 연결을 삭제합니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인엔드 포인트 연결 / 쓰기 | 기존 개인 끝점 연결을 승인 또는 거부합니다. |
> |  | **서버/개인링크리소스** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 개인링크자원 / 읽기 | 해당 MariaDB 서버에 대한 개인 링크 리소스 가져오기 |
> |  | **서버/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **서버/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | MariaDB 서버에 사용 가능한 로그를 가져옵니다. |
> |  | **서버/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **servers/recoverableServers** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/recoverableServers/read | 복구 가능한 MariaDB 서버 정보를 반환 |
> |  | **서버/복제본** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / 복제본 / 읽기 | MariaDB 서버의 복제본 읽기 |
> |  | **servers/securityAlertPolicies** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> |  | **servers/topQueryStatistics** |  |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / top쿼리 통계 / 읽기 | 최상위 쿼리에 대한 쿼리 통계 목록을 반환합니다. |
> | 작업 | 마이크로 소프트.DBforMariaDB / 서버 / top쿼리 통계 / 읽기 | 쿼리 통계 반환 |
> |  | **servers/virtualNetworkRules** |  |
> | 작업 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> |  | **servers/waitStatistics** |  |
> | 작업 | 마이크로소프트.DBforMariaDB/서버/대기통계/읽기 | 인스턴스에 대한 대기 통계를 반환합니다. |
> | 작업 | 마이크로소프트.DBforMariaDB/서버/대기통계/읽기 | 대기 통계 반환 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

Azure 서비스: [MySQL용 Azure 데이터베이스](../mysql/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.DBforMySQL/레지스터/작업 | MySQL 리소스 공급자 등록 |
> | 작업 | 마이크로소프트.DBforMySQL/체크네임가용성/액션 | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> |  | **위치/azureAsync작업** |  |
> | 작업 | 마이크로소프트.DBforMySQL/위치/azureAsync작업/읽기 | MySQL 서버 작업 결과 반환 |
> |  | **위치/작업결과** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 작업결과 / 읽기 | 리소스 그룹 기반 MySQL 서버 작업 결과 반환 |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 작업결과 / 읽기 | MySQL 서버 작업 결과 반환 |
> |  | **위치/퍼포먼스티어** |  |
> | 작업 | Microsoft.DBforMySQL/locations/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> |  | **위치/프라이빗엔드포인트커넥스애싱크오Operation** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 개인 엔드 포인트 연결AzureAsync작업 / 읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/프라이빗엔드포인트연결작업결과** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 개인 엔드 포인트 연결작업 결과 / 읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/개인엔드포인트연결프록시A싱크오Operation** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 개인 엔드 포인트연결프록시Async작업 / 읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/프라이빗엔드포인트커넥션프록시작업결과** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 개인 엔드 포인트연결프록시작업결과/읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/보안경고정책AzureAsyncOperation** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 보안경고정책AzureAsync작업 /읽기 | 서버 위협 탐지 작업 결과 목록을 반환합니다. |
> |  | **위치/보안경고정책운영결과** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 위치 / 보안경고정책작업결과/읽기 | 서버 위협 탐지 작업 결과 목록을 반환합니다. |
> |  | **위치/서버키AzureAsync작업** |  |
> | 작업 | 마이크로소프트.DBforMySQL/위치/서버키AzureAsync작업/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/서버키작업결과** |  |
> | 작업 | 마이크로소프트.DBforMySQL/위치/서버키작업결과/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.DBforMySQL/작업/읽기 | MySQL 작업 목록을 반환합니다. |
> |  | **퍼포먼스티어** |  |
> | 작업 | Microsoft.DBforMySQL/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> |  | **서버** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 쿼리텍스트 / 작업 | 쿼리 목록에 대한 텍스트 반환 |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 쿼리텍스트 / 작업 | 쿼리 텍스트를 반환합니다. |
> | 작업 | Microsoft.DBforMySQL/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMySQL/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.DBforMySQL/servers/delete | 기존 서버를 삭제합니다. |
> | 작업 | 마이크로소프트.DBforMySQL/서버/다시 시작/작업 | 특정 서버를 다시 시작합니다. |
> | 작업 | Microsoft.DBforMySQL/servers/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> |  | **servers/administrators** |  |
> | 작업 | 마이크로소프트.DBforMySQL/서버/관리자/읽기 | MySQL 서버 관리자 목록을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 관리자 / 쓰기 | 지정된 매개 변수를 사용 하 고 MySQL 서버 관리자를 만듭니다 또는 업데이트 합니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 관리자 / 삭제 | MySQL 서버의 기존 관리자를 삭제합니다. |
> |  | **servers/advisors** |  |
> | 작업 | 마이크로소프트.DBforMySQL/서버/고문/읽기 | 관리자 목록을 반환합니다. |
> | 작업 | 마이크로소프트.DBforMySQL/서버/고문/읽기 | 어드바이저 반환 |
> | 작업 | 마이크로소프트.DBforMySQL/서버/고문/create권장 작업 세션/작업 | 새 추천 작업 세션 만들기 |
> |  | **서버/고문/권장 작업** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 고문 / 권장 작업 / 읽기 | 권장된 작업 목록을 반환 |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 고문 / 권장 작업 / 읽기 | 권장된 작업 목록을 반환 |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 고문 / 권장 작업 / 읽기 | 권장 작업 반환 |
> |  | **servers/configurations** |  |
> | 작업 | Microsoft.DBforMySQL/servers/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMySQL/servers/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> |  | **servers/databases** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 데이터베이스 / 읽기 | MySQL 데이터베이스 목록을 반환하거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 데이터베이스 / 쓰기 | 지정된 매개 변수를 사용하여 MySQL 데이터베이스를 만들거나 지정된 데이터베이스에 대한 속성을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 데이터베이스 / 삭제 | 기존 MySQL 데이터베이스를 삭제합니다. |
> |  | **servers/firewallRules** |  |
> | 작업 | Microsoft.DBforMySQL/servers/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMySQL/servers/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 작업 | Microsoft.DBforMySQL/servers/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> |  | **servers/keys** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 키 / 읽기 | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 키 / 쓰기 | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 키 / 삭제 | 기존 서버 키를 삭제합니다. |
> |  | **서버/로그파일** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 로그 파일 / 읽기 | MySQL 로그파일 목록을 반환합니다. |
> |  | **서버/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인 엔드 포인트 연결 Proxies / 유효성 검사 / 작업 | NRP 측에서 개인 엔드포인트 연결 create 호출 유효성 검사 |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인엔드 포인트 연결 Proxies / 읽기 | 개인 끝점 연결 프록시 목록을 반환하거나 지정된 개인 끝점 연결 프록시에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인엔드 포인트 연결 Proxies / 쓰기 | 지정된 매개 변수를 사용 하 고 개인 끝점 연결 프록시를 만들거나 지정 된 개인 끝점 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인엔드 포인트 연결 Proxies / 삭제 | 기존 개인 끝점 연결 프록시 삭제 |
> |  | **서버/개인엔드포인트연결** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인엔드 포인트 연결 / 읽기 | 개인 끝점 연결 목록을 반환하거나 지정된 개인 끝점 연결에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인 엔드 포인트 연결 / 삭제 | 기존 개인 끝점 연결을 삭제합니다. |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 개인엔드 포인트 연결 / 쓰기 | 기존 개인 끝점 연결을 승인 또는 거부합니다. |
> |  | **서버/개인링크리소스** |  |
> | 작업 | 마이크로소프트.DBforMySQL/서버/개인링크자원/읽기 | 해당 MySQL 서버에 대한 개인 링크 리소스 가져오기 |
> |  | **서버/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **서버/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | MySQL 서버에 사용 가능한 로그를 가져오기 |
> |  | **서버/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **servers/recoverableServers** |  |
> | 작업 | Microsoft.DBforMySQL/servers/recoverableServers/read | 복구 가능한 MySQL 서버 정보를 반환합니다. |
> |  | **서버/복제본** |  |
> | 작업 | 마이크로 소프트.DBforMySQL / 서버 / 복제본 / 읽기 | MySQL 서버의 복제본 읽기 |
> |  | **servers/securityAlertPolicies** |  |
> | 작업 | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> |  | **servers/topQueryStatistics** |  |
> | 작업 | 마이크로소프트.DBforMySQL/서버/topQueryStatistics/읽기 | 최상위 쿼리에 대한 쿼리 통계 목록을 반환합니다. |
> | 작업 | 마이크로소프트.DBforMySQL/서버/topQueryStatistics/읽기 | 쿼리 통계 반환 |
> |  | **servers/virtualNetworkRules** |  |
> | 작업 | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> |  | **servers/waitStatistics** |  |
> | 작업 | 마이크로소프트.DBforMySQL/서버/대기통계/읽기 | 인스턴스에 대한 대기 통계를 반환합니다. |
> | 작업 | 마이크로소프트.DBforMySQL/서버/대기통계/읽기 | 대기 통계 반환 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

Azure 서비스: [PostgreSQL용 Azure 데이터베이스](../postgresql/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.DBforPostgreSQL/레지스터/작업 | 포스트그레SQL 리소스 공급자 등록 |
> | 작업 | 마이크로소프트.DBforPostgreSQL/체크네임가용성/액션 | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> |  | **위치/azureAsync작업** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/azureAsync작업/읽기 | PostgreSQL 서버 작업 결과 반환 |
> |  | **위치/작업결과** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/작업결과/읽기 | 리소스 그룹 기반 PostgreSQL 서버 작업 결과 반환 |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/작업결과/읽기 | PostgreSQL 서버 작업 결과 반환 |
> |  | **위치/퍼포먼스티어** |  |
> | 작업 | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> |  | **위치/프라이빗엔드포인트커넥스애싱크오Operation** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/개인엔드포인트커넥스아싱크연수/읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/프라이빗엔드포인트연결작업결과** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/개인엔드포인트연결작업결과/읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/개인엔드포인트연결프록시A싱크오Operation** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/개인엔드포인트커넥션프록시A싱크작업/읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/프라이빗엔드포인트커넥션프록시작업결과** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/개인엔드포인트연결프록시작업결과/읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/보안경고정책AzureAsyncOperation** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/보안경고정책AzureAsyncOperation/읽기 | 서버 위협 탐지 작업 결과 목록을 반환합니다. |
> |  | **위치/보안경고정책운영결과** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 위치 / 보안경고정책작업결과/읽기 | 서버 위협 탐지 작업 결과 목록을 반환합니다. |
> |  | **위치/서버키AzureAsync작업** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/서버키AzureAsync작업/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/서버키작업결과** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/위치/서버키작업결과/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/작업/읽기 | PostgreSQL 작업 목록을 반환합니다. |
> |  | **퍼포먼스티어** |  |
> | 작업 | Microsoft.DBforPostgreSQL/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> |  | **서버** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 쿼리 텍스트를 반환합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/delete | 기존 서버를 삭제합니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/다시 시작/작업 | 특정 서버를 다시 시작합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> |  | **servers/administrators** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/관리자/읽기 | PostgreSQL 서버 관리자 목록을 가져옵니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/관리자/쓰기 | 지정된 매개 변수를 사용 하 고 PostgreSQL 서버 관리자를 만듭니다 또는 업데이트 합니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/관리자/삭제 | PostgreSQL 서버의 기존 관리자를 삭제합니다. |
> |  | **servers/advisors** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/advisors/read | 관리자 목록을 반환합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 권장 사항 수행 |
> |  | **서버/고문/권장 작업** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 권장된 작업 목록을 반환 |
> |  | **servers/configurations** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> |  | **servers/databases** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 데이터베이스 / 읽기 | PostgreSQL 데이터베이스 목록을 반환하거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 데이터베이스 / 쓰기 | 지정된 매개 변수를 사용하여 PostgreSQL 데이터베이스를 만들거나 지정된 데이터베이스에 대한 속성을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 데이터베이스 / 삭제 | 기존 PostgreSQL 데이터베이스를 삭제합니다. |
> |  | **servers/firewallRules** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> |  | **servers/keys** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/키/읽기 | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 키 / 쓰기 | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 키 / 삭제 | 기존 서버 키를 삭제합니다. |
> |  | **서버/로그파일** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 로그 파일 / 읽기 | PostgreSQL 로그파일 목록을 반환합니다. |
> |  | **서버/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/개인엔드포인트커넥션프록시스/유효성 검사/조치 | NRP 측에서 개인 엔드포인트 연결 create 호출 유효성 검사 |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 개인엔드 포인트 연결 프로시저 / 읽기 | 개인 끝점 연결 프록시 목록을 반환하거나 지정된 개인 끝점 연결 프록시에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 개인엔드 포인트 연결 Proxies / 쓰기 | 지정된 매개 변수를 사용 하 고 개인 끝점 연결 프록시를 만들거나 지정 된 개인 끝점 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 개인엔드 포인트 연결 Proxies / 삭제 | 기존 개인 끝점 연결 프록시 삭제 |
> |  | **서버/개인엔드포인트연결** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/개인엔드포인트연결/읽기 | 개인 끝점 연결 목록을 반환하거나 지정된 개인 끝점 연결에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 개인 엔드 포인트 연결 / 삭제 | 기존 개인 끝점 연결을 삭제합니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/개인엔드포인트연결/쓰기 | 기존 개인 끝점 연결을 승인 또는 거부합니다. |
> |  | **서버/개인링크리소스** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/개인링크자원/읽기 | 해당 PostgreSQL 서버에 대한 개인 링크 리소스 가져오기 |
> |  | **서버/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **서버/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | PostgreSQL 서버에 사용할 수 있는 로그 가져옵니다. |
> |  | **서버/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **servers/queryTexts** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버 / 쿼리텍스트 / 읽기 | 쿼리 목록에 대한 텍스트 반환 |
> |  | **servers/recoverableServers** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 복구 가능한 PostgreSQL 서버 정보를 반환합니다. |
> |  | **서버/복제본** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버/복제본/읽기 | PostgreSQL 서버의 복제본 읽기 |
> |  | **servers/securityAlertPolicies** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> |  | **servers/topQueryStatistics** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 최상위 쿼리에 대한 쿼리 통계 목록을 반환합니다. |
> |  | **servers/virtualNetworkRules** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> |  | **servers/waitStatistics** |  |
> | 작업 | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 인스턴스에 대한 대기 통계를 반환합니다. |
> |  | **서버v2** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/읽기 | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/쓰기 | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/삭제 | 기존 서버를 삭제합니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/업데이트구성/작업 | 지정된 서버의 구성을 업데이트합니다. |
> |  | **서버v2/구성** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버v2 / 구성 / 읽기 | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버v2 / 구성 / 쓰기 | 지정된 구성의 값을 업데이트합니다. |
> |  | **서버v2/방화벽규칙** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버v2 / 방화벽규칙 / 읽기 | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버v2 / 방화벽규칙 / 쓰기 | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 서버v2 / 방화벽규칙 / 삭제 | 기존 방화벽 규칙을 삭제합니다. |
> |  | **서버v2/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/공급자/마이크로소프트.인사이트/진단설정/읽기 | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **서버v2/공급자/마이크로소프트.인사이트/로그정의** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/공급자/마이크로소프트.인사이트/로그정의/읽기 | PostgreSQL 서버에 사용할 수 있는 로그 가져옵니다. |
> |  | **서버v2/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/서버v2/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **단일 서버** |  |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 단일 서버 / 읽기 | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 단일 서버 / 쓰기 | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | 마이크로 소프트.DBforPostgreSQL / 단일 서버 / 삭제 | 기존 서버를 삭제합니다. |
> |  | **단일 서버/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/단일 서버/공급자/마이크로소프트.인사이트/진단설정/읽기 | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | 마이크로소프트.DBforPostgreSQL/단일 서버/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **단일 서버/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/단일 서버/공급자/마이크로소프트.인사이트/로그정의/읽기 | PostgreSQL 서버에 사용할 수 있는 로그 가져옵니다. |
> |  | **단일 서버/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.DBforPostgreSQL/단일 서버/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |

## <a name="microsoftdevices"></a>Microsoft.Devices

Azure 서비스: [IoT 허브, IoT](../iot-hub/index.yml) [허브 장치 프로비저닝 서비스](../iot-dps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Devices/register/action | IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다. |
> | 작업 | Microsoft.Devices/checkNameAvailability/Action | IotHub 이름이 사용 가능한지 확인합니다. |
> | 작업 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 프로비전 서비스 이름을 사용할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Devices/register/action | IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다. |
> | 작업 | Microsoft.Devices/checkNameAvailability/Action | IotHub 이름이 사용 가능한지 확인합니다. |
> | 작업 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 프로비전 서비스 이름을 사용할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Devices/register/action | IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다. |
> |  | **계정/진단설정** |  |
> | 작업 | 마이크로 소프트.장치 / 계정 / 진단설정 / 읽기 | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | 마이크로 소프트.장치 / 계정 / 진단설정 / 쓰기 | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **계정/로그정의 정의** |  |
> | 작업 | 마이크로 소프트.장치 / 계정 / 로그 정의 / 읽기 | IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다. |
> |  | **계정/메트릭정의** |  |
> | 작업 | Microsoft.장치/계정/메트릭정의/읽기 | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> |  | **디지털 트윈스** |  |
> | 작업 | 마이크로소프트.장치/디지털트윈스/읽기 | 구독에 연결된 디지털 트윈스 계정 목록을 가져옵니다. |
> | 작업 | 마이크로소프트.장치/디지털트윈스/쓰기 | 새 디지털 트윈 계정 만들기 |
> | 작업 | 마이크로소프트.장치/디지털트윈스/삭제 | 기존 디지털 트윈스 계정 및 모든 자식 삭제 |
> |  | **디지털트윈/운영 결과** |  |
> | 작업 | 마이크로 소프트.장치 / 디지털 트윈스 / 작업 결과 / 읽기 | 디지털 트윈스 계정에 대한 작업 상태 얻기 |
> |  | **디지털 트윈스/스쿠스** |  |
> | 작업 | 마이크로소프트.장치/디지털트윈스/스쿠스/읽기 | 디지털 트윈 계정에 대한 유효한 SUS 목록 받기 |
> |  | **탄성 풀/진단설정** |  |
> | 작업 | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **탄성 풀/이벤트그리드필터** |  |
> | 작업 | 마이크로 소프트.장치 / 탄성 풀 / 이벤트그리드 필터 / 쓰기 | 새 만들기 또는 기존 탄력적 풀 이벤트 그리드 필터 업데이트 |
> | 작업 | 마이크로 소프트.장치 / 탄성 풀 / 이벤트그리드 필터 / 읽기 | 탄력적 풀 이벤트 그리드 필터 를 가져옵니다. |
> | 작업 | 마이크로 소프트.장치 / 탄성 풀 / 이벤트그리드 필터 / 삭제 | 탄력적 풀 이벤트 그리드 필터 삭제 |
> |  | **탄성 풀/iotHub테** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/Write | IotHub 테넌트 리소스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/Read | IotHub 테넌트 리소스를 가져옵니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/Delete | IotHub 테넌트 리소스를 삭제합니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | IotHub 테넌트 키를 가져옵니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 디바이스를 내보냅니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 디바이스 가져오기 |
> |  | **탄성 풀/iotHubTenants/인증서** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 인증서를 가져옵니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 인증서를 삭제합니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> |  | **탄성 풀/IotHubTenants/진단설정** |  |
> | 작업 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **elasticPools/iotHubTenants/이벤트허브엔드포인트/소비자그룹** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub 소비자 그룹을 만듭니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub 소비자 그룹을 가져옵니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub 소비자 그룹을 삭제합니다. |
> |  | **탄성 풀/iotHubTenants/getStats** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | IotHub 테넌트 통계 리소스를 가져옵니다. |
> |  | **탄성 풀/iotHubTenants/iotHub키** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | IotHub 테넌트 키를 가져옵니다. |
> |  | **탄성 풀/iotHubTenants/작업** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다. |
> |  | **탄성 풀/IotHub테/로그정의** |  |
> | 작업 | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다. |
> |  | **탄성 풀/IotHub테/메트릭정의** |  |
> | 작업 | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> |  | **탄성 풀/iotHubTenants/할당량메트릭** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 할당량 메트릭을 가져옵니다. |
> |  | **탄성 풀/iotHubTenants/라우팅/경로** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 모든 기존 경로에 대해 메시지를 테스트합니다. |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 제공된 테스트 경로에 대해 메시지를 테스트합니다. |
> |  | **elasticPools/iotHubTenants/라우팅엔드포인트건강** |  |
> | 작업 | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | IotHub에 대한 모든 라우팅 엔드포인트의 상태를 가져옵니다. |
> |  | **탄성 풀/iotHub테/보안설정** |  |
> | 작업 | 마이크로 소프트.장치 /탄성 풀 / iotHubTenants / 보안설정 / 쓰기 | Iot 테넌트 허브에서 Azure 보안 센터 설정 업데이트 |
> | 작업 | 마이크로 소프트.장치 / 탄성 풀 / iotHubTenants / 보안설정 / 읽기 | Iot 테넌트 허브에서 Azure 보안 센터 설정 받기 |
> |  | **elasticPools/iotHubTenants/보안설정/운영결과** |  |
> | 작업 | 마이크로 소프트.장치 /탄성 풀 / iotHubTenants / 보안설정 / 작업결과 / 읽기 | Iot 테넌트 허브 보안 설정에 대한 비동기 넣기 작업의 결과 가져옵니다. |
> |  | **탄력적 풀/메트릭정의** |  |
> | 작업 | Microsoft.Devices/ElasticPools/metricDefinitions/read | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> |  | **iotHubs** |  |
> | 작업 | Microsoft.Devices/iotHubs/Read | IotHub 리소스를 가져옵니다. |
> | 작업 | Microsoft.Devices/iotHubs/Write | IotHub 리소스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Devices/iotHubs/Delete | IotHub 리소스를 삭제합니다. |
> | 작업 | Microsoft.Devices/iotHubs/listkeys/Action | 모든 IotHub 키를 가져옵니다. |
> | 작업 | Microsoft.Devices/iotHubs/exportDevices/Action | 디바이스를 내보냅니다. |
> | 작업 | Microsoft.Devices/iotHubs/importDevices/Action | 디바이스 가져오기 |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결승인 / 작업 | 개인 끝점 연결 승인 또는 거부 |
> |  | **iotHubs/인증서** |  |
> | 작업 | Microsoft.Devices/iotHubs/certificates/Read | 인증서를 가져옵니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/Delete | 인증서를 삭제합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/Read | 인증서를 가져옵니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/Delete | 인증서를 삭제합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 작업 | Microsoft.Devices/iotHubs/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> |  | **IotHubs/진단설정** |  |
> | 작업 | Microsoft.Devices/IotHubs/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Devices/IotHubs/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **iotHubs/디지털트윈스링크스** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 디지털 트윈스링크 / 쓰기 |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 디지털 트윈스링크 / 읽기 |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 디지털 트윈스링크 / 삭제 |  |
> |  | **iotHubs/이벤트그리드필터** |  |
> | 작업 | Microsoft.Devices/iotHubs/eventGridFilters/Write | 새 Event Grid 필터를 만들거나 기존 Event Grid 필터를 업데이트합니다. |
> | 작업 | Microsoft.Devices/iotHubs/eventGridFilters/Read | Event Grid 필터를 가져옵니다. |
> | 작업 | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Event Grid 필터를 삭제합니다. |
> |  | **iotHubs/이벤트허브엔드포인트/소비자그룹** |  |
> | 작업 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub 소비자 그룹을 만듭니다. |
> | 작업 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub 소비자 그룹을 가져옵니다. |
> | 작업 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub 소비자 그룹을 삭제합니다. |
> |  | **iotHubs/iotHubKeys** |  |
> | 작업 | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 지정된 이름의 IotHub 키를 가져옵니다. |
> |  | **iotHubs/iotHubStats** |  |
> | 작업 | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub 통계를 가져옵니다. |
> |  | **iotHubs/작업** |  |
> | 작업 | Microsoft.Devices/iotHubs/jobs/Read | 지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다. |
> |  | **IotHubs/로그정의정의** |  |
> | 작업 | Microsoft.Devices/IotHubs/logDefinitions/read | IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다. |
> |  | **IotHubs/메트릭정의** |  |
> | 작업 | Microsoft.Devices/IotHubs/metricDefinitions/read | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> |  | **iotHubs/작업 결과** |  |
> | 작업 | Microsoft.Devices/iotHubs/operationresults/Read | 작업 결과를 가져옵니다(사용되지 않는 API). |
> |  | **iotHubs/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 Proxies / 유효성 검사 / 작업 | 만드는 동안 개인 엔드포인트 연결 프록시 입력의 유효성을 검사합니다. |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 Proxies / 읽기 | 지정된 개인 끝점 연결 프록시에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 Proxies / 쓰기 | 개인 끝점 연결 프록시를 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 Proxies / 삭제 | 기존 개인 끝점 연결 프록시 삭제 |
> |  | **iotHubs/개인엔드포인트커넥션프로시즈/운영결과** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 Proxies / 작업결과 / 읽기 | 개인 끝점 연결 프록시에서 비동기 작업의 결과 가져옵니다. |
> |  | **iotHubs/프라이빗엔드포인트커넥션** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 / 읽기 | 지정된 개인 끝점 연결에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 / 삭제 | 기존 개인 끝점 연결을 삭제합니다. |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 / 쓰기 | 개인 끝점 연결을 만들거나 업데이트합니다. |
> |  | **iotHubs/프라이빗엔드포인트연결/운영결과** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인엔드 포인트 연결 / 작업결과 / 읽기 | 개인 끝점 연결에서 비동기 작업의 결과 가져옵니다. |
> |  | **iotHubs/프라이빗링크리소스** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 개인링크자원 / 읽기 | IotHub에 대한 개인 링크 리소스 가져오기 |
> |  | **iotHubs/할당량메트릭** |  |
> | 작업 | Microsoft.Devices/iotHubs/quotaMetrics/Read | 할당량 메트릭을 가져옵니다. |
> |  | **iotHubs/라우팅** |  |
> | 작업 | Microsoft.Devices/iotHubs/routing/$testall/Action | 모든 기존 경로에 대해 메시지를 테스트합니다. |
> | 작업 | Microsoft.Devices/iotHubs/routing/$testnew/Action | 제공된 테스트 경로에 대해 메시지를 테스트합니다. |
> |  | **iotHubs/라우팅엔드포인트건강** |  |
> | 작업 | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | IotHub에 대한 모든 라우팅 엔드포인트의 상태를 가져옵니다. |
> |  | **iotHubs/보안설정** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 보안설정 / 쓰기 | Iot Hub에서 Azure 보안 센터 설정 업데이트 |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 보안설정 / 읽기 | Iot Hub에서 Azure 보안 센터 설정 받기 |
> |  | **iotHubs/보안설정/운영결과** |  |
> | 작업 | 마이크로 소프트.장치 / iotHubs / 보안설정 / 작업결과 / 읽기 | Iot 허브 보안 설정에 대한 비동기 넣기 작업의 결과 가져옵니다. |
> |  | **iotHubs/스쿠스** |  |
> | 작업 | Microsoft.Devices/iotHubs/skus/Read | 유효한 IotHub SKU를 가져옵니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.Devices/locations/operationresults/Read | 작업 결과를 기반으로 위치를 가져옵니다. |
> | 작업 | Microsoft.Devices/locations/operationresults/Read | 작업 결과를 기반으로 위치를 가져옵니다. |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.Devices/operationresults/Read | 작업 결과를 가져옵니다. |
> | 작업 | Microsoft.Devices/operationresults/Read | 작업 결과를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Devices/operations/Read | 모든 ResourceProvider 작업을 가져옵니다. |
> | 작업 | Microsoft.Devices/operations/Read | 모든 ResourceProvider 작업을 가져옵니다. |
> |  | **프로비저닝서비스** |  |
> | 작업 | Microsoft.Devices/provisioningServices/Read | IotDps 리소스를 가져옵니다. |
> | 작업 | Microsoft.Devices/provisioningServices/Write | IotDps 리소스를 만듭니다. |
> | 작업 | Microsoft.Devices/provisioningServices/Delete | IotDps 리소스를 삭제합니다. |
> | 작업 | Microsoft.Devices/provisioningServices/listkeys/Action | 모든 IotDps 키를 가져옵니다. |
> |  | **프로비저닝서비스/인증서** |  |
> | 작업 | Microsoft.Devices/provisioningServices/certificates/Read | 인증서를 가져옵니다. |
> | 작업 | Microsoft.Devices/provisioningServices/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Devices/provisioningServices/certificates/Delete | 인증서를 삭제합니다. |
> | 작업 | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 작업 | Microsoft.Devices/provisioningServices/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> |  | **프로비전서비스/진단설정** |  |
> | 작업 | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **프로비저닝서비스/키** |  |
> | 작업 | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 키 이름에 대 한 IotDps 키를 가져옵니다. |
> |  | **프로비전서비스/로그정의정의** |  |
> | 작업 | Microsoft.Devices/provisioningServices/logDefinitions/read | 서비스를 프로비전하는 데 사용할 수 있는 로그 정의를 가져옵니다. |
> |  | **프로비전서비스/메트릭정의** |  |
> | 작업 | Microsoft.Devices/provisioningServices/metricDefinitions/read | 서비스를 프로비전하는 데 사용할 수 있는 메트릭을 가져옵니다. |
> |  | **프로비저닝서비스/운영결과** |  |
> | 작업 | Microsoft.Devices/provisioningServices/operationresults/Read | DPS 작업 결과를 가져옵니다. |
> |  | **프로비저닝서비스/스쿠스** |  |
> | 작업 | Microsoft.Devices/provisioningServices/skus/Read | 유효한 IotDps Sku를 가져옵니다. |
> |  | **사용** |  |
> | 작업 | Microsoft.Devices/usages/Read | 이 공급자에 대한 구독 사용 정보를 가져옵니다. |
> | 작업 | Microsoft.Devices/usages/Read | 이 공급자에 대한 구독 사용 정보를 가져옵니다. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

Azure 서비스: [Azure 개발자 공간](../dev-spaces/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DevSpaces/register/action | 구독으로 Microsoft Dev Spaces 리소스 공급자 등록 |
> |  | **controllers** |  |
> | 작업 | Microsoft.DevSpaces/controllers/read | Azure Dev Spaces 컨트롤러 속성 읽기 |
> | 작업 | Microsoft.DevSpaces/controllers/write | Azure Dev Spaces 컨트롤러 속성 만들기 또는 업데이트 |
> | 작업 | Microsoft.DevSpaces/controllers/delete | Azure Dev Spaces 컨트롤러 및 데이터 평면 서비스 삭제 |
> | 작업 | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Azure Dev Spaces 컨트롤러의 인프라에 대한 연결 세부 정보 나열 |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.데브스페이스/위치/체크컨테이너호스트매핑/액션 | 컨테이너 호스트에 대한 기존 컨트롤러 매핑 확인 |
> |  | **위치/체크컨테이너호스트매핑** |  |
> | 작업 | 마이크로소프트.데브 스페이스/위치/체크컨테이너호스트매핑/읽기 | 컨테이너 호스트에 대한 기존 컨트롤러 매핑 확인 |
> |  | **위치/작업 결과** |  |
> | 작업 | 마이크로소프트.데브스페이스/위치/작업 결과/읽기 | 비동기 작업에 대한 상태 읽기 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

Azure 서비스: [Azure 랩 서비스](../lab-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DevTestLab/register/action | 구독을 등록합니다. |
> |  | **랩 센터** |  |
> | 작업 | Microsoft.DevTestLab/labCenters/delete | 랩 센터를 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labCenters/read | 랩 센터를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labCenters/write | 랩 센터를 추가 또는 수정합니다. |
> |  | **labs** |  |
> | 작업 | Microsoft.DevTestLab/labs/delete | 랩을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/read | 랩을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/write | 랩을 추가 또는 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/ListVhds/action | 사용자 지정 이미지를 만드는 데 사용할 수 있는 디스크 이미지를 나열합니다. |
> | 작업 | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 랩에 사용자 지정 디스크 이미지를 업로드하기 위한 URI를 생성합니다. |
> | 작업 | Microsoft.DevTestLab/labs/CreateEnvironment/action | 랩에 가상 머신을 만듭니다. |
> | 작업 | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 랩에서 임의 클레임 가능 가상 머신을 클레임합니다. |
> | 작업 | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 랩 리소스 사용량을 스토리지 계정으로 내보냅니다. |
> | 작업 | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 가상 머신을 다른 랩으로 가져옵니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/보장전류유저프로필/액션 | 현재 사용자가 랩에 유효한 프로필을 가지고 있는지 확인합니다. |
> |  | **랩/아티팩트 소스** |  |
> | 작업 | Microsoft.DevTestLab/labs/artifactSources/delete | 아티팩트 원본을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/artifactSources/read | 아티팩트 원본을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/artifactSources/write | 아티팩트 원본을 추가하거나 수정합니다. |
> |  | **랩/아티팩트소스/암템플릿** |  |
> | 작업 | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Azure Resource Manager 템플릿을 읽습니다. |
> |  | **랩/아티팩트소스/아티팩트** |  |
> | 작업 | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 아티팩트를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 지정된 아티팩트에 대한 Azure 리소스 관리자 템플릿을 생성하고, 필요한 파일을 저장소 계정에 업로드하고, 생성된 아티팩트의 유효성을 검사합니다. |
> |  | **labs/costs** |  |
> | 작업 | Microsoft.DevTestLab/labs/costs/read | 비용을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/costs/write | 비용을 추가하거나 수정합니다. |
> |  | **랩/사용자 지정 이미지** |  |
> | 작업 | Microsoft.DevTestLab/labs/customImages/delete | 사용자 지정 이미지를 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/customImages/read | 사용자 지정 이미지를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/customImages/write | 사용자 지정 이미지를 추가하거나 수정합니다. |
> |  | **labs/formulas** |  |
> | 작업 | Microsoft.DevTestLab/labs/formulas/delete | 수식을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/formulas/read | 수식을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/formulas/write | 수식을 추가하거나 수정합니다. |
> |  | **랩/갤러리이미지** |  |
> | 작업 | Microsoft.DevTestLab/labs/galleryImages/read | 갤러리 이미지를 읽습니다. |
> |  | **랩/알림채널** |  |
> | 작업 | Microsoft.DevTestLab/labs/notificationChannels/delete | 알림 채널을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/notificationChannels/read | 알림 채널을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/notificationChannels/write | 알림 채널을 추가하거나 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 제공된 채널에 알림을 보냅니다. |
> |  | **랩/정책세트** |  |
> | 작업 | Microsoft.DevTestLab/labs/policySets/read | 정책 집합을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 랩 정책을 평가합니다. |
> |  | **랩/정책세트/정책** |  |
> | 작업 | Microsoft.DevTestLab/labs/policySets/policies/delete | 정책을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/policySets/policies/read | 정책을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/policySets/policies/write | 정책을 추가하거나 수정합니다. |
> |  | **labs/schedules** |  |
> | 작업 | Microsoft.DevTestLab/labs/schedules/delete | 일정을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/schedules/read | 일정을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/schedules/write | 일정을 추가하거나 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/schedules/Execute/action | 일정을 실행합니다. |
> | 작업 | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 적용 가능한 모든 일정을 나열합니다. |
> |  | **실험실/서비스 러너** |  |
> | 작업 | Microsoft.DevTestLab/labs/serviceRunners/delete | 서비스 실행기를 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/serviceRunners/read | 서비스 실행기를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/serviceRunners/write | 서비스 실행기를 추가하거나 수정합니다. |
> |  | **랩/공유 갤러리** |  |
> | 작업 | 마이크로소프트.데브테스트랩/랩/공유갤러리/삭제 | 공유 갤러리를 삭제합니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/공유갤러리/읽기 | 공유 갤러리를 읽습니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/공유갤러리/쓰기 | 공유 갤러리를 추가하거나 수정합니다. |
> |  | **랩/공유 갤러리/공유 이미지** |  |
> | 작업 | 마이크로소프트.DevTestLab/랩/공유 갤러리/공유 이미지/삭제 | 공유 된 이미지를 삭제합니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/공유갤러리/공유이미지/읽기 | 공유 된 이미지를 읽습니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/공유갤러리/공유이미지/쓰기 | 공유 이미지를 추가하거나 수정합니다. |
> |  | **labs/users** |  |
> | 작업 | Microsoft.DevTestLab/labs/users/delete | 사용자 프로필을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/read | 사용자 프로필을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/write | 사용자 프로필을 추가하거나 수정합니다. |
> |  | **labs/users/disks** |  |
> | 작업 | Microsoft.DevTestLab/labs/users/disks/delete | 디스크를 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/disks/read | 디스크를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/disks/write | 디스크를 추가 또는 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/disks/Attach/action | 디스크의 임대를 만들고 가상 머신에 연결합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/disks/Detach/action | 가상 머신에 연결된 디스크의 임대를 분리하고 해제합니다. |
> |  | **labs/users/environments** |  |
> | 작업 | Microsoft.DevTestLab/labs/users/environments/delete | 환경을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/environments/read | 환경을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/environments/write | 환경을 추가하거나 수정합니다. |
> |  | **labs/users/secrets** |  |
> | 작업 | Microsoft.DevTestLab/labs/users/secrets/delete | 암호를 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/secrets/read | 암호를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/secrets/write | 암호를 추가 또는 수정합니다. |
> |  | **랩/사용자/서비스패브릭** |  |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 서비스 패브릭을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 서비스 패브릭을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 서비스 패브릭을 추가하거나 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 서비스 패브릭을 시작합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 서비스 패브릭을 중지합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> |  | **랩/사용자/서비스패브릭/스케줄** |  |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 일정을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 일정을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 일정을 추가하거나 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 일정을 실행합니다. |
> |  | **랩/가상 머신** |  |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/delete | 가상 머신을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/read | 가상 머신을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/write | 가상 머신을 추가 또는 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 가상 머신에 새 또는 기존 데이터 디스크를 연결합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 가상 머신에 아티팩트를 적용합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/claim/action | 기존 가상 머신의 소유권을 가져옵니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/가상머신/클리어아티팩트결과/액션 | 가상 시스템의 아티팩트 결과를 지웁습니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 가상 머신에서 지정된 디스크를 분리합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 가상 머신에 대한 RDP 파일의 콘텐츠를 나타내는 문자열을 가져옵니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 가상 머신을 다시 배포합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 가상 머신 크기를 조정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 가상 머신을 다시 시작합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 가상 머신을 시작합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 가상 머신 중지 |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 현재 사용자가 소유할 가상 머신에 연결된 모든 데이터 디스크를 전송합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 기존 가상 머신의 소유권을 해제합니다. |
> |  | **랩/가상 머신/일정** |  |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 일정을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 일정을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 일정을 추가하거나 수정합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 일정을 실행합니다. |
> |  | **랩/가상 네트워크** |  |
> | 작업 | Microsoft.DevTestLab/labs/virtualNetworks/delete | 가상 네트워크를 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualNetworks/read | 가상 네트워크를 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/virtualNetworks/write | 가상 네트워크를 추가 또는 수정합니다. |
> |  | **랩/가상 네트워크/바스티안호스트** |  |
> | 작업 | 마이크로소프트.데브테스트랩/랩/가상네트워크/바스티온호스트/삭제 | 요새 호스트를 삭제합니다. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/가상네트워크/바스티온호스트/읽기 | 방호호스트를 읽어보십시오. |
> | 작업 | 마이크로소프트.데브테스트랩/랩/가상네트워크/바스티온호스트/쓰기 | 배스션 호스트를 추가하거나 수정합니다. |
> |  | **실험실/vmPools** |  |
> | 작업 | Microsoft.DevTestLab/labs/vmPools/delete | 가상 머신 풀을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/labs/vmPools/read | 가상 머신 풀을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/labs/vmPools/write | 가상 머신 풀을 추가하거나 수정합니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.DevTestLab/locations/operations/read | 읽기 작업 |
> |  | **일정** |  |
> | 작업 | Microsoft.DevTestLab/schedules/delete | 일정을 삭제합니다. |
> | 작업 | Microsoft.DevTestLab/schedules/read | 일정을 읽습니다. |
> | 작업 | Microsoft.DevTestLab/schedules/write | 일정을 추가하거나 수정합니다. |
> | 작업 | Microsoft.DevTestLab/schedules/Execute/action | 일정을 실행합니다. |
> | 작업 | Microsoft.DevTestLab/schedules/Retarget/action | 일정의 대상 리소스 ID를 업데이트합니다. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

Azure 서비스: [Azure 코스모스 DB](../cosmos-db/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DocumentDB/register/action |  구독에 대한 Microsoft DocumentDB 리소스 공급자를 등록합니다. |
> |  | **databaseAccountNames** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccountNames/read | 이름 가용성을 확인합니다. |
> |  | **데이터베이스계정** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/read | 데이터베이스 계정을 읽습니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/write | 데이터베이스 계정을 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 데이터베이스 계정의 키를 나열합니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 데이터베이스 계정의 키를 순환합니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 데이터베이스 계정에 대한 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 데이터베이스 계정의 리소스 그룹을 변경합니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 데이터베이스 계정 영역의 장애 조치 우선 순위를 변경합니다. 수동 장애 조치 작업을 수행하는 데 사용됩니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 데이터베이스 계정의 영역을 오프라인으로 설정합니다.  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 데이터베이스 계정의 영역을 온라인으로 설정합니다. |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/delete | 데이터베이스 계정을 삭제합니다. |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/getBackupPolicy/작업 | 데이터베이스 계정의 백업 정책 받기 |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/restore/action | 복원 요청 제출 |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/backup/action | 백업 구성하는 요청 제출 |
> |  | **데이터베이스계정/카산드라키스페이스** |  |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/카산드라키스페이스/쓰기 | 카산드라 키스페이스를 만듭니다. |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/카산드라키스페이스/읽기 | 카산드라 키스페이스를 읽거나 모든 카산드라 키스페이스를 나열합니다. |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/카산드라키스페이스/삭제 | 카산드라 키스페이스를 삭제합니다. |
> |  | **데이터베이스계정/카산드라키스페이스/작업결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 카산드라키 스페이스 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/카산드라키스페이스/테이블** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/카산드라키스페이스/테이블/쓰기 | 카산드라 테이블을 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 카산드라키 스페이스 / 테이블 / 읽기 | 카산드라 테이블을 읽거나 모든 카산드라 테이블을 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/카산드라키스페이스/테이블/삭제 | 카산드라 테이블을 삭제합니다. |
> |  | **데이터베이스계정/카산드라키스페이스/테이블/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/카산드라키스페이스/테이블/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/카산드라키스페이스/테이블/처리량설정** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/카산드라키스페이스/테이블/처리량설정/쓰기 | 카산드라 테이블 처리량을 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/카산드라키스페이스/테이블/처리량설정/읽기 | 카산드라 테이블 처리량을 읽어보십시오. |
> |  | **데이터베이스계정/카산드라키스페이스/테이블/처리량설정/작업결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 카산드라키 스페이스 / 테이블 / 처리량설정 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/카산드라키스페이스/처리량설정** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 카산드라키 스페이스 / 처리량설정 / 쓰기 | 카산드라 키스페이스 처리량을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 카산드라키 스페이스 / 처리량설정 / 읽기 | 카산드라 키스페이스 처리량을 읽어보십시오. |
> |  | **데이터베이스계정/카산드라키스페이스/처리량설정/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 카산드라키 스페이스 / 처리량설정 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/메트릭정의** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 컬렉션 메트릭 정의를 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 컬렉션 메트릭을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/파티션키레인지Id/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 데이터베이스 계정 파티션 키 수준 메트릭을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/파티션** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 컬렉션의 데이터베이스 계정 파티션을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/파티션/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 데이터베이스 계정 파티션 수준 메트릭을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/파티션/사용** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 데이터베이스 계정 파티션 수준 사용량을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/컬렉션/사용** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 컬렉션 사용 현황을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/메트릭정의** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 데이터베이스 메트릭 정의를 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 데이터베이스 메트릭을 읽습니다. |
> |  | **데이터베이스계정/데이터베이스/사용법** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 데이터베이스 사용 현황을 읽습니다. |
> |  | **데이터베이스계정/그렘린데이터베이스** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 쓰기 | 그렘린 데이터베이스를 만듭니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 읽기 | 그렘린 데이터베이스를 읽거나 모든 그렘린 데이터베이스를 나열합니다. |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/그렘린데이터베이스/삭제 | 그렘린 데이터베이스를 삭제합니다. |
> |  | **데이터베이스계정/그렘린데이터베이스/그래프** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 쓰기 | 그렘린 그래프를 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 읽기 | 그렘린 그래프를 읽거나 모든 그렘린 그래프를 나열합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 삭제 | 그렘린 그래프를 삭제합니다. |
> |  | **데이터베이스계정/그렘린데이터베이스/그래프/작업결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/그렘린데이터베이스/그래프/처리량설정** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 처리량설정 / 쓰기 | 그렘린 그래프 처리량을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 처리량설정 / 읽기 | 그렘린 그래프 처리량을 읽어보십시오. |
> |  | **데이터베이스계정/그렘린데이터베이스/그래프/처리량설정/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 그래프 / 처리량설정 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/그렘린데이터베이스/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/그렘린데이터베이스/처리량설정** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 처리량설정 / 쓰기 | 그렘린 데이터베이스 처리량을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 처리량설정 / 읽기 | 그렘린 데이터베이스 처리량을 읽어보십시오. |
> |  | **데이터베이스계정/그렘린데이터베이스/처리량설정/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 그렘린 데이터베이스 / 처리량설정 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/메트릭정의** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 데이터베이스 계정 메트릭 정의를 읽습니다. |
> |  | **데이터베이스계정/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/metrics/read | 데이터베이스 계정 메트릭을 읽습니다. |
> |  | **데이터베이스계정/몽고브데이터베이스** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몬고드 데이터베이스 / 쓰기 | MongoDB 데이터베이스를 만듭니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몽고브 데이터베이스 / 읽기 | MongoDB 데이터베이스를 읽거나 모든 MongoDB 데이터베이스를 나열합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몽고드 데이터베이스 / 삭제 | MongoDB 데이터베이스를 삭제합니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/컬렉션** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몽고브 데이터베이스 / 컬렉션 / 쓰기 | MongoDB 컬렉션을 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몽고브 데이터베이스 / 컬렉션 / 읽기 | MongoDB 컬렉션을 읽거나 모든 MongoDB 컬렉션을 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/몽고드데이터베이스/컬렉션/삭제 | MongoDB 컬렉션을 삭제합니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/컬렉션/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / mongodb데이터베이스 / 컬렉션 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/컬렉션/처리량설정** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / mongodb데이터베이스 / 컬렉션 / 처리량설정 / 쓰기 | MongoDB 컬렉션 처리량을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몽고브 데이터베이스 / 컬렉션 / 처리량설정 / 읽기 | MongoDB 컬렉션 처리량을 읽습니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/컬렉션/처리량설정/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / mongodb데이터베이스 / 컬렉션 / 처리량설정 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몽고브 데이터베이스 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/처리량설정** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몬고드 데이터베이스 / 처리량설정 / 쓰기 | MongoDB 데이터베이스 처리량을 업데이트합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 몬고드 데이터베이스 / 처리량설정 / 읽기 | MongoDB 데이터베이스 처리량을 읽습니다. |
> |  | **데이터베이스계정/몽고브데이터베이스/처리량설정/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / mongodb데이터베이스 / 처리량설정 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/노트북워크스페이스** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 노트북워크 스페이스 / 쓰기 | 전자 필기장 작업 영역 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/노트북워크스페이스/읽기 | 노트북 작업 공간 읽기 |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/노트북워크스페이스/삭제 | 노트북 작업 영역 삭제 |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/노트북워크스페이스/목록연결정보/액션 | 전자 필기장 작업 영역에 대한 연결 정보 나열 |
> |  | **데이터베이스계정/노트북작업공간/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/노트북작업 공간/작업결과/읽기 | 노트북 작업 공간에서 비동기 작업 상태 읽기 |
> |  | **데이터베이스계정/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 비동기 작업 상태를 읽습니다. |
> |  | **데이터베이스계정/백분위수** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/percentile/read | 복제 대기 시간의 백분위 수를 읽습니다. |
> |  | **데이터베이스계정/백분위수/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 대기 시간 메트릭을 읽습니다. |
> |  | **데이터베이스계정/백분위수/소스지역/대상지역/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 특정 소스 및 대상 지역에 대한 대기 시간 메트릭을 읽습니다. |
> |  | **데이터베이스계정/백분위수/대상지역/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 특정 대상 지역에 대한 대기 시간 메트릭을 읽습니다. |
> |  | **데이터베이스계정/사설엔드포인트커넥션** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 개인엔드 포인트 연결 Proxies / 읽기 | 데이터베이스 계정의 개인 끝점 연결 프록시 읽기 |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 개인엔드 포인트 연결 Proxies / 쓰기 | 데이터베이스 계정의 개인 끝점 연결 프록시 만들기 또는 업데이트 |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 개인엔드 포인트 연결 Proxies / 삭제 | 데이터베이스 계정의 개인 끝점 연결 프록시 삭제 |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 개인엔드 포인트 연결Proxies / 유효성 검사 / 작업 | 데이터베이스 계정의 개인 끝점 연결 프록시 유효성 검사 |
> |  | **데이터베이스계정/사설엔드포인트커넥션프록시스/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 개인엔드 포인트 연결Proxies / 작업결과 / 읽기 | 개인 엔드포인트 연결 프록시 비동기 작업의 상태 읽기 |
> |  | **데이터베이스계정/프라이빗엔드포인트연결** |  |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/개인엔드포인트연결/읽기 | 개인 끝점 연결을 읽거나 데이터베이스 계정의 모든 개인 끝점 연결을 나열합니다. |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/개인엔드포인트연결/쓰기 | 데이터베이스 계정의 개인 끝점 연결 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/개인엔드포인트연결/삭제 | 데이터베이스 계정의 개인 끝점 연결 삭제 |
> |  | **데이터베이스계정/사설엔드포인트연결/운영결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / 개인엔드 포인트 연결 / 작업결과 / 읽기 | 읽기 개인엔드포인트코넨션 비동기 작업 상태 읽기 |
> |  | **데이터베이스계정/개인링크자원** |  |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/개인링크자원/읽기 | 개인 링크 리소스 읽기 또는 데이터베이스 계정의 모든 개인 링크 리소스 나열 |
> |  | **데이터베이스계정/읽기 전용 키** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> |  | **데이터베이스계정/지역/데이터베이스/컬렉션/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 지역 컬렉션 메트릭을 읽습니다. |
> |  | **데이터베이스계정/지역/데이터베이스/컬렉션/파티션키레인지Id/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 지역 데이터베이스 계정 파티션 키 수준 메트릭을 읽습니다. |
> |  | **데이터베이스계정/지역/데이터베이스/컬렉션/파티션** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 컬렉션의 지역 데이터베이스 계정 파티션을 읽습니다. |
> |  | **데이터베이스계정/지역/데이터베이스/컬렉션/파티션/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 지역 데이터베이스 계정 파티션 수준 메트릭을 읽습니다. |
> |  | **데이터베이스계정/지역/메트릭** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 지역 및 데이터베이스 계정 메트릭을 읽습니다. |
> |  | **데이터베이스계정/sqlDatabases** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / sql데이터베이스 / 쓰기 | SQL 데이터베이스를 만듭니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / sql데이터베이스 / 읽기 | SQL 데이터베이스를 읽거나 모든 SQL 데이터베이스를 나열합니다. |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / sql데이터베이스 / 삭제 | SQL 데이터베이스를 삭제합니다. |
> |  | **데이터베이스계정/sql데이터베이스/컨테이너** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/쓰기 | SQL 컨테이너를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/읽기 | SQL 컨테이너를 읽거나 모든 SQL 컨테이너를 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/삭제 | SQL 컨테이너를 삭제합니다. |
> |  | **데이터베이스계정/sql데이터베이스/컨테이너/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/sql데이터베이스/컨테이너/저장프로시즈** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/저장프로시저/쓰기 | SQL 저장 프로시저를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/저장프로시저/읽기 | SQL 저장 프로시저를 읽거나 모든 SQL 저장 프로시저를 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/저장프로시서/삭제 | SQL 저장 프로시저를 삭제합니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/저장절차/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/저장프로시서/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/처리량설정** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/처리량설정/쓰기 | SQL 컨테이너 처리량을 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/처리량설정/읽기 | SQL 컨테이너 처리량을 읽습니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/처리량설정/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/처리량설정/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/트리거** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/트리거/쓰기 | SQL 트리거를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/트리거/읽기 | SQL 트리거를 읽거나 모든 SQL 트리거를 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/트리거/삭제 | SQL 트리거를 삭제합니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/트리거/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/트리거/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/사용자정의 기능** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/사용자정의 기능/쓰기 | SQL 사용자 정의 함수를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/사용자정의 기능/읽기 | SQL 사용자 정의 함수를 읽거나 모든 SQL 사용자 정의 함수를 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/사용자정의 기능/삭제 | SQL 사용자 정의 함수를 삭제합니다. |
> |  | **데이터베이스계정/sqlDatabase/컨테이너/사용자정의 기능/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/컨테이너/사용자정의 기능/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/sql데이터베이스/작업결과** |  |
> | 작업 | 마이크로 소프트.DocumentDB / 데이터베이스계정 / sql데이터베이스 / 작업결과 / 읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/sqlDatabase/처리량설정** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/처리량설정/쓰기 | SQL 데이터베이스 처리량을 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/처리량설정/읽기 | SQL 데이터베이스 처리량을 읽습니다. |
> |  | **데이터베이스계정/sql데이터베이스/처리량설정/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/sqlDatabase/처리량설정/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/테이블** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/테이블/쓰기 | 테이블을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.DocumentDB/데이터베이스계정/테이블/읽기 | 테이블을 읽거나 모든 테이블을 나열합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/테이블/삭제 | 테이블을 삭제합니다. |
> |  | **데이터베이스계정/표/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/테이블/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/표/처리량설정** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/테이블/처리량설정/쓰기 | 테이블 처리량을 업데이트합니다. |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/테이블/처리량설정/읽기 | 테이블 처리량을 읽습니다. |
> |  | **데이터베이스계정/표/처리량설정/작업결과** |  |
> | 작업 | Microsoft.DocumentDB/데이터베이스계정/테이블/처리량설정/작업결과/읽기 | 비동기 작업의 상태를 읽습니다. |
> |  | **데이터베이스계정/사용법** |  |
> | 작업 | Microsoft.DocumentDB/databaseAccounts/usages/read | 데이터베이스 계정 사용 현황을 읽습니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.DocumentDB에 가상 네트워크 또는 서브넷이 삭제됨을 알립니다. |
> |  | **위치/삭제가상네트워크또는서브넷/운영결과** |  |
> | 작업 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | deleteVirtualNetworkOrSubnets 비동기 작업의 상태를 읽습니다. |
> |  | **위치/작업상태** |  |
> | 작업 | 마이크로소프트.문서DB/위치/작업상태/읽기 | 비동기 작업의 상태 읽기 |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.DocumentDB/operationResults/read | 비동기 작업 상태를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.DocumentDB/operations/read | Microsoft DocumentDB에 사용 가능한 작업을 읽습니다.  |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

Azure 서비스: [앱 서비스](../app-service/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.DomainRegistration/generateSsoRequest/Action | 도메인 제어 센터에 대한 로그인 요청을 생성합니다. |
> | 작업 | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 도메인 구매 개체를 제출하지 않고 유효성을 검사합니다. |
> | 작업 | Microsoft.DomainRegistration/checkDomainAvailability/Action | 도메인을 구매할 수 있는지를 확인합니다. |
> | 작업 | Microsoft.DomainRegistration/listDomainRecommendations/Action | 키워드를 기준으로 목록 도메인 권장 사항을 검색합니다. |
> | 작업 | Microsoft.DomainRegistration/register/action | 구독에 대한 Microsoft 도메인 리소스 공급자를 등록합니다. |
> |  | **도메인** |  |
> | 작업 | Microsoft.DomainRegistration/domains/Read | 도메인 목록을 가져옵니다. |
> | 작업 | Microsoft.DomainRegistration/domains/Read | 도메인을 가져옵니다. |
> | 작업 | Microsoft.DomainRegistration/domains/Write | 새 도메인을 추가하거나 기존 도메인을 업데이트합니다. |
> | 작업 | Microsoft.DomainRegistration/domains/Delete | 기존 도메인을 삭제합니다. |
> | 작업 | Microsoft.DomainRegistration/domains/renew/Action | 기존 도메인을 갱신합니다. |
> |  | **도메인/도메인 소유권 식별자** |  |
> | 작업 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 소유권 식별자를 나열합니다. |
> | 작업 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 소유권 식별자를 가져옵니다. |
> | 작업 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 업데이트 식별자를 만듭니다. |
> | 작업 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 소유권 식별자를 삭제합니다. |
> |  | **도메인/작업 결과** |  |
> | 작업 | Microsoft.DomainRegistration/domains/operationresults/Read | 도메인 작업을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.DomainRegistration/operations/Read | 앱 서비스 도메인 등록의 모든 작업을 나열합니다. |
> |  | **topLevelDomains** |  |
> | 작업 | Microsoft.DomainRegistration/topLevelDomains/Read | 최상위 도메인을 가져옵니다. |
> | 작업 | Microsoft.DomainRegistration/topLevelDomains/Read | 최상위 도메인을 가져옵니다. |
> | 작업 | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 규약 작업을 나열합니다. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

Azure 서비스: [이벤트 그리드](../event-grid/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.EventGrid/register/action | EventGrid 리소스 공급자에 대한 구독을 등록합니다. |
> | 작업 | Microsoft.EventGrid/unregister/action | EventGrid 리소스 공급자에 대한 구독을 등록 취소합니다. |
> |  | **도메인** |  |
> | 작업 | Microsoft.EventGrid/domains/write | 도메인을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.EventGrid/domains/read | 도메인을 읽습니다. |
> | 작업 | Microsoft.EventGrid/domains/delete | 도메인을 삭제합니다. |
> | 작업 | Microsoft.EventGrid/domains/listKeys/action | 도메인에 대한 키를 나열합니다. |
> | 작업 | Microsoft.EventGrid/domains/regenerateKey/action | 도메인에 대한 키를 다시 생성합니다. |
> |  | **도메인/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트연결프록시/유효성 검사/조치 | 도메인에 대한 프라이빗 엔드포인트연결프록시스 검증 |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트연결프록시스/읽기 | 도메인에 대한 프라이빗 엔드포인트연결프록시스 읽기 |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트커넥션프록시스/쓰기 | 도메인에 대한 프라이빗 엔드포인트연결프록시스 작성 |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트연결프록시/삭제 | 도메인에 대한 프라이빗 엔드포인트연결프록시스 삭제 |
> |  | **도메인/프라이빗엔드포인트연결** |  |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트 연결/읽기 | 도메인에 대한 프라이빗 엔드포인트 연결 읽기 |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트 연결/쓰기 | 도메인에 대한 개인 엔드포인트 연결 작성 |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인엔드포인트 연결/삭제 | 도메인에 대한 개인 엔드포인트 연결 삭제 |
> |  | **도메인/개인링크리소스** |  |
> | 작업 | 마이크로소프트.EventGrid/도메인/개인링크리소스/읽기 | 도메인에 대한 개인 링크 리소스 가져오기 또는 목록 |
> |  | **도메인/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | 마이크로소프트.EventGrid/도메인/공급자/마이크로소프트.인사이트/로그정의/읽기 | 진단 로그에 대한 액세스 허용 |
> |  | **도메인/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 도메인에 사용 가능한 메트릭을 가져옵니다. |
> |  | **domains/topics** |  |
> | 작업 | Microsoft.EventGrid/domains/topics/read | 도메인 토픽을 읽습니다. |
> | 작업 | 마이크로소프트.EventGrid/도메인/토픽/쓰기 | 도메인 토픽 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.EventGrid/도메인/토픽/삭제 | 도메인 주제 삭제 |
> |  | **eventSubscriptions** |  |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/write | 이벤트 구독 만들기 또는 업데이트 |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/read | 이벤트 구독 읽기 |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/delete | 이벤트 구독 삭제 |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 이벤트 구독의 전체 url을 가져옵니다. |
> |  | **이벤트 구독/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 이벤트 구독에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 이벤트 구독에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **이벤트 구독/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | eventSubscription에 사용 가능한 메트릭을 가져옵니다. |
> |  | **extensionTopics** |  |
> | 작업 | Microsoft.EventGrid/extensionTopics/read | extensionTopic을 읽습니다. |
> |  | **확장주제/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 토픽에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 토픽에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **확장주제/공급자/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 토픽에 사용 가능한 메트릭을 가져옵니다. |
> |  | **위치/이벤트 구독** |  |
> | 작업 | Microsoft.EventGrid/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.EventGrid/locations/operationResults/read | 국가별 작업의 결과를 읽습니다. |
> |  | **위치/작업상태** |  |
> | 작업 | Microsoft.EventGrid/locations/operationsStatus/read | 국가별 작업의 상태를 읽습니다. |
> |  | **위치/주제 유형/이벤트 구독** |  |
> | 작업 | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.EventGrid/operationResults/read | 작업의 결과를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.EventGrid/operations/read | EventGrid 작업을 나열합니다. |
> |  | **작업 상태** |  |
> | 작업 | Microsoft.EventGrid/operationsStatus/read | 작업의 상태를 읽습니다. |
> |  | **파트너 네임 스페이스** |  |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/쓰기 | 파트너 네임스페이스 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/읽기 | 파트너 네임스페이스 읽기 |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/삭제 | 파트너 네임스페이스 삭제 |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/목록 키/작업 | 파트너 네임스페이스에 대한 목록 키 |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/다시 생성키/작업 | 파트너 네임스페이스에 대한 키 재생성 |
> |  | **파트너네임스페이스/이벤트채널** |  |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/이벤트채널/읽기 | 이벤트 채널 읽기 |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/이벤트채널/쓰기 | 이벤트 채널 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.EventGrid/파트너네임 스페이스/이벤트채널/삭제 | 이벤트 채널 삭제 |
> |  | **파트너 등록** |  |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너등록/쓰기 | 파트너 등록 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너등록/읽기 | 파트너 등록 읽기 |
> | 작업 | 마이크로소프트.EventGrid/파트너등록/삭제 | 파트너 등록 삭제 |
> |  | **파트너 주제** |  |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너주제/읽기 | 파트너 주제 읽기 |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너주제/쓰기 | 파트너 항목 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너주제/삭제 | 파트너 주제 삭제 |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너주제/활성화/작업 | 파트너 주제 활성화 |
> | 작업 | 마이크로소프트.이벤트 그리드/파트너주제/비활성화/작업 | 파트너 주제 비활성화 |
> |  | **sku** |  |
> | 작업 | 마이크로소프트.이벤트 그리드/스쿠/읽기 | 이벤트 그리드 리소스에 대한 사용 가능한 Sku 정의 읽기 |
> |  | **시스템 주제** |  |
> | 작업 | 마이크로소프트.이벤트 그리드/시스템주제/읽기 | 시스템 주제 읽기 |
> | 작업 | 마이크로소프트.이벤트 그리드/시스템주제/쓰기 | 시스템 항목 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.이벤트 그리드/시스템주제/삭제 | 시스템 항목 삭제 |
> |  | **시스템주제/제공업체/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로소프트.EventGrid/시스템주제/공급자/마이크로소프트.인사이트/진단설정/읽기 | 시스템 항목에 대한 진단 설정 가져옵니다. |
> | 작업 | 마이크로소프트.EventGrid/시스템주제/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 시스템 항목에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **시스템주제/공급자/마이크로소프트.인사이트/로그정의** |  |
> | 작업 | 마이크로소프트.EventGrid/시스템주제/공급자/마이크로소프트.인사이트/로그정의/읽기 | 진단 로그에 대한 액세스 허용 |
> |  | **시스템주제/제공업체/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | 마이크로소프트.EventGrid/시스템주제/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | 시스템 항목에 사용할 수 있는 메트릭을 가져옵니다. |
> |  | **항목** |  |
> | 작업 | Microsoft.EventGrid/topics/write | 토픽을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.EventGrid/topics/read | 토픽을 읽습니다. |
> | 작업 | Microsoft.EventGrid/topics/delete | 항목 삭제 |
> | 작업 | Microsoft.EventGrid/topics/listKeys/action | 토픽에 대한 키를 나열합니다. |
> | 작업 | Microsoft.EventGrid/topics/regenerateKey/action | 토픽에 대한 키를 다시 생성합니다. |
> |  | **주제/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.EventGrid/토픽/프라이빗엔드포인트커넥션프록시스/유효성 검사/조치 | 주제에 대한 프라이빗 엔드포인트연결프록시스 검증 |
> | 작업 | 마이크로소프트.EventGrid/토픽/개인엔드포인트커넥션프록시스/읽기 | 주제에 대한 개인 엔드 포인트 연결 프록시읽기 |
> | 작업 | 마이크로소프트.EventGrid/토픽/개인엔드포인트커넥션프로시/쓰기 | 주제에 대한 개인 엔드 포인트 연결 프록시 작성 |
> | 작업 | 마이크로소프트.EventGrid/토픽/프라이빗엔드포인트커넥션프록시스/삭제 | 항목에 대한 개인 엔드 포인트 연결 프록시 삭제 |
> |  | **주제/프라이빗 엔드포인트연결** |  |
> | 작업 | 마이크로소프트.이벤트 그리드/토픽/프라이빗엔드포인트연결/읽기 | 주제에 대한 개인 엔드포인트 연결 읽기 |
> | 작업 | 마이크로소프트.EventGrid/토픽/프라이빗엔드포인트연결/쓰기 | 주제에 대한 개인 엔드포인트 연결 작성 |
> | 작업 | 마이크로소프트.EventGrid/토픽/프라이빗엔드포인트연결/삭제 | 항목에 대한 비공개 엔드포인트 연결 삭제 |
> |  | **주제/개인링크리소스** |  |
> | 작업 | 마이크로소프트.이벤트 그리드/토픽/개인링크자원/읽기 | 주제에 대한 개인 링크 자원 읽기 |
> |  | **주제/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 토픽에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 토픽에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **토픽/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | 마이크로소프트.EventGrid/토픽/공급자/마이크로소프트.인사이트/로그정의/읽기 | 진단 로그에 대한 액세스 허용 |
> |  | **토픽/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 토픽에 사용 가능한 메트릭을 가져옵니다. |
> |  | **주제 유형** |  |
> | 작업 | Microsoft.EventGrid/topictypes/read | 토픽을 읽습니다. |
> |  | **주제 유형/이벤트 구독** |  |
> | 작업 | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 항목 유형별로 글로벌 이벤트 구독을 나열합니다. |
> |  | **주제 유형/이벤트 유형** |  |
> | 작업 | Microsoft.EventGrid/topictypes/eventtypes/read | topictype에서 지원하는 eventtype을 읽습니다. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

Azure 서비스: [이벤트 허브](../event-hubs/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.EventHub/checkNamespaceAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailability를 대신 사용하세요. |
> | 작업 | Microsoft.EventHub/checkNameAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. |
> | 작업 | Microsoft.EventHub/register/action | EventHub 리소스 공급자에 대한 구독을 등록하고 EventHub 리소스를 만들도록 설정합니다. |
> | 작업 | Microsoft.EventHub/unregister/action | EventHub 리소스 공급자를 등록합니다. |
> |  | **사용 가능한 클러스터 리전** |  |
> | 작업 | 마이크로소프트.이벤트 허브/사용 가능한 클러스터리전/읽기 | Azure 리전에서 사용 가능한 사전 프로비저닝된 클러스터를 나열하기 위한 작업을 읽습니다. |
> |  | **클러스터** |  |
> | 작업 | Microsoft.EventHub/clusters/read | 클러스터 리소스 설명을 가져옵니다. |
> | 작업 | Microsoft.EventHub/clusters/write | 기존 클러스터 리소스를 만들거나 수정합니다. |
> | 작업 | 마이크로소프트.EventHub/클러스터/삭제 | 기존 클러스터 리소스를 삭제합니다. |
> |  | **클러스터/네임스페이스** |  |
> | 작업 | Microsoft.EventHub/클러스터/네임스페이스/읽기 | 클러스터 내의 네임스페이스에 대한 네임스페이스 Azure 리소스 관리자 아이디를 나열합니다. |
> |  | **클러스터/작업 결과** |  |
> | 작업 | Microsoft.EventHub/클러스터/작업 결과/읽기 | 비동기 클러스터 작업의 상태를 가져옵니다. |
> |  | **클러스터/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 클러스터 메트릭 리소스 설명의 목록을 가져옵니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 지정된 VNet에 대한 EventHub 리소스 공급자의 VNet 규칙을 삭제합니다. |
> |  | **namespaces** |  |
> | 작업 | Microsoft.EventHub/namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 작업 | Microsoft.EventHub/namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 작업 | Microsoft.EventHub/namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 작업 | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS 네임스페이스를 제거합니다. |
> |  | **네임스페이스/권한 부여규칙** |  |
> | 작업 | Microsoft.EventHub/namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 작업 | Microsoft.EventHub/namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 작업 | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **네임스페이스/재해 복구 구성** |  |
> | 작업 | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다. |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 네임스페이스와 연결된 재해 복구 구성을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다. |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다. |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다. |
> |  | **네임스페이스/재해복구구성/권한 부여규칙** |  |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다. |
> |  | **namespaces/eventhubs** |  |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/write | EventHub 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/read | EventHub 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/Delete | EventHub 리소스 삭제 작업 |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | EventHub를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> |  | **namespaces/eventhubs/authorizationRules** |  |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  EventHub 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | EventHub 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | EventHub 권한 부여 규칙 삭제 작업 |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | EventHub의 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **네임스페이스/이벤트허브/소비자 그룹** |  |
> | 작업 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | ConsumerGroup 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | ConsumerGroup 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup 리소스 삭제 작업 |
> |  | **네임스페이스/ipFilterRules** |  |
> | 작업 | Microsoft.EventHub/namespaces/ipFilterRules/read | IP 필터 리소스 가져오기 |
> | 작업 | Microsoft.EventHub/namespaces/ipFilterRules/write | IP 필터 리소스 만들기 |
> | 작업 | Microsoft.EventHub/namespaces/ipFilterRules/delete | IP 필터 리소스 삭제 |
> |  | **네임스페이스/메시징플랜** |  |
> | 작업 | Microsoft.EventHub/namespaces/messagingPlan/read | 네임스페이스에 대한 메시지 계획을 가져옵니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 작업 | Microsoft.EventHub/namespaces/messagingPlan/write | 네임스페이스에 대한 메시지 계획을 업데이트합니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> |  | **네임스페이스/네트워크 규칙 집합** |  |
> | 작업 | Microsoft.EventHub/네임스페이스/네트워크 규칙 집합/읽기 | 네트워크규칙 집합 리소스 를 가져옵니다. |
> | 작업 | Microsoft.EventHub/네임스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.EventHub/네임스페이스/네트워크 규칙 집합/삭제 | VNET 규칙 리소스 삭제 |
> |  | **네임스페이스/네트워크 규칙 집합** |  |
> | 작업 | Microsoft.EventHub/네임스페이스/네트워크 규칙 집합/읽기 | 네트워크규칙 집합 리소스 를 가져옵니다. |
> | 작업 | Microsoft.EventHub/네임스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.EventHub/네임스페이스/네트워크 규칙 집합/삭제 | VNET 규칙 리소스 삭제 |
> |  | **네임스페이스/작업 결과** |  |
> | 작업 | Microsoft.EventHub/namespaces/operationresults/read | 네임스페이스 작업의 상태를 가져옵니다. |
> |  | **네임스페이스/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.EventHub/네임스페이스/프라이빗엔드포인트커넥션프록시/유효성 검사/액션 | 개인 엔드포인트 연결 프록시 유효성 검사 |
> | 작업 | 마이크로소프트.EventHub/네임스페이스/프라이빗엔드포인트커넥션프록시/읽기 | 개인 엔드포인트 연결 프록시 받기 |
> | 작업 | 마이크로소프트.EventHub/네임스페이스/프라이빗엔드포인트커넥션프록시/쓰기 | 프라이빗 엔드포인트 연결 프록시 작성 |
> | 작업 | 마이크로소프트.EventHub/네임스페이스/프라이빗엔드포인트커넥션프록시/삭제 | 개인 끝점 연결 프록시 삭제 |
> |  | **네임스페이스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/공급자/Microsoft.Insights/logDEFINITIONs** |  |
> | 작업 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 네임스페이스 로그 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/가상네트워크규칙** |  |
> | 작업 | Microsoft.EventHub/namespaces/virtualNetworkRules/read | VNET 규칙 리소스 가져오기 |
> | 작업 | Microsoft.EventHub/namespaces/virtualNetworkRules/write | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | VNET 규칙 리소스 삭제 |
> |  | **작업** |  |
> | 작업 | Microsoft.EventHub/operations/read | 작업을 가져옵니다. |
> |  | **sku** |  |
> | 작업 | Microsoft.EventHub/sku/read | Sku 리소스 설명의 목록을 가져옵니다. |
> |  | **스쿠 /지역** |  |
> | 작업 | Microsoft.EventHub/sku/regions/read | SkuRegions 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/메시지** |  |
> | DataAction | Microsoft.EventHub/네임스페이스/메시지/보내기/작업 | 메시지 보내기 |
> | DataAction | Microsoft.EventHub/네임스페이스/메시지/수신/작업 | 메시지 받기 |

## <a name="microsoftfeatures"></a>Microsoft.Features

Azure 서비스: [Azure 리소스 관리자](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Features/register/action | 구독 기능을 등록합니다. |
> |  | **기능** |  |
> | 작업 | Microsoft.Features/features/read | 구독 기능을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Features/operations/read | 작업 목록을 가져옵니다. |
> |  | **공급자/기능** |  |
> | 작업 | Microsoft.Features/providers/features/read | 지정된 리소스 공급자에서 구독의 기능을 가져옵니다. |
> | 작업 | Microsoft.Features/providers/features/register/action | 지정된 리소스 공급자에서 구독의 기능을 등록합니다. |
> | 작업 | Microsoft.Features/providers/features/unregister/action | 지정된 리소스 공급자에서 구독의 기능을 등록 취소합니다. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

Azure 서비스: [Azure 정책](../governance/policy/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.게스트 구성/등록/작업 | Microsoft.GuestConfiguration 리소스 공급자에 대 한 구독을 등록 합니다. |
> |  | **guestConfigurationAssignments** |  |
> | 작업 | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 새 게스트 구성 할당을 만듭니다. |
> | 작업 | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 게스트 구성 할당을 가져옵니다. |
> | 작업 | 마이크로소프트.게스트 구성/게스트구성할당/삭제 | 게스트 구성 할당을 삭제합니다. |
> |  | **게스트구성할당/보고서** |  |
> | 작업 | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 게스트 구성 할당 보고서를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.게스트 구성/작업/읽기 | Microsoft.GuestConfiguration 리소스 공급자에 대 한 작업을 가져옵니다. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

Azure 서비스: [HDInsight](../hdinsight/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.HDInsight/등록/작업 | 구독을 위해 HDInsight 리소스 공급자 등록 |
> | 작업 | 마이크로소프트.HDInsight/등록 취소/작업 | 구독을 위한 HDInsight 리소스 공급자 등록 취소 |
> |  | **클러스터** |  |
> | 작업 | Microsoft.HDInsight/clusters/write | HDInsight 클러스터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.HDInsight/clusters/read | HDInsight 클러스터에 대한 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.HDInsight/clusters/delete | HDInsight 클러스터를 삭제합니다. |
> | 작업 | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight 클러스터의 RDP 설정을 변경합니다. |
> | 작업 | 마이크로소프트.HDInsight/클러스터/getGatewaySettings/작업 | HDInsight 클러스터의 게이트웨이 설정 받기 |
> | 작업 | 마이크로소프트.HDInsight/클러스터/업데이트게이트웨이설정/액션 | HDInsight 클러스터의 게이트웨이 설정 업데이트 |
> | 작업 | Microsoft.HDInsight/clusters/configurations/action | HDInsight 클러스터 구성을 가져옵니다. |
> |  | **clusters/applications** |  |
> | 작업 | Microsoft.HDInsight/clusters/applications/read | HDInsight 클러스터용 애플리케이션을 가져옵니다. |
> | 작업 | Microsoft.HDInsight/clusters/applications/write | HDInsight 클러스터용 애플리케이션을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.HDInsight/clusters/applications/delete | HDInsight 클러스터용 애플리케이션을 삭제합니다. |
> |  | **클러스터/구성** |  |
> | 작업 | Microsoft.HDInsight/clusters/configurations/read | HDInsight 클러스터 구성을 가져옵니다. |
> |  | **클러스터/확장** |  |
> | 작업 | 마이크로소프트.HDInsight/클러스터/확장/쓰기 | HDInsight 클러스터를 위한 클러스터 확장 생성 |
> | 작업 | 마이크로소프트.HDInsight/클러스터/확장/읽기 | HDInsight 클러스터를 위한 클러스터 확장 받기 |
> | 작업 | 마이크로소프트.HDInsight/클러스터/확장/삭제 | HDInsight 클러스터에 대한 클러스터 확장 삭제 |
> |  | **클러스터/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 리소스 HDInsight 클러스터에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 리소스 HDInsight 클러스터에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **클러스터/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | HDInsight 클러스터에 사용 가능한 메트릭을 가져옵니다. |
> |  | **클러스터/역할** |  |
> | 작업 | Microsoft.HDInsight/clusters/roles/resize/action | HDInsight 클러스터 크기를 조정합니다. |
> |  | **위치/기능** |  |
> | 작업 | Microsoft.HDInsight/locations/capabilities/read | 구독 기능을 가져옵니다. |
> |  | **위치/확인이름사용 가능** |  |
> | 작업 | Microsoft.HDInsight/locations/checkNameAvailability/read | 이름 가용성을 확인합니다. |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

Azure 서비스: [Azure Arc](../azure-arc/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/레지스터/작업 | Microsoft.HybridCompute 리소스 공급자에 대 한 구독을 등록 합니다. |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/등록 취소/작업 | Microsoft.HybridCompute 리소스 공급자에 대 한 구독을 등록 취소 합니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/위치/작업 결과/읽기 | Microsoft.HybridCompute 리소스 공급자에서 작업 상태 읽기 |
> |  | **기계** |  |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/기계/읽기 | Azure Arc 컴퓨터 읽기 |
> | 작업 | 마이크로 소프트.하이브리드 컴퓨팅 / 기계 / 쓰기 | Azure Arc 컴퓨터 작성 |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/컴퓨터/삭제 | Azure Arc 컴퓨터 삭제 |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/컴퓨터/다시 연결/작업 | Azure Arc 컴퓨터를 다시 연결합니다. |
> |  | **기계/확장** |  |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/컴퓨터/확장/읽기 | Azure Arc 확장 읽기 |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/컴퓨터/확장/쓰기 | Azure Arc 확장 설치 또는 업데이트 |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/컴퓨터/확장/삭제 | Azure Arc 확장 삭제 |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.하이브리드 컴퓨팅/작업/읽기 | 서버에 대한 Azure Arc에 대한 모든 작업 읽기 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

Azure 서비스: [Azure 가져오기/내보내기](../storage/common/storage-import-export-service.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ImportExport/register/action | Import/Export 리소스 공급자에 대한 구독을 등록하고 Import/Export 작업을 만들도록 설정합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ImportExport/jobs/write | 지정된 매개 변수를 사용하여 작업을 만들거나 지정된 작업에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.ImportExport/jobs/read | 지정된 작업에 대한 속성을 가져오거나 작업 목록을 반환합니다. |
> | 작업 | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 지정된 작업에 대한 BitLocker 키를 가져옵니다. |
> | 작업 | Microsoft.ImportExport/jobs/delete | 기존 작업을 삭제합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.ImportExport/locations/read | 지정된 위치에 대한 속성을 가져오거나 위치 목록을 반환합니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.가져오기내보내기/작업/읽기 | 리소스 공급자가 지원하는 작업을 가져옵니다. |

## <a name="microsoftinsights"></a>Microsoft.Insights

Azure 서비스: [Azure 모니터](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Insights/Metrics/Action | 메트릭 작업 |
> | 작업 | Microsoft.Insights/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | 작업 | Microsoft.Insights/Unregister/Action | Microsoft Insights 공급자를 등록합니다. |
> | 작업 | Microsoft.Insights/ListMigrationDate/Action | 구독 마이그레이션 날짜를 뒤로 돌립니다. |
> | 작업 | Microsoft.Insights/MigrateToNewpricingModel/Action | 구독을 새로운 가격 책정 모델로 마이그레이션합니다. |
> | 작업 | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 구독을 레거시 가격 책정 모델로 롤백합니다. |
> |  | **작업 그룹** |  |
> | 작업 | Microsoft.Insights/ActionGroups/Write | 작업 그룹을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/ActionGroups/Delete | 작업 그룹을 삭제합니다. |
> | 작업 | Microsoft.Insights/ActionGroups/Read | 작업 그룹을 읽습니다. |
> |  | **활동 로그 경고** |  |
> | 작업 | Microsoft.Insights/ActivityLogAlerts/Write | 활동 로그 경고를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/ActivityLogAlerts/Delete | 활동 로그 경고를 삭제합니다. |
> | 작업 | Microsoft.Insights/ActivityLogAlerts/Read | 활동 로그 경고를 읽습니다. |
> | 작업 | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 활동 로그 경고가 활성화됩니다. |
> |  | **경고 규칙** |  |
> | 작업 | Microsoft.Insights/AlertRules/Write | 클래식 메트릭 경고를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/AlertRules/Delete | 클래식 메트릭 경고를 삭제합니다. |
> | 작업 | Microsoft.Insights/AlertRules/Read | 클래식 메트릭 경고를 읽습니다. |
> | 작업 | Microsoft.Insights/AlertRules/Activated/Action | 클래식 메트릭 경고가 활성화되었습니다. |
> | 작업 | Microsoft.Insights/AlertRules/Resolved/Action | 클래식 메트릭 경고가 해결되었습니다. |
> | 작업 | Microsoft.Insights/AlertRules/Throttled/Action | 클래식 메트릭 경고 규칙이 제한되었습니다. |
> |  | **경고 규칙/인시던트** |  |
> | 작업 | Microsoft.Insights/AlertRules/Incidents/Read | 클래식 메트릭 경고 인시던트를 읽습니다. |
> |  | **자동 크기 조정 설정** |  |
> | 작업 | Microsoft.Insights/AutoscaleSettings/Write | 자동 크기 조정 설정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/Delete | 자동 크기 조정 설정을 삭제합니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/Read | 자동 크기 조정 설정을 읽습니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 자동 크기 조정 규모 확대가 시작되었습니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 자동 크기 조정 규모 축소가 시작되었습니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 자동 크기 조정 규모 확대가 완료되었습니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 자동 크기 조정 규모 축소가 완료되었습니다. |
> |  | **자동 조정설정/공급자/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 리소스 진단 설정을 읽습니다. |
> | 작업 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 리소스 진단 설정을 만들거나 업데이트합니다. |
> |  | **자동 크기 조정 설정/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 로그 정의 읽기 |
> |  | **자동 크기 조정 설정/공급자/Microsoft.인사이트/메트릭 정의** |  |
> | 작업 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 메트릭 정의 읽기 |
> |  | **기준선** |  |
> | 작업 | 마이크로소프트.인사이트/기준선/읽기 | 메트릭 기준 읽기(미리 보기) |
> |  | **계산기준선** |  |
> | 작업 | 마이크로소프트.인사이트/계산기준/읽기 | 메트릭 값에 대한 기준 계산(미리 보기) |
> |  | **구성 요소** |  |
> | 작업 | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 분석 테이블 작업입니다. |
> | 작업 | Microsoft.Insights/Components/ApiKeys/Action | Application Insights API 키를 생성합니다. |
> | 작업 | Microsoft.Insights/components/purge/action | Application Insights에서 데이터 삭제 |
> | 작업 | 마이크로소프트.인사이트/구성 요소/데일리캡도달/액션 | 애플리케이션 인사이트 구성 요소의 일일 한도 에 도달했습니다. |
> | 작업 | 마이크로소프트.인사이트/구성 요소/데일리캡경고임계값도달/조치 | 애플리케이션 인사이트 구성 요소에 대한 일일 한도 경고 임계값에 도달했습니다. |
> | 작업 | Microsoft.Insights/Components/Write | Application Insights 구성 요소 구성에 씁니다. |
> | 작업 | Microsoft.Insights/Components/Delete | Application Insights 구성 요소 구성을 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/Read | Application Insights 구성 요소 구성을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 내보내기 설정 작업입니다. |
> | 작업 | Microsoft.Insights/Components/Move/Action | Application Insights 구성 요소를 다른 리소스 그룹 또는 구독으로 이동합니다. |
> |  | **구성 요소/분석항목** |  |
> | 작업 | Microsoft.Insights/Components/AnalyticsItems/Delete | Application Insights 분석 항목을 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/AnalyticsItems/Read | Application Insights 분석 항목을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/AnalyticsItems/Write | Application Insights 분석 항목을 작성합니다. |
> |  | **구성 요소/분석 테이블** |  |
> | 작업 | Microsoft.Insights/Components/AnalyticsTables/Delete | Application Insights 분석 테이블 스키마를 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/AnalyticsTables/Read | Application Insights 분석 테이블 스키마를 읽습니다. |
> | 작업 | Microsoft.Insights/Components/AnalyticsTables/Write | Application Insights 분석 테이블 스키마를 작성합니다. |
> |  | **구성요소/주석** |  |
> | 작업 | Microsoft.Insights/Components/Annotations/Delete | Application Insights 주석을 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/Annotations/Read | Application Insights 주석을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/Annotations/Write | Application Insights 주석을 작성합니다. |
> |  | **구성 요소/API** |  |
> | 작업 | Microsoft.Insights/Components/Api/Read | Application Insights 구성 요소 데이터 API를 읽습니다. |
> |  | **구성 요소/Api키** |  |
> | 작업 | Microsoft.Insights/Components/ApiKeys/Delete | Application Insights API 키를 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/ApiKeys/Read | Application Insights API 키를 읽습니다. |
> |  | **구성 요소/청구계획포컴포컴포컴포** |  |
> | 작업 | Microsoft.Insights/Components/BillingPlanForComponent/Read | Application Insights 구성 요소에 대한 청구 계획을 읽습니다. |
> |  | **구성 요소/현재 청구 기능** |  |
> | 작업 | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights 구성 요소에 대한 현재 청구 기능을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights 구성 요소에 대한 현재 청구 기능을 작성합니다. |
> |  | **구성 요소/기본 작업항목 구성 요소** |  |
> | 작업 | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Application Insights 기본 ALM 통합 구성을 읽습니다. |
> |  | **구성 요소/이벤트** |  |
> | 작업 | Microsoft.Insights/Components/Events/Read | OData 쿼리 형식을 사용하여 Application Insights에서 로그를 가져옵니다. |
> |  | **구성 요소/내보내기 구성** |  |
> | 작업 | Microsoft.Insights/Components/ExportConfiguration/Delete | Application Insights 내보내기 설정을 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/ExportConfiguration/Read | Application Insights 내보내기 설정을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/ExportConfiguration/Write | Application Insights 내보내기 설정을 작성합니다. |
> |  | **구성 요소/확장쿼리** |  |
> | 작업 | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights 구성 요소 확장 쿼리 결과를 읽습니다. |
> |  | **구성 요소/즐겨찾기** |  |
> | 작업 | Microsoft.Insights/Components/Favorites/Delete | Application Insights 즐겨찾기를 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/Favorites/Read | Application Insights 즐겨찾기를 읽습니다. |
> | 작업 | Microsoft.Insights/Components/Favorites/Write | Application Insights 즐겨찾기를 작성합니다. |
> |  | **구성 요소/기능 기능** |  |
> | 작업 | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights 구성 요소 기능을 읽습니다. |
> |  | **구성 요소/사용 가능한 결제 기능** |  |
> | 작업 | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 청구 기능에 사용 가능한 Application Insights 구성 요소를 읽습니다. |
> |  | **구성 요소/GetToken** |  |
> | 작업 | Microsoft.Insights/Components/GetToken/Read | Application Insights 구성 요소 토큰을 읽습니다. |
> |  | **구성 요소/메트릭 정의** |  |
> | 작업 | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights 구성 요소 메트릭 정의를 읽습니다. |
> |  | **구성 요소/메트릭** |  |
> | 작업 | Microsoft.Insights/Components/Metrics/Read | Application Insights 구성 요소 메트릭을 읽습니다. |
> |  | **구성 요소/마이애널리틱스항목** |  |
> | 작업 | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Application Insights 개인 분석 항목을 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/MyAnalyticsItems/Write | Application Insights 개인 분석 항목을 작성합니다. |
> | 작업 | Microsoft.Insights/Components/MyAnalyticsItems/Read | Application Insights 개인 분석 항목을 읽습니다. |
> |  | **구성 요소/즐겨찾기** |  |
> | 작업 | Microsoft.Insights/Components/MyFavorites/Read | Application Insights 개인 즐겨찾기를 읽습니다. |
> |  | **구성 요소/작업** |  |
> | 작업 | Microsoft.Insights/Components/Operations/Read | Application Insights에서 장기 실행 작업의 상태를 가져옵니다. |
> |  | **구성 요소/가격 책정 계획** |  |
> | 작업 | Microsoft.Insights/Components/PricingPlans/Read | Application Insights 구성 요소 가격 책정 계획을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/PricingPlans/Write | Application Insights 구성 요소 가격 책정 계획을 작성합니다. |
> |  | **구성 요소/사전 감지 구성 요소** |  |
> | 작업 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Application Insights 자동 관리 검색 구성을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Application Insights 자동 관리 검색 구성을 작성합니다. |
> |  | **구성 요소/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | 마이크로소프트.인사이트/구성 요소/공급자/마이크로소프트.인사이트/진단설정/읽기 | 리소스 진단 설정을 읽습니다. |
> | 작업 | 마이크로소프트.인사이트/구성 요소/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 리소스 진단 설정을 만들거나 업데이트합니다. |
> |  | **구성 요소/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | 마이크로소프트.인사이트/구성 요소/공급자/마이크로소프트.인사이트/로그정의/읽기 | 로그 정의 읽기 |
> |  | **구성 요소/공급자/Microsoft.인사이트/메트릭 정의** |  |
> | 작업 | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 메트릭 정의 읽기 |
> |  | **구성 요소/쿼리** |  |
> | 작업 | Microsoft.Insights/Components/Query/Read | Application Insights 로그에 대해 쿼리를 실행합니다. |
> |  | **구성 요소/할당량 상태** |  |
> | 작업 | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights 구성 요소 할당량 상태를 읽습니다. |
> |  | **구성 요소/합성 모니터 위치** |  |
> | 작업 | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights 웹 테스트 위치를 읽습니다. |
> |  | **구성 요소/웹 테스트** |  |
> | 작업 | Microsoft.Insights/Components/Webtests/Read | 웹 테스트 구성을 읽습니다. |
> |  | **구성 요소/작업항목 구성 요소** |  |
> | 작업 | Microsoft.Insights/Components/WorkItemConfigs/Delete | Application Insights ALM 통합 구성을 삭제합니다. |
> | 작업 | Microsoft.Insights/Components/WorkItemConfigs/Read | Application Insights ALM 통합 구성을 읽습니다. |
> | 작업 | Microsoft.Insights/Components/WorkItemConfigs/Write | Application Insights ALM 통합 구성을 작성합니다. |
> |  | **데이터 컬렉션규칙협회** |  |
> | 작업 | 마이크로소프트.인사이트/데이터 컬렉션규칙협회/읽기 | 데이터 수집 규칙과 리소스 연결 읽기 |
> | 작업 | 마이크로소프트.인사이트/데이터 컬렉션규칙연결/쓰기 | 데이터 수집 규칙과 리소스 연결을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.인사이트/데이터 컬렉션규칙연결/삭제 | 데이터 수집 규칙과 리소스 연결 삭제 |
> |  | **데이터 컬렉션규칙** |  |
> | 작업 | 마이크로소프트.인사이트/데이터 컬렉션규칙/읽기 | 데이터 수집 규칙 읽기 |
> | 작업 | 마이크로소프트.인사이트/데이터 컬렉션규칙/쓰기 | 데이터 수집 규칙 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/데이터 컬렉션규칙/삭제 | 데이터 수집 규칙 삭제 |
> |  | **진단 설정** |  |
> | 작업 | Microsoft.Insights/DiagnosticSettings/Write | 리소스 진단 설정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/DiagnosticSettings/Delete | 리소스 진단 설정을 삭제합니다. |
> | 작업 | Microsoft.Insights/DiagnosticSettings/Read | 리소스 진단 설정을 읽습니다. |
> |  | **이벤트 범주** |  |
> | 작업 | Microsoft.Insights/EventCategories/Read | 사용 가능한 활동 로그 이벤트 범주를 읽습니다. |
> |  | **이벤트 유형/다이제스트 이벤트** |  |
> | 작업 | Microsoft.Insights/eventtypes/digestevents/Read | 관리 이벤트 유형 다이제스트 읽기 |
> |  | **이벤트 유형/값** |  |
> | 작업 | Microsoft.Insights/eventtypes/values/Read | 활동 로그 이벤트를 읽습니다. |
> |  | **확장진단설정** |  |
> | 작업 | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 네트워크 흐름 로그 진단 설정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 네트워크 흐름 로그 진단 설정을 삭제합니다. |
> | 작업 | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 네트워크 흐름 로그 진단 설정을 읽습니다. |
> |  | **목록마이그레이션날짜** |  |
> | 작업 | Microsoft.Insights/ListMigrationDate/Read | 구독 마이그레이션 날짜를 뒤로 돌립니다. |
> |  | **로그 정의** |  |
> | 작업 | Microsoft.Insights/LogDefinitions/Read | 로그 정의 읽기 |
> |  | **로그 프로파일** |  |
> | 작업 | Microsoft.Insights/LogProfiles/Write | 활동 로그의 로그 프로필을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/LogProfiles/Delete | 활동 로그의 로그 프로필을 삭제합니다. |
> | 작업 | Microsoft.Insights/LogProfiles/Read | 활동 로그의 로그 프로필을 읽습니다. |
> |  | **로그** |  |
> | 작업 | Microsoft.Insights/Logs/Read | 모든 로그에서 데이터를 읽습니다. |
> |  | **로그/AD평가 추천** |  |
> | 작업 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | ADAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/AD복제결과** |  |
> | 작업 | Microsoft.Insights/Logs/ADReplicationResult/Read | ADReplicationResult 테이블에서 데이터를 읽습니다. |
> |  | **로그/AD보안평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | ADSecurityAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/경고** |  |
> | 작업 | Microsoft.Insights/Logs/Alert/Read | Alert 테이블에서 데이터를 읽습니다. |
> |  | **로그/경고 기록** |  |
> | 작업 | Microsoft.Insights/Logs/AlertHistory/Read | AlertHistory 테이블에서 데이터를 읽습니다. |
> |  | **로그/애플리케이션인사이트** |  |
> | 작업 | Microsoft.Insights/Logs/ApplicationInsights/Read | ApplicationInsights 테이블에서 데이터를 읽습니다. |
> |  | **로그/AzureActivity** |  |
> | 작업 | Microsoft.Insights/Logs/AzureActivity/Read | AzureActivity 테이블에서 데이터를 읽습니다. |
> |  | **로그/AzureMetrics** |  |
> | 작업 | Microsoft.Insights/Logs/AzureMetrics/Read | AzureMetrics 테이블에서 데이터를 읽습니다. |
> |  | **로그/바운드포트** |  |
> | 작업 | Microsoft.Insights/Logs/BoundPort/Read | BoundPort 테이블에서 데이터를 읽습니다. |
> |  | **로그/커먼시큐리티로그** |  |
> | 작업 | Microsoft.Insights/Logs/CommonSecurityLog/Read | CommonSecurityLog 테이블에서 데이터를 읽습니다. |
> |  | **로그/컴퓨터 그룹** |  |
> | 작업 | Microsoft.Insights/Logs/ComputerGroup/Read | ComputerGroup 테이블에서 데이터를 읽습니다. |
> |  | **로그/구성 변경** |  |
> | 작업 | Microsoft.Insights/Logs/ConfigurationChange/Read | ConfigurationChange 테이블에서 데이터를 읽습니다. |
> |  | **로그/구성데이터** |  |
> | 작업 | Microsoft.Insights/Logs/ConfigurationData/Read | ConfigurationData 테이블에서 데이터를 읽습니다. |
> |  | **로그/컨테이너이미지 인벤토리** |  |
> | 작업 | Microsoft.Insights/Logs/ContainerImageInventory/Read | ContainerImageInventory 테이블에서 데이터를 읽습니다. |
> |  | **로그/컨테이너 인벤토리** |  |
> | 작업 | Microsoft.Insights/Logs/ContainerInventory/Read | ContainerInventory 테이블에서 데이터를 읽습니다. |
> |  | **로그/컨테이너 로그** |  |
> | 작업 | Microsoft.Insights/Logs/ContainerLog/Read | ContainerLog 테이블에서 데이터를 읽습니다. |
> |  | **로그/컨테이너서비스로그** |  |
> | 작업 | Microsoft.Insights/Logs/ContainerServiceLog/Read | ContainerServiceLog 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스 앱크래시** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceAppCrash/Read | DeviceAppCrash 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스앱 런칭** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceAppLaunch/Read | DeviceAppLaunch 테이블에서 데이터를 읽습니다. |
> |  | **로그/장치캘린더** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceCalendar/Read | DeviceCalendar 테이블에서 데이터를 읽습니다. |
> |  | **로그/장치 정리** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceCleanup/Read | DeviceCleanup 테이블에서 데이터를 읽습니다. |
> |  | **로그/장치 연결 세션** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceConnectSession/Read | DeviceConnectSession 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스Etw** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceEtw/Read | DeviceEtw 테이블에서 데이터를 읽습니다. |
> |  | **로그/장치하드웨어건강** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | DeviceHardwareHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스Health** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceHealth/Read | DeviceHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스하트비트** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceHeartbeat/Read | DeviceHeartbeat 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스스카이프하트비트** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | DeviceSkypeHeartbeat 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스스카이프로그인** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | DeviceSkypeSignIn 테이블에서 데이터를 읽습니다. |
> |  | **로그/디바이스슬립스테이트** |  |
> | 작업 | Microsoft.Insights/Logs/DeviceSleepState/Read | DeviceSleepState 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHApp실패** |  |
> | 작업 | Microsoft.Insights/Logs/DHAppFailure/Read | DHAppFailure 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHApp 신뢰성** |  |
> | 작업 | Microsoft.Insights/Logs/DHAppReliability/Read | DHAppReliability 테이블에서 데이터를 읽습니다. |
> |  | **로그/DH드라이버신뢰성** |  |
> | 작업 | Microsoft.Insights/Logs/DHDriverReliability/Read | DHDriverReliability 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHLogon실패** |  |
> | 작업 | Microsoft.Insights/Logs/DHLogonFailures/Read | DHLogonFailures 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHLogonMetrics** |  |
> | 작업 | Microsoft.Insights/Logs/DHLogonMetrics/Read | DHLogonMetrics 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHOS크래시데이터** |  |
> | 작업 | Microsoft.Insights/Logs/DHOSCrashData/Read | DHOSCrashData 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHOS신뢰성** |  |
> | 작업 | Microsoft.Insights/Logs/DHOSReliability/Read | DHOSReliability 테이블에서 데이터를 읽습니다. |
> |  | **로그/DHWipApp학습** |  |
> | 작업 | Microsoft.Insights/Logs/DHWipAppLearning/Read | DHWipAppLearning 테이블에서 데이터를 읽습니다. |
> |  | **로그/Dns이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/DnsEvents/Read | DnsEvents 테이블에서 데이터를 읽습니다. |
> |  | **로그/Dns인벤토리** |  |
> | 작업 | Microsoft.Insights/Logs/DnsInventory/Read | DnsInventory 테이블에서 데이터를 읽습니다. |
> |  | **로그/ETW이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/ETWEvent/Read | ETWEvent 테이블에서 데이터를 읽습니다. |
> |  | **로그/이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/Event/Read | Event 테이블에서 데이터를 읽습니다. |
> |  | **로그/교환평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | ExchangeAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/교환온라인평가추천서** |  |
> | 작업 | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | ExchangeOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/하트비트** |  |
> | 작업 | Microsoft.Insights/Logs/Heartbeat/Read | Heartbeat 테이블에서 데이터를 읽습니다. |
> |  | **로그/IIS평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | IISAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/인바운드 연결** |  |
> | 작업 | Microsoft.Insights/Logs/InboundConnection/Read | InboundConnection 테이블에서 데이터를 읽습니다. |
> |  | **로그/쿠베노드인벤토리** |  |
> | 작업 | Microsoft.Insights/Logs/KubeNodeInventory/Read | KubeNodeInventory 테이블에서 데이터를 읽습니다. |
> |  | **로그/쿠베포드인벤토리** |  |
> | 작업 | Microsoft.Insights/Logs/KubePodInventory/Read | KubePodInventory 테이블에서 데이터를 읽습니다. |
> |  | **로그/리눅스감사로그** |  |
> | 작업 | Microsoft.Insights/Logs/LinuxAuditLog/Read | LinuxAuditLog 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA응용 프로그램** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplication/Read | MAApplication 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA응용 프로그램Health** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplicationHealth/Read | MAApplicationHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA응용프로그램건강대체** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | MAApplicationHealthAlternativeVersions 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA응용 프로그램건강 문제** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | MAApplicationHealthIssues 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA응용 프로그램 인스턴스** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplicationInstance/Read | MAApplicationInstance 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAApplication인스턴스 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | MAApplicationInstanceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAApplication 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAApplicationReadiness/Read | MAApplicationReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA 배포 계획** |  |
> | 작업 | Microsoft.Insights/Logs/MADeploymentPlan/Read | MADeploymentPlan 테이블에서 데이터를 읽습니다. |
> |  | **로그/장치** |  |
> | 작업 | Microsoft.Insights/Logs/MADevice/Read | MADevice 테이블에서 데이터를 읽습니다. |
> |  | **로그/마디바이스PnPHealth** |  |
> | 작업 | Microsoft.Insights/Logs/MADevicePnPHealth/Read | MADevicePnPHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/MADevicePnPHealth대안** |  |
> | 작업 | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | MADevicePnPHealthAlternativeVersions 테이블에서 데이터 읽기 |
> |  | **로그/마디바이스PnPHealth문제** |  |
> | 작업 | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | MADevicePnPHealthIssues 테이블에서 데이터 읽기 |
> |  | **로그/장치 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MADeviceReadiness/Read | MADeviceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/MADriver인스턴스 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | MADriverInstanceReadiness 테이블에서 데이터 읽기 |
> |  | **로그/마드라이버 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MADriverReadiness/Read | MADriverReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스아딘** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAddin/Read | MAOfficeAddin 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스애딘헬스** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | MAOfficeAddinHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스아딘건강문제** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | MAOfficeAddinHealthIssues 테이블에서 데이터 읽기 |
> |  | **로그/마오피스애딘인스턴스** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | MAOfficeAddinInstance 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOfficeAddin인스턴스 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | MAOfficeAddinInstanceReadiness 테이블에서 데이터 읽기 |
> |  | **로그/MAOfficeAddin준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | MAOfficeAddinReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스앱** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeApp/Read | MAOfficeApp 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스앱헬스** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | MAOfficeAppHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/매오피스앱인스턴스** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | MAOfficeAppInstance 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOfficeApp 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | MAOfficeAppReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스빌드정보** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | MAOfficeBuildInfo 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOffice통화평가** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | MAOfficeCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOffice통화평가일일카운트** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | MAOfficeCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOffice 배포 상태** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | MAOfficeDeploymentStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스매크로헬스** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | MAOfficeMacroHealth 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스매크로헬스이슈** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | MAOfficeMacroHealthIssues 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOffice매크로문제인스턴스준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | MAOfficeMacroIssueInstanceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOffice 매크로 문제 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | MAOfficeMacroIssueReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAOffice매크로 요약** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | MAOfficeMacroSummary 테이블에서 데이터를 읽습니다. |
> |  | **로그/마오피스스위트** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeSuite/Read | MAOfficeSuite 테이블에서 데이터를 읽습니다. |
> |  | **로그/매오피스스위트인스턴스** |  |
> | 작업 | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | MAOfficeSuiteInstance 테이블에서 데이터를 읽습니다. |
> |  | **로그/MA제안 파일럿장치** |  |
> | 작업 | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | MAProposedPilotDevices 테이블에서 데이터를 읽습니다. |
> |  | **로그/마윈도우빌드정보** |  |
> | 작업 | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | MAWindowsBuildInfo 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAWindows통화평가** |  |
> | 작업 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | MAWindowsCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAWindows통화평가일일수** |  |
> | 작업 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | MAWindowsCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAWindows 배포 상태** |  |
> | 작업 | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | MAWindowsDeploymentStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/MAWindowsSysReq인스턴스 준비** |  |
> | 작업 | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | MAWindowsSysReqInstanceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **로그/네트워크 모니터링** |  |
> | 작업 | Microsoft.Insights/Logs/NetworkMonitoring/Read | NetworkMonitoring 테이블에서 데이터를 읽습니다. |
> |  | **로그/사무실 활동** |  |
> | 작업 | Microsoft.Insights/Logs/OfficeActivity/Read | OfficeActivity 테이블에서 데이터를 읽습니다. |
> |  | **로그/작업** |  |
> | 작업 | Microsoft.Insights/Logs/Operation/Read | Operation 테이블에서 데이터를 읽습니다. |
> |  | **로그/아웃바운드 연결** |  |
> | 작업 | Microsoft.Insights/Logs/OutboundConnection/Read | OutboundConnection 테이블에서 데이터를 읽습니다. |
> |  | **로그/퍼프** |  |
> | 작업 | Microsoft.Insights/Logs/Perf/Read | Perf 테이블에서 데이터를 읽습니다. |
> |  | **로그/보호 상태** |  |
> | 작업 | Microsoft.Insights/Logs/ProtectionStatus/Read | ProtectionStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/예약된 AzureCommonFields** |  |
> | 작업 | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | ReservedAzureCommonFields 테이블에서 데이터를 읽습니다. |
> |  | **로그/예약된 커먼필드** |  |
> | 작업 | Microsoft.Insights/Logs/ReservedCommonFields/Read | ReservedCommonFields 테이블에서 데이터를 읽습니다. |
> |  | **로그/SCCM평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | SCCMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/SCOM평가 추천** |  |
> | 작업 | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | SCOMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/보안경고** |  |
> | 작업 | Microsoft.Insights/Logs/SecurityAlert/Read | SecurityAlert 테이블에서 데이터를 읽습니다. |
> |  | **로그/보안 기준선** |  |
> | 작업 | Microsoft.Insights/Logs/SecurityBaseline/Read | SecurityBaseline 테이블에서 데이터를 읽습니다. |
> |  | **로그/보안기준요약** |  |
> | 작업 | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | SecurityBaselineSummary 테이블에서 데이터를 읽습니다. |
> |  | **로그/보안 검색** |  |
> | 작업 | Microsoft.Insights/Logs/SecurityDetection/Read | SecurityDetection 테이블에서 데이터를 읽습니다. |
> |  | **로그/보안 이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/SecurityEvent/Read | SecurityEvent 테이블에서 데이터를 읽습니다. |
> |  | **로그/서비스패브릭오퍼리** |  |
> | 작업 | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | ServiceFabricOperationalEvent 테이블에서 데이터를 읽습니다. |
> |  | **로그/서비스패브릭신뢰할 수 있는 액터이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | ServiceFabricReliableActorEvent 테이블에서 데이터를 읽습니다. |
> |  | **로그/서비스패브릭신뢰할 수 있는 서비스이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | ServiceFabricReliableServiceEvent 테이블에서 데이터를 읽습니다. |
> |  | **로그/SfB평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | SfBAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/SfB온라인평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | SfBOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/쉐어포인트온라인평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | SharePointOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/SP평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | SPAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/SQL평가 권장 사항** |  |
> | 작업 | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | SQLAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/SQL쿼리 성능** |  |
> | 작업 | Microsoft.Insights/Logs/SQLQueryPerformance/Read | SQLQueryPerformance 테이블에서 데이터를 읽습니다. |
> |  | **로그/시슬로그** |  |
> | 작업 | Microsoft.Insights/Logs/Syslog/Read | Syslog 테이블에서 데이터를 읽습니다. |
> |  | **로그/시스몬 이벤트** |  |
> | 작업 | Microsoft.Insights/Logs/SysmonEvent/Read | SysmonEvent 테이블에서 데이터를 읽습니다. |
> |  | **로그/테이블.사용자 지정** |  |
> | 작업 | 마이크로소프트.인사이트/로그/테이블.사용자 지정/읽기 | 사용자 지정 로그에서 데이터를 읽습니다. |
> |  | **로그/UAApp** |  |
> | 작업 | Microsoft.Insights/Logs/UAApp/Read | UAApp 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA컴퓨터** |  |
> | 작업 | Microsoft.Insights/Logs/UAComputer/Read | UAComputer 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA컴퓨터 랭크** |  |
> | 작업 | Microsoft.Insights/Logs/UAComputerRank/Read | UAComputerRank 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA드라이버** |  |
> | 작업 | Microsoft.Insights/Logs/UADriver/Read | UADriver 테이블에서 데이터를 읽습니다. |
> |  | **로그/UADriver문제 코드** |  |
> | 작업 | Microsoft.Insights/Logs/UADriverProblemCodes/Read | UADriverProblemCodes 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA피드백** |  |
> | 작업 | Microsoft.Insights/Logs/UAFeedback/Read | UAFeedback 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA하드웨어보안** |  |
> | 작업 | Microsoft.Insights/Logs/UAHardwareSecurity/Read | UAHardwareSecurity 테이블에서 데이터를 읽습니다. |
> |  | **로그/UAIE사이트디스커버리** |  |
> | 작업 | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | UAIESiteDiscovery 테이블에서 데이터를 읽습니다. |
> |  | **로그/UAOfficeAddIn** |  |
> | 작업 | Microsoft.Insights/Logs/UAOfficeAddIn/Read | UAOfficeAddIn 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA제안 된 행동 계획** |  |
> | 작업 | Microsoft.Insights/Logs/UAProposedActionPlan/Read | UAProposedActionPlan 테이블에서 데이터를 읽습니다. |
> |  | **로그/UASysReq문제** |  |
> | 작업 | Microsoft.Insights/Logs/UASysReqIssue/Read | UASysReqIssue 테이블에서 데이터를 읽습니다. |
> |  | **로그/UA업그레이드컴퓨터** |  |
> | 작업 | Microsoft.Insights/Logs/UAUpgradedComputer/Read | UAUpgradedComputer 테이블에서 데이터를 읽습니다. |
> |  | **로그/업데이트** |  |
> | 작업 | Microsoft.Insights/Logs/Update/Read | Update 테이블에서 데이터를 읽습니다. |
> |  | **로그/업데이트실행진행상황** |  |
> | 작업 | Microsoft.Insights/Logs/UpdateRunProgress/Read | UpdateRunProgress 테이블에서 데이터를 읽습니다. |
> |  | **로그/업데이트 요약** |  |
> | 작업 | Microsoft.Insights/Logs/UpdateSummary/Read | UpdateSummary 테이블에서 데이터를 읽습니다. |
> |  | **로그/사용** |  |
> | 작업 | Microsoft.Insights/Logs/Usage/Read | Usage 테이블에서 데이터를 읽습니다. |
> |  | **로그/W3CIISLog** |  |
> | 작업 | Microsoft.Insights/Logs/W3CIISLog/Read | W3CIISLog 테이블에서 데이터를 읽습니다. |
> |  | **로그/와스배포상태** |  |
> | 작업 | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | WaaSDeploymentStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/와스인사이더 상태** |  |
> | 작업 | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | WaaSInsiderStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/와아업데이트 상태** |  |
> | 작업 | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | WaaSUpdateStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/WDAV 상태** |  |
> | 작업 | Microsoft.Insights/Logs/WDAVStatus/Read | WDAVStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/WDAV위협** |  |
> | 작업 | Microsoft.Insights/Logs/WDAVThreat/Read | WDAVThreat 테이블에서 데이터를 읽습니다. |
> |  | **로그/윈도우클라이언트평가추천** |  |
> | 작업 | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | WindowsClientAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/윈도우 방화벽** |  |
> | 작업 | Microsoft.Insights/Logs/WindowsFirewall/Read | WindowsFirewall 테이블에서 데이터를 읽습니다. |
> |  | **로그/윈도우서버평가권장사항** |  |
> | 작업 | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | WindowsServerAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **로그/와이어데이터** |  |
> | 작업 | Microsoft.Insights/Logs/WireData/Read | WireData 테이블에서 데이터를 읽습니다. |
> |  | **로그/WUDO집계 상태** |  |
> | 작업 | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | WUDOAggregatedStatus 테이블에서 데이터를 읽습니다. |
> |  | **로그/WUDO상태** |  |
> | 작업 | Microsoft.Insights/Logs/WUDOStatus/Read | WUDOStatus 테이블에서 데이터를 읽습니다. |
> |  | **메트릭 경고** |  |
> | 작업 | Microsoft.Insights/MetricAlerts/Write | 메트릭 경고를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Insights/MetricAlerts/Delete | 메트릭 경고를 삭제합니다. |
> | 작업 | Microsoft.Insights/MetricAlerts/Read | 메트릭 경고를 읽습니다. |
> |  | **메트릭 경고/상태** |  |
> | 작업 | Microsoft.Insights/MetricAlerts/Status/Read | 메트릭 경고 상태를 읽습니다. |
> |  | **메트릭 기준선** |  |
> | 작업 | 마이크로소프트.인사이트/메트릭베이스라인/읽기 | 메트릭 기준 읽기 |
> |  | **메트릭 정의** |  |
> | 작업 | Microsoft.Insights/MetricDefinitions/Read | 메트릭 정의 읽기 |
> |  | **메트릭 정의/마이크로소프트.인사이트** |  |
> | 작업 | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 메트릭 정의 읽기 |
> |  | **메트릭 정의/공급자/마이크로소프트.인사이트** |  |
> | 작업 | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 메트릭 정의 읽기 |
> |  | **메트릭 이름 공백** |  |
> | 작업 | 마이크로소프트.인사이트/메트릭나메스페이스/읽기 | 메트릭 네임스페이스 읽기 |
> |  | **메트릭** |  |
> | 작업 | Microsoft.Insights/Metrics/Read | 메트릭 읽기 |
> |  | **메트릭/마이크로소프트.인사이트** |  |
> | 작업 | 마이크로소프트.인사이트/메트릭/마이크로소프트.인사이트/읽기 | 메트릭 읽기 |
> |  | **메트릭/공급자/메트릭** |  |
> | 작업 | Microsoft.Insights/Metrics/providers/Metrics/Read | 메트릭 읽기 |
> |  | **myWorkbooks** |  |
> | 작업 | 마이크로소프트.인사이트/마이워크북/쓰기 | 개인 통합 문서 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/마이워크북/읽기 | 개인 통합 문서 읽기 |
> |  | **작업** |  |
> | 작업 | Microsoft.Insights/Operations/Read | 읽기 작업 |
> |  | **프라이빗 링크스코프운영 상태** |  |
> | 작업 | 마이크로소프트.인사이트/프라이빗링크스코프오퍼레이션상태/읽기 | 개인 링크 범위 작업 상태 읽기 |
> |  | **프라이빗 링크스코프** |  |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/읽기 | 개인 링크 범위 읽기 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/쓰기 | 개인 링크 범위 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/삭제 | 개인 링크 범위 삭제 |
> |  | **프라이빗 링크스코프/프라이빗 엔드포인트커넥션** |  |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트커넥션프록시/읽기 | 개인 엔드포인트 연결 프록시 읽기 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트커넥션프록시/쓰기 | 개인 끝점 연결 프록시 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트커넥션프록시/삭제 | 개인 끝점 연결 프록시 삭제 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트커넥션프록시/유효성 검사/조치 | 개인 끝점 연결 프록시 유효성 검사 |
> |  | **프라이빗 링크스코프/프라이빗 엔드포인트커넥션** |  |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트연결/읽기 | 개인 끝점 연결 읽기 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트연결/쓰기 | 개인 끝점 연결 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗 엔드포인트 연결/삭제 | 개인 끝점 연결 삭제 |
> |  | **프라이빗 링크스코프/프라이빗링크리소스** |  |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/프라이빗링크리소스/읽기 | 개인 링크 리소스 읽기 |
> |  | **프라이빗 링크스코프/스코프리소스** |  |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/스코프리소스/읽기 | 개인 링크 범위 리소스 읽기 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/스코프리소스/쓰기 | 개인 링크 범위 리소스 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/프라이빗 링크스코프/스코프리소스/삭제 | 개인 링크 범위 리소스 삭제 |
> |  | **예약된 쿼리 규칙** |  |
> | 작업 | Microsoft.Insights/ScheduledQueryRules/Write | 예약된 쿼리 규칙을 씁니다. |
> | 작업 | Microsoft.Insights/ScheduledQueryRules/Read | 예약된 쿼리 규칙을 읽습니다. |
> | 작업 | Microsoft.Insights/ScheduledQueryRules/Delete | 예약된 쿼리 규칙을 삭제합니다. |
> |  | **테넌트** |  |
> | 작업 | Microsoft.Insights/Tenants/Register/Action | Microsoft Insights 공급자를 초기화합니다. |
> |  | **웹 테스트** |  |
> | 작업 | Microsoft.Insights/Webtests/Write | 웹 테스트 구성에 씁니다. |
> | 작업 | Microsoft.Insights/Webtests/Delete | 웹 테스트 구성을 삭제합니다. |
> | 작업 | Microsoft.Insights/Webtests/Read | 웹 테스트 구성을 읽습니다. |
> |  | **웹 테스트/GetToken** |  |
> | 작업 | Microsoft.Insights/Webtests/GetToken/Read | 웹 테스트 토큰을 읽습니다. |
> |  | **웹 테스트/메트릭 정의** |  |
> | 작업 | Microsoft.Insights/Webtests/MetricDefinitions/Read | 웹 테스트 메트릭 정의를 읽습니다. |
> |  | **웹 테스트/메트릭** |  |
> | 작업 | Microsoft.Insights/Webtests/Metrics/Read | 웹 테스트 메트릭을 읽습니다. |
> |  | **통합 문서** |  |
> | 작업 | 마이크로소프트.인사이트/통합 문서/쓰기 | 통합 문서 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.인사이트/통합 문서/삭제 | 통합 문서 삭제 |
> | 작업 | 마이크로소프트.인사이트/통합 문서/읽기 | 통합 문서 읽기 |
> |  | **데이터 컬렉션규칙/데이터** |  |
> | DataAction | 마이크로소프트.인사이트/데이터 컬렉션규칙/데이터/쓰기 | 데이터 수집 규칙으로 데이터 보내기 |
> |  | **메트릭** |  |
> | DataAction | Microsoft.Insights/Metrics/Write | 메트릭을 작성합니다. |

## <a name="microsoftintune"></a>Microsoft.Intune

마이크로소프트 모니터링 인사이트

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **진단 설정** |  |
> | 작업 | Microsoft.Intune/diagnosticsettings/write | 진단 설정을 작성합니다. |
> | 작업 | Microsoft.Intune/diagnosticsettings/read | 진단 설정을 읽습니다. |
> | 작업 | Microsoft.Intune/diagnosticsettings/delete | 진단 설정을 삭제합니다. |
> |  | **진단 설정범주** |  |
> | 작업 | Microsoft.Intune/diagnosticsettingscategories/read | 진단 설정 범주를 읽습니다. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

Azure 서비스: [IoT 센트럴](../iot-central/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.IoTCentral/checkNameAvailability/action | IoT Central 애플리케이션 이름이 사용 가능한지 확인합니다. |
> | 작업 | Microsoft.IoTCentral/checkSubdomainAvailability/action | IoT Central 애플리케이션 하위 도메인이 사용 가능한지 확인합니다. |
> | 작업 | 마이크로소프트.IoTCentral/앱 템플릿/액션 | Azure IoT Central에서 사용 가능한 모든 응용 프로그램 템플릿을 가져옵니다. |
> | 작업 | Microsoft.IoTCentral/register/action | Azure IoT Central 리소스 공급자에 대한 구독 등록 |
> |  | **IoTApps** |  |
> | 작업 | Microsoft.IoTCentral/IoTApps/read | 단일 IoT Central 애플리케이션 가져오기 |
> | 작업 | Microsoft.IoTCentral/IoTApps/write | IoT Central 애플리케이션을 만들거나 업데이트 |
> | 작업 | Microsoft.IoTCentral/IoTApps/delete | IoT Central 애플리케이션 삭제 |
> |  | **작업** |  |
> | 작업 | Microsoft.IoTCentral/operations/read | IoT Central 애플리케이션에 사용 가능한 모든 작업 가져오기 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

Azure 서비스: [Azure 디지털 트윈](../digital-twins/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.IoTSpaces/register/action | 리소스를 만들 수 있게 설정하려면 Microsoft.IoTSpaces Graph 리소스 공급자에 대한 구독을 등록하세요. |
> |  | **그래프** |  |
> | 작업 | Microsoft.IoTSpaces/Graph/write | Microsoft.IoTSpaces Graph 리소스를 만들기 |
> | 작업 | Microsoft.IoTSpaces/Graph/read | Microsoft.IoTSpaces Graph 리소스를 가져오기 |
> | 작업 | Microsoft.IoTSpaces/Graph/delete | Microsoft.IoTSpaces Graph 리소스를 삭제 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Azure 서비스: [키 볼트](../key-vault/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.KeyVault/register/action | 구독을 등록합니다. |
> | 작업 | Microsoft.KeyVault/unregister/action | 구독을 등록 취소합니다. |
> |  | **확인이름가용성** |  |
> | 작업 | Microsoft.KeyVault/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> |  | **deletedVaults** |  |
> | 작업 | Microsoft.KeyVault/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> |  | **hsmPools** |  |
> | 작업 | Microsoft.KeyVault/hsmPools/read | HSM 풀의 속성을 봅니다. |
> | 작업 | Microsoft.KeyVault/hsmPools/write | 기존 HSM 풀의 속성 업데이트에 대해 새 HSM 풀을 만듭니다. |
> | 작업 | Microsoft.KeyVault/hsmPools/delete | HSM 풀을 삭제합니다. |
> | 작업 | Microsoft.KeyVault/hsmPools/joinVault/action | 키 자격 증명 모음을 HSM 풀에 조인합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷이 삭제됨을 Microsoft.KeyVault에 알립니다. |
> |  | **위치/삭제된 볼트** |  |
> | 작업 | Microsoft.KeyVault/locations/deletedVaults/read | 일시 삭제된 한 Key Vault의 속성을 봅니다. |
> | 작업 | Microsoft.KeyVault/locations/deletedVaults/purge/action | 일시 삭제된 Key Vault를 제거합니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.KeyVault/locations/operationResults/read | 장기 실행 작업의 결과를 확인합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.KeyVault/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> |  | **보관소** |  |
> | 작업 | Microsoft.KeyVault/vaults/read | Key Vault의 속성을 봅니다. |
> | 작업 | Microsoft.KeyVault/vaults/write | 새 Key Vault를 만들거나 기존 Key Vault의 속성을 업데이트합니다. |
> | 작업 | Microsoft.KeyVault/vaults/delete | Key Vault를 삭제합니다. |
> | 작업 | Microsoft.KeyVault/vaults/deploy/action | Azure Resource를 배포하는 동안 Key Vault의 비밀에 액세스할 수 있습니다. |
> |  | **vaults/accessPolicies** |  |
> | 작업 | Microsoft.KeyVault/vaults/accessPolicies/write | 병합 또는 바꾸기를 통해 기존 액세스 정책을 업데이트하거나 새 액세스 정책을 자격 증명 모음에 추가합니다. |
> |  | **볼트/이벤트그리드필터** |  |
> | 작업 | 마이크로소프트.키볼트/볼트/이벤트그리드필터/읽기 | 키 볼트에 대한 이벤트 그리드 구독이 조회되고 있음을 Microsoft.KeyVault에 통보합니다. |
> | 작업 | 마이크로소프트.키볼트/볼트/이벤트그리드필터/쓰기 | 키 볼트에 대한 새 EventGrid 구독이 생성되고 있음을 Microsoft.KeyVault에 통보합니다. |
> | 작업 | 마이크로소프트.키볼트/볼트/이벤트그리드필터/삭제 | 키 볼트에 대한 EventGrid 구독이 삭제되고 있음을 Microsoft.KeyVault에 통보합니다. |
> |  | **vaults/secrets** |  |
> | 작업 | Microsoft.KeyVault/vaults/secrets/read | 비밀의 속성을 볼 수 있지만 해당 값은 볼 수 없습니다. |
> | 작업 | Microsoft.KeyVault/vaults/secrets/write | 새 비밀을 만들거나 기존 비밀의 값을 업데이트합니다. |
> |  | **볼트/인증서** |  |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/삭제 | 인증서 이저사용자 삭제 |
> | DataAction | 마이크로소프트.키볼트/볼트/인증서/읽기 | 인증서 이저서 읽기 |
> | DataAction | 마이크로소프트.키볼트/볼트/인증서/쓰기 | 쓰기 인증서 Issuser |
> |  | **볼트/인증서 연락처** |  |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서 연락처/쓰기 | 인증서 연락처 관리 |
> |  | **볼트/인증서** |  |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/삭제 | 인증서를 삭제합니다. |
> | DataAction | 마이크로소프트.키볼트/볼트/인증서/읽기 | 지정된 키 자격 증명 모음에 인증서를 나열하거나 인증서에 대한 정보를 가져옵니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/백업/작업 | 인증서의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 키 볼트에서 인증서를 복원하는 데 사용할 수 있습니다. 제한 사항이 적용될 수 있습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/제거/작업 | 인증서를 지우면 복구할 수 없게 됩니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/업데이트/작업 | 지정된 인증서와 연결된 지정된 특성을 업데이트합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/만들기/작업 | 새 인증서를 만듭니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/가져오기/작업 | 개인 키가 포함된 기존 유효한 인증서를 Azure Key Vault로 가져갑니다. 가져올 인증서는 PFX 또는 PEM 형식일 수 있습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/복구/작업 | 삭제된 인증서를 복구합니다. 이 작업은 Delete 작업의 반전을 수행합니다. 이 작업은 소프트 삭제를 사용하도록 설정된 볼트에 적용되며 보존 간격 동안 발행되어야 합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/인증서/복원/작업 | 백업된 인증서 및 모든 버전을 볼트로 복원합니다. |
> |  | **볼트/키** |  |
> | DataAction | 마이크로소프트.키볼트/볼트/키/읽기 | 지정된 볼트에 키를 나열하거나 키의 속성 및 공용 자료를 읽습니다.<br>비대칭 키의 경우 이 작업은 공개 키를 노출하고 암호화 및 서명 확인과 같은 공개 키 알고리즘을 수행하는 기능을 포함합니다.<br>개인 키와 대칭 키는 노출되지 않습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/업데이트/작업 | 지정된 키와 연결된 지정된 특성을 업데이트합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/만들기/작업 | 새 키를 만듭니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/가져오기/작업 | 외부에서 만든 키를 가져와 저장한 다음 키 매개 변수와 특성을 클라이언트로 반환합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/복구/작업 | 삭제된 키를 복구합니다. 이 작업은 Delete 작업의 반전을 수행합니다. 이 작업은 소프트 삭제를 사용하도록 설정된 볼트에 적용되며 보존 간격 동안 발행되어야 합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/복원/작업 | 백업된 키와 모든 버전을 볼트로 복원합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/삭제 | 키를 삭제합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/백업/작업 | 키의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 키 볼트에서 키를 복원하는 데 사용할 수 있습니다. 제한 사항이 적용될 수 있습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/퍼지/작업 | 키를 제거하여 복구할 수 없게 만듭니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/암호화/작업 | 키로 일반 텍스트를 암호화합니다. 키가 비대칭인 경우 읽기 액세스 권한이 있는 보안 주체가 이 작업을 수행할 수 있습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/암호 해독/작업 | 키로 암호 해독. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/랩/작업 | 키 볼트 키로 대칭 키를 래핑합니다. 키 볼트 키가 비대칭인 경우 읽기 액세스로 이 작업을 수행할 수 있습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/래핑 해제/작업 | 키 볼트 키로 대칭 키의 래핑을 해제합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/기호/작업 | 키로 해시에 서명합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/키/확인/작업 | 해시를 확인합니다. 키가 비대칭인 경우 읽기 액세스 권한이 있는 보안 주체가 이 작업을 수행할 수 있습니다. |
> |  | **vaults/secrets** |  |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/삭제 | 비밀을 삭제합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/백업/작업 | 비밀의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 키 볼트에서 비밀을 복원하는 데 사용할 수 있습니다. 제한 사항이 적용될 수 있습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/제거/작업 | 비밀을 제거하여 복구할 수 없게 만듭니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/업데이트/작업 | 지정된 보안 비밀과 연결된 지정된 특성을 업데이트합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/복구/작업 | 삭제된 비밀을 복구합니다. 이 작업은 Delete 작업의 반전을 수행합니다. 이 작업은 소프트 삭제를 사용하도록 설정된 볼트에 적용되며 보존 간격 동안 발행되어야 합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/복원/작업 | 백업된 비밀 및 모든 버전을 볼트로 복원합니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/읽기 메타데이터/작업 | 비밀의 속성을 나열하거나 볼 수 있지만 해당 값은 볼 수 없습니다. |
> | DataAction | 마이크로소프트.KeyVault/볼트/비밀/getSecret/작업 | 비밀의 가치를 가져옵니다. |
> | DataAction | 마이크로소프트.키볼트/볼트/비밀/세트비밀/액션 | 새 비밀을 만듭니다. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

Azure 서비스: [Azure 데이터 탐색기](../data-explorer/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.쿠스토/레지스터/액션 | 구독 등록 작업 |
> | 작업 | 마이크로소프트.쿠스토/등록/작업 | Kusto 리소스 공급자에 대한 구독을 등록합니다. |
> | 작업 | 마이크로소프트.쿠스토/등록 취소/작업 | Kusto 리소스 공급자에 대한 구독을 등록 취소합니다. |
> |  | **클러스터** |  |
> | 작업 | Microsoft.Kusto/Clusters/read | 클러스터 리소스를 읽습니다. |
> | 작업 | Microsoft.Kusto/Clusters/write | 클러스터 리소스를 씁니다. |
> | 작업 | Microsoft.Kusto/Clusters/delete | 클러스터 리소스를 삭제합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/시작/작업 | 클러스터를 시작합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/중지/작업 | 클러스터를 중지합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/활성화/작업 | 클러스터를 시작합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/비활성화/작업 | 클러스터를 중지합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/체크네임가용성/액션 | 클러스터 이름 가용성을 확인합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/데타치팔로워데이터베이스/액션 | 팔로워의 데이터베이스를 분리합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/리스트팔로워데이터베이스/액션 | 팔로워의 데이터베이스를 나열합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/진단가상네트워크/액션 | 서비스가 종속된 외부 리소스에 대한 네트워크 연결 상태를 진단합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/목록언어 확장/작업 | 언어 확장 프로그램을 나열합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/애드랭스익스텐션/액션 | 언어 확장 프로그램을 추가합니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/제거언어 확장/작업 | 언어 확장 프로그램을 제거합니다. |
> |  | **클러스터/첨부된 데이터베이스 구성** |  |
> | 작업 | 마이크로소프트.쿠스토/클러스터/첨부된 데이터베이스 구성/읽기 | 연결된 데이터베이스 구성 리소스를 읽습니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/첨부된 데이터베이스 구성/쓰기 | 연결된 데이터베이스 구성 리소스를 씁니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/첨부된 데이터베이스 구성/삭제 | 첨부된 데이터베이스 구성 리소스를 삭제합니다. |
> |  | **클러스터/데이터베이스** |  |
> | 작업 | Microsoft.Kusto/Clusters/Databases/read | 데이터베이스 리소스를 읽습니다. |
> | 작업 | Microsoft.Kusto/Clusters/Databases/write | 데이터베이스 리소스를 씁니다. |
> | 작업 | Microsoft.Kusto/Clusters/Databases/delete | 데이터베이스 리소스를 삭제합니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/목록 교장/작업 | 데이터베이스 보안 주체를 나열합니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/추가 교장/작업 | 데이터베이스 보안 주체를 추가합니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/제거교장/작업 | 데이터베이스 보안 주체를 제거합니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/데이터 연결 유효성 검사/작업 | 데이터베이스 데이터 연결의 유효성을 검사합니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/체크네가용성/작업 | 지정된 형식의 이름 가용성을 확인합니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/이벤트허브연결검증/작업 | 데이터베이스 이벤트 허브 연결의 유효성을 검사합니다. |
> |  | **클러스터/데이터베이스/데이터 연결** |  |
> | 작업 | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 데이터 연결 리소스를 읽습니다. |
> | 작업 | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 데이터 연결 리소스를 씁니다. |
> | 작업 | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 데이터 연결 리소스를 삭제합니다. |
> |  | **클러스터/데이터베이스/이벤트 허브연결** |  |
> | 작업 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 이벤트 허브 연결 리소스를 읽습니다. |
> | 작업 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 이벤트 허브 연결 리소스를 작성합니다. |
> | 작업 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 이벤트 허브 연결 리소스를 삭제합니다. |
> |  | **클러스터/데이터베이스/보안 주체 할당** |  |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/기본 할당/읽기 | 데이터베이스 주 체어 할당 리소스를 읽습니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/기본 할당/쓰기 | 데이터베이스 주체 할당 리소스를 씁니다. |
> | 작업 | Microsoft.Kusto/클러스터/데이터베이스/보안 주사 할당/삭제 | 데이터베이스 주 체어 할당 리소스를 삭제합니다. |
> |  | **클러스터/데이터 연결** |  |
> | 작업 | 마이크로소프트.쿠스토/클러스터/데이터 연결/읽기 | 클러스터의 데이터 연결 리소스를 읽습니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/데이터 연결/쓰기 | 클러스터의 데이터 연결 리소스를 씁니다. |
> | 작업 | 마이크로소프트.쿠스토/클러스터/데이터 연결/삭제 | 클러스터의 데이터 연결 리소스를 삭제합니다. |
> |  | **클러스터/보안 주체 할당** |  |
> | 작업 | Microsoft.Kusto/클러스터/보안 주체 할당/읽기 | 클러스터 주 체어 할당 리소스를 읽습니다. |
> | 작업 | Microsoft.Kusto/클러스터/보안 주체 할당/쓰기 | 클러스터 주체 할당 리소스를 씁니다. |
> | 작업 | Microsoft.Kusto/클러스터/보안 주체 할당/삭제 | 클러스터 주 체수 할당 리소스를 삭제합니다. |
> |  | **클러스터/SUS** |  |
> | 작업 | 마이크로소프트.쿠스토/클러스터/SKU/읽기 | 클러스터 SKU 리소스를 읽습니다. |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.쿠스토/위치/체크네임가용성/액션 | 리소스 이름 가용성을 확인합니다. |
> | 작업 | 마이크로소프트.쿠스토/위치/GetNetwork정책/작업 | 네트워크 의도 정책 가져옵니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.Kusto/locations/operationresults/read | 작업 리소스를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Kusto/Operations/read | 작업 리소스를 읽습니다. |
> |  | **SKU** |  |
> | 작업 | 마이크로소프트.쿠스토/SKU/읽기 | SKU 리소스를 읽습니다. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

Azure 서비스: [Azure 랩 서비스](../lab-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.LabServices/register/action | 구독을 등록합니다. |
> |  | **랩계정** |  |
> | 작업 | Microsoft.LabServices/labAccounts/delete | 랩 계정을 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/read | 랩 계정을 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/write | 랩 계정을 추가 또는 수정합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/CreateLab/action | 랩 계정에서 랩을 만듭니다. |
> | 작업 | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 랩 계정에서 구성된 각 크기 범주에 대한 지역별 가용성 정보를 가져옵니다. |
> | 작업 | 마이크로소프트.LabServices/랩계정/GetPricing및가용성/액션 | 랩 계정의 크기, 지역 및 운영 체제 조합의 가격 및 가용성을 확인합니다. |
> | 작업 | 마이크로소프트.랩 서비스/랩계정/GetRestrictionsAnd사용/액션 | 이 구독에 대한 핵심 제한 및 사용법 받기 |
> |  | **랩계정/갤러리이미지** |  |
> | 작업 | Microsoft.LabServices/labAccounts/galleryImages/delete | 갤러리 이미지를 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/galleryImages/read | 갤러리 이미지를 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/galleryImages/write | 갤러리 이미지를 추가하거나 수정합니다. |
> |  | **랩계정/랩** |  |
> | 작업 | Microsoft.LabServices/labAccounts/labs/delete | 랩을 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/read | 랩을 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/write | 랩을 추가 또는 수정합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 랩에 사용자 추가 |
> | 작업 | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 등록 링크가 포함된 이메일을 랩으로 보냅니다. |
> | 작업 | 마이크로소프트.LabServices/랩계정/랩/겟랩디페이시지및가용성/액션 | 이 랩의 랩 단위당 가격과 이 랩을 확장할 수 있는지 여부를 나타내는 가용성을 가져옵니다. |
> |  | **랩계정/랩/환경설정** |  |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 환경 설정을 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 환경 설정을 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 환경 설정을 추가하거나 수정합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 랩/환경 설정의 현재 상태에 따라 환경 설정에 필요한 리소스를 프로비전/프로비전 해제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 템플릿 내의 모든 리소스를 시작하여 템플릿을 시작합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 템플릿 내의 모든 리소스를 중지하여 템플릿을 중지합니다. |
> | 작업 | Microsoft.LabServices/랩계정/랩/환경설정/저장이미지/액션 | 현재 템플릿 이미지를 랩 계정의 공유 갤러리에 저장합니다. |
> | 작업 | Microsoft.LabServices/랩계정/랩/환경설정/암호 재설정/작업 | 템플릿 가상 컴퓨터에서 암호를 재설정합니다. |
> |  | **랩계정/랩/환경설정/환경** |  |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 환경을 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 환경을 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 환경 내의 모든 리소스를 시작하여 환경을 시작합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 환경 내의 모든 리소스를 중지하여 환경을 중지합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 환경의 사용자 암호 다시 설정 |
> |  | **랩계정/랩/환경설정/일정** |  |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 일정을 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 일정을 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 일정을 추가하거나 수정합니다. |
> |  | **랩계정/랩/사용자** |  |
> | 작업 | Microsoft.LabServices/labAccounts/labs/users/delete | 사용자를 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/users/read | 사용자를 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/labs/users/write | 사용자를 추가하거나 수정합니다. |
> |  | **랩계정/공유갤러리** |  |
> | 작업 | Microsoft.LabServices/labAccounts/sharedGalleries/delete | sharedgalleries를 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/sharedGalleries/read | sharedgalleries를 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/sharedGalleries/write | sharedgalleries를 추가 또는 수정합니다. |
> |  | **랩계정/공유 이미지** |  |
> | 작업 | Microsoft.LabServices/labAccounts/sharedImages/delete | sharedimages를 삭제합니다. |
> | 작업 | Microsoft.LabServices/labAccounts/sharedImages/read | sharedimages를 읽습니다. |
> | 작업 | Microsoft.LabServices/labAccounts/sharedImages/write | sharedimages를 추가 또는 수정합니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.LabServices/locations/operations/read | 읽기 작업 |
> |  | **사용자** |  |
> | 작업 | Microsoft.LabServices/users/Register/action | 사용자를 관리되는 랩에 등록합니다. |
> | 작업 | 마이크로소프트.랩 서비스/사용자/목록모든 환경/작업 | 사용자에 대한 모든 환경 나열 |
> | 작업 | Microsoft.LabServices/users/StartEnvironment/action | 환경 내의 모든 리소스를 시작하여 환경을 시작합니다. |
> | 작업 | Microsoft.LabServices/users/StopEnvironment/action | 환경 내의 모든 리소스를 중지하여 환경을 중지합니다. |
> | 작업 | Microsoft.LabServices/users/ResetPassword/action | 환경의 사용자 암호 다시 설정 |
> | 작업 | 마이크로소프트.LabServices/사용자/사용자 설정/작업 | 개인 사용자 설정을 업데이트하고 반환합니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

Azure 서비스: [논리 앱](../logic-apps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Logic/register/action | 지정된 구독에 대한 Microsoft.Logic 리소스 공급자를 등록합니다. |
> |  | **통합 계정** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/read | 통합 계정을 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/write | 통합 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/delete | 통합 계정을 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 선택키 암호를 다시 생성합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 통합 계정의 콜백 URL을 가져옵니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 키 자격 증명 모음의 키를 가져옵니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 통합 계정의 추적 이벤트를 기록합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/join/action | 통합 계정을 조인합니다. |
> |  | **통합계정/계약** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/agreements/read | 통합 계정에서 규약을 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/agreements/write | 통합 계정에서 규약을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/agreements/delete | 통합 계정에서 규약을 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 통합 계정의 규약 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> |  | **통합계정/어셈블리** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/assemblies/read | 통합 계정에서 어셈블리를 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/assemblies/write | 통합 계정에서 어셈블리를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/assemblies/delete | 통합 계정에서 어셈블리를 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 통합 계정의 어셈블리 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> |  | **통합계정/배치구성** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 통합 계정에서 일괄 처리 구성을 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 통합 계정에서 일괄 처리 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 통합 계정에서 일괄 처리 구성을 삭제합니다. |
> |  | **통합 계정/인증서** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/certificates/read | 통합 계정에서 인증서를 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/certificates/write | 통합 계정에서 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/certificates/delete | 통합 계정에서 인증서를 삭제합니다. |
> |  | **통합계정/그룹** |  |
> | 작업 | 마이크로 소프트.로직 / 통합계정 / 그룹 / 읽기 | 통합 계정에서 그룹을 읽습니다. |
> | 작업 | 마이크로 소프트.로직 / 통합계정 / 그룹 / 쓰기 | 통합 계정에서 그룹을 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.로직 / 통합계정 / 그룹 / 삭제 | 통합 계정에서 그룹을 삭제합니다. |
> |  | **통합계정/맵** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/maps/read | 통합 계정에서 맵을 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/maps/write | 통합 계정에서 맵을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/maps/delete | 통합 계정의 맵을 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 통합 계정의 맵 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> |  | **통합계정/파트너** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/partners/read | 통합 계정에서 파트너를 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/partners/write | 통합 계정에서 파트너를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/partners/delete | 통합 계정에서 파트너를 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 통합 계정의 파트너 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> |  | **통합계정/공급자/마이크로소프트.인사이트/로그정의** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 통합 계정 로그 정의를 읽습니다. |
> |  | **통합계정/로제타넷프로세스구성** |  |
> | 작업 | 마이크로 소프트.로직 / 통합계정 계정 / 로제타넷 프로세스 구성 / 읽기 | 통합 계정에서 RosettaNet 프로세스 구성을 읽습니다. |
> | 작업 | 마이크로 소프트.로직 / 통합계정 계정 / 로제타넷 프로세스 구성 / 쓰기 | 통합 계정에서 RosettaNet 프로세스 구성을 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.로직 / 통합계정 계정 / 로제타넷 프로세스 구성 / 삭제 | 통합 계정에서 RosettaNet 프로세스 구성을 삭제합니다. |
> |  | **통합계정/일정** |  |
> | 작업 | 마이크로 소프트.논리 / 통합계정 / 일정 / 읽기 | 통합 계정에서 일정을 읽습니다. |
> | 작업 | 마이크로 소프트.논리 / 통합계정 / 일정 / 쓰기 | 통합 계정에서 일정을 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.논리 / 통합계정 / 일정 / 삭제 | 통합 계정의 일정을 삭제합니다. |
> |  | **통합계정/스키마** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/schemas/read | 통합 계정에서 스키마를 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/schemas/write | 통합 계정에서 스키마를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/schemas/delete | 통합 계정에서 스키마를 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 통합 계정의 스키마 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> |  | **통합계정/세션** |  |
> | 작업 | Microsoft.Logic/integrationAccounts/sessions/read | 통합 계정에서 세션을 읽습니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/sessions/write | 통합 계정에서 세션을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationAccounts/sessions/delete | 통합 계정에서 세션을 삭제합니다. |
> |  | **통합서비스환경** |  |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/read | 통합 서비스 환경을 읽습니다. |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/write | 통합 서비스 환경을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/delete | 통합 서비스 환경을 삭제합니다. |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/join/action | 통합 서비스 환경에 조인합니다. |
> |  | **통합서비스환경/사용 가능관리** |  |
> | 작업 | 마이크로 소프트.로직 / 통합서비스환경 / 사용 가능관리아피 / 읽기 | 사용 가능한 통합 서비스 환경을 읽는 관리되는 API입니다. |
> |  | **통합서비스환경/관리형** |  |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 통합 서비스 환경 관리되는 API를 읽습니다. |
> | 작업 | 마이크로 소프트.로직 / 통합서비스환경 / 관리아피 / 쓰기 | 통합 서비스 환경 관리 API를 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.로직 / 통합서비스환경 / 관리아피 / 조인 / 작업 | 통합 서비스 환경 관리 API에 가입합니다. |
> |  | **통합서비스환경/관리형Apiis/apiOperations** |  |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 통합 서비스 환경 관리되는 API 작업을 읽습니다. |
> |  | **통합서비스환경/관리형Apiis/운영상태** |  |
> | 작업 | 마이크로 소프트.로직 / 통합서비스환경 / 관리Apis / 운영상태 / 읽기 | 통합 서비스 환경 관리 API 작업 상태를 읽습니다. |
> |  | **통합서비스환경/작업상태** |  |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 통합 서비스 환경 작업 상태를 읽습니다. |
> |  | **통합환경환경/공급자/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 통합 서비스 환경 메트릭 정의를 읽습니다. |
> |  | **위치/워크플로우** |  |
> | 작업 | Microsoft.Logic/locations/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
> | 작업 | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 워크플로 권장 작업 그룹을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Logic/operations/read | 작업을 가져옵니다. |
> |  | **워크플로** |  |
> | 작업 | Microsoft.Logic/workflows/read | 워크플로를 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/write | 워크플로를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/workflows/delete | 워크플로를 삭제합니다. |
> | 작업 | Microsoft.Logic/workflows/run/action | 워크플로의 실행을 시작합니다. |
> | 작업 | Microsoft.Logic/workflows/disable/action | 워크플로를 사용하지 않도록 설정합니다. |
> | 작업 | Microsoft.Logic/workflows/enable/action | 워크플로를 사용하도록 설정합니다. |
> | 작업 | Microsoft.Logic/workflows/suspend/action | 워크플로를 일시 중단합니다. |
> | 작업 | Microsoft.Logic/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
> | 작업 | Microsoft.Logic/workflows/move/action | 워크플로를 기존의 구독 ID, 리소스 그룹 및/또는 이름에서 다른 구독 ID, 리소스 그룹 및/또는 이름으로 이동합니다. |
> | 작업 | Microsoft.Logic/workflows/listSwagger/action | 워크플로 Swagger 정의를 가져옵니다. |
> | 작업 | Microsoft.Logic/workflows/regenerateAccessKey/action | 선택키 암호를 다시 생성합니다. |
> | 작업 | Microsoft.Logic/workflows/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |
> |  | **워크플로/액세스 키** |  |
> | 작업 | Microsoft.Logic/workflows/accessKeys/read | 선택키를 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/accessKeys/write | 선택키를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Logic/workflows/accessKeys/delete | 선택키를 삭제합니다. |
> | 작업 | Microsoft.Logic/workflows/accessKeys/list/action | 선택키 암호를 나열합니다. |
> | 작업 | Microsoft.Logic/workflows/accessKeys/regenerate/action | 선택키 암호를 다시 생성합니다. |
> |  | **워크플로우/검출기** |  |
> | 작업 | 마이크로 소프트.로직 / 워크 플로우 / 검출기 / 읽기 | 워크플로우 검출기를 읽습니다. |
> |  | **워크플로우/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 워크플로 진단 설정을 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 워크플로 진단 설정을 만들거나 업데이트합니다. |
> |  | **워크플로/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 워크플로 로그 정의를 읽습니다. |
> |  | **워크플로/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 워크플로 메트릭 정의를 읽습니다. |
> |  | **워크플로/실행** |  |
> | 작업 | Microsoft.Logic/workflows/runs/read | 워크플로 실행을 읽습니다. |
> | 작업 | 마이크로 소프트.로직 / 워크 플로우 / 실행 / 삭제 | 워크플로의 실행을 삭제합니다. |
> | 작업 | Microsoft.Logic/workflows/runs/cancel/action | 워크플로 실행을 취소합니다. |
> |  | **워크플로/실행/작업** |  |
> | 작업 | Microsoft.Logic/workflows/runs/actions/read | 워크플로 실행 작업을 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 워크플로 실행 작업 식 추적을 가져옵니다. |
> |  | **워크플로/ 실행/작업/반복** |  |
> | 작업 | Microsoft.Logic/workflows/runs/actions/repetitions/read | 워크플로 실행 작업 반복을 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 워크플로 실행 작업 반복 식 추적을 가져옵니다. |
> |  | **워크플로/ 실행/작업/반복/요청** |  |
> | 작업 | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 워크플로 실행 반복 작업 요청 기록을 읽습니다. |
> |  | **워크플로/실행/작업/요청** |  |
> | 작업 | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 워크플로 실행 작업 요청 기록을 읽습니다. |
> |  | **워크플로/실행/작업/범위 반복** |  |
> | 작업 | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 워크플로 실행 작업 범위 반복을 읽습니다. |
> |  | **워크플로/ 실행/작업** |  |
> | 작업 | Microsoft.Logic/workflows/runs/operations/read | 워크플로 실행 작업 상태를 읽습니다. |
> |  | **워크플로/트리거** |  |
> | 작업 | Microsoft.Logic/workflows/triggers/read | 트리거를 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/triggers/run/action | 트리거를 실행합니다. |
> | 작업 | Microsoft.Logic/workflows/triggers/reset/action | 트리거를 다시 설정합니다. |
> | 작업 | Microsoft.Logic/workflows/triggers/setState/action | 트리거 상태를 설정합니다. |
> | 작업 | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |
> |  | **워크플로/트리거/기록** |  |
> | 작업 | Microsoft.Logic/workflows/triggers/histories/read | 트리거 기록을 읽습니다. |
> | 작업 | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 워크플로 트리거를 다시 전송합니다. |
> |  | **워크플로/버전** |  |
> | 작업 | Microsoft.Logic/workflows/versions/read | 워크플로 버전을 읽습니다. |
> |  | **워크플로/버전/트리거** |  |
> | 작업 | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

Azure 서비스: [기계 학습 스튜디오](../machine-learning/studio/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.MachineLearning/register/action | Machine Learning 웹 서비스 리소스 공급자에 대한 구독을 등록하고 웹 서비스를 만들도록 설정합니다. |
> | 작업 | Microsoft.MachineLearning/webServices/action | 지원되는 지역에 대한 국가별 웹 서비스 속성을 만듭니다. |
> |  | **약정 계획** |  |
> | 작업 | Microsoft.MachineLearning/commitmentPlans/read | Machine Learning 약정 요금을 읽습니다. |
> | 작업 | Microsoft.MachineLearning/commitmentPlans/write | Machine Learning 약정 요금을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.MachineLearning/commitmentPlans/delete | Machine Learning 약정 요금을 삭제합니다. |
> | 작업 | Microsoft.MachineLearning/commitmentPlans/join/action | Machine Learning 약정 요금제에 가입합니다. |
> |  | **약정계획/약정협회** |  |
> | 작업 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Machine Learning 약정 요금 관계를 읽습니다. |
> | 작업 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Machine Learning 약정 요금 관계를 이동합니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning 작업의 결과를 가져옵니다. |
> |  | **위치/작업 상태** |  |
> | 작업 | Microsoft.MachineLearning/locations/operationsstatus/read | 진행 중인 Machine Learning 작업의 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.MachineLearning/operations/read | Machine Learning 작업을 가져옵니다. |
> |  | **Sku** |  |
> | 작업 | Microsoft.MachineLearning/skus/read | Machine Learning 약정 요금제 SKU를 가져옵니다. |
> |  | **My.webservices** |  |
> | 작업 | Microsoft.MachineLearning/webServices/read | Machine Learning 웹 서비스를 읽습니다. |
> | 작업 | Microsoft.MachineLearning/webServices/write | Machine Learning 웹 서비스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.MachineLearning/webServices/delete | Machine Learning 웹 서비스를 삭제합니다. |
> |  | **웹 서비스/목록 키** |  |
> | 작업 | Microsoft.MachineLearning/webServices/listkeys/read | Machine Learning 웹 서비스에 대한 키를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.MachineLearning/Workspaces/read | Machine Learning 작업 영역을 읽습니다. |
> | 작업 | Microsoft.MachineLearning/Workspaces/write | Machine Learning 작업 영역을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.MachineLearning/Workspaces/delete | Machine Learning 작업 영역을 삭제합니다. |
> | 작업 | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Machine Learning 작업 영역에 대한 키를 나열합니다. |
> | 작업 | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Machine Learning 작업 영역에 대해 구성된 스토리지 계정의 키를 다시 동기화합니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

Azure 서비스: [기계 학습 서비스](../machine-learning/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.MachineLearningServices/register/action | Machine Learning Services 리소스 공급자에 대한 구독을 등록합니다. |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/위치/업데이트할당량/액션 | 작업 영역의 각 VM 패밀리에 대한 할당량을 업데이트합니다. |
> |  | **위치/계산 작업 상태** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/위치/계산운영 상태/읽기 | 특정 컴퓨팅 작업의 상태를 가져옵니다. |
> |  | **위치/할당량** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/위치/할당량/읽기 | VMFamily를 기반으로 현재 할당된 작업 영역 할당량을 가져옵니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.MachineLearningServices/locations/usages/read | 구독의 aml 컴퓨팅 리소스에 대한 사용량 보고서입니다. |
> |  | **위치/vmsizes** |  |
> | 작업 | Microsoft.MachineLearningServices/locations/vmsizes/read | 지원되는 vm 크기를 가져옵니다. |
> |  | **위치/작업 공간운영 상태** |  |
> | 작업 | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 특정 작업 영역 작업의 상태를 가져옵니다. |
> |  | **workspaces** |  |
> | 작업 | Microsoft.MachineLearningServices/workspaces/read | Machine Learning Services 작업 영역을 가져옵니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/write | Machine Learning Services 작업 영역을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/delete | Machine Learning Services 작업 영역을 삭제합니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/listKeys/action | Machine Learning Services 작업 영역에 대한 비밀을 나열합니다. |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/프라이빗엔드포인트연결승인/액션 | Microsoft.Network 공급자의 개인 엔드포인트 리소스에 대한 연결 승인 또는 거부 |
> |  | **workspaces/computes** |  |
> | 작업 | Microsoft.MachineLearningServices/workspaces/computes/read | Machine Learning Services 작업 영역에서 컴퓨팅 리소스를 가져옵니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/computes/write | Machine Learning Services 작업 영역에서 컴퓨팅 리소스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services 작업 영역에서 컴퓨팅 리소스를 삭제합니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Machine Learning Services 작업 영역에서 컴퓨팅 리소스에 대한 비밀을 나열합니다. |
> | 작업 | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Machine Learning Services 작업 영역의 컴퓨팅 리소스에 대한 노드를 나열합니다. |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 영역/컴퓨팅/시작/액션 | 기계 학습 서비스 작업 영역에서 계산 리소스 시작 |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/컴퓨팅/중지/액션 | 기계 학습 서비스 작업 영역에서 계산 리소스 중지 |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 영역/컴퓨팅/다시 시작/작업 | 기계 학습 서비스 작업 영역에서 계산 리소스 다시 시작 |
> |  | **작업 영역/이벤트그리드 필터** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/이벤트그리드필터/읽기 | 특정 작업 영역에 대한 이벤트 그리드 필터 받기 |
> |  | **작업 영역/피처** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/기능/읽기 | 기계 학습 서비스 작업 영역에 대한 모든 지원 기능 가져옵니다. |
> |  | **작업 영역/개인엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/업무공간/프라이빗엔드포인트커넥션프록시/읽기 | Microsoft.Network 공급자의 개인 엔드포인트 리소스에 대한 연결 프록시 상태 보기 |
> | 작업 | 마이크로소프트.머신러닝서비스/업무공간/사설엔드포인트커넥션프록시/쓰기 | 연결 프록시의 상태를 Microsoft.Network 공급자의 개인 끝점 리소스로 변경합니다. |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/프라이빗엔드포인트커넥션프록시/삭제 | Microsoft.Network 공급자의 개인 엔드포인트 리소스에 연결 프록시 삭제 |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/프라이빗엔드포인트커넥션프록시/유효성 검사/조치 | Microsoft.Network 공급자의 개인 엔드포인트 리소스에 대한 연결 프록시 유효성 검사 |
> |  | **작업 영역/프라이빗 엔드포인트연결** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/업무공간/프라이빗엔드포인트연결/읽기 | Microsoft.Network 공급자의 개인 엔드포인트 리소스에 대한 연결 상태 보기 |
> | 작업 | 마이크로소프트.머신러닝서비스/업무공간/프라이빗엔드포인트연결/쓰기 | Microsoft.Network 공급자의 개인 끝점 리소스에 대한 연결 상태 변경 |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/프라이빗엔드포인트연결/삭제 | Microsoft.Network 공급자의 개인 엔드포인트 리소스에 대한 연결 삭제 |
> |  | **작업 영역/개인링크리소스** |  |
> | 작업 | 마이크로소프트.머신러닝서비스/작업 공간/프라이빗링크리소스/읽기 | 기계 학습 서비스 작업 영역의 지정된 인스턴스에 사용할 수 있는 개인 링크 리소스를 가져옵니다. |
> |  | **작업 영역/데이터 드리프트 검출기** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/데이터 드리프트검출기/읽기 | 기계 학습 서비스 작업 공간에서 데이터 드리프트 검출기 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/데이터 드리프트검출기/쓰기 | 기계 학습 서비스 작업 공간에서 데이터 드리프트 감지기를 생성하거나 업데이트합니다. |
> |  | **작업 영역/데이터 집합/등록** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/읽기 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합을 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/쓰기 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합을 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/삭제 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합 삭제 |
> |  | **작업 영역/데이터 집합/등록/미리 보기** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/미리 보기/읽기 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합에 대한 데이터 집합 미리 보기 가져옵니다. |
> |  | **작업 영역/데이터 집합/등록/프로필** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/프로필/읽기 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합에 대한 데이터 집합 프로필 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/프로필/쓰기 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합에 대한 데이터 집합 프로필을 만들거나 업데이트합니다. |
> |  | **작업 영역/데이터 집합/등록/스키마** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록/스키마/읽기 | 기계 학습 서비스 작업 공간에서 등록된 데이터 집합에 대한 데이터 집합 스키마 를 가져옵니다. |
> |  | **작업 영역/데이터 집합/등록되지 않은** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/읽기 | 기계 학습 서비스 작업 공간에서 등록되지 않은 데이터 집합을 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/쓰기 | 기계 학습 서비스 작업 공간에서 등록되지 않은 데이터 집합을 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/삭제 | 기계 학습 서비스 작업 영역에서 등록되지 않은 데이터 집합 삭제 |
> |  | **작업 영역/데이터 집합/등록되지 않은/미리 보기** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/미리 보기/읽기 | 기계 학습 서비스 작업 영역에서 등록되지 않은 데이터 집합에 대한 데이터 집합 미리 보기 가져옵니다. |
> |  | **작업 영역/데이터 집합/등록되지 않은/프로필** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/프로필/읽기 | 기계 학습 서비스 작업 공간에서 등록되지 않은 데이터 집합에 대한 데이터 집합 프로필 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/프로필/쓰기 | 기계 학습 서비스 작업 영역에서 등록되지 않은 데이터 집합에 대한 데이터 집합 프로필을 만들거나 업데이트합니다. |
> |  | **작업 영역/데이터 집합/등록되지 않은/스키마** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/데이터 집합/등록되지 않은/스키마/읽기 | 기계 학습 서비스 작업 공간에서 등록되지 않은 데이터 집합에 대한 데이터 집합 스키마 를 가져옵니다. |
> |  | **작업 영역/데이터 스토어** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/데이터스토어/읽기 | 기계 학습 서비스 작업 공간에서 데이터 스토어 를 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/데이터스토어/쓰기 | 기계 학습 서비스 작업 공간에서 데이터 스토어를 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/데이터스토어/삭제 | 기계 학습 서비스 작업 공간에서 데이터 스토어 삭제 |
> |  | **작업 영역/끝점/파이프라인** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/끝점/파이프라인/읽기 | 기계 학습 서비스 작업 공간에서 게시된 파이프라인 및 파이프라인 끝점 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/끝점/파이프라인/쓰기 | 기계 학습 서비스 작업 공간에서 게시된 파이프라인 및 파이프라인 끝점을 만들거나 업데이트합니다. |
> |  | **작업 영역/환경** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/환경/읽기 | 기계 학습 서비스 작업 공간에서 환경을 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/환경/읽기비밀/액션 | 기계 학습 서비스 작업 공간에서 비밀이 있는 환경을 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/환경/쓰기 | 기계 학습 서비스 작업 공간에서 환경을 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 영역/환경/빌드/액션 | 기계 학습 서비스 작업 공간에서 환경 구축 |
> |  | **workspaces/experiments** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/실험/읽기 | 기계 학습 서비스 작업 공간에서 실험 을 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/실험/쓰기 | 기계 학습 서비스 작업 공간에서 실험을 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/실험/삭제 | 기계 학습 서비스 작업 공간에서 실험을 삭제합니다. |
> |  | **작업 영역/실험/실행** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/실험/실행/읽기 | 기계 학습 서비스 작업 공간에서 실행되다 |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/실험/실행/쓰기 | 기계 학습 서비스 작업 공간에서 실행 또는 업데이트 생성 또는 업데이트 |
> |  | **작업 영역/실험/실행/스크립트 실행** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/실험/실행/스크립트 실행/제출/작업 | 기계 학습 서비스 작업 공간에서 스크립트 실행을 만들거나 업데이트합니다. |
> |  | **작업 영역/라벨링** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/라벨링/내보내기/작업 | 기계 학습 서비스 작업 공간에서 레이블 레이블 내보내기 |
> |  | **작업 영역/레이블/레이블** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/라벨링/라벨/읽기 | 기계 학습 서비스 작업 공간에서 프로젝트 레이블 을 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/라벨링/라벨/쓰기 | 기계 학습 서비스 작업 공간에서 프로젝트 레이블 을 만듭니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/레이블/레이블/거부/작업 | 기계 학습 서비스 작업 공간에서 레이블 레이블 거부 |
> |  | **작업 영역/레이블/프로젝트** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/라벨링/프로젝트/읽기 | 기계 학습 서비스 작업 공간에서 레이블 지정 프로젝트 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/라벨링/프로젝트/쓰기 | 기계 학습 서비스 작업 공간에서 레이블 지정 프로젝트를 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/레이블 지정/프로젝트/삭제 | 기계 학습 서비스 작업 영역에서 레이블 지정 프로젝트 삭제 |
> |  | **작업 영역/레이블/프로젝트/요약** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/레이블/프로젝트/요약/읽기 | 기계 학습 서비스 작업 공간에서 프로젝트 요약 레이블 지정 |
> |  | **작업 영역/메타데이터/아티팩트** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/아티팩트/읽기 | 기계 학습 서비스 작업 공간에서 아티팩트 를 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/아티팩트/쓰기 | 기계 학습 서비스 작업 공간에서 아티팩트를 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/아티팩트/삭제 | 기계 학습 서비스 작업 공간에서 아티팩트 삭제 |
> |  | **작업 영역/메타데이터/비밀** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/비밀/읽기 | 기계 학습 서비스 작업 공간에서 비밀을 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/비밀/쓰기 | 기계 학습 서비스 작업 공간에서 비밀을 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/비밀/삭제 | 기계 학습 서비스 작업 공간에서 비밀 삭제 |
> |  | **작업 영역/메타데이터/스냅샷** |  |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/스냅샷/읽기 | 기계 학습 서비스 작업 공간에서 스냅샷 가져옵니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/스냅샷/쓰기 | 기계 학습 서비스 작업 공간에서 스냅샷을 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/메타데이터/스냅샷/삭제 | 기계 학습 서비스 작업 공간에서 스냅샷 삭제 |
> |  | **작업 영역/모델** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/모델/읽기 | 기계 학습 서비스 작업 공간에서 모델 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/모델/쓰기 | 기계 학습 서비스 작업 공간에서 모델을 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/모델/삭제 | 기계 학습 서비스 작업 공간에서 모델 삭제 |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/모델/패키지/액션 | 기계 학습 서비스 작업 공간의 패키지 모델 |
> |  | **작업 영역/모듈** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/모듈/읽기 | 기계 학습 서비스 작업 공간에서 모듈 을 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/모듈/쓰기 | 기계 학습 서비스 작업 공간에서 모듈을 만들거나 업데이트합니다. |
> |  | **작업 영역/파이프라인 초안** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/파이프라인 초안/읽기 | 기계 학습 서비스 작업 공간에서 파이프라인 초안 가져옵니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/파이프라인 초안/쓰기 | 기계 학습 서비스 작업 공간에서 파이프라인 초안을 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/파이프라인 드래프트/삭제 | 기계 학습 서비스 작업 공간에서 파이프라인 초안 삭제 |
> |  | **작업 영역/서비스** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/읽기 | 기계 학습 서비스 작업 공간에서 서비스 가져옵니다. |
> |  | **작업 영역/서비스/aci** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aci/쓰기 | 기계 학습 서비스 작업 공간에서 ACI 서비스를 만들거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aci/목록 키/작업 | 기계 학습 서비스 작업 영역에서 ACI 서비스에 대한 키를 나열합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aci/삭제 | 기계 학습 서비스 작업 영역에서 ACI 서비스 삭제 |
> |  | **작업 영역/서비스/aks/devtest** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/devtest/쓰기 | 기계 학습 서비스 작업 공간에서 AKS 서비스를 개발하거나 업데이트합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/devtest/목록 키/작업 | 기계 학습 서비스 작업 공간에서 DEVTEST AKS 서비스에 대한 키 를 나열합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/devtest/삭제 | 기계 학습 서비스 작업 공간에서 devtest AKS 서비스를 삭제합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/devtest/점수/액션 | 기계 학습 서비스 작업 공간에서 AKS 서비스를 개발하는 점수 |
> |  | **작업 영역/서비스/aks/prod** |  |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/prod/쓰기 | 기계 학습 서비스 작업 공간에서 prod AKS 서비스를 만들거나 업데이트합니다. |
> | DataAction | Microsoft.MachineLearningServices/작업 영역/서비스/aks/prod/목록 키/작업 | 기계 학습 서비스 작업 영역에서 prod AKS 서비스에 대한 키를 나열합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/prod/delete | 기계 학습 서비스 작업 공간에서 prod AKS 서비스를 삭제합니다. |
> | DataAction | 마이크로소프트.머신러닝서비스/작업 공간/서비스/aks/prod/점수/액션 | 기계 학습 서비스 작업 공간에서 PROD AKS 서비스 점수 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

Azure 서비스: [Azure 리소스에 대한 관리되는 ID](../active-directory/managed-identities-azure-resources/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ManagedIdentity/register/action | 관리 ID 리소스 공급자에 대한 구독을 등록합니다. |
> |  | **Id** |  |
> | 작업 | 마이크로소프트.ManagedId/ID/읽기 | 기존 시스템에 할당된 ID 를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.ManagedId/운영/읽기 | Microsoft.ManagedIdId 리소스 공급자에서 사용할 수 있는 작업을 나열합니다. |
> |  | **userAssignedIdentities** |  |
> | 작업 | Microsoft.ManagedIdentity/userAssignedIdentities/read | 기존 사용자 할당 ID를 가져옵니다. |
> | 작업 | Microsoft.ManagedIdentity/userAssignedIdentities/write | 새로운 사용자 할당 ID를 만들거나 기존 사용자 할당 ID와 연결된 태그를 업데이트합니다. |
> | 작업 | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 기존 사용자 할당 ID를 삭제합니다. |
> | 작업 | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 기존 사용자 할당 ID를 리소스에 할당하기 위한 RBAC 작업입니다. |

## <a name="microsoftmanagedservices"></a>마이크로소프트.ManagedServices

Azure 서비스: [Azure 등대](../lighthouse/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.ManagedServices/레지스터/작업 | 관리되는 서비스에 등록합니다. |
> | 작업 | 마이크로소프트.ManagedServices/등록 취소/작업 | 관리되는 서비스에서 등록을 취소합니다. |
> |  | **마켓플레이스등록정의** |  |
> | 작업 | 마이크로소프트.ManagedServices/마켓플레이스등록정의/읽기 | 관리되는 서비스 등록 정의 목록을 검색합니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.ManagedServices/운영/읽기 | 관리되는 서비스 작업 목록을 검색합니다. |
> |  | **작업상태** |  |
> | 작업 | 마이크로소프트.관리 서비스/작업상태/읽기 | 리소스의 작업 상태를 읽습니다. |
> |  | **등록할당** |  |
> | 작업 | Microsoft.Managed서비스/등록할당/읽기 | 관리되는 서비스 등록 할당 목록을 검색합니다. |
> | 작업 | Microsoft.Managed서비스/등록할당/쓰기 | 관리되는 서비스 등록 할당을 추가하거나 수정합니다. |
> | 작업 | Microsoft.Managed서비스/등록할당/삭제 | 관리되는 서비스 등록 할당을 제거합니다. |
> |  | **등록정의** |  |
> | 작업 | 마이크로소프트.ManagedServices/등록정의/읽기 | 관리되는 서비스 등록 정의 목록을 검색합니다. |
> | 작업 | 마이크로소프트.ManagedServices/등록정의/쓰기 | 관리되는 서비스 등록 정의를 추가하거나 수정합니다. |
> | 작업 | 마이크로소프트.ManagedServices/등록정의/삭제 | 관리되는 서비스 등록 정의를 제거합니다. |

## <a name="microsoftmanagement"></a>Microsoft.Management

Azure 서비스: [관리 그룹](../governance/management-groups/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Management/checkNameAvailability/action | 지정된 관리 그룹 이름이 유효하고 고유한지 확인합니다. |
> | 작업 | Microsoft.Management/getEntities/action | 인증된 사용자에 대한 모든 엔터티(관리 그룹, 구독 등)를 나열합니다. |
> | 작업 | Microsoft.Management/register/action | 지정된 구독을 Microsoft.Management에 등록합니다. |
> |  | **관리 그룹** |  |
> | 작업 | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | 작업 | Microsoft.Management/managementGroups/write | 관리 그룹을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Management/managementGroups/delete | 관리 그룹을 삭제합니다. |
> |  | **관리 그룹/하위 항목** |  |
> | 작업 | Microsoft.관리/관리그룹/하위 항목/읽기 | 관리 그룹의 모든 하위 항목(관리 그룹, 구독)을 가져옵니다. |
> |  | **관리 그룹/설정** |  |
> | 작업 | Microsoft.관리/관리 그룹/설정/읽기 | 기존 관리 그룹 계층 구조 설정을 나열합니다. |
> | 작업 | Microsoft.관리/관리 그룹/설정/쓰기 | 관리 그룹 계층 구조 설정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.관리/관리 그룹/설정/삭제 | 관리 그룹 계층 구조 설정을 삭제합니다. |
> |  | **관리 그룹/구독** |  |
> | 작업 | Microsoft.Management/managementGroups/subscriptions/write | 기존 구독을 관리 그룹과 연결합니다. |
> | 작업 | Microsoft.Management/managementGroups/subscriptions/delete | 관리 그룹에서 구독의 연결을 해제합니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

Azure 서비스: [Azure 지도](../azure-maps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Maps/register/action | 공급자를 등록합니다. |
> |  | **계정** |  |
> | 작업 | Microsoft.Maps/accounts/write | Maps 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Maps/accounts/read | Maps 계정을 가져옵니다. |
> | 작업 | Microsoft.Maps/accounts/delete | Maps 계정을 삭제합니다. |
> | 작업 | Microsoft.Maps/accounts/listKeys/action | Maps 계정 키를 나열합니다. |
> | 작업 | Microsoft.Maps/accounts/regenerateKey/action | 새 Maps 계정 기본 키 또는 보조 키를 생성합니다. |
> |  | **accounts/eventGridFilters** |  |
> | 작업 | Microsoft.Maps/accounts/eventGridFilters/delete | Event Grid 필터를 삭제합니다. |
> | 작업 | Microsoft.Maps/accounts/eventGridFilters/read | Event Grid 필터를 가져옵니다. |
> | 작업 | Microsoft.Maps/accounts/eventGridFilters/write | Event Grid 필터를 만들거나 업데이트합니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.지도/작업/읽기 | 공급자 작업 읽기 |
> |  | **계정/데이터** |  |
> | DataAction | Microsoft.Maps/accounts/data/read | Maps 계정에 데이터 읽기 액세스 권한을 부여합니다. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Marketplace/register/action | 구독에서 Microsoft.Marketplace 리소스 공급자를 등록합니다. |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/액션 | 개인 스토어를 업데이트합니다. |
> |  | **offerTypes/publishers/offers/plans/agreements** |  |
> | 작업 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 규약을 반환합니다. |
> | 작업 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 서명된 규약을 수락합니다. |
> |  | **offerTypes/publishers/offers/plans/configs** |  |
> | 작업 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 구성을 반환합니다. |
> | 작업 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 구성을 저장합니다. |
> | 작업 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 이미지를 최종 사용자의 ACR로 가져옵니다. |
> |  | **프라이빗 스토어** |  |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/쓰기 | 개인 저장소를 만듭니다. |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/삭제 | 프라이빗 스토어를 삭제합니다. |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/오퍼/액션 | 업데이트는 프라이빗 스토어에서 제공됩니다. |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/읽기 | 개인 상점을 읽습니다. |
> |  | **프라이빗 스토어/오퍼** |  |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/오퍼/쓰기 | 프라이빗 스토어에서 쿠폰을 만듭니다. |
> | 작업 | 마이크로소프트.마켓플레이스/프라이빗 스토어/오퍼/삭제 | 프라이빗 스토어에서 오퍼를 삭제합니다. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **클래식데브서비스** |  |
> | 작업 | Microsoft.MarketplaceApps/ClassicDevServices/read | 클래식 개발 서비스에서 GET 작업을 수행합니다. |
> | 작업 | Microsoft.MarketplaceApps/ClassicDevServices/delete | 클래식 개발 서비스 리소스에서 DELETE 작업을 수행합니다. |
> | 작업 | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 클래식 개발자 서비스에 대한 Single Sign-On URL을 가져옵니다. |
> | 작업 | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 클래식 개발 서비스 리소스 관리 키를 가져옵니다. |
> | 작업 | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 클래식 개발 서비스 리소스 관리 키를 생성합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.MarketplaceApps/Operations/read | 모든 리소스 종류에 대한 작업을 읽습니다. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **계약** |  |
> | 작업 | Microsoft.MarketplaceOrdering/agreements/read | 지정된 구독의 모든 규약을 반환합니다. |
> |  | **계약/제안/계획** |  |
> | 작업 | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 지정된 Marketplace 항목에 대한 규약을 반환합니다. |
> | 작업 | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 지정된 Marketplace 항목에 대한 규약에 서명합니다. |
> | 작업 | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 지정된 Marketplace 항목에 대한 규약을 취소합니다. |
> |  | **오퍼타입/퍼블리셔/오퍼/플랜/계약** |  |
> | 작업 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 지정된 마켓플레이스 가상 머신 항목에 대한 규약을 가져옵니다. |
> | 작업 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 지정된 마켓플레이스 가상 머신 항목에 대한 규약에 서명하거나 규약을 취소합니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.마켓플레이스 주문/작업/읽기 | API에서 가능한 모든 작업 나열 |

## <a name="microsoftmedia"></a>Microsoft.Media

Azure 서비스: [미디어 서비스](../media-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Media/register/action | Media Services 리소스 공급자에 대한 구독을 등록하고 Media Services 계정을 만들도록 설정합니다. |
> | 작업 | Microsoft.Media/unregister/action | Media Services 리소스 공급자에 대한 구독을 등록 취소합니다. |
> | 작업 | Microsoft.Media/checknameavailability/action | Media Services 계정 이름을 사용할 수 있는지 확인합니다. |
> |  | **mediaservices** |  |
> | 작업 | Microsoft.Media/mediaservices/read | Media Services 계정을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/write | Media Services 계정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/delete | Media Services 계정을 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/syncStorageKeys/action | 연결된 Azure Storage 계정에 대한 스토리지 키를 동기화합니다. |
> |  | **미디어 서비스/계정 필터** |  |
> | 작업 | Microsoft.Media/mediaservices/accountfilters/read | 계정 필터를 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/accountfilters/write | 계정 필터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/accountfilters/delete | 계정 필터를 삭제합니다. |
> |  | **mediaservices/assets** |  |
> | 작업 | Microsoft.Media/mediaservices/assets/read | 모든 자산을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/write | 모든 자산을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/delete | 모든 자산을 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/listContainerSas/action | 자산 컨테이너 SAS URL을 나열합니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 자산 암호화 키를 가져옵니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 자산에 대한 스트리밍 로케이터를 나열합니다. |
> |  | **미디어 서비스/자산/자산 필터** |  |
> | 작업 | Microsoft.Media/mediaservices/assets/assetfilters/read | 자산 필터를 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/assetfilters/write | 자산 필터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/assets/assetfilters/delete | 자산 필터를 삭제합니다. |
> |  | **mediaservices/contentKeyPolicies** |  |
> | 작업 | Microsoft.Media/mediaservices/contentKeyPolicies/read | 모든 콘텐츠 키 정책을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/contentKeyPolicies/write | 모든 콘텐츠 키 정책을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 모든 콘텐츠 키 정책을 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 비밀을 사용하여 정책 속성을 가져옵니다. |
> |  | **mediaservices/eventGridFilters** |  |
> | 작업 | Microsoft.Media/mediaservices/eventGridFilters/read | 모든 Event Grid 필터를 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/eventGridFilters/write | 모든 Event Grid 필터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/eventGridFilters/delete | 모든 Event Grid 필터를 삭제합니다. |
> |  | **mediaservices/liveEventOperations** |  |
> | 작업 | Microsoft.Media/mediaservices/liveEventOperations/read | 모든 라이브 이벤트 작업을 읽습니다. |
> |  | **미디어 서비스/라이브 이벤트** |  |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/read | 모든 라이브 이벤트를 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/write | 모든 라이브 이벤트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/delete | 모든 라이브 이벤트를 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/start/action | 모든 라이브 이벤트 작업을 시작합니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/stop/action | 모든 라이브 이벤트 작업을 중지합니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/reset/action | 모든 라이브 이벤트 작업을 다시 설정합니다. |
> |  | **mediaservices/liveEvents/liveOutputs** |  |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 모든 라이브 출력을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 모든 라이브 출력을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 모든 라이브 출력을 삭제합니다. |
> |  | **mediaservices/liveOutputOperations** |  |
> | 작업 | Microsoft.Media/mediaservices/liveOutputOperations/read | 모든 라이브 출력 작업을 읽습니다. |
> |  | **mediaservices/streamingEndpointOperations** |  |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 모든 스트리밍 엔드포인트 작업을 읽습니다. |
> |  | **mediaservices/streamingEndpoints** |  |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpoints/read | 모든 스트리밍 엔드포인트를 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpoints/write | 모든 스트리밍 엔드포인트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpoints/delete | 모든 스트리밍 엔드포인트를 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 모든 스트리밍 엔드포인트 작업을 시작합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 모든 스트리밍 엔드포인트 작업을 중지합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 모든 스트리밍 엔드포인트 작업의 크기를 조정합니다. |
> |  | **mediaservices/streamingLocators** |  |
> | 작업 | Microsoft.Media/mediaservices/streamingLocators/read | 모든 스트리밍 로케이터를 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingLocators/write | 모든 스트리밍 로케이터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingLocators/delete | 모든 스트리밍 로케이터를 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 콘텐츠 키를 나열합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 경로를 나열합니다. |
> |  | **mediaservices/streamingPolicies** |  |
> | 작업 | Microsoft.Media/mediaservices/streamingPolicies/read | 모든 스트리밍 정책을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingPolicies/write | 모든 스트리밍 정책을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/streamingPolicies/delete | 모든 스트리밍 정책을 삭제합니다. |
> |  | **mediaservices/transforms** |  |
> | 작업 | Microsoft.Media/mediaservices/transforms/read | 모든 변환을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/transforms/write | 모든 변환을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/transforms/delete | 모든 변환을 삭제합니다. |
> |  | **mediaservices/transforms/jobs** |  |
> | 작업 | Microsoft.Media/mediaservices/transforms/jobs/read | 모든 작업을 읽습니다. |
> | 작업 | Microsoft.Media/mediaservices/transforms/jobs/write | 모든 작업을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Media/mediaservices/transforms/jobs/delete | 모든 작업을 삭제합니다. |
> | 작업 | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 작업을 취소합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Media/operations/read | 사용 가능한 작업을 가져옵니다. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

Azure 서비스: [Azure 마이그레이션](../migrate/migrate-services-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Migrate/register/action | Microsoft.Compute 리소스 공급자로 구독을 등록합니다. |
> |  | **assessmentprojects** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/읽기 | 평가 프로젝트의 속성 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/쓰기 | 새 평가 프로젝트를 만들거나 기존 평가 프로젝트를 업데이트합니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/삭제 | 평가 프로젝트 삭제 |
> |  | **평가 프로젝트/평가옵션** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/평가옵션/읽기 | 지정된 위치에서 사용할 수 있는 평가 옵션을 가져옵니다. |
> |  | **평가 프로젝트/평가** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/평가/읽기 | 프로젝트 내의 평가를 나열합니다. |
> |  | **평가 프로젝트/그룹** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/읽기 | 그룹의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/쓰기 | 새 그룹을 만들거나 기존 그룹을 업데이트합니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/삭제 | 그룹을 삭제합니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/업데이트컴퓨터/작업 | 컴퓨터를 추가하거나 제거하여 그룹 업데이트 |
> |  | **평가 프로젝트/그룹/평가** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/평가/읽기 | 평가의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/평가/쓰기 | 새 평가를 만들거나 기존 평가를 업데이트합니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/평가/삭제 | 평가를 삭제합니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/평가/다운로드/작업 | 평가 보고서의 URL을 다운로드합니다. |
> |  | **평가 프로젝트/그룹/평가/평가 기계** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/그룹/평가/평가/읽기 | 평가된 머신의 속성을 가져옵니다. |
> |  | **평가 프로젝트/하이퍼컬렉터** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/하이퍼브컬렉터/읽기 | HyperV 수집기의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/하이퍼브컬렉터/쓰기 | 새 HyperV 수집기 생성 또는 기존 HyperV 수집기 업데이트 |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/하이퍼브컬렉터/삭제 | 하이퍼V 컬렉터 삭제 |
> |  | **평가 프로젝트/수입 수집가** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/가져오기 수집기/읽기 | 가져오기 수집기의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/가져오기 수집기/쓰기 | 새 가져오기 수집기 생성 또는 기존 가져오기 수집기 업데이트 |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/가져오기 수집기/삭제 | 가져오기 수집기 삭제 |
> |  | **평가 프로젝트/기계** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/컴퓨터/읽기 | 머신의 속성을 가져옵니다. |
> |  | **평가 프로젝트/서버 수집기** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/서버 수집기/읽기 | 서버 수집기의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/서버 수집기/쓰기 | 새 서버 수집기 생성 또는 기존 서버 수집기 업데이트 |
> |  | **평가 프로젝트/vmware수집기** |  |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/vmware수집기/읽기 | VMware 수집기의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/vmware수집기/쓰기 | 새 VMware 수집기 생성 또는 기존 VMware 수집기 업데이트 |
> | 작업 | Microsoft.마이그레이션/평가 프로젝트/vmware수집기/삭제 | VMware 수집기 삭제 |
> |  | **위치** |  |
> | 작업 | Microsoft.Migrate/locations/checknameavailability/action | 지정된 위치에서 지정된 구독의 리소스 이름의 가용성을 확인합니다. |
> |  | **위치/평가옵션** |  |
> | 작업 | Microsoft.Migrate/locations/assessmentOptions/read | 지정된 위치에서 사용할 수 있는 평가 옵션을 가져옵니다. |
> |  | **migrateprojects** |  |
> | 작업 | Microsoft.Migrate/migrateprojects/read | 마이그레이션 프로젝트의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/쓰기 | 새 마이그레이션 프로젝트를 만들거나 기존 마이그레이션 프로젝트를 업데이트합니다. |
> | 작업 | Microsoft.마이그레이션/프로젝트 마이그레이션/삭제 | 마이그레이션 프로젝트 삭제 |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/registerTool/작업 | 마이그레이션 프로젝트에 도구 등록 |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/새로 고침 요약/작업 | 마이그레이션 프로젝트 요약 새로 고침 |
> |  | **프로젝트/데이터베이스인스턴스 마이그레이션** |  |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/데이터베이스 인스턴스/읽기 | 데이터베이스 인스턴스의 속성을 가져옵니다. |
> |  | **프로젝트/데이터베이스 마이그레이션** |  |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/데이터베이스/읽기 | 데이터베이스의 속성을 가져옵니다. |
> |  | **프로젝트/기계 마이그레이션** |  |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/컴퓨터/읽기 | 머신의 속성을 가져옵니다. |
> |  | **프로젝트/마이그레이션이벤트 마이그레이션** |  |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/마이그레이션이벤트/읽기 | 마이그레이션 이벤트의 속성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/마이그레이션이벤트/삭제 | 마이그레이션 이벤트 삭제 |
> |  | **프로젝트/솔루션 마이그레이션** |  |
> | 작업 | Microsoft.Migrate/migrateprojects/solutions/read | 프로젝트 솔루션 마이그레이션의 속성 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/솔루션/쓰기 | 새 마이그레이션 프로젝트 솔루션을 만들거나 기존 마이그레이션 프로젝트 솔루션을 업데이트합니다. |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/솔루션/삭제 | 마이그레이션 프로젝트 솔루션 삭제 |
> | 작업 | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 마이그레이션 프로젝트 솔루션 구성을 가져옵니다. |
> | 작업 | Microsoft.마이그레이션/마이그레이션 프로젝트/솔루션/정리데이터/작업 | 마이그레이션 프로젝트 솔루션 데이터 정리 |
> |  | **작업** |  |
> | 작업 | Microsoft.Migrate/Operations/read | Microsoft.KeyVault 리소스 공급자에서 사용할 수 있는 작업을 나열합니다. |
> |  | **프로젝트** |  |
> | 작업 | Microsoft.Migrate/projects/read | 프로젝트의 속성을 가져옵니다. |
> | 작업 | Microsoft.Migrate/projects/write | 새 프로젝트를 만들거나 기존 프로젝트를 업데이트합니다. |
> | 작업 | Microsoft.Migrate/projects/delete | 프로젝트를 삭제합니다. |
> | 작업 | Microsoft.Migrate/projects/keys/action | 프로젝트의 공유 키를 가져옵니다. |
> |  | **프로젝트/평가** |  |
> | 작업 | Microsoft.Migrate/projects/assessments/read | 프로젝트 내의 평가를 나열합니다. |
> |  | **프로젝트/그룹** |  |
> | 작업 | Microsoft.Migrate/projects/groups/read | 그룹의 속성을 가져옵니다. |
> | 작업 | Microsoft.Migrate/projects/groups/write | 새 그룹을 만들거나 기존 그룹을 업데이트합니다. |
> | 작업 | Microsoft.Migrate/projects/groups/delete | 그룹을 삭제합니다. |
> |  | **프로젝트/그룹/평가** |  |
> | 작업 | Microsoft.Migrate/projects/groups/assessments/read | 평가의 속성을 가져옵니다. |
> | 작업 | Microsoft.Migrate/projects/groups/assessments/write | 새 평가를 만들거나 기존 평가를 업데이트합니다. |
> | 작업 | Microsoft.Migrate/projects/groups/assessments/delete | 평가를 삭제합니다. |
> | 작업 | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 평가 보고서의 URL을 다운로드합니다. |
> |  | **프로젝트/그룹/평가/평가기계** |  |
> | 작업 | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 평가된 머신의 속성을 가져옵니다. |
> |  | **프로젝트/기계** |  |
> | 작업 | Microsoft.Migrate/projects/machines/read | 머신의 속성을 가져옵니다. |

## <a name="microsoftmixedreality"></a>마이크로소프트.혼합현실

Azure 서비스: [Azure 공간 앵커](../spatial-anchors/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.혼합현실/레지스터/액션 | 혼합 현실 리소스 공급자에 대한 구독을 등록합니다. |
> |  | **원격 렌더링계정/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | 마이크로소프트.MixedReality/원격 렌더링계정/공급자/마이크로소프트.인사이트/메트릭정의/읽기 | Microsoft.MixedReality/원격 렌더링 계정에 사용할 수 있는 메트릭을 가져옵니다. |
> |  | **공간앵커계정/제공업체/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | 마이크로 소프트.MixedReality /공간앵커계정 / 제공 업체 / 마이크로 소프트.인사이트 / 진단 설정 / 읽기 | Microsoft.MixedReality/공간 앵커에 대한 진단 설정을 가져옵니다. |
> | 작업 | 마이크로 소프트.MixedReality /공간앵커계정 / 제공 업체 / 마이크로 소프트.인사이트 / 진단 설정 / 쓰기 | Microsoft.MixedReality/공간 앵커계정의 진단 설정을 만들거나 업데이트합니다. |
> |  | **공간앵커계정/제공업체/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | 마이크로소프트.MixedReality/공간앵커계정/제공업체/마이크로소프트.인사이트/메트릭정의/읽기 | Microsoft.MixedReality/공간앵커에 사용할 수 있는 메트릭을 가져옵니다. |
> |  | **원격 렌더링 계정** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 원격 렌더링계정 / 변환 / 작업 | 자산 전환 시작 |
> | DataAction | 마이크로소프트.혼합현실/원격 렌더링계정/관리세션/액션 | 세션 시작 |
> |  | **원격 렌더링계정/변환** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 원격 렌더링계정 / 변환 / 읽기 | 자산 변환 속성 받기 |
> | DataAction | 마이크로 소프트.MixedReality / 원격 렌더링계정 / 변환 / 삭제 | 자산 변환 중지 |
> |  | **원격 렌더링계정/진단** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 원격 렌더링계정 / 진단 / 읽기 | 원격 렌더링 검사기에 연결 |
> |  | **원격 렌더링계정/관리 세션** |  |
> | DataAction | 마이크로 소프트.MixedReality / 원격 렌더링계정 / 관리 세션 / 읽기 | 세션 속성 받기 |
> | DataAction | 마이크로 소프트.MixedReality / 원격 렌더링계정 / 관리 세션 / 삭제 | 세션 중지 |
> |  | **원격 렌더링계정/렌더링** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 원격 렌더링계정 / 렌더링 / 읽기 | 세션에 연결 |
> |  | **공간앵커계정** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 공간 앵커계정 / 생성 / 작업 | 공간 앵커 작성 |
> | DataAction | 마이크로 소프트.혼합 현실 / 공간 앵커계정 / 삭제 | 공간 앵커 삭제 |
> | DataAction | 마이크로 소프트.혼합 현실 / 공간 앵커계정 / 쓰기 | 공간 앵커 속성 업데이트 |
> |  | **공간앵커계정/발견** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 공간 앵커계정 / 발견 / 읽기 | 근처의 공간 앵커를 발견하세요 |
> |  | **공간앵커계정/속성** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 공간 앵커계정 / 속성 / 읽기 | 공간 앵커의 속성 받기 |
> |  | **공간앵커계정/쿼리** |  |
> | DataAction | 마이크로 소프트.MixedReality / 공간 앵커계정 / 쿼리 / 읽기 | 공간 앵커 찾기 |
> |  | **공간앵커계정/제출디아그** |  |
> | DataAction | 마이크로 소프트.혼합 현실 / 공간 앵커계정 / 제출 / 읽기 | Azure 공간 앵커 서비스의 품질을 개선하는 데 도움이 되는 진단 데이터 제출 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

Azure 서비스: [Azure NetApp 파일](../azure-netapp-files/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.넷앱/레지스터/액션 | 구독 등록 작업 |
> | 작업 | 마이크로소프트.넷앱/등록 취소/작업 | Microsoft.NetApp 리소스 공급자를 사용 하 여 구독을 등록 취소 합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.NetApp/locations/read | 가용성 확인 리소스를 읽습니다. |
> | 작업 | 마이크로소프트.NetApp/위치/체크 네임 가용성/작업 | 리소스 이름을 사용할 수 있는지 확인 |
> | 작업 | 마이크로소프트.NetApp/위치/체크파일 경로 가용성/작업 | 파일 경로를 사용할 수 있는지 확인 |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.NetApp/locations/operationresults/read | 작업 결과 리소스를 읽습니다. |
> |  | **넷앱 어카운트** |  |
> | 작업 | Microsoft.NetApp/netAppAccounts/read | 계정 리소스를 읽습니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/write | 계정 리소스를 씁니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/delete | 계정 리소스를 삭제합니다. |
> |  | **넷앱 계정/계정 백업** |  |
> | 작업 | 마이크로 소프트.NetApp / 넷 앱 계정 / 계정 백업 / 읽기 | 계정 백업 리소스를 읽습니다. |
> | 작업 | 마이크로 소프트.NetApp / 넷 앱 계정 / 계정 백업 / 쓰기 | 계정 백업 리소스를 씁니다. |
> | 작업 | 마이크로소프트.넷앱/넷앱 계정/계정 백업/삭제 | 계정 백업 리소스를 삭제합니다. |
> |  | **넷앱 계정/백업정책** |  |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/백업정책/읽기 | 백업 정책 리소스를 읽습니다. |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/백업정책/쓰기 | 백업 정책 리소스를 씁니다. |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/백업정책/삭제 | 백업 정책 리소스를 삭제합니다. |
> |  | **넷앱계정/용량풀** |  |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/read | 풀 리소스를 읽습니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/write | 풀 리소스를 씁니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 풀 리소스를 삭제합니다. |
> |  | **netApp계정/용량풀/볼륨** |  |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 볼륨 리소스를 읽습니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 볼륨 리소스를 씁니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 볼륨 리소스를 삭제합니다. |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/되돌리기/작업 | 볼륨을 특정 스냅샷으로 되돌리기 |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/브레이크복제/액션 | 나누기 볼륨 복제 관계 |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/복제 상태/작업 | 볼륨 복제의 상태를 읽습니다. |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/승인 복제/작업 | 원본 볼륨 복제 권한 부여 |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/재동기화 복제/작업 | 대상 볼륨에서 복제 재동기화 |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/삭제 복제/작업 | 대상 볼륨에서 복제 삭제 |
> |  | **netApp계정/용량풀/볼륨/백업** |  |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/백업/읽기 | 백업 리소스를 읽습니다. |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/백업/쓰기 | 백업 리소스를 씁니다. |
> | 작업 | Microsoft.NetApp/netApp계정/용량풀/볼륨/백업/삭제 | 백업 리소스를 삭제합니다. |
> |  | **netApp계정/용량풀/볼륨/스냅샷** |  |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 스냅샷 리소스를 읽습니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 스냅샷 리소스를 씁니다. |
> | 작업 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 스냅샷 리소스를 삭제합니다. |
> |  | **넷앱계정/스냅샷정책** |  |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/스냅샷정책/읽기 | 스냅숏 정책 리소스를 읽습니다. |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/스냅샷정책/쓰기 | 스냅숏 정책 리소스를 작성합니다. |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/스냅샷정책/삭제 | 스냅숏 정책 리소스를 삭제합니다. |
> | 작업 | Microsoft.NetApp/넷앱 계정/스냅샷정책/목록 볼륨/작업 | 스냅숏 정책에 연결된 볼륨 목록 |
> |  | **넷앱 계정/볼트** |  |
> | 작업 | 마이크로소프트.넷앱/넷앱계정/볼트/읽기 | 볼트 리소스를 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.NetApp/Operations/read | 작업 리소스를 읽습니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network

Azure 서비스 : [가상 네트워크,](../virtual-network/index.yml) [로드 밸러서,](../load-balancer/index.yml) [응용 프로그램 게이트웨이,](../application-gateway/index.yml) [Azure DNS,](../dns/index.yml) [ExpressRoute,](../expressroute/index.yml) [VPN 게이트웨이,](../vpn-gateway/index.yml) [트래픽 관리자](../traffic-manager/index.yml), [네트워크 감시자](../network-watcher/index.yml), [Azure 방화벽](../firewall/index.yml) [Azure 정문 서비스,](../frontdoor/index.yml) [Azure Bastion](../bastion/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Network/register/action | 구독을 등록합니다. |
> | 작업 | Microsoft.Network/unregister/action | 구독을 등록 취소합니다. |
> | 작업 | Microsoft.Network/checkTrafficManagerNameAvailability/action | Traffic Manager 상대 DNS 이름의 가용성을 확인합니다. |
> | 작업 | Microsoft.Network/checkFrontDoorNameAvailability/action | Front Door 이름이 사용 가능한지 여부 확인 |
> |  | **응용 프로그램 게이트웨이사용 요청헤더** |  |
> | 작업 | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Application Gateway 사용 가능한 요청 헤더를 가져옵니다. |
> |  | **응용 프로그램 게이트웨이사용 가능한 응답헤더** |  |
> | 작업 | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Application Gateway 사용 가능한 응답 헤더를 가져옵니다. |
> |  | **응용 프로그램 게이트웨이사용 서버변수** |  |
> | 작업 | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Application Gateway 사용 가능한 서버 변수를 가져옵니다. |
> |  | **응용 프로그램게이트웨이사용가능Ssl옵션** |  |
> | 작업 | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway에서 사용 가능한 Ssl 옵션 |
> |  | **응용 프로그램게이트웨이사용가능옵션/사전 정의된정책** |  |
> | 작업 | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl 미리 정의된 정책 |
> |  | **응용 프로그램 게이트웨이사용 가능한 와프룰세트** |  |
> | 작업 | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Application Gateway 사용 가능 Waf 규칙 집합을 가져옵니다. |
> |  | **애플리케이션 게이트웨이** |  |
> | 작업 | Microsoft.Network/applicationGateways/read | 애플리케이션 게이트웨이를 가져옵니다. |
> | 작업 | Microsoft.Network/applicationGateways/write | 애플리케이션 게이트웨이를 만들거나 애플리케이션 게이트웨이를 업데이트합니다. |
> | 작업 | Microsoft.Network/applicationGateways/delete | 애플리케이션 게이트웨이를 삭제합니다. |
> | 작업 | Microsoft.Network/applicationGateways/backendhealth/action | 애플리케이션 게이트웨이 백 엔드 상태를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램 게이트웨이/getBackendHealth온디맨드/액션 | 지정된 http 설정 및 백 엔드 풀에 대한 요청 시 응용 프로그램 게이트웨이 백 엔드 상태를 가져옵니다. |
> | 작업 | Microsoft.Network/applicationGateways/start/action | Application Gateway를 시작합니다. |
> | 작업 | Microsoft.Network/applicationGateways/stop/action | 애플리케이션 게이트웨이를 중지합니다. |
> |  | **애플리케이션 게이트웨이/백엔드주소풀** |  |
> | 작업 | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 응용 프로그램 게이트웨이 백 엔드 주소 풀에 조인합니다. 경고할 수 없습니다. |
> |  | **응용 프로그램 게이트웨이웹응용 프로그램 방화벽정책** |  |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램 게이트웨이웹응용 프로그램정책/읽기 | 응용 프로그램 게이트웨이 WAF 정책 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램 게이트웨이웹응용 프로그램정책/쓰기 | 응용 프로그램 게이트웨이 WAF 정책을 만들거나 응용 프로그램 게이트웨이 WAF 정책을 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램게이트웨이웹응용프로그램정책/삭제 | 응용 프로그램 게이트웨이 WAF 정책 삭제 |
> |  | **응용 프로그램규칙컬렉션** |  |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램규칙컬렉션/읽기 | Azure 방화벽 응용 프로그램 규칙 컬렉션 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램규칙컬렉션/쓰기 | 생성OrUpdates Azure 방화벽 응용 프로그램규칙컬렉션 |
> | 작업 | 마이크로소프트.네트워크/응용 프로그램규칙컬렉션/삭제 | 삭제 Azure 방화벽 응용 프로그램규칙컬렉션 |
> |  | **응용 프로그램보안그룹** |  |
> | 작업 | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | IP 구성을 애플리케이션 보안 그룹에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 보안 규칙을 애플리케이션 보안 그룹에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/applicationSecurityGroups/read | 애플리케이션 보안 그룹 ID를 가져옵니다. |
> | 작업 | Microsoft.Network/applicationSecurityGroups/write | 애플리케이션 보안 그룹을 만들거나 기존 애플리케이션 보안 그룹을 업데이트합니다. |
> | 작업 | Microsoft.Network/applicationSecurityGroups/delete | 애플리케이션 보안 그룹을 삭제합니다. |
> | 작업 | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | ApplicationSecurityGroup의 IP 구성을 나열합니다. |
> |  | **azureFirewallFqdnTags** |  |
> | 작업 | Microsoft.Network/azureFirewallFqdnTags/read | Azure Firewall FQDN 태그를 가져옵니다. |
> |  | **azurefirewalls** |  |
> | 작업 | Microsoft.Network/azurefirewalls/read | Azure Firewall을 가져옵니다. |
> | 작업 | Microsoft.Network/azurefirewalls/write | Azure Firewall을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/azurefirewalls/delete | Azure Firewall을 삭제합니다. |
> |  | **요새호스트** |  |
> | 작업 | Microsoft.Network/bastionHosts/read | 요새 호스트 가져오기 |
> | 작업 | Microsoft.Network/bastionHosts/write | 요새 호스트 만들기 또는 업데이트 |
> | 작업 | Microsoft.Network/bastionHosts/delete | 요새 호스트 삭제 |
> | 작업 | 마이크로소프트.네트워크/요새호스트/게활성 세션/액션 | 요새 호스트에서 활성 세션 받기 |
> | 작업 | Microsoft.네트워크/방역호스트/연결 해제 활성 세션/작업 | 배스션 호스트에서 지정된 활성 세션 연결 끊기 |
> | 작업 | 마이크로소프트.네트워크/요새호스트/getShareable링크/액션 | 해당 URL이 생성된 경우 Bastion 서브넷에서 지정된 VM에 대한 공유 가능한 URL을 반환합니다. |
> | 작업 | 마이크로소프트.네트워크/요새호스트/생성공유링크/액션 | 요새 에서 VM에 대 한 공유 가능한 URL을 만들고 URL을 반환 합니다. |
> | 작업 | 마이크로소프트.네트워크/요새호스트/삭제공유링크/액션 | 요새에서 제공된 VM에 대한 공유 가능한 URL삭제 |
> |  | **bgpServiceCommunities** |  |
> | 작업 | Microsoft.Network/bgpServiceCommunities/read | BGP 서비스 커뮤니티를 가져옵니다. |
> |  | **연결** |  |
> | 작업 | Microsoft.Network/connections/read | VirtualNetworkGatewayConnection을 가져옵니다. |
> | 작업 | Microsoft.Network/connections/write | VirtualNetworkGatewayConnection을 만들거나 기존 VirtualNetworkGatewayConnection을 업데이트합니다. |
> | 작업 | Microsoft.Network/connections/delete | VirtualNetworkGatewayConnection을 삭제합니다. |
> | 작업 | Microsoft.Network/connections/sharedkey/action | VirtualNetworkGatewayConnection SharedKey를 가져옵니다. |
> | 작업 | Microsoft.Network/connections/vpndeviceconfigurationscript/action | VirtualNetworkGatewayConnection의 Vpn 디바이스 구성을 가져옵니다. |
> | 작업 | Microsoft.Network/connections/revoke/action | 기본 경로 연결 상태를 해지됨으로 표시합니다. |
> | 작업 | Microsoft.네트워크/연결/시작 패킷 캡처/작업 | 가상 네트워크 게이트웨이 연결 패킷 캡처를 시작합니다. |
> | 작업 | Microsoft.네트워크/연결/중지 패킷 캡처/작업 | 가상 네트워크 게이트웨이 연결 패킷 캡처를 중지합니다. |
> |  | **연결/공유 키** |  |
> | 작업 | Microsoft.Network/connections/sharedKey/read | VirtualNetworkGatewayConnection SharedKey를 가져옵니다. |
> | 작업 | Microsoft.Network/connections/sharedKey/write | VirtualNetworkGatewayConnection SharedKey를 만들거나 기존 VirtualNetworkGatewayConnection SharedKey를 업데이트합니다. |
> |  | **ddosCustomPolicies** |  |
> | 작업 | Microsoft.Network/ddosCustomPolicies/read | DDoS 사용자 지정 정책 정의 가져오기 |
> | 작업 | Microsoft.Network/ddosCustomPolicies/write | DDoS 사용자 지정 정책을 만들거나 기존 DDoS 사용자 지정 정책 업데이트 |
> | 작업 | Microsoft.Network/ddosCustomPolicies/delete | DDoS 사용자 지정 정책 삭제 |
> |  | **ddos보호계획** |  |
> | 작업 | Microsoft.Network/ddosProtectionPlans/read | DDoS 보호 계획을 가져옵니다. |
> | 작업 | Microsoft.Network/ddosProtectionPlans/write | DDoS 보호 계획을 만들거나 DDoS 보호 계획을 업데이트합니다.  |
> | 작업 | Microsoft.Network/ddosProtectionPlans/delete | DDoS 보호 계획을 삭제합니다. |
> | 작업 | Microsoft.Network/ddosProtectionPlans/join/action | DDoS 보호 계획에 가입합니다. 경고할 수 없습니다. |
> |  | **dns작업 결과** |  |
> | 작업 | Microsoft.Network/dnsoperationresults/read | DNS 작업의 결과를 가져옵니다. |
> |  | **dns운영 상태** |  |
> | 작업 | Microsoft.Network/dnsoperationstatuses/read | DNS 작업의 상태를 가져옵니다.  |
> |  | **dnszones** |  |
> | 작업 | Microsoft.Network/dnszones/read | Json 형식의 DNS 영역을 가져옵니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다. 이 명령은 영역 내에 포함된 레코드 집합을 검색하지 않습니다. |
> | 작업 | Microsoft.Network/dnszones/write | 리소스 그룹 내에 DNS 영역을 만들거나 업데이트합니다.  DNS 영역 리소스에서 태그를 업데이트하는 데 사용됩니다. 이 명령은 영역 내에서 레코드 집합을 만들거나 업데이트하는 데 사용할 수 없습니다. |
> | 작업 | Microsoft.Network/dnszones/delete | Json 형식의 DNS 영역을 삭제합니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다. |
> |  | **dnszones/A** |  |
> | 작업 | Microsoft.Network/dnszones/A/read | JSON 형식으로 'A' 유형의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/A/write | DNS 영역 내에서 'A' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/A/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'A'를 입력합니다. |
> |  | **dnszones/AAAA** |  |
> | 작업 | Microsoft.Network/dnszones/AAAA/read | JSON 형식으로 'AAAA'형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/AAAA/write | DNS 영역 내에서 'AAAA' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/AAAA/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'AAAA'를 입력합니다. |
> |  | **dnszones/all** |  |
> | 작업 | Microsoft.Network/dnszones/all/read | 여러 형식의 DNS 레코드 집합을 가져옵니다. |
> |  | **dnszones/CAA** |  |
> | 작업 | Microsoft.Network/dnszones/CAA/read | JSON 형식으로 'CAA' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/CAA/write | DNS 영역 내에서 'CAA' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/CAA/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'CAA'를 입력합니다. |
> |  | **dnszones/CNAME** |  |
> | 작업 | Microsoft.Network/dnszones/CNAME/read | JSON 형식으로 'CNAME' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/CNAME/write | DNS 영역 내에서 'CNAME' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/CNAME/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'CNAME'을 입력합니다. |
> |  | **dnszones/MX** |  |
> | 작업 | Microsoft.Network/dnszones/MX/read | JSON 형식으로 유형 'MX'의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/MX/write | DNS 영역 내에서 'MX' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/MX/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'MX'를 입력합니다. |
> |  | **dnszones/NS** |  |
> | 작업 | Microsoft.Network/dnszones/NS/read | NS 형식의 DNS 레코드 집합을 가져옵니다. |
> | 작업 | Microsoft.Network/dnszones/NS/write | NS 형식의 DNS 레코드 집합을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/dnszones/NS/delete | NS 형식의 DNS 레코드 집합을 삭제합니다. |
> |  | **dnszones/PTR** |  |
> | 작업 | Microsoft.Network/dnszones/PTR/read | JSON 형식으로 'PTR' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/PTR/write | DNS 영역 내에서 'PTR' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/PTR/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'PTR'을 입력합니다. |
> |  | **dnszones/recordsets** |  |
> | 작업 | Microsoft.Network/dnszones/recordsets/read | 여러 형식의 DNS 레코드 집합을 가져옵니다. |
> |  | **dnszones/SOA** |  |
> | 작업 | Microsoft.Network/dnszones/SOA/read | SOA 형식의 DNS 레코드 집합을 가져옵니다. |
> | 작업 | Microsoft.Network/dnszones/SOA/write | SOA 형식의 DNS 레코드 집합을 만들거나 업데이트합니다. |
> |  | **dnszones/SRV** |  |
> | 작업 | Microsoft.Network/dnszones/SRV/read | JSON 형식으로 유형 'SRV'의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/SRV/write | SRV 형식의 레코드 집합을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/dnszones/SRV/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'SRV'를 입력합니다. |
> |  | **dnszones/TXT** |  |
> | 작업 | Microsoft.Network/dnszones/TXT/read | JSON 형식으로 유형 'TXT'의 레코드 세트를 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | Microsoft.Network/dnszones/TXT/write | DNS 영역 내에서 'TXT' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | Microsoft.Network/dnszones/TXT/delete | 지정된 이름의 레코드 집합을 제거하고 DNS 영역에서 'TXT'를 입력합니다. |
> |  | **익스프레스루트회로** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/read | ExpressRouteCircuit을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/write | ExpressRouteCircuit을 만들거나 기존 ExpressRouteCircuit을 업데이트합니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/join/action | 고속 경로 회로에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/delete | ExpressRouteCircuit을 삭제합니다. |
> |  | **익스프레스루트회로/권한 부여** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/authorizations/read | ExpressRouteCircuit Authorization을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/authorizations/write | ExpressRouteCircuit Authorization을 만들거나 기존 ExpressRouteCircuit Authorization을 업데이트합니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/authorizations/delete | ExpressRouteCircuit Authorization을 삭제합니다. |
> |  | **익스프레스루트회로/피어링** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/read | ExpressRouteCircuit Peering을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/write | ExpressRouteCircuit Peering을 만들거나 기존 ExpressRouteCircuit Peering을 업데이트합니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/delete | ExpressRouteCircuit Peering을 삭제합니다. |
> |  | **익스프레스루트서킷/피어링/어프테이블** |  |
> | 작업 | 마이크로소프트.네트워크/익스프레스루트회로/피어링/어프테이블/읽기 | ExpressRouteCircuit Peering ArpTable을 가져옵니다. |
> |  | **익스프레스루트회로/피어링/커넥션** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/connections/read | ExpressRouteCircuit 연결을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/connections/write | ExpressRouteCircuit 연결 리소스를 만들거나 기존 ExpressRouteCircuit 연결 리소스를 업데이트합니다. |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | ExpressRouteCircuit 연결을 삭제합니다. |
> |  | **익스프레스루트회로/피어링/피어커커넥션** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 피어링 기본 경로 회로 연결을 가져옵니다. |
> |  | **익스프레스루트회로/피어링/루트테이블** |  |
> | 작업 | Microsoft.Network/익스프레스루트회로/피어링/경로테이블/읽기 | ExpressRouteCircuit Peering RouteTable을 가져옵니다. |
> |  | **익스프레스루트서킷/피어링/루트테이블요약** |  |
> | 작업 | 마이크로소프트.네트워크/익스프레스루트회로/피어링/경로테이블요약/읽기 | ExpressRouteCircuit Peering RouteTable Summary를 가져옵니다. |
> |  | **익스프레스루트회로/피어링/통계** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/peerings/stats/read | ExpressRouteCircuit Peering Stat을 가져옵니다. |
> |  | **익스프레스루트회로/통계** |  |
> | 작업 | Microsoft.Network/expressRouteCircuits/stats/read | ExpressRouteCircuit Stat을 가져옵니다. |
> |  | **익스프레스루트크로스커넥션** |  |
> | 작업 | Microsoft.Network/expressRouteCrossConnections/read | Express Route 교차 연결을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteCrossConnections/join/action | 고속 루트 교차 연결에 연결합니다. 경고할 수 없습니다. |
> |  | **expressRouteCrossConnections/peerings** |  |
> | 작업 | Microsoft.Network/expressRouteCrossConnections/peerings/read | Express Route 교차 연결 피어링을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteCrossConnections/peerings/write | Express Route 교차 연결 피어링을 만들거나 기존 Express Route 교차 연결 피어링을 업데이트 합니다. |
> | 작업 | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Express Route 교차 연결 피어링을 삭제합니다. |
> |  | **익스프레스루트크로스커넥션/피어링/어프테이블** |  |
> | 작업 | 마이크로소프트.네트워크/익스프레스루트크로스연결/피어링/어프테이블/읽기 | Express Route 교차 연결 피어링 Arp 테이블을 가져옵니다. |
> |  | **익스프레스루트크로스커넥션/피어링/루트테이블** |  |
> | 작업 | 마이크로소프트.네트워크/익스프레스루트크로스연결/피어링/루트테이블/읽기 | Express Route 교차 연결 피어링 경로 테이블을 가져옵니다. |
> |  | **expressRouteCrossConnections/피어링/경로테이블요약** |  |
> | 작업 | 마이크로소프트.네트워크/익스프레스루트크로스연결/피어링/루트테이블요약/읽기 | Express Route 교차 연결 피어링 경로 테이블 요약 정보를 가져옵니다. |
> |  | **익스프레스루트게이트웨이** |  |
> | 작업 | Microsoft.Network/expressRouteGateways/read | Express Route 게이트웨이를 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteGateways/join/action | 익스프레스 루트 게이트웨이에 가입합니다. 경고할 수 없습니다. |
> |  | **익스프레스루트게이트웨이/익스프레스루트커넥션** |  |
> | 작업 | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Express Route 연결을 가져옵니다. |
> | 작업 | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Express Route 연결을 만들거나 기존 Express Route 연결을 업데이트 합니다. |
> | 작업 | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Express Route 연결을 삭제합니다. |
> |  | **익스프레스루트포트** |  |
> | 작업 | Microsoft.Network/expressRoutePorts/read | ExpressRoutePorts를 가져옵니다. |
> | 작업 | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/expressRoutePorts/join/action | 익스프레스 루트 포트를 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/expressRoutePorts/delete | ExpressRoutePorts를 삭제합니다. |
> |  | **익스프레스루트포트/링크** |  |
> | 작업 | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink를 가져옵니다. |
> |  | **익스프레스루트포트위치** |  |
> | 작업 | Microsoft.Network/expressRoutePortsLocations/read | Express 경로 포트 위치를 가져옵니다. |
> |  | **expressRouteServiceProviders** |  |
> | 작업 | Microsoft.Network/expressRouteServiceProviders/read | Express Route 서비스 공급자를 가져옵니다. |
> |  | **방화벽정책** |  |
> | 작업 | 마이크로소프트.네트워크/방화벽정책/읽기 | 방화벽 정책 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/방화벽정책/쓰기 | 방화벽 정책을 만들거나 기존 방화벽 정책을 업데이트합니다. |
> | 작업 | Microsoft.네트워크/방화벽정책/조인/작업 | 방화벽 정책에 가입합니다. 경고할 수 없습니다. |
> | 작업 | 마이크로소프트.네트워크/방화벽정책/삭제 | 방화벽 정책 삭제 |
> |  | **방화벽정책/규칙그룹** |  |
> | 작업 | Microsoft.네트워크/방화벽정책/규칙 그룹/읽기 | 방화벽 정책 규칙 그룹 가져옵니다. |
> | 작업 | Microsoft.네트워크/방화벽정책/규칙 그룹/쓰기 | 방화벽 정책 규칙 그룹을 만들거나 기존 방화벽 정책 규칙 그룹을 업데이트합니다. |
> | 작업 | Microsoft.네트워크/방화벽정책/규칙 그룹/삭제 | 방화벽 정책 규칙 그룹 삭제 |
> |  | **프론트 도어** |  |
> | 작업 | Microsoft.Network/frontDoors/read | Front Door 가져오기 |
> | 작업 | Microsoft.Network/frontDoors/write | Front Door를 만들거나 업데이트 |
> | 작업 | Microsoft.Network/frontDoors/delete | Front Door 삭제 |
> | 작업 | Microsoft.Network/frontDoors/purge/action | Front Door에서 캐시된 콘텐츠 제거 |
> | 작업 | Microsoft.Network/frontDoors/validateCustomDomain/action | Front Door에 대한 프런트 엔드 엔드포인트 유효성 검사 |
> |  | **프론트도어/백엔드풀** |  |
> | 작업 | Microsoft.Network/frontDoors/backendPools/read | 백 엔드 풀 가져오기 |
> | 작업 | Microsoft.Network/frontDoors/backendPools/write | 백 엔드 풀 만들기 또는 업데이트 |
> | 작업 | Microsoft.Network/frontDoors/backendPools/delete | 백 엔드 풀 삭제 |
> |  | **프론트도어/프론트엔드엔드엔드포인트** |  |
> | 작업 | Microsoft.Network/frontDoors/frontendEndpoints/read | 프런트 엔드 엔드포인트 가져오기 |
> | 작업 | Microsoft.Network/frontDoors/frontendEndpoints/write | 프런트 엔드 엔드포인트를 만들거나 업데이트 |
> | 작업 | Microsoft.Network/frontDoors/frontendEndpoints/delete | 프런트 엔드 엔드포인트 삭제 |
> | 작업 | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 프런트 엔드 엔드포인트에서 HTTPS를 사용하도록 설정 |
> | 작업 | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 프런트 엔드 엔드포인트에서 HTTPS를 사용하지 않도록 설정 |
> |  | **프론트도어/헬스프로브설정** |  |
> | 작업 | Microsoft.Network/frontDoors/healthProbeSettings/read | 상태 프로브 설정 가져오기 |
> | 작업 | Microsoft.Network/frontDoors/healthProbeSettings/write | 상태 프로브 설정을 만들거나 업데이트 |
> | 작업 | Microsoft.Network/frontDoors/healthProbeSettings/delete | 상태 프로브 설정 삭제 |
> |  | **프론트도어/로드밸런싱설정** |  |
> | 작업 | Microsoft.Network/frontDoors/loadBalancingSettings/read | 부하 분산 설정 가져오기 |
> | 작업 | Microsoft.Network/frontDoors/loadBalancingSettings/write | 부하 분산 설정을 만들거나 업데이트 |
> | 작업 | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 부하 분산 설정을 만들거나 업데이트 |
> |  | **프론트도어/라우팅규칙** |  |
> | 작업 | Microsoft.Network/frontDoors/routingRules/read | 라우팅 규칙 가져오기 |
> | 작업 | Microsoft.Network/frontDoors/routingRules/write | 라우팅 규칙을 만들거나 업데이트 |
> | 작업 | Microsoft.Network/frontDoors/routingRules/delete | 라우팅 규칙 삭제 |
> |  | **프론트도어웹응용프로그램관리규칙세트** |  |
> | 작업 | 마이크로소프트.네트워크/프론트도어웹응용방화벽관리규칙세트/읽기 | 웹 응용 프로그램 방화벽 관리 되는 규칙 집합을 가져옵니다. |
> |  | **프론트도어웹응용방화벽정책** |  |
> | 작업 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 웹 애플리케이션 방화벽 정책 가져오기 |
> | 작업 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 웹 애플리케이션 방화벽 정책을 만들거나 업데이트 |
> | 작업 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 웹 애플리케이션 방화벽 정책 삭제 |
> | 작업 | 마이크로소프트.네트워크/프론트도어웹응용방화벽정책/조인/액션 | 웹 응용 프로그램 방화벽 정책에 가입합니다. 경고할 수 없습니다. |
> |  | **ip할당** |  |
> | 작업 | 마이크로소프트.네트워크/ip할당/읽기 | IP 할당 받기 |
> | 작업 | 마이크로소프트.네트워크/ip할당/쓰기 | Ip 할당을 만들거나 기존 Ip 할당을 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/ip할당/삭제 | Ip 할당 삭제 |
> |  | **ip 그룹** |  |
> | 작업 | 마이크로소프트.네트워크/ip그룹/읽기 | Ip그룹 을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/ipGroups/쓰기 | Ip그룹 생성 또는 기존 Ip그룹 업데이트 |
> | 작업 | 마이크로소프트.네트워크/ipGroups/유효성 검사/작업 | Ip그룹 유효성 검사 |
> | 작업 | Microsoft.네트워크/ipGroups/업데이트참조/작업 | IpGroup에서 참조 업데이트 |
> | 작업 | 마이크로소프트.네트워크/ipGroups/조인/작업 | Ip그룹에 가입합니다. 경고할 수 없습니다. |
> | 작업 | 마이크로소프트.네트워크/ipGroups/삭제 | Ip그룹 삭제 |
> |  | **loadBalancers** |  |
> | 작업 | Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/loadBalancers/write | 부하 분산 장치를 만들거나 기존 부하 분산 장치를 업데이트합니다. |
> | 작업 | Microsoft.Network/loadBalancers/delete | 부하 분산 장치를 삭제합니다. |
> |  | **로드밸런스/백엔드주소풀** |  |
> | 작업 | Microsoft.Network/loadBalancers/backendAddressPools/read | 부하 분산 장치 백 엔드 주소 풀 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 로드 밸러서 백엔드 주소 풀에 조인합니다. 경고할 수 없습니다. |
> |  | **로드밸런스/프론트엔드IP구성** |  |
> | 작업 | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 부하 분산 장치 프런트 엔드 IP 구성 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 부하 분산 장치 프런트 엔드 IP 구성을 조인합니다. 경고할 수 없습니다. |
> |  | **로드밸런스/인바운드내트풀** |  |
> | 작업 | Microsoft.Network/loadBalancers/inboundNatPools/read | 부하 분산 장치 인바운드 NAT 풀 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 로드 밸러블러 인바운드 NAT 풀에 조인합니다. 경고할 수 없습니다. |
> |  | **로드 밸런스 /인바운드나트규칙** |  |
> | 작업 | Microsoft.Network/loadBalancers/inboundNatRules/read | 부하 분산 장치 인바운드 NAT 규칙 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/loadBalancers/inboundNatRules/write | 부하 분산 장치 인바운드 NAT 규칙을 만들거나 기존 부하 분산 장치의 인바운드 NAT 규칙을 업데이트합니다. |
> | 작업 | Microsoft.Network/loadBalancers/inboundNatRules/delete | 부하 분산 장치 인바운드 NAT 규칙을 삭제합니다. |
> | 작업 | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 로드 밸러블러 인바운드 nat 규칙을 조인합니다. 경고할 수 없습니다. |
> |  | **로드 밸런스/로드 밸런싱규칙** |  |
> | 작업 | Microsoft.Network/loadBalancers/loadBalancingRules/read | 부하 분산 장치 부하 분산 규칙 정의를 가져옵니다. |
> |  | **로드밸서/네트워크인터페이스** |  |
> | 작업 | Microsoft.Network/loadBalancers/networkInterfaces/read | 부하 분산 장치의 모든 네트워크 인터페이스에 대한 참조를 가져옵니다. |
> |  | **로드밸런스/아웃바운드규칙** |  |
> | 작업 | Microsoft.Network/loadBalancers/outboundRules/read | 부하 분산 장치 아웃바운드 규칙 정의를 가져옵니다. |
> |  | **로드밸런스/프로브** |  |
> | 작업 | Microsoft.Network/loadBalancers/probes/read | 부하 분산 장치 프로브를 가져옵니다. |
> | 작업 | Microsoft.Network/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. 경고할 수 없습니다. |
> |  | **로드밸런스/가상머신** |  |
> | 작업 | Microsoft.Network/loadBalancers/virtualMachines/read | 부하 분산 장치의 모든 가상 머신에 대한 참조를 가져옵니다. |
> |  | **localnetworkgateways** |  |
> | 작업 | Microsoft.Network/localnetworkgateways/read | LocalNetworkGateway를 가져옵니다. |
> | 작업 | Microsoft.Network/localnetworkgateways/write | LocalNetworkGateway를 만들거나 기존 LocalNetworkGateway를 업데이트합니다. |
> | 작업 | Microsoft.Network/localnetworkgateways/delete | LocalNetworkGateway를 삭제합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 가속 네트워킹 지원을 확인합니다. |
> | 작업 | 마이크로소프트.네트워크/위치/배치알림사설엔드포인트For리소스이동/액션 | 리소스 이동을 위해 일괄 처리로 개인 끝점에 이를 지정합니다. |
> | 작업 | 마이크로소프트.네트워크/위치/체크PrivateLink서비스가시성/작업 | 개인 링크 서비스 가시성 확인 |
> | 작업 | Microsoft.Network/locations/bareMetalTenants/action | 운영 체제 미 설치 컴퓨터 테넌트를 할당하거나 유효성을 검사합니다. |
> |  | **위치/자동 승인개인 링크서비스** |  |
> | 작업 | 마이크로소프트.네트워크/위치/자동 승인된 개인 링크 서비스/읽기 | 자동 승인 개인 링크 서비스 받기 |
> |  | **위치/사용 가능 위임** |  |
> | 작업 | Microsoft.Network/locations/availableDelegations/read | 사용 가능한 위임을 가져옵니다. |
> |  | **위치/사용 가능프라이빗 엔드포인트유형** |  |
> | 작업 | 마이크로소프트.네트워크/위치/사용 가능한개인 엔드포인트 유형/읽기 | 사용 가능한 개인 엔드포인트 리소스 가져오기 |
> |  | **위치/사용 가능서비스알리아스** |  |
> | 작업 | 마이크로소프트.네트워크/위치/사용 가능한서비스알리아스/읽기 | 사용 가능한 서비스 별칭 을 가져옵니다. |
> |  | **위치/체크DnsName가용성** |  |
> | 작업 | Microsoft.Network/locations/checkDnsNameAvailability/read | DNS 레이블을 지정된 위치에서 사용할 수 있는지 확인합니다. |
> |  | **위치/작업결과** |  |
> | 작업 | Microsoft.Network/locations/operationResults/read | 비동기 POST 또는 DELETE 작업의 작업 결과를 가져옵니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.Network/locations/operations/read | 비동기 작업의 상태를 나타내는 작업 리소스를 가져옵니다. |
> |  | **위치/서비스태그** |  |
> | 작업 | 마이크로소프트.네트워크/위치/서비스태그/읽기 | 서비스 태그 받기 |
> |  | **위치/지원가상머신크기** |  |
> | 작업 | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 지원되는 가상 머신 크기를 가져옵니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.Network/locations/usages/read | 리소스 사용 메트릭을 가져옵니다. |
> |  | **위치/가상네트워크사용가능엔드포인트서비스** |  |
> | 작업 | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 사용 가능한 가상 네트워크 엔드포인트 서비스 목록을 가져옵니다. |
> |  | **natGateways** |  |
> | 작업 | 마이크로소프트.네트워크/natGateways/조인/작업 | NAT 게이트웨이 조인 |
> |  | **natRule컬렉션** |  |
> | 작업 | 마이크로소프트.네트워크/natRule컬렉션/읽기 | Azure 방화벽 NatRuleCollection 을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/natRule컬렉션/쓰기 | 생성OrUpdates Azure 방화벽 NatRuleCollection |
> | 작업 | 마이크로소프트.네트워크/natRule컬렉션/삭제 | 삭제 Azure 방화벽 NatRuleCollection |
> |  | **네트워크실험프로필** |  |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/읽기 | 인터넷 분석기 프로필 받기 |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/쓰기 | 인터넷 분석기 프로필 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/삭제 | 인터넷 분석기 프로필 삭제 |
> |  | **네트워크실험프로필/실험** |  |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/실험/읽기 | 인터넷 분석기 테스트 받기 |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/실험/쓰기 | 인터넷 분석기 테스트 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/실험/삭제 | 인터넷 분석기 테스트 삭제 |
> | 작업 | Microsoft.Network/네트워크실험프로필/실험/타임시리즈/액션 | 인터넷 분석기 테스트의 타임시리즈 받기 |
> | 작업 | Microsoft.Network/네트워크실험프로필/실험/대기 시간 스코어카드/액션 | 인터넷 분석기 테스트의 대기 시간 성과 기록표 받기 |
> |  | **네트워크실험프로파일/사전 구성엔드포인트** |  |
> | 작업 | 마이크로소프트.네트워크/네트워크실험프로필/사전 구성된엔드포인트/읽기 | 인터넷 분석기 프로필의 사전 구성된 엔드포인트 받기 |
> |  | **네트워크인의도정책** |  |
> | 작업 | Microsoft.Network/networkIntentPolicies/read | 네트워크 의도 정책 설명을 가져옵니다. |
> | 작업 | Microsoft.Network/networkIntentPolicies/write | 네트워크 의도 정책을 만들거나 기존 네트워크 의도 정책을 업데이트합니다. |
> | 작업 | Microsoft.Network/networkIntentPolicies/delete | 네트워크 의도 정책을 삭제합니다. |
> |  | **네트워크인터페이스** |  |
> | 작업 | Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | 작업 | Microsoft.Network/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | 작업 | Microsoft.Network/networkInterfaces/join/action | 가상 컴퓨터를 네트워크 인터페이스에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/networkInterfaces/delete | 네트워크 인터페이스를 삭제합니다. |
> | 작업 | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | VM의 네트워크 인터페이스에 구성된 경로 테이블을 가져옵니다. |
> | 작업 | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | VM의 네트워크 인터페이스에 구성된 네트워크 보안 그룹을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/네트워크인터페이스/업데이트부모닉어치튼탄성/액션 | 탄력적 NIC에 연결된 상위 NIC를 업데이트합니다. |
> |  | **네트워크인터페이스/ip구성** |  |
> | 작업 | Microsoft.Network/networkInterfaces/ipconfigurations/read | 네트워크 인터페이스의 IP 구성 정의를 가져옵니다.  |
> | 작업 | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 네트워크 인터페이스 IP 구성을 조인합니다. 경고할 수 없습니다. |
> |  | **네트워크인터페이스/로드밸런스** |  |
> | 작업 | Microsoft.Network/networkInterfaces/loadBalancers/read | 네트워크 인터페이스가 속하는 모든 부하 분산 장치를 가져옵니다. |
> |  | **네트워크인터페이스/탭구성** |  |
> | 작업 | Microsoft.Network/networkInterfaces/tapConfigurations/read | 네트워크 인터페이스 탭 구성을 가져옵니다. |
> | 작업 | Microsoft.Network/networkInterfaces/tapConfigurations/write | 네트워크 인터페이스 탭 구성을 만들거나 기존 네트워크 인터페이스 탭 구성을 업데이트합니다. |
> | 작업 | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 네트워크 인터페이스 탭 구성을 삭제합니다. |
> |  | **네트워크 프로필** |  |
> | 작업 | Microsoft.Network/networkProfiles/read | 네트워크 프로필을 가져옵니다. |
> | 작업 | Microsoft.Network/networkProfiles/write | 네트워크 프로필을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/networkProfiles/delete | 네트워크 프로필을 삭제합니다. |
> | 작업 | Microsoft.Network/networkProfiles/setContainers/action | 컨테이너를 설정합니다. |
> | 작업 | Microsoft.Network/networkProfiles/removeContainers/action | 컨테이너를 제거합니다. |
> | 작업 | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 컨테이너 네트워크 인터페이스를 설정합니다. |
> |  | **네트워크규칙컬렉션** |  |
> | 작업 | 마이크로소프트.네트워크/네트워크규칙컬렉션/읽기 | Azure 방화벽 네트워크규칙 컬렉션 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/네트워크규칙컬렉션/쓰기 | 생성자업데이트 Azure 방화벽 네트워크규칙컬렉션 |
> | 작업 | 마이크로소프트.네트워크/네트워크규칙컬렉션/삭제 | 삭제 Azure 방화벽 네트워크규칙컬렉션 |
> |  | **networkSecurityGroups** |  |
> | 작업 | Microsoft.Network/networkSecurityGroups/read | 네트워크 보안 그룹 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/networkSecurityGroups/write | 네트워크 보안 그룹을 만들거나 기존 네트워크 보안 그룹을 업데이트합니다. |
> | 작업 | Microsoft.Network/networkSecurityGroups/delete | 네트워크 보안 그룹을 삭제합니다. |
> | 작업 | Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹에 가입합니다. 경고할 수 없습니다. |
> |  | **네트워크 보안 그룹/기본보안규칙** |  |
> | 작업 | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 기본 보안 규칙 정의를 가져옵니다. |
> |  | **네트워크 보안 그룹/보안규칙** |  |
> | 작업 | Microsoft.Network/networkSecurityGroups/securityRules/read | 보안 규칙 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/networkSecurityGroups/securityRules/write | 보안 규칙을 만들거나 기존 보안 규칙을 업데이트합니다. |
> | 작업 | Microsoft.Network/networkSecurityGroups/securityRules/delete | 보안 규칙을 삭제합니다. |
> |  | **networkWatchers** |  |
> | 작업 | Microsoft.Network/networkWatchers/read | Network Watcher 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/write | Network Watcher를 만들거나 기존 Network Watcher를 업데이트합니다. |
> | 작업 | Microsoft.Network/networkWatchers/delete | Network Watcher를 삭제합니다. |
> | 작업 | Microsoft.Network/networkWatchers/configureFlowLog/action | 대상 리소스에 대한 흐름 로깅을 구성합니다. |
> | 작업 | Microsoft.Network/networkWatchers/ipFlowVerify/action | 패킷이 특정 대상에서 허용될지 또는 거부될지를 반환합니다. |
> | 작업 | Microsoft.Network/networkWatchers/nextHop/action | 지정된 목표 및 대상 IP 주소에 대해 다음 홉 형식 및 다음 홉 IP 주소를 반환합니다. |
> | 작업 | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 리소스에 대한 흐름 로깅의 상태를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 이전에 실행되었거나 현재 실행 중인 문제 해결 작업의 문제 해결 결과를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/securityGroupView/action | VM에 적용된 유효한 구성된 네트워크 보안 그룹 규칙을 봅니다. |
> | 작업 | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 네트워크 구성을 진단합니다. |
> | 작업 | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 지정된 엔드포인트 간의 연결 모니터링 쿼리를 일괄 처리합니다. |
> | 작업 | Microsoft.Network/networkWatchers/topology/action | 리소스 그룹에 포함된 리소스의 네트워크 수준 보기 및 해당 관계를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/troubleshoot/action | Azure의 네트워킹 리소스에 대한 문제 해결을 시작합니다. |
> | 작업 | Microsoft.Network/networkWatchers/connectivityCheck/action | 가상 머신에서 다른 VM 또는 임의의 원격 서버를 포함한 특정 엔드포인트로 직접 TCP 연결을 설정할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 지정된 위치에서 Azure 지역까지 인터넷 서비스 공급자의 상대 대기 시간을 반환합니다. |
> | 작업 | Microsoft.Network/networkWatchers/availableProvidersList/action | 지정된 Azure 지역에 사용 가능한 모든 인터넷 서비스 공급자를 반환합니다. |
> |  | **네트워크 감시자/연결모니터** |  |
> | 작업 | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 지정된 엔드포인트 간의 연결 모니터링을 시작합니다. |
> | 작업 | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 지정된 엔드포인트 간의 연결 모니터링을 중지/일시 중지합니다. |
> | 작업 | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 지정된 엔드포인트 간의 연결 모니터링을 쿼리합니다. |
> | 작업 | Microsoft.Network/networkWatchers/connectionMonitors/read | 연결 모니터 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/connectionMonitors/write | 연결 모니터를 만듭니다. |
> | 작업 | Microsoft.Network/networkWatchers/connectionMonitors/delete | 연결 모니터를 삭제합니다. |
> |  | **네트워크 감시자/플로우로그** |  |
> | 작업 | 마이크로소프트.네트워크/네트워크 감시자/흐름 로그/읽기 | 흐름 로그 세부 정보 가져오기 |
> | 작업 | 마이크로소프트.네트워크/네트워크 감시자/플로우로그/쓰기 | 흐름 로그 생성 |
> | 작업 | 마이크로소프트.네트워크/네트워크 감시자/플로우로그/삭제 | 흐름 로그 삭제 |
> |  | **네트워크 감시자/렌즈** |  |
> | 작업 | Microsoft.Network/networkWatchers/lenses/start/action | 지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 시작합니다. |
> | 작업 | Microsoft.Network/networkWatchers/lenses/stop/action | 지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 중지/일시 중지합니다. |
> | 작업 | Microsoft.Network/networkWatchers/lenses/query/action | 지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 쿼리합니다. |
> | 작업 | Microsoft.Network/networkWatchers/lenses/read | 렌즈 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/lenses/write | 렌즈를 만듭니다. |
> | 작업 | Microsoft.Network/networkWatchers/lenses/delete | 렌즈를 삭제합니다. |
> |  | **네트워크 감시자/패킷캡처** |  |
> | 작업 | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 패킷 캡처 리소스의 속성 및 상태에 대한 정보를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 실행 중인 패킷 캡처 세션을 중지합니다. |
> | 작업 | Microsoft.Network/networkWatchers/packetCaptures/read | 패킷 캡처 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/packetCaptures/write | 패킷 캡처를 만듭니다. |
> | 작업 | Microsoft.Network/networkWatchers/packetCaptures/delete | 패킷 캡처를 삭제합니다. |
> |  | **네트워크 감시자/핑메시** |  |
> | 작업 | Microsoft.Network/networkWatchers/pingMeshes/start/action | 지정된 VM 간에 PingMesh를 시작합니다. |
> | 작업 | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 지정된 VM 간에 PingMesh를 중지합니다. |
> | 작업 | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh 세부 정보를 가져옵니다. |
> | 작업 | Microsoft.Network/networkWatchers/pingMeshes/write | PingMesh를 만듭니다. |
> | 작업 | Microsoft.Network/networkWatchers/pingMeshes/delete | PingMesh를 삭제합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Network/operations/read | 사용 가능한 작업을 가져옵니다. |
> |  | **p2sVpn게이트웨이** |  |
> | 작업 | Microsoft.Network/p2sVpnGateways/read | P2SVpnGateway를 가져옵니다. |
> | 작업 | Microsoft.Network/p2sVpnGateways/write | P2SVpnGateway를 씁니다. |
> | 작업 | Microsoft.Network/p2sVpnGateways/delete | P2SVpnGateway를 삭제합니다. |
> | 작업 | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | P2SVpnGateway에 대한 Vpn 프로필을 생성합니다. |
> | 작업 | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | P2SVpnGateway에 대한 P2S Vpn 연결 상태를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/p2sVpn게이트웨이/getp2svpn연결 상세/액션 | P2SVpn게이트웨이에 대한 자세한 P2S VPN 연결 상태를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/p2sVpn게이트웨이/분리p2svpn연결/액션 | p2s VPN 연결 끊기 |
> |  | **프라이빗Dns작업결과** |  |
> | 작업 | 마이크로소프트.네트워크/개인Dns작업결과/읽기 | 개인 DNS 작업의 결과 가져옵니다. |
> |  | **privateDns운영 상태** |  |
> | 작업 | 마이크로소프트.네트워크/개인Dns운영 상태/읽기 | 개인 DNS 작업의 상태를 가져옵니다. |
> |  | **프라이빗DnsZones** |  |
> | 작업 | 마이크로소프트.네트워크/개인DnsZones/읽기 | JSON 형식으로 개인 DNS 영역 속성을 가져옵니다. 이 명령은 개인 DNS 영역이 연결된 가상 네트워크 또는 영역 내에 포함된 레코드 집합을 검색하지 않습니다. |
> | 작업 | 마이크로소프트.네트워크/개인DnsZones/쓰기 | 리소스 그룹 내에서 개인 DNS 영역을 만들거나 업데이트합니다. 이 명령은 영역 내에서 가상 네트워크 링크 또는 레코드 집합을 만들거나 업데이트하는 데 사용할 수 없습니다. |
> | 작업 | 마이크로소프트.네트워크/개인DnsZones/삭제 | 개인 DNS 영역을 삭제합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/조인/액션 | 프라이빗 DNS 영역 가입 |
> |  | **프라이빗Dns존/A** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/A/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'A' 유형의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/A/쓰기 | 개인 DNS 영역 내에서 'A' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/A/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'A'를 입력합니다. |
> |  | **프라이빗들존/AAAA** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/AAAA/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'AAAA' 유형의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/AAAA/쓰기 | 개인 DNS 영역 내에서 'AAAA' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/AAAA/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'AAAA'를 입력합니다. |
> |  | **프라이빗Dns존/모두** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/ALL/읽기 | 유형 간에 개인 DNS 레코드 집합 가져옵니다. |
> |  | **프라이빗Dns존/CNAME** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/CNAME/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'CNAME' 형식의 레코드 집합을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/CNAME/쓰기 | 개인 DNS 영역 내에서 'CNAME' 형식의 레코드 집합을 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/CNAME/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'CNAME'을 입력합니다. |
> |  | **프라이빗센스존/MX** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/MX/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'MX' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/MX/쓰기 | 개인 DNS 영역 내에서 'MX' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/MX/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'MX'를 입력합니다. |
> |  | **프라이빗Dns존/PTR** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/PTR/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'PTR' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/PTR/쓰기 | 개인 DNS 영역 내에서 'PTR' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/PTR/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'PTR'을 입력합니다. |
> |  | **프라이빗DnsZones/레코드 세트** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/레코드 집합/읽기 | 유형 간에 개인 DNS 레코드 집합 가져옵니다. |
> |  | **프라이빗DnsZones/SOA** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/SOA/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'SOA' 형식의 레코드 집합을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/SOA/쓰기 | 개인 DNS 영역 내에서 'SOA' 형식의 레코드 집합을 업데이트합니다. |
> |  | **프라이빗DnsZones/SRV** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/SRV/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'SRV' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/SRV/쓰기 | 개인 DNS 영역 내에서 'SRV' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/SRV/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'SRV'를 입력합니다. |
> |  | **프라이빗디존/TXT** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/TXT/읽기 | JSON 형식으로 개인 DNS 영역 내에서 'TXT' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/TXT/쓰기 | 개인 DNS 영역 내에서 'TXT' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/TXT/삭제 | 지정된 이름의 레코드 집합을 제거하고 개인 DNS 영역에서 'TXT'를 입력합니다. |
> |  | **프라이빗DnsZones/가상네트워크링크** |  |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/가상네트워크링크/읽기 | JSON 형식으로 가상 네트워크 속성에 대한 개인 DNS 영역 링크를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/가상네트워크링크/쓰기 | 가상 네트워크에 대한 개인 DNS 영역 링크를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/프라이빗DnsZones/가상네트워크링크/삭제 | 가상 네트워크에 대한 개인 DNS 영역 링크를 삭제합니다. |
> |  | **프라이빗엔드포인트리디렉션** |  |
> | 작업 | 마이크로소프트.네트워크/개인엔드포인트다이렉트맵/읽기 | 개인 엔드포인트 리디렉션맵 을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/개인엔드포인트리디렉션/쓰기 | 전용 엔드포인트 리디렉션맵 을 만들거나 기존 프라이빗 엔드포인트 리디렉션맵 업데이트 |
> |  | **프라이빗 엔드포인트** |  |
> | 작업 | 마이크로소프트.네트워크/개인엔드포인트/읽기 | 개인 끝점 리소스를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/개인엔드포인트/쓰기 | 새 개인 끝점을 만들거나 기존 개인 끝점을 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/개인엔드포인트/삭제 | 개인 끝점 리소스를 삭제합니다. |
> |  | **프라이빗엔드포인트/프라이빗Dns존콘피그** |  |
> | 작업 | 마이크로소프트.네트워크/개인엔드포인트/프라이빗DnsZoneConfigs/쓰기 | 개인 DNS 영역 구성을 넣습니다. |
> |  | **privateLinkServices** |  |
> | 작업 | Microsoft.Network/privateLinkServices/read | 프라이빗 링크 서비스 리소스를 가져옵니다. |
> | 작업 | Microsoft.Network/privateLinkServices/write | 새로운 프라이빗 링크 서비스를 만들거나 기존 프라이빗 링크 서비스를 업데이트합니다. |
> | 작업 | Microsoft.Network/privateLinkServices/delete | 프라이빗 링크 서비스 자원을 삭제합니다. |
> |  | **프라이빗링크서비스/프라이빗엔드포인트커넥션** |  |
> | 작업 | 마이크로소프트.네트워크/개인링크 서비스/개인엔드포인트연결/읽기 | 개인 끝점 연결 정의를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/개인링크 서비스/개인엔드포인트연결/쓰기 | 새 개인 끝점 연결을 만들거나 기존 개인 끝점 연결을 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/개인링크 서비스/개인엔드포인트연결/삭제 | 개인 끝점 연결을 삭제합니다. |
> |  | **공개 IP 주소** |  |
> | 작업 | Microsoft.Network/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/publicIPAddresses/write | 공용 IP 주소를 만들거나 기존 공용 IP 주소를 업데이트합니다.  |
> | 작업 | Microsoft.Network/publicIPAddresses/delete | 공용 IP 주소를 삭제합니다. |
> | 작업 | Microsoft.Network/publicIPAddresses/join/action | 공용 IP 주소를 조인합니다. 경고할 수 없습니다. |
> |  | **퍼블릭IP사전수정** |  |
> | 작업 | Microsoft.Network/publicIPPrefixes/read | 공용 IP 접두사 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/publicIPPrefixes/write | 공용 IP 접두사를 만들거나 기존 공용 IP 접두사를 업데이트합니다. |
> | 작업 | Microsoft.Network/publicIPPrefixes/delete | 공용 IP 접두사를 삭제합니다. |
> | 작업 | Microsoft.Network/publicIPPrefixes/join/action | 공용 IP사전에 가입합니다. 경고할 수 없습니다. |
> |  | **경로 필터** |  |
> | 작업 | Microsoft.Network/routeFilters/read | 경로 필터 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/routeFilters/join/action | 경로 필터를 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/routeFilters/delete | 경로 필터 정의를 삭제합니다. |
> | 작업 | Microsoft.Network/routeFilters/write | 경로 필터를 만들거나 기존 경로 필터를 업데이트합니다. |
> |  | **경로 필터/경로필터규칙** |  |
> | 작업 | Microsoft.Network/routeFilters/routeFilterRules/read | 경로 필터 규칙 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/routeFilters/routeFilterRules/write | 경로 필터 규칙을 만들거나 기존 경로 필터를 규칙을 업데이트합니다. |
> | 작업 | Microsoft.Network/routeFilters/routeFilterRules/delete | 경로 필터 규칙 정의를 삭제합니다. |
> |  | **루트테이블** |  |
> | 작업 | Microsoft.Network/routeTables/read | 경로 테이블 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/routeTables/write | 경로 테이블을 만들거나 기존 경로 테이블을 업데이트합니다. |
> | 작업 | Microsoft.Network/routeTables/delete | 경로 테이블 정의를 삭제합니다. |
> | 작업 | Microsoft.Network/routeTables/join/action | 라우트 테이블을 조인합니다. 경고할 수 없습니다. |
> |  | **경로테이블/경로** |  |
> | 작업 | Microsoft.Network/routeTables/routes/read | 경로 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/routeTables/routes/write | 경로를 만들거나 기존 경로를 업데이트합니다. |
> | 작업 | Microsoft.Network/routeTables/routes/delete | 경로 정의를 삭제합니다. |
> |  | **서비스 엔드포인트정책** |  |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/read | 서비스 엔드포인트 정책 설명을 가져옵니다. |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/write | 서비스 엔드포인트 정책을 만들거나 기존 서비스 엔드포인트 정책을 업데이트합니다. |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/delete | 서비스 엔드포인트 정책을 삭제합니다. |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/join/action | 서비스 끝점 정책을 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 서브넷 서비스 엔드포인트 정책을 조인합니다. 경고할 수 없습니다. |
> |  | **서비스Endpoint정책/서비스EndpointPolicy정의** |  |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 서비스 엔드포인트 정책 정의 설명을 가져옵니다. |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 서비스 엔드포인트 정책 정의를 만들거나 기존 서비스 엔드포인트 정책 정의를 업데이트합니다. |
> | 작업 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 서비스 엔드포인트 정책 정의를 삭제합니다. |
> |  | **trafficManagerGeographicHierarchies** |  |
> | 작업 | Microsoft.Network/trafficManagerGeographicHierarchies/read | 지리적 트래픽 라우팅 메서드에서 사용할 수 있는 영역을 포함하는 Traffic Manager 지리적 계층 구조를 가져옵니다. |
> |  | **트래픽관리자프로필** |  |
> | 작업 | Microsoft.Network/trafficManagerProfiles/read | Traffic Manager 프로필 구성을 가져옵니다. 여기에는 DNS 설정, 트래픽 라우팅 설정, 엔드포인트 모니터링 설정 및 이 Traffic Manager 프로필이 라우팅하는 엔드포인트 목록이 포함됩니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager 프로필을 만들거나 기존 Traffic Manager 프로필의 구성을 수정합니다.<br>여기에는 프로필을 사용하거나 사용하지 않도록 설정, DNS 설정, 트래픽 라우팅 설정 또는 엔드포인트 모니터링 설정의 수정이 포함됩니다.<br>Traffic Manager 프로필이 라우팅하는 엔드포인트를 추가 및 제거하고, 사용하거나 사용하지 않도록 설정할 수 있습니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/delete | Traffic Manager 프로필을 삭제합니다. Traffic Manager 프로필과 연결된 모든 설정이 손실되고 프로필은 더 이상 트래픽을 라우팅하는 데 사용할 수 없습니다. |
> |  | **트래픽관리자프로필/azureEndpoints** |  |
> | 작업 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Azure 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 Azure 엔드포인트를 가져옵니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 기존 Traffic Manager 프로필에서 새 Azure 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 Azure 엔드포인트의 속성을 업데이트합니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 기존 Traffic Manager 프로필에서 Azure 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 Azure 엔드포인트로의 트래픽 라우팅을 중지합니다. |
> |  | **트래픽관리자프로필/외부엔드포인트** |  |
> | 작업 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 해당 외부 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 외부 엔드포인트를 가져옵니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 기존 Traffic Manager 프로필에서 새 외부 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 외부 엔드포인트의 속성을 업데이트합니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 기존 Traffic Manager 프로필에서 외부 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 외부 엔드포인트로의 트래픽 라우팅을 중지합니다. |
> |  | **트래픽관리자프로필/히트맵** |  |
> | 작업 | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 위치 및 소스 IP별 쿼리 수와 대기 시간 데이터를 포함하고 있는 특정 Traffic Manager의 Traffic Manager 열 지도를 가져옵니다. |
> |  | **트래픽관리자프로필/중첩엔드포인트** |  |
> | 작업 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 중첩 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 중첩 엔드포인트를 가져옵니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 기존 Traffic Manager 프로필에서 새 중첩 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 중첩 엔드포인트의 속성을 업데이트합니다. |
> | 작업 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 기존 Traffic Manager 프로필에서 중첩 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 중첩 엔드포인트로의 트래픽 라우팅을 중지합니다. |
> |  | **트래픽관리자사용자메트릭키** |  |
> | 작업 | Microsoft.Network/trafficManagerUserMetricsKeys/read | 실시간 사용자 메트릭 수집에 사용되는 구독 수준 키를 가져옵니다. |
> | 작업 | Microsoft.Network/trafficManagerUserMetricsKeys/write | 실시간 사용자 메트릭 수집에 사용할 새로운 구독 수준 키를 만듭니다. |
> | 작업 | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 실시간 사용자 메트릭 수집에 사용되는 구독 수준 키를 삭제합니다. |
> |  | **버추얼 허브** |  |
> | 작업 | Microsoft.Network/virtualHubs/delete | 가상 허브를 삭제합니다. |
> | 작업 | Microsoft.Network/virtualHubs/read | 가상 허브를 가져옵니다. |
> | 작업 | Microsoft.Network/virtualHubs/write | 가상 허브를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/가상 허브/효과적인 경로/작업 | 가상 허브에서 효과적인 경로 구성 |
> |  | **가상 허브/허브가상 네트워크 연결** |  |
> | 작업 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | HubVirtualNetworkConnection을 가져옵니다. |
> | 작업 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | HubVirtualNetworkConnection을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | HubVirtualNetworkConnection을 삭제합니다. |
> |  | **가상 허브/경로테이블** |  |
> | 작업 | 마이크로소프트.네트워크/가상 허브/경로 테이블/읽기 | 가상허브루트테이블V2 받기 |
> | 작업 | 마이크로소프트.네트워크/가상 허브/경로 테이블/쓰기 | 가상허브루트테이블V2 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.네트워크/가상 허브/경로 테이블/삭제 | 가상허브루트테이블V2 삭제 |
> |  | **virtualnetworkgateways** |  |
> | 작업 | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 지원되는 Vpn 디바이스를 나열합니다. |
> | 작업 | Microsoft.Network/virtualNetworkGateways/read | VirtualNetworkGateway를 가져옵니다. |
> | 작업 | Microsoft.Network/virtualNetworkGateways/write | VirtualNetworkGateway를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/virtualNetworkGateways/delete | virtualNetworkGateway를 삭제합니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | VirtualNetworkGateway용 VpnClient 패키지를 생성합니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VirtualNetworkGateway용 VpnProfile 패키지를 생성합니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | VirtualNetworkGateway에 대한 VPN 클라이언트 연결당 상태를 가져옵니다. |
> | 작업 | microsoft.network/가상 네트워크 게이트웨이/분리된 가상 네트워크 게이트웨이vpn연결/작업 | 가상 네트워크 게이트웨이 VPN 연결 끊기 |
> | 작업 | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 미리 생성된 vpn 클라이언트 프로필 패키지의 URL을 가져옵니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient Ipsec 매개 변수를 설정합니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient Ipsec 매개 변수를 가져옵니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient 공유 키를 다시 설정합니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/reset/action | virtualNetworkGateway를 다시 설정합니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | virtualNetworkGateway 보급된 경로를 가져옵니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | virtualNetworkGateway bgp 피어 상태를 가져옵니다. |
> | 작업 | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | virtualnetworkgateway 학습한 경로를 가져옵니다. |
> | 작업 | microsoft.network/가상 네트워크 게이트웨이/시작 패킷 캡처/작업 | 가상 네트워크 게이트웨이 패킷 캡처를 시작합니다. |
> | 작업 | microsoft.network/가상 네트워크 게이트웨이/중지 패킷 캡처/작업 | 가상 네트워크 게이트웨이 패킷 캡처를 중지합니다. |
> |  | **가상 네트워크 게이트웨이/연결** |  |
> | 작업 | microsoft.network/virtualnetworkgateways/connections/read | VirtualNetworkGatewayConnection을 가져옵니다. |
> |  | **가상 네트워크** |  |
> | 작업 | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/virtualNetworks/write | 가상 네트워크를 만들거나 기존 가상 네트워크를 업데이트합니다. |
> | 작업 | Microsoft.Network/virtualNetworks/delete | 가상 네트워크를 삭제합니다. |
> | 작업 | Microsoft.Network/virtualNetworks/peer/action | 가상 네트워크를 다른 가상 네트워크와 피어링합니다. |
> | 작업 | 마이크로소프트.네트워크/가상 네트워크/조인/작업 | 가상 네트워크에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/virtualNetworks/BastionHosts/action | Virtual Network의 요새 호스트 참조를 가져옵니다. |
> |  | **가상 네트워크/요새호스트** |  |
> | 작업 | 마이크로소프트.네트워크/가상 네트워크/요새호스트/기본/작업 | Virtual Network의 요새 호스트 참조를 가져옵니다. |
> |  | **가상 네트워크/체크IpAddress가용성** |  |
> | 작업 | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | IP 주소를 지정된 가상 네트워크에서 사용할 수 있는지 확인합니다. |
> |  | **가상 네트워크/서브넷** |  |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/write | 가상 네트워크 서브넷을 만들거나 기존 가상 네트워크 서브넷을 업데이트합니다. |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/delete | 가상 네트워크 서브넷을 삭제합니다. |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 필요한 네트워크 정책을 적용하여 서브넷을 준비합니다. |
> | 작업 | Microsoft.Network/가상 네트워크/서브넷/준비 취소네트워크 정책/작업 | 적용된 네트워크 정책을 제거하여 서브넷 준비 취소 |
> |  | **가상 네트워크/서브넷/컨텍스트서비스EndpointPolicy** |  |
> | 작업 | Microsoft.Network/가상 네트워크/서브넷/컨텍스트서비스엔드포인트정책/읽기 | 컨텍스트 서비스 엔드포인트 정책 가져옵니다. |
> | 작업 | Microsoft.Network/가상 네트워크/서브넷/컨텍스트서비스엔드포인트정책/쓰기 | 컨텍스트 서비스 끝점 정책을 만들거나 기존 컨텍스트 서비스 끝점 정책을 업데이트합니다. |
> | 작업 | Microsoft.Network/가상 네트워크/서브넷/컨텍스트서비스엔드포인트정책/삭제 | 컨텍스트 서비스 끝점 정책 삭제 |
> |  | **가상 네트워크/서브넷/가상 머신** |  |
> | 작업 | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 가상 네트워크 서브넷의 모든 가상 머신에 대한 참조를 가져옵니다. |
> |  | **가상 네트워크/사용** |  |
> | 작업 | Microsoft.Network/virtualNetworks/usages/read | 가상 네트워크의 각 서브넷에 대한 IP 사용량을 가져옵니다. |
> |  | **가상 네트워크/가상 머신** |  |
> | 작업 | Microsoft.Network/virtualNetworks/virtualMachines/read | 가상 네트워크의 모든 가상 머신에 대한 참조를 가져옵니다. |
> |  | **virtualNetworks/virtualNetworkPeerings** |  |
> | 작업 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 가상 네트워크 피어링 정의를 가져옵니다. |
> | 작업 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 가상 네트워크 피어링을 만들거나 기존 가상 네트워크 피어링을 업데이트합니다. |
> | 작업 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 가상 네트워크 피어링을 삭제합니다. |
> |  | **가상 네트워크 탭** |  |
> | 작업 | Microsoft.Network/virtualNetworkTaps/read | 가상 네트워크 탭을 가져옵니다. |
> | 작업 | Microsoft.Network/virtualNetworkTaps/join/action | 가상 네트워크 탭에 참여합니다. 경고할 수 없습니다. |
> | 작업 | Microsoft.Network/virtualNetworkTaps/delete | 가상 네트워크 탭을 삭제합니다. |
> | 작업 | Microsoft.Network/virtualNetworkTaps/write | 가상 네트워크 탭을 만들거나 업데이트합니다. |
> |  | **가상 라우터** |  |
> | 작업 | 마이크로소프트.네트워크/가상 라우터/읽기 | 가상 라우터를 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/가상 라우터/쓰기 | 가상 라우터를 만들거나 기존 가상 라우터를 업데이트합니다. |
> | 작업 | 마이크로소프트.네트워크/가상 라우터/삭제 | 가상 라우터 삭제 |
> | 작업 | 마이크로소프트.네트워크/가상 라우터/조인/작업 | 가상 라우터에 가입합니다. 경고할 수 없습니다. |
> |  | **가상 라우터/피어링** |  |
> | 작업 | 마이크로소프트.네트워크/가상 라우터/피어링/읽기 | 가상 라우터 피어링을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/가상 라우터/피어링/쓰기 | 가상 라우터 피어링을 만들거나 기존 가상 라우터 피어링을 업데이트합니다. |
> | 작업 | Microsoft.Network/가상 라우터/피어링/삭제 | 가상 라우터피어링 삭제 |
> |  | **버추얼 완** |  |
> | 작업 | Microsoft.Network/virtualWans/delete | 가상 Wan을 삭제합니다. |
> | 작업 | Microsoft.Network/virtualWans/read | 가상 Wan을 가져옵니다. |
> | 작업 | Microsoft.Network/virtualWans/write | 가상 Wan을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/virtualwans/vpnconfiguration/action | VPN 구성을 가져옵니다. |
> | 작업 | 마이크로소프트.네트워크/가상 wans/vpnServer구성/작업 | 버추얼완Vpn 서버 구성 |
> | 작업 | 마이크로소프트.네트워크/가상 wans/생성VpnProfile/작업 | 가상WanVpn서버 구성 VPN프로필 생성 |
> |  | **가상 Wans/p2sVpn서버 구성** |  |
> | 작업 | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 가상 Wan P2SVpnServerConfiguration을 가져옵니다. |
> | 작업 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 가장 Wan P2SVpnServerConfiguration을 만들거나 기존 가상 Wan P2SVpnServerConfiguration을 업데이트합니다. |
> | 작업 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 가상 Wan P2SVpnServerConfiguration을 삭제합니다. |
> |  | **버추얼 완 /지원보안 제공자** |  |
> | 작업 | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 지원되는 VirtualWan 보안 공급자를 가져옵니다. |
> |  | **버추얼 완/버추얼 허브** |  |
> | 작업 | Microsoft.Network/virtualWans/virtualHubs/read | 가상 WAN을 참조하는 모든 가상 허브를 가져옵니다. |
> |  | **가상Wans/vpnSites** |  |
> | 작업 | Microsoft.Network/virtualWans/vpnSites/read | 가상 WAN을 참조하는 모든 VPN 사이트를 가져옵니다. |
> |  | **vpn게이트웨이** |  |
> | 작업 | Microsoft.Network/vpnGateways/read | VpnGateway를 가져옵니다. |
> | 작업 | Microsoft.Network/vpnGateways/write | VpnGateway를 씁니다. |
> | 작업 | Microsoft.Network/vpnGateways/delete | VpnGateway를 삭제합니다. |
> | 작업 | microsoft.network/vpngateways/reset/action | VpnGateway를 다시 설정합니다. |
> | 작업 | microsoft.network/vpngateways/listvpnconnectionshealth/action | VpnGateway의 모든 연결 또는 하위 연결에 대한 연결 상태를 가져옵니다. |
> |  | **vpn게이트웨이/vpnConnections** |  |
> | 작업 | microsoft.network/vpnGateways/vpnConnections/read | VpnConnection을 가져옵니다. |
> | 작업 | microsoft.network/vpnGateways/vpnConnections/write | VpnConnection을 씁니다. |
> | 작업 | microsoft.network/vpnGateways/vpnConnections/delete | VpnConnection을 삭제합니다. |
> |  | **vpn게이트웨이/vpn연결/vpnLink연결** |  |
> | 작업 | 마이크로 소프트.네트워크 / vpn게이트웨이 / vpnConnections / vpnLink연결 / 읽기 | VPN 연결 받기 |
> |  | **vpnServer 구성** |  |
> | 작업 | 마이크로소프트.네트워크/vpnServer구성/읽기 | VPN서버 구성 받기 |
> | 작업 | 마이크로소프트.네트워크/vpnServer구성/쓰기 | VpnServer 구성 생성 또는 업데이트 |
> | 작업 | 마이크로소프트.네트워크/vpnServer구성/삭제 | VpnServer구성 삭제 |
> |  | **vpnsites** |  |
> | 작업 | Microsoft.Network/vpnsites/read | Vpn 사이트 리소스를 가져옵니다. |
> | 작업 | Microsoft.Network/vpnsites/write | Vpn 사이트 리소스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Network/vpnsites/delete | Vpn 사이트 리소스를 삭제합니다. |
> |  | **vpnSites/vpnSiteLinks** |  |
> | 작업 | 마이크로 소프트.네트워크 / vpnSites / vpnSiteLinks / 읽기 | VPN 사이트 링크 받기 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

Azure 서비스: [알림 허브](../notification-hubs/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.NotificationHubs/register/action | NotificationHubs 리소스 공급자에 대한 구독을 등록하고 네임스페이스와 NotificationHubs 만들기를 사용하도록 설정합니다. |
> | 작업 | Microsoft.NotificationHubs/unregister/action | NotificationHubs 리소스 공급자에 대한 구독을 등록 취소하고 네임스페이스와 NotificationHubs 만들기를 사용하도록 설정합니다. |
> | 작업 | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 주어진 네임스페이스 리소스 이름을 NotificationHub 서비스 내에서 사용할 수 있는지 여부를 확인합니다. |
> |  | **네임스페이스** |  |
> | 작업 | Microsoft.NotificationHubs/Namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 주어진 NotificationHub 이름을 네임스페이스 내에서 사용할 수 있는지 여부를 확인합니다. |
> |  | **네임스페이스/권한 부여규칙** |  |
> | 작업 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 작업 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 네임스페이스 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요. |
> |  | **네임스페이스/알림 허브** |  |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 알림 허브를 만들고 해당 속성을 업데이트합니다. 속성에는 주로 PNS 자격 증명, 권한 부여 규칙 및 TTL이 포함됩니다. 권한 부여 규칙 및 TTL |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 알림 허브 리소스 설명 목록을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 알림 허브 리소스를 삭제합니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 알림 허브 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 모든 알림 허브 PNS 자격 증명을 가져옵니다. 여기에는 WNS, MPNS, APNS, GCM 및 Baidu 자격 증명이 포함됩니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 테스트 푸시 알림을 보냅니다. |
> |  | **네임스페이스/알림 허브/권한 부여규칙** |  |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 알림 허브 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 알림 허브 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 알림 허브 권한 부여 규칙을 삭제합니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 알림 허브 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 알림 허브 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요. |
> |  | **네임스페이스/알림 허브/메트릭정의** |  |
> | 작업 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.NotificationHubs/operationResults/read | Notification Hubs 공급자에 대한 작업 결과를 반환합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.NotificationHubs/operations/read | Notification Hubs 공급자에 대해 지원되는 작업 목록을 반환합니다. |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

Azure 서비스: [Azure 마이그레이션](../migrate/migrate-services-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.OffAzure/register/action | Microsoft.OffAzure 리소스 공급자에 구독을 등록합니다. |
> | 작업 | Microsoft.OffAzure/register/action | Microsoft.OffAzure 리소스 공급자에 구독을 등록합니다. |
> | 작업 | Microsoft.OffAzure/register/action | Microsoft.OffAzure 리소스 공급자에 구독을 등록합니다. |
> | 작업 | Microsoft.OffAzure/register/action | Microsoft.OffAzure 리소스 공급자에 구독을 등록합니다. |
> |  | **하이퍼V사이트** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/read | Hyper-V 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/write | Hyper-V 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/delete | Hyper-V 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/read | Hyper-V 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/write | Hyper-V 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/delete | Hyper-V 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/read | Hyper-V 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/write | Hyper-V 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/delete | Hyper-V 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/read | Hyper-V 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/write | Hyper-V 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/delete | Hyper-V 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V 사이트 내의 개체를 새로 고칩니다. |
> |  | **하이퍼V사이트/클러스터** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V 클러스터의 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V 클러스터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V 클러스터의 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V 클러스터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V 클러스터의 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V 클러스터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V 클러스터의 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V 클러스터를 만들거나 업데이트합니다. |
> |  | **하이퍼V사이트/상태 요약** |  |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/상태 요약/읽기 | 하이퍼-V 리소스에 대한 상태 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/상태 요약/읽기 | 하이퍼-V 리소스에 대한 상태 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/상태 요약/읽기 | 하이퍼-V 리소스에 대한 상태 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/상태 요약/읽기 | 하이퍼-V 리소스에 대한 상태 요약을 가져옵니다. |
> |  | **하이퍼V사이트/호스트** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V 호스트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V 호스트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V 호스트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V 호스트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V 호스트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V 호스트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V 호스트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V 호스트를 만들거나 업데이트합니다. |
> |  | **하이퍼V사이트/채용 정보** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V 작업 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V 작업 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V 작업 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V 작업 속성을 가져옵니다. |
> |  | **하이퍼V사이트/컴퓨터** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V 머신 속성을 가져옵니다. |
> |  | **하이퍼V사이트/운영 상태** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 작업 상태 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 작업 상태 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 작업 상태 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 작업 상태 속성을 가져옵니다. |
> |  | **하이퍼V사이트/루나스계정** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 계정으로 실행된 Hyper-V 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 계정으로 실행된 Hyper-V 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 계정으로 실행된 Hyper-V 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 계정으로 실행된 Hyper-V 속성을 가져옵니다. |
> |  | **하이퍼사이트/요약** |  |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/요약/읽기 | 하이퍼-V 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/요약/읽기 | 하이퍼-V 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/요약/읽기 | 하이퍼-V 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/하이퍼V사이트/요약/읽기 | 하이퍼-V 사이트의 요약을 가져옵니다. |
> |  | **하이퍼V사이트/사용** |  |
> | 작업 | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V 사이트 사용량을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V 사이트 사용량을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V 사이트 사용량을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V 사이트 사용량을 가져옵니다. |
> |  | **가져오기사이트** |  |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/읽기 | 가져오기 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/쓰기 | 가져오기 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/삭제 | 가져오기 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/가져오기/작업 | 컴퓨터 CSV 파일을 가져오기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/내보내기/작업 | 컴퓨터 CSV 파일을 내보내기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/읽기 | 가져오기 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/쓰기 | 가져오기 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/삭제 | 가져오기 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/가져오기/작업 | 컴퓨터 CSV 파일을 가져오기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/내보내기/작업 | 컴퓨터 CSV 파일을 내보내기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/읽기 | 가져오기 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/쓰기 | 가져오기 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/삭제 | 가져오기 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/가져오기/작업 | 컴퓨터 CSV 파일을 가져오기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/내보내기/작업 | 컴퓨터 CSV 파일을 내보내기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/읽기 | 가져오기 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/쓰기 | 가져오기 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/삭제 | 가져오기 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/가져오기/작업 | 컴퓨터 CSV 파일을 가져오기 위한 SAS uri를 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/내보내기/작업 | 컴퓨터 CSV 파일을 내보내기 위한 SAS uri를 가져옵니다. |
> |  | **가져오기사이트/작업** |  |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/작업/읽기 | 가져오기 작업의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/작업/읽기 | 가져오기 작업의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/작업/읽기 | 가져오기 작업의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/작업/읽기 | 가져오기 작업의 속성을 가져옵니다. |
> |  | **가져오기사이트/컴퓨터** |  |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/읽기 | 가져오기 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/삭제 | 가져오기 컴퓨터 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/읽기 | 가져오기 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/삭제 | 가져오기 컴퓨터 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/읽기 | 가져오기 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/삭제 | 가져오기 컴퓨터 삭제 |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/읽기 | 가져오기 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/가져오기 사이트/컴퓨터/삭제 | 가져오기 컴퓨터 삭제 |
> |  | **작업** |  |
> | 작업 | Microsoft.OffAzure/Operations/read | 노출된 작업을 읽습니다. |
> |  | **서버 사이트** |  |
> | 작업 | 마이크로소프트.오프Azure/서버 사이트/읽기 | 서버 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/쓰기 | 서버 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/삭제 | 서버 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/새로 고침/작업 | 서버 사이트 내의 개체 새로 고침 |
> | 작업 | 마이크로소프트.오프Azure/서버 사이트/읽기 | 서버 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/쓰기 | 서버 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/삭제 | 서버 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/새로 고침/작업 | 서버 사이트 내의 개체 새로 고침 |
> | 작업 | 마이크로소프트.오프Azure/서버 사이트/읽기 | 서버 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/쓰기 | 서버 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/삭제 | 서버 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/새로 고침/작업 | 서버 사이트 내의 개체 새로 고침 |
> | 작업 | 마이크로소프트.오프Azure/서버 사이트/읽기 | 서버 사이트의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/쓰기 | 서버 사이트를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/삭제 | 서버 사이트 삭제 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/새로 고침/작업 | 서버 사이트 내의 개체 새로 고침 |
> |  | **서버 사이트/작업** |  |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/작업/읽기 | 서버 작업의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/작업/읽기 | 서버 작업의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/작업/읽기 | 서버 작업의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/작업/읽기 | 서버 작업의 속성을 가져옵니다. |
> |  | **서버사이트/컴퓨터** |  |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/읽기 | 서버 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/쓰기 | 서버 컴퓨터의 속성 작성 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/읽기 | 서버 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/쓰기 | 서버 컴퓨터의 속성 작성 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/읽기 | 서버 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/쓰기 | 서버 컴퓨터의 속성 작성 |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/읽기 | 서버 컴퓨터의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/컴퓨터/쓰기 | 서버 컴퓨터의 속성 작성 |
> |  | **서버사이트/운영 상태** |  |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/운영 상태/읽기 | 서버 작업 상태의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/운영 상태/읽기 | 서버 작업 상태의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/운영 상태/읽기 | 서버 작업 상태의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/운영 상태/읽기 | 서버 작업 상태의 속성을 가져옵니다. |
> |  | **서버사이트/루나스계정** |  |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/루나계정/읽기 | 서버의 속성을 계정으로 실행합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/루나계정/읽기 | 서버의 속성을 계정으로 실행합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/루나계정/읽기 | 서버의 속성을 계정으로 실행합니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/루나계정/읽기 | 서버의 속성을 계정으로 실행합니다. |
> |  | **서버사이트/요약** |  |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/요약/읽기 | 서버 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/요약/읽기 | 서버 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/요약/읽기 | 서버 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/서버사이트/요약/읽기 | 서버 사이트의 요약을 가져옵니다. |
> |  | **서버 사이트/사용** |  |
> | 작업 | 마이크로소프트.오프 Azure/서버사이트/사용/읽기 | 서버 사이트의 사용법 가져옵니다. |
> | 작업 | 마이크로소프트.오프 Azure/서버사이트/사용/읽기 | 서버 사이트의 사용법 가져옵니다. |
> | 작업 | 마이크로소프트.오프 Azure/서버사이트/사용/읽기 | 서버 사이트의 사용법 가져옵니다. |
> | 작업 | 마이크로소프트.오프 Azure/서버사이트/사용/읽기 | 서버 사이트의 사용법 가져옵니다. |
> |  | **VMware사이트** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/read | VMware 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/write | VMware 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/delete | VMware 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/refresh/action | VMware 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/내보내기 응용 프로그램/작업 | VMware 응용 프로그램 및 역할 데이터를 xls로 내보임 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/업데이트프로퍼시/액션 | 사이트의 컴퓨터에 대한 속성 업데이트 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성거친지도/액션 | 컴퓨터 목록에 대한 거친 맵 생성 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성 상세한 지도/작업 | 상세한 VMware 거친 지도를 생성합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/클라이언트그룹구성원/액션 | 선택한 클라이언트 그룹에 대한 클라이언트 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/서버그룹구성원/액션 | 선택한 서버 그룹에 대한 서버 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMwareSites/get응용 프로그램/작업 | 선택한 컴퓨터에 대한 응용 프로그램 정보 목록 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/read | VMware 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/write | VMware 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/delete | VMware 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/refresh/action | VMware 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/내보내기 응용 프로그램/작업 | VMware 응용 프로그램 및 역할 데이터를 xls로 내보임 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/업데이트프로퍼시/액션 | 사이트의 컴퓨터에 대한 속성 업데이트 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성거친지도/액션 | 컴퓨터 목록에 대한 거친 맵 생성 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성 상세한 지도/작업 | 상세한 VMware 거친 지도를 생성합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/클라이언트그룹구성원/액션 | 선택한 클라이언트 그룹에 대한 클라이언트 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/서버그룹구성원/액션 | 선택한 서버 그룹에 대한 서버 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMwareSites/get응용 프로그램/작업 | 선택한 컴퓨터에 대한 응용 프로그램 정보 목록 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/read | VMware 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/write | VMware 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/delete | VMware 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/refresh/action | VMware 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/내보내기 응용 프로그램/작업 | VMware 응용 프로그램 및 역할 데이터를 xls로 내보임 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/업데이트프로퍼시/액션 | 사이트의 컴퓨터에 대한 속성 업데이트 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성거친지도/액션 | 컴퓨터 목록에 대한 거친 맵 생성 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성 상세한 지도/작업 | 상세한 VMware 거친 지도를 생성합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/클라이언트그룹구성원/액션 | 선택한 클라이언트 그룹에 대한 클라이언트 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/서버그룹구성원/액션 | 선택한 서버 그룹에 대한 서버 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMwareSites/get응용 프로그램/작업 | 선택한 컴퓨터에 대한 응용 프로그램 정보 목록 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/read | VMware 사이트 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/write | VMware 사이트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/delete | VMware 사이트를 삭제합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/refresh/action | VMware 사이트 내의 개체를 새로 고칩니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/내보내기 응용 프로그램/작업 | VMware 응용 프로그램 및 역할 데이터를 xls로 내보임 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/업데이트프로퍼시/액션 | 사이트의 컴퓨터에 대한 속성 업데이트 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성거친지도/액션 | 컴퓨터 목록에 대한 거친 맵 생성 |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/생성 상세한 지도/작업 | 상세한 VMware 거친 지도를 생성합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/클라이언트그룹구성원/액션 | 선택한 클라이언트 그룹에 대한 클라이언트 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프Azure/VMwareSites/서버그룹구성원/액션 | 선택한 서버 그룹에 대한 서버 그룹 구성원을 나열합니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMwareSites/get응용 프로그램/작업 | 선택한 컴퓨터에 대한 응용 프로그램 정보 목록 가져옵니다. |
> |  | **VMware사이트/상태 요약** |  |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/상태 요약/읽기 | VMware 리소스에 대한 상태 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/상태 요약/읽기 | VMware 리소스에 대한 상태 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/상태 요약/읽기 | VMware 리소스에 대한 상태 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/상태 요약/읽기 | VMware 리소스에 대한 상태 요약을 가져옵니다. |
> |  | **VMware사이트/채용 정보** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/jobs/read | VMware 작업 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/jobs/read | VMware 작업 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/jobs/read | VMware 작업 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/jobs/read | VMware 작업 속성을 가져옵니다. |
> |  | **VMware사이트/기계** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/read | VMware 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware 머신을 중지합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware 머신을 시작합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/read | VMware 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware 머신을 중지합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware 머신을 시작합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/read | VMware 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware 머신을 중지합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware 머신을 시작합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/read | VMware 머신 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware 머신을 중지합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware 머신을 시작합니다. |
> |  | **VMware사이트/기계/응용 프로그램** |  |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/컴퓨터/응용 프로그램/읽기 | VMware 컴퓨터 응용 프로그램의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/컴퓨터/응용 프로그램/읽기 | VMware 컴퓨터 응용 프로그램의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/컴퓨터/응용 프로그램/읽기 | VMware 컴퓨터 응용 프로그램의 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.오프Azure/VMware사이트/컴퓨터/응용 프로그램/읽기 | VMware 컴퓨터 응용 프로그램의 속성을 가져옵니다. |
> |  | **VMware사이트/운영 상태** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 작업 상태 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 작업 상태 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 작업 상태 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 작업 상태 속성을 가져옵니다. |
> |  | **VMwareSites/runasaccounts** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 계정으로 실행된 VMware 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 계정으로 실행된 VMware 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 계정으로 실행된 VMware 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 계정으로 실행된 VMware 속성을 가져옵니다. |
> |  | **VMware사이트/요약** |  |
> | 작업 | 마이크로소프트.오프 Azure/VMware사이트/요약/읽기 | VMware 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMware사이트/요약/읽기 | VMware 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMware사이트/요약/읽기 | VMware 사이트의 요약을 가져옵니다. |
> | 작업 | 마이크로소프트.오프 Azure/VMware사이트/요약/읽기 | VMware 사이트의 요약을 가져옵니다. |
> |  | **VMware사이트/사용** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/usage/read | VMware 사이트 사용량을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/usage/read | VMware 사이트 사용량을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/usage/read | VMware 사이트 사용량을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/usage/read | VMware 사이트 사용량을 가져옵니다. |
> |  | **VMware사이트/vcenters** |  |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter 속성을 가져옵니다. |
> | 작업 | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter를 만들거나 업데이트합니다. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

Azure 서비스: [Azure 모니터](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.OperationalInsights/register/action | 리소스 공급자에 구독을 등록합니다. |
> | 작업 | 마이크로소프트.오퍼리인사이트/레지스터/액션 | 구독을 리기스터합니다. |
> | 작업 | 마이크로 소프트.운영 통찰력 / 등록취소 / 작업 | 구독을 등록 취소합니다. |
> |  | **사용 가능한 서비스 티어** |  |
> | 작업 | 마이크로소프트.오퍼런스/사용 가능한 서비스 티어/읽기 | 사용 가능한 서비스 계층을 가져옵니다. |
> |  | **클러스터** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/클러스터/읽기 | 클러스터 받기 |
> | 작업 | 마이크로소프트.오퍼리인사이트/클러스터/쓰기 | 클러스터 만들기 또는 업데이트 |
> | 작업 | 마이크로소프트.오퍼리인사이트/클러스터/삭제 | 클러스터 삭제 |
> |  | **linkTargets** |  |
> | 작업 | Microsoft.OperationalInsights/linkTargets/read | Azure 구독과 연결되지 않은 기존 계정 목록을 표시합니다. Azure 구독을 작업 영역에 연결하려면 이 작업에서 반환된 고객 ID를 작업 영역 만들기 작업의 고객 ID 속성에 사용하세요. |
> |  | **위치/작업상태** |  |
> | 작업 | 마이크로소프트.운영 인사이트/위치/운영상태/읽기 | 로그 분석 Azure 비동기 작업 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | microsoft.operationalinsights/operations/read | 사용 가능한 모든 OperationalInsights Rest API 작업을 나열합니다. |
> |  | **workspaces** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/write | 새 작업 영역을 만들거나 기존 작업 영역의 고객 ID를 제공하여 기존 작업 영역에 연결합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/read | 기존 작업 영역을 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/delete | 작업 영역을 삭제합니다. 생성 시 작업 영역이 기존 작업 영역에 연결된 경우 연결된 작업 영역은 삭제되지 않습니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 작업 영역에 대한 등록 인증서를 생성합니다. 이 인증서는 Microsoft System Center Operation Manager를 작업 영역에 연결하는 데 사용됩니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/listKeys/action | 작업 영역에 대한 목록 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/purge/action | 작업 영역에서 지정된 데이터를 삭제합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 지정된 작업 영역 공유 키를 다시 생성합니다. |
> |  | **작업 영역/분석** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> |  | **작업 영역/분석/쿼리/스키마** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 검색 스키마 V2를 가져옵니다. |
> |  | **작업 영역/API** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/api/query/action | 새 엔진을 사용하여 검색합니다. |
> |  | **작업 영역/API/쿼리/스키마** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 검색 스키마 V2를 가져옵니다. |
> |  | **작업 영역/구성범위** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 구성 범위를 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 구성 범위를 설정합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 구성 범위를 삭제합니다. |
> |  | **작업 영역/데이터 내보내기** |  |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 데이터 내보내기 / 읽기 | 특정 데이터 내보내기를 가져옵니다. |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 데이터 내보내기 / 쓰기 | 데이터 내보내기를 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 데이터 내보내기 / 삭제 | 특정 데이터 내보내기를 삭제합니다. |
> |  | **작업 영역/데이터 원본** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/datasources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/datasources/write | 작업 영역의 데이터 원본을 생성/업데이트합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/datasources/delete | 작업 영역의 데이터 원본을 삭제합니다. |
> |  | **작업 영역/게이트웨이** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/gateways/delete | 작업 영역에 대해 구성된 게이트웨이를 제거합니다. |
> |  | **작업 영역/인텔리전스 팩** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 지정된 작업 영역에 대해 표시되는 모든 인텔리전스 팩 목록과 해당 작업 영역에 팩을 사용할 수 있는지 여부를 표시합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 지정된 작업 영역에 인텔리전스 팩을 사용하도록 설정합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 지정된 작업 영역에 인텔리전스 팩을 사용하지 않도록 설정합니다. |
> |  | **workspaces/linkedServices** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/linkedServices/read | 지정된 작업 영역에서 연결된 서비스를 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/linkedServices/write | 지정된 작업 영역에서 연결된 서비스를 만듭니다/업데이트합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 지정된 작업 영역에서 연결된 서비스를 삭제합니다. |
> |  | **작업 영역/목록 키** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/listKeys/read | 작업 영역에 대한 목록 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> |  | **작업 영역/관리그룹** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/managementGroups/read | 이 작업 영역에 연결된 System Center Operations Manager 관리 그룹의 이름 및 메타데이터를 가져옵니다. |
> |  | **작업 영역/메트릭정의** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 작업 영역의 메트릭 정의를 가져옵니다. |
> |  | **작업 영역/알림설정** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 작업 영역에 대한 사용자 알림 설정을 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 작업 영역에 대한 사용자 알림 설정을 지정합니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 작업 영역에 대한 사용자 알림 설정을 삭제합니다. |
> |  | **작업 영역/작업** |  |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 작업 / 읽기 | OperationInsights 작업 영역 작업의 상태를 가져옵니다. |
> |  | **workspaces/query** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> |  | **작업 영역/쿼리/AAD도메인 서비스 계정 로그온** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AADDomainServices계정 조정/읽기 | AAD도메인서비스계정로그온 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AAD도메인 서비스 계정 관리** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AADDomainServices계정 관리/읽기 | AAD도메인서비스계정관리표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AAD도메인 서비스 디렉터리서비스액세스** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AADDomainServicesDirectoryService액세스/읽기 | AADDomainServicesDirectoryServiceAccess 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AADDomainServicesLogonLogoff** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AADDomainServicesLogonoff/읽기 | AADDomainServicesLogonLogoff 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AAD도메인 서비스정책변경** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AADDomainServicesPolicy변경/읽기 | AAD도메인 서비스정책 변경 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AAD도메인 서비스프리특전 사용** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AADDomainServices권한 사용/읽기 | AADDomainServices권한 사용 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AAD도메인서비스시스템보안** |  |
> | 작업 | 마이크로소프트.오퍼리티인사이트/작업 영역/쿼리/AADDomainServicesSystem보안/읽기 | AAD도메인서비스시스템보안표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AD평가 권장 사항** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | ADAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/AddonAzure백업경고** |  |
> | 작업 | 마이크로소프트.오퍼랙트인사이트/작업 영역/쿼리/애드온Azure백업경고/읽기 | AddonAzureBackupAlerts 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/애드온Azure백업작업** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/애드온Azure백업작업/읽기 | AddonAzureBackupJobs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AddonAzure 백업 정책** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/애드온Azure백업정책/읽기 | AddonAzure백업정책 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/애드온Azure백업보호인스턴스** |  |
> | 작업 | 마이크로소프트.오퍼랙시크/작업 영역/쿼리/애드온Azure백업보호인스턴스/읽기 | AddonAzure백업보호인스턴스 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/애드온Azure백업스토리지** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/애드온Azure백업스토리지/읽기 | AddonAzureBackupStorage 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFActivityRun** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFActivityRun/읽기 | ADFActivityRun 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADF파이프라인 실행** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADF파이프라인실행/읽기 | ADF파이프라인Run 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFSSIS통합실행 시간 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFSSIS통합실행 시간 로그/읽기 | ADFSSIS통합실행시간 로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFSIs패키지이벤트컨텍스트** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/ADFSSIS패키지이벤트컨텍스트/읽기 | ADFSSIS패키지이벤트상황표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFSSIS패키지이벤트메시지** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFSSIS패키지메시지/읽기 | ADFSSIS패키지메시지 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFSSIS패키지 실행 통계** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFSSIS패키지 실행 가능 통계/읽기 | ADFSSIS패키지실행 가능 통계표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFSSIS패키지실행구성 요소 단계** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFSSIS패키지실행구성 요소 단계/읽기 | ADFSSIS패키지실행구성요소 테이블의 데이터 읽기 |
> |  | **작업 영역/쿼리/ADFSIs패키지실행데이터통계** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFSSIS패키지실행데이터통계/읽기 | ADFSSIS패키지실행데이터통계표 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ADF트리거런** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/ADFTriggerRun/읽기 | ADFTriggerRun 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AD복제결과** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | ADReplicationResult 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/AD보안평가추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | ADSecurityAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/AegDelivery실패로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AegDelivery실패로그/읽기 | AegDeliveryFailureLogs 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/Aeg게시실패로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/Aeg게시실패로그/읽기 | AegPublishFailureLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/경고** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Alert/read | Alert 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/경고기록** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | AlertHistory 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/AmlCompute클러스터 이벤트** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AmlComputeCluster이벤트/읽기 | AmlComputeClusterEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AmlCompute 클러스터노드이벤트** |  |
> | 작업 | 마이크로소프트.오퍼레이트인사이트/작업 영역/쿼리/AmlComputeClusterNodeEvent/읽기 | AmlComputeClusterNodeEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AmlComputeJobEvent** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/AmlComputeJobEvent/읽기 | AmlComputeJobEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/ApiManagement게이트웨이 로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/ApiManagement게이트웨이로그/읽기 | ApiManagement게이트웨이로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱가용성결과** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 가용성결과/읽기 | 앱가용성결과 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱브라우저타이밍** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱브라우저타이밍/읽기 | AppBrowserTimings 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱센터오류** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | AppCenterError 테이블의 데이터 읽기 |
> |  | **작업 영역/쿼리/앱 종속성** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 종속성/읽기 | 앱리펜던스 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱 이벤트** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 이벤트/읽기 | AppEvents 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/애플리케이션 인사이트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | ApplicationInsights 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/앱메트릭** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱메트릭/읽기 | AppMetrics 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱페이지뷰** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/앱페이지뷰/읽기 | AppPageView 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱 성능 카운터** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 성능 카운터/읽기 | AppPerformanceCounter 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱플랫폼로그스프링** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 공간/쿼리/앱플랫폼로그스프링/읽기 | 앱플랫폼로그포스프링 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱플랫폼시스템 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱플랫폼시스템 로그/읽기 | 앱플랫폼시스템로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱 요청** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 요청/읽기 | AppRequests 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱서비스 앱로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 서비스 앱로그/읽기 | AppServiceAppLogs 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱서비스 감사 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 서비스 감사 로그/읽기 | AppServiceAuditLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱서비스콘솔로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/앱서비스콘솔로그/읽기 | AppServiceConsoleLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱 서비스환경플랫폼 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱 서비스환경플랫폼로그/읽기 | 앱 서비스환경플랫폼로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱서비스파일 감사 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱서비스파일 감사 로그/읽기 | AppServiceFileAuditLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱서비스HTTP로그** |  |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 쿼리 / 앱 서비스HTTP로그 / 읽기 | AppServiceHTTPLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱시스템이벤트** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱시스템이벤트/읽기 | 앱시스템이벤트 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/앱트레이스** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/앱트레이스/읽기 | AppTraces 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/감사 로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | AuditLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/자동 크기 조정 평가로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/자동 크기 조정 평가로그/읽기 | 자동 크기 조정평가로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/자동 크기 조정 작업 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/자동 크기 조정 작업 로그/읽기 | 자동 크기 조정 작업로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AWS클라우드트레일** |  |
> | 작업 | 마이크로소프트.오퍼랙티안/워크스페이스/쿼리/AWSCloudTrail/읽기 | AWSCloudTrail 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AzureActivity** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | AzureActivity 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/Azure평가 권장 사항** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/Azure평가추천/읽기 | Azure평가추천테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AzureDevOps감사** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/AzureDevOps감사/읽기 | AzureDevOps감사 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/Azure진단** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/Azure진단/읽기 | Azure진단 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/AzureMetrics** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | AzureMetrics 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/바이클러스터이벤트** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/바이클러스터이벤트/읽기 | BaiClusterEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/바이클러스터노드이벤트** |  |
> | 작업 | 마이크로소프트.오퍼레이션인사이트/작업 영역/쿼리/바이클러스터노드이벤트/읽기 | 바이클러스터노드이벤트 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/바이잡이벤트** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/바이잡이벤트/읽기 | BaiJobEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/블록체인 응용 프로그램 로그** |  |
> | 작업 | Microsoft.오퍼리얼인사이트/작업 영역/쿼리/블록체인응용프로그램 로그/읽기 | 블록체인응용프로그램로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/블록체인프록시로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/블록체인프록시로그/읽기 | 블록체인프록시로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/경계 포트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | BoundPort 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/공통 보안 로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | CommonSecurityLog 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/컴퓨터 그룹** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | ComputerGroup 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/구성 변경** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | ConfigurationChange 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/구성데이터** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | ConfigurationData 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/컨테이너이미지 인벤토리** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | ContainerImageInventory 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/컨테이너 인벤토리** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | ContainerInventory 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/컨테이너 로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | ContainerLog 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/컨테이너노드 인벤토리** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/컨테이너노드인벤토리/읽기 | 컨테이너노드인벤토리 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/컨테이너레지스트리로그인이벤트** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/컨테이너레지스트리로그인이벤트/읽기 | 컨테이너 레지스트리로그인이벤트 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/컨테이너레지스트리리리포지토리이벤트** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/컨테이너레지스트리리포지토리이벤트/읽기 | 컨테이너 레지스트리리포지토리이벤트 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/컨테이너 서비스 로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | ContainerServiceLog 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/코어Azure 백업** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/코어Azure백업/읽기 | 코어AzureBackup 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스계정** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/데이터브릭스계정/읽기 | 데이터 브릭스계정 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스클러스터** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/데이터브릭스클러스터/읽기 | 데이터브릭스클러스터 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터브릭스DBFS** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/데이터브릭스DBFS/읽기 | 데이터브릭스DBFS 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스인스턴스풀** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/데이터브릭인스턴스풀/읽기 | 데이터 브릭스인스턴스풀 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터브릭스작업** |  |
> | 작업 | Microsoft.오퍼리인사이트/작업 영역/쿼리/데이터브릭스작업/읽기 | 데이터브릭스잡스 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터브릭스노트북** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/데이터브릭스노트북/읽기 | 데이터브릭스노트북 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스비밀** |  |
> | 작업 | Microsoft.오퍼리인사이트/작업 영역/쿼리/데이터브릭스비밀/읽기 | 데이터브릭스시크릿 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스SQL권한** |  |
> | 작업 | 마이크로 소프트.오퍼리 인사이트 / 작업 영역 / 쿼리 / 데이터 브릭스SQL권한 / 읽기 | 데이터 브릭스SQL권한 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스SSH** |  |
> | 작업 | Microsoft.오퍼리인사이트/작업 영역/쿼리/데이터브릭스SSH/읽기 | 데이터 브릭스SSH 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터 브릭스테이블** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/데이터브릭스테이블/읽기 | 데이터 브릭스테이블 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/데이터브릭스워크스페이스** |  |
> | 작업 | Microsoft.오퍼랙시브인사이트/작업 영역/쿼리/데이터브릭스워크스페이스/읽기 | 데이터브릭스워크스페이스 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/종속성** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/종속성/읽기 | 종속성 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/장치 앱 충돌** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | DeviceAppCrash 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/디바이스 앱 시작** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | DeviceAppLaunch 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치캘린더** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | DeviceCalendar 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치 정리** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | DeviceCleanup 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치 연결 세션** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | DeviceConnectSession 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치 Etw** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | DeviceEtw 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치하드웨어 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | DeviceHardwareHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | DeviceHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치 하트 비트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | DeviceHeartbeat 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치스카이프하트비트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | DeviceSkypeHeartbeat 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치Skype사인** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | DeviceSkypeSignIn 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/장치슬립 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | DeviceSleepState 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHApp실패** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | DHAppFailure 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHApp 안정성** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | DHAppReliability 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHCPActivity** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/DHCP활동/읽기 | DHCPActivity 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/DH드라이버 안정성** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | DHDriverReliability 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHLogon실패** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | DHLogonFailures 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHLogonMetrics** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | DHLogonMetrics 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHOSCrashData** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | DHOSCrashData 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHOS신뢰성** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | DHOSReliability 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/DHWipApp학습** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | DHWipAppLearning 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/Dns이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | DnsEvents 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/Dns 인벤토리** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | DnsInventory 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/ETWEvent** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | ETWEvent 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Event/read | Event 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/교환평가 추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | ExchangeAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/교환온라인평가추천서** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | ExchangeOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/실패** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/실패/읽기 | 실패 한 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/함수 앱 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/함수 앱 로그/읽기 | 함수앱로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/하트비트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Heartbeat 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/헌팅북마크** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/헌팅북마크/읽기 | 헌팅북마크 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/IIS평가 추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | IISAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/인바운드 연결** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | InboundConnection 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/인사이트메트릭** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/인사이트메트릭/읽기 | 인사이트메트릭스 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/인튠감사 로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | IntuneAuditLogs 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/IntuneDevice 컴플라이언스 조직** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/IntuneDeviceComplianceOrg/읽기 | IntuneDevice컴플라이언스 조직 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/인튠오퍼로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | IntuneOperationalLogs 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/IoTHub분산Tracing** |  |
> | 작업 | Microsoft.오퍼리인사이트/작업 영역/쿼리/IoTHub분산Tracing/읽기 | IoTHub분산Tracing 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/KubeEvents** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | KubeEvents 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/KubeHealth** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/KubeHealth/읽기 | KubeHealth 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/쿠베몬에이전트이벤트** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/쿠베몬에이전트이벤트/읽기 | KubeMonAgent이벤트 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/KubeNode인벤토리** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | KubeNodeInventory 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/KubePod인벤토리** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | KubePodInventory 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/KubeServices** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | KubeServices 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/LinuxAuditLog** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | LinuxAuditLog 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MA응용 프로그램** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | MAApplication 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MA응용 프로그램 Health** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | MAApplicationHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MA응용 프로그램건강대체버전** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | MAApplicationHealthAlternativeVersions 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MA응용 프로그램건강 문제** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | MAApplicationHealthIssues 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAApplication인스턴스** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | MAApplicationInstance 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAApplication인스턴스 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | MAApplicationInstanceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAApplication 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | MAApplicationReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MA 배포 계획** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | MADeploymentPlan 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MADevice** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADevice/read | MADevice 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MADeviceNot등록됨** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | MADeviceNotEnrolled 테이블의 데이터 읽기 |
> |  | **작업 영역/쿼리/MADeviceNRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MADeviceNRT/읽기 | MADeviceNRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MADevicePnPHealth** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | MADevicePnPHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MADevicePnPHealth대안버전** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | MADevicePnPHealthAlternativeVersions 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MADevicePnPHealth문제** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | MADevicePnPHealthIssues 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MADevice 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | MADeviceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MADriver인스턴스 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | MADriverInstanceReadiness 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MADriver 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | MADriverReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeAddin** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | MAOfficeAddin 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeAddin엔터티Health** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAOfficeAddinEntityHealth/읽기 | MAOfficeAddinEntityHealth 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeAddinHealth** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | MAOfficeAddinHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeAddinHealthEventNRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 공간/쿼리/MAOfficeAddinHealthEventNRT/읽기 | MAOfficeAddinHealthEventNRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeAddinHealth 문제** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | MAOfficeAddinHealthIssues 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeAddin인스턴스** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | MAOfficeAddinInstance 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeAddin인스턴스 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | MAOfficeAddinInstanceReadiness 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeAddin준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | MAOfficeAddinReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeApp** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | MAOfficeApp 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeApp충돌NRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAOfficeApp충돌NRT/읽기 | MAOfficeApp충돌NRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeAppHealth** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | MAOfficeAppHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeApp인스턴스** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | MAOfficeAppInstance 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeApp인스턴스건강** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAOfficeApp인스턴스건강/읽기 | MAOfficeApp인스턴스건강 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeApp 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | MAOfficeAppReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeAppSessionsNRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAOfficeAppSessionsNRT/읽기 | MAOfficeAppSessionsNRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOfficeBuildInfo** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | MAOfficeBuildInfo 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeCurrency평가** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | MAOfficeCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeCurrency평가일일 카운트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | MAOfficeCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOffice 배포 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | MAOfficeDeploymentStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOffice 배포상태NRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAOffice배포NRT/읽기 | MAOffice배포상태NRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOffice매크로오류NRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAOfficeMacroErrorNRT/읽기 | MAOfficeMacroErrorNRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOffice매크로글로벌헬스** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 공간/쿼리/MAOfficeMacroGlobalHealth/읽기 | MAOffice매크로글로벌헬스 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAOffice 매크로Health** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | MAOfficeMacroHealth 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOffice 매크로 헬스 문제** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | MAOfficeMacroHealthIssues 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOffice 매크로 문제 인스턴스 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | MAOfficeMacroIssueInstanceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOffice 매크로 문제 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | MAOfficeMacroIssueReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOffice 매크로 요약** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | MAOfficeMacroSummary 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeSuite** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | MAOfficeSuite 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAOfficeSuite인스턴스** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | MAOfficeSuiteInstance 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MA제안 파일럿장치** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | MAProposedPilotDevices 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAWindows빌드정보** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | MAWindowsBuildInfo 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAWindows통화평가** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | MAWindowsCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAWindows통화평가일일 카운트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | MAWindowsCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAWindows 배포 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | MAWindowsDeploymentStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/MAWindows배포상태NRT** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/MAWindows배포상태NRT/읽기 | MAWindows배포상태NRT 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/MAWindowsSysReq인스턴스 준비** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | MAWindowsSysReqInstanceReadiness 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/McasShadowIt보고** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/McasShadowIt보고/읽기 | McasShadowIt보고 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트AzureBastion감사로그** |  |
> | 작업 | 마이크로소프트.오퍼레이션인사이트/작업 영역/쿼리/마이크로소프트Azure바스티온감사글로그/읽기 | 마이크로소프트AzureBastionAuditLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트데이터쉐어수신스냅로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/마이크로소프트데이터쉐어수신스냅샷로그/읽기 | 마이크로소프트데이터쉐어수신스냅로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트데이터쉐어센트스냅로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/마이크로소프트데이터쉐어센트스냅샷로그/읽기 | 마이크로소프트데이터쉐어센트스냅로그 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트데이터쉐어로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/마이크로소프트데이터쉐어로그/읽기 | 마이크로소프트데이터쉐어로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트역학텔레메트리성능로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/마이크로소프트다이내믹텔레메트리성능로그/읽기 | 마이크로소프트역학텔레메트리성능로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트역학텔레메트릭시스템로그** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/마이크로소프트다이나믹텔레메트릭시스템메트릭스로그/읽기 | 마이크로소프트역학텔레메트릭스로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/마이크로소프트 헬스케어ApisAuditlogs** |  |
> | 작업 | 마이크로소프트.오퍼리인사이트/작업 영역/쿼리/마이크로소프트헬스케어ApisAuditlogs/읽기 | 마이크로소프트헬스케어아피스감사그표의 데이터 읽기 |
> |  | **작업 영역/쿼리/네트워크 모니터링** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | NetworkMonitoring 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/Office 활동** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | OfficeActivity 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/작업** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Operation/read | Operation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/아웃바운드 연결** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | OutboundConnection 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/퍼프** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Perf/read | Perf 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보호 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | ProtectionStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/요청** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/요청/읽기 | 요청 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/예약된 공통 필드** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | ReservedCommonFields 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SCCM평가 추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | SCCMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SCOM평가 추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | SCOMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보안 경고** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | SecurityAlert 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보안 기준선** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | SecurityBaseline 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보안베이스라인요약** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | SecurityBaselineSummary 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보안 검색** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | SecurityDetection 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보안 이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | SecurityEvent 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/보안IoTRawEvent** |  |
> | 작업 | 마이크로소프트.오퍼리티인사이트/작업 영역/쿼리/보안IoTRawEvent/읽기 | 보안IoTRawEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/보안 추천** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/보안 추천/읽기 | 보안 추천 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/서비스 패브릭운영 이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | ServiceFabricOperationalEvent 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/서비스패브릭신뢰할 수 있는 행위자이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | ServiceFabricReliableActorEvent 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/서비스패브릭신뢰할 서비스이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | ServiceFabricReliableServiceEvent 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SfB평가추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | SfBAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SfB온라인평가추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | SfBOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SharePoint온라인평가추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | SharePointOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SignalRService진단 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/SignalRService진단 진단 로그/읽기 | SignalRService진단로그 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/로그인 로그** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | SigninLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/SP평가 추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | SPAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SQL평가 권장 사항** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | SQLAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SqlData분류** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/SqlData분류/읽기 | SqlData분류 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/SQL쿼리 성능** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | SQLQueryPerformance 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/SqlThreatProtectionLogin감사** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/SqlThreatProtectionLogin감사/읽기 | SqlThreatProtectionLogin감사 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/Sql취약성평가결과** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/Sql취약성평가결과/읽기 | Sql취약성평가결과표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/저장소BlobLogs** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/저장소BlobLogs/읽기 | StorageBlobLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/저장소 파일로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/저장소 파일로그/읽기 | StorageFileLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/저장소큐로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/저장소큐로그/읽기 | StorageQueueLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/저장소 테이블 로그** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/저장소 테이블로그/읽기 | StorageTableLogs 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/성공** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/성공/읽기 | 성공 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/Syslog** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Syslog 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/Sysmon이벤트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | SysmonEvent 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/테이블.사용자 지정** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 사용자 지정 로그에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/위협 인텔리전스 표시기** |  |
> | 작업 | Microsoft.운영 인사이트/작업 영역/쿼리/위협 인텔리전스 표시기/읽기 | 위협 인텔리전스표시기 표에서 데이터 읽기 |
> |  | **작업 영역/쿼리/UAApp** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAApp/read | UAApp 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UAComputer** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | UAComputer 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UAComputerRank** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | UAComputerRank 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UADriver** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UADriver/read | UADriver 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UADriver문제 코드** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | UADriverProblemCodes 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UA피드백** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | UAFeedback 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UA하드웨어 보안** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | UAHardwareSecurity 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UAIESiteDiscovery** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | UAIESiteDiscovery 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UAOfficeAddIn** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | UAOfficeAddIn 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UA제안 된 작업 계획** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | UAProposedActionPlan 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UASysReq문제** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | UASysReqIssue 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/UA업그레이드컴퓨터** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | UAUpgradedComputer 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/업데이트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Update/read | Update 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/업데이트실행 진행률** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | UpdateRunProgress 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/업데이트 요약** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Update/read | UpdateSummary 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/사용** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/Usage/read | Usage 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/VM바운드 포트** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | VMBoundPort 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/VMComputer** |  |
> | 작업 | Microsoft.오퍼리인사이트/작업 영역/쿼리/VMComputer/읽기 | VMComputer 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/VM연결** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | VMConnection 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/VMProcess** |  |
> | 작업 | Microsoft.오퍼리인사이트/작업 영역/쿼리/VMProcess/읽기 | VMProcess 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/W3CIISLog** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | W3CIISLog 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WaaS배포 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | WaaSDeploymentStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WaaSInsiderStatus** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | WaaSInsiderStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WaaSUpdateStatus** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | WaaSUpdateStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WDAV상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | WDAVStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WDAVThreat** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | WDAVThreat 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/Windows클라이언트평가추천** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | WindowsClientAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WindowsEvent** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | WindowsEvent 테이블에서 데이터 읽기 |
> |  | **작업 영역/쿼리/Windows방화벽** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | WindowsFirewall 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WindowsServer평가권장 사항** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | WindowsServerAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/와이어데이터** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WireData/read | WireData 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/워크로드 모니터링퍼프** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | WorkloadMonitoringPerf 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WUDO집계 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | WUDOAggregatedStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/쿼리/WUDO 상태** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | WUDOStatus 테이블에서 데이터를 읽습니다. |
> |  | **작업 영역/규칙** |  |
> | 작업 | microsoft.operationalinsights/workspaces/rules/read | 모든 경고 규칙을 가져옵니다. |
> |  | **workspaces/savedSearches** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/savedSearches/read | 저장된 검색 쿼리를 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/savedSearches/write | 저장된 검색 쿼리를 만듭니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 저장된 검색 쿼리를 삭제합니다. |
> |  | **작업 영역/저장된 검색/결과** |  |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/results/read | 저장된 검색 결과를 가져옵니다. 사용되지 않음 |
> |  | **작업 영역/저장된 검색/일정** |  |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 예약된 검색을 가져옵니다. |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 예약된 검색을 삭제합니다. |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 예약된 검색을 만들거나 업데이트합니다. |
> |  | **작업 영역/저장된 검색/일정/작업** |  |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 예약된 검색 작업을 가져옵니다. |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 예약된 검색 작업을 삭제합니다. |
> | 작업 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 예약된 검색 작업을 만들거나 업데이트합니다. |
> |  | **작업 영역/스키마** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/schema/read | 작업 영역에 대한 검색 스키마를 가져옵니다.  검색 스키마에는 노출된 필드와 필드 형식이 포함되어 있습니다. |
> |  | **작업 영역/범위개인링크프록시** |  |
> | 작업 | 마이크로소프트.운영 인사이트/작업 영역/범위개인링크프로시스/읽기 | 범위 별 개인 링크 프록시를 가져옵니다. |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 범위개인 링크 Proxies / 쓰기 | 범위 별 개인 링크 프록시를 배치합니다. |
> | 작업 | 마이크로 소프트.운영 통찰력 / 작업 영역 / 범위개인 링크 Proxies / 삭제 | 범위 별 개인 링크 프록시 삭제. |
> |  | **작업 영역/검색** |  |
> | 작업 | microsoft.operationalinsights/workspaces/search/read | 검색 결과를 가져옵니다. 사용되지 않습니다. |
> |  | **작업 영역/공유 키** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> |  | **작업 영역/스토리지인사이트구성** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 새 스토리지 구성을 만듭니다. 이러한 구성은 기존 스토리지 계정의 위치에서 데이터를 가져오는 데 사용됩니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 스토리지 구성을 가져옵니다. |
> | 작업 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 스토리지 구성을 삭제합니다. 이렇게 하면 Microsoft Operational Insights에서 스토리지 계정의 데이터 읽기를 중지합니다. |
> |  | **작업 영역/업그레이드 번역 실패** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 작업 영역에 대한 검색 업그레이드 변환 오류 로그를 가져옵니다. |
> |  | **작업 영역/사용** |  |
> | 작업 | Microsoft.OperationalInsights/workspaces/usages/read | 작업 영역에서 읽는 데이터 양을 포함하여 작업 영역에 대한 사용량 현황 데이터를 가져옵니다. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

Azure 서비스: [Azure 모니터](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.OperationsManagement/register/action | 리소스 공급자에 구독을 등록합니다. |
> |  | **관리 협회** |  |
> | 작업 | Microsoft.OperationsManagement/managementAssociations/write | 새 관리 연결을 만듭니다. |
> | 작업 | Microsoft.OperationsManagement/managementAssociations/read | 기존 관리 연결을 가져옵니다. |
> | 작업 | Microsoft.OperationsManagement/managementAssociations/delete | 기존 관리 연결을 삭제합니다. |
> |  | **관리 구성 구성** |  |
> | 작업 | Microsoft.OperationsManagement/managementConfigurations/write | 새 기존 관리 구성을 만듭니다. |
> | 작업 | Microsoft.OperationsManagement/managementConfigurations/read | 기존 관리 구성을 가져옵니다. |
> | 작업 | Microsoft.OperationsManagement/managementConfigurations/delete | 기존 관리 구성을 삭제합니다. |
> |  | **솔루션** |  |
> | 작업 | Microsoft.OperationsManagement/solutions/write | 새 OMS 솔루션을 만듭니다. |
> | 작업 | Microsoft.OperationsManagement/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | 작업 | Microsoft.OperationsManagement/solutions/delete | 기존 OMS 솔루션을 삭제합니다. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

Azure 서비스: [Azure 정책](../governance/policy/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.PolicyInsights/register/action | Microsoft 정책 인사이트 리소스 공급자를 등록하고 이에 대한 작업을 활성화합니다. |
> | 작업 | Microsoft.정책 인사이트/등록 취소/작업 | Microsoft 정책 인사이트 리소스 공급자등록을 취소합니다. |
> |  | **비동기작업 결과** |  |
> | 작업 | Microsoft.PolicyInsights/asyncOperationResults/read | 비동기 작업 결과를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.정책인사이트/운영/읽기 | Microsoft.PolicyInsights 네임스페이스에서 지원되는 작업 가져옵니다. |
> |  | **policyEvents** |  |
> | 작업 | Microsoft.PolicyInsights/policyEvents/queryResults/action | 정책 이벤트에 대한 정보를 쿼리합니다. |
> |  | **정책이벤트/쿼리결과** |  |
> | 작업 | Microsoft.PolicyInsights/policyEvents/queryResults/read | 정책 이벤트에 대한 정보를 쿼리합니다. |
> |  | **정책 메타데이터** |  |
> | 작업 | Microsoft.정책 인사이트/정책메타데이터/읽기 | 정책 메타데이터 리소스를 가져옵니다. |
> |  | **policyStates** |  |
> | 작업 | Microsoft.PolicyInsights/policyStates/queryResults/action | 정책 상태에 대한 정보를 쿼리합니다. |
> | 작업 | Microsoft.PolicyInsights/policyStates/summarize/action | 최신 정책 상태에 대한 요약 정보를 쿼리합니다. |
> | 작업 | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 선택한 범위에 대한 새 준수 평가를 트리거합니다. |
> |  | **정책상태/쿼리결과** |  |
> | 작업 | Microsoft.PolicyInsights/policyStates/queryResults/read | 정책 상태에 대한 정보를 쿼리합니다. |
> |  | **정책상태/요약** |  |
> | 작업 | Microsoft.PolicyInsights/policyStates/summarize/read | 최신 정책 상태에 대한 요약 정보를 쿼리합니다. |
> |  | **정책추적리소스/쿼리결과** |  |
> | 작업 | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | DeployIfNotExists 정책에 필요한 리소스에 대한 정보를 쿼리합니다. |
> |  | **문제 해결** |  |
> | 작업 | Microsoft.PolicyInsights/remediations/read | 정책 수정을 가져옵니다. |
> | 작업 | Microsoft.PolicyInsights/remediations/write | Microsoft 정책 업데이트 만들기 또는 업데이트합니다. |
> | 작업 | Microsoft.PolicyInsights/remediations/delete | 정책 수정을 삭제합니다. |
> | 작업 | Microsoft.PolicyInsights/remediations/cancel/action | 진행 중인 Microsoft 정책 수정을 취소합니다. |
> |  | **업데이트 적용/목록 배포** |  |
> | 작업 | Microsoft.PolicyInsights/remediations/listDeployments/read | 정책 수정에 필요한 배포를 나열합니다. |
> | DataAction | Microsoft.정책 인사이트/체크데이터정책 준수/작업 | 데이터 정책에 대해 지정된 구성 요소의 규정 준수 상태를 확인합니다. |
> |  | **policyEvents** |  |
> | DataAction | Microsoft.정책 인사이트/정책이벤트/로그데이터이벤트/액션 | 리소스 구성 요소 정책 이벤트를 기록합니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

Azure 서비스: [Azure 포털](../azure-portal/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Portal/register/action | 포털에 등록합니다. |
> |  | **consoles** |  |
> | 작업 | Microsoft.Portal/consoles/delete | 클라우드 셸 인스턴스를 제거합니다. |
> | 작업 | Microsoft.Portal/consoles/write | 클라우드 셸 인스턴스를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.포털/콘솔/읽기 | 클라우드 셸 인스턴스를 읽습니다. |
> |  | **대시보드** |  |
> | 작업 | Microsoft.Portal/dashboards/read | 구독에 대한 대시보드를 읽습니다. |
> | 작업 | Microsoft.Portal/dashboards/write | 구독에 대해 대시보드를 추가하거나 수정합니다. |
> | 작업 | Microsoft.Portal/dashboards/delete | 구독에서 대시보드를 제거합니다. |
> |  | **사용자 설정** |  |
> | 작업 | Microsoft.Portal/usersettings/delete | 클라우드 셸 사용자 설정을 제거합니다. |
> | 작업 | Microsoft.Portal/usersettings/write | Cloud Shell 사용자 설정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Portal/usersettings/read | 클라우드 셸 사용자 설정을 읽습니다. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

Azure 서비스: [전원 BI 임베디드](https://docs.microsoft.com/azure/power-bi-embedded/)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.PowerBIDedicated/register/action | Power BI 전용 리소스 공급자를 등록합니다. |
> |  | **capacities** |  |
> | 작업 | Microsoft.PowerBIDedicated/capacities/read | 지정된 Power BI 전용 용량에 대한 정보를 검색합니다. |
> | 작업 | Microsoft.PowerBIDedicated/capacities/write | 지정된 Power BI 전용 용량을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.PowerBIDedicated/capacities/delete | Power BI 전용 용량을 삭제합니다. |
> | 작업 | Microsoft.PowerBIDedicated/capacities/suspend/action | 용량을 일시 중단합니다. |
> | 작업 | Microsoft.PowerBIDedicated/capacities/resume/action | 용량을 다시 시작합니다. |
> |  | **용량/스쿠스** |  |
> | 작업 | Microsoft.PowerBIDedicated/capacities/skus/read | 용량에 사용 가능한 SKU 정보를 검색합니다. |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.PowerBI전용/위치/체크네임가용성/액션 | 지정된 Power BI 전용 용량 이름이 올바르고 사용 중이 아닌지 확인합니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.PowerBIDedicated/locations/operationresults/read | 지정된 작업 결과에 대한 정보를 검색합니다. |
> |  | **위치/작업 상태** |  |
> | 작업 | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 지정된 작업 상태에 대한 정보를 검색합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.PowerBIDedicated/operations/read | 작업 정보를 검색합니다. |
> |  | **Sku** |  |
> | 작업 | Microsoft.PowerBIDedicated/skus/read | SKU 정보를 검색합니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

Azure 서비스: [사이트 복구](../site-recovery/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.RecoveryServices/register/action | 지정된 리소스 공급자에 대한 구독을 등록합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | 작업 | Microsoft.RecoveryServices/Locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | 작업 | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | 작업 | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Check Resource Name Availability는 리소스 이름을 사용할 수 있는지 확인하는 API입니다. |
> |  | **위치/할당스탬프** |  |
> | 작업 | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> |  | **위치/백업보호항목** |  |
> | 작업 | 마이크로소프트.복구 서비스/위치/백업보호항목/쓰기 | 백업 보호 항목을 만듭니다. |
> |  | **위치/백업보호항목** |  |
> | 작업 | Microsoft.복구 서비스/위치/백업보호항목/읽기 | 모든 보호 항목 목록을 반환합니다. |
> |  | **위치/작업 상태** |  |
> | 작업 | Microsoft.RecoveryServices/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.RecoveryServices/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> |  | **보관소** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Recovery Services 자격 증명 모음에 대한 보안 PIN 정보를 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/delete | 자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다. |
> |  | **볼트/백업 구성** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services 자격 증명 모음에 구성을 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupconfig/write | Recovery Services 자격 증명 모음에 대한 구성을 업데이트합니다. |
> |  | **볼트/백업암호화구성** |  |
> | 작업 | 마이크로 소프트.복구 서비스 / 볼트 / 백업암호화 구성 / 읽기 | 백업 리소스 암호화 구성을 가져옵니다. |
> | 작업 | 마이크로 소프트.복구 서비스 / 볼트 / 백업암호화 구성 / 쓰기 | 백업 리소스 암호화 구성 업데이트 |
> |  | **볼트/백업엔진** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> |  | **볼트/백업패브릭** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> |  | **볼트/백업패브릭/백업보호의도** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | 백업 보호 의도를 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> |  | **볼트/백업패브릭/작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> |  | **볼트/백업패브릭/작업상태** |  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/백업패브릭/작업상태/읽기 | 작업의 상태를 반환합니다. |
> |  | **볼트/백업패브릭/보호가능한 컨테이너** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> |  | **볼트/백업패브릭/보호컨테이너** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | 등록된 컨테이너를 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | 컨테이너 내의 워크로드를 조회합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 등록된 컨테이너를 만듭니다. |
> |  | **볼트/백업패브릭/보호컨테이너/항목** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> |  | **볼트/백업패브릭/보호컨테이너/작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> |  | **볼트/백업패브릭/보호컨테이너/운영상태** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/백업패브릭/보호컨테이너/작업상태/읽기 | 보호 컨테이너에서 수행된 작업 상태를 가져옵니다. |
> |  | **볼트/백업패브릭/보호컨테이너/보호항목** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 보호 항목 Backup을 수행합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 보호된 항목을 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> |  | **볼트/백업패브릭/보호컨테이너/보호항목/작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> |  | **볼트/백업패브릭/보호컨테이너/보호항목/작업상태** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> |  | **볼트/백업패브릭/보호컨테이너/보호항목/복구포인트** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 프로비전합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 보호 항목의 복구 지점을 복원합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 취소합니다. |
> |  | **볼트/백업작업** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 작업을 취소합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupJobs/read | 모든 작업 개체를 반환합니다. |
> |  | **볼트/백업작업/작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 작업의 작업 결과를 반환합니다. |
> |  | **볼트/백업작업/작업상태** |  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/백업작업/작업상태/읽기 | 작업 작업의 상태를 반환합니다. |
> |  | **볼트/백업작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services 자격 증명 모음의 Backup 작업 결과를 반환합니다. |
> |  | **볼트/백업운영** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> |  | **볼트/백업정책** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | 보호 정책을 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 보호 정책을 만듭니다. |
> |  | **볼트/백업정책/운영결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> |  | **볼트/백업정책/작업** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> |  | **볼트/백업보호가능아이템** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 모든 보호 가능한 항목 목록을 반환합니다. |
> |  | **볼트/백업보호항목** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> |  | **볼트/백업보호컨테이너** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> |  | **볼트/백업보호 의도** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> |  | **볼트/백업스토리지구성** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services 자격 증명 모음에 대한 스토리지 구성을 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Recovery Services 자격 증명 모음에 대한 스토리지 구성을 업데이트합니다. |
> |  | **볼트/백업사용요약** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> |  | **볼트/인증서** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> |  | **볼트/확장정보** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> |  | **볼트/모니터링경고** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> |  | **볼트/모니터링구성** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Recovery Services 자격 증명 모음 알림 구성을 가져옵니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Recovery Services 자격 증명 모음에 이메일 알림을 구성합니다. |
> |  | **볼트/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.복구 서비스 / 볼트 / 개인엔드 포인트 연결 Proxies / 삭제 | 몇 분간 기다린 다음 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |
> | 작업 | 마이크로 소프트.복구 서비스 / 볼트 / 개인엔드 포인트 연결 Proxies / 읽기 | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | 작업 | 마이크로소프트.복구 서비스/볼트/개인엔드포인트연결프록시/유효성 검사/조치 | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | 작업 | 마이크로 소프트.복구 서비스 / 볼트 / 개인엔드 포인트 연결 Proxies / 쓰기 | 보호 가능한 컨테이너를 모두 가져옵니다. |
> |  | **볼트/프라이빗엔드포인트커넥션프로시즈/운영상태** |  |
> | 작업 | 마이크로 소프트.복구 서비스 / 볼트 / 개인엔드 포인트 연결Proxies / 작업상태 / 읽기 | 보호 가능한 컨테이너를 모두 가져옵니다. |
> |  | **볼트/프라이빗 엔드포인트 커넥션** |  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/개인엔드포인트 연결/삭제 | 개인 엔드포인트 요청을 삭제합니다. 이 호출은 백업 관리자에 의해 이루어집니다. |
> | 작업 | 마이크로소프트.복구 서비스/볼트/개인엔드포인트연결/쓰기 | 비공개 엔드포인트 요청을 승인 또는 거부합니다. 이 호출은 백업 관리자에 의해 이루어집니다. |
> |  | **볼트/프라이빗엔드포인트연결/운영상태** |  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/개인엔드포인트 연결/작업상태/읽기 | 개인 끝점 연결에 대한 작업 상태를 반환합니다. |
> |  | **볼트/등록된ID** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | 컨테이너 등록 취소 작업을 사용하여 컨테이너 등록을 취소할 수 있습니다. |
> |  | **볼트/등록된신원/운영결과** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> |  | **볼트/복제경고설정** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 경고 설정을 만들거나 업데이트합니다. |
> |  | **볼트/복제이벤트** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> |  | **볼트/복제패브릭** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 패브릭을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 패브릭을 제거합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 패브릭의 일관성을 검사합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 패브릭을 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 패브릭용 인증서 갱신 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 프로세스 서버 이미지를 배포합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 게이트웨이를 다시 연결합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Fabric에서 AAD로 마이그레이션합니다. |
> |  | **볼트/복제패브릭/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/작업 결과/읽기 | 리소스 패브릭에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제논리적 네트워크** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 논리 네트워크를 읽습니다. |
> |  | **볼트/복제패브릭/복제네트워크** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> |  | **볼트/복제패브릭/복제네트워크/복제네트워크매핑** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 네트워크 매핑을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 네트워크 매핑을 삭제합니다. |
> |  | **볼트/복제패브릭/복제보호보호컨테이너** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 보호 가능한 항목을 검색합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 보호 컨테이너를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 보호 컨테이너를 제거합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 보호 컨테이너를 전환합니다. |
> |  | **볼트/복제패브릭/복제보호보호컨테이너/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호보호컨테이너/작업 결과/읽기 | 리소스 보호 컨테이너에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제보호보호컨테이너/복제마이그레이션항목** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 마이그레이션 항목을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 마이그레이션 항목을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 마이그레이션 항목을 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제항목/재동기화/작업 | 다시 동기화 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 마이그레이션 항목 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 테스트 마이그레이션 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 테스트 마이그레이션 정리 |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제마이그레이션항목/마이그레이션복구포인트** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 마이그레이션 복제 복구 지점을 읽습니다. |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제마이그레이션항목/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호보호컨테이너/복제항목/작업 결과/읽기 | 리소스 마이그레이션 항목에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제보호보호컨테이너/복제보호보호가능아이템** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제보호항목** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 보호된 항목을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 보호된 항목을 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 보호된 항목을 제거합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 계획된 장애 조치(Failover) |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 장애 조치 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 테스트 장애 조치(Failover) |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 테스트 장애 조치(Failover) 정리 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 장애 조치(Failover) 커밋 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 보호된 항목을 다시 보호합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 모바일 서비스를 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 복제를 복구합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 복구 지점을 적용합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 피드백을 제출합니다. |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제보호항목/추가 디스크/작업 | 디스크 추가 |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제보호항목/제거디스크/액션 | 디스크 제거 |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제보호항목/해결상태 오류/작업 |  |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제보호항목/작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제보호항목/작업 결과/읽기 | 리소스 보호 항목에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제보호항목/복구포인트** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제보호항목/대상컴퓨터크기** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 모든 대상 컴퓨팅 크기 읽기 |
> |  | **볼트/복제패브릭/복제보호컨테이너/복제보호보호컨테이너매핑** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 보호 컨테이너 매핑을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 보호 컨테이너 매핑을 제거합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 보호 컨테이너 매핑을 삭제합니다. |
> |  | **볼트/복제패브릭/복제보호보호컨테이너/복제보호보호컨테이너매핑/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제보호컨테이너매핑/작업 결과/읽기 | 리소스 보호 컨테이너 매핑에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제복구서비스제공자** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services 공급자를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services 공급자를 제거합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services 공급자를 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 공급자를 새로 고칩니다. |
> |  | **볼트/복제패브릭/복제복구서비스제공자/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제복구서비스제공자/작업 결과/읽기 | 리소스 복구 서비스 공급자에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제스토리지분류** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> |  | **볼트/복제패브릭/복제스토리지분류/복제스토리지분류매핑** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 스토리지 분류 매핑을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 스토리지 분류 매핑을 삭제합니다. |
> |  | **볼트/복제패브릭/복제스토리지분류/복제스토리지분류분류/작업결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제스토리지분류/복제스토리지분류매핑/작업 결과/읽기 | 리소스 저장소 분류 매핑에서 비동기 작업 결과 추적 |
> |  | **볼트/복제패브릭/복제v센터** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | vCenter를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | vCenter를 삭제합니다. |
> |  | **볼트/복제패브릭/복제v센터/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제패브릭/복제v센터/작업 결과/읽기 | 리소스 vCenters에서 비동기 작업 결과 추적 |
> |  | **볼트/복제작업** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationJobs/read | 작업을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 작업을 취소합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 작업을 다시 시작합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 작업을 계속합니다. |
> |  | **볼트/복제작업/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제작업/작업 결과/읽기 | 리소스 작업에서 비동기 작업의 결과 추적 |
> |  | **볼트/복제마이그레이션항목** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 마이그레이션 항목을 읽습니다. |
> |  | **볼트/복제네트워크매핑** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> |  | **볼트/복제네트워크** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 네트워크를 읽습니다. |
> |  | **볼트/복제작업 상태** |  |
> | 작업 | Microsoft.복구 서비스/볼트/복제작업 상태/읽기 | 자격 증명 모음 복제 작업 상태 읽기 |
> |  | **볼트/복제정책** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 정책을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 정책을 삭제합니다. |
> |  | **볼트/복제정책/작업 결과** |  |
> | 작업 | Microsoft.복구 서비스/볼트/복제정책/작업 결과/읽기 | 리소스 정책에서 비동기 작업 결과 추적 |
> |  | **볼트/복제보호항목** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> |  | **볼트/복제보호컨테이너매핑** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> |  | **볼트/복제보호컨테이너** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> |  | **볼트/복제복구계획** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 복구 계획을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 복구 계획을 삭제합니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 계획된 장애 조치(Failover) 복구 계획 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 장애 조치(Failover) 복구 계획 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 테스트 장애 조치(failover) 복구 계획 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 테스트 장애 조치(failover) 정리 복구 계획 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 장애 조치(Failover) 커밋 복구 계획 |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 복구 계획을 다시 보호합니다. |
> |  | **볼트/복제복구계획/작업 결과** |  |
> | 작업 | Microsoft.RecoveryServices/볼트/복제복구계획/작업 결과/읽기 | 리소스 복구 계획에서 비동기 작업 결과 추적 |
> |  | **볼트/복제복구서비스제공자** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> |  | **볼트/복제스토리지분류매핑** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> |  | **볼트/복제스토리지분류** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> |  | **볼트/복제지원운영시스템** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 모든 항목을 읽습니다.  |
> |  | **볼트/복제사용** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationUsages/read | 자격 증명 모음 복제 사용 현황을 읽습니다. |
> |  | **볼트/복제볼트Health** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 자격 증명 모음 복제 상태를 읽습니다. |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 자격 증명 모음 상태를 새로 고칩니다. |
> |  | **볼트/복제볼트건강/작업 결과** |  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/복제볼트건강/작업 결과/읽기 | 리소스 볼트 복제 상태에서 비동기 작업 결과 추적 |
> |  | **볼트/복제볼트설정** |  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/복제볼트설정/읽기 | 모든 항목을 읽습니다.  |
> | 작업 | 마이크로소프트.복구 서비스/볼트/복제볼트설정/쓰기 | 만들기 또는 업데이트  |
> |  | **볼트/복제v센터** |  |
> | 작업 | Microsoft.RecoveryServices/vaults/replicationvCenters/read | vCenter를 읽습니다. |
> |  | **볼트/사용** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | 작업 | Microsoft.RecoveryServices/Vaults/usages/read | 자격 증명 모음 사용 현황을 읽습니다. |
> |  | **볼트/볼트토큰** |  |
> | 작업 | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |

## <a name="microsoftrelay"></a>Microsoft.Relay

Azure 서비스: [Azure 릴레이](../service-bus-relay/relay-what-is-it.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Relay/checkNamespaceAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailability를 대신 사용하세요. |
> | 작업 | Microsoft.Relay/checkNameAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. |
> | 작업 | Microsoft.Relay/register/action | 릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다. |
> | 작업 | Microsoft.Relay/unregister/action | 릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다. |
> |  | **namespaces** |  |
> | 작업 | Microsoft.Relay/namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 작업 | Microsoft.Relay/namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 작업 | Microsoft.Relay/namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 작업 | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS 네임스페이스를 제거합니다. |
> |  | **네임스페이스/권한 부여규칙** |  |
> | 작업 | Microsoft.Relay/namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 작업 | Microsoft.Relay/namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 작업 | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **네임스페이스/재해 복구 구성** |  |
> | 작업 | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다. |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 네임스페이스와 연결된 재해 복구 구성을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다. |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다. |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다. |
> |  | **네임스페이스/재해복구구성/권한 부여규칙** |  |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다. |
> |  | **네임스페이스/하이브리드 연결** |  |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/write | HybridConnection 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection 리소스 삭제 작업 |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | HybridConnection을 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> |  | **네임스페이스/하이브리드 연결/권한 부여규칙** |  |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  HybridConnection 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | HybridConnection 권한 부여 규칙 삭제 작업 |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | HybridConnection 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **네임스페이스/메시징플랜** |  |
> | 작업 | Microsoft.Relay/namespaces/messagingPlan/read | 네임스페이스에 대한 메시지 계획을 가져옵니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 작업 | Microsoft.Relay/namespaces/messagingPlan/write | 네임스페이스에 대한 메시지 계획을 업데이트합니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> |  | **네임스페이스/네트워크 규칙 집합** |  |
> | 작업 | Microsoft.릴레이/네임스페이스/네트워크 규칙 집합/읽기 | 네트워크규칙 집합 리소스 를 가져옵니다. |
> | 작업 | Microsoft.릴레이/네임스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.릴레이/네임스페이스/네트워크 규칙 집합/삭제 | VNET 규칙 리소스 삭제 |
> |  | **네임스페이스/작업 결과** |  |
> | 작업 | Microsoft.Relay/namespaces/operationresults/read | 네임스페이스 작업의 상태를 가져옵니다. |
> |  | **네임스페이스/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로소프트.릴레이/네임스페이스/프라이빗엔드포인트커넥션프록시스/유효성 검사/액션 | 개인 엔드포인트 연결 프록시 유효성 검사 |
> | 작업 | 마이크로 소프트.릴레이 / 네임 스페이스 / 개인엔드 포인트 연결 Proxies / 읽기 | 개인 엔드포인트 연결 프록시 받기 |
> | 작업 | 마이크로 소프트.릴레이 / 네임 스페이스 / 개인엔드 포인트 연결 Proxies / 쓰기 | 프라이빗 엔드포인트 연결 프록시 작성 |
> | 작업 | 마이크로 소프트.릴레이 / 네임 스페이스 / 개인엔드 포인트 연결 Proxies / 삭제 | 개인 끝점 연결 프록시 삭제 |
> |  | **네임스페이스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | 마이크로소프트.릴레이/네임스페이스/공급자/마이크로소프트.인사이트/진단설정/읽기 | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 작업 | 마이크로소프트.릴레이/네임스페이스/공급자/마이크로소프트.인사이트/진단설정/쓰기 | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/공급자/Microsoft.Insights/logDEFINITIONs** |  |
> | 작업 | 마이크로소프트.릴레이/네임스페이스/공급자/마이크로소프트.인사이트/로그정의/읽기 | 네임스페이스 로그 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/Wcf릴레이** |  |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/write | WcfRelay 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/read | WcfRelay 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/Delete | WcfRelay 리소스 삭제 작업 |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | WcfRelay를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> |  | **네임스페이스/Wcf릴레이/권한 부여규칙** |  |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  WcfRelay 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | WcfRelay 권한 부여 규칙 삭제 작업 |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | WcfRelay의 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Relay/operations/read | 작업을 가져옵니다. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

Azure 서비스: [Azure 서비스 상태](../service-health/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ResourceHealth/register/action | Microsoft ResourceHealth에 대한 구독을 등록합니다. |
> | 작업 | Microsoft.ResourceHealth/unregister/action | Microsoft ResourceHealth에 대한 구독을 등록 취소합니다. |
> | 작업 | Microsoft.Resourcehealth/healthevent/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> |  | **가용성 상태** |  |
> | 작업 | Microsoft.ResourceHealth/AvailabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> |  | **가용성 상태/현재 상태** |  |
> | 작업 | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 지정된 리소스에 대한 가용성 상태를 가져옵니다. |
> |  | **신흥 이슈** |  |
> | 작업 | 마이크로소프트.리소스헬스/신흥 이슈/읽기 | Azure 서비스의 새로운 문제 받기 |
> |  | **이벤트** |  |
> | 작업 | Microsoft.ResourceHealth/events/read | 지정된 구독에 대한 Service Health 이벤트를 가져옵니다. |
> |  | **헬스 이벤트** |  |
> | 작업 | Microsoft.Resourcehealth/healthevent/Activated/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 작업 | Microsoft.Resourcehealth/healthevent/Updated/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 작업 | Microsoft.Resourcehealth/healthevent/Resolved/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 작업 | Microsoft.Resourcehealth/healthevent/InProgress/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 작업 | Microsoft.Resourcehealth/healthevent/Pending/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> |  | **impactedResources** |  |
> | 작업 | Microsoft.ResourceHealth/impactedResources/read | 지정된 리소스로 인해 영향을 받는 리소스를 가져옵니다. |
> |  | **메타 데이터** |  |
> | 작업 | 마이크로소프트.리소스 건강/메타 데이터/읽기 | 메타데이터 가져옵니다. |
> |  | **알림** |  |
> | 작업 | 마이크로소프트.리소스 건강/알림/읽기 | Azure 리소스 관리자 알림 수신 |
> |  | **작업** |  |
> | 작업 | Microsoft.ResourceHealth/Operations/read | Microsoft ResourceHealth에 사용 가능한 작업을 가져옵니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

Azure 서비스: [Azure 리소스 관리자](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Resources/checkResourceName/action | 리소스 이름의 유효성을 확인합니다. |
> | 작업 | 마이크로 소프트.자원 / 계산템플릿 해시 / 작업 | 제공된 템플릿의 해시를 계산합니다. |
> |  | **확인정책 준수** |  |
> | 작업 | Microsoft.리소스/체크정책 준수/읽기 | 지정된 리소스의 준수 상태를 리소스 정책과 비교하여 확인합니다. |
> |  | **배포** |  |
> | 작업 | Microsoft.Resources/deployments/read | 배포를 가져오거나 나열합니다. |
> | 작업 | Microsoft.Resources/deployments/write | 배포를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Resources/deployments/delete | 배포를 삭제합니다. |
> | 작업 | Microsoft.Resources/deployments/cancel/action | 배포를 취소합니다. |
> | 작업 | Microsoft.Resources/deployments/validate/action | 배포의 유효성을 검사합니다. |
> | 작업 | Microsoft.리소스/배포/whatIf/작업 | 템플릿 배포 변경 사항을 예측합니다. |
> | 작업 | Microsoft.리소스/배포/내보내기템플릿/작업 | 배포용 템플릿 내보내기 |
> |  | **배포/작업** |  |
> | 작업 | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> |  | **배포/작업 상태** |  |
> | 작업 | Microsoft.리소스/배포/작업 상태/읽기 | 배포 작업 상태를 가져오거나 나열합니다. |
> |  | **배포스크립트** |  |
> | 작업 | Microsoft.리소스/배포스크립트/읽기 | 배포 스크립트 를 가져옵니다 또는 나열 |
> | 작업 | Microsoft.리소스/배포스크립트/쓰기 | 배포 스크립트 를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.리소스/배포스크립트/삭제 | 배포 스크립트 삭제 |
> |  | **배포스크립트/로그** |  |
> | 작업 | Microsoft.리소스/배포스크립트/로그/읽기 | 배포 스크립트 로그 를 얻거나 나열합니다. |
> |  | **링크** |  |
> | 작업 | Microsoft.Resources/links/read | 리소스 링크를 가져오거나 나열합니다. |
> | 작업 | Microsoft.Resources/links/write | 리소스 링크를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Resources/links/delete | 리소스 링크를 삭제합니다. |
> |  | **시장** |  |
> | 작업 | Microsoft.Resources/marketplace/purchase/action | 마켓플레이스에서 리소스를 구매합니다. |
> |  | **providers** |  |
> | 작업 | Microsoft.Resources/providers/read | 공급자 목록을 가져옵니다. |
> |  | **리소스** |  |
> | 작업 | Microsoft.Resources/resources/read | 필터를 기준으로 리소스 목록을 가져옵니다. |
> |  | **구독** |  |
> | 작업 | Microsoft.Resources/subscriptions/read | 구독 목록을 가져옵니다. |
> |  | **구독/위치** |  |
> | 작업 | Microsoft.Resources/subscriptions/locations/read | 지원되는 위치를 가져오거나 나열합니다. |
> |  | **구독/작업 결과** |  |
> | 작업 | Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> |  | **subscriptions/providers** |  |
> | 작업 | Microsoft.Resources/subscriptions/providers/read | 리소스 공급자를 가져오거나 나열합니다. |
> |  | **subscriptions/resourceGroups** |  |
> | 작업 | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | 작업 | Microsoft.Resources/subscriptions/resourceGroups/write | 리소스 그룹을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Resources/subscriptions/resourceGroups/delete | 리소스 그룹과 해당하는 모든 리소스를 삭제합니다. |
> | 작업 | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 한 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동합니다. |
> | 작업 | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 한 리소스 그룹에서 다른 리소스 그룹으로 리소스 이동의 유효성을 검사합니다. |
> |  | **구독/리소스 그룹/배포** |  |
> | 작업 | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 배포를 가져오거나 나열합니다. |
> | 작업 | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 배포를 만들거나 업데이트합니다. |
> |  | **구독/리소스 그룹/배포/작업** |  |
> | 작업 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> |  | **구독/리소스 그룹/배포/작업 상태** |  |
> | 작업 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 배포 작업 상태를 가져오거나 나열합니다. |
> |  | **subscriptions/resourcegroups/resources** |  |
> | 작업 | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 리소스 그룹에 대한 리소스를 가져옵니다. |
> |  | **subscriptions/resources** |  |
> | 작업 | Microsoft.Resources/subscriptions/resources/read | 구독 리소스를 가져옵니다. |
> |  | **구독/태그 이름** |  |
> | 작업 | Microsoft.Resources/subscriptions/tagNames/read | 구독 태그를 가져오거나 나열합니다. |
> | 작업 | Microsoft.Resources/subscriptions/tagNames/write | 구독 태그를 추가합니다. |
> | 작업 | Microsoft.Resources/subscriptions/tagNames/delete | 구독 태그를 삭제합니다. |
> |  | **subscriptions/tagNames/tagValues** |  |
> | 작업 | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 구독 태그 값을 가져오거나 나열합니다. |
> | 작업 | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 구독 태그 값을 추가합니다. |
> | 작업 | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 구독 태그 값을 삭제합니다. |
> |  | **태그** |  |
> | 작업 | 마이크로소프트.리소스/태그/읽기 | 리소스의 모든 태그를 가져옵니다. |
> | 작업 | 마이크로소프트.리소스/태그/쓰기 | 기존 태그를 새 태그 집합으로 대체하거나 병합하거나 기존 태그를 제거하여 리소스의 태그를 업데이트합니다. |
> | 작업 | 마이크로소프트.리소스/태그/삭제 | 리소스의 모든 태그를 제거합니다. |
> |  | **거주자** |  |
> | 작업 | Microsoft.Resources/tenants/read | 테넌트 목록을 가져옵니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

Azure 서비스: [스케줄러](../scheduler/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **jobcollections** |  |
> | 작업 | Microsoft.Scheduler/jobcollections/read | 작업 컬렉션을 가져옵니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/write | 작업 컬렉션을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/delete | 작업 컬렉션을 삭제합니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/enable/action | 작업 컬렉션을 사용하도록 설정합니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/disable/action | 작업 컬렉션을 사용하지 않도록 설정합니다. |
> |  | **작업 컬렉션/작업** |  |
> | 작업 | Microsoft.Scheduler/jobcollections/jobs/read | 작업을 가져옵니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/jobs/write | 작업을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/jobs/delete | 작업을 삭제합니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/jobs/run/action | 작업을 실행합니다. |
> | 작업 | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Scheduler 작업을 기반으로 하여 논리 앱 정의를 생성합니다. |
> |  | **일자리/일자리/직업** |  |
> | 작업 | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 작업 내역을 가져옵니다. |

## <a name="microsoftsearch"></a>Microsoft.Search

Azure 서비스: [Azure 검색](../search/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Search/register/action | Search 리소스 공급자에 대한 구독을 등록하고 Search 서비스를 만들도록 설정합니다. |
> | 작업 | Microsoft.Search/checkNameAvailability/action | 서비스 이름의 가용성을 확인합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Search/operations/read | Microsoft.Search 공급자의 사용 가능한 작업을 모두 나열합니다. |
> |  | **검색 서비스** |  |
> | 작업 | Microsoft.Search/searchServices/write | Search 서비스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Search/searchServices/read | Search 서비스를 읽습니다. |
> | 작업 | Microsoft.Search/searchServices/delete | Search 서비스를 삭제합니다. |
> | 작업 | Microsoft.Search/searchServices/start/action | Search 서비스를 시작합니다. |
> | 작업 | Microsoft.Search/searchServices/stop/action | Search 서비스를 중지합니다. |
> | 작업 | Microsoft.Search/searchServices/listAdminKeys/action | 관리 키를 읽습니다. |
> | 작업 | Microsoft.Search/searchServices/regenerateAdminKey/action | 관리 키를 다시 생성합니다. |
> | 작업 | 마이크로소프트.검색/검색 서비스/목록쿼리키/작업 | 지정된 Azure Search 서비스에 대한 쿼리 API 키 목록을 반환합니다. |
> | 작업 | Microsoft.Search/searchServices/createQueryKey/action | 쿼리 키를 만듭니다. |
> |  | **검색 서비스/삭제쿼리키** |  |
> | 작업 | Microsoft.Search/searchServices/deleteQueryKey/delete | 쿼리 키를 삭제합니다. |
> |  | **검색 서비스/개인엔드포인트커넥션** |  |
> | 작업 | 마이크로 소프트.검색 /검색 서비스 / 개인엔드 포인트 연결Proxies / 유효성 검사 / 작업 | NRP 측에서 개인 엔드포인트 연결 create 호출 유효성 검사 |
> | 작업 | 마이크로 소프트.검색 / 검색 서비스 / 개인엔드 포인트 연결 Proxies / 쓰기 | 지정된 매개 변수를 사용 하 고 개인 끝점 연결 프록시를 만들거나 지정 된 개인 끝점 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 작업 | 마이크로 소프트.검색 / 검색 서비스 / 개인엔드 포인트 연결 Proxies / 읽기 | 개인 끝점 연결 프록시 목록을 반환하거나 지정된 개인 끝점 연결 프록시에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로 소프트.검색 / 검색 서비스 / 개인엔드 포인트 연결 Proxies / 삭제 | 기존 개인 끝점 연결 프록시 삭제 |

## <a name="microsoftsecurity"></a>Microsoft.Security

Azure 서비스: [보안 센터](../security-center/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Security/register/action | Azure Security Center에 대한 구독을 등록합니다. |
> | 작업 | Microsoft.Security/unregister/action | Azure Security Center에서 구독을 등록 취소합니다. |
> |  | **적응네트워크강화** |  |
> | 작업 | Microsoft.보안/적응네트워크 강화/읽기 | Azure 보호 리소스의 적응형 네트워크 강화 권장 사항 가져옵니다. |
> | 작업 | Microsoft.보안/적응네트워크 강화/적용/작업 | 지정된 네트워크 보안 그룹에 일치하는 보안 규칙을 만들어 지정된 트래픽 강화 규칙을 적용합니다. |
> |  | **advancedThreatProtectionSettings** |  |
> | 작업 | Microsoft.Security/advancedThreatProtectionSettings/read | 리소스에 대한 Advanced Threat Protection 설정을 가져옵니다. |
> | 작업 | Microsoft.Security/advancedThreatProtectionSettings/write | 리소스에 대한 Advanced Threat Protection 설정을 업데이트합니다. |
> |  | **경고** |  |
> | 작업 | Microsoft.Security/alerts/read | 모든 사용 가능한 보안 경고를 가져옵니다. |
> |  | **applicationWhitelistings** |  |
> | 작업 | Microsoft.Security/applicationWhitelistings/read | 애플리케이션 허용 목록을 가져옵니다. |
> | 작업 | Microsoft.Security/applicationWhitelistings/write | 새 애플리케이션 허용 목록을 만들거나 기존 애플리케이션 허용 목록을 업데이트합니다. |
> |  | **평가메타데이터** |  |
> | 작업 | Microsoft.보안/평가메타데이터/읽기 | 구독에서 사용 가능한 보안 평가 메타데이터 얻기 |
> | 작업 | Microsoft.보안/평가메타데이터/쓰기 | 보안 평가 메타데이터 생성 또는 업데이트 |
> |  | **평가** |  |
> | 작업 | Microsoft.보안/평가/읽기 | 구독에 대한 보안 평가 받기 |
> | 작업 | Microsoft.보안/평가/쓰기 | 구독에서 보안 평가 생성 또는 업데이트 |
> |  | **자동 프로비저닝 설정** |  |
> | 작업 | 마이크로소프트.보안/자동 프로비저닝설정/읽기 | 구독에 대한 보안 자동 프로비저닝 설정 받기 |
> | 작업 | 마이크로 소프트.보안 / 자동 프로비저닝설정 / 쓰기 | 구독에 대한 보안 자동 프로비저닝 설정 만들기 또는 업데이트 |
> |  | **컴플라이언스 결과** |  |
> | 작업 | Microsoft.Security/complianceResults/read | 리소스에 대한 준수 결과를 가져옵니다. |
> |  | **정보 보호 정책** |  |
> | 작업 | Microsoft.Security/informationProtectionPolicies/read | 리소스에 대한 정보 보호 정책을 가져옵니다. |
> | 작업 | Microsoft.Security/informationProtectionPolicies/write | 리소스에 대한 정보 보호 정책을 업데이트합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.Security/locations/read | 보안 데이터 위치를 가져옵니다. |
> |  | **위치/알림** |  |
> | 작업 | Microsoft.Security/locations/alerts/read | 모든 사용 가능한 보안 경고를 가져옵니다. |
> | 작업 | Microsoft.Security/locations/alerts/dismiss/action | 보안 경고를 해제합니다. |
> | 작업 | Microsoft.Security/locations/alerts/activate/action | 보안 경고를 활성화합니다. |
> |  | **위치/jitNetworkAccessPolicy** |  |
> | 작업 | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Just-In-Time 네트워크 액세스 정책을 가져옵니다. |
> | 작업 | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 새 Just-In-Time 네트워크 액세스 정책을 만들거나 기존 Just-In-Time 네트워크 액세스 정책을 업데이트합니다. |
> | 작업 | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Just-In-Time 네트워크 액세스 정책을 삭제합니다. |
> | 작업 | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Just-In-Time 네트워크 액세스 정책 요청을 시작합니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.Security/locations/tasks/read | 모든 사용 가능한 보안 권장 사항을 가져옵니다. |
> | 작업 | Microsoft.Security/locations/tasks/start/action | 보안 권장 사항을 시작합니다. |
> | 작업 | Microsoft.Security/locations/tasks/resolve/action | 보안 권장 사항을 해결합니다. |
> | 작업 | Microsoft.Security/locations/tasks/activate/action | 보안 권장 사항을 활성화합니다. |
> | 작업 | Microsoft.Security/locations/tasks/dismiss/action | 보안 권장 사항을 해제합니다. |
> |  | **정책** |  |
> | 작업 | Microsoft.Security/policies/read | 보안 규칙을 가져옵니다. |
> | 작업 | Microsoft.Security/policies/write | 보안 규칙을 업데이트합니다. |
> |  | **pricings** |  |
> | 작업 | Microsoft.Security/pricings/read | 범위에 대한 가격 책정 설정을 가져옵니다. |
> | 작업 | Microsoft.Security/pricings/write | 범위에 대한 가격 책정 설정을 업데이트합니다. |
> | 작업 | Microsoft.Security/pricings/delete | 범위에 대한 가격 책정 설정을 삭제합니다. |
> |  | **securityContacts** |  |
> | 작업 | Microsoft.Security/securityContacts/read | 보안 연락처를 가져옵니다. |
> | 작업 | Microsoft.Security/securityContacts/write | 보안 연락처를 업데이트합니다. |
> | 작업 | Microsoft.Security/securityContacts/delete | 보안 연락처를 삭제합니다. |
> |  | **securitySolutions** |  |
> | 작업 | Microsoft.Security/securitySolutions/read | 보안 솔루션을 가져옵니다. |
> | 작업 | Microsoft.Security/securitySolutions/write | 새 보안 솔루션을 만들거나 기존 보안 솔루션을 업데이트합니다. |
> | 작업 | Microsoft.Security/securitySolutions/delete | 보안 솔루션을 삭제합니다. |
> |  | **securitySolutionsReferenceData** |  |
> | 작업 | Microsoft.Security/securitySolutionsReferenceData/read | 보안 솔루션 참조 데이터를 가져옵니다. |
> |  | **securityStatuses** |  |
> | 작업 | Microsoft.Security/securityStatuses/read | Azure 리소스의 보안 상태를 가져옵니다. |
> |  | **securityStatusesSummaries** |  |
> | 작업 | Microsoft.Security/securityStatusesSummaries/read | 범위에 대한 보안 상태 요약 정보를 가져옵니다. |
> |  | **설정** |  |
> | 작업 | Microsoft.Security/settings/read | 범위에 대한 설정을 가져옵니다. |
> | 작업 | Microsoft.Security/settings/write | 범위에 대한 설정을 업데이트합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Security/tasks/read | 모든 사용 가능한 보안 권장 사항을 가져옵니다. |
> |  | **웹응용프로그램방화벽** |  |
> | 작업 | Microsoft.Security/webApplicationFirewalls/read | 웹 애플리케이션 방화벽을 가져옵니다. |
> | 작업 | Microsoft.Security/webApplicationFirewalls/write | 새 웹 애플리케이션 방화벽을 만들거나 기존 웹 애플리케이션 방화벽을 업데이트합니다. |
> | 작업 | Microsoft.Security/webApplicationFirewalls/delete | 웹 애플리케이션 방화벽을 삭제합니다. |
> |  | **workspaceSettings** |  |
> | 작업 | Microsoft.Security/workspaceSettings/read | 작업 영역 설정을 가져옵니다. |
> | 작업 | Microsoft.Security/workspaceSettings/write | 작업 영역 설정을 업데이트합니다. |
> | 작업 | Microsoft.Security/workspaceSettings/delete | 작업 영역 설정을 삭제합니다. |
> | 작업 | Microsoft.Security/workspaceSettings/connect/action | 작업 영역 설정 재연결 설정을 변경합니다. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

마이크로소프트 모니터링 인사이트

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **진단 설정** |  |
> | 작업 | Microsoft.SecurityGraph/diagnosticsettings/write | 진단 설정을 작성합니다. |
> | 작업 | Microsoft.SecurityGraph/diagnosticsettings/read | 진단 설정을 읽습니다. |
> | 작업 | Microsoft.SecurityGraph/diagnosticsettings/delete | 진단 설정을 삭제합니다. |
> |  | **진단 설정범주** |  |
> | 작업 | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 진단 설정 범주를 읽습니다. |

## <a name="microsoftsecurityinsights"></a>마이크로소프트.시큐리티인사이트

Azure 서비스: [Azure 센티넬](../sentinel/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.시큐리티인사이트/레지스터/액션 | Azure Sentinel에 대한 구독 등록 |
> | 작업 | 마이크로소프트.시큐리티인사이트/등록 취소/작업 | Azure Sentinel에서 구독 등록 취소 |
> |  | **집계** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/집계/읽기 | 집계된 정보 가져오기 |
> |  | **경고규칙** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/경고규칙/읽기 | 경고 규칙 가져옵니다. |
> | 작업 | 마이크로소프트.시큐리티인사이트/경고규칙/쓰기 | 업데이트 경고 규칙 |
> | 작업 | 마이크로소프트.시큐리티인사이트/경고규칙/삭제 | 경고 규칙 삭제 |
> |  | **경고규칙/작업** |  |
> | 작업 | 마이크로소프트.보안인사이트/경고규칙/작업/읽기 | 경고 규칙의 응답 작업 가져옵니다. |
> | 작업 | 마이크로소프트.보안인사이트/경고규칙/작업/쓰기 | 경고 규칙의 응답 작업 업데이트 |
> | 작업 | 마이크로 소프트.보안 인사이트 / 경고규칙 / 작업 / 삭제 | 경고 규칙의 응답 작업 삭제 |
> |  | **책갈피** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/북마크/읽기 | 책갈피 받기 |
> | 작업 | 마이크로소프트.시큐리티인사이트/북마크/쓰기 | 북마크 업데이트 |
> | 작업 | 마이크로소프트.시큐리티인사이트/북마크/삭제 | 책갈피 삭제 |
> | 작업 | 마이크로소프트.시큐리티인사이트/북마크/확장/액션 | 특정 확장에 의해 엔터티의 관련 엔터티를 가져옵니다. |
> |  | **경우** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/사례/읽기 | 대/소문자 를 가져옵니다. |
> | 작업 | 마이크로소프트.시큐리티인사이트/사례/쓰기 | 사례 업데이트 |
> | 작업 | 마이크로소프트.시큐리티인사이트/서비스 케이스/삭제 | 대/소문자 삭제 |
> |  | **서비스 케이스/댓글** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/케이스/코멘트/읽기 | 사례 주석 가져옵니다. |
> | 작업 | 마이크로소프트.시큐리티인사이트/케이스/코멘트/쓰기 | 대/소문자 주석 생성 |
> |  | **사례/조사** |  |
> | 작업 | Microsoft.보안 인사이트/사례/조사/읽기 | 사례 조사 를 가져옵니다. |
> | 작업 | Microsoft.보안 인사이트/사례/조사/쓰기 | 사례의 메타데이터 업데이트 |
> |  | **데이터 커넥터** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/데이터커넥터/읽기 | 데이터 커넥터 를 가져옵니다. |
> | 작업 | 마이크로소프트.시큐리티인사이트/데이터커넥터/쓰기 | 데이터 커넥터 업데이트 |
> | 작업 | 마이크로소프트.시큐리티인사이트/데이터커넥터/삭제 | 데이터 커넥터 삭제 |
> |  | **사건** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/인시던트/읽기 | 인시던트 발생 |
> | 작업 | 마이크로소프트.시큐리티인사이트/인시던트/쓰기 | 인시던트 업데이트 |
> | 작업 | 마이크로소프트.시큐리티인사이트/인시던트/삭제 | 인시던트 삭제 |
> |  | **인시던트/댓글** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/인시던트/코멘트/읽기 | 인시던트 주석 가져옵니다. |
> | 작업 | 마이크로소프트.시큐리티인사이트/인시던트/댓글/쓰기 | 인시던트에 대한 댓글 생성 |
> |  | **사건/관계** |  |
> | 작업 | Microsoft.보안인사이트/인시던트/관계/읽기 | 인시던트와 관련 리소스 간의 관계 가져오기 |
> | 작업 | Microsoft.보안인사이트/인시던트/관계/쓰기 | 인시던트와 관련 리소스 간의 관계 업데이트 |
> | 작업 | Microsoft.보안인사이트/인시던트/관계/삭제 | 인시던트와 관련 리소스 간의 관계를 삭제합니다. |
> |  | **설정** |  |
> | 작업 | 마이크로소프트.시큐리티인사이트/설정/읽기 | 설정 가져옵니다. |
> | 작업 | 마이크로소프트.시큐리티인사이트/설정/쓰기 | 업데이트 설정 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

Azure 서비스: [서비스 버스](../service-bus/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ServiceBus/checkNamespaceAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailability를 대신 사용하세요. |
> | 작업 | Microsoft.ServiceBus/checkNameAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. |
> | 작업 | Microsoft.ServiceBus/register/action | ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다. |
> | 작업 | Microsoft.ServiceBus/unregister/action | ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 지정된 VNet에 대한 ServiceBus 리소스 공급자의 VNet 규칙을 삭제합니다. |
> |  | **namespaces** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/migrate/action | 네임스페이스 작업을 마이그레이션합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS 네임스페이스를 제거합니다. |
> |  | **네임스페이스/권한 부여규칙** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 작업 | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **네임스페이스/재해 복구 구성** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 네임스페이스와 연결된 재해 복구 구성을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다. |
> |  | **네임스페이스/재해복구구성/권한 부여규칙** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다. |
> |  | **네임스페이스/이벤트그리드필터** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 네임스페이스와 연결된 Event Grid 필터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 네임스페이스와 연결된 Event Grid 필터를 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 네임스페이스와 연결된 Event Grid 필터를 삭제합니다. |
> |  | **namespaces/eventhubs** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/eventhubs/read | EventHub 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/ipFilterRules** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/ipFilterRules/read | IP 필터 리소스 가져오기 |
> | 작업 | Microsoft.ServiceBus/namespaces/ipFilterRules/write | IP 필터 리소스 만들기 |
> | 작업 | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | IP 필터 리소스 삭제 |
> |  | **네임스페이스/메시징플랜** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/messagingPlan/read | 네임스페이스에 대한 메시지 계획을 가져옵니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/messagingPlan/write | 네임스페이스에 대한 메시지 계획을 업데이트합니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> |  | **네임스페이스/마이그레이션구성** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 마이그레이션 구성을 만들거나 업데이트합니다. 이 작업을 수행하면 표준에서 프리미엄 네임스페이스로의 리소스 동기화가 시작됩니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 마이그레이션 및 보류 중인 복제 작업 상태를 나타내는 마이그레이션 구성을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 마이그레이션 구성을 삭제합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 표준을 프리미엄 네임스페이스 마이그레이션으로 되돌립니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 표준 네임스페이스와 연결된 DNS를 마이그레이션을 완료하고 표준에서 프리미엄 네임스페이스로의 리소스 동기화를 중지하는 프리미엄 네임스페이스에 할당합니다. |
> |  | **네임스페이스/네트워크 규칙 집합** |  |
> | 작업 | Microsoft.ServiceBus/네임스페이스/네트워크 규칙 집합/읽기 | 네트워크규칙 집합 리소스 를 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/네임스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.ServiceBus/네임스페이스/네트워크 규칙 집합/삭제 | VNET 규칙 리소스 삭제 |
> |  | **네임스페이스/네트워크 규칙 집합** |  |
> | 작업 | Microsoft.ServiceBus/네임스페이스/네트워크 규칙 집합/읽기 | 네트워크규칙 집합 리소스 를 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/네임스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.ServiceBus/네임스페이스/네트워크 규칙 집합/삭제 | VNET 규칙 리소스 삭제 |
> |  | **네임스페이스/작업 결과** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/operationresults/read | 네임스페이스 작업의 상태를 가져옵니다. |
> |  | **네임스페이스/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | Microsoft.ServiceBus/네임스페이스/프라이빗엔드포인트커넥션프록시/유효성 검사/작업 | 개인 엔드포인트 연결 프록시 유효성 검사 |
> | 작업 | 마이크로소프트.ServiceBus/네임스페이스/프라이빗엔드포인트커넥션프록시/읽기 | 개인 엔드포인트 연결 프록시 받기 |
> | 작업 | 마이크로소프트.ServiceBus/네임스페이스/프라이빗엔드포인트커넥션프록시/쓰기 | 프라이빗 엔드포인트 연결 프록시 작성 |
> | 작업 | 마이크로소프트.ServiceBus/네임스페이스/프라이빗엔드포인트커넥션프록시/삭제 | 개인 끝점 연결 프록시 삭제 |
> |  | **네임스페이스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/공급자/Microsoft.Insights/logDEFINITIONs** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 네임스페이스 로그 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> |  | **namespaces/queues** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/write | 큐 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/read | 큐 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/Delete | 큐 리소스 삭제 작업 |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 큐를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> |  | **네임스페이스/큐/권한 부여규칙** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 큐 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  큐 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 큐 권한 부여 규칙 삭제 작업 |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 큐의 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **namespaces/topics** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/write | 토픽 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/read | 토픽 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/Delete | 토픽 리소스 삭제 작업 |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 토픽을 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> |  | **네임스페이스/토픽/권한 부여규칙** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 토픽 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  토픽 권한 부여 규칙 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 토픽 권한 부여 규칙 삭제 작업 |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 토픽의 연결 문자열을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> |  | **namespaces/topics/subscriptions** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | TopicSubscription 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription 리소스 삭제 작업 |
> |  | **namespaces/topics/subscriptions/rules** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 규칙 속성을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 규칙 리소스 설명의 목록을 가져옵니다. |
> | 작업 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 규칙 리소스 삭제 작업 |
> |  | **네임스페이스/가상네트워크규칙** |  |
> | 작업 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | VNET 규칙 리소스 가져오기 |
> | 작업 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | VNET 규칙 리소스 만들기 |
> | 작업 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | VNET 규칙 리소스 삭제 |
> |  | **작업** |  |
> | 작업 | Microsoft.ServiceBus/operations/read | 작업을 가져옵니다. |
> |  | **sku** |  |
> | 작업 | Microsoft.ServiceBus/sku/read | Sku 리소스 설명의 목록을 가져옵니다. |
> |  | **스쿠 /지역** |  |
> | 작업 | Microsoft.ServiceBus/sku/regions/read | SkuRegions 리소스 설명의 목록을 가져옵니다. |
> |  | **네임스페이스/메시지** |  |
> | DataAction | Microsoft.ServiceBus/네임스페이스/메시지/보내기/작업 | 메시지 보내기 |
> | DataAction | Microsoft.ServiceBus/네임스페이스/메시지/수신/작업 | 메시지 받기 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

Azure 서비스: [서비스 패브릭](../service-fabric/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.ServiceFabric/register/action | 작업을 등록합니다. |
> |  | **클러스터** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/read | 클러스터를 읽습니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/write | 클러스터를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/delete | 클러스터를 삭제합니다. |
> |  | **clusters/applications** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/read | 애플리케이션을 읽습니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/write | 애플리케이션을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/delete | 애플리케이션을 삭제합니다. |
> |  | **클러스터/애플리케이션/서비스** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/services/read | 서비스를 읽습니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/services/write | 서비스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/services/delete | 서비스를 삭제합니다. |
> |  | **클러스터/애플리케이션/서비스/파티션** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 파티션을 읽습니다. |
> |  | **클러스터/응용 프로그램/서비스/파티션/복제본** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 복제본을 읽습니다. |
> |  | **클러스터/애플리케이션/서비스/상태** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 서비스 상태를 읽습니다. |
> |  | **클러스터/응용 프로그램유형** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applicationTypes/read | 애플리케이션 유형을 읽습니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applicationTypes/write | 애플리케이션 유형을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 애플리케이션 유형을 삭제합니다. |
> |  | **클러스터/응용 프로그램유형/버전** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 애플리케이션 유형 버전을 읽습니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 애플리케이션 유형 버전을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 애플리케이션 유형 버전을 삭제합니다. |
> |  | **클러스터/노드** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/nodes/read | 노드를 읽습니다. |
> |  | **클러스터/상태** |  |
> | 작업 | Microsoft.ServiceFabric/clusters/statuses/read | 클러스터 상태를 읽습니다. |
> |  | **위치/클러스터버전** |  |
> | 작업 | Microsoft.ServiceFabric/locations/clusterVersions/read | 클러스터 버전을 읽습니다. |
> |  | **위치/환경/클러스터버전** |  |
> | 작업 | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 특정 환경에 대한 클러스터 버전을 읽습니다. |
> |  | **위치/작업 결과** |  |
> | 작업 | Microsoft.ServiceFabric/locations/operationresults/read | 작업 결과를 읽습니다. |
> |  | **위치/작업** |  |
> | 작업 | Microsoft.ServiceFabric/locations/operations/read | 위치별로 작업을 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.ServiceFabric/operations/read | 사용 가능한 작업을 읽습니다. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

Azure 서비스: [Azure 신호R 서비스](../azure-signalr/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.SignalRService/register/action | ‘Microsoft.SignalRService’ 리소스 공급자를 구독에 등록합니다. |
> | 작업 | Microsoft.SignalRService/unregister/action | ‘Microsoft.SignalRService’ 리소스 공급자를 구독에서 등록 취소합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.SignalRService/locations/checknameavailability/action | 새 SignalR 서비스에서 이름을 사용할 수 있는지 확인합니다. |
> |  | **위치/작업 결과/신호기** |  |
> | 작업 | Microsoft.SignalRService/locations/operationresults/signalr/read | 위치 기반 비동기 작업의 결과를 쿼리합니다. |
> |  | **위치/작업상태/운영ID** |  |
> | 작업 | 마이크로 소프트.SignalRService / 위치 / 작업상태 / 운영ID / 읽기 | 위치 기반 비동기 작업의 상태를 쿼리합니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.SignalRService/locations/usages/read | Azure SignalR 서비스에 대한 할당량 사용량을 가져옵니다. |
> |  | **작업 결과** |  |
> | 작업 | Microsoft.SignalRService/operationresults/read | 공급자 수준 비동기 작업의 결과를 쿼리합니다. |
> |  | **작업** |  |
> | 작업 | 마이크로 소프트.시그널서비스/운영/읽기 | Azure SignalR 서비스에 대한 작업을 나열합니다. |
> |  | **작업 상태** |  |
> | 작업 | 마이크로 소프트.시그널서비스/운영 상태/읽기 | 공급자 수준 비동기 작업의 상태를 쿼리합니다. |
> |  | **시그널러** |  |
> | 작업 | Microsoft.SignalRService/SignalR/read | 관리 포털 또는 API를 통해 SignalR의 설정과 구성을 확인합니다. |
> | 작업 | Microsoft.SignalRService/SignalR/write | 관리 포털 또는 API를 통해 SignalR의 설정과 구성을 수정합니다. |
> | 작업 | Microsoft.SignalRService/SignalR/delete | 전체 SignalR 서비스를 삭제합니다. |
> | 작업 | Microsoft.SignalRService/SignalR/listkeys/action | 관리 포털에서 또는 API를 통해 SignalR 액세스 키 값을 봅니다. |
> | 작업 | Microsoft.SignalRService/SignalR/regeneratekey/action | 관리 포털에서 또는 API를 통해 SignalR 액세스 키 값을 변경합니다. |
> | 작업 | Microsoft.SignalRService/SignalR/restart/action | 관리 포털 또는 API를 통해 Azure SignalR 서비스를 다시 시작합니다. 가동 중지 시간이 있을 것입니다. |
> |  | **시그널R/이벤트그리드필터** |  |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/이벤트그리드필터/읽기 | 지정된 이벤트 그리드 필터의 속성을 얻거나 지정된 SignalR에 대한 모든 이벤트 그리드 필터를 나열합니다. |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/이벤트그리드필터/쓰기 | 지정된 매개 변수를 사용하여 SignalR에 대한 이벤트 그리드 필터를 만들거나 업데이트합니다. |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/이벤트그리드필터/삭제 | SignalR에서 이벤트 그리드 필터를 삭제합니다. |
> |  | **시그널R/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.시그널서비스/시그널R/프라이빗엔드포인트커넥션프록시/유효성 검사/조치 | 개인 엔드포인트 연결 프록시 유효성 검사 |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/프라이빗엔드포인트커넥션프록시/쓰기 | 전용 끝점 연결 프록시 만들기 |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/프라이빗엔드포인트커넥션프록시/읽기 | 개인 엔드포인트 조작 프록시 읽기 |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/프라이빗엔드포인트커넥션프록시/삭제 | 개인 끝점 연결 프록시 삭제 |
> |  | **시그널R/프라이빗 엔드포인트커넥션** |  |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/프라이빗엔드포인트연결/쓰기 | 개인 끝점 연결 승인 또는 거부 |
> | 작업 | 마이크로 소프트.시그널서비스/시그널러/프라이빗엔드포인트연결/읽기 | 개인 끝점 연결 읽기 |
> |  | **시그널R/프라이빗링크리소스** |  |
> | 작업 | 마이크로 소프트.시그널서비스/시그널R/프라이빗링크리소/읽기 | 모든 SignalR 개인 링크 리소스 나열 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

Azure 서비스: [Azure 관리 응용 프로그램](../azure-resource-manager/managed-applications/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Solutions/register/action | 솔루션에 등록합니다. |
> | 작업 | Microsoft.솔루션/등록 취소/작업 | 솔루션에서 등록을 취소합니다. |
> |  | **응용 프로그램 정의** |  |
> | 작업 | Microsoft.Solutions/applicationDefinitions/read | 애플리케이션 정의 목록을 검색합니다. |
> | 작업 | Microsoft.Solutions/applicationDefinitions/write | 애플리케이션 정의를 추가 또는 수정합니다. |
> | 작업 | Microsoft.Solutions/applicationDefinitions/delete | 애플리케이션 정의를 제거합니다. |
> |  | **응용 프로그램정의/응용 프로그램 아티팩트** |  |
> | 작업 | 마이크로 소프트.솔루션 / 응용 프로그램정의 / 응용 프로그램 아티팩트 / 읽기 | 응용 프로그램 정의의 응용 프로그램 아티팩트를 나열합니다. |
> |  | **응용 프로그램** |  |
> | 작업 | Microsoft.Solutions/applications/read | 애플리케이션 목록을 검색합니다. |
> | 작업 | Microsoft.Solutions/applications/write | 애플리케이션을 만듭니다. |
> | 작업 | Microsoft.Solutions/applications/delete | 애플리케이션을 제거합니다. |
> | 작업 | Microsoft.솔루션/응용 프로그램/새로 고침권한/작업 | 응용 프로그램 권한을 새로 고칩니다. |
> | 작업 | Microsoft.솔루션/응용 프로그램/업데이트액세스/작업 | 응용 프로그램 액세스를 업데이트합니다. |
> |  | **응용 프로그램/응용 프로그램 아티팩트** |  |
> | 작업 | 마이크로 소프트.솔루션 / 응용 프로그램 / 응용 프로그램 유물 / 읽기 | 응용 프로그램 아티팩트를 나열합니다. |
> |  | **jitRequests** |  |
> | 작업 | Microsoft.Solutions/jitRequests/read | JitRequests 목록을 검색합니다. |
> | 작업 | Microsoft.Solutions/jitRequests/write | JitRequest를 만듭니다. |
> | 작업 | Microsoft.Solutions/jitRequests/delete | JitRequest를 제거합니다. |
> |  | **위치/작업상태** |  |
> | 작업 | Microsoft.Solutions/locations/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.솔루션/운영/읽기 | 작업 목록을 가져옵니다. |

## <a name="microsoftsql"></a>Microsoft.Sql

Azure 서비스: [Azure SQL 데이터베이스,](../sql-database/index.yml) [SQL 데이터 웨어하우스](../synapse-analytics/sql-data-warehouse/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Sql/checkNameAvailability/action | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Sql/register/action | Microsoft SQL Database 리소스 공급자에 대한 구독을 등록하고 Microsoft SQL Databases를 만들도록 설정합니다. |
> | 작업 | Microsoft.Sql/unregister/action | Microsoft SQL Database 리소스 공급자에 대한 구독을 등록 취소하고 Microsoft SQL Databases를 만들도록 설정합니다. |
> | 작업 | 마이크로소프트.Sql/개인엔드포인트연결승인/액션 | 사용자가 개인 끝점 연결을 승인할 수 있는지 확인합니다. |
> |  | **인스턴스풀** |  |
> | 작업 | Microsoft.Sql/instancePools/read | 인스턴스 풀을 가져옵니다. |
> | 작업 | Microsoft.Sql/instancePools/write | 인스턴스 풀을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/instancePools/delete | 인스턴스 풀을 삭제합니다. |
> |  | **인스턴스풀/사용** |  |
> | 작업 | Microsoft.Sql/인스턴스풀/사용/읽기 | 인스턴스 풀의 사용 정보를 가져옵니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.Sql/locations/read | 지정된 구독에 사용 가능한 위치를 가져옵니다. |
> |  | **위치/감사설정AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> |  | **위치/감사설정작업결과** |  |
> | 작업 | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> |  | **위치/기능** |  |
> | 작업 | Microsoft.Sql/locations/capabilities/read | 지정된 위치에서 이 구독에 대한 기능을 가져옵니다. |
> |  | **위치/데이터베이스AzureAsync작업** |  |
> | 작업 | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 데이터베이스 작업의 상태를 가져옵니다. |
> |  | **위치/데이터베이스작업결과** |  |
> | 작업 | Microsoft.Sql/locations/databaseOperationResults/read | 데이터베이스 작업의 상태를 가져옵니다. |
> |  | **위치/삭제된서버Async작업** |  |
> | 작업 | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 삭제된 서버에서 진행 중인 작업을 가져옵니다. |
> |  | **위치/삭제된 서버작업결과** |  |
> | 작업 | Microsoft.Sql/locations/deletedServerOperationResults/read | 삭제된 서버에서 진행 중인 작업을 가져옵니다. |
> |  | **위치/삭제된 서버** |  |
> | 작업 | Microsoft.Sql/locations/deletedServers/read | 삭제된 서버 목록을 반환하거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/locations/deletedServers/recover/action | 삭제된 서버를 복구합니다. |
> |  | **위치/탄성풀AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 탄력적 풀 비동기 작업에 대한 azure 비동기 작업을 가져옵니다. |
> |  | **위치/탄력적풀작업결과** |  |
> | 작업 | Microsoft.Sql/locations/elasticPoolOperationResults/read | 탄력적 풀 작업의 결과를 가져옵니다. |
> |  | **위치/암호화보호서AzureAsyncOperation** |  |
> | 작업 | 마이크로소프트.Sql/위치/암호화보호서AzureAsync작업/읽기 | 투명한 데이터 암호화 암호화 보호기에서 진행 중인 작업 가져옵니다. |
> |  | **위치/암호화보호기작업결과** |  |
> | 작업 | Microsoft.Sql/위치/암호화보호기작업결과/읽기 | 투명한 데이터 암호화 암호화 보호기에서 진행 중인 작업 가져옵니다. |
> |  | **위치/확장감사설정AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> |  | **위치/확장감사설정작업결과** |  |
> | 작업 | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> |  | **위치/방화벽규칙AzureAsync작업** |  |
> | 작업 | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 방화벽 규칙 작업의 상태를 가져옵니다. |
> |  | **위치/방화벽규칙운영결과** |  |
> | 작업 | Microsoft.Sql/locations/firewallRulesOperationResults/read | 방화벽 규칙 작업의 상태를 가져옵니다. |
> |  | **locations/instanceFailoverGroups** |  |
> | 작업 | Microsoft.Sql/locations/instanceFailoverGroups/read | 인스턴스 장애 조치(failover) 그룹 목록을 반환하거나 지정된 인스턴스 장애 조치(failover) 그룹의 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/locations/instanceFailoverGroups/write | 지정된 매개 변수를 사용 하 여 인스턴스 장애 조치 그룹을 만들거나 지정된 인스턴스 장애 조치 그룹에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 작업 | Microsoft.Sql/locations/instanceFailoverGroups/delete | 기존 인스턴스 장애 조치(failover) 그룹을 삭제합니다. |
> | 작업 | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 기존 인스턴스 장애 조치(failover) 그룹에서 계획된 장애 조치(failover)를 실행합니다. |
> | 작업 | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 기존 인스턴스 장애 조치(failover) 그룹에서 강제 장애 조치(failover)를 실행합니다. |
> |  | **위치/인스턴스풀AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 인스턴스 풀 작업의 상태를 가져옵니다. |
> |  | **위치/인스턴스풀작업결과** |  |
> | 작업 | Microsoft.Sql/locations/instancePoolOperationResults/read | 인스턴스 풀 작업에 대한 결과를 가져옵니다. |
> |  | **위치/인터페이스EndpointProfileAzureAsync작업** |  |
> | 작업 | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 특정 인터페이스 엔드포인트 Azure 비동기 작업의 세부 정보를 반환합니다. |
> |  | **위치/인터페이스EndpointProfile작업결과** |  |
> | 작업 | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 지정된 인터페이스 엔드포인트 프로필 작업의 세부 정보를 반환합니다. |
> |  | **위치/작업에이전트AzureAsync작업** |  |
> | 작업 | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 작업 에이전트 작업의 상태를 가져옵니다. |
> |  | **위치/작업에이전트작업결과** |  |
> | 작업 | Microsoft.Sql/locations/jobAgentOperationResults/read | 작업 에이전트 작업의 결과를 가져옵니다. |
> |  | **위치/장기 보존 백업** |  |
> | 작업 | Microsoft.Sql/locations/longTermRetentionBackups/read | 위치에 있는 모든 서버의 모든 데이터베이스에 대한 장기 보존 백업을 나열합니다. |
> |  | **위치/장기 보존관리관리인스턴스백업** |  |
> | 작업 | Microsoft.Sql/위치/장기 보존관리인스턴스백업/읽기 | 특정 위치에 대한 관리되는 인스턴스 LTR 백업 목록을 반환합니다.  |
> |  | **위치/장기 보존관리사례/장기 보존 데이터베이스/장기 보존관리관리인스턴스백업** |  |
> | 작업 | Microsoft.Sql/위치/장기 보존 관리 사례/장기 보존 데이터베이스/장기 보존 관리 인스턴스 백업/읽기 | 관리되는 인스턴스 데이터베이스에 대한 LTR 백업 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/위치/장기 보존 관리 사례/장기 보존 데이터베이스/장기 보존 관리 인스턴스 백업/삭제 | 관리되는 인스턴스 데이터베이스에 대한 LTR 백업 삭제 |
> |  | **위치/장기 보존관리사례/장기보존관리관리소백업** |  |
> | 작업 | Microsoft.Sql/위치/장기 보존관리사례/장기 보존관리관리백업/읽기 | 특정 관리 되는 인스턴스에 대 한 관리 되는 인스턴스 LTR 백업 목록을 반환 합니다. |
> |  | **위치/장기 보존 서버/장기 보존 백업** |  |
> | 작업 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 서버의 모든 데이터베이스에 대한 장기 보존 백업을 나열합니다. |
> |  | **위치/장기 보존 서버/장기 보존 데이터베이스/장기 보존 백업** |  |
> | 작업 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 데이터베이스의 장기 보존 백업을 나열합니다. |
> | 작업 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 장기 보존 백업을 삭제합니다. |
> |  | **위치/관리형데이터베이스복원AzureAsync작업** |  |
> | 작업 | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 관리되는 데이터베이스 복원 작업을 완료합니다. |
> |  | **위치/관리인스턴스암호화보호기AzureAsyncOperation** |  |
> | 작업 | 마이크로소프트.Sql/위치/관리 인스턴스암호화 보호자AzureAsync작업/읽기 | 투명한 데이터 암호화 관리형 인스턴스 암호화 보호기에서 진행 중인 작업 가져옵니다. |
> |  | **위치/관리인스턴스암호화보호기작업결과** |  |
> | 작업 | Microsoft.Sql/위치/관리인스턴스암호화보호기작업결과/읽기 | 투명한 데이터 암호화 관리형 인스턴스 암호화 보호기에서 진행 중인 작업 가져옵니다. |
> |  | **위치/관리인스턴스키AzureAsync작업** |  |
> | 작업 | 마이크로소프트.Sql/위치/관리인스턴스키AzureAsync작업/읽기 | 투명한 데이터 암호화 관리 형 인스턴스 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/관리 인스턴스키작업결과** |  |
> | 작업 | 마이크로소프트.Sql/위치/관리 인스턴스키작업결과/읽기 | 투명한 데이터 암호화 관리 형 인스턴스 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/관리인스턴스장기보존정책AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/위치/관리인스턴스장기 보존 정책AzureAsync작업/읽기 | 관리되는 데이터베이스에 대한 장기 보존 정책 작업의 상태를 가져옵니다. |
> |  | **위치/관리인스턴스장기보존정책작업결과** |  |
> | 작업 | Microsoft.Sql/위치/관리인스턴스장기 보존 정책작업결과/읽기 | 관리되는 데이터베이스에 대한 장기 보존 정책 작업의 상태를 가져옵니다. |
> |  | **위치/관리단기보존정책운영결과** |  |
> | 작업 | Microsoft.Sql/위치/관리되는 단기 보존 정책작업결과/읽기 | 단기 보존 정책 작업의 상태를 가져옵니다. |
> |  | **위치/관리투명데이터암호화AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 관리되는 데이터베이스 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다. |
> |  | **위치/관리투명데이터 암호화작업결과** |  |
> | 작업 | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 관리되는 데이터베이스 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다. |
> |  | **위치/프라이빗엔드포인트커넥스애싱크오Operation** |  |
> | 작업 | 마이크로소프트.Sql/위치/개인엔드포인트연결AzureAsyncOperation/읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/프라이빗엔드포인트연결작업결과** |  |
> | 작업 | 마이크로소프트.Sql/위치/개인엔드포인트연결작업결과/읽기 | 개인 끝점 연결 작업에 대한 결과 가져옵니다. |
> |  | **위치/개인엔드포인트연결프록시A싱크오Operation** |  |
> | 작업 | 마이크로소프트.Sql/위치/개인엔드포인트연결프록시A싱크작업/읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/프라이빗엔드포인트커넥션프록시작업결과** |  |
> | 작업 | 마이크로소프트.Sql/위치/개인엔드포인트연결프록시작업결과/읽기 | 개인 끝점 연결 프록시 작업에 대한 결과를 가져옵니다. |
> |  | **위치/서버관리자AzureAsync작업** |  |
> | 작업 | 마이크로소프트.Sql/위치/서버관리자AzureAsync작업/읽기 | 서버 Azure Active Directory 관리자 비동기 작업 결과 |
> |  | **위치/서버관리자작업결과** |  |
> | 작업 | Microsoft.Sql/위치/서버관리자작업결과/읽기 | 서버 Azure Active Directory 관리자 작업 결과 |
> |  | **위치/서버키AzureAsync작업** |  |
> | 작업 | 마이크로소프트.Sql/위치/서버키AzureAsync작업/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/서버키작업결과** |  |
> | 작업 | 마이크로소프트.Sql/위치/서버키작업결과/읽기 | 투명한 데이터 암호화 서버 키에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/단기 보존 정책작업결과** |  |
> | 작업 | Microsoft.Sql/위치/단기 보존 정책작업결과/읽기 | 단기 보존 정책 작업의 상태를 가져옵니다. |
> |  | **위치/동기화에이전트작업결과** |  |
> | 작업 | Microsoft.Sql/locations/syncAgentOperationResults/read | 동기화 에이전트 리소스 작업의 결과를 검색합니다. |
> |  | **위치/동기화데이터베이스ID** |  |
> | 작업 | Microsoft.Sql/locations/syncDatabaseIds/read | 특정 지역 및 구독에 대한 동기화 데이터베이스 id를 검색합니다. |
> |  | **위치/동기화그룹작업결과** |  |
> | 작업 | Microsoft.Sql/locations/syncGroupOperationResults/read | 동기화 그룹 리소스 작업의 결과를 검색합니다. |
> |  | **위치/동기화멤버작업결과** |  |
> | 작업 | Microsoft.Sql/locations/syncMemberOperationResults/read | 동기화 멤버 리소스 작업의 결과를 검색합니다. |
> |  | **위치/시간대** |  |
> | 작업 | 마이크로소프트.Sql/위치/시간대/읽기 | 위치별로 관리되는 인스턴스 표준 시간대 목록을 반환합니다. |
> |  | **위치/투명데이터 암호화AzureAsync작업** |  |
> | 작업 | 마이크로소프트.Sql/위치/투명데이터 암호화AzureAsync작업/읽기 | 논리 데이터베이스 투명 데이터 암호화에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/투명데이터 암호화작업결과** |  |
> | 작업 | 마이크로소프트.Sql/위치/투명 데이터 암호화작업결과/읽기 | 논리 데이터베이스 투명 데이터 암호화에 대한 진행 중인 작업 가져옵니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.Sql/locations/usages/read | 한 위치에서 이 구독의 사용량 메트릭 컬렉션을 가져옵니다. |
> |  | **위치/가상네트워크규칙AzureAsyncOperation** |  |
> | 작업 | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 지정된 가상 네트워크 규칙 azure 비동기 작업의 세부 정보를 반환합니다.  |
> |  | **위치/가상 네트워크규칙작업결과** |  |
> | 작업 | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 지정된 가상 네트워크 규칙 작업의 세부 정보를 반환합니다.  |
> |  | **managedInstances** |  |
> | 작업 | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/managedInstances/read | 관리되는 인스턴스 목록을 반환하거나 지정된 관리되는 인스턴스에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/write | 지정된 매개 변수를 사용하여 관리되는 인스턴스를 만들거나 지정된 관리되는 인스턴스에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/managedInstances/delete | 기존 관리되는 인스턴스를 삭제합니다. |
> |  | **관리되는 인스턴스/관리자** |  |
> | 작업 | Microsoft.Sql/managedInstances/administrators/read | 관리되는 인스턴스 관리자 목록을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/administrators/write | 지정된 매개 변수를 사용하여 관리되는 인스턴스 관리자를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/managedInstances/administrators/delete | 관리되는 인스턴스의 기존 관리자를 삭제합니다. |
> |  | **관리되는 인스턴스/데이터베이스** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/read | 기존 관리되는 데이터베이스를 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/delete | 기존 관리되는 데이터베이스를 삭제합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/write | 새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다. |
> | 작업 | Microsoft.Sql/관리되는 인스턴스/데이터베이스/완전 복원/작업 | 관리되는 데이터베이스 복원 작업을 완료합니다. |
> |  | **관리 인스턴스/데이터베이스/백업LongTerm 보존 정책** |  |
> | 작업 | Microsoft.Sql/관리 형 사례/데이터베이스/백업장기 보존 정책/쓰기 | 관리되는 데이터베이스에 대한 장기 보존 정책 업데이트 |
> | 작업 | Microsoft.Sql/관리 형 사례/데이터베이스/백업장기 보존 정책/읽기 | 관리되는 데이터베이스에 대한 장기 보존 정책 가져옵니다. |
> |  | **managedInstances/databases/backupShortTermRetentionPolicies** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 관리되는 데이터베이스에 대한 단기 보존 정책을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 관리되는 데이터베이스에 대한 단기 보존 정책을 업데이트합니다. |
> |  | **관리인스턴스/데이터베이스/열** |  |
> | 작업 | Microsoft.Sql/관리형 인스턴스/데이터베이스/열/읽기 | 관리되는 데이터베이스에 대한 열 목록 반환 |
> |  | **관리 인스턴스/데이터베이스/현재감도 레이블** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 작업 | Microsoft.Sql/관리 형 사례/데이터베이스/현재감도레이블/쓰기 | 일괄 처리 업데이트 민감도 레이블 |
> |  | **관리인스턴스/데이터베이스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **관리인스턴스/데이터베이스/공급자/Microsoft.Insights/logDefinitions** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 관리형 인스턴스 데이터베이스에 사용 가능한 로그를 가져옵니다. |
> |  | **관리 인스턴스/데이터베이스/권장감도 레이블** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 작업 | Microsoft.Sql/관리형 인스턴스/데이터베이스/권장감도 레이블/쓰기 | 일괄 업데이트 권장 민감도 레이블 |
> |  | **관리되는 인스턴스/데이터베이스/복원세부정보** |  |
> | 작업 | Microsoft.Sql/관리되는 인스턴스/데이터베이스/복원세부정보/읽기 | 복원이 진행되는 동안 관리되는 데이터베이스 복원 세부 정보를 반환합니다. |
> |  | **관리 인스턴스/데이터베이스/스키마** |  |
> | 작업 | Microsoft.Sql/관리 되는 인스턴스/데이터베이스/스키마/읽기 | 관리되는 데이터베이스 스키마를 가져옵니다. |
> |  | **관리 인스턴스/데이터베이스/스키마/테이블** |  |
> | 작업 | Microsoft.Sql/관리 인스턴스/데이터베이스/스키마/테이블/읽기 | 관리되는 데이터베이스 테이블 받기 |
> |  | **관리 인스턴스/데이터베이스/스키마/테이블/열** |  |
> | 작업 | Microsoft.Sql/관리형 인스턴스/데이터베이스/스키마/테이블/열/읽기 | 관리되는 데이터베이스 열 받기 |
> |  | **managedInstances/databases/schemas/tables/columns/sensitivityLabels** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 지정된 열의 민감도 레이블을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 지정된 열의 민감도 레이블을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 지정된 열의 민감도 레이블을 삭제합니다. |
> | 작업 | Microsoft.Sql/managedInstances/데이터베이스/스키마/테이블/열/민감도레이블/비활성화/작업 | 지정된 열에서 민감도 권장 사항 사용 안 함 |
> | 작업 | Microsoft.Sql/managedInstances/데이터베이스/스키마/테이블/열/민감도레이블/사용/작업 | 지정된 열에 대한 민감도 권장 사항 사용 |
> |  | **관리 인스턴스/데이터베이스/보안경고정책** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 지정된 관리되는 데이터베이스에 대한 데이터베이스 위협 검색 정책을 변경합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 지정된 서버에 대해 구성된 관리되는 데이터베이스 위협 탐지 정책 목록 검색 |
> |  | **관리 인스턴스/데이터베이스/보안이벤트** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/securityEvents/read | 관리되는 데이터베이스 보안 이벤트를 검색합니다. |
> |  | **관리 인스턴스/데이터베이스/감도레이블** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> |  | **관리인스턴스/데이터베이스/투명데이터 암호화** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 지정된 관리되는 데이터베이스에서 데이터베이스 투명한 데이터 암호화의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 지정된 관리되는 데이터베이스에 대한 데이터베이스 투명한 데이터 암호화를 변경합니다. |
> |  | **managedInstances/databases/vulnerabilityAssessments** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 지정된 데이터베이스에 대한 취약성 평가를 변경합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 지정된 데이터베이스에 대한 취약성 평가를 제거합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | givendatabase에서 취약성 평가 정책을 검색합니다. |
> |  | **managedInstances/databases/vulnerabilityAssessments/rules/baselines** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 제거합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 변경합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 가져옵니다. |
> |  | **관리인스턴스/데이터베이스/취약점평가/검사** |  |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 취약점 평가 데이터베이스 검사를 실행합니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 기존 검사 결과를 사람이 읽을 수 있는 형식으로 변환합니다. 이미 있는 경우 아무 일도 발생하지 않습니다. |
> | 작업 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 데이터베이스 취약성 평가 검사 레코드 목록을 반환하거나 지정된 검사 ID에 대한 검사 레코드를 가져옵니다. |
> |  | **managedInstances/encryptionProtector** |  |
> | 작업 | Microsoft.Sql/관리 사례/암호화 보호/유효성 검사/작업 | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> | 작업 | Microsoft.Sql/managedInstances/encryptionProtector/read | 서버 암호화 보호기 목록을 반환하거나 지정된 서버 암호화 보호기에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/encryptionProtector/write | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> |  | **관리 인스턴스/액세스할 수 없는 관리 데이터베이스** |  |
> | 작업 | Microsoft.Sql/관리형/액세스할 수 없는 관리데이터베이스/읽기 | 관리되는 인스턴스에서 액세스할 수 없는 관리되는 데이터베이스 목록을 가져옵니다. |
> |  | **managedInstances/keys** |  |
> | 작업 | Microsoft.Sql/managedInstances/keys/read | 관리되는 인스턴스 키 목록을 반환하거나 지정된 관리되는 인스턴스 키의 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 관리되는 인스턴스 키의 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/managedInstances/keys/delete | 기존 Azure SQL Managed Instance 키를 삭제합니다. |
> |  | **관리되는 인스턴스/메트릭정의** |  |
> | 작업 | Microsoft.Sql/managedInstances/metricDefinitions/read | 관리되는 인스턴스 메트릭 정의 가져오기 |
> |  | **관리되는 인스턴스/메트릭** |  |
> | 작업 | Microsoft.Sql/managedInstances/metrics/read | 관리되는 인스턴스 메트릭 가져오기 |
> |  | **관리 인스턴스/연산** |  |
> | 작업 | Microsoft.Sql/관리 인스턴스/작업/읽기 | 관리형 인스턴스 작업 받기 |
> | 작업 | Microsoft.Sql/관리 인스턴스/작업/취소/작업 | 아직 완료되지 않은 비동기 작업이 보류 중인 Azure SQL 관리 인스턴스를 취소합니다. |
> |  | **관리인스턴스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **관리 인스턴스/공급자/Microsoft.Insights/로그정의** |  |
> | 작업 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 관리형 인스턴스에 사용 가능한 로그를 가져옵니다. |
> |  | **관리 인스턴스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 관리되는 인스턴스에 사용 가능한 메트릭 유형을 반환합니다. |
> |  | **관리 인스턴스/복구 가능한 데이터베이스** |  |
> | 작업 | Microsoft.Sql/managedInstances/recoverableDatabases/read | 복구 가능한 관리되는 데이터베이스 목록을 반환합니다. |
> |  | **관리 인스턴스/복원 가능한 데이터베이스** |  |
> | 작업 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 복원 가능한 삭제된 관리되는 데이터베이스 목록을 반환합니다. |
> |  | **managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies** |  |
> | 작업 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 삭제된 관리되는 데이터베이스에 대한 단기 보존 정책을 가져옵니다. |
> | 작업 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 삭제된 관리되는 데이터베이스에 대한 단기 보존 정책을 업데이트합니다. |
> |  | **관리 인스턴스/보안경고 정책** |  |
> | 작업 | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 지정된 관리되는 서버에 대한 관리되는 서버 위협 검색 정책을 변경합니다. |
> | 작업 | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 지정된 서버에 대해 구성된 관리되는 서버 위협 탐지 정책 목록 검색 |
> |  | **managedInstances/vulnerabilityAssessments** |  |
> | 작업 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 주어진 관리되는 인스턴스에 대한 취약성 평가를 변경합니다. |
> | 작업 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 주어진 관리되는 인스턴스에 대한 취약성 평가를 제거합니다. |
> | 작업 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 지정된 관리되는 인스턴스에서 취약성 평가 정책을 검색합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Sql/operations/read | 사용 가능한 REST 작업을 가져옵니다. |
> |  | **서버** |  |
> | 작업 | Microsoft.Sql/servers/tdeCertificates/action | TDE 인증서를 만들거나 업데이트합니다. |
> | 작업 | 마이크로소프트.Sql/서버/비활성화AzureAD전용 인증/작업 | 논리 서버에서 Azure Active Directory만 인증 을 사용하지 않도록 설정 |
> | 작업 | Microsoft.Sql/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/delete | 기존 서버를 삭제합니다. |
> | 작업 | 마이크로소프트.Sql/서버/개인엔드포인트연결승인/액션 | 사용자가 개인 끝점 연결을 승인할 수 있는지 확인합니다. |
> | 작업 | Microsoft.Sql/servers/import/action | 서버에서 새 데이터베이스를 만들고 DacPac 패키지의 스키마 및 데이터를 배포합니다. |
> |  | **서버/관리자작업결과** |  |
> | 작업 | Microsoft.Sql/servers/administratorOperationResults/read | 서버 관리자에 대해 진행 중인 작업을 가져옵니다. |
> |  | **servers/administrators** |  |
> | 작업 | Microsoft.Sql/servers/administrators/read | 특정 Azure Active Directory 관리자 개체 를 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/administrators/write | 특정 Azure Active Directory 관리자 개체 추가 또는 업데이트 |
> | 작업 | Microsoft.Sql/servers/administrators/delete | 특정 Azure Active Directory 관리자 개체 삭제 |
> |  | **servers/advisors** |  |
> | 작업 | Microsoft.Sql/servers/advisors/read | 서버에 사용할 수 있는 Advisor의 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/advisors/write | 서버 수준에서 Advisor의 자동 실행 상태를 업데이트합니다. |
> |  | **서버/고문/권장 작업** |  |
> | 작업 | Microsoft.Sql/servers/advisors/recommendedActions/read | 서버에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/advisors/recommendedActions/write | 서버에 권장 작업을 적용합니다. |
> |  | **서버/감사정책** |  |
> | 작업 | Microsoft.Sql/servers/auditingPolicies/read | 지정된 서버에 구성된 기본 서버 테이블 감사 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/auditingPolicies/write | 지정된 서버에 대한 기본 서버 테이블 감사를 변경합니다. |
> |  | **servers/auditingSettings** |  |
> | 작업 | Microsoft.Sql/servers/auditingSettings/read | 지정된 서버에 구성된 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/auditingSettings/write | 지정된 서버에 대한 서버 Blob 감사를 변경합니다. |
> |  | **서버/감사설정/운영결과** |  |
> | 작업 | Microsoft.Sql/servers/auditingSettings/operationResults/read | 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> |  | **서버/자동 튜닝** |  |
> | 작업 | Microsoft.Sql/servers/automaticTuning/read | 서버에 대한 자동 조정 설정을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/automaticTuning/write | 서버에 대한 자동 조정 설정을 업데이트하고 업데이트된 설정을 반환합니다. |
> |  | **servers/backupLongTermRetentionVaults** |  |
> | 작업 | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | 이 작업은 백업 장기 보존 자격 증명 모음을 가져오는 데 사용됩니다. 서버에 등록된 자격 증명 모음에 대한 정보를 반환합니다. |
> | 작업 | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | 이 작업은 백업 장기 보존 자격 증명 모음을 서버에 등록하는 데 사용됩니다. |
> | 작업 | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 기존 백업 보관 자격 증명 모음을 삭제합니다. |
> |  | **servers/communicationLinks** |  |
> | 작업 | Microsoft.Sql/servers/communicationLinks/read | 지정된 서버의 통신 연결 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/communicationLinks/write | 서버 통신 연결을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/communicationLinks/delete | 기존 서버 통신 연결을 삭제합니다. |
> |  | **servers/connectionPolicies** |  |
> | 작업 | Microsoft.Sql/servers/connectionPolicies/read | 지정된 서버의 서버 연결 정책 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/connectionPolicies/write | 서버 연결 정책을 만들거나 업데이트합니다. |
> |  | **servers/databases** |  |
> | 작업 | Microsoft.Sql/servers/databases/read | 데이터베이스 목록을 가져오거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/write | 지정된 매개 변수를 사용하여 데이터베이스를 만들거나 지정된 데이터베이스에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/databases/delete | 기존 데이터베이스를 삭제합니다. |
> | 작업 | Microsoft.Sql/servers/databases/pause/action | Azure SQL Datawarehouse 데이터베이스를 일시 중지합니다. |
> | 작업 | Microsoft.Sql/servers/databases/resume/action | Azure SQL Datawarehouse 데이터베이스를 다시 시작합니다. |
> | 작업 | Microsoft.Sql/servers/databases/export/action | Azure SQL Database를 내보냅니다. |
> | 작업 | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Azure SQL Datawarehouse 데이터베이스를 업그레이드합니다. |
> | 작업 | Microsoft.Sql/servers/databases/move/action | 기존 데이터베이스의 이름을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/databases/restorePoints/action | 새 복원 지점을 만듭니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/장애 조치/작업 | 고객이 데이터베이스 장애 조치(failover)를 시작했습니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 취약점 평가 데이터베이스 검사를 실행합니다. |
> |  | **servers/databases/advisors** |  |
> | 작업 | Microsoft.Sql/servers/databases/advisors/read | 데이터베이스에 사용할 수 있는 Advisor의 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/advisors/write | 데이터베이스 수준에서 Advisor의 자동 실행 상태를 업데이트합니다. |
> |  | **서버/데이터베이스/고문/권장작업** |  |
> | 작업 | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 데이터베이스에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 데이터베이스에 권장 작업을 적용합니다. |
> |  | **서버/데이터베이스/감사정책** |  |
> | 작업 | Microsoft.Sql/servers/databases/auditingPolicies/read | 지정된 데이터베이스에 구성된 테이블 감사 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/auditingPolicies/write | 지정된 데이터베이스에 대한 테이블 감사 정책을 변경합니다. |
> |  | **servers/databases/auditingSettings** |  |
> | 작업 | Microsoft.Sql/servers/databases/auditingSettings/read | 지정된 데이터베이스에 구성된 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/auditingSettings/write | 지정된 데이터베이스에 대한 Blob 감사 정책을 변경합니다. |
> |  | **서버/데이터베이스/감사기록** |  |
> | 작업 | Microsoft.Sql/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> |  | **서버/데이터베이스/자동 튜닝** |  |
> | 작업 | Microsoft.Sql/servers/databases/automaticTuning/read | 데이터베이스에 대한 자동 조정 설정을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/automaticTuning/write | 데이터베이스에 대한 자동 조정 설정을 업데이트하고 업데이트된 설정을 반환합니다. |
> |  | **서버/데이터베이스/azureAsync작업** |  |
> | 작업 | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 데이터베이스 작업의 상태를 가져옵니다. |
> |  | **servers/databases/backupLongTermRetentionPolicies** |  |
> | 작업 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 데이터베이스 백업 보관 정책을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 지정된 데이터베이스의 백업 보관 정책 목록을 반환합니다. |
> |  | **servers/databases/backupShortTermRetentionPolicies** |  |
> | 작업 | Microsoft.Sql/서버/데이터베이스/백업단기 보존 정책/읽기 | 데이터베이스에 대한 단기 보존 정책 가져옵니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/백업단기보존정책/쓰기 | 데이터베이스에 대한 단기 보존 정책 업데이트 |
> |  | **서버/데이터베이스/열** |  |
> | 작업 | Microsoft.Sql/서버/데이터베이스/열/읽기 | 데이터베이스에 대한 열 목록 반환 |
> |  | **servers/databases/connectionPolicies** |  |
> | 작업 | Microsoft.Sql/servers/databases/connectionPolicies/read | 지정된 데이터베이스에 구성된 연결 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/connectionPolicies/write | 지정된 데이터베이스에 대한 연결 정책을 변경합니다. |
> |  | **서버/데이터베이스/현재감도레이블** |  |
> | 작업 | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/현재감도레이블/쓰기 | 일괄 처리 업데이트 민감도 레이블 |
> |  | **servers/databases/dataMaskingPolicies** |  |
> | 작업 | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 데이터베이스 데이터 마스킹 정책 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 지정된 데이터베이스에 대한 데이터 마스킹 정책을 변경합니다. |
> |  | **servers/databases/dataMaskingPolicies/rules** |  |
> | 작업 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 지정된 데이터베이스에 구성된 데이터 마스킹 정책 규칙의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 삭제합니다. |
> |  | **서버/데이터베이스/데이터웨어쿼리** |  |
> | 작업 | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 선택한 쿼리 ID에 대한 데이터 웨어하우스 배포 쿼리 정보를 반환합니다. |
> |  | **서버/데이터베이스/데이터웨어쿼리/데이터웨어웨어쿼리단계** |  |
> | 작업 | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 선택한 단계 ID에 대한 데이터 웨어하우스 쿼리의 분산 쿼리 단계 정보를 반환합니다. |
> |  | **서버/데이터베이스/데이터웨어웨어사용자활동** |  |
> | 작업 | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 실행 중인 쿼리와 일시 중단된 쿼리를 포함하고 있는 SQL Data Warehouse 인스턴스의 사용자 작업을 검색합니다. |
> |  | **servers/databases/extendedAuditingSettings** |  |
> | 작업 | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 지정된 데이터베이스에 구성된 확장 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 지정된 데이터베이스에 대한 확장 Blob 감사 정책을 변경합니다. |
> |  | **servers/databases/extensions** |  |
> | 작업 | Microsoft.Sql/servers/databases/extensions/read | 데이터베이스에 대한 확장 컬렉션을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/extensions/write | 지정된 데이터베이스에 대한 확장을 변경합니다. |
> |  | **서버/데이터베이스/확장/가져오기확장작업결과** |  |
> | 작업 | Microsoft.Sql/서버/데이터베이스/확장/가져오기확장작업결과/읽기 | 진행 중인 가져오기 작업 가져오기 |
> |  | **servers/databases/geoBackupPolicies** |  |
> | 작업 | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 지정된 데이터베이스에 대한 지역 백업 정책을 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 데이터베이스 지역 백업 정책을 만들거나 업데이트합니다. |
> |  | **서버/데이터베이스/가져오기내보내기작업결과** |  |
> | 작업 | Microsoft.Sql/servers/databases/importExportOperationResults/read | 진행 중인 가져오기/내보내기 작업을 가져옵니다. |
> |  | **서버/데이터베이스/유지 관리창옵션** |  |
> | 작업 | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 선택한 데이터베이스에 사용 가능한 유지 관리 기간 목록을 가져옵니다. |
> |  | **서버/데이터베이스/유지 관리윈도우** |  |
> | 작업 | Microsoft.Sql/servers/databases/maintenanceWindows/read | 선택한 데이터베이스에 대한 유지 관리 기간 설정을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/maintenanceWindows/write | 선택한 데이터베이스에 대한 유지 관리 기간 설정을 지정합니다. |
> |  | **서버/데이터베이스/메트릭정의** |  |
> | 작업 | Microsoft.Sql/servers/databases/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **서버/데이터베이스/메트릭** |  |
> | 작업 | Microsoft.Sql/servers/databases/metrics/read | 데이터베이스에 대한 메트릭을 반환합니다. |
> |  | **서버/데이터베이스/작업결과** |  |
> | 작업 | Microsoft.Sql/servers/databases/operationResults/read | 데이터베이스 작업의 상태를 가져옵니다. |
> |  | **서버/데이터베이스/작업** |  |
> | 작업 | Microsoft.Sql/servers/databases/operations/cancel/action | 아직 완료되지 않은 Azure SQL Database 보류 중인 비동기 작업을 취소합니다. |
> | 작업 | Microsoft.Sql/servers/databases/operations/read | 데이터베이스에서 수행된 작업 목록을 반환합니다. |
> |  | **서버/데이터베이스/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **서버/데이터베이스/공급자/Microsoft.Insights/logDefinitions** |  |
> | 작업 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 데이터베이스에 대해 사용 가능한 로그를 가져옵니다. |
> |  | **서버/데이터베이스/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **서버/데이터베이스/쿼리스토어** |  |
> | 작업 | Microsoft.Sql/servers/databases/queryStore/read | 데이터베이스에 대한 쿼리 저장소 설정의 현재 값을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/queryStore/write | 데이터베이스에 대한 쿼리 저장소 설정을 업데이트합니다. |
> |  | **서버/데이터베이스/쿼리스토어/쿼리텍스트** |  |
> | 작업 | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 지정된 매개 변수에 해당하는 쿼리 텍스트 컬렉션을 반환합니다. |
> |  | **서버/데이터베이스/권장감도레이블** |  |
> | 작업 | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/권장감도레이블/쓰기 | 일괄 업데이트 권장 민감도 레이블 |
> |  | **서버/데이터베이스/복제링크** |  |
> | 작업 | Microsoft.Sql/servers/databases/replicationLinks/read | 복제 링크 목록을 반환하거나 지정된 복제 링크의 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/replicationLinks/delete | 잠재적 데이터 손실이 발생하는 상태로 강제로 복제 관계를 종료합니다. |
> | 작업 | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 주 데이터베이스의 모든 변경 내용을 동기화한 후에 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다. |
> | 작업 | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 잠재적 데이터 손실이 발생하는 상태로 즉시 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다. |
> | 작업 | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 연결에 대한 복제 모드를 동기 또는 비동기 모드로 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 강제로 또는 파트너와 동기화한 후에 복제 관계를 종료합니다. |
> |  | **서버/데이터베이스/복원포인트** |  |
> | 작업 | Microsoft.Sql/servers/databases/restorePoints/read | 데이터베이스의 복원 지점을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/restorePoints/delete | 데이터베이스에 대한 복원 지점을 삭제합니다. |
> |  | **서버/데이터베이스/스키마** |  |
> | 작업 | Microsoft.Sql/servers/databases/schemas/read | 데이터베이스 스키마를 가져옵니다. |
> |  | **서버/데이터베이스/스키마/테이블** |  |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/read | 데이터베이스 테이블을 가져옵니다. |
> |  | **서버/데이터베이스/스키마/테이블/열** |  |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 데이터베이스 열을 가져옵니다. |
> |  | **servers/databases/schemas/tables/columns/sensitivityLabels** |  |
> | 작업 | Microsoft.Sql/서버/데이터베이스/스키마/테이블/열/감도레이블/사용/작업 | 지정된 열에 대한 민감도 권장 사항 사용 |
> | 작업 | Microsoft.Sql/서버/데이터베이스/스키마/테이블/열/감도레이블/비활성화/작업 | 지정된 열에서 민감도 권장 사항 사용 안 함 |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 지정된 열의 민감도 레이블을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 지정된 열의 민감도 레이블을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 지정된 열의 민감도 레이블을 삭제합니다. |
> |  | **서버/데이터베이스/스키마/테이블/권장인덱스** |  |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 데이터베이스에 대한 인덱스 권장 사항 목록을 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 인덱스 권장 사항을 적용합니다. |
> |  | **servers/databases/securityAlertPolicies** |  |
> | 작업 | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 지정된 데이터베이스에 대한 데이터베이스 위협 탐지 정책을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 지정된 서버에 대해 구성된 데이터베이스 위협 탐지 정책 목록 검색 |
> |  | **서버/데이터베이스/보안메트릭** |  |
> | 작업 | Microsoft.Sql/servers/databases/securityMetrics/read | 데이터베이스 보안 메트릭 컬렉션을 가져옵니다. |
> |  | **서버/데이터베이스/감도레이블** |  |
> | 작업 | Microsoft.Sql/servers/databases/sensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> |  | **서버/데이터베이스/서비스티어어드바이저** |  |
> | 작업 | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 성능을 향상시키거나 비용을 절감하기 위해 쿼리 실행 통계를 기준으로 데이터베이스를 확장하거나 축소하기 위한 제안 사항을 반환합니다. |
> |  | **서버/데이터베이스/스쿠스** |  |
> | 작업 | Microsoft.Sql/servers/databases/skus/read | 데이터베이스에 사용 가능한 SKU 컬렉션을 가져옵니다. |
> |  | **servers/databases/syncGroups** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 동기화 허브 데이터베이스 스키마를 새로 고칩니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 동기화 그룹 동기화를 취소합니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 동기화 그룹 동기화를 트리거합니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/read | 동기화 그룹 목록을 가져오거나 지정된 동기화 그룹에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/write | 지정된 매개 변수를 사용하여 동기화 그룹을 만들거나 지정된 동기화 그룹에 대한 속성을 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/delete | 기존 동기화 그룹을 삭제합니다. |
> |  | **서버/데이터베이스/동기화그룹/허브스키마** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 동기화 허브 데이터베이스 스키마 목록을 반환합니다. |
> |  | **서버/데이터베이스/동기화그룹/로그** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/logs/read | 동기화 그룹 로그 목록을 반환합니다. |
> |  | **서버/데이터베이스/동기화그룹/새로 고침허브스키마작업결과** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 동기화 허브 스키마 새로 고침 작업의 결과를 검색합니다. |
> |  | **servers/databases/syncGroups/syncMembers** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 동기화 멤버 목록을 가져오거나 지정된 동기화 멤버에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 지정된 매개 변수를 사용하여 동기화 멤버를 만들거나 지정된 동기화 멤버에 대한 속성을 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 기존 동기화 멤버를 삭제합니다. |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 동기화 멤버 스키마를 새로 고칩니다. |
> |  | **서버/데이터베이스/동기화그룹/동기화멤버/새로 고침스키마작업결과** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 동기화 멤버 스키마 새로 고침 작업의 결과를 검색합니다. |
> |  | **서버/데이터베이스/동기화그룹/동기화멤버/스키마** |  |
> | 작업 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 동기화 멤버 데이터베이스 스키마 목록을 반환합니다. |
> |  | **서버/데이터베이스/탑쿼리** |  |
> | 작업 | Microsoft.Sql/servers/databases/topQueries/queryText/action | 선택한 쿼리 ID에 대한 Transact-SQL 텍스트를 반환합니다. |
> | 작업 | Microsoft.Sql/servers/databases/topQueries/read | 선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다. |
> |  | **서버/데이터베이스/탑쿼리/통계** |  |
> | 작업 | Microsoft.Sql/servers/databases/topQueries/statistics/read | 선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다. |
> |  | **servers/databases/transparentDataEncryption** |  |
> | 작업 | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 지정된 관리 데이터베이스에서 논리 데이터베이스 투명 데이터 암호화의 세부 정보 검색 |
> | 작업 | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 지정된 논리 데이터베이스에 대한 데이터베이스 투명 데이터 암호화 변경 |
> |  | **서버/데이터베이스/투명데이터암호화/운영결과** |  |
> | 작업 | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다. |
> |  | **서버/데이터베이스/사용** |  |
> | 작업 | Microsoft.Sql/servers/databases/usages/read | Azure SQL Database 사용 정보를 가져옵니다. |
> |  | **servers/databases/vulnerabilityAssessments** |  |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 지정된 데이터베이스에 대한 취약성 평가를 변경합니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 지정된 데이터베이스에 대한 취약성 평가를 제거합니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | givendatabase에서 취약성 평가 정책을 검색합니다. |
> |  | **servers/databases/vulnerabilityAssessments/rules/baselines** |  |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 제거합니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 가져옵니다. |
> |  | **서버/데이터베이스/취약점평가/스캔** |  |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 취약점 평가 데이터베이스 검사를 실행합니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 데이터베이스 취약성 평가 검사 레코드 목록을 반환하거나 지정된 검사 ID에 대한 검사 레코드를 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 기존 검사 결과를 사람이 읽을 수 있는 형식으로 변환합니다. 이미 있는 경우 아무 일도 발생하지 않습니다. |
> |  | **서버/데이터베이스/취약점평가스캔/운영결과** |  |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 데이터베이스 취약성 평가 검사 실행 작업의 결과를 검색합니다. |
> |  | **서버/데이터베이스/취약점평가설정** |  |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 지정된 데이터베이스에 구성된 취약성 평가 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 지정된 데이터베이스에 대한 취약성 평가를 변경합니다. |
> |  | **서버/데이터베이스/워크로드그룹** |  |
> | 작업 | Microsoft.Sql/서버/데이터베이스/워크로드그룹/읽기 | 선택한 데이터베이스의 워크로드 그룹을 나열합니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/워크로드그룹/쓰기 | 특정 워크로드 그룹에 대한 속성을 설정합니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/워크로드그룹/삭제 | 특정 워크로드 그룹을 삭제합니다. |
> |  | **서버/데이터베이스/워크로드그룹/워크로드분류기** |  |
> | 작업 | Microsoft.Sql/서버/데이터베이스/워크로드그룹/워크로드분류기/읽기 | 선택한 데이터베이스에 대한 워크로드 분류기를 나열합니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/워크로드그룹/워크로드분류기/쓰기 | 특정 워크로드 분류자의 속성을 설정합니다. |
> | 작업 | Microsoft.Sql/서버/데이터베이스/워크로드그룹/워크로드분류기/삭제 | 특정 워크로드 분류기 삭제 |
> |  | **servers/disasterRecoveryConfiguration** |  |
> | 작업 | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 이 서버를 포함하는 재해 복구 구성 컬렉션을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 서버 재해 복구 구성을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 지정된 서버에 대한 기존 재해 복구 구성을 삭제합니다. |
> | 작업 | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | DisasterRecoveryConfiguration을 장애 조치(failover)합니다. |
> | 작업 | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | DisasterRecoveryConfiguration을 강제 장애 조치(failover)합니다. |
> |  | **서버/탄력적풀추정** |  |
> | 작업 | Microsoft.Sql/servers/elasticPoolEstimates/read | 이 서버에 대해 이미 작성된 탄력적 풀 예상치의 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/elasticPoolEstimates/write | 제공된 데이터베이스 목록에 대한 새 탄력적 풀 예상치를 만듭니다. |
> |  | **서버/탄력적풀** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/read | 지정된 서버에서 탄력적 풀의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/elasticPools/write | 탄력적 풀을 새로 만들거나 기존 탄력적 풀의 속성을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/elasticPools/delete | 기존 탄력적 풀을 삭제합니다. |
> | 작업 | Microsoft.Sql/서버/elasticPools/장애 조치/작업 | 고객이 탄력적 풀 장애 조치(failover)를 시작했습니다. |
> |  | **서버/탄력적풀/어드바이저** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/advisors/read | 탄력적 풀에 사용할 수 있는 Advisor의 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/elasticPools/advisors/write | 탄력적 풀 수준에서 Advisor의 자동 실행 상태를 업데이트합니다. |
> |  | **서버/탄력적 풀/어드바이저/권장작업** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 탄력적 풀에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 탄력적 풀에 권장 작업을 적용합니다. |
> |  | **서버/탄력적풀/데이터베이스** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/databases/read | 탄력적 풀에 대한 데이터베이스 목록을 가져옵니다. |
> |  | **서버/탄력적 풀/탄성풀 활동** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 지정된 Elastic Database 풀에 대한 활동 및 세부 정보를 검색합니다. |
> |  | **서버/탄력적 풀/탄성풀데이터베이스활동** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Elastic Database 풀에 속하는 지정된 데이터베이스에 대한 활동 및 세부 정보를 검색합니다. |
> |  | **서버/탄력적 풀/메트릭정의** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **서버/탄력적 풀/메트릭** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/metrics/read | 탄력적 데이터베이스 풀에 대한 메트릭을 반환합니다. |
> |  | **서버/탄력적풀/운영** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 아직 완료되지 않은 Azure SQL 탄력적 풀 보류 중인 비동기 작업을 취소합니다. |
> | 작업 | Microsoft.Sql/servers/elasticPools/operations/read | 탄력적 풀에서 수행된 작업 목록을 반환합니다. |
> |  | **서버/탄성풀/공급자/Microsoft.Insights/진단설정** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> |  | **서버/탄력적 풀/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **서버/탄성풀/스쿠스** |  |
> | 작업 | Microsoft.Sql/servers/elasticPools/skus/read | 탄력적 풀에 사용 가능한 SKU 컬렉션을 가져옵니다. |
> |  | **servers/encryptionProtector** |  |
> | 작업 | Microsoft.Sql/서버/암호화 보호/유효성 검사/작업 | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/encryptionProtector/read | 서버 암호화 보호기 목록을 반환하거나 지정된 서버 암호화 보호기에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/encryptionProtector/write | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> |  | **servers/extendedAuditingSettings** |  |
> | 작업 | Microsoft.Sql/servers/extendedAuditingSettings/read | 지정된 서버에 구성된 확장 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 작업 | Microsoft.Sql/servers/extendedAuditingSettings/write | 지정된 서버에 대한 확장 서버 Blob 감사를 변경합니다. |
> |  | **servers/failoverGroups** |  |
> | 작업 | Microsoft.Sql/servers/failoverGroups/read | 장애 조치(failover) 그룹 목록을 가져오거나 지정된 장애 조치(failover) 그룹에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/failoverGroups/write | 지정된 매개 변수를 사용하여 장애 조치(failover) 그룹을 만들거나 지정된 장애 조치(failover) 그룹에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/failoverGroups/delete | 기존 장애 조치(failover) 그룹을 삭제합니다. |
> | 작업 | Microsoft.Sql/servers/failoverGroups/failover/action | 기존 장애 조치(failover) 그룹에서 계획된 장애 조치(failover)를 실행합니다. |
> | 작업 | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 기존 장애 조치(failover) 그룹에서 강제 장애 조치(failover)를 실행합니다. |
> |  | **servers/firewallRules** |  |
> | 작업 | Microsoft.Sql/servers/firewallRules/write | 지정된 매개 변수를 사용하여 서버 방화벽 규칙을 만들고 지정된 규칙에 대한 속성을 업데이트하거나 모든 기존 규칙을 새로운 서버 방화벽 규칙으로 덮어씁니다. |
> | 작업 | Microsoft.Sql/servers/firewallRules/read | 서버 방화벽 규칙 목록을 반환하거나 지정된 서버 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/firewallRules/delete | 기존 서버 방화벽 규칙을 삭제합니다. |
> |  | **서버/가져오기내보내기작업결과** |  |
> | 작업 | Microsoft.Sql/servers/importExportOperationResults/read | 진행 중인 가져오기/내보내기 작업을 가져옵니다. |
> |  | **서버/액세스할 수 없는 데이터베이스** |  |
> | 작업 | Microsoft.Sql/서버/액세스할 수 없는 데이터베이스/읽기 | 논리 서버에서 액세스할 수 없는 데이터베이스 목록을 반환합니다. |
> |  | **서버/인터페이스EndpointProfiles** |  |
> | 작업 | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 지정된 매개 변수를 사용하여 인터페이스 엔드포인트 프로필을 만들거나 지정된 인터페이스 엔드포인트에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 지정된 인터페이스 엔드포인트 프로필의 특성을 반환합니다. |
> | 작업 | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 지정된 인터페이스 엔드포인트 프로필을 삭제합니다. |
> |  | **servers/jobAgents** |  |
> | 작업 | Microsoft.Sql/servers/jobAgents/read | Azure SQL DB 작업 에이전트를 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/jobAgents/write | Azure SQL DB 작업 에이전트를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/jobAgents/delete | Azure SQL DB 작업 에이전트를 삭제합니다. |
> |  | **servers/keys** |  |
> | 작업 | Microsoft.Sql/servers/keys/read | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/keys/delete | 기존 서버 키를 삭제합니다. |
> |  | **서버/작업결과** |  |
> | 작업 | Microsoft.Sql/servers/operationResults/read | 진행 중인 서버 작업을 가져옵니다. |
> |  | **서버/작업** |  |
> | 작업 | Microsoft.Sql/서버/작업/읽기 | 서버에서 수행된 작업 목록을 반환합니다. |
> |  | **서버/개인엔드포인트커넥션프록시** |  |
> | 작업 | Microsoft.Sql/서버/개인엔드포인트연결프록시/유효성 검사/작업 | NRP 측에서 개인 엔드포인트 연결 create 호출 유효성 검사 |
> | 작업 | 마이크로소프트.Sql/서버/개인엔드포인트커넥션프록시스/읽기 | 개인 끝점 연결 프록시 목록을 반환하거나 지정된 개인 끝점 연결 프록시에 대한 속성을 가져옵니다. |
> | 작업 | 마이크로소프트.Sql/서버/개인엔드포인트커넥션프록시스/쓰기 | 지정된 매개 변수를 사용 하 고 개인 끝점 연결 프록시를 만들거나 지정 된 개인 끝점 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 작업 | 마이크로소프트.Sql/서버/개인엔드포인트연결프록시시/삭제 | 기존 개인 끝점 연결 프록시 삭제 |
> |  | **서버/개인엔드포인트연결** |  |
> | 작업 | 마이크로소프트.Sql/서버/개인엔드포인트연결/읽기 | 개인 끝점 연결 목록을 반환하거나 지정된 개인 끝점 연결에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/서버/개인엔드포인트연결/삭제 | 기존 개인 끝점 연결을 삭제합니다. |
> | 작업 | 마이크로소프트.Sql/서버/개인엔드포인트연결/쓰기 | 기존 개인 끝점 연결을 승인 또는 거부합니다. |
> |  | **서버/개인링크리소스** |  |
> | 작업 | 마이크로소프트.Sql/서버/개인링크리소스/읽기 | 해당 SQL 서버에 대한 개인 링크 리소스 가져오기 |
> |  | **서버/공급자/Microsoft.Insights/메트릭정의** |  |
> | 작업 | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 서버에 사용할 수 있는 메트릭 형식을 반환합니다. |
> |  | **서버/권장탄리풀** |  |
> | 작업 | Microsoft.Sql/servers/recommendedElasticPools/read | 기록 리소스 사용률에 따라 비용을 절감하거나 성능을 향상시키기 위한 Elastic Database 풀 권장 사항을 검색합니다. |
> |  | **서버/권장탄리풀/데이터베이스** |  |
> | 작업 | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 지정된 서버에 대해 권장되는 Elastic Database 풀의 메트릭을 검색합니다. |
> |  | **서버/복구 가능 데이터베이스** |  |
> | 작업 | Microsoft.Sql/servers/recoverableDatabases/read | 이 작업은 라이브 데이터베이스의 재해 복구 기능이 데이터베이스를 마지막 정상 지점으로 복원하는 데 사용합니다. 마지막 정상 백업에 대한 정보가 반환되지만 실제로 데이터베이스는 복원되지 않습니다. |
> |  | **서버/복제링크** |  |
> | 작업 | Microsoft.Sql/servers/replicationLinks/read | 복제 링크 목록을 반환하거나 지정된 복제 링크의 속성을 가져옵니다. |
> |  | **servers/restorableDroppedDatabases** |  |
> | 작업 | Microsoft.Sql/servers/restorableDroppedDatabases/read | 지정된 서버에서 삭제되었지만 여전히 보존 정책에 속하는 데이터베이스 목록을 가져옵니다. |
> |  | **servers/securityAlertPolicies** |  |
> | 작업 | Microsoft.Sql/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> | 작업 | Microsoft.Sql/servers/securityAlertPolicies/read | 지정된 서버에 대해 구성된 서버 위협 탐지 정책 목록 검색 |
> |  | **서버/보안경고정책/운영결과** |  |
> | 작업 | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 서버 위협 검색 정책 쓰기 작업의 결과를 검색합니다. |
> |  | **서버/서비스목표** |  |
> | 작업 | Microsoft.Sql/servers/serviceObjectives/read | 지정된 서버에서 사용할 수 있는 서비스 수준 목표(성능 계층이라고도 함) 목록을 검색합니다. |
> |  | **servers/syncAgents** |  |
> | 작업 | Microsoft.Sql/servers/syncAgents/read | 동기화 에이전트 목록을 가져오거나 지정된 동기화 에이전트에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/syncAgents/write | 지정된 매개 변수를 사용하여 동기화 에이전트를 만들거나 지정된 동기화 에이전트에 대한 속성을 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/syncAgents/delete | 기존 동기화 에이전트를 삭제합니다. |
> | 작업 | Microsoft.Sql/servers/syncAgents/generateKey/action | 동기화 에이전트 등록 키를 생성합니다. |
> |  | **서버/동기화 에이전트/링크된 데이터베이스** |  |
> | 작업 | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 동기화 에이전트 연결 데이터베이스 목록을 반환합니다. |
> |  | **서버/사용** |  |
> | 작업 | Microsoft.Sql/servers/usages/read | 서버 내의 모든 데이터베이스에서 서버 DTU 할당량 및 현재 DTU 사용량 반환 |
> |  | **servers/virtualNetworkRules** |  |
> | 작업 | Microsoft.Sql/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 작업 | Microsoft.Sql/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> |  | **servers/vulnerabilityAssessments** |  |
> | 작업 | Microsoft.Sql/servers/vulnerabilityAssessments/write | 주어진 서버에 대한 취약성 평가를 변경합니다. |
> | 작업 | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 주어진 서버에 대한 취약성 평가를 제거합니다. |
> | 작업 | Microsoft.Sql/servers/vulnerabilityAssessments/read | 지정된 서버에서 취약성 평가 정책을 검색합니다. |
> |  | **가상 클러스터** |  |
> | 작업 | Microsoft.Sql/virtualClusters/read | 가상 클러스터 목록을 반환하거나 지정된 가상 클러스터에 대한 속성을 가져옵니다. |
> | 작업 | Microsoft.Sql/virtualClusters/write | 가상 클러스터 태그를 업데이트합니다. |
> | 작업 | 마이크로소프트.Sql/가상 클러스터/삭제 | 기존 가상 클러스터를 삭제합니다. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

Azure 서비스: [Azure 가상 시스템의 SQL 서버](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로소프트.Sql가상기계/레지스터/액션 | Microsoft.SqlVirtualMachine 리소스 공급자를 사용 하 여 구독 등록 |
> | 작업 | 마이크로소프트.Sql가상기계/등록 취소/작업 | Microsoft.SqlVirtualMachine 리소스 공급자를 사용 하 여 구독을 등록 취소 |
> |  | **위치** |  |
> | 작업 | 마이크로소프트.SqlVirtualMachine/위치/레지스터SqlVm후보/액션 | SQL Vm 후보 등록 |
> |  | **위치/가용성그룹Listener작업결과** |  |
> | 작업 | 마이크로소프트.Sql가상기계/위치/가용성그룹Listener작업결과/읽기 | 가용성 그룹 리스너 작업의 결과 가져옵니다. |
> |  | **위치/sql가상기계그룹작업결과** |  |
> | 작업 | 마이크로소프트.Sql가상기계/위치/sql가상머신그룹작업결과/읽기 | SQL 가상 컴퓨터 그룹 작업의 결과 가져옵니다. |
> |  | **위치/sql가상기계작업결과** |  |
> | 작업 | 마이크로소프트.Sql가상기계/위치/sql가상머신작업결과/읽기 | SQL 가상 시스템 작동 결과 얻기 |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.Sql가상기계/연산/읽기 |  |
> |  | **sql가상머신그룹** |  |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/읽기 | SQL 가상 머신 그룹의 세부 정보 |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/쓰기 | 기존 SQL 가상 컴퓨터 그룹의 새 속성 또는 변경 속성 만들기 |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/삭제 | 기존 SQL 가상 컴퓨터 그룹 삭제 |
> |  | **sql가상 머신 그룹 / 가용성그룹청취자** |  |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/가용성그룹리스/읽기 | 지정된 SQL 가상 컴퓨터 그룹에서 SQL 가용성 그룹 리스너에 대한 세부 정보를 검색합니다. |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/가용성그룹리스/쓰기 | 기존 SQL 가용성 그룹 수신기의 새 속성 또는 변경 속성 만들기 |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/가용성그룹리스/삭제 | 기존 가용성 그룹 리스너 삭제 |
> |  | **sql가상기계그룹/sql가상머신** |  |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신그룹/sql가상머신/읽기 | 특정 SQL 가상 컴퓨터 그룹별로 Sql 가상 컴퓨터 나열 |
> |  | **sql가상 머신** |  |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신/읽기 | SQL 가상 머신의 세부 정보 검색 |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신/쓰기 | 기존 SQL 가상 시스템의 새 속성 또는 변경 속성 만들기 |
> | 작업 | 마이크로소프트.Sql가상기계/sql가상머신/삭제 | 기존 SQL 가상 컴퓨터 삭제 |

## <a name="microsoftstorage"></a>Microsoft.Storage

Azure 서비스: [저장소](../storage/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Storage/register/action | 스토리지 리소스 공급자에 대한 구독을 등록하고 스토리지 계정을 만들도록 설정합니다. |
> |  | **checknameavailability** |  |
> | 작업 | Microsoft.Storage/checknameavailability/read | 해당 계정 이름이 올바른지와 사용되고 있지 않은지 확인합니다. |
> |  | **위치** |  |
> | 작업 | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷을 삭제 중임을 Microsoft.Storage에 알립니다. |
> |  | **위치/확인 이름 사용 가능 여부** |  |
> | 작업 | 마이크로소프트.스토리지/위치/체크 네임 가용성/읽기 | 해당 계정 이름이 올바른지와 사용되고 있지 않은지 확인합니다. |
> |  | **위치/사용법** |  |
> | 작업 | Microsoft.Storage/locations/usages/read | 지정한 구독의 리소스에 대한 한도 및 현재 사용 수를 반환합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.Storage/operations/read | 비동기 작업의 상태를 폴링합니다. |
> |  | **Sku** |  |
> | 작업 | Microsoft.Storage/skus/read | Microsoft Storage에서 지원하는 SKU를 나열합니다. |
> |  | **저장소 계정** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/복원BlobRanges/작업 | Blob 범위를 지정된 시간의 상태로 복원 |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/프라이빗엔드포인트연결승인/액션 | 개인 엔드포인트 연결 승인 |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/장애 조치/작업 | 고객은 가용성 문제가 있는 경우 장애 조치(failover)를 제어할 수 있습니다. |
> | 작업 | Microsoft.Storage/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/regeneratekey/action | 지정된 스토리지 계정에 대한 액세스 키를 다시 생성합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 지정된 스토리지 계정에 대한 모든 사용자 위임 키를 취소합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/delete | 기존 스토리지 계정을 삭제합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | 작업 | Microsoft.Storage/storageAccounts/listAccountSas/Action | 지정된 스토리지 계정에 대한 계정 SAS 토큰을 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/listServiceSas/Action | 지정된 스토리지 계정에 대한 서비스 SAS 토큰을 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/write | 지정된 매개 변수를 사용하여 스토리지 계정을 만들거나, 속성 또는 태그를 업데이트하거나, 지정된 스토리지 계정의 사용자 지정 도메인을 추가합니다. |
> |  | **storageAccounts/blobServices** |  |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob Service에 대한 사용자 위임 키를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/write | Blob service 속성 넣기의 결과를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/read | Blob service 속성 또는 통계를 반환합니다. |
> |  | **저장소계정/Blob서비스/컨테이너** |  |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blob 컨테이너 패치 결과를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 컨테이너 삭제 결과를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너 목록을 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Blob 컨테이너 임대 결과를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blob 컨테이너 넣기의 결과를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Blob 컨테이너 법적 보존을 지웁니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Blob 컨테이너 법적 보존을 설정합니다. |
> |  | **저장소계정/blob서비스/컨테이너/불변성정책** |  |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Blob 컨테이너 불변성 정책을 확장합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Blob 컨테이너 불변성 정책을 삭제합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Blob 컨테이너 불변성 정책을 넣습니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blob 컨테이너 불변성 정책을 잠급니다. |
> | 작업 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Blob 컨테이너 불변성 정책을 가져옵니다. |
> |  | **저장소계정/데이터공유정책** |  |
> | 작업 | 마이크로소프트.스토리지/저장소계정/데이터 쉐어정책/삭제 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/데이터쉐어정책/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/데이터쉐어정책/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/데이터쉐어정책/쓰기 |  |
> |  | **저장소계정/암호화스코프** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/암호화스코프/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/암호화스코프/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/암호화스코프/쓰기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/암호화스코프/쓰기 |  |
> |  | **storageAccounts/fileServices** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/공유/작업 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/쓰기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/읽기 | 파일 서비스 속성 받기 |
> |  | **저장소계정/파일서비스/공유** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/공유/삭제 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/공유/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/공유/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/파일서비스/공유/쓰기 |  |
> |  | **저장소계정/로컬사용자** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/로컬사용자/삭제 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/로컬 사용자/목록 키/작업 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/로컬 사용자/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/로컬 사용자/읽기 |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/로컬 사용자/쓰기 |  |
> |  | **저장소계정/관리정책** |  |
> | 작업 | Microsoft.저장소/저장소계정/관리정책/삭제 | 저장소 계정 관리 정책 삭제 |
> | 작업 | Microsoft.저장소/저장소계정/관리정책/읽기 | 스토리지 관리 계정 정책 받기 |
> | 작업 | 마이크로소프트.저장소/저장소계정/관리정책/쓰기 | 저장소 계정 관리 정책 넣기 |
> |  | **저장소계정/개체복제정책** |  |
> | 작업 | Microsoft.저장소/저장소계정/개체복제정책/삭제 |  |
> | 작업 | Microsoft.저장소/저장소계정/개체복제정책/읽기 |  |
> | 작업 | Microsoft.저장소/저장소계정/개체복제정책/읽기 |  |
> | 작업 | Microsoft.저장소/저장소계정/개체복제정책/쓰기 |  |
> |  | **스토리지계정/프라이빗엔드포인트커넥션프록시** |  |
> | 작업 | 마이크로 소프트.스토리지 / 스토리지계정 / 개인엔드 포인트 연결 Proxies / 읽기 | 개인 엔드포인트 연결 프록시 받기 |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/사설엔드포인트연결프록시/삭제 | 개인 끝점 연결 프록시 삭제 |
> | 작업 | 마이크로 소프트.스토리지 / 스토리지계정 / 개인엔드 포인트 연결 Proxies / 쓰기 | 개인 끝점 연결 프록시 넣기 |
> |  | **스토리지계정/프라이빗엔드포인트커넥션** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/프라이빗엔드포인트연결/삭제 | 개인 끝점 연결 삭제 |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/프라이빗엔드포인트연결/읽기 | 개인 엔드포인트 연결 받기 |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/개인엔드포인트연결/쓰기 | 개인 끝점 연결 넣기 |
> |  | **스토리지계정/프라이빗링크리소스** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/개인링크자원/읽기 | 저장소 계정 그룹화 받기 |
> |  | **storageAccounts/queueServices** |  |
> | 작업 | Microsoft.Storage/storageAccounts/queueServices/read | 큐 서비스 속성 받기 |
> | 작업 | Microsoft.Storage/storageAccounts/queueServices/read | 큐 서비스 속성 또는 통계를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/queueServices/write | 큐 서비스 속성 설정 결과를 반환합니다. |
> |  | **저장소계정/큐서비스/큐** |  |
> | 작업 | Microsoft.Storage/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/queueServices/queues/write | 큐 쓰기 결과를 반환합니다. |
> | 작업 | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 큐 삭제 결과를 반환합니다. |
> |  | **storageAccounts/services/diagnosticSettings** |  |
> | 작업 | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 스토리지 계정 진단 설정을 만들거나 업데이트합니다. |
> |  | **storageAccounts/tableServices** |  |
> | 작업 | 마이크로소프트.스토리지/스토리지계정/테이블서비스/읽기 | 테이블 서비스 속성 받기 |
> |  | **사용** |  |
> | 작업 | Microsoft.Storage/usages/read | 지정한 구독의 리소스에 대한 한도 및 현재 사용 수를 반환합니다. |
> |  | **저장소계정/Blob서비스/컨테이너/Blob** |  |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록을 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과를 반환합니다. |
> | DataAction | 마이크로소프트.스토리지/스토리지계정/blobServices/컨테이너/Blobs/deleteBlobVersion/action | Blob 버전을 삭제한 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Blob 콘텐츠 추가 결과를 반환합니다. |
> | DataAction | Microsoft.저장소/저장소계정/BlobServices/컨테이너/Blob/필터/작업 | 일치하는 태그 필터가 있는 계정 아래의 Blob 목록을 반환합니다. |
> | DataAction | Microsoft.저장소/저장소계정/BlobServices/컨테이너/Blob/이동/작업 | Blob을 한 경로에서 다른 경로로 이동 |
> | DataAction | Microsoft.저장소/저장소계정/BlobServices/컨테이너/Blob/관리소유권/작업 | Blob의 소유권 변경 |
> | DataAction | Microsoft.저장소/저장소계정/BlobServices/컨테이너/Blob/수정권한/작업 | Blob의 사용 권한을 수정합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Blob 명령의 결과를 반환합니다. |
> |  | **저장소계정/Blob서비스/컨테이너/Blob/태그** |  |
> | DataAction | Microsoft.저장소/저장소계정/BlobServices/컨테이너/Blob/태그/읽기 | Blob 태그 읽기의 결과를 반환합니다. |
> | DataAction | Microsoft.저장소/저장소계정/BlobServices/컨테이너/Blob/태그/쓰기 | Blob 태그 작성 결과 반환 |
> |  | **저장소계정/파일서비스/파일공유/파일** |  |
> | DataAction | 마이크로소프트.스토리지/스토리지계정/파일서비스/파일공유/파일/읽기 | 파일/폴더 또는 파일/폴더 목록 반환 |
> | DataAction | 마이크로소프트.스토리지/스토리지계정/파일서비스/파일공유/파일/파일/쓰기 | 파일을 작성하거나 폴더를 만든 결과를 반환합니다. |
> | DataAction | 마이크로소프트.스토리지/스토리지계정/파일서비스/파일공유/파일/삭제 | 파일/폴더 삭제 결과를 반환합니다. |
> | DataAction | Microsoft.저장소/저장소계정/파일서비스/파일공유/파일/수정 권한/작업 | 파일/폴더에 대한 사용 권한 수정 결과 반환 |
> | DataAction | 마이크로소프트.스토리지/스토리지계정/파일서비스/파일공유/파일/액타수퍼유저/액션 | 파일 관리자 권한 받기 |
> |  | **저장소계정/큐서비스/큐/메시지** |  |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 메시지를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 메시지 작성 결과 반환 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 메시지 삭제 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 메시지 추가 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 메시지 처리 결과를 반환합니다. |

## <a name="microsoftstoragesync"></a>마이크로소프트.스토리지싱크

Azure 서비스: [저장소](../storage/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | 마이크로 소프트.storagesync / 레지스터 / 작업 | 저장소 동기화 공급자에 대한 구독 등록 |
> | 작업 | 마이크로 소프트.storagesync / 레지스터 / 비등록 / 작업 | 저장소 동기화 공급자에 대 한 구독을 등록 취소 합니다. |
> |  | **위치** |  |
> | 작업 | microsoft.storagesync/locations/checkNameAvailability/action | 해당 스토리지 동기화 서비스 이름이 유효하며 사용되지 않는지 확인합니다. |
> |  | **위치/워크플로/ 운영** |  |
> | 작업 | microsoft.storagesync/locations/workflows/operations/read | 비동기 작업의 상태를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로 소프트.storagesync / 작업 / 읽기 | 지원되는 작업 목록 가져옵니다. |
> |  | **스토리지싱크서비스** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/read | Storage Sync Services를 읽습니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/write | Storage Sync Services를 만들거나 업데이트합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/delete | Storage Sync Services를 삭제합니다. |
> |  | **스토리지싱크서비스/제공업체/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Storage Sync Services에 사용 가능한 메트릭을 가져옵니다. |
> |  | **storageSyncServices/registeredServers** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/registeredServers/read | 등록된 서버를 읽습니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/registeredServers/write | 등록된 서버를 만들거나 업데이트합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/registeredServers/delete | 등록된 서버를 삭제합니다. |
> |  | **스토리지싱크서비스/등록서버/제공업체/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 등록된 서버에 사용 가능한 메트릭을 가져옵니다. |
> |  | **storageSyncServices/syncGroups** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/read | 동기화 그룹을 읽습니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/write | 동기화 그룹을 만들거나 업데이트합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/delete | 동기화 그룹을 삭제합니다. |
> |  | **storageSyncServices/syncGroups/cloudEndpoints** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 클라우드 엔드포인트를 준비합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 클라우드 엔드포인트를 만들거나 업데이트합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 클라우드 엔드포인트를 삭제합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 백업 전에 이 작업을 호출합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 백업 후 이 작업을 호출합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 복원 전에 이 작업을 호출합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 복원 후 이 작업을 호출합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 하트비트를 복원합니다. |
> | 작업 | 마이크로 소프트.storagesync/스토리지싱크서비스/동기화그룹/클라우드엔드포인트/트리거변경감지/액션 | 이 작업을 호출하여 클라우드 끝점의 파일 공유에서 변경 내용 검색을 트리거합니다. |
> |  | **스토리지싱크서비스/동기화그룹/클라우드엔드포인트/운영결과** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 비동기 백업/복원 작업의 상태를 가져오기 |
> |  | **스토리지싱크서비스/싱크그룹/공급자/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 동기화 그룹에 사용 가능한 메트릭을 가져옵니다. |
> |  | **storageSyncServices/syncGroups/serverEndpoints** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 서버 엔드포인트를 읽습니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 서버 엔드포인트를 만들거나 업데이트합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 서버 엔드포인트를 삭제합니다. |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 파일을 서버로 회수하려면 이 작업을 호출합니다. |
> |  | **스토리지싱크서비스/싱크그룹/서버엔드포인트/공급자/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 서버 엔드포인트에 사용 가능한 메트릭을 가져옵니다. |
> |  | **storageSyncServices/workflows** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/workflows/read | 워크플로 읽기 |
> |  | **스토리지싱크서비스/워크플로우/작업결과** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 비동기 작업의 상태를 가져옵니다. |
> |  | **스토리지싱크서비스/워크플로/운영** |  |
> | 작업 | microsoft.storagesync/storageSyncServices/workflows/operations/read | 비동기 작업의 상태를 가져옵니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

Azure 서비스: [스토심플](../storsimple/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.StorSimple/register/action | Microsoft.StorSimple 공급자를 등록합니다. |
> |  | **managers** |  |
> | 작업 | Microsoft.StorSimple/managers/clearAlerts/action | 디바이스 관리자와 관련된 모든 경고를 선택 취소합니다. |
> | 작업 | Microsoft.StorSimple/managers/getEncryptionKey/action | 디바이스 관리자에 대한 암호화 키를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/read | 디바이스 관리자를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/delete | 디바이스 관리자를 삭제합니다. |
> | 작업 | Microsoft.StorSimple/managers/write | 디바이스 관리자를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/configureDevice/action | 디바이스를 구성합니다. |
> | 작업 | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 클래식에서 리소스 관리자로 마이그레이션 |
> | 작업 | Microsoft.StorSimple/managers/listActivationKey/action | StorSimple 디바이스 관리자의 활성화 키를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/regenerateActivationKey/action | 기존 StorSimple 디바이스 관리자의 활성화 키를 다시 생성합니다. |
> | 작업 | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | StorSimple 디바이스 관리자의 공용 암호화 키를 나열합니다. |
> | 작업 | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 새 클라우드 어플라이언스를 만듭니다. |
> | 작업 | Microsoft.StorSimple/Managers/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | 작업 | Microsoft.StorSimple/Managers/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/Managers/delete | 자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다. |
> |  | **관리자/액세스 제어 레코드** |  |
> | 작업 | Microsoft.StorSimple/managers/accessControlRecords/read | Access Control 레코드를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/accessControlRecords/write | Access Control 레코드를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/accessControlRecords/delete | Access Control 레코드를 삭제합니다. |
> |  | **관리자/액세스 관리 기록/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/경고** |  |
> | 작업 | Microsoft.StorSimple/managers/alerts/read | 경고를 나열하거나 가져옵니다. |
> |  | **관리자/백업** |  |
> | 작업 | Microsoft.StorSimple/managers/backups/read | Backup 세트를 나열하거나 가져옵니다. |
> |  | **관리자/대역폭설정** |  |
> | 작업 | Microsoft.StorSimple/managers/bandwidthSettings/read | 대역폭 설정을 나열합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/bandwidthSettings/write | 새 대역폭 설정을 만들거나 대역폭 설정을 업데이트합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/bandwidthSettings/delete | 기존 대역폭 설정을 삭제합니다(8000 시리즈만 해당). |
> |  | **관리자/대역폭설정/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/인증서** |  |
> | 작업 | Microsoft.StorSimple/managers/certificates/write | 인증서를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/Managers/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> |  | **관리자/클라우드 앱런스 구성** |  |
> | 작업 | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 클라우드 어플라이언스 지원 구성을 나열합니다. |
> |  | **관리자/장치** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 구성된 전자 메일 받는 사람에게 테스트 경고 전자 메일을 보냅니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 디바이스에서 업데이트를 검색합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/download/action | 장치에 대한 업데이트를 다운로드합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/install/action | 디바이스에 업데이트를 설치합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/read | 디바이스를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/write | 디바이스를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/delete | 디바이스를 삭제합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/deactivate/action | 디바이스를 비활성화합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/failover/action | 디바이스의 장애 조치(Failover)입니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 기존 디바이스의 지원 패키지를 게시합니다. StorSimple 지원 패키지는 StorSimple 디바이스 문제를 해결하는 데 있어서 Microsoft 지원을 지원하기 위해 모든 관련 로그를 수집하는 사용하기 쉬운 메커니즘입니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 디바이스의 서비스 암호화 키 롤오버에 대한 권한을 부여합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/installUpdates/action | 디바이스에 업데이트를 설치합니다(8000 시리즈만). |
> | 작업 | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 기존 디바이스에 대한 장애 조치(failover) 집합을 나열합니다(8000 시리즈만). |
> | 작업 | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 디바이스의 장애 조치(failover) 대상을 나열합니다(8000 시리즈만). |
> | 작업 | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 디바이스 관리자의 공용 암호화 키를 나열합니다. |
> |  | **관리자/장치/경고설정** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/alertSettings/read | 경고 설정을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/alertSettings/write | 경고 설정을 만들거나 업데이트합니다. |
> |  | **관리자/장치/경고설정/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/백업정책** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/write | 새 Backup 정책을 만들거나 Backup 정책을 업데이트합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/read | Backup 정책을 나열합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 기존 Backup 정책을 삭제합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 정책으로 보호되는 모든 볼륨의 요청 시 백업을 만들기 위해 수동 백업을 수행합니다. |
> |  | **관리자/장치/백업정책/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/백업 정책/일정** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 새 일정을 만들거나 일정을 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 일정을 나열합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 기존 일정을 삭제합니다. |
> |  | **관리자/장치/백업정책/일정/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/백업** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backups/read | Backup 세트를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/backups/delete | Backup 세트를 삭제합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/backups/restore/action | 백업 세트에서 모든 볼륨을 복원합니다. |
> |  | **관리자/장치/백업/요소** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 백업 요소를 사용하여 공유 또는 볼륨을 복제합니다. |
> |  | **관리자/장치/백업/요소/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/백업/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/백업일정그룹** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Backup 일정 그룹을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Backup 일정 그룹을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Backup 일정 그룹을 삭제합니다. |
> |  | **관리자/장치/백업일정그룹/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/chapSettings** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/chapSettings/write | CHAP 설정을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/chapSettings/read | CHAP 설정을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/chapSettings/delete | CHAP 설정을 삭제합니다. |
> |  | **관리자/장치/chapSettings/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/디스크** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/disks/read | 디스크를 나열하거나 가져옵니다. |
> |  | **관리자/장치/장애 조치/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/장애 조치 대상** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/failoverTargets/read | 디바이스의 장애 조치(Failover) 대상을 나열하거나 가져옵니다. |
> |  | **관리자/장치/파일 서버** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/read | 파일 서버를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/write | 파일 서버를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/delete | 파일 서버를 삭제합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 파일 서버 백업을 수행합니다. |
> |  | **관리자/장치/파일 서버/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> |  | **관리자/장치/파일 서버/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> |  | **관리자/장치/파일 서버/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/파일 서버/공유** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 공유를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 공유를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 공유를 삭제합니다. |
> |  | **관리자/장치/파일 서버/공유/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> |  | **관리자/장치/파일 서버/공유/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> |  | **관리자/장치/파일 서버/공유/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/하드웨어구성 요소 그룹** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 하드웨어 구성 요소 그룹을 나열합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 하드웨어 구성 요소 그룹의 컨트롤러 전원 상태를 변경합니다. |
> |  | **관리자/장치/하드웨어구성 요소 그룹/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/이시서버** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/read | iSCSI 서버를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/write | iSCSI 서버를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/delete | ISCSI 서버를 삭제합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | ISCSI 서버 백업을 수행합니다. |
> |  | **관리자/장치/이csiservers/디스크** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 디스크를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 디스크를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 디스크를 삭제합니다. |
> |  | **관리자/장치/이csiservers/디스크/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> |  | **관리자/장치/iscsiservers/디스크/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> |  | **관리자/장치/이csiservers/디스크/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/이csiservers/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> |  | **관리자/장치/이csiservers/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> |  | **관리자/장치/이csiservers/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/작업** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/jobs/read | 작업을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 실행 중인 작업을 취소합니다. |
> |  | **관리자/장치/작업/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> |  | **관리자/장치/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> |  | **관리자/장치/마이그레이션소스구성** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 마이그레이션 위한 원본 구성을 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 마이그레이션 프로세스 기간을 예측하는 작업을 시작합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 원본 구성을 사용하여 마이그레이션을 시작합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 성공적인 마이그레이션을 확인하고 커밋합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 마이그레이션 예측 작업의 상태를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 마이그레이션의 상태를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 마이그레이션 확인 상태를 가져옵니다. |
> |  | **관리자/장치/마이그레이션소스구성/확인마이그레이션상태** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 마이그레이션 확인 상태를 나열합니다. |
> |  | **관리자/장치/마이그레이션소스구성/마이그레이션추정** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 마이그레이션 추정을 나열합니다. |
> |  | **관리자/장치/마이그레이션소스구성/마이그레이션상태** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 마이그레이션 상태를 나열합니다. |
> |  | **관리자/장치/마이그레이션소스구성/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/네트워크설정** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/networkSettings/read | 네트워크 설정을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/networkSettings/write | 새 네트워크 설정을 만들거나 네트워크 설정을 업데이트합니다. |
> |  | **관리자/장치/네트워크설정/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/보안설정** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 보안 설정을 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/securitySettings/read | 보안 설정을 나열합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 디바이스에 대한 원격 관리 인증서를 동기화합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/securitySettings/write | 새 보안 설정을 만들거나 보안 설정을 업데이트합니다. |
> |  | **관리자/장치/보안설정/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/공유** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/shares/read | 공유를 나열하거나 가져옵니다. |
> |  | **관리자/장치/시간 설정** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/timeSettings/read | 시간 설정을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/timeSettings/write | 새 시간 설정을 만들거나 시간 설정을 업데이트합니다. |
> |  | **관리자/장치/시간설정/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/업데이트/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/장치/업데이트요약** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/updateSummary/read | 업데이트 요약을 나열하거나 가져옵니다. |
> |  | **관리자/장치/볼륨컨테이너** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/write | 새 볼륨 컨테이너를 만들거나 볼륨 컨테이너를 업데이트합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/read | 볼륨 컨테이너를 나열합니다(8000 시리즈만 해당). |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 기존 볼륨 컨테이너를 삭제합니다(8000 시리즈만 해당). |
> |  | **관리자/장치/볼륨컨테이너/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 메트릭을 나열합니다 |
> |  | **관리자/장치/볼륨컨테이너/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 메트릭 정의를 나열합니다. |
> |  | **관리자/장치/볼륨컨테이너/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/볼륨컨테이너/볼륨** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 볼륨을 나열합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 새 볼륨을 만들거나 볼륨을 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 기존 볼륨을 삭제합니다. |
> |  | **관리자/장치/볼륨컨테이너/볼륨/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 메트릭을 나열합니다 |
> |  | **관리자/장치/볼륨컨테이너/볼륨/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 메트릭 정의를 나열합니다. |
> |  | **관리자/장치/볼륨컨테이너/볼륨/작업결과** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 작업 결과를 나열합니다. |
> |  | **관리자/장치/볼륨** |  |
> | 작업 | Microsoft.StorSimple/managers/devices/volumes/read | 볼륨을 나열합니다. |
> |  | **관리자/암호화설정** |  |
> | 작업 | Microsoft.StorSimple/managers/encryptionSettings/read | 암호화 설정을 나열하거나 가져옵니다. |
> |  | **관리자/확장정보** |  |
> | 작업 | Microsoft.StorSimple/managers/extendedInformation/read | 확장 자격 증명 모음 정보를 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/extendedInformation/write | 확장 자격 증명 모음 정보를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/extendedInformation/delete | 확장 자격 증명 모음 정보를 삭제합니다. |
> | 작업 | Microsoft.StorSimple/Managers/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/Managers/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 작업 | Microsoft.StorSimple/Managers/extendedInformation/delete | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> |  | **관리자/기능** |  |
> | 작업 | Microsoft.StorSimple/managers/features/read | 기능을 나열합니다. |
> |  | **관리자/파일 서버** |  |
> | 작업 | Microsoft.StorSimple/managers/fileservers/read | 파일 서버를 나열하거나 가져옵니다. |
> |  | **관리자/이시서버** |  |
> | 작업 | Microsoft.StorSimple/managers/iscsiservers/read | iSCSI 서버를 나열하거나 가져옵니다. |
> |  | **관리자/작업** |  |
> | 작업 | Microsoft.StorSimple/managers/jobs/read | 작업을 나열하거나 가져옵니다. |
> |  | **관리자/메트릭** |  |
> | 작업 | Microsoft.StorSimple/managers/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> |  | **관리자/메트릭정의** |  |
> | 작업 | Microsoft.StorSimple/managers/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> |  | **관리자/마이그레이션소스구성** |  |
> | 작업 | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 마이그레이션 원본 구성을 나열합니다(8000 시리즈만). |
> |  | **관리자/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/저장소 계정 자격 증명** |  |
> | 작업 | Microsoft.StorSimple/managers/storageAccountCredentials/write | Storage 계정 자격 증명을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/storageAccountCredentials/read | Storage 계정 자격 증명을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Storage 계정 자격 증명을 삭제합니다. |
> |  | **관리자/저장소계정 자격 증명/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **관리자/스토리지도메인** |  |
> | 작업 | Microsoft.StorSimple/managers/storageDomains/read | 스토리지 도메인을 나열하거나 가져옵니다. |
> | 작업 | Microsoft.StorSimple/managers/storageDomains/write | 스토리지 도메인을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.StorSimple/managers/storageDomains/delete | 스토리지 도메인을 삭제합니다. |
> |  | **관리자/저장소도메인/운영결과** |  |
> | 작업 | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.StorSimple/operations/read | 작업을 나열하거나 가져옵니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

Azure 서비스: [스트림 분석](../stream-analytics/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.StreamAnalytics/Register/action | 구독을 Stream Analytics 리소스 공급자에 등록합니다. |
> |  | **위치/할당량** |  |
> | 작업 | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics 구독 할당량을 읽습니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.StreamAnalytics/operations/Read | Stream Analytics 작업을 읽습니다. |
> |  | **streamingjobs** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics 작업을 삭제합니다. |
> | 작업 | 마이크로소프트.Stream애널리틱스/스트리밍작업/게시엣지패키지/액션 | 스트림 분석 작업에 대한 에지 패키지 게시 |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics 작업을 읽습니다. |
> | 작업 | 마이크로소프트.StreamAnalytics/스트리밍 작업/규모/작업 | 스케일 스트림 분석 작업 |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics 작업을 시작합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics 작업을 중지합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analytics 작업을 씁니다. |
> |  | **스트리밍 작업/기능** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics 작업 함수를 삭제합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics 작업 함수를 읽습니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Stream Analytics 작업 함수의 기본 정의를 검색합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics 작업 함수를 테스트합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Stream Analytics 작업 함수를 씁니다. |
> |  | **스트리밍 작업/기능/작업 결과** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Stream Analytics 작업 함수에 대한 작업 결과를 읽습니다. |
> |  | **스트리밍 작업/입력** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics 작업 입력을 삭제합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Stream Analytics 작업 입력을 읽습니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Stream Analytics 작업 입력을 샘플링합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics 작업 입력을 테스트합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Stream Analytics 작업 입력을 씁니다. |
> |  | **스트리밍 작업/입력/작업 결과** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Stream Analytics 작업 입력에 대한 작업 결과를 읽습니다. |
> |  | **스트리밍 작업/메트릭 정의** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 메트릭 정의를 읽습니다. |
> |  | **스트리밍 작업/작업 결과** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics 작업에 대한 작업 결과를 읽습니다. |
> |  | **스트리밍 작업/출력** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics 작업 출력을 삭제합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics 작업 출력을 읽습니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics 작업 출력을 테스트합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics 작업 출력을 씁니다. |
> |  | **스트리밍 작업/출력/작업 결과** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Stream Analytics 작업 출력에 대한 작업 결과를 읽습니다. |
> |  | **스트리밍작업/제공업체/마이크로소프트.인사이트/진단설정** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 진단 설정을 읽습니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 씁니다. |
> |  | **스트리밍작업/공급자/마이크로소프트.인사이트/로그정의** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | streamingjobs에 대해 사용 가능한 로그를 가져옵니다. |
> |  | **스트리밍작업/공급자/마이크로소프트.인사이트/메트릭정의** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | streamingjobs에 대해 사용 가능한 메트릭을 가져옵니다. |
> |  | **스트리밍작업/변환** |  |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics 작업 변환을 삭제합니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics 작업 변환을 읽습니다. |
> | 작업 | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Stream Analytics 작업 변환을 씁니다. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Subscription/CreateSubscription/action | Azure 구독을 만듭니다. |
> | 작업 | Microsoft.Subscription/register/action | Microsoft.Subscription 리소스 공급자에 구독을 등록합니다. |
> | 작업 | Microsoft.구독/취소/작업 | 구독 취소 |
> | 작업 | 마이크로소프트.구독/이름 바꾸기/작업 | 구독 이름 바꾸기 |
> |  | **SubscriptionDefinitions** |  |
> | 작업 | Microsoft.Subscription/SubscriptionDefinitions/read | 관리 그룹의 Azure 구독 정의를 가져옵니다. |
> | 작업 | Microsoft.Subscription/SubscriptionDefinitions/write | Azure 구독 정의를 만듭니다. |

## <a name="microsoftsupport"></a>Microsoft.Support

Azure 서비스: 코어

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.Support/register/action | 지원 리소스 공급자 등록 |
> | 작업 | 마이크로소프트.지원/확인이름가용성/작업 | 해당 이름이 유효하고 리소스 유형에 사용되지 않는지 확인합니다. |
> |  | **작업 결과** |  |
> | 작업 | 마이크로소프트.지원/작업 결과/읽기 | 작업 결과를 가져옵니다. |
> |  | **작업** |  |
> | 작업 | 마이크로소프트.지원/운영/읽기 | Microsoft.Support 리소스 공급자에서 사용할 수 있는 작업 나열 |
> |  | **작업 상태** |  |
> | 작업 | Microsoft.지원/운영 상태/읽기 | 작업 상태 가져오기 |
> |  | **서비스** |  |
> | 작업 | 마이크로소프트.지원/서비스/읽기 | 지원에 사용할 수 있는 Azure 서비스 목록 가져옵니다. |
> |  | **서비스/문제분류** |  |
> | 작업 | Microsoft.지원/서비스/문제 분류/읽기 | Azure 서비스에 사용할 수 있는 문제 분류 목록 가져옵니다. |
> |  | **지원 티켓** |  |
> | 작업 | Microsoft.Support/supportTickets/read | 지원 티켓 목록을 가져옵니다. |
> | 작업 | Microsoft.Support/supportTickets/write | 지원 티켓을 비동기적으로 만들거나 업데이트합니다. 기술, 청구, 할당량 또는 구독 관리 관련 문제에 대한 지원 티켓을 만들 수 있습니다. 기존 지원 티켓의 심각도 및 연락처 세부 정보를 업데이트할 수 있습니다. |
> |  | **지원티켓/통신** |  |
> | 작업 | Microsoft.지원/지원티켓/통신/읽기 | 지원 티켓 통신 목록 가져옵니다. |
> | 작업 | Microsoft.지원/지원티켓/통신/쓰기 | 지원 티켓 통신 생성 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

Azure 서비스: [타임시리즈 인사이트](../time-series-insights/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.TimeSeriesInsights/register/action | Time Series Insights 리소스 공급자에 대한 구독을 등록하고 Time Series Insights 환경을 만들도록 설정합니다. |
> |  | **환경** |  |
> | 작업 | Microsoft.TimeSeriesInsights/environments/read | 환경 속성을 가져옵니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/write | 새 환경을 만들거나 기존 환경을 업데이트합니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/delete | 환경을 삭제합니다. |
> |  | **환경/액세스 정책** |  |
> | 작업 | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 액세스 정책 속성을 가져옵니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 환경에 대한 새 액세스 정책을 만들거나 기존 액세스 정책을 업데이트합니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 액세스 정책을 삭제합니다. |
> |  | **environments/eventsources** |  |
> | 작업 | Microsoft.TimeSeriesInsights/environments/eventsources/read | 이벤트 원본 속성을 가져옵니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/eventsources/write | 환경에 대한 새 이벤트 원본을 만들거나 기존 이벤트 원본을 업데이트합니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 이벤트 원본을 삭제합니다. |
> |  | **environments/referencedatasets** |  |
> | 작업 | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 참조 데이터 집합의 속성을 가져옵니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 환경에 대한 새 참조 데이터 집합을 만들거나 기존 참조 데이터 집합을 업데이트합니다. |
> | 작업 | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 참조 데이터 집합을 삭제합니다. |
> |  | **환경/상태** |  |
> | 작업 | Microsoft.TimeSeriesInsights/environments/status/read | 환경의 상태 및 환경과 연결된 작업 상태(예: 수신)를 가져옵니다. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

Azure 서비스: [Azure DevOps](https://docs.microsoft.com/azure/devops/)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | Microsoft.VisualStudio/Register/Action | Azure 구독을 Microsoft.VisualStudio 공급자에 등록합니다. |
> |  | **계정** |  |
> | 작업 | Microsoft.VisualStudio/Account/Write | 계정을 설정합니다. |
> | 작업 | Microsoft.VisualStudio/Account/Delete | 계정을 삭제합니다. |
> | 작업 | Microsoft.VisualStudio/Account/Read | 계정을 읽습니다. |
> |  | **계정/확장** |  |
> | 작업 | Microsoft.VisualStudio/Account/Extension/Read | 계정/확장을 읽습니다. |
> |  | **계정/프로젝트** |  |
> | 작업 | Microsoft.VisualStudio/Account/Project/Read | 계정/프로젝트를 읽습니다. |
> | 작업 | Microsoft.VisualStudio/Account/Project/Write | 계정/프로젝트를 설정합니다. |
> |  | **확장** |  |
> | 작업 | Microsoft.VisualStudio/Extension/Write | 확장을 설정합니다. |
> | 작업 | Microsoft.VisualStudio/Extension/Delete | 확장을 삭제합니다. |
> | 작업 | Microsoft.VisualStudio/Extension/Read | 확장을 읽습니다. |
> |  | **Project** |  |
> | 작업 | Microsoft.VisualStudio/Project/Write | 프로젝트를 설정합니다. |
> | 작업 | Microsoft.VisualStudio/Project/Delete | 프로젝트를 삭제합니다. |
> | 작업 | Microsoft.VisualStudio/Project/Read | 프로젝트를 읽습니다. |

## <a name="microsoftweb"></a>microsoft.web

Azure 서비스: [앱 서비스,](../app-service/index.yml) [기능](../azure-functions/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> | 작업 | microsoft.web/unregister/action | 구독에 대한 Microsoft.Web 리소스 공급자를 등록 취소합니다. |
> | 작업 | microsoft.web/validate/action | 유효성 검사를 수행합니다. |
> | 작업 | microsoft.web/register/action | 구독에 대한 Microsoft.Web 리소스 공급자를 등록합니다. |
> | 작업 | microsoft.web/verifyhostingenvironmentvnet/action | 호스팅 환경 Vnet을 확인합니다. |
> |  | **apimanagement계정/아피아클** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apiacls/read | API Management 계정 Apiacls를 가져옵니다. |
> |  | **apimanagement계정/API** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apis/read | API Management 계정 API를 가져옵니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/delete | API Management 계정 API를 삭제합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/write | API Management 계정 API를 업데이트합니다. |
> |  | **apimanagement계정/API/apiacls** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apis/apiacls/delete | API Management 계정 API Apiacls를 삭제합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/apiacls/read | API Management 계정 API Apiacls를 가져옵니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/apiacls/write | API Management 계정 API Apiacls를 업데이트합니다. |
> |  | **apimanagement계정/APIS/커넥션아클** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connectionacls/read | API Management 계정 API Connectionacls를 가져옵니다. |
> |  | **apimanagement계정/APIS/연결** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/read | API Management 계정 API 연결을 가져옵니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 동의 코드 API Management 계정 API 연결을 확인합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/delete | API Management 계정 API 연결을 삭제합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | API Management 계정 API 연결에 대한 동의 링크를 가져옵니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/write | API Management 계정 API 연결을 업데이트합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 연결 키 API Management 계정 API 연결을 나열합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 비밀 API Management 계정 API 연결을 나열합니다. |
> |  | **apimanagement계정/APIS/연결/연결/연결** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | API Management 계정 API 연결 Connectionacls를 삭제합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | API Management 계정 API 연결 Connectionacls를 가져옵니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | API Management 계정 API 연결 Connectionacls를 업데이트합니다. |
> |  | **apimanagement계정/APIS/지역화된 정의** |  |
> | 작업 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | API Management 계정 API 지역화된 정의를 삭제합니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | API Management 계정 API 지역화된 정의를 가져옵니다. |
> | 작업 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | API Management 계정 API 지역화된 정의를 업데이트합니다. |
> |  | **apimanagement계정/커넥션아클** |  |
> | 작업 | microsoft.web/apimanagementaccounts/connectionacls/read | API Management 계정 Connectionacls를 가져옵니다. |
> |  | **사용 가능한 스택** |  |
> | 작업 | microsoft.web/availablestacks/read | 사용 가능한 스택을 가져옵니다. |
> |  | **인증서** |  |
> | 작업 | Microsoft.Web/certificates/Read | 인증서 목록을 가져옵니다. |
> | 작업 | Microsoft.Web/certificates/Write | 새 인증서를 추가하거나 기존 인증서를 업데이트합니다. |
> | 작업 | Microsoft.Web/certificates/Delete | 기존 인증서를 삭제합니다. |
> |  | **checknameavailability** |  |
> | 작업 | microsoft.web/checknameavailability/read | 리소스 이름이 사용 가능한지 확인합니다. |
> |  | **classicmobileservices** |  |
> | 작업 | microsoft.web/classicmobileservices/read | 클래식 Mobile Services를 가져옵니다. |
> |  | **connectionGateways** |  |
> | 작업 | Microsoft.Web/connectionGateways/Read | 연결 게이트웨이 목록을 가져옵니다. |
> | 작업 | Microsoft.Web/connectionGateways/Write | 연결 게이트웨이를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Web/connectionGateways/Delete | 연결 게이트웨이를 삭제합니다. |
> | 작업 | Microsoft.Web/connectionGateways/Move/Action | 연결 게이트웨이를 이동합니다. |
> | 작업 | Microsoft.Web/connectionGateways/Join/Action | 연결 게이트웨이를 연결합니다. |
> | 작업 | Microsoft.Web/connectionGateways/ListStatus/Action | 연결 게이트웨이 상태를 나열합니다. |
> |  | **연결** |  |
> | 작업 | Microsoft.Web/connections/Read | 연결 목록을 가져옵니다. |
> | 작업 | Microsoft.Web/connections/Write | 연결을 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Web/connections/Delete | 연결을 삭제합니다. |
> | 작업 | Microsoft.Web/connections/Move/Action | 연결을 이동합니다. |
> | 작업 | Microsoft.Web/connections/Join/Action | 연결을 연결합니다. |
> | 작업 | microsoft.web/connections/confirmconsentcode/action | 연결 동의 코드를 확인합니다. |
> | 작업 | microsoft.web/connections/listconsentlinks/action | 연결에 대한 동의 링크를 나열합니다. |
> |  | **사용자 정의 Apis** |  |
> | 작업 | Microsoft.Web/customApis/Read | 사용자 지정 API 목록을 가져옵니다. |
> | 작업 | Microsoft.Web/customApis/Write | 사용자 지정 API를 만들거나 업데이트합니다. |
> | 작업 | Microsoft.Web/customApis/Delete | 사용자 지정 API를 삭제합니다. |
> | 작업 | Microsoft.Web/customApis/Move/Action | 사용자 지정 API를 이동합니다. |
> | 작업 | Microsoft.Web/customApis/Join/Action | 사용자 지정 API를 조인합니다. |
> | 작업 | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | WSDL에서 API 정의를 추출합니다. |
> | 작업 | Microsoft.Web/customApis/listWsdlInterfaces/Action | 사용자 지정 API에 대한 WSDL 인터페이스를 나열합니다. |
> |  | **deletedSites** |  |
> | 작업 | Microsoft.Web/deletedSites/Read | 삭제된 웹앱의 속성을 가져옵니다. |
> |  | **배포 위치** |  |
> | 작업 | microsoft.web/deploymentlocations/read | 배포 위치를 가져옵니다. |
> |  | **지역 정보** |  |
> | 작업 | Microsoft.Web/geoRegions/Read | 지리적 영역 목록을 가져옵니다. |
> |  | **hostingEnvironments** |  |
> | 작업 | Microsoft.Web/hostingEnvironments/Read | App Service 환경의 속성을 가져옵니다. |
> | 작업 | Microsoft.Web/hostingEnvironments/Write | 새 App Service 환경을 만들거나 기존 App Service 환경을 업데이트합니다. |
> | 작업 | Microsoft.Web/hostingEnvironments/Delete | App Service 환경을 삭제합니다. |
> | 작업 | 마이크로소프트.웹/호스팅환경/조인/작업 | 앱 서비스 환경에 조인 |
> | 작업 | Microsoft.Web/hostingEnvironments/reboot/Action | App Service 환경의 모든 컴퓨터를 다시 부팅합니다. |
> | 작업 | 마이크로소프트.웹/호스팅환경/프라이빗 엔드포인트연결승인/액션 | 개인 엔드포인트 연결 승인 |
> | 작업 | microsoft.web/hostingenvironments/resume/action | 호스팅 환경을 계속합니다. |
> | 작업 | microsoft.web/hostingenvironments/suspend/action | 호스팅 환경을 일시 중단합니다. |
> |  | **호스팅환경/용량** |  |
> | 작업 | microsoft.web/hostingenvironments/capacities/read | 호스팅 환경 용량을 가져옵니다. |
> |  | **호스팅환경/검출기** |  |
> | 작업 | microsoft.web/hostingenvironments/detectors/read | 호스팅 환경 탐지기를 가져옵니다. |
> |  | **호스팅 환경/진단** |  |
> | 작업 | microsoft.web/hostingenvironments/diagnostics/read | 호스팅 환경 진단을 가져옵니다. |
> |  | **호스팅환경/이벤트그리드필터** |  |
> | 작업 | 마이크로소프트.웹/호스팅환경/이벤트그리드필터/삭제 | 호스팅 환경에서 이벤트 그리드 필터 삭제. |
> | 작업 | 마이크로소프트.웹/호스팅환경/이벤트그리드 필터/읽기 | 호스팅 환경에서 이벤트 그리드 필터를 가져옵니다. |
> | 작업 | 마이크로소프트.웹/호스팅환경/이벤트그리드필터/쓰기 | 호스팅 환경에 이벤트 그리드 필터를 배치합니다. |
> |  | **호스팅 환경/인바운드 네트워크 종속성** |  |
> | 작업 | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 모든 인바운드 종속성의 네트워크 엔드포인트를 가져옵니다. |
> |  | **호스팅 환경/메트릭 정의** |  |
> | 작업 | microsoft.web/hostingenvironments/metricdefinitions/read | 호스팅 환경 메트릭 정의를 가져옵니다. |
> |  | **hostingEnvironments/multiRolePools** |  |
> | 작업 | Microsoft.Web/hostingEnvironments/multiRolePools/Read | App Service 환경의 FrontEnd 풀 속성을 가져옵니다. |
> | 작업 | Microsoft.Web/hostingEnvironments/multiRolePools/Write | App Service 환경에 새 FrontEnd 풀을 만들거나 기존 FrontEnd 풀을 업데이트합니다. |
> |  | **호스팅 환경/다중 역할 풀/메트릭 정의** |  |
> | 작업 | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 호스팅 환경 MultiRole 풀 메트릭 정의를 가져옵니다. |
> |  | **호스팅 환경/다중 역할 풀/메트릭** |  |
> | 작업 | microsoft.web/hostingenvironments/multirolepools/metrics/read | 호스팅 환경 MultiRole 풀 메트릭을 가져옵니다. |
> |  | **호스팅환경/다기능 풀/스쿠스** |  |
> | 작업 | microsoft.web/hostingenvironments/multirolepools/skus/read | 호스팅 환경 MultiRole 풀 SKU를 가져옵니다. |
> |  | **호스팅 환경/다중 역할 풀/사용** |  |
> | 작업 | microsoft.web/hostingenvironments/multirolepools/usages/read | 호스팅 환경 MultiRole 풀 사용 현황을 가져옵니다. |
> |  | **호스팅 환경/운영** |  |
> | 작업 | microsoft.web/hostingenvironments/operations/read | 호스팅 환경 작업을 가져옵니다. |
> |  | **호스팅 환경/아웃바운드 네트워크 종속성** |  |
> | 작업 | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 모든 아웃바운드 종속성의 네트워크 엔드포인트를 가져옵니다. |
> |  | **호스팅 환경/서버 팜** |  |
> | 작업 | microsoft.web/hostingenvironments/serverfarms/read | 호스팅 환경 App Service 계획을 가져옵니다. |
> |  | **호스팅 환경/사이트** |  |
> | 작업 | microsoft.web/hostingenvironments/sites/read | 호스팅 환경 Web Apps를 가져옵니다. |
> |  | **호스팅 환경/사용** |  |
> | 작업 | microsoft.web/hostingenvironments/usages/read | 호스팅 환경 사용 현황을 가져옵니다. |
> |  | **hostingEnvironments/workerPools** |  |
> | 작업 | Microsoft.Web/hostingEnvironments/workerPools/Read | App Service 환경의 작업자 풀 속성을 가져옵니다. |
> | 작업 | Microsoft.Web/hostingEnvironments/workerPools/Write | App Service 환경에 새 작업자 풀을 만들거나 기존 작업자 풀을 업데이트합니다. |
> |  | **호스팅 환경/작업자 풀/메트릭 정의** |  |
> | 작업 | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 호스팅 환경 Workerpools 메트릭 정의를 가져옵니다. |
> |  | **호스팅 환경/작업자 풀/메트릭** |  |
> | 작업 | microsoft.web/hostingenvironments/workerpools/metrics/read | 호스팅 환경 Workerpools 메트릭을 가져옵니다. |
> |  | **호스팅 환경/작업자 풀/스쿠스** |  |
> | 작업 | microsoft.web/hostingenvironments/workerpools/skus/read | 호스팅 환경 Workerpools SKU를 가져옵니다. |
> |  | **호스팅 환경/작업자 풀/사용** |  |
> | 작업 | microsoft.web/hostingenvironments/workerpools/usages/read | 호스팅 환경 Workerpools 사용 현황을 가져옵니다. |
> |  | **ishosting환경 이름 사용 가능** |  |
> | 작업 | microsoft.web/ishostingenvironmentnameavailable/read | 호스팅 환경 이름을 사용할 수 있는지 여부를 가져옵니다. |
> |  | **ishostname사용 가능** |  |
> | 작업 | microsoft.web/ishostnameavailable/read | 호스트 이름을 사용할 수 있는지를 확인합니다. |
> |  | **isusername사용 가능** |  |
> | 작업 | microsoft.web/isusernameavailable/read | 사용자 이름을 사용할 수 있는지를 확인합니다. |
> |  | **목록사이트할당된ToHostName** |  |
> | 작업 | Microsoft.Web/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트의 이름을 가져옵니다. |
> |  | **위치** |  |
> | 작업 | microsoft.web/locations/extractapidefinitionfromwsdl/action | 위치에 대한 WSDL에서 Api 정의를 추출합니다. |
> | 작업 | microsoft.web/locations/listwsdlinterfaces/action | 위치에 대한 WSDL 인터페이스를 나열합니다. |
> | 작업 | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 위치에 대한 Vnet 또는 서브넷 삭제 알림입니다. |
> |  | **위치/apioperations** |  |
> | 작업 | microsoft.web/locations/apioperations/read | 위치 API 작업을 가져옵니다. |
> |  | **위치/연결 게이트웨이설치** |  |
> | 작업 | microsoft.web/locations/connectiongatewayinstallations/read | 위치 연결 게이트웨이 설치를 가져옵니다. |
> |  | **위치/관리** |  |
> | 작업 | microsoft.web/locations/managedapis/read | 위치 관리되는 AAPI를 가져옵니다. |
> | 작업 | Microsoft.Web/locations/managedapis/Join/Action | 관리되는 API를 조인합니다. |
> |  | **위치/관리형 apioperations** |  |
> | 작업 | microsoft.web/locations/managedapis/apioperations/read | 위치 관리되는 API 작업을 가져옵니다. |
> |  | **위치/작업결과** |  |
> | 작업 | 마이크로 소프트.웹 / 위치 / 작업결과 / 읽기 | 작업을 가져옵니다. |
> |  | **위치/작업** |  |
> | 작업 | 마이크로 소프트.웹 / 위치 / 작업 / 읽기 | 작업을 가져옵니다. |
> |  | **작업** |  |
> | 작업 | microsoft.web/operations/read | 작업을 가져옵니다. |
> |  | **게시 사용자** |  |
> | 작업 | microsoft.web/publishingusers/read | 게시 사용자를 가져옵니다. |
> | 작업 | microsoft.web/publishingusers/write | 게시 사용자를 업데이트합니다. |
> |  | **권장 사항** |  |
> | 작업 | Microsoft.Web/recommendations/Read | 구독에 대한 권장 사항 목록을 가져옵니다. |
> |  | **리소스 상태 메타데이터** |  |
> | 작업 | microsoft.web/resourcehealthmetadata/read | Resource Health 메타데이터를 가져옵니다. |
> |  | **서버 팜** |  |
> | 작업 | Microsoft.Web/serverfarms/Read | App Service 계획의 속성을 가져옵니다. |
> | 작업 | Microsoft.Web/serverfarms/Write | 새 App Service 계획을 만들거나 기존 App Service 계획을 업데이트합니다. |
> | 작업 | Microsoft.Web/serverfarms/Delete | 기존 App Service 계획 삭제 |
> | 작업 | Microsoft.Web/serverfarms/restartSites/Action | App Service 계획의 모든 Web Apps를 다시 시작합니다. |
> |  | **서버 팜/기능** |  |
> | 작업 | microsoft.web/serverfarms/capabilities/read | App Service 계획 기능을 가져옵니다. |
> |  | **서버 팜/이벤트그리드 필터** |  |
> | 작업 | 마이크로소프트.웹/서버 팜/이벤트그리드필터/삭제 | 서버 팜에서 이벤트 그리드 필터 삭제 |
> | 작업 | 마이크로소프트.웹/서버 팜/이벤트그리드 필터/읽기 | 서버 팜에서 이벤트 그리드 필터를 가져옵니다. |
> | 작업 | 마이크로소프트.웹/서버 팜/이벤트그리드 필터/쓰기 | 서버 팜에 이벤트 그리드 필터를 배치합니다. |
> |  | **서버 팜/퍼스트 파티 앱/설정** |  |
> | 작업 | microsoft.web/serverfarms/firstpartyapps/settings/delete | App Service 계획 자사 앱 설정을 삭제합니다. |
> | 작업 | microsoft.web/serverfarms/firstpartyapps/settings/read | App Service 계획 자사 앱 설정을 가져옵니다. |
> | 작업 | microsoft.web/serverfarms/firstpartyapps/settings/write | App Service 계획 자사 앱 설정을 업데이트합니다. |
> |  | **서버팜/하이브리드 연결네임스페이스/릴레이** |  |
> | 작업 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | App Service 계획 하이브리드 연결 네임스페이스 릴레이를 가져옵니다. |
> | 작업 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | App Service 계획 하이브리드 연결 네임스페이스 릴레이를 삭제합니다. |
> |  | **서버 팜/하이브리드 연결 네임스페이스/릴레이/사이트** |  |
> | 작업 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | App Service 계획 하이브리드 연결 네임스페이스 릴레이 Web Apps를 가져옵니다. |
> |  | **서버팜/하이브리드 연결 계획 제한** |  |
> | 작업 | microsoft.web/serverfarms/hybridconnectionplanlimits/read | App Service 계획 하이브리드 연결 계획 제한을 가져옵니다. |
> |  | **서버팜/하이브리드 연결 릴레이** |  |
> | 작업 | microsoft.web/serverfarms/hybridconnectionrelays/read | App Service 계획 하이브리드 연결 릴레이를 가져옵니다. |
> |  | **서버 팜/메트릭 정의** |  |
> | 작업 | microsoft.web/serverfarms/metricdefinitions/read | App Service 계획 메트릭 정의를 가져옵니다. |
> |  | **서버 팜/메트릭** |  |
> | 작업 | microsoft.web/serverfarms/metrics/read | App Service 계획 메트릭을 가져옵니다. |
> |  | **서버 팜/작업 결과** |  |
> | 작업 | microsoft.web/serverfarms/operationresults/read | App Service 계획 작업 결과를 가져옵니다. |
> |  | **서버 팜/사이트** |  |
> | 작업 | microsoft.web/serverfarms/sites/read | App Service 계획 Web Apps를 가져옵니다. |
> |  | **서버 팜/스쿠스** |  |
> | 작업 | microsoft.web/serverfarms/skus/read | App Service 계획 SKU를 가져옵니다. |
> |  | **서버 팜/사용** |  |
> | 작업 | microsoft.web/serverfarms/usages/read | App Service 계획 사용 현황을 가져옵니다. |
> |  | **서버 팜/가상 네트워크 연결** |  |
> | 작업 | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service 계획 Virtual Network 연결을 가져옵니다. |
> |  | **서버팜/가상 네트워크 연결/게이트웨이** |  |
> | 작업 | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service 계획 Virtual Network 연결 게이트웨이를 업데이트합니다. |
> |  | **서버 팜/가상 네트워크 연결/경로** |  |
> | 작업 | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | App Service 계획 Virtual Network 연결 경로를 삭제합니다. |
> | 작업 | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service 계획 Virtual Network 연결 경로를 가져옵니다. |
> | 작업 | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service 계획 Virtual Network 연결 경로를 업데이트합니다. |
> |  | **서버 팜/작업자** |  |
> | 작업 | microsoft.web/serverfarms/workers/reboot/action | App Service 계획 작업자를 다시 부팅합니다. |
> |  | **sites** |  |
> | 작업 | Microsoft.Web/sites/Read | 웹앱의 속성을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/Write | 새 웹앱을 만들거나 기존 웹앱을 업데이트합니다. |
> | 작업 | Microsoft.Web/sites/Delete | 기존 웹앱 삭제 |
> | 작업 | Microsoft.Web/sites/backup/Action | 새 웹앱 백업을 만듭니다. |
> | 작업 | Microsoft.Web/sites/publishxml/Action | 웹앱에 대한 게시 프로필 xml을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/publish/Action | 웹앱을 게시합니다. |
> | 작업 | Microsoft.Web/sites/restart/Action | 웹앱을 다시 시작합니다. |
> | 작업 | Microsoft.Web/sites/start/Action | 웹앱을 시작합니다. |
> | 작업 | Microsoft.Web/sites/stop/Action | 웹앱을 중지합니다. |
> | 작업 | Microsoft.Web/sites/slotsswap/Action | 웹앱 배포 슬롯을 교환합니다. |
> | 작업 | Microsoft.Web/sites/slotsdiffs/Action | 웹앱 및 슬롯 간 구성 차이를 가져옵니다. |
> | 작업 | Microsoft.Web/sites/applySlotConfig/Action | 대상 슬롯의 웹앱 슬롯 구성을 현재 웹앱에 적용합니다. |
> | 작업 | Microsoft.Web/sites/resetSlotConfig/Action | 웹앱 구성을 다시 설정합니다. |
> | 작업 | 마이크로소프트.웹/사이트/프라이빗 엔드포인트연결승인/액션 | 개인 엔드포인트 연결 승인 |
> | 작업 | microsoft.web/sites/functions/action | 합수 Web Apps입니다. |
> | 작업 | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 동기화 기능 트리거 상태를 나열합니다. |
> | 작업 | microsoft.web/sites/networktrace/action | 네트워크 추적 Web Apps입니다. |
> | 작업 | microsoft.web/sites/newpassword/action | Newpassword Web Apps입니다. |
> | 작업 | microsoft.web/sites/sync/action | Web Apps를 동기화합니다. |
> | 작업 | microsoft.web/sites/migratemysql/action | MySql Web Apps를 마이그레이션합니다. |
> | 작업 | microsoft.web/sites/recover/action | Web Apps을 복구합니다. |
> | 작업 | microsoft.web/sites/restoresnapshot/action | Web Apps 스냅샷을 복원합니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 복원에서삭제 앱 / 작업 | 삭제된 앱에서 Web Apps를 복원합니다. |
> | 작업 | microsoft.web/sites/syncfunctiontriggers/action | 동기화 기능이 트리거됩니다. |
> | 작업 | microsoft.web/sites/backups/action | Azure Storage의 Blob에서 복원할 수 있는 기존 앱 백업을 검색합니다. |
> | 작업 | microsoft.web/sites/containerlogs/action | Web App에 대한 압축된 컨테이너 로그를 가져옵니다. |
> | 작업 | microsoft.web/sites/restorefrombackupblob/action | 백업 Blob에서 웹앱을 복원합니다. |
> |  | **사이트/분석사용자 지정 호스트 이름** |  |
> | 작업 | microsoft.web/sites/analyzecustomhostname/read | 사용자 지정 호스트 이름을 분석합니다. |
> |  | **사이트/백업** |  |
> | 작업 | microsoft.web/sites/backup/read | Web Apps Backup을 가져옵니다. |
> | 작업 | microsoft.web/sites/backup/write | Web Apps Backup을 업데이트합니다. |
> |  | **사이트/백업** |  |
> | 작업 | Microsoft.Web/sites/backups/Read | 웹앱 백업의 속성을 가져옵니다. |
> | 작업 | microsoft.web/sites/backups/list/action | Web Apps Backup을 나열합니다. |
> | 작업 | microsoft.web/sites/backups/restore/action | Web Apps Backup을 복원합니다. |
> | 작업 | microsoft.web/sites/backups/delete | Web Apps Backup을 삭제합니다. |
> | 작업 | microsoft.web/sites/backups/write | Web Apps 백업을 업데이트합니다. |
> |  | **사이트/구성** |  |
> | 작업 | Microsoft.Web/sites/config/Read | 웹앱 구성 설정을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/config/list/Action | 게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 보안 중요 설정을 나열합니다. |
> | 작업 | Microsoft.Web/sites/config/Write | 웹앱 구성 설정을 업데이트합니다. |
> | 작업 | microsoft.web/sites/config/delete | Web Apps 구성을 삭제합니다. |
> |  | **사이트/구성/스냅샷** |  |
> | 작업 | microsoft.web/sites/config/snapshots/read | Web Apps 구성 스냅샷을 가져옵니다. |
> |  | **사이트/컨테이너 로그** |  |
> | 작업 | microsoft.web/sites/containerlogs/download/action | Web Apps 컨테이너 로그를 다운로드합니다. |
> |  | **사이트/연속 웹 작업** |  |
> | 작업 | microsoft.web/sites/continuouswebjobs/delete | Web Apps 연속 웹 작업을 삭제합니다. |
> | 작업 | microsoft.web/sites/continuouswebjobs/read | Web Apps 연속 웹 작업을 가져옵니다. |
> | 작업 | microsoft.web/sites/continuouswebjobs/start/action | Web Apps 연속 웹 작업을 시작합니다. |
> | 작업 | microsoft.web/sites/continuouswebjobs/stop/action | Web Apps 연속 웹 작업을 중지합니다. |
> |  | **사이트/배포** |  |
> | 작업 | microsoft.web/sites/deployments/delete | Web Apps 배포를 삭제합니다. |
> | 작업 | microsoft.web/sites/deployments/read | Web Apps 배포를 가져옵니다. |
> | 작업 | microsoft.web/sites/deployments/write | Web Apps 배포를 업데이트합니다. |
> |  | **사이트/배포/로그** |  |
> | 작업 | microsoft.web/sites/deployments/log/read | Web Apps 배포 로그를 가져옵니다. |
> |  | **사이트/감지기** |  |
> | 작업 | microsoft.web/sites/detectors/read | Web Apps 탐지기를 가져옵니다. |
> |  | **사이트/진단** |  |
> | 작업 | microsoft.web/sites/diagnostics/read | Web Apps 진단 로그 범주를 가져옵니다. |
> |  | **사이트/진단/분석** |  |
> | 작업 | microsoft.web/sites/diagnostics/analyses/read | Web Apps 진단 분석을 가져옵니다. |
> | 작업 | microsoft.web/sites/diagnostics/analyses/execute/Action | Web Apps 진단 분석을 실행합니다. |
> |  | **사이트/진단/아스넷코어** |  |
> | 작업 | microsoft.web/sites/diagnostics/aspnetcore/read | ASP.NET Core 앱용 Web Apps 진단을 가져옵니다. |
> |  | **사이트/진단/자가 치료** |  |
> | 작업 | microsoft.web/sites/diagnostics/autoheal/read | Web Apps 진단 Autoheal을 가져옵니다. |
> |  | **사이트/진단/배포** |  |
> | 작업 | microsoft.web/sites/diagnostics/deployment/read | Web Apps 진단 배포를 가져옵니다. |
> |  | **사이트/진단/배포** |  |
> | 작업 | microsoft.web/sites/diagnostics/deployments/read | Web Apps 진단 배포를 가져옵니다. |
> |  | **사이트/진단/검출기** |  |
> | 작업 | microsoft.web/sites/diagnostics/detectors/read | Web Apps 진단 감지기를 가져옵니다. |
> | 작업 | microsoft.web/sites/diagnostics/detectors/execute/Action | Web Apps 진단 감지기를 실행합니다. |
> |  | **사이트/진단/실패한 요청** |  |
> | 작업 | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Web Apps 진단 Uri당 실패한 요청을 가져옵니다. |
> |  | **사이트/진단/frebanalysis** |  |
> | 작업 | microsoft.web/sites/diagnostics/frebanalysis/read | Web Apps 진단 FREB 분석을 가져옵니다. |
> |  | **사이트/진단/로그분석기** |  |
> | 작업 | microsoft.web/sites/diagnostics/loganalyzer/read | Web Apps 진단 로그 분석기를 가져옵니다. |
> |  | **사이트/진단/런타임 가용성** |  |
> | 작업 | microsoft.web/sites/diagnostics/runtimeavailability/read | Web Apps 진단 런타임 가용성을 가져옵니다. |
> |  | **사이트/진단/서비스 상태** |  |
> | 작업 | microsoft.web/sites/diagnostics/servicehealth/read | Web Apps 진단 서비스 상태를 가져옵니다. |
> |  | **사이트/진단/사이트CPU분석** |  |
> | 작업 | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Web Apps 진단 사이트 CPU 분석을 가져옵니다. |
> |  | **사이트/진단/사이트 충돌** |  |
> | 작업 | microsoft.web/sites/diagnostics/sitecrashes/read | Web Apps 진단 사이트 크래시를 가져옵니다. |
> |  | **사이트/진단/사이트 지연** |  |
> | 작업 | microsoft.web/sites/diagnostics/sitelatency/read | Web Apps 진단 사이트 대기 시간을 가져옵니다. |
> |  | **사이트/진단/사이트 메모리 분석** |  |
> | 작업 | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Web Apps 진단 사이트 메모리 분석을 가져옵니다. |
> |  | **사이트/진단/사이트 다시 시작 설정업데이트** |  |
> | 작업 | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Web Apps 진단 사이트 다시 시작 설정 업데이트를 가져옵니다. |
> |  | **사이트/진단/사이트 재시작user** |  |
> | 작업 | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Web Apps 진단 사이트 시작 사용자 다시 시작을 가져옵니다. |
> |  | **사이트/진단/사이트 스왑** |  |
> | 작업 | microsoft.web/sites/diagnostics/siteswap/read | Web Apps 진단 사이트 스왑을 가져옵니다. |
> |  | **사이트/진단/스레드 수** |  |
> | 작업 | microsoft.web/sites/diagnostics/threadcount/read | Web Apps 진단 사이트 스레드 수를 가져옵니다. |
> |  | **사이트/진단/작업자 가용성** |  |
> | 작업 | microsoft.web/sites/diagnostics/workeravailability/read | Web Apps 진단 Workeravailability를 가져옵니다. |
> |  | **사이트/진단/작업자 프로세스 재활용** |  |
> | 작업 | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Web Apps 진단 작업자 프로세스 재활용을 가져옵니다. |
> |  | **사이트/도메인 소유권 식별자** |  |
> | 작업 | microsoft.web/sites/domainownershipidentifiers/read | Web Apps 도메인 소유권 식별자를 가져옵니다. |
> | 작업 | microsoft.web/sites/domainownershipidentifiers/write | Web Apps 도메인 소유권 식별자를 업데이트합니다. |
> |  | **사이트/이벤트그리드필터** |  |
> | 작업 | 마이크로소프트.웹/사이트/이벤트그리드필터/삭제 | 웹 앱에서 이벤트 그리드 필터 삭제. |
> | 작업 | 마이크로소프트.웹/사이트/이벤트그리드필터/읽기 | 웹 앱에서 이벤트 그리드 필터를 가져옵니다. |
> | 작업 | 마이크로소프트.웹/사이트/이벤트그리드필터/쓰기 | 웹 앱에 이벤트 그리드 필터를 배치합니다. |
> |  | **사이트/확장 프로그램** |  |
> | 작업 | 마이크로 소프트 웹 / 사이트 / 확장 / 삭제 | Web Apps 사이트 확장을 삭제합니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 확장 / 읽기 | Web Apps 사이트 확장을 가져옵니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 확장 / 쓰기 | Web Apps 사이트 확장을 업데이트합니다. |
> |  | **사이트/기능** |  |
> | 작업 | microsoft.web/sites/functions/delete | Web Apps 함수를 삭제합니다. |
> | 작업 | microsoft.web/sites/functions/listsecrets/action | 기능 비밀을 나열합니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 기능 / 목록 키 / 작업 | 기능 키를 나열합니다. |
> | 작업 | microsoft.web/sites/functions/read | Web Apps 함수를 가져옵니다. |
> | 작업 | microsoft.web/sites/functions/write | Web Apps 함수를 업데이트합니다. |
> |  | **사이트/기능/키** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 기능 / 키 / 쓰기 | 기능 키를 업데이트합니다. |
> | 작업 | 마이크로 소프트 웹 / 사이트 / 기능 / 키 / 삭제 | 기능 키를 삭제합니다. |
> |  | **사이트/기능/마스터키** |  |
> | 작업 | microsoft.web/sites/functions/masterkey/read | Web Apps 함수 Masterkey를 가져옵니다. |
> |  | **사이트/기능/속성** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 기능 / 속성 / 읽기 | 읽기 웹 앱 기능 속성입니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 기능 / 속성 / 쓰기 | 웹 앱 기능 속성 업데이트. |
> |  | **사이트/함수/토큰** |  |
> | 작업 | microsoft.web/sites/functions/token/read | Web Apps 함수 토큰을 가져옵니다. |
> |  | **사이트/호스트** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 호스트 / 목록 키 / 작업 | 기능 호스트 키를 나열합니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 호스트 / 동기화 / 작업 | 동기화 기능이 트리거됩니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 호스트 / 리스트 동기화 상태 / 작업 | 목록 동기화 기능은 상태를 트리거합니다. |
> |  | **사이트/호스트/함수 키** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 호스트 / 기능 키 / 쓰기 | 함수 호스트 함수 키를 업데이트합니다. |
> | 작업 | 마이크로 소프트 웹 / 사이트 / 호스트 / 기능 키 / 삭제 | 함수 호스트 함수 키 삭제합니다. |
> |  | **사이트/호스트/속성** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 호스트 / 속성 / 읽기 | 읽기 웹 앱 기능 호스트 속성입니다. |
> |  | **사이트/호스트/시스템 키** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 호스트 / 시스템 키 / 쓰기 | 업데이트 기능 호스트 시스템 키. |
> | 작업 | 마이크로 소프트 웹 / 사이트 / 호스트 / 시스템 키 / 삭제 | 함수 호스트 시스템 키 삭제합니다. |
> |  | **사이트/호스트 이름 바인딩** |  |
> | 작업 | microsoft.web/sites/hostnamebindings/delete | Web Apps 호스트 이름 바인딩을 삭제합니다. |
> | 작업 | microsoft.web/sites/hostnamebindings/read | Web Apps 호스트 이름 바인딩을 가져옵니다. |
> | 작업 | microsoft.web/sites/hostnamebindings/write | Web Apps 호스트 이름 바인딩을 업데이트합니다. |
> |  | **사이트/호스트 런타임** |  |
> | 작업 | Microsoft.Web/sites/hostruntime/host/action | 트리거 동기화, 함수 추가, 함수 호출, 함수 삭제 등과 같은 함수 앱 런타임 작업을 수행합니다. |
> |  | **사이트/호스트 런타임/기능/키** |  |
> | 작업 | microsoft.web/sites/hostruntime/functions/keys/read | Web Apps Hostruntime 함수 키를 가져옵니다. |
> |  | **사이트/호스트 런타임/호스트** |  |
> | 작업 | microsoft.web/sites/hostruntime/host/read | Web Apps Hostruntime 호스트를 가져옵니다. |
> |  | **사이트/호스트 런타임/호스트/_master** |  |
> | 작업 | Microsoft.Web/sites/hostruntime/host/_master/read | 관리 작업에 대한 함수 앱의 마스터 키를 가져옵니다. |
> |  | **사이트/하이브리드 연결** |  |
> | 작업 | microsoft.web/sites/hybridconnection/delete | Web Apps 하이브리드 연결을 삭제합니다. |
> | 작업 | microsoft.web/sites/hybridconnection/read | Web Apps 하이브리드 연결을 가져옵니다. |
> | 작업 | microsoft.web/sites/hybridconnection/write | Web Apps 하이브리드 연결을 업데이트합니다. |
> |  | **사이트/하이브리드 연결네임스페이스/릴레이** |  |
> | 작업 | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Web Apps 하이브리드 연결 네임스페이스 릴레이를 삭제합니다. |
> | 작업 | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 키 Web Apps 하이브리드 연결 네임스페이스 릴레이를 나열합니다. |
> | 작업 | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Web Apps 하이브리드 연결 네임스페이스 릴레이를 업데이트합니다. |
> | 작업 | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Web Apps 하이브리드 연결 네임스페이스 릴레이를 가져옵니다. |
> |  | **사이트/하이브리드 연결 릴레이** |  |
> | 작업 | microsoft.web/sites/hybridconnectionrelays/read | Web Apps 하이브리드 연결 릴레이를 가져옵니다. |
> |  | **sites/instances** |  |
> | 작업 | microsoft.web/sites/instances/read | Web Apps 인스턴스를 가져옵니다. |
> |  | **사이트/인스턴스/배포** |  |
> | 작업 | microsoft.web/sites/instances/deployments/read | Web Apps 인스턴스 배포를 가져옵니다. |
> | 작업 | microsoft.web/sites/instances/deployments/delete | Web Apps 인스턴스 배포를 삭제합니다. |
> |  | **sites/instances/extensions** |  |
> | 작업 | microsoft.web/sites/instances/extensions/read | Web Apps 인스턴스 확장을 가져옵니다. |
> |  | **사이트/인스턴스/확장/로그** |  |
> | 작업 | microsoft.web/sites/instances/extensions/log/read | Web Apps 인스턴스 확장 로그를 가져옵니다. |
> |  | **사이트/인스턴스/확장/프로세스** |  |
> | 작업 | microsoft.web/sites/instances/extensions/processes/read | Web Apps 인스턴스 확장 프로세스를 가져옵니다. |
> |  | **사이트/인스턴스/프로세스** |  |
> | 작업 | microsoft.web/sites/instances/processes/delete | Web Apps 인스턴스 프로세스를 삭제합니다. |
> | 작업 | microsoft.web/sites/instances/processes/read | Web Apps 인스턴스 프로세스를 가져옵니다. |
> |  | **사이트/인스턴스/프로세스/모듈** |  |
> | 작업 | microsoft.web/sites/instances/processes/modules/read | Web Apps 인스턴스 프로세스 모듈을 가져옵니다. |
> |  | **사이트/인스턴스/프로세스/스레드** |  |
> | 작업 | microsoft.web/sites/instances/processes/threads/read | Web Apps 인스턴스 프로세스 스레드를 가져옵니다. |
> |  | **사이트/메트릭 정의** |  |
> | 작업 | microsoft.web/sites/metricdefinitions/read | Web Apps 메트릭 정의를 가져옵니다. |
> |  | **sites/metrics** |  |
> | 작업 | microsoft.web/sites/metrics/read | Web Apps 메트릭을 가져옵니다. |
> |  | **사이트/메트릭 정의** |  |
> | 작업 | microsoft.web/sites/metricsdefinitions/read | Web Apps 메트릭 정의를 가져옵니다. |
> |  | **사이트/마이그레이션** |  |
> | 작업 | microsoft.web/sites/migratemysql/read | Web Apps 마이그레이션 MySql을 가져옵니다. |
> |  | **사이트/네트워크 추적/작업 결과** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 네트워크 추적 / 작업 결과 / 읽기 | 웹 앱 네트워크 추적 작업 결과를 가져옵니다. |
> |  | **사이트/작업 결과** |  |
> | 작업 | microsoft.web/sites/operationresults/read | Web Apps 작업 결과를 가져옵니다. |
> |  | **사이트/작업** |  |
> | 작업 | microsoft.web/sites/operations/read | Web Apps 작업을 가져옵니다. |
> |  | **사이트/퍼카운터** |  |
> | 작업 | microsoft.web/sites/perfcounters/read | Web Apps 성능 카운터를 가져옵니다. |
> |  | **sites/premieraddons** |  |
> | 작업 | microsoft.web/sites/premieraddons/delete | Web Apps 프리미어 추가 기능을 삭제합니다. |
> | 작업 | microsoft.web/sites/premieraddons/read | Web Apps 프리미어 추가 기능을 가져옵니다. |
> | 작업 | microsoft.web/sites/premieraddons/write | Web Apps 프리미어 추가 기능을 업데이트합니다. |
> |  | **사이트/개인 액세스** |  |
> | 작업 | microsoft.web/sites/privateaccess/read | 프라이빗 사이트 액세스 사용 및 사이트에 액세스할 수 있는 승인된 Virtual Networks 관련 데이터를 가져옵니다. |
> |  | **사이트/프로세스** |  |
> | 작업 | microsoft.web/sites/processes/read | Web Apps 프로세스를 가져옵니다. |
> |  | **사이트/프로세스/모듈** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 프로세스 / 모듈 / 읽기 | 웹 앱 프로세스 모듈을 가져옵니다. |
> |  | **사이트/프로세스/스레드** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 프로세스 / 스레드 / 읽기 | 웹 앱프로세스 스레드를 가져옵니다. |
> |  | **사이트/인증서** |  |
> | 작업 | microsoft.web/sites/publiccertificates/delete | Web Apps 공용 인증서를 삭제합니다. |
> | 작업 | microsoft.web/sites/publiccertificates/read | Web Apps 공용 인증서를 가져옵니다. |
> | 작업 | microsoft.web/sites/publiccertificates/write | Web Apps 공용 인증서를 업데이트합니다. |
> |  | **사이트/게시스ml** |  |
> | 작업 | microsoft.web/sites/publishxml/read | Web Apps 게시 XML을 가져옵니다. |
> |  | **사이트/추천역사** |  |
> | 작업 | microsoft.web/sites/recommendationhistory/read | Web Apps 권장 사항 기록을 가져옵니다. |
> |  | **sites/recommendations** |  |
> | 작업 | Microsoft.Web/sites/recommendations/Read | 웹앱에 대한 권장 사항 목록을 가져옵니다. |
> | 작업 | microsoft.web/sites/recommendations/disable/action | Web Apps 권장 사항을 사용하지 않도록 설정합니다. |
> |  | **사이트/리소스 상태 메타데이터** |  |
> | 작업 | microsoft.web/sites/resourcehealthmetadata/read | Web Apps Resource Health 메타데이터를 가져옵니다. |
> |  | **사이트/복원** |  |
> | 작업 | microsoft.web/sites/restore/read | Web Apps 복원을 가져옵니다. |
> | 작업 | microsoft.web/sites/restore/write | Web Apps을 복원합니다. |
> |  | **사이트/사이트 확장** |  |
> | 작업 | microsoft.web/sites/siteextensions/delete | Web Apps 사이트 확장을 삭제합니다. |
> | 작업 | microsoft.web/sites/siteextensions/read | Web Apps 사이트 확장을 가져옵니다. |
> | 작업 | microsoft.web/sites/siteextensions/write | Web Apps 사이트 확장을 업데이트합니다. |
> |  | **sites/slots** |  |
> | 작업 | Microsoft.Web/sites/slots/Write | 새 웹앱 슬롯을 만들거나 기존 웹앱 슬롯을 업데이트합니다. |
> | 작업 | Microsoft.Web/sites/slots/Delete | 기존 웹앱 슬롯을 삭제합니다. |
> | 작업 | Microsoft.Web/sites/slots/backup/Action | 새 웹앱 슬롯 백업을 만듭니다. |
> | 작업 | Microsoft.Web/sites/slots/publishxml/Action | 웹앱 슬롯에 대한 게시 프로필 xml을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/slots/publish/Action | 웹앱 슬롯을 게시합니다. |
> | 작업 | Microsoft.Web/sites/slots/restart/Action | 웹앱 슬롯을 다시 시작합니다. |
> | 작업 | Microsoft.Web/sites/slots/start/Action | 웹앱 슬롯을 시작합니다. |
> | 작업 | Microsoft.Web/sites/slots/stop/Action | 웹앱 슬롯을 중지합니다. |
> | 작업 | Microsoft.Web/sites/slots/slotsswap/Action | 웹앱 배포 슬롯을 교환합니다. |
> | 작업 | Microsoft.Web/sites/slots/slotsdiffs/Action | 웹앱 및 슬롯 간 구성 차이를 가져옵니다. |
> | 작업 | Microsoft.Web/sites/slots/applySlotConfig/Action | 대상 슬롯의 웹앱 슬롯 구성을 현재 슬롯에 적용합니다. |
> | 작업 | Microsoft.Web/sites/slots/resetSlotConfig/Action | 웹앱 슬롯 구성을 다시 설정합니다. |
> | 작업 | Microsoft.Web/sites/slots/Read | 웹앱 배포 슬롯의 속성을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps 슬롯입니다. |
> | 작업 | microsoft.web/sites/slots/sync/action | Web Apps 슬롯을 동기화합니다. |
> | 작업 | microsoft.web/sites/slots/networktrace/action | 네트워크 추적 Web Apps 슬롯입니다. |
> | 작업 | microsoft.web/sites/slots/recover/action | Web Apps 슬롯을 복구합니다. |
> | 작업 | microsoft.web/sites/slots/restoresnapshot/action | Web Apps 슬롯 스냅샷을 복원합니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 슬롯 / 복원에서삭제 된 응용 프로그램 / 작업 | 삭제된 앱에서 웹앱 슬롯을 복원합니다. |
> | 작업 | microsoft.web/sites/slots/backups/action | Web Apps 슬롯 백업을 검색합니다. |
> | 작업 | microsoft.web/sites/slots/containerlogs/action | 웹앱 슬롯에 대한 압축된 컨테이너 로그를 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/restorefrombackupblob/action | 백업 Blob에서 Web Apps 슬롯을 복원합니다. |
> |  | **사이트/슬롯/분석사용자 호스트 이름** |  |
> | 작업 | microsoft.web/sites/slots/analyzecustomhostname/read | Web Apps 슬롯 사용자 지정 호스트 이름 분석을 가져옵니다. |
> |  | **사이트/슬롯/백업** |  |
> | 작업 | microsoft.web/sites/slots/backup/write | Web Apps 슬롯 백업을 업데이트합니다. |
> | 작업 | microsoft.web/sites/slots/backup/read | Web Apps 슬롯 백업을 가져옵니다. |
> |  | **사이트/슬롯/백업** |  |
> | 작업 | Microsoft.Web/sites/slots/backups/Read | 웹앱 슬롯 백업의 속성을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/backups/list/action | Web Apps 슬롯 백업을 나열합니다. |
> | 작업 | microsoft.web/sites/slots/backups/restore/action | Web Apps 슬롯 백업을 복원합니다. |
> | 작업 | microsoft.web/sites/slots/backups/delete | Web Apps 슬롯 백업을 삭제합니다. |
> |  | **사이트/슬롯/구성** |  |
> | 작업 | Microsoft.Web/sites/slots/config/Read | 웹앱 슬롯의 구성 설정을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/slots/config/list/Action | 게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 슬롯 보안 중요 설정을 나열합니다. |
> | 작업 | Microsoft.Web/sites/slots/config/Write | 웹앱 슬롯의 구성 설정을 업데이트합니다. |
> | 작업 | microsoft.web/sites/slots/config/delete | Web Apps 슬롯 구성을 삭제합니다. |
> |  | **사이트/슬롯/컨테이너 로그** |  |
> | 작업 | microsoft.web/sites/slots/containerlogs/download/action | Web Apps 슬롯 컨테이너 로그를 다운로드합니다. |
> |  | **사이트/슬롯/연속웹작업** |  |
> | 작업 | microsoft.web/sites/slots/continuouswebjobs/delete | Web Apps 슬롯 연속 웹 작업을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/continuouswebjobs/read | Web Apps 슬롯 연속 웹 작업을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/continuouswebjobs/start/action | Web Apps 슬롯 연속 웹 작업을 시작합니다. |
> | 작업 | microsoft.web/sites/slots/continuouswebjobs/stop/action | Web Apps 슬롯 연속 웹 작업을 중지합니다. |
> |  | **사이트/슬롯/배포** |  |
> | 작업 | microsoft.web/sites/slots/deployments/delete | Web Apps 슬롯 배포를 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/deployments/read | Web Apps 슬롯 배포를 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/deployments/write | Web Apps 슬롯 배포를 업데이트합니다. |
> |  | **사이트/슬롯/배포/로그** |  |
> | 작업 | microsoft.web/sites/slots/deployments/log/read | Web Apps 슬롯 배포 로그를 가져옵니다. |
> |  | **사이트/슬롯/감지기** |  |
> | 작업 | microsoft.web/sites/slots/detectors/read | Web Apps 슬롯 감지기를 가져옵니다. |
> |  | **사이트/슬롯/진단** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/read | Web Apps 슬롯 진단을 가져옵니다. |
> |  | **사이트/슬롯/진단/분석** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/analyses/read | Web Apps 슬롯 진단 분석을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Web Apps 슬롯 진단 분석을 실행합니다. |
> |  | **사이트/슬롯/진단/아스넷코어** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/aspnetcore/read | ASP.NET Core 앱용 Web Apps 슬롯 진단을 가져옵니다. |
> |  | **사이트/슬롯/진단/자가 치료** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/autoheal/read | Web Apps 슬롯 진단 Autoheal을 가져옵니다. |
> |  | **사이트/슬롯/진단/배포** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/deployment/read | Web Apps 슬롯 진단 배포를 가져옵니다. |
> |  | **사이트/슬롯/진단/배포** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/deployments/read | Web Apps 슬롯 진단 배포를 가져옵니다. |
> |  | **사이트/슬롯/진단/검출기** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/detectors/read | Web Apps 슬롯 진단 감지기를 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Web Apps 슬롯 진단 감지기를 실행합니다. |
> |  | **사이트/슬롯/진단/frebanalysis** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Web Apps 슬롯 진단 FREB 분석을 가져옵니다. |
> |  | **사이트/슬롯/진단/로그분석기** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Web Apps 슬롯 진단 로그 분석기를 가져옵니다. |
> |  | **사이트/슬롯/진단/런타임 가용성** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Web Apps 슬롯 진단 런타임 가용성을 가져옵니다. |
> |  | **사이트/슬롯/진단/서비스 상태** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/servicehealth/read | Web Apps 슬롯 진단 서비스 상태를 가져옵니다. |
> |  | **사이트/슬롯/진단/sitecpuanalysis** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Web Apps 슬롯 진단 사이트 CPU 분석을 가져옵니다. |
> |  | **사이트/슬롯/진단/사이트 충돌** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Web Apps 슬롯 진단 사이트 크래시를 가져옵니다. |
> |  | **사이트/슬롯/진단/사이트 지연** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/sitelatency/read | Web Apps 슬롯 진단 사이트 대기 시간을 가져옵니다. |
> |  | **사이트/슬롯/진단/사이트 메모리 분석** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Web Apps 슬롯 진단 사이트 메모리 분석을 가져옵니다. |
> |  | **사이트 /슬롯 / 진단 / 사이트 다시 시작 업데이트** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Web Apps 슬롯 진단 사이트 다시 시작 설정 업데이트를 가져옵니다. |
> |  | **사이트 / 슬롯 / 진단 / 사이트 다시 시작userinitiated** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Web Apps 슬롯 진단 사이트 시작된 사용자 다시 시작을 가져옵니다. |
> |  | **사이트/슬롯/진단/사이트 스왑** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/siteswap/read | Web Apps 슬롯 진단 사이트 스왑을 가져옵니다. |
> |  | **사이트/슬롯/진단/스레드 수** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/threadcount/read | Web Apps 슬롯 진단 스레드 수를 가져옵니다. |
> |  | **사이트/슬롯/진단/작업자 가용성** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/workeravailability/read | Web Apps 슬롯 진단 Workeravailability를 가져옵니다. |
> |  | **사이트/슬롯/진단/작업자 프로세스 재활용** |  |
> | 작업 | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Web Apps 슬롯 진단 작업자 프로세스 재활용을 가져옵니다. |
> |  | **사이트/슬롯/도메인 소유권 식별자** |  |
> | 작업 | microsoft.web/sites/slots/domainownershipidentifiers/read | Web Apps 슬롯 도메인 소유권 식별자를 가져옵니다. |
> |  | **사이트/슬롯/확장** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 슬롯 / 확장 / 읽기 | 웹 앱 슬롯 확장 프로그램을 가져옵니다. |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 슬롯 / 확장 / 쓰기 | 웹 앱 슬롯 확장 을 업데이트합니다. |
> |  | **사이트/슬롯/기능** |  |
> | 작업 | microsoft.web/sites/slots/functions/read | Web Apps 슬롯 함수를 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/functions/listsecrets/action | 비밀 Web Apps 슬롯 함수를 나열합니다. |
> |  | **사이트/슬롯/호스트 네임 바인딩** |  |
> | 작업 | microsoft.web/sites/slots/hostnamebindings/delete | Web Apps 슬롯 호스트 이름 바인딩을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/hostnamebindings/read | Web Apps 슬롯 호스트 이름 바인딩을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/hostnamebindings/write | Web Apps 슬롯 호스트 이름 바인딩을 업데이트합니다. |
> |  | **사이트/슬롯/하이브리드 연결** |  |
> | 작업 | microsoft.web/sites/slots/hybridconnection/delete | Web Apps 슬롯 하이브리드 연결을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/hybridconnection/read | Web Apps 슬롯 하이브리드 연결을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/hybridconnection/write | Web Apps 슬롯 하이브리드 연결을 업데이트합니다. |
> |  | **사이트/슬롯/하이브리드연결네임스페이스/릴레이** |  |
> | 작업 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Web Apps 슬롯 하이브리드 연결 네임스페이스 릴레이를 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Web Apps 슬롯 하이브리드 연결 네임스페이스 릴레이를 업데이트합니다. |
> |  | **사이트/슬롯/하이브리드연결릴레이** |  |
> | 작업 | microsoft.web/sites/slots/hybridconnectionrelays/read | Web Apps 슬롯 하이브리드 연결 릴레이를 가져옵니다. |
> |  | **sites/slots/instances** |  |
> | 작업 | microsoft.web/sites/slots/instances/read | Web Apps 슬롯 인스턴스를 가져옵니다. |
> |  | **사이트/슬롯/인스턴스/배포** |  |
> | 작업 | microsoft.web/sites/slots/instances/deployments/read | Web Apps 슬롯 인스턴스 배포를 가져옵니다. |
> |  | **사이트/슬롯/인스턴스/프로세스** |  |
> | 작업 | microsoft.web/sites/slots/instances/processes/read | Web Apps 슬롯 인스턴스 프로세스를 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/instances/processes/delete | Web Apps 슬롯 인스턴스 프로세스를 삭제합니다. |
> |  | **사이트/슬롯/메트릭 정의** |  |
> | 작업 | microsoft.web/sites/slots/metricdefinitions/read | Web Apps 슬롯 메트릭 정의를 가져옵니다. |
> |  | **sites/slots/metrics** |  |
> | 작업 | microsoft.web/sites/slots/metrics/read | Web Apps 슬롯 메트릭을 가져옵니다. |
> |  | **사이트 /슬롯 / migratemysql** |  |
> | 작업 | microsoft.web/sites/slots/migratemysql/read | Web Apps 슬롯 마이그레이션 MySql을 가져옵니다. |
> |  | **사이트/슬롯/네트워크 추적/작업 결과** |  |
> | 작업 | 마이크로 소프트.웹 / 사이트 / 슬롯 / 네트워크 추적 / 작업 결과 / 읽기 | 웹 앱 슬롯 네트워크 추적 작업 결과 가져옵니다. |
> |  | **사이트/슬롯/작업 결과** |  |
> | 작업 | microsoft.web/sites/slots/operationresults/read | Web Apps 슬롯 작업 결과를 가져옵니다. |
> |  | **사이트/슬롯/작업** |  |
> | 작업 | microsoft.web/sites/slots/operations/read | Web Apps 슬롯 작업을 가져옵니다. |
> |  | **사이트/슬롯/퍼카운터** |  |
> | 작업 | microsoft.web/sites/slots/perfcounters/read | Web Apps 슬롯 성능 카운터를 가져옵니다. |
> |  | **사이트/슬롯/프블로깅** |  |
> | 작업 | microsoft.web/sites/slots/phplogging/read | Web Apps 슬롯 Phplogging을 가져옵니다. |
> |  | **사이트/슬롯/프리미어 애드온** |  |
> | 작업 | microsoft.web/sites/slots/premieraddons/delete | Web Apps 슬롯 프리미어 추가 기능을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/premieraddons/read | Web Apps 슬롯 프리미어 추가 기능을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/premieraddons/write | Web Apps 슬롯 프리미어 추가 기능을 업데이트합니다. |
> |  | **사이트/슬롯/프로세스** |  |
> | 작업 | microsoft.web/sites/slots/processes/read | Web Apps 슬롯 프로세스를 가져옵니다. |
> |  | **사이트/슬롯/공공 인증서** |  |
> | 작업 | microsoft.web/sites/slots/publiccertificates/read | Web Apps 슬롯 공용 인증서를 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/publiccertificates/write | Web Apps 슬롯 공용 인증서를 만들거나 업데이트합니다. |
> | 작업 | microsoft.web/sites/slots/publiccertificates/delete | Web Apps 슬롯 공용 인증서를 삭제합니다. |
> |  | **사이트/슬롯/리소스 상태 메타데이터** |  |
> | 작업 | microsoft.web/sites/slots/resourcehealthmetadata/read | Web Apps 슬롯 Resource Health 메타데이터를 가져옵니다. |
> |  | **사이트/슬롯/복원** |  |
> | 작업 | microsoft.web/sites/slots/restore/read | Web Apps 슬롯 복원을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/restore/write | Web Apps 슬롯을 복원합니다. |
> |  | **사이트/슬롯/사이트 확장** |  |
> | 작업 | microsoft.web/sites/slots/siteextensions/delete | Web Apps 슬롯 사이트 확장을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/siteextensions/read | Web Apps 슬롯 사이트 확장을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/siteextensions/write | Web Apps 슬롯 사이트 확장을 업데이트합니다. |
> |  | **사이트/슬롯/스냅샷** |  |
> | 작업 | microsoft.web/sites/slots/snapshots/read | Web Apps 슬롯 스냅샷을 가져옵니다. |
> |  | **사이트/슬롯/소스 컨트롤** |  |
> | 작업 | Microsoft.Web/sites/slots/sourcecontrols/Read | 웹앱 슬롯의 소스 제어 구성 설정을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/slots/sourcecontrols/Write | 웹앱 슬롯의 소스 제어 구성 설정을 업데이트합니다. |
> | 작업 | Microsoft.Web/sites/slots/sourcecontrols/Delete | 웹앱 슬롯의 소스 제어 구성 설정을 삭제합니다. |
> |  | **사이트/슬롯/트리거된 웹작업** |  |
> | 작업 | microsoft.web/sites/slots/triggeredwebjobs/delete | Web Apps 슬롯 트리거 Webjob을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/triggeredwebjobs/read | Web Apps 슬롯 트리거 Webjob을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/triggeredwebjobs/run/action | Web Apps 슬롯 트리거 Webjob을 실행합니다. |
> |  | **사이트/슬롯/사용** |  |
> | 작업 | microsoft.web/sites/slots/usages/read | Web Apps 슬롯 사용 현황을 가져옵니다. |
> |  | **사이트/슬롯/가상 네트워크 연결** |  |
> | 작업 | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web Apps 슬롯 Virtual Network 연결을 삭제합니다. |
> | 작업 | microsoft.web/sites/slots/virtualnetworkconnections/read | Web Apps 슬롯 Virtual Network 연결을 가져옵니다. |
> | 작업 | microsoft.web/sites/slots/virtualnetworkconnections/write | Web Apps 슬롯 Virtual Network 연결을 업데이트합니다. |
> |  | **사이트/슬롯/가상 네트워크 연결/게이트웨이** |  |
> | 작업 | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Web Apps 슬롯 Virtual Network 연결 게이트웨이를 업데이트합니다. |
> |  | **사이트/슬롯/웹작업** |  |
> | 작업 | microsoft.web/sites/slots/webjobs/read | Web Apps 슬롯 Webjob을 가져옵니다. |
> |  | **사이트/스냅샷** |  |
> | 작업 | microsoft.web/sites/snapshots/read | Web Apps 스냅샷을 가져옵니다. |
> |  | **사이트/소스 컨트롤** |  |
> | 작업 | Microsoft.Web/sites/sourcecontrols/Read | 웹앱의 소스 제어 구성 설정을 가져옵니다. |
> | 작업 | Microsoft.Web/sites/sourcecontrols/Write | 웹앱의 소스 제어 구성 설정을 업데이트합니다. |
> | 작업 | Microsoft.Web/sites/sourcecontrols/Delete | 웹앱의 소스 제어 구성 설정을 삭제합니다. |
> |  | **사이트/트리거된 웹작업** |  |
> | 작업 | microsoft.web/sites/triggeredwebjobs/delete | Web Apps 트리거 Webjob을 삭제합니다. |
> | 작업 | microsoft.web/sites/triggeredwebjobs/read | Web Apps 트리거 Webjob을 가져옵니다. |
> | 작업 | microsoft.web/sites/triggeredwebjobs/run/action | Web Apps 트리거 Webjob을 실행합니다. |
> |  | **사이트/트리거된 웹작업/기록** |  |
> | 작업 | microsoft.web/sites/triggeredwebjobs/history/read | Web Apps 트리거 WebJobs 기록을 가져옵니다. |
> |  | **사이트/사용** |  |
> | 작업 | microsoft.web/sites/usages/read | Web Apps 사용 현황을 가져옵니다. |
> |  | **사이트/가상 네트워크 연결** |  |
> | 작업 | microsoft.web/sites/virtualnetworkconnections/delete | Web Apps Virtual Network 연결을 삭제합니다. |
> | 작업 | microsoft.web/sites/virtualnetworkconnections/read | Web Apps Virtual Network 연결을 가져옵니다. |
> | 작업 | microsoft.web/sites/virtualnetworkconnections/write | Web Apps Virtual Network 연결을 업데이트합니다. |
> |  | **사이트/가상 네트워크 연결/게이트웨이** |  |
> | 작업 | microsoft.web/sites/virtualnetworkconnections/gateways/read | Web Apps Virtual Network 연결 게이트웨이를 가져옵니다. |
> | 작업 | microsoft.web/sites/virtualnetworkconnections/gateways/write | Web Apps Virtual Network 연결 게이트웨이를 업데이트합니다. |
> |  | **사이트/웹 작업** |  |
> | 작업 | microsoft.web/sites/webjobs/read | Web Apps Webjob을 가져옵니다. |
> |  | **Sku** |  |
> | 작업 | microsoft.web/skus/read | SKU를 가져옵니다. |
> |  | **소스 컨트롤** |  |
> | 작업 | microsoft.web/sourcecontrols/read | 소스 제어를 가져옵니다. |
> | 작업 | microsoft.web/sourcecontrols/write | 소스 제어를 업데이트합니다. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

Azure 서비스: [Azure 모니터](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업(Operation) | Description |
> | --- | --- | --- |
> |  | **구성 요소** |  |
> | 작업 | Microsoft.WorkloadMonitor/components/read | 리소스에 대한 구성 요소를 가져옵니다. |
> |  | **componentsSummary** |  |
> | 작업 | Microsoft.WorkloadMonitor/componentsSummary/read | 구성 요소의 요약을 가져옵니다. |
> |  | **monitorInstances** |  |
> | 작업 | Microsoft.WorkloadMonitor/monitorInstances/read | 리소스에 대한 모니터의 인스턴스를 가져옵니다. |
> |  | **monitorInstancesSummary** |  |
> | 작업 | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | 모니터 인스턴스의 요약을 가져옵니다. |
> |  | **모니터** |  |
> | 작업 | Microsoft.WorkloadMonitor/monitors/read | 리소스에 대한 모니터를 가져옵니다. |
> | 작업 | Microsoft.WorkloadMonitor/monitors/write | 리소스에 대한 모니터를 구성합니다. |
> |  | **notificationSettings** |  |
> | 작업 | Microsoft.WorkloadMonitor/notificationSettings/read | 리소스에 대한 알림 설정을 가져옵니다. |
> | 작업 | Microsoft.WorkloadMonitor/notificationSettings/write | 리소스에 대한 알림 설정을 구성합니다. |
> |  | **작업** |  |
> | 작업 | Microsoft.WorkloadMonitor/operations/read | 지원되는 작업을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

- [리소스 공급자를 서비스로 일치시다](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 사용자 지정 역할](custom-roles.md)
- [Azure 기본 제공 역할](built-in-roles.md)
