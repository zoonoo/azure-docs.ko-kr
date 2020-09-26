---
title: Azure Active Directory 로그인 활동 보고서-미리 보기 | Microsoft Docs
description: Azure Active Directory 포털의 로그인 활동 보고서 소개
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
ms.date: 09/23/2020
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fc5051a6cc8b8f36a980ff86690ed4f8cbac60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347712"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory 로그인 활동 보고서-미리 보기

Azure AD(Azure Active Directory)의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **작업** 
    - **로그인** -사용자, 응용 프로그램 및 관리 되는 리소스가 Azure AD에 로그인 하 고 리소스에 액세스 하는 시기에 대 한 정보입니다.
    - **감사 로그**  -  [감사 로그](concept-audit-logs.md) 는 사용자 및 그룹 관리, 관리 되는 응용 프로그램 및 디렉터리 작업에 대 한 시스템 작업 정보를 제공 합니다.
- **보안** 
    - **위험한 로그인** - [위험한 로그인](concept-risky-sign-ins.md) 은 사용자 계정의 합법적인 소유자가 아닌 사용자의 로그인 시도에 대 한 표시기입니다.
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](concept-user-at-risk.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Azure Active Directory의 클래식 로그인 보고서에서는 대화형 사용자 로그인에 대 한 개요를 제공 합니다. 또한 이제 미리 보기로 제공 되는 세 가지 추가 로그인 보고서에 액세스할 수 있습니다.

- 비 대화형 사용자 로그인

- 서비스 사용자 로그인

- Azure 리소스 로그인에 대 한 관리 되는 id

이 문서에서는 Azure 리소스 로그인에 대 한 비 대화형 응용 프로그램, 응용 프로그램 및 관리 되는 id의 미리 보기가 포함 된 로그인 활동 보고서에 대 한 개요를 제공 합니다. 미리 보기 기능이 없는 로그인 보고서에 대 한 자세한 내용은  [Azure Active Directory 포털의 로그인 활동 보고서](concept-sign-ins.md)를 참조 하세요.



## <a name="prerequisites"></a>사전 요구 사항

이 기능을 사용 하기 전에 다음에 대 한 답변을 알고 있어야 합니다.

- 데이터에 액세스할 수 있는 사용자는 누구인가요?

- 로그인 작업에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?

### <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?

- 보안 관리자, 보안 읽기 권한자 및 보고서 구독자 역할의 사용자

- 글로벌 관리자

- 모든 사용자(비관리자)가 자신의 로그인에 액세스할 수 있습니다. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>로그인 작업에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?

로그인 활동을 보려면 테 넌 트에 연결 된 Azure AD Premium 라이선스가 있어야 합니다. [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요. 업그레이드 하기 전에 데이터 작업 없이 프리미엄 라이선스로 업그레이드 한 후 데이터가 보고서에 표시 되는 데 몇 일이 걸릴 수 있습니다.



## <a name="sign-ins-report"></a>로그인 보고서

로그인 보고서는 다음 질문에 대 한 답변을 제공 합니다.

- 사용자, 응용 프로그램 또는 서비스의 로그인 패턴은 무엇 인가요?
- 일주일 동안 로그인 한 사용자, 앱 또는 서비스의 수는 몇 개입니까?
- 이러한 로그인의 상태란?


로그인 보고서 블레이드에서 다음을 전환할 수 있습니다.

- **대화형 사용자 로그인** -사용자가 암호, MFA 앱을 통한 응답, 생체 인식 요소 또는 QR 코드와 같은 인증 요인을 제공 하는 로그인 로그인입니다.

- **비 대화형 사용자 로그인** -사용자를 대신해 클라이언트에서 수행 하는 로그인입니다. 이러한 로그인에는 사용자의 상호 작용 또는 인증 요인이 필요 하지 않습니다. 예를 들어 사용자가 자격 증명을 입력할 필요가 없는 새로 고침 및 액세스 토큰을 사용 하 여 인증 및 권한 부여를 수행 합니다.

- **서비스 사용자 로그인** -사용자를 포함 하지 않는 앱 및 서비스 사용자에의 한 로그인입니다. 이러한 로그인에서 앱 또는 서비스는 리소스를 인증 하거나 액세스 하기 위해 자체 자격 증명을 제공 합니다.

- Azure **리소스에 대 한 관리 되는 id 로그인** -azure에서 관리 하는 암호를 포함 하는 azure 리소스에서 로그인 합니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 을 참조 하세요. 


![로그인 보고서 유형](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>사용자 로그인

로그인 블레이드의 각 탭에는 아래의 기본 열이 표시 됩니다. 일부 탭에는 추가 열이 있습니다.

- 로그인 날짜

- 요청 ID

- 사용자 이름 또는 사용자 ID

- 응용 프로그램 이름 또는 응용 프로그램 ID

- 로그인 상태

- 로그인에 사용 되는 장치의 IP 주소



### <a name="interactive-user-sign-ins"></a>대화형 사용자 로그인


대화형 사용자 로그인은 사용자가 Azure AD에 인증 요인을 제공 하거나 Azure AD와 직접 상호 작용 하거나 Microsoft Authenticator 앱과 같은 도우미 앱과 상호 작용 하는 로그인입니다. 사용자에 게 제공 되는 요인에는 암호, MFA 문제에 대 한 응답, 생체 인식 요소 또는 사용자가 Azure AD 또는 도우미 앱에 제공 하는 QR 코드 등이 있습니다.

이 보고서에는 Azure AD에 페더레이션된 id 공급자의 페더레이션된 로그인도 포함 됩니다.  


**보고서 크기:** 작음 <br> 
**예와**

- 사용자는 Azure AD 로그인 화면에서 사용자 이름 및 암호를 제공 합니다.

- 사용자가 SMS MFA 챌린지를 전달 합니다.

- 사용자는 비즈니스용 Windows Hello를 사용 하 여 Windows PC의 잠금을 해제 하는 생체 인식 제스처를 제공 합니다.

- 사용자가 AD FS SAML 어설션을 사용 하 여 Azure AD에 페더레이션 됩니다.


기본 필드 외에도 대화형 로그인 보고서에는 다음이 표시 됩니다. 

- 로그인 위치

- 조건부 액세스 적용 여부



도구 모음에서 **열** 을 클릭 하 여 목록 보기를 사용자 지정할 수 있습니다.

![대화형 사용자 로그인 열](./media/concept-all-sign-ins/columns-interactive.png "대화형 사용자 로그인 열")





뷰를 사용자 지정 하면 추가 필드를 표시 하거나 이미 표시 된 필드를 제거할 수 있습니다.

![모든 대화형 열](./media/concept-all-sign-ins/all-interactive-columns.png)


관련 로그인에 대 한 자세한 정보를 보려면 목록 뷰에서 항목을 선택 합니다.

![로그인 작업](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "대화형 사용자 로그인")



### <a name="non-interactive-user-sign-ins"></a>비 대화형 사용자 로그인

비 대화형 사용자 로그인은 사용자를 대신해 클라이언트 앱 또는 OS 구성 요소에 의해 수행 된 로그인입니다. 대화형 사용자 로그인과 마찬가지로 이러한 로그인은 사용자를 대신 하 여 수행 됩니다. 대화형 사용자 로그인과 달리 이러한 로그인은 사용자가 인증 요인을 제공 하지 않아도 됩니다. 대신, 장치 또는 클라이언트 앱은 토큰 또는 코드를 사용 하 여 사용자를 대신 하 여 리소스에 인증 하거나 액세스 합니다. 일반적으로 사용자는 이러한 로그인을 사용자 활동의 백그라운드에서 발생 하는 것으로 파악 합니다.


**보고서 크기:** 큰 <br>
**예:** 

- 클라이언트 앱은 OAuth 2.0 새로 고침 토큰을 사용 하 여 액세스 토큰을 가져옵니다.

- 클라이언트는 OAuth 2.0 인증 코드를 사용 하 여 액세스 토큰과 새로 고침 토큰을 가져옵니다.

- 사용자가 Azure AD에 가입 된 PC에서 웹 또는 Windows 앱에 대 한 SSO (Single Sign-On)를 수행 합니다.

- 사용자는 FOCI (클라이언트 Id 패밀리)를 사용 하 여 모바일 장치에 세션이 있는 동안 두 번째 Microsoft Office 앱에 로그인 합니다.




기본 필드 외에도 비 대화형 로그인 보고서에는 다음이 표시 됩니다. 

- 리소스 ID

- 그룹화 된 로그인 수




이 보고서에 표시 된 필드는 사용자 지정할 수 없습니다.


![사용 하지 않는 열](./media/concept-all-sign-ins/disabled-columns.png "사용 하지 않는 열")

데이터를 더 쉽게 다이제스트 하기 위해 비 대화형 로그인 이벤트는 그룹화 됩니다. 클라이언트는 일반적으로 로그인을 시도 하는 시간을 제외 하 고 동일한 특성을 모두 공유 하는 짧은 기간 동안 동일한 사용자를 대신 하 여 대화형이 아닌 많은 로그인을 만듭니다. 예를 들어 클라이언트는 사용자를 대신 하 여 시간당 한 번 액세스 토큰을 가져올 수 있습니다. 사용자 또는 클라이언트가 상태를 변경 하지 않는 경우 IP 주소, 리소스 및 기타 모든 정보는 각 액세스 토큰 요청에 대해 동일 합니다. Azure AD가 시간 및 날짜와 동일한 여러 로그인을 기록 하는 경우 해당 로그인은 동일한 엔터티에서 하나의 행으로 집계 됩니다. 여러 개의 동일한 로그인이 있는 행 (날짜 및 시간 발급 제외)은 # 로그인 열에서 1 보다 큰 값을 갖습니다. 행을 확장 하 여 다른 로그인과 다른 타임 스탬프를 모두 볼 수 있습니다. 다음 데이터가 일치 하는 경우 비 대화형 사용자에 게 로그인이 집계 됩니다.


- 애플리케이션

- 사용자

- IP 주소

- 상태

- 리소스 ID


다음을 수행할 수 있습니다.

- 노드를 확장 하 여 그룹의 개별 항목을 표시 합니다.  

- 모든 세부 정보를 보려면 개별 항목을 클릭 하십시오. 


![비 대화형 사용자 로그인 세부 정보](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>서비스 사용자 로그인

대화형 및 비 대화형 사용자 로그인과 달리 서비스 사용자 로그인은 사용자를 포함 하지 않습니다. 대신 사용자 계정 (관리 id 로그인 보고서에만 포함 된 관리 되는 id 로그인 제외)과 같은 사용자가 아닌 계정으로 로그인 합니다. 이러한 로그인에서 앱 또는 서비스는 리소스를 인증 하거나 액세스 하기 위한 인증서 또는 앱 암호와 같은 자체 자격 증명을 제공 합니다.


**보고서 크기:** 큰 <br>
**예:**

- 서비스 주체는 인증서를 사용 하 여 Microsoft Graph 인증 하 고 액세스 합니다. 

- 응용 프로그램은 클라이언트 암호를 사용 하 여 OAuth 클라이언트 자격 증명 흐름에서 인증 합니다. 


이 보고서에는 다음을 보여 주는 기본 목록 보기가 있습니다.

- 로그인 날짜

- 요청 ID

- 서비스 사용자 이름 또는 ID

- 상태

- IP 주소

- 리소스 이름

- 리소스 ID

- 로그인 수

이 보고서에 표시 된 필드는 사용자 지정할 수 없습니다.

![사용 하지 않는 열](./media/concept-all-sign-ins/disabled-columns.png "사용 하지 않는 열")

서비스 사용자 로그인 로그에서 데이터를 더 쉽게 다이제스트 하기 위해 서비스 사용자 로그인 이벤트를 그룹화 합니다. 동일한 조건에서 동일한 엔터티의 로그인은 단일 행으로 집계 됩니다. 행을 확장 하 여 다른 로그인과 다른 타임 스탬프를 모두 볼 수 있습니다. 다음 데이터가 일치 하는 경우 서비스 사용자 보고서에서 로그인이 집계 됩니다.

- 서비스 사용자 이름 또는 ID

- 상태

- IP 주소

- 리소스 이름 또는 ID

다음을 수행할 수 있습니다.

- 노드를 확장 하 여 그룹의 개별 항목을 표시 합니다.  

- 개별 항목을 클릭 하 여 모든 세부 정보 보기 


![열 세부 정보](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "열 세부 정보")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure 리소스 로그인에 대 한 관리 id 

Azure 리소스 로그인에 대 한 관리 되는 id는 자격 증명 관리를 간소화 하기 위해 Azure에서 관리 하는 비밀이 있는 리소스에서 수행 된 로그인입니다.

**보고서 크기:** 소량 <br> 
**예와**

관리 되는 자격 증명이 있는 VM은 Azure AD를 사용 하 여 액세스 토큰을 가져옵니다.   


이 보고서에는 다음을 보여 주는 기본 목록 보기가 있습니다.


- 관리 id ID

- 관리 id 이름

- 리소스

- 리소스 ID

- 그룹화 된 로그인 수

이 보고서에 표시 된 필드는 사용자 지정할 수 없습니다.

데이터의 다이제스트를 용이 하 게 하기 위해 Azure 리소스에 대 한 관리 id 로그인 로그는 비 대화형 로그인 이벤트를 그룹화 합니다. 동일한 엔터티에서의 로그인은 단일 행으로 집계 됩니다. 행을 확장 하 여 다른 로그인과 다른 타임 스탬프를 모두 볼 수 있습니다. 다음 데이터가 모두 일치 하는 경우 관리 되는 id 보고서에서 로그인이 집계 됩니다.

- 관리 id 이름 또는 ID

- 상태

- IP 주소

- 리소스 이름 또는 ID

노드 아래에 그룹화 된 모든 로그인을 표시 하려면 목록 뷰에서 항목을 선택 합니다.

그룹화 된 항목을 선택 하 여 로그인의 모든 세부 정보를 확인 합니다. 


## <a name="filter-sign-in-activities"></a>로그인 활동 필터링

필터를 설정 하 여 반환 된 로그인 데이터의 범위를 좁힐 수 있습니다. Azure AD는 설정할 수 있는 광범위 한 추가 필터를 제공 합니다. 필터를 설정 하는 경우 항상 구성 된 **날짜** 범위 필터에 특히 주의 해야 합니다. 적절 한 날짜 범위 필터를 사용 하면 Azure AD에서 매우 중요 한 데이터만 반환 합니다.     

**날짜** 범위 필터를 사용 하면 반환 된 데이터에 대 한 기간을 정의할 수 있습니다.
가능한 값은 다음과 같습니다.

- 1달

- 7일

- 24 시간

- 사용자 지정

![날짜 범위 필터](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>사용자 로그인 필터링

대화형 및 비 대화형 로그인에 대 한 필터는 동일 합니다. 이로 인해 대화형 로그인에 대해 구성 된 필터는 비 대화형 로그인에 대해 유지 되 고 그 반대의 경우도 마찬가지입니다. 






## <a name="access-the-new-sign-in-activity-reports"></a>새 로그인 활동 보고서에 액세스 

Azure Portal의 로그인 활동 보고서는 미리 보기 보고서를 설정 및 해제 하는 간단한 방법을 제공 합니다. 미리 보기 보고서를 사용 하도록 설정한 경우 모든 로그인 활동 보고서 유형에 액세스할 수 있는 새 메뉴가 표시 됩니다.     


비 대화형 및 응용 프로그램 로그인을 사용 하 여 새 로그인 보고서에 액세스 하려면 다음을 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**를 선택합니다.

    ![Azure AD 선택](./media/concept-all-sign-ins/azure-services.png)

2. **모니터링** 섹션에서 **로그인**을 클릭 합니다.

    ![로그인 선택](./media/concept-all-sign-ins/sign-ins.png)

3. **미리 보기** 표시줄을 클릭 합니다.

    ![새 보기 사용](./media/concept-all-sign-ins/enable-new-preview.png)

4. 기본 보기로 다시 전환 하려면 **미리 보기** 표시줄을 다시 클릭 합니다. 

    ![클래식 보기 복원](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>로그인 활동 보고서 다운로드

로그인 활동 보고서를 다운로드 하는 경우 다음이 적용 됩니다.

- 로그인 보고서는 CSV 또는 JSON 파일로 다운로드할 수 있습니다.

- 최대 100 개의 레코드를 다운로드할 수 있습니다. 더 많은 데이터를 다운로드 하려는 경우 보고 API를 사용 합니다.

- 다운로드는 필터 선택에 따라 결정 됩니다.

- 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다. 


![보고서 다운로드](./media/concept-all-sign-ins/download-reports.png "보고서 다운로드")


각 CSV 다운로드는 6 개의 다른 파일로 구성 되어 있습니다.

- 대화형 로그인

- 대화형 로그인의 인증 세부 정보

- 비 대화형 로그인

- 비 대화형 로그인의 인증 세부 정보

- 서비스 사용자 로그인

- Azure 리소스 로그인에 대 한 관리 id

각 JSON 다운로드는 네 개의 서로 다른 파일로 구성 됩니다.

- 대화형 로그인 (인증 세부 정보 포함)

- 비 대화형 로그인 (인증 세부 정보 포함)

- 서비스 사용자 로그인

- Azure 리소스 로그인에 대 한 관리 id

![파일 다운로드](./media/concept-all-sign-ins/download-files.png "파일 다운로드")




## <a name="next-steps"></a>다음 단계

* [로그인 활동 보고서 오류 코드](reference-sign-ins-error-codes.md)
* [Azure AD 데이터 보존 정책](reference-reports-data-retention.md)
* [Azure AD 보고서 대기 시간](reference-reports-latencies.md)
