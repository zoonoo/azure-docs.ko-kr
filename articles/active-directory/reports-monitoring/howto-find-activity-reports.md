---
title: Azure Portal에서 Azure Active Directory 사용자 작업 보고서 찾기 | Microsoft Docs
description: Azure Portal에서 Azure Active Directory 사용자 작업 보고서가 어디에 위치하는지 알아봅니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bc726f63ea5c8473601623024e18c1402cbebb51
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820320"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal에서 작업 보고서 찾기

이 문서에서는 Azure Portal에서 Azure AD(Azure Active Directory) 사용자 작업 보고서를 찾는 방법을 알아봅니다.

## <a name="audit-logs-report"></a>감사 로그 보고서

감사 로그 보고서는 애플리케이션 작업의 여러 보고서를 컨텍스트 기반 보고에 대한 단일 보기로 결합합니다. 감사 로그 보고서에 액세스하려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 오른쪽 위 모서리에서 디렉터리를 선택한 다음, 왼쪽 탐색 창에서 **Azure Active Directory** 블레이드를 선택합니다.
3. Azure Active Directory 블레이드의 **활동** 섹션에서 **감사 로그**를 선택합니다. 

    ![감사 로그](./media/howto-find-activity-reports/482.png "감사 로그")

감사 로그 보고서는 다음과 같은 보고서를 통합합니다.

* 감사 보고서
* 암호 재설정 활동
* 암호 재설정 등록 활동
* 셀프 서비스 그룹 작업
* Office 365 그룹 이름 변경
* 계정 프로비전 활동
* 암호 롤오버 상태
* 계정 프로비전 오류

### <a name="filtering-on-audit-logs"></a>감사 로그 필터링

감사 보고서에서 고급 필터링을 사용하여 **활동 범주** 필터에서 지정하여 특정 범주의 감사 데이터에 액세스할 수 있습니다. 예를 들어 셀프 서비스 암호 재설정과 관련된 모든 작업을 보려면 **셀프 서비스 암호 관리** 범주를 선택합니다. 

작업 범주에는 다음과 같은 항목이 포함됩니다.

- 핵심 디렉터리
- 셀프 서비스 암호 관리
- 셀프 서비스 그룹 관리
- 계정 프로비전


## <a name="sign-ins-report"></a>로그인 보고서 

**로그인** 보기는 모든 사용자 로그인 뿐만 아니라 **애플리케이션 사용량** 보고서를 포함합니다. **Enterprise 애플리케이션** 개요의 **관리** 섹션에서도 애플리케이션 사용량 정보를 볼 수 있습니다.

로그인 보고서에 액세스하려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 오른쪽 위 모서리에서 디렉터리를 선택한 다음, 왼쪽 탐색 창에서 **Azure Active Directory** 블레이드를 선택합니다.
3. Azure Active Directory 블레이드의 **활동** 섹션에서 **로그인**을 선택합니다. 

    ![로그인 보기](./media/howto-find-activity-reports/483.png "로그인 보기")


### <a name="filtering-on-application-name"></a>애플리케이션 이름 필터링

로그인 보고서를 사용하여 사용자 이름 또는 애플리케이션 이름을 필터링하여 애플리케이션 사용량에 대한 세부 정보를 볼 수 있습니다.

![로그인 이벤트 필터링 페이지](./media/howto-find-activity-reports/07.png "로그인 이벤트 필터링 페이지")

## <a name="security-reports"></a>보안 보고서

### <a name="anomalous-activity-reports"></a>비정상적인 활동 보고서

비정상적인 활동 보고서는 Azure AD가 감지하고 보고할 수 있는 보안 관련 위험 이벤트에 대한 정보를 제공합니다.

다음 테이블에는 Azure Portal의 Azure AD 비정상 작업 보안 보고서 및 해당하는 위험 이벤트 형식이 나열되어 있습니다. 자세한 내용은 [Azure Active Directory 위험 이벤트](concept-risk-events.md)를 참조하세요.  


| Azure AD 비정상 작업 보고서 |  ID 보호 위험 이벤트 유형|
| :--- | :--- |
| 자격 증명이 손실된 사용자 | 유출된 자격 증명 |
| 비정상적인 로그인 작업 | 비정상적 위치로 불가능한 이동 |
| 감염 가능성이 있는 디바이스에서 로그인 | 감염된 디바이스에서 로그인|
| 알 수 없는 원본에서 로그인 | 익명 IP 주소에서 로그인 |
| 의심스러운 작업이 있는 IP 주소에서 로그인 | 의심스러운 작업이 있는 IP 주소에서 로그인 |
| - | 알 수 없는 위치에서 로그인 |

다음 Azure AD 비정상 작업 보안 보고서는 Azure Portal에 위험 이벤트로 포함되지 않습니다.

* 여러 번의 실패 후 로그인
* 여러 지역에서의 로그인


### <a name="detected-risk-events"></a>검색된 위험 이벤트

