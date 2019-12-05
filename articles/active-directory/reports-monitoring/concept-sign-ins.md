---
title: Azure Active Directory 포털의 로그인 작업 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 로그인 작업 보고서 소개
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
ms.date: 12/03/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 706cc38c997c91efc890b213444ff779eb86d5c1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805269"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 로그인 작업 보고서

Azure AD(Azure Active Directory)의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **활동** 
    - **로그인** – 관리되는 애플리케이션 및 사용자 로그인 활동의 사용량에 대한 정보입니다.
    - **감사 로그 - 감사** 로그는 사용자 및 그룹 관리, 관리 되는 응용 프로그램 및 디렉터리 작업에 대 한 시스템 작업 정보 [를 제공 합니다](concept-audit-logs.md) .
- **보안** 
    - **위험한 로그인** - [위험한 로그인](concept-risky-sign-ins.md) 은 사용자 계정의 합법적인 소유자가 아닌 사용자의 로그인 시도에 대 한 표시기입니다.
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](concept-user-at-risk.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이 문서에서는 로그인 보고서의 개요를 제공 합니다.

## <a name="prerequisites"></a>전제 조건

### <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?

* 보안 관리자, 보안 읽기 권한자, 전역 읽기 권한자 및 보고서 구독자 역할의 사용자
* 글로벌 관리자
* 모든 사용자(비관리자)가 자신의 로그인에 액세스할 수 있습니다. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>로그인 작업에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?

* 모든 로그인 활동 보고서를 보려면 테넌트에 이와 관련된 Azure AD Premium 라이선스가 있어야 합니다. Azure Active Directory 버전을 업그레이드 하려면 [Azure Active Directory Premium 시작](../fundamentals/active-directory-get-started-premium.md) 을 참조 하세요. 업그레이드 하기 전에 데이터 작업 없이 프리미엄 라이선스로 업그레이드 한 후 데이터가 보고서에 표시 되는 데 몇 일이 걸릴 수 있습니다.

## <a name="sign-ins-report"></a>로그인 보고서

사용자 로그인 보고서는 다음과 같은 질문에 대한 답변을 제공합니다.

* 사용자의 로그인 패턴이란?
* 한 주 동안 얼마나 많은 사용자가 로그인했나요?
* 이러한 로그인의 상태란?

[Azure Portal](https://portal.azure.com)으로 시작 합니다. 로그인 보고서에 액세스 하려면 **로그인**을 선택 하 고 모니터링을 계속 진행 **합니다.** 일부 로그인 레코드가 포털에 표시 되는 데 최대 2 시간이 걸릴 수 있습니다.

![로그인 활동](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "로그인 작업")

> [!IMPORTANT]
> 로그인 보고서에는 **대화형** 로그인, 즉 사용자 이름과 암호를 사용하는 수동 로그인만 표시됩니다. 비대화형 로그인(서비스 간 인증)은 로그인 보고서에 표시되지 않습니다. 

로그인 로그에는 다음 항목을 보여주는 기본 목록 보기가 있습니다.

- 로그인 날짜
- 관련된 사용자
- 사용자가 로그인 한 응용 프로그램
- 로그인 상태
- 위험 검색 상태
- MFA(Multi-Factor Authentication) 요구 사항 상태

![로그인 활동](./media/concept-sign-ins/sign-in-activity.png "로그인 작업")

도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![로그인 활동](./media/concept-sign-ins/19.png "로그인 작업")

추가 필드를 표시 하거나 이미 표시 된 필드를 제거 합니다.

![로그인 활동](./media/concept-sign-ins/columns.png "로그인 작업")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![로그인 활동](./media/concept-sign-ins/basic-sign-in.png "로그인 작업")

> [!NOTE]
> 이제 고객은 모든 로그인 보고서를 통해 조건부 액세스 정책의 문제를 해결할 수 있습니다. 고객은 로그인 레코드에 대 한 **조건부 액세스** 탭을 클릭 하 여 조건부 액세스 상태를 검토 하 고 로그인에 적용 된 정책과 각 정책에 대 한 결과에 대 한 세부 정보를 확인할 수 있습니다.
> 자세한 내용은 [모든 로그인의 CA 정보에 대한 질문과 대답](reports-faq.md#conditional-access)을 참조하세요.



## <a name="filter-sign-in-activities"></a>로그인 활동 필터링

먼저 보고 된 데이터를 자신에 게 적합 한 수준으로 축소 합니다. 둘째, 날짜 필드를 기본 필터로 사용 하 여 로그인 데이터를 필터링 합니다. Azure AD는 설정할 수 있는 광범위 한 추가 필터를 제공 합니다.

![로그인 활동](./media/concept-sign-ins/04.png "로그인 작업")

**사용자** 필터를 사용하면 관심 있는 사용자의 이름이나 UPN(사용자 계정 이름)을 지정할 수 있습니다.

**애플리케이션** 필터를 사용하면 관심 있는 애플리케이션의 이름을 지정할 수 있습니다.

**로그인 상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- 전체
- 성공
- 실패

**조건부 액세스** 필터를 사용하면 로그인에 대한 CA 정책 상태를 선택할 수 있습니다.

- 전체
- 적용되지 않음
- 성공
- 실패

**날짜** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.  
가능한 값은 다음과 같습니다.

- 1 개월
- 7일
- 24시간
- 사용자 지정 시간 간격

사용자 지정 시간 범위를 선택하면 시작 시간과 종료 시간을 구성할 수 있습니다.

로그인 보기에 다른 필드를 추가할 경우 이러한 필드가 자동으로 필터 목록에 추가됩니다. 예를 들어 **클라이언트 앱** 필드를 목록에 추가하면 다음 필드를 설정할 수 있는 또 다른 필터 옵션이 제공됩니다.  
![로그인 활동](./media/concept-sign-ins/12.png "로그인 작업")

- **브라우저**  
    이 필터는 브라우저 흐름을 사용 하 여 로그인을 시도 하는 모든 이벤트를 표시 합니다.
- **Exchange ActiveSync (지원 됨)**  
    이 필터는 iOS, Android, Windows Phone 등의 지원 되는 플랫폼에서 EAS (Exchange ActiveSync) 프로토콜이 시도 된 모든 로그인 시도를 표시 합니다.
- **Exchange ActiveSync (지원 되지 않음)**  
    이 필터는 Linux 배포판과 같은 지원 되지 않는 플랫폼에서 EAS 프로토콜이 시도 된 모든 로그인 시도를 표시 합니다.
- **Mobile Apps 및 데스크톱 클라이언트** 이 필터는 브라우저 흐름을 사용 하지 않는 모든 로그인 시도를 표시 합니다. 예를 들어 모든 프로토콜을 사용 하는 모든 플랫폼에서 모바일 앱을 사용 하거나 Windows 또는 MacOS의 Office와 같은 데스크톱 클라이언트 앱에서 모바일 앱을 선택 합니다.
  
- **기타 클라이언트**
    - **IMAP**  
        전자 메일을 검색 하는 데 IMAP를 사용 하는 레거시 메일 클라이언트입니다.
    - **MAPI**  
        ADAL을 사용 하 고 MAPI를 사용 하는 Office 2013.
    - **이전 Office 클라이언트**  
        ADAL이 사용 하도록 설정 되어 있지 않고 ADAL이 사용 하지 않도록 설정 된 경우 MAPI 또는 Office 2016를 사용 하는 기본 구성의 office 2013입니다.
    - **POP**  
        POP3를 사용 하 여 전자 메일을 검색 하는 레거시 메일 클라이언트입니다.
    - **SMTP**  
        SMTP를 사용 하 여 전자 메일을 보내는 레거시 메일 클라이언트입니다.

## <a name="download-sign-in-activities"></a>로그인 활동 다운로드

**다운로드** 옵션을 클릭 하 여 가장 최근 25만 레코드의 CSV 또는 JSON 파일을 만듭니다. Azure Portal 외부에서 작업 하려는 경우 [로그인 데이터 다운로드](quickstart-download-sign-in-report.md) 로 시작 합니다.  

![다운로드](./media/concept-sign-ins/71.png "다운로드")

> [!IMPORTANT]
> 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다.  


## <a name="sign-ins-data-shortcuts"></a>로그인 데이터 바로 가기

Azure AD와 Azure Portal는 모두 로그인 데이터를 위한 추가 진입점을 제공 합니다.

- ID 보안 보호 개요
- 사용자
- 그룹
- Enterprise 애플리케이션

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID 보안 보호에서 사용자 로그인 데이터

**Id 보안 보호** 개요 페이지의 사용자 로그인 그래프에는 로그인의 주간 집계가 표시 됩니다. 기간의 기본값은 30 일입니다.

![로그인 활동](./media/concept-sign-ins/06.png "로그인 작업")

로그인 그래프에서 한 날짜를 클릭하면 해당 날짜의 로그인 활동에 대한 개요가 표시됩니다.

로그인 활동 목록의 각 행에는 다음 내용이 표시됩니다.

* 누가 로그인했나요?
* 어떤 애플리케이션이 로그인할 대상이었나요?
* 로그인의 상태는 어떻습니까?
* 로그인의 MFA 상태는 어떻습니까?

항목을 클릭하면 로그인 작업에 대한 세부 정보가 표시됩니다.

- 사용자 ID
- 사용자
- 사용자 이름
- 애플리케이션 UI
- 애플리케이션
- 클라이언트
- 위치
- IP 주소
- 날짜
- 필요한 MFA
- 로그인 상태

> [!NOTE]
> IP 주소는 IP 주소와 해당 주소가 실제로 연결된 컴퓨터 간에 확실한 연결이 없는 경우와 같은 방법으로 발급됩니다. IP 주소 매핑은 클라이언트 디바이스가 실제로 사용되는 위치에서 종종 매우 먼 중앙 풀에서 모바일 공급자 또는 VPN이 IP 주소를 발급한다는 사실로 인해 복잡해집니다. 현재로서는 Azure AD 보고서에서 IP 주소를 실제 위치로 변환하는 것은 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보를 바탕으로 하는 최상의 노력입니다.

**사용자** 페이지에서 **활동** 섹션의 **로그인**을 클릭하면 모든 사용자 로그인에 대한 전체 개요가 표시됩니다.

![로그인 활동](./media/concept-sign-ins/08.png "로그인 작업")

## <a name="usage-of-managed-applications"></a>관리되는 애플리케이션의 사용량

로그인 데이터의 애플리케이션 중심 보기를 사용하여 다음과 같은 질문에 대답할 수 있습니다.

* 누가 내 애플리케이션을 사용하나요?
* 조직에서 상위 3 개의 응용 프로그램은 무엇 인가요?
* 최신 응용 프로그램이 어떻게 작동 하나요?

이 데이터에 대 한 진입점은 조직에서 상위 3 개의 응용 프로그램입니다. 데이터는 **엔터프라이즈 응용 프로그램**의 **개요** 섹션에 있는 지난 30 일 보고서 내에 포함 되어 있습니다.

![로그인 활동](./media/concept-sign-ins/10.png "로그인 작업")

앱 사용 그래프에는 지정 된 기간 동안 상위 3 개 응용 프로그램에 대 한 로그인의 주간 집계가 있습니다. 시간에 대한 기본값은 30일입니다.

![로그인 활동](./media/concept-sign-ins/graph-chart.png "로그인 작업")

원하면 특정 애플리케이션에 포커스를 설정할 수 있습니다.

![보고](./media/concept-sign-ins/single-app-usage-graph.png "보고")

앱 사용량 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

**로그인** 옵션을 선택하면 애플리케이션에 대한 모든 로그인 이벤트의 전체적인 개요를 보여 줍니다.

## <a name="office-365-activity-logs"></a>Office 365 활동 로그

[Microsoft 365 관리 센터](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)에서 Office 365 활동 로그를 볼 수 있습니다. Office 365 활동 및 Azure AD 활동 로그가 많은 디렉터리 리소스를 공유 한다는 점을 고려해 야 합니다. Microsoft 365 관리 센터 에서만 Office 365 활동 로그의 전체 보기를 제공 합니다. 

[Office 365 관리 api](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)를 사용 하 여 프로그래밍 방식으로 office 365 활동 로그에 액세스할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [로그인 활동 보고서 오류 코드](reference-sign-ins-error-codes.md)
* [Azure AD 데이터 보존 정책](reference-reports-data-retention.md)
* [Azure AD 보고서 대기 시간](reference-reports-latencies.md)

