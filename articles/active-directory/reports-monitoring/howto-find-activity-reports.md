---
title: Azure Portal에서 Azure Active Directory 사용자 작업 보고서 찾기 | Microsoft Docs
description: Azure Portal에서 Azure Active Directory 사용자 작업 보고서가 어디에 위치하는지 알아봅니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624915"
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

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

작업 범주에는 다음과 같은 항목이 포함됩니다.

- 핵심 디렉터리
- 셀프 서비스 암호 관리
- 셀프 서비스 그룹 관리
- 계정 프로비전


## <a name="sign-ins-report"></a>로그인 보고서 

**로그인** 보기는 모든 사용자 로그인 뿐만 아니라 **애플리케이션 사용량** 보고서를 포함합니다. **Enterprise 애플리케이션** 개요의 **관리** 섹션에서도 애플리케이션 사용량 정보를 볼 수 있습니다.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

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

## <a name="next-steps"></a>다음 단계

* [감사 로그 개요](concept-audit-logs.md)
* [로그인 개요](concept-sign-ins.md)
* [위험 이벤트 개요](concept-risk-events.md)