[Azure Portal](https://portal.azure.com)의 **Azure Active Directory** 블레이드 **보안** 섹션에서 검색된 위험 이벤트에 대한 보고서에 액세스할 수 있습니다. 검색된 위험 이벤트는 다음과 같은 보고서에서 추적됩니다.   

- [위험에 노출된 사용자](concept-user-at-risk.md)
- [위험한 로그인](concept-risky-sign-ins.md)

    ![보안 보고서](./media/howto-find-activity-reports/04.png "보안 보고서")

## <a name="troubleshoot-issues-with-activity-reports"></a>활동 보고서 관련 문제 해결

### <a name="missing-data-in-the-downloaded-activity-logs"></a>다운로드된 활동 로그에서 데이터 누락

#### <a name="symptoms"></a>증상 

활동 로그(감사 또는 로그인)를 다운로드했고 선택한 시간에 대한 모든 레코드가 표시되지 않습니다. 그 이유는 

 ![보고](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>원인

Azure Portal에서 활동 로그를 다운로드할 때 크기를 가장 최근에 먼저 정렬된 5000개의 레코드로 제한합니다. 

#### <a name="resolution"></a>해결 방법

[Azure AD Reporting API](concept-reporting-api.md)를 활용하여 특정 시점에서 최대 백만 개의 레코드를 가져올 수 있습니다. 기간에 따라(예: 매일 또는 매주) 증분 방식으로 레코드를 가져오도록 보고서 API를 호출하는 [스크립트를 일정에 따라 실행](tutorial-signin-logs-download-script.md)하는 것이 좋습니다. 

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Azure Portal에서 최근 작업의 감사 데이터 누락

#### <a name="symptoms"></a>증상

Azure Portal에서 일부 작업을 수행했고 `Activity logs > Audit Logs` 블레이드에서 해당 작업에 대한 감사 로그가 표시될 것을 예상했지만 찾을 수 없습니다.

 ![보고](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>원인

작업이 활동 로그에 즉시 나타나지 않습니다. 아래 표에는 활동 로그의 대기 시간 숫자가 나열되어 있습니다. 

| 보고서 | &nbsp; | 대기 시간(P95) | 대기 시간(P99) |
|--------|--------|---------------|---------------|
| 디렉터리 감사 | &nbsp; | 2분 | 5분 |
| 로그인 작업 | &nbsp; | 2분 | 5분 | 

#### <a name="resolution"></a>해결 방법

15분에서 2시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 2시간 후에도 로그가 보이지 않으면 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)해 주시면 저희 쪽에서 살펴보겠습니다.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Azure AD 로그인 활동 로그에서 최근 사용자 로그인 로그 누락

#### <a name="symptoms"></a>증상

최근에 Azure Portal에 로그인했고 `Activity logs > Sign-ins` 블레이드에 이 작업에 대한 로그인 로그가 표시될 것으로 예상했지만 찾을 수 없습니다.

 ![보고](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>원인

작업이 활동 로그에 즉시 나타나지 않습니다. 아래 표에는 활동 로그의 대기 시간 숫자가 나열되어 있습니다. 

| 보고서 | &nbsp; | 대기 시간(P95) | 대기 시간(P99) |
|--------|--------|---------------|---------------|
| 디렉터리 감사 | &nbsp; | 2분 | 5분 |
| 로그인 작업 | &nbsp; | 2분 | 5분 | 

#### <a name="resolution"></a>해결 방법

15분에서 2시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 2시간 후에도 로그가 보이지 않으면 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)해 주시면 저희 쪽에서 살펴보겠습니다.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Azure Portal에서 30일이 초과된 보고서 데이터를 볼 수 없습니다.

#### <a name="symptoms"></a>증상

Azure Portal에서 30일이 초과된 로그인 및 감사 데이터를 볼 수 없습니다. 그 이유는 

 ![보고](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>원인

Azure Active Directory 작업은 사용자 라이선스에 따라 다음 기간 동안 작업 보고서를 저장합니다.

| 보고서           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| 디렉터리 감사  | &nbsp; |   7 일     | 30일             | 30일             |
| 로그인 작업 | &nbsp; | 사용할 수 없음. 로그인 작업은 개별 사용자 프로필 블레이드에서 7일 동안 액세스할 수 있습니다. | 30일 | 30일             |

자세한 내용은 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)을 참조하세요.  

#### <a name="resolution"></a>해결 방법

데이터를 30일 넘게 보존하는 두 가지 옵션이 있습니다. [Azure AD Reporting API](concept-reporting-api.md)를 사용하여 프로그래밍 방식으로 데이터를 검색하고 데이터베이스에 저장합니다. 또는 감사 로그를 Splunk 또는 SumoLogic 같은 타사 SIEM 시스템에 통합합니다.

## <a name="next-steps"></a>다음 단계

* [감사 로그 개요](concept-audit-logs.md)
* [로그인 개요](concept-sign-ins.md)
* [위험 이벤트 개요](concept-risk-events.md)
