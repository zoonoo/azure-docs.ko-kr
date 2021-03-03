---
title: Azure Portal의 프로 비전 로그 개요 (미리 보기) | Microsoft Docs
description: Azure Portal를 통해 Azure Active Directory에서 로그 보고서를 프로 비전 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 284b554581534a8493225fba0b70b074fb7dd982
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651940"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Azure Portal의 프로 비전 로그 개요 (미리 보기)

Azure AD(Azure Active Directory)의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- 활동: 
    - **로그인**: 관리 되는 응용 프로그램 및 사용자 로그인 활동 사용에 대 한 정보입니다.
    - [감사 로그](concept-audit-logs.md): 사용자 및 그룹 관리, 관리 되는 응용 프로그램 및 디렉터리 작업에 대 한 시스템 활동 정보입니다.
    - **프로 비전 로그**: Azure AD 프로 비전 서비스에서 프로 비전 하는 사용자, 그룹 및 역할에 대 한 시스템 활동입니다. 

- 보안: 
    - **위험한 로그인**: [위험한 로그인](../identity-protection/overview-identity-protection.md) 은 사용자 계정의 합법적인 소유자가 아닌 사용자가 수행 했을 수 있는 로그인 시도에 대 한 표시기입니다.
    - **위험 플래그가 지정 된 사용자**: [위험한 사용자](../identity-protection/overview-identity-protection.md) 는 손상 되었을 수 있는 사용자 계정에 대 한 표시기입니다.

이 항목에서는 프로 비전 로그의 개요를 제공 합니다. 로그는 다음과 같은 질문에 대 한 답변을 제공 합니다. 

* ServiceNow에서 성공적으로 생성 된 그룹은 무엇 인가요?
* Adobe에서 성공적으로 제거 된 사용자는 무엇입니까?
* Active Directory에서 만든 Workday의 사용자는 무엇 인가요? 

## <a name="prerequisites"></a>필수 구성 요소

이러한 사용자는 프로 비전 로그의 데이터에 액세스할 수 있습니다.

* 응용 프로그램 소유자 (자체 응용 프로그램에 대 한 로그)
* 보안 관리자, 보안 읽기 권한자, 보고서 구독자, 보안 운영자, 응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자 역할의 사용자
* [ProvisioningLogs 권한이](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions) 있는 사용자 지정 역할의 사용자
* 전역 관리자


프로 비전 활동 보고서를 보려면 테 넌 트에 Azure AD Premium 라이선스가 연결 되어 있어야 합니다. Azure AD 버전을 업그레이드 하려면 [Azure Active Directory Premium 시작](../fundamentals/active-directory-get-started-premium.md)을 참조 하세요. 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>프로 비전 로그와 상호 작용 하는 방법 
고객은 다음 네 가지 방법으로 프로 비전 로그와 상호 작용할 수 있습니다.

- 다음 섹션에 설명 된 대로 Azure Portal에서 로그에 액세스 합니다.
- 프로 비전 로그를 [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md)으로 스트리밍합니다. 이 메서드를 사용 하 여 데이터 보존을 확장 하 고 사용자 지정 대시보드, 경고 및 쿼리를 작성할 수 있습니다.
- 프로 비전 로그에 대 한 [MICROSOFT GRAPH API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) 를 쿼리 합니다.
- 프로 비전 로그를 CSV 또는 JSON 파일로 다운로드 합니다.

