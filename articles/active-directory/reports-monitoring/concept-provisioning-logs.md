---
title: Azure Active Directory 포털의 로그 프로비저닝(미리 보기) | 마이크로 소프트 문서
description: Azure Active Directory 포털의 프로비저닝 활동 보고서 소개
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113362"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Azure Active Directory 포털에서 보고서 프로비저닝(미리 보기)

Azure AD(Azure Active Directory)의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **활동** 
    - **로그인** – 관리되는 애플리케이션 및 사용자 로그인 활동의 사용량에 대한 정보입니다.
    - **감사 로그** - [감사 로그는](concept-audit-logs.md) 사용자 및 그룹 관리, 관리되는 응용 프로그램 및 디렉터리 활동에 대한 시스템 활동 정보를 제공합니다.
    - **프로비전 로그** - Azure AD 프로비저닝 서비스에서 프로비전되는 사용자, 그룹 및 역할에 대한 시스템 활동을 제공합니다. 

- **보안** 
    - **위험한 로그인** - [위험한 로그인은](concept-risky-sign-ins.md) 사용자 계정의 합법적인 소유자가 아닌 사람이 수행했을 수 있는 로그인 시도를 나타내는 지표입니다.
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](concept-user-at-risk.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이 항목에서는 프로비저닝 보고서에 대한 개요를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?
* 보안 관리자, 보안 리더, 보고서 판독기, 응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자 역할의 사용자
* 글로벌 관리자


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>프로비저닝 활동에 액세스하려면 어떤 Azure AD 라이선스가 필요합니까?

테넌트는 모든 최대 프로비저닝 활동 보고서를 보려면 테넌트가 연결된 Azure AD Premium 라이선스를 가지고 있어야 합니다. [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요. 

## <a name="provisioning-logs"></a>프로비저닝 로그

프로비저닝 로그는 다음 질문에 대한 답변을 제공합니다.

* ServiceNow에서 어떤 그룹이 성공적으로 만들어졌습니까?
* Amazon 웹 서비스에서 역할을 가져온 방법은 무엇입니까?
* DropBox에서 어떤 사용자가 성공적으로 생성되지 못했나요?

[Azure 포털의](https://portal.azure.com) **Azure Active Directory** 블레이드의 **모니터링** 섹션에서 **프로비저닝 로그를 선택하여 프로비저닝** 로그에 액세스할 수 있습니다. 일부 프로비저닝 레코드가 포털에 표시되는 데 최대 2시간이 걸릴 수 있습니다.

![프로비저닝 로그](./media/concept-provisioning-logs/access-provisioning-logs.png "프로비저닝 로그")


프로비저닝 로그에는 다음을 보여 주는 기본 목록 보기가 있습니다.

- ID
- 이 작업은
- 소스 시스템
- 대상 시스템
- 상태는
- 날짜


![기본 열](./media/concept-provisioning-logs/default-columns.png "기본 열")

도구 모음에서 열을 클릭하여 목록 보기를 사용자 지정할 수 **있습니다.**

![열 선택기](./media/concept-provisioning-logs/column-chooser.png "열 선택기")

그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![사용 가능한 열](./media/concept-provisioning-logs/available-columns.png "사용 가능한 열")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![자세한 정보](./media/concept-provisioning-logs/steps.png "Assert")


## <a name="filter-provisioning-activities"></a>프로비저닝 활동 필터링

프로비저닝 데이터를 필터링할 수 있습니다. 일부 필터 값은 테넌트에 따라 동적으로 채워집니다. 예를 들어 테넌트에 만들기 이벤트가 없는 경우 만들기에 대한 필터 옵션이 없습니다.
기본 보기에서 다음 필터를 선택할 수 있습니다.

- ID
- Date
- 상태
- 작업


![필터](./media/concept-provisioning-logs/default-filter.png "Assert")

**ID** 필터를 사용하면 관심 있는 이름이나 ID를 지정할 수 있습니다. 이 ID는 사용자, 그룹, 역할 또는 기타 개체일 수 있습니다. 개체의 이름이나 ID로 검색할 수 있습니다. ID는 시나리오에 따라 다릅니다. 예를 들어 Azure AD에서 SalesForce로 개체를 프로비전할 때 소스 ID는 Azure AD에 있는 사용자의 개체 ID이며 TargetID는 Salesforce의 사용자의 ID입니다. Workday에서 Active 디렉터리로 프로비전할 때 소스 ID는 Workday 작업자 직원 ID입니다. 사용자 이름이 ID 열에 항상 있는 것은 아닙니다. 항상 하나의 ID가 있을 것입니다. 


**날짜** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.  
가능한 값은 다음과 같습니다.

- 1개월
- 7 일
- 30일
- 24시간
- 사용자 지정 시간 간격

사용자 지정 시간 프레임을 선택하면 시작 날짜와 종료 날짜를 구성할 수 있습니다.


**상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- 모두
- Success
- 실패
- 건너뜀



**작업** 필터를 사용하면 다음을 필터링할 수 있습니다.

- 생성 
- 업데이트
- DELETE
- 사용 안 함
- 기타

또한 기본 보기의 필터에 다음 필터를 설정할 수도 있습니다.

- 작업 ID
- 사이클 ID
- ID 변경
- 원본 ID
- 대상 ID
- 애플리케이션


![필드 선택](./media/concept-provisioning-logs/add-filter.png "필드 선택")


- **작업 ID** - 고유한 작업 ID는 프로비저닝을 사용하도록 설정한 각 응용 프로그램과 연결됩니다.   

- **주기 ID** - 프로비저닝 주기를 고유하게 식별합니다. 이 ID를 공유하여 이 이벤트가 발생한 주기를 조회할 수 있습니다.

- **ID 변경** - 프로비저닝 이벤트에 대한 고유 식별자입니다. 이 ID를 공유하여 프로비저닝 이벤트를 조회할 수 있습니다.   


- **원본 시스템** - ID가 프로비전되는 위치를 지정할 수 있습니다. 예를 들어 Azure AD에서 ServiceNow로 개체를 프로비전할 때 원본 시스템은 Azure AD입니다. 

- **대상 시스템** - ID가 프로비전되는 위치를 지정할 수 있습니다. 예를 들어 Azure AD에서 ServiceNow로 개체를 프로비전할 때 대상 시스템은 ServiceNow입니다. 

- **응용 프로그램** - 특정 문자열을 포함하는 표시 이름으로 응용 프로그램의 레코드만 표시할 수 있습니다.

 

## <a name="provisioning-details"></a>프로비저닝 세부 정보 

프로비저닝 목록 보기에서 항목을 선택하면 이 항목에 대한 자세한 정보를 얻을 수 있습니다.
세부 정보는 다음 범주에 따라 그룹화됩니다.

- 단계

- 문제 해결 및 권장 사항

- 수정된 속성

- 요약


![필터](./media/concept-provisioning-logs/provisioning-tabs.png "탭")



### <a name="steps"></a>단계

**단계** 탭에는 개체를 프로비전하기 위해 수행된 단계가 간략하게 설명되어 있습니다. 개체 프로비전은 다음 네 단계로 구성될 수 있습니다. 

- 개체 가져오기
- 개체가 범위에 있는지 확인
- 소스와 대상 간의 오브젝트 일치
- 프로비전 객체(작업 수행 - 생성, 업데이트, 삭제 또는 비활성화될 수 있음)



![필터](./media/concept-provisioning-logs/steps.png "Assert")


### <a name="troubleshoot-and-recommendations"></a>문제 해결 및 권장 사항


**문제 해결 및 권장 사항** 탭은 오류 코드와 이유를 제공합니다. 오류 정보는 오류가 발생한 경우에만 사용할 수 있습니다. 


### <a name="modified-properties"></a>수정된 속성

**수정된 속성은** 이전 값과 새 값을 표시합니다. 이전 값이 없는 경우 이전 값 열이 비어 있습니다. 


### <a name="summary"></a>요약

**요약** 탭은 소스 및 대상 시스템의 개체에 대해 발생한 작업과 식별자에 대한 개요를 제공합니다. 

## <a name="what-you-should-know"></a>알아야 할 사항

- Azure 포털 저장소는 프리미엄 버전이 있는 경우 30일 동안 프로비저닝 데이터를 보고하고 무료 버전이 있는 경우 7일 동안 보고했습니다.

- ID 변경 특성을 고유 식별자로 사용할 수 있습니다. 예를 들어 제품 지원과 상호 작용할 때 유용합니다.

- 현재 프로비저닝 데이터를 다운로드할 수 있는 옵션은 없습니다.

- 현재 로그 분석에 대한 지원이 없습니다.

- 앱컨텍스트에서 프로비저닝 로그에 액세스하면 감사 로그가 수행하는 방식으로 특정 앱으로 이벤트를 자동으로 필터링하지 않습니다.

## <a name="error-codes"></a>오류 코드

프로비저닝 로그에서 찾을 수 있는 오류를 해결하는 방법을 더 잘 이해하려면 아래 표를 사용합니다. 누락된 오류 코드의 경우 이 페이지 하단의 링크를 사용하여 피드백을 제공합니다. 

|오류 코드|Description|
|---|---|
|충돌, 항목 충돌|Azure AD 또는 응용 프로그램에서 충돌하는 특성 값을 수정하거나 충돌하는 사용자 계정이 일치하고 인계되어야 하는 경우 일치하는 특성 구성을 검토합니다. 일치하는 특성 구성에 대한 자세한 내용은 다음 [설명서를](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 참조하십시오.|
|TooManyRequests|대상 앱이 오버로드되고 너무 많은 요청을 수신하기 때문에 사용자를 업데이트하려는 이 시도를 거부했습니다. 할 일이 없습니다. 이 시도는 자동으로 사용 중지됩니다. Microsoft는 이 문제에 대해서도 통보받았습니다.|
|InternalServerError |대상 앱에서 예기치 않은 오류를 반환했습니다. 대상 응용 프로그램에 서비스 문제가 있어 작동하지 않을 수 있습니다. 이 시도는 40분 만에 자동으로 사용 중지됩니다.|
|불충분한 권리, 방법 허용되지 않음, 허용되지 않음, 무단| Azure AD는 대상 응용 프로그램으로 인증할 수 있었지만 업데이트를 수행할 수 있는 권한이 없습니다. 대상 응용 프로그램뿐만 아니라 해당 응용 프로그램 [자습서에서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)제공하는 지침을 검토하십시오.|
|처리 할 수없는 엔티티|대상 응용 프로그램이 예기치 않은 응답을 반환했습니다. 대상 응용 프로그램의 구성이 올바르지 않거나 대상 응용 프로그램에 서비스 문제가 발생하여 작동하지 않을 수 있습니다.|
|웹예외프로토콜오류 |대상 응용 프로그램에 연결하는 동안 HTTP 프로토콜 오류가 발생했습니다. 할 일이 없습니다. 이 시도는 40분 만에 자동으로 사용 중지됩니다.|
|유효하지 않음앵커|프로비저닝 서비스에서 이전에 만들거나 일치시킨 사용자는 더 이상 존재하지 않습니다. 사용자가 있는지 확인합니다. 모든 사용자의 재일치를 강제하려면 MS Graph API를 사용하여 [작업을 다시 시작합니다.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 프로비저닝을 다시 시작하면 초기 주기가 트리거되며 완료하는 데 시간이 걸릴 수 있습니다. 또한 프로비저닝 서비스가 작동하는 데 사용하는 캐시를 삭제하므로 테넌트의 모든 사용자와 그룹을 다시 평가해야 하며 특정 프로비저닝 이벤트가 삭제될 수 있습니다.|
|NotImplemented | 대상 앱에서 예기치 않은 응답을 반환했습니다. 앱의 구성이 올바르지 않거나 대상 앱에 서비스 문제가 발생하여 작동하지 않을 수 있습니다. 대상 응용 프로그램뿐만 아니라 해당 응용 프로그램 [자습서에서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)제공하는 지침을 검토하십시오. |
|필수필드누락, 누락된값 |필요한 값이 없기 때문에 사용자를 만들 수 없습니다. 원본 레코드에서 누락된 특성 값을 수정하거나 일치하는 특성 구성을 검토하여 필요한 필드가 생략되지 않았는지 확인합니다. 일치하는 특성 구성에 대해 [자세히 알아보세요.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)|
|스키마속성NotNot |대상 응용 프로그램에 없는 특성을 지정했기 때문에 작업을 수행할 수 없습니다. 특성 사용자 지정에 대한 [설명서를](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 참조하고 구성이 올바른지 확인합니다.|
|InternalError |Azure AD 프로비저닝 서비스 내에서 내부 서비스 오류가 발생했습니다. 할 일이 없습니다. 이 시도는 40분 만에 자동으로 다시 시도됩니다.|
|InvalidDomain |잘못된 도메인 이름을 포함하는 특성 값으로 인해 작업을 수행할 수 없습니다. 사용자의 도메인 이름을 업데이트하거나 대상 응용 프로그램의 허용목록에 추가합니다. |
|시간 제한 |대상 응용 프로그램이 응답하는 데 시간이 너무 오래 걸리므로 작업을 완료할 수 없습니다. 할 일이 없습니다. 이 시도는 40분 만에 자동으로 다시 시도됩니다.|
|라이선스제한 초과|이 사용자에 사용할 수 있는 라이선스가 없기 때문에 대상 응용 프로그램에서 사용자를 만들 수 없습니다. 대상 응용 프로그램에 대한 추가 라이선스를 조달하거나 사용자 할당 및 특성 매핑 구성을 검토하여 올바른 사용자에게 올바른 특성이 할당되었는지 확인합니다.|
|중복 대상 항목  |대상 응용 프로그램에서 구성된 일치 특성을 가진 두 명 이상의 사용자를 찾았기 때문에 작업을 완료할 수 없습니다. 대상 응용 프로그램에서 중복 사용자를 제거하거나 [여기에](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)설명된 대로 특성 매핑을 다시 구성합니다.|
|중복 소스항목 | 구성된 일치 특성을 가진 두 명 이상의 사용자를 찾았기 때문에 작업을 완료할 수 없습니다. 중복 된 사용자를 제거하거나 [여기에](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)설명된 대로 특성 매핑을 다시 구성합니다.|

## <a name="next-steps"></a>다음 단계

* [사용자 프로비저닝 상태 확인](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


