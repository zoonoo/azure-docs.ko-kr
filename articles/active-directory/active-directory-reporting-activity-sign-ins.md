---
title: "Azure Active Directory 포털 – 미리 보기의 로그인 활동 보고서 | Microsoft Docs"
description: "Azure Active Directory 포털 – 미리 보기의 로그인 활동 보고서 소개"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 78617dc7e3a4b6eb4fc32d32b6850b8c0832d6d8
ms.openlocfilehash: 9819c5f6a3aea53664d86e3a23b25946c0f2b731
ms.lasthandoff: 02/22/2017


---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal---preview"></a>Azure Active Directory 포털 – 미리 보기의 로그인 활동 보고서

Azure Active Directory [미리 보기](active-directory-preview-explainer.md)에서 보고를 통해 사용자 환경의 작동 방법을 결정하는 데 필요한 모든 정보를 얻을 수 있습니다.

Azure Active Directory의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **활동** 
    - **로그인 활동** – 관리되는 응용 프로그램 및 사용자 로그인 활동의 사용량에 대한 정보
    - **감사 로그** - 사용자 및 그룹 관리, 관리되는 응용 프로그램 및 디렉터리 활동에 대한 시스템 작업 정보
- **보안** 
    - **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 자세한 내용은 위험한 로그인을 참조하세요.
    - **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 자세한 내용은 위험 플래그가 지정된 사용자를 참조하세요.

이 항목에서는 로그인 활동에 대한 개요를 제공합니다.

## <a name="signs-in-activities"></a>로그인 활동

보고서에서 사용자 로그인에 의해 제공되는 정보를 사용하여 다음과 같은 질문에 대한 대답을 찾습니다.

* 사용자의 로그인 패턴이란?
* 한 주 동안 얼마나 많은 사용자가 로그인했나요?
* 이러한 로그인의 상태란?

이 데이터에 대한 진입점은 **사용자 및 그룹**의 **개요** 섹션에서 사용자 로그인 그래프입니다.

 ![보고](./media/active-directory-reporting-activity-sign-ins/05.png "Reporting")

사용자 로그인 그래프에서는 지정된 기간 내에 모든 사용자에 대한 로그인의 주간 집계를 보여 줍니다. 시간에 대한 기본값은 30일입니다.

![보고](./media/active-directory-reporting-activity-sign-ins/02.png "Reporting")

로그인 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

![보고](./media/active-directory-reporting-activity-sign-ins/03.png "Reporting")

로그인 활동 목록에서 각 행은 다음과 같이 선택된 로그인에 대한 자세한 내용을 보여 줍니다.

* 누가 로그인했나요?
* 관련된 UPN는 무엇이었나요?
* 어떤 응용 프로그램이 로그인할 대상이었나요?
* 로그인의 IP 주소는 무엇인가요?
* 로그인의 상태는 어떠했나요?

## <a name="usage-of-managed-applications"></a>관리되는 응용 프로그램의 사용량

로그인 데이터의 응용 프로그램 중심 보기를 사용하여 다음과 같은 질문에 대답할 수 있습니다.

* 누가 내 응용 프로그램을 사용하나요?
* 조직에서 상위 3개의 응용 프로그램은 무엇인가요?
* 최근에 응용 프로그램을 롤아웃했습니다. 어떻게 작동하고 있나요?

이 데이터에 대한 진입점은 **엔터프라이즈 응용 프로그램**의 **개요** 섹션에 있는 지난 30일 간의 보고서에서 조직에 포함된 상위 3개의 응용 프로그램입니다.

 ![보고](./media/active-directory-reporting-activity-sign-ins/06.png "Reporting")

지정된 기간 동안 상위 3가지 응용 프로그램에 대한 로그인의 주간 사용량 그래프 집계입니다. 시간에 대한 기본값은 30일입니다.

![보고](./media/active-directory-reporting-activity-sign-ins/78.png "Reporting")

원하면 특정 응용 프로그램에 포커스를 설정할 수 있습니다.

![보고](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Reporting")

앱 사용량 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

![보고](./media/active-directory-reporting-activity-sign-ins/top_app_sign_ins.png "Reporting")

**로그인** 옵션을 선택하면 응용 프로그램에 대한 모든 로그인 이벤트의 전체적인 개요를 보여 줍니다.

![보고](./media/active-directory-reporting-activity-sign-ins/85.png "Reporting")

열 선택기를 사용하여 표시하려는 데이터 필드를 선택할 수 있습니다.

![보고](./media/active-directory-reporting-activity-sign-ins/column_chooser.png "Reporting")

## <a name="filtering-sign-ins"></a>로그인 필터링
다음 필드를 사용하여 표시된 데이터의 양을 제한하도록 로그인을 필터링할 수 있습니다.

* 날짜 및 시간 
* 사용자의 사용자 계정 이름
* 응용 프로그램 이름
* 클라이언트 이름
* 로그인 상태

![보고](./media/active-directory-reporting-activity-sign-ins/293.png "Reporting")

활동에서 로그인의 항목을 필터링하는 다른 방법은 특정 항목을 검색하는 것입니다.
검색 메서드를 사용하면 특정 **사용자**, **그룹** 또는 **응용 프로그램**에 대한 로그인의 범위를 지정할 수 있습니다.

![보고](./media/active-directory-reporting-activity-sign-ins/84.png "Reporting")


## <a name="next-steps"></a>다음 단계
[Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)를 참조하세요.


