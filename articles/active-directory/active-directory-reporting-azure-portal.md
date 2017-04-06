---
title: "Azure Active Directory Reporting - 미리 보기 | Microsoft Docs"
description: "Azure Active Directory Reporting 미리 보기에 사용할 수 있는 다양한 보고서를 나열합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: be986fd7bb1745dcf43a1066dfabc1e1c699ab4c
ms.openlocfilehash: b9cd11954a52600c1cd50155cb7ce9b7d2355cd3
ms.lasthandoff: 02/16/2017


---
# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory Reporting - 미리 보기


*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

Azure Active Directory 미리 보기에서 리포팅으로 사용자 환경의 작동 방법을 결정하는 데 필요한 모든 정보를 얻을 수 있습니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md)

보고의 두 가지 주요 영역이 있습니다.

* **로그인 활동** – 관리되는 응용 프로그램 및 사용자 로그인 활동의 사용량에 대한 정보
* **감사 로그** - 사용자 및 그룹 관리, 관리되는 응용 프로그램 및 디렉터리 활동에 대한 시스템 활동 정보

원하는 데이터의 범위에 따라 [Azure Portal](https://portal.azure.com)의 서비스 목록에서 **사용자 및 그룹** 또는 **엔터프라이즈 응용 프로그램**을 클릭하여 이러한 보고서에 액세스할 수 있습니다.

## <a name="sign-in-activities"></a>로그인 활동
### <a name="user-sign-in-activities"></a>사용자 로그인 활동
보고서에서 사용자 로그인에 의해 제공되는 정보를 사용하여 다음과 같은 질문에 대한 대답을 찾습니다.

* 사용자의 로그인 패턴이란?
* 한 주 동안 얼마나 많은 사용자가 로그인했나요?
* 이러한 로그인의 상태란?

이 데이터에 대한 진입점은 **사용자 및 그룹**의 **개요** 섹션에서 사용자 로그인 그래프입니다.

 ![보고](./media/active-directory-reporting-azure-portal/05.png "Reporting")

사용자 로그인 그래프에서는 지정된 기간 내에 모든 사용자에 대한 로그인의 주간 집계를 보여 줍니다. 시간에 대한 기본값은 30일입니다.

![보고](./media/active-directory-reporting-azure-portal/02.png "Reporting")

로그인 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

![보고](./media/active-directory-reporting-azure-portal/03.png "Reporting")

로그인 활동 목록에서 각 행은 다음과 같이 선택된 로그인에 대한 자세한 내용을 보여 줍니다.

* 누가 로그인했나요?
* 관련된 UPN는 무엇이었나요?
* 어떤 응용 프로그램이 로그인할 대상이었나요?
* 로그인의 IP 주소는 무엇인가요?
* 로그인의 상태는 어떠했나요?

### <a name="usage-of-managed-applications"></a>관리되는 응용 프로그램의 사용량
로그인 데이터의 응용 프로그램 중심 보기를 사용하여 다음과 같은 질문에 대답할 수 있습니다.

* 누가 내 응용 프로그램을 사용하나요?
* 조직에서 상위 3개의 응용 프로그램은 무엇인가요?
* 최근에 응용 프로그램을 롤아웃했습니다. 어떻게 작동하고 있나요?

이 데이터에 대한 진입점은 **엔터프라이즈 응용 프로그램**의 **개요** 섹션에 있는 지난 30일 간의 보고서에서 조직에 포함된 상위 3개의 응용 프로그램입니다.

 ![보고](./media/active-directory-reporting-azure-portal/06.png "Reporting")

지정된 기간 동안 상위 3가지 응용 프로그램에 대한 로그인의 주간 사용량 그래프 집계입니다. 시간에 대한 기본값은 30일입니다.

![보고](./media/active-directory-reporting-azure-portal/78.png "Reporting")

원하면 특정 응용 프로그램에 포커스를 설정할 수 있습니다.

![보고](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Reporting")

앱 사용량 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

![보고](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Reporting")

**로그인** 옵션을 선택하면 응용 프로그램에 대한 모든 로그인 이벤트의 전체적인 개요를 보여 줍니다.

![보고](./media/active-directory-reporting-azure-portal/85.png "Reporting")

열 선택기를 사용하여 표시하려는 데이터 필드를 선택할 수 있습니다.

![보고](./media/active-directory-reporting-azure-portal/column_chooser.png "Reporting")

### <a name="filtering-sign-ins"></a>로그인 필터링
다음 필드를 사용하여 표시된 데이터의 양을 제한하도록 로그인을 필터링할 수 있습니다.

* 날짜 및 시간 
* 사용자의 사용자 계정 이름
* 응용 프로그램 이름
* 클라이언트 이름
* 로그인 상태

![보고](./media/active-directory-reporting-azure-portal/293.png "Reporting")

활동에서 로그인의 항목을 필터링하는 다른 방법은 특정 항목을 검색하는 것입니다.
검색 메서드를 사용하면 특정 **사용자**, **그룹** 또는 **응용 프로그램**에 대한 로그인의 범위를 지정할 수 있습니다.

![보고](./media/active-directory-reporting-azure-portal/84.png "Reporting")

## <a name="audit-logs"></a>감사 로그
Azure Active Directory의 감사 로그는 규정 준수를 위한 시스템 활동의 기록을 제공합니다.

Azure Portal에서 관련된 활동을 감사하기 위한 세 가지 범주가 있습니다.

* 개요   
* 응용 프로그램
* 디렉터리   

감사 보고서 활동의 전체 목록은 [감사 보고서 이벤트 목록](active-directory-reporting-audit-events.md#list-of-audit-report-events)을 참조하세요.

모든 감사된 데이터에 대한 진입점은 **Azure Active Directory**의 **활동** 섹션에서 **감사 로그**입니다.

![감사](./media/active-directory-reporting-azure-portal/61.png "Auditing")

감사 로그에는 행위자(누가), 활동(무엇) 및 대상을 표시하는 목록 보기가 있습니다.

![감사](./media/active-directory-reporting-azure-portal/345.png "Auditing")

목록 보기에서 항목을 클릭하여 자세한 정보를 얻을 수 있습니다.

![감사](./media/active-directory-reporting-azure-portal/873.png "Auditing")

### <a name="users-and-groups-audit-logs"></a>사용자 및 그룹 감사 로그
사용자 및 그룹 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

* 어떤 종류의 업데이트가 사용자에게 적용되나요?
* 얼마나 많은 사용자가 변경되었나요?
* 얼마나 많은 암호가 변경되었나요?
* 관리자가 디렉터리에서 무엇을 수행했나요?
* 추가된 그룹은 무엇인가요?
* 멤버 자격이 변경된 그룹이 있나요?
* 그룹의 소유자가 변경되었나요?
* 어떤 라이선스가 그룹 또는 사용자에 할당되었나요?

사용자 및 그룹에 관련된 감사 데이터를 검토하려면 **사용자 및 그룹**의 **활동** 섹션에 있는 **감사 로그**에서 필터링된 보기를 찾을 수 있습니다.

![감사](./media/active-directory-reporting-azure-portal/93.png "Auditing")

### <a name="application-audit-logs"></a>응용 프로그램 감사 로그
응용 프로그램 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

* 추가되거나 업데이트된 응용 프로그램은 무엇인가요?
* 삭제된 응용 프로그램은 무엇인가요?
* 응용 프로그램에 대한 서비스 원칙이 변경되었나요?
* 응용 프로그램의 이름이 변경되었나요?
* 누가 응용 프로그램에 동의했나요?

응용 프로그램에 관련된 감사 데이터를 검토하려면 **엔터프라이즈 응용 프로그램**의 **활동** 섹션에 있는 **감사 로그**에서 필터링된 보기를 찾을 수 있습니다.

![감사](./media/active-directory-reporting-azure-portal/134.png "Auditing")

### <a name="filtering-audit-logs"></a>감사 로그 필터링
다음 필드를 사용하여 표시된 데이터의 양을 제한하도록 로그인을 필터링할 수 있습니다.

* 날짜 및 시간
* 행위자의 사용자 계정 이름
* 활동 유형
* 작업

![감사](./media/active-directory-reporting-azure-portal/356.png "Auditing")

**활동 유형** 목록의 콘텐츠는 이 블레이드에 대한 진입점에 연결됩니다.  
진입점이 Azure Active Directory인 경우 이 목록에는 사용 가능한 모든 활동 유형이 포함됩니다.

* 응용 프로그램 
* 그룹 
* 사용자
* 장치
* 디렉터리
* 정책
* 기타

![감사](./media/active-directory-reporting-azure-portal/825.png "Auditing")

나열된 활동은 활동 유형으로 범위가 지정됩니다.
예를 들어 **그룹**을 **활동 유형**으로 선택한 경우 **활동** 목록은 그룹 관련 활동을 포함합니다.   

![감사](./media/active-directory-reporting-azure-portal/654.png "Auditing")

감사 로그의 항목을 필터링하는 다른 방법은 특정 항목을 검색하는 것입니다.

![감사](./media/active-directory-reporting-azure-portal/237.png "Auditing")

## <a name="next-steps"></a>다음 단계
[Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)를 참조하세요.