## <a name="access-the-logs-from-the-azure-portal"></a>Azure Portal에서 로그에 액세스 합니다.
[Azure Portal](https://portal.azure.com) **Azure Active Directory** 창의 **모니터링** 섹션에서 **프로 비전** 로그를 선택 하 여 프로 비전 로그에 액세스할 수 있습니다. 일부 프로 비전 레코드가 포털에 표시 되는 데 최대 2 시간이 걸릴 수 있습니다.

![프로 비전 로그에 액세스 하기 위한 선택 항목을 보여 주는 스크린샷](./media/concept-provisioning-logs/access-provisioning-logs.png "프로비저닝 로그")


프로 비전 로그에는 다음을 보여 주는 기본 목록 보기가 있습니다.

- Id
- 작업
- 원본 시스템
- 대상 시스템
- 상태
- 날짜


![프로 비전 로그의 기본 열을 보여 주는 스크린샷](./media/concept-provisioning-logs/default-columns.png "기본 열")

도구 모음에서 **열** 을 선택 하 여 목록 뷰를 사용자 지정할 수 있습니다.

![열을 사용자 지정 하는 단추를 보여 주는 스크린샷](./media/concept-provisioning-logs/column-chooser.png "열 선택")

이 영역에서는 추가 필드를 표시 하거나 이미 표시 된 필드를 제거할 수 있습니다.

![일부 선택 된 상태에서 사용 가능한 열을 보여 주는 스크린샷](./media/concept-provisioning-logs/available-columns.png "사용 가능한 열")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![자세한 정보를 보여 주는 스크린샷](./media/concept-provisioning-logs/steps.png "Assert")


## <a name="filter-provisioning-activities"></a>프로 비전 작업 필터링

프로 비전 데이터를 필터링 할 수 있습니다. 일부 필터 값은 테 넌 트를 기반으로 동적으로 채워집니다. 예를 들어 테 넌 트에 "create" 이벤트가 없는 경우 필터 **만들기** 옵션이 없습니다.

기본 보기에서 다음 필터를 선택할 수 있습니다.

- **ID**
- **날짜**
- **상태**
- **작업**


![필터 값을 보여 주는 스크린샷](./media/concept-provisioning-logs/default-filter.png "Assert")

**Id** 필터를 사용 하면 관심 있는 이름이 나 id를 지정할 수 있습니다. 이 id는 사용자, 그룹, 역할 또는 다른 개체 일 수 있습니다. 

개체의 이름 또는 ID를 기준으로 검색할 수 있습니다. ID는 시나리오에 따라 달라 집니다. 예를 들어 Azure AD에서 Salesforce로 개체를 프로 비전 하는 경우 원본 ID는 Azure AD에서 사용자의 개체 ID입니다. 대상 ID는 Salesforce의 사용자 ID입니다. Workday에서 Active Directory로 프로 비전 하는 경우 원본 ID는 Workday 작업자 직원 ID입니다. 

> [!NOTE]
> 사용자 이름은 항상 **id** 열에 표시 되지 않을 수 있습니다. 항상 하나의 ID가 있습니다. 


**날짜** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다. 가능한 값은 다음과 같습니다.

- 1개월
- 7 일
- 30일
- 24시간
- 사용자 지정 시간 간격

사용자 지정 시간 프레임을 선택 하는 경우 시작 날짜와 종료 날짜를 구성할 수 있습니다.

**상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- **모두**
- **Success**
- **실패**
- **생략**

**작업** 필터를 사용 하 여 다음 작업을 필터링 할 수 있습니다.

- **만들기** 
- **업데이트**
- **Delete**
- **사용 안 함**
- **기타**

기본 보기의 필터 외에도 다음 필터를 설정할 수 있습니다.

![필터로 추가할 수 있는 필드를 보여 주는 스크린샷](./media/concept-provisioning-logs/add-filter.png "필드 선택")

- **작업 id**: 고유한 작업 id는 프로 비전을 사용 하도록 설정한 각 응용 프로그램과 연결 됩니다.   

- **주기 id**: 주기 id는 프로 비전 주기를 고유 하 게 식별 합니다. 이 ID를 제품 지원과 공유 하 여이 이벤트가 발생 한 주기를 조회할 수 있습니다.

- **변경 id**: 변경 id는 프로 비전 이벤트의 고유 식별자입니다. 이 ID를 제품 지원과 공유 하 여 프로 비전 이벤트를 조회할 수 있습니다.   

- **원본 시스템**: id가 프로 비전 되는 위치를 지정할 수 있습니다. 예를 들어 Azure AD에서 ServiceNow로 개체를 프로 비전 하는 경우 원본 시스템은 Azure AD입니다. 

- **대상 시스템**: id가 프로 비전 되는 위치를 지정할 수 있습니다. 예를 들어 Azure AD에서 ServiceNow로 개체를 프로 비전 하는 경우 대상 시스템은 ServiceNow입니다. 

- **응용 프로그램**: 특정 문자열을 포함 하는 표시 이름으로 응용 프로그램의 레코드만 표시할 수 있습니다.

## <a name="provisioning-details"></a>프로 비전 세부 정보 

프로 비전 목록 보기에서 항목을 선택 하면이 항목에 대 한 자세한 정보를 볼 수 있습니다. 세부 정보는 다음 탭으로 그룹화 됩니다.

![프로 비전 세부 정보를 포함 하는 네 개의 탭을 보여 주는 스크린샷](./media/concept-provisioning-logs/provisioning-tabs.png "탭")

- **단계**: 개체를 프로 비전 하는 데 필요한 단계를 간략하게 설명 합니다. 개체 프로 비전은 다음 네 단계로 구성 될 수 있습니다.
  
  1. 개체를 가져옵니다.
  1. 개체가 범위 내에 있는지 여부를 확인 합니다.
  1. 원본과 대상 간의 개체를 일치 시킵니다.
  1. 개체 (만들기, 업데이트, 삭제 또는 사용 안 함)를 프로 비전 합니다.

  ![스크린샷 단계 탭의 프로 비전 단계를 보여 줍니다.](./media/concept-provisioning-logs/steps.png "Assert")

- **& 권장 사항 문제 해결**: 오류 코드 및 이유를 제공 합니다. 오류 정보는 오류가 발생 하는 경우에만 사용할 수 있습니다.

- **수정 된 속성**: 이전 값과 새 값을 표시 합니다. 이전 값이 없는 경우에는 해당 열이 비어 있습니다.

- **요약**: 원본 및 대상 시스템에서 개체의 변경 된 내용 및 식별자에 대 한 개요를 제공 합니다.

## <a name="download-logs-as-csv-or-json"></a>로그를 CSV 또는 JSON으로 다운로드

Azure Portal에서 로그로 이동 하 고 **다운로드** 를 선택 하 여 나중에 사용할 프로 비전 로그를 다운로드할 수 있습니다. 선택한 필터 조건에 따라 파일이 필터링 됩니다. 다운로드 크기와 시간을 줄이기 위해 필터를 가능한 한 구체적으로 만듭니다. 

CSV 다운로드에는 다음 세 개의 파일이 포함 됩니다.

* **ProvisioningLogs**: 프로 비전 단계와 수정 된 속성을 제외 하 고 모든 로그를 다운로드 합니다.
* **ProvisioningLogs_ProvisioningSteps**: 프로 비전 단계와 변경 ID를 포함 합니다. 변경 ID를 사용 하 여 이벤트를 다른 두 파일에 조인할 수 있습니다.
* **ProvisioningLogs_ModifiedProperties**: 변경 된 특성과 변경 ID를 포함 합니다. 변경 ID를 사용 하 여 이벤트를 다른 두 파일에 조인할 수 있습니다.

#### <a name="open-the-json-file"></a>JSON 파일 열기
JSON 파일을 열려면 [Microsoft Visual Studio 코드](https://aka.ms/vscode)와 같은 텍스트 편집기를 사용 합니다. Visual Studio Code 구문 강조 표시를 제공 하 여 파일을 더 쉽게 읽을 수 있도록 합니다. [Microsoft Edge](https://aka.ms/msedge)와 같은 편집할 수 없는 형식으로 브라우저를 사용 하 여 JSON 파일을 열 수도 있습니다. 

#### <a name="prettify-the-json-file"></a>JSON 파일 꾸밀
JSON 파일은 다운로드 크기를 줄이기 위해 파일 형식으로 다운로드 됩니다. 이 형식을 사용 하면 페이로드를 읽기가 어려울 수 있습니다. 다음 두 가지 옵션을 확인 하 여 파일을 꾸밀.

- [JSON 형식을 지정 하려면 Visual Studio Code을](https://code.visualstudio.com/docs/languages/json#_formatting)사용 합니다.

- PowerShell을 사용 하 여 JSON 형식을 지정 합니다. 이 스크립트는 탭과 공백을 포함 하는 형식으로 JSON을 출력 합니다. 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>JSON 파일 구문 분석

PowerShell을 사용 하 여 JSON 파일을 사용할 수 있는 몇 가지 샘플 명령은 다음과 같습니다. 편안 하 게 사용할 수 있는 모든 프로그래밍 언어를 사용할 수 있습니다.  

먼저 다음 명령을 실행 하 여 [JSON 파일을 읽습니다](/powershell/module/microsoft.powershell.utility/convertfrom-json?view=powershell-7.1) .

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

이제 시나리오에 따라 데이터를 구문 분석할 수 있습니다. 다음은 몇 가지 예입니다. 

- JSON 파일에 모든 작업 Id를 출력 합니다.

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- 작업이 "만들기" 인 이벤트의 모든 변경 Id를 출력 합니다.

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>알아야 할 사항

보고서를 프로 비전 하기 위한 몇 가지 팁과 고려 사항은 다음과 같습니다.

- 이 Azure Portal는 premium edition이 있는 경우 30 일 동안 보고 된 프로 비전 데이터를 저장 하 고 무료 버전이 있는 경우 7 일 동안 저장 합니다. 프로 비전 로그는 30 일 넘게 보존 하기 위해 [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) 에 게시할 수 있습니다. 

- 변경 ID 특성을 고유 식별자로 사용할 수 있습니다. 예를 들어 제품 지원과 상호 작용 하는 경우에 유용 합니다.

- 범위에 없는 사용자에 대 한 건너뛴 이벤트를 볼 수 있습니다. 이는 특히 동기화 범위가 모든 사용자 및 그룹으로 설정 된 경우에 필요 합니다. 서비스는 범위를 벗어난 모든 개체를 포함 하 여 테 넌 트의 모든 개체를 평가 합니다. 

- 프로 비전 로그는 현재 정부 클라우드에서 사용할 수 없습니다. 프로 비전 로그에 액세스할 수 없는 경우 감사 로그를 임시 해결 방법으로 사용 합니다. 

- 프로 비전 로그는 역할 가져오기를 표시 하지 않습니다 (AWS, Salesforce 및 Zendesk에 적용 됨). 감사 로그에서 역할 가져오기에 대 한 로그를 찾을 수 있습니다. 

## <a name="error-codes"></a>오류 코드

다음 표를 사용 하 여 프로 비전 로그에서 발견 한 오류를 해결 하는 방법을 보다 잘 이해할 수 있습니다. 누락 된 오류 코드의 경우이 페이지의 맨 아래에 있는 링크를 사용 하 여 피드백을 제공 합니다. 

|오류 코드|Description|
|---|---|
|충돌, EntryConflict|Azure AD 또는 응용 프로그램에서 충돌 하는 특성 값을 수정 합니다. 또는 충돌 하는 사용자 계정이 일치 하 고 수행 되어야 하는 경우 일치 하는 특성 구성을 검토 합니다. 일치 특성을 구성 하는 방법에 대 한 자세한 내용은 [설명서](../app-provisioning/customize-application-attributes.md) 를 참조 하십시오.|
|TooManyRequests|오버 로드 되어 너무 많은 요청을 받고 있으므로 대상 앱에서 사용자를 업데이트 하려는 시도를 거부 했습니다. 수행할 작업이 없습니다. 이 시도는 자동으로 사용 중지 됩니다. Microsoft도이 문제에 대해 알려줍니다.|
|InternalServerError |대상 앱에서 예기치 않은 오류를 반환 했습니다. 대상 응용 프로그램의 서비스 문제로 인해이 작업이 작동 하지 않을 수 있습니다. 이 시도는 자동으로 40 분 후에 사용 중지 됩니다.|
|InsufficientRights, MethodNotAllowed, NotPermitted, 권한 없음| Azure AD가 대상 응용 프로그램으로 인증 되었지만 업데이트를 수행할 수 있는 권한이 없습니다. 해당 하는 응용 프로그램 [자습서](../saas-apps/tutorial-list.md)와 함께 대상 응용 프로그램이 제공한 모든 지침을 검토 합니다.|
|UnprocessableEntity|대상 응용 프로그램에서 예기치 않은 응답을 반환 했습니다. 대상 응용 프로그램의 구성이 올바르지 않거나 대상 응용 프로그램의 서비스 문제로 인해이 작업이 제대로 작동 하지 않을 수 있습니다.|
|WebExceptionProtocolError |대상 응용 프로그램에 연결 하는 동안 HTTP 프로토콜 오류가 발생 했습니다. 수행할 작업이 없습니다. 이 시도는 자동으로 40 분 후에 사용 중지 됩니다.|
|InvalidAnchor|프로 비전 서비스에서 이전에 만들었거나 일치 한 사용자가 더 이상 존재 하지 않습니다. 사용자가 존재 하는지 확인 합니다. 모든 사용자의 새 일치를 적용 하려면 Microsoft Graph API를 사용 하 여 [작업을 다시 시작](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)합니다. <br><br>프로 비전을 다시 시작 하면 초기 주기가 트리거되고이를 완료 하는 데 시간이 걸릴 수 있습니다. 프로 비전을 다시 시작 하면 프로 비전 서비스에서 작동 하는 데 사용 하는 캐시도 삭제 됩니다. 즉, 테 넌 트의 모든 사용자와 그룹을 다시 평가 하 고 특정 프로 비전 이벤트를 삭제할 수 있습니다.|
|NotImplemented | 대상 앱에서 예기치 않은 응답을 반환 했습니다. 앱 구성이 올바르지 않거나 대상 앱의 서비스 문제로 인해이 작업이 제대로 작동 하지 않을 수 있습니다. 해당 하는 응용 프로그램 [자습서](../saas-apps/tutorial-list.md)와 함께 대상 응용 프로그램이 제공한 모든 지침을 검토 합니다. |
|MandatoryFieldsMissing, MissingValues |필요한 값이 없으므로 사용자를 만들 수 없습니다. 원본 레코드에서 누락 된 특성 값을 수정 하거나 일치 하는 특성 구성을 검토 하 여 필수 필드가 생략 되지 않도록 하십시오. 일치 하는 특성 구성에 [대해 자세히 알아보세요](../app-provisioning/customize-application-attributes.md) .|
|SchemaAttributeNotFound |대상 응용 프로그램에 존재 하지 않는 특성이 지정 되었으므로 작업을 수행할 수 없습니다. 특성 사용자 지정에 대 한 [설명서](../app-provisioning/customize-application-attributes.md) 를 참조 하 여 구성이 올바른지 확인 합니다.|
|InternalError |Azure AD 프로 비전 서비스 내에서 내부 서비스 오류가 발생 했습니다. 수행할 작업이 없습니다. 이 시도는 40 분 내에 자동으로 다시 시도 됩니다.|
|InvalidDomain |특성 값에 잘못 된 도메인 이름이 있으므로 작업을 수행할 수 없습니다. 사용자의 도메인 이름을 업데이트 하거나 대상 응용 프로그램의 허용 목록에 추가 합니다. |
|제한 시간 |대상 응용 프로그램에서 응답 하는 데 너무 오래 걸려 작업을 완료할 수 없습니다. 수행할 작업이 없습니다. 이 시도는 40 분 내에 자동으로 다시 시도 됩니다.|
|LicenseLimitExceeded|이 사용자에 대해 사용할 수 있는 라이선스가 없어서 대상 응용 프로그램에서 사용자를 만들 수 없습니다. 대상 응용 프로그램에 대 한 더 많은 라이선스를 조달 합니다. 또는 사용자 할당 및 특성 매핑 구성을 검토 하 여 올바른 사용자가 올바른 특성으로 할당 되었는지 확인 합니다.|
|DuplicateTargetEntries  |구성 된 일치 특성을 사용 하 여 대상 응용 프로그램에서 둘 이상의 사용자를 찾았지만 작업을 완료할 수 없습니다. 대상 응용 프로그램에서 중복 사용자를 제거 하거나 [특성 매핑을 다시 구성](../app-provisioning/customize-application-attributes.md)하십시오.|
|DuplicateSourceEntries | 구성 된 일치 특성을 가진 사용자를 둘 이상 찾았지만 작업을 완료할 수 없습니다. 중복 사용자를 제거 하거나 [특성 매핑을 다시 구성](../app-provisioning/customize-application-attributes.md)하십시오.|
|ImportSkipped | 각 사용자가 평가 되 면 시스템은 원본 시스템에서 사용자를 가져오려고 시도 합니다. 이 오류는 일반적으로 가져올 사용자에 게 특성 매핑에 정의 된 일치 속성이 없는 경우에 발생 합니다. 사용자 개체에 일치 하는 특성에 대 한 값이 없는 경우 시스템은 범위 지정, 일치 또는 변경 내용 내보내기를 평가할 수 없습니다. 이 오류가 있는 경우 사용자의 범위를 아직 평가 하지 않았기 때문에 사용자가 범위 내에 있음을 나타내는 것은 아닙니다.|
|EntrySynchronizationSkipped | 프로 비전 서비스에서 원본 시스템을 쿼리하고 사용자를 식별 했습니다. 사용자에 대 한 추가 작업을 수행 하지 않았으므로 건너뛰었습니다. 사용자가 범위를 벗어난 것일 수도 있고, 사용자가 더 이상 변경 하지 않아도 대상 시스템에 이미 있었던 것일 수도 있습니다.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| 사용자 또는 그룹을 검색 하는 GET 요청은 응답에 여러 사용자 또는 그룹을 받았습니다. 시스템은 응답에서 사용자 또는 그룹을 하나만 받게 됩니다. [예](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)를 들어 GET 요청을 수행 하 여 그룹을 검색 하 고 멤버를 제외 하는 필터를 제공 하는 경우 Scim (도메인 간 id 관리) 끝점에 대 한 시스템에서 해당 멤버를 반환 하면이 오류가 발생 합니다.|

## <a name="next-steps"></a>다음 단계

* [사용자 프로 비전 상태를 확인 합니다.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](../app-provisioning/application-provisioning-config-problem.md)
* [프로 비전 로그를 위한 Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)