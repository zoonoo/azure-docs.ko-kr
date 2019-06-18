---
title: Azure Active Directory 보고에 대 한 Azure Monitor 통합 문서를 사용 하 여 | Microsoft Docs
description: Azure Active Directory 보고서에 대 한 Azure Monitor 통합 문서를 사용 하는 방법에 알아봅니다.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6d461725d0d4c86ee7aa5b450d42c01ca77ffb16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107671"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Active Directory 보고에 대 한 Azure Monitor 통합 문서를 사용 하는 방법

하 시겠습니까?

- 효과 이해 하 [조건부 액세스 정책을](../conditional-access/overview.md) 사용자 로그인 환경에서?

- 조직의 로그인 상태를 더 자세히 보려면 문제를 신속 하 게 확인 하 고 로그인 실패 문제 해결?

- 사용자 환경에 로그인 할 레거시 인증을 사용 하는 사람을 알고 있습니까? (의해 [레거시 인증 차단](../conditional-access/block-legacy-authentication.md), 테 넌 트의 보호를 향상 시킬 수 있습니다.)

이러한 질문을 하려면 Active Directory 모니터링에 대 한 통합 문서를 제공 합니다. [Azure Monitor 통합 문서](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) 풍부한 대화형 보고서에 텍스트, 분석 쿼리, 메트릭 및 매개 변수를 결합 합니다. 

이 문서의 내용:

- 하는 방법에 익숙한 가정 [모니터 통합 문서를 사용 하 여 대화형 보고서를 만들고](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)합니다.

- 모니터 통합 문서를 사용 하 여 로그인 실패 문제를 해결 하 고 레거시 인증을 식별 하 여 조건부 액세스 정책 적용을 이해 하는 방법에 설명 합니다.
 


## <a name="prerequisites"></a>필수 조건

모니터 통합 문서를 사용 하려면 다음을 수행 해야 합니다.

- Premium (P1 또는 P2) 라이선스를 사용 하 여 Active Directory 테 넌입니다. 에 대해 알아봅니다 하는 방법 [premium 라이선스 가져오기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)합니다.

- A [Log Analytics 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)합니다.

## <a name="workbook-access"></a>통합 문서 액세스 

에 액세스 하려면 통합 문서:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽된 탐색 창에서 선택 **Azure Active Directory**합니다.

3. 에 **모니터링** 섹션에서 **Insights**합니다. 

    ![Insights 선택](./media/howto-use-azure-monitor-workbooks/41.png)

4. 보고서 또는 템플릿을 선택 하거나 도구 모음에서 선택 **열려**합니다. 

    ![열기를 선택 합니다.](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>로그인 분석

로그인 분석 통합 문서에 액세스 하는 **사용 현황** 섹션에서 **로그인**합니다. 

이 통합 문서는 다음 로그인 한 추세를 보여 줍니다.

- 모든 로그인

- 성공

- 보류 중인 사용자 작업

- 실패

다음 범주에서 각 추세를 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 분석](./media/howto-use-azure-monitor-workbooks/43.png)


각 추세에 대 한 다음 범주별으로 분류를 가져옵니다.

- Location

    ![위치에서 로그인](./media/howto-use-azure-monitor-workbooks/45.png)

- 디바이스

    ![장치에서 로그인](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>레거시 인증을 사용 하 여 로그인 


로그인을 사용 하는 통합 문서에 액세스 하려면 [레거시 인증](../conditional-access/block-legacy-authentication.md)를 **사용량** 섹션에서 **로그인 레거시 인증을 사용 하 여**. 

이 통합 문서는 다음 로그인 한 추세를 보여 줍니다.

- 모든 로그인

- 성공


다음 범주에서 각 추세를 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

- 프로토콜

![레거시 인증으로 로그인](./media/howto-use-azure-monitor-workbooks/47.png)


각 추세에 대 한 앱 및 프로토콜에 대 한 분석을 얻을 수 있습니다.

![앱 및 프로토콜에서 레거시 인증 로그인](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>조건부 액세스 하 여 로그인 


로그인 하 여 통합 문서에 액세스 하려면 [조건부 액세스 정책을](../conditional-access/overview.md)를 **조건부 액세스** 섹션에서 **조건부 액세스에 의해 로그인**합니다. 

이 통합 문서에는 비활성화 된 로그인에 대 한 추세를 보여 줍니다. 다음 범주에서 각 추세를 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![조건부 액세스를 사용하여 로그인](./media/howto-use-azure-monitor-workbooks/49.png)


비활성화 된 로그인에 대 한 조건부 액세스 상태별에 대 한 분석을 가져옵니다.

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>로그인 하 여 권한 부여 컨트롤

로그인 하 여 통합 문서에 액세스 하려면 [권한 부여 컨트롤](../conditional-access/controls.md)를 **조건부 액세스** 섹션에서 **로그인 권한 부여 컨트롤에서**합니다. 

이 통합 문서의 다음 비활성화 된 로그인 추세를 보여 줍니다.

- MFA 요구
 
- 사용 약관 필요

- 개인정보취급방침 필요

- 기타


다음 범주에서 각 추세를 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 하 여 권한 부여 컨트롤](./media/howto-use-azure-monitor-workbooks/50.png)


각 추세에 대 한 앱 및 프로토콜에 대 한 분석을 얻을 수 있습니다.

![최근 로그인에 대 한 분석](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>로그인 실패 분석

사용 된 **로그인 실패 분석** 다음을 사용 하 여 오류를 해결 하려면 통합 문서:

- 로그인
- 조건부 액세스 정책
- 레거시 인증 


에 로그인 하 여 조건부 액세스 데이터를 액세스 하려면 합니다 **문제 해결** 섹션에서 **로그인 레거시 인증을 사용 하 여**입니다. 

이 통합 문서는 다음 로그인 한 추세를 보여 줍니다.

- 모든 로그인

- 성공

- 보류 중인 작업

- 실패


다음 범주에서 각 추세를 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 문제 해결](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Monitor에 로그인 문제를 해결 하는 데 도움이 다음 범주별으로 분석을 제공 합니다.

- 상위 오류

    ![최상위 오류의 요약](./media/howto-use-azure-monitor-workbooks/53.png)

- 로그인 사용자 작업에서 대기 중

    ![로그인 사용자 작업에서 대기 하는 요약](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>다음 단계

[모니터 통합 문서를 사용 하 여 대화형 보고서를 만들고](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)합니다.