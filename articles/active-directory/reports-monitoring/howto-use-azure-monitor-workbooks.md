---
title: Azure Active Directory 보고서에 Azure Monitor 통합 문서 사용 | Microsoft Docs
description: Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법에 대해 알아봅니다.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: c1ecdb80263efda4cbbb43caaa4e27a04b261f81
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989798"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법

다음 작업을 수행 하 시겠습니까?

- 사용자의 로그인 환경에 대 한 [조건부 액세스 정책의](../conditional-access/overview.md) 효과를 이해 합니다.

- 로그인 오류 문제를 해결 하 여 조직의 로그인 상태를 더 잘 파악 하 고 문제를 신속 하 게 해결 하 시겠습니까?

- 사용자 환경에 로그인 하는 데 레거시 인증을 사용 하는 사용자를 알고 싶으세요? [레거시 인증을 차단](../conditional-access/block-legacy-authentication.md)하 여 테 넌 트의 보호를 향상할 수 있습니다.

이러한 질문을 해결 하는 데 도움이 되도록 Active Directory는 모니터링을 위한 통합 문서를 제공 합니다. [Azure Monitor 통합 문서](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) 는 텍스트, 분석 쿼리, 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합 합니다. 

이 문서의 내용:

- [통합 문서 모니터링을 사용 하 여 대화형 보고서를 만드는](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)방법을 잘 알고 있다고 가정 합니다.

- 통합 문서 모니터링을 사용 하 여 조건부 액세스 정책의 영향을 파악 하 고, 로그인 오류 문제를 해결 하 고, 레거시 인증을 식별 하는 방법을 설명 합니다.
 


## <a name="prerequisites"></a>전제 조건

통합 문서 모니터링을 사용 하려면 다음이 필요 합니다.

- Premium (P1 또는 P2) 라이선스가 있는 Active Directory 테 넌 트. [프리미엄 라이선스를 얻는](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)방법에 대해 알아봅니다.

- [Log Analytics 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)입니다.

## <a name="workbook-access"></a>통합 문서 액세스 

통합 문서에 액세스 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 탐색 창에서 **Azure Active Directory**을 선택 합니다.

3. **모니터링** 섹션에서 **통합 문서**를 선택 합니다. 

    ![정보 선택](./media/howto-use-azure-monitor-workbooks/41.png)

4. 보고서 또는 템플릿을 선택 하거나 도구 모음에서 **열기**를 선택 합니다. 

    ![열기 선택](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>로그인 분석

로그인 분석 통합 문서에 액세스 하려면 **사용** 섹션에서 **로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여 줍니다.

- 모든 로그인

- Success

- 보류 중인 사용자 작업

- 실패

각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 분석](./media/howto-use-azure-monitor-workbooks/43.png)


각 추세에 대해 다음 범주의 분석 결과를 얻을 수 있습니다.

- 위치

    ![위치별 로그인](./media/howto-use-azure-monitor-workbooks/45.png)

- 디바이스

    ![장치에서 로그인](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>레거시 인증을 사용 하 여 로그인 


[레거시 인증](../conditional-access/block-legacy-authentication.md)을 사용 하는 로그인에 대 한 통합 문서에 액세스 하려면 **사용** 섹션에서 **레거시 인증을 사용 하 여 로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여 줍니다.

- 모든 로그인

- Success


각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

- 프로토콜

![레거시 인증으로 로그인](./media/howto-use-azure-monitor-workbooks/47.png)


각 추세에 대해 앱 및 프로토콜을 기준으로 분석을 받습니다.

![앱 및 프로토콜인 레거시 인증 로그인](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>조건부 액세스로 로그인 


[조건부 액세스 정책의](../conditional-access/overview.md)로그인에 대 한 통합 문서에 액세스 하려면 조건부 액세스 섹션 에서 **조건부 액세스 별 로그인**을 선택 합니다. 

이 통합 문서에서는 비활성화 된 로그인에 대 한 추세를 보여 줍니다. 각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![조건부 액세스를 사용하여 로그인](./media/howto-use-azure-monitor-workbooks/49.png)


비활성화 된 로그인의 경우 조건부 액세스 상태에 대 한 분석을 받게 됩니다.

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>권한 부여 컨트롤을 통한 로그인

[권한 부여 컨트롤](../conditional-access/controls.md)을 사용 하 여 로그인에 대 한 통합 문서에 액세스 하려면 **조건부 액세스** 섹션에서 **권한 부여 컨트롤을 사용 하 여 로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 비활성화 된 로그인 추세를 보여 줍니다.

- MFA 요구
 
- 사용 약관 필요

- 개인정보 취급 방침 필요

- 기타


각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![권한 부여 컨트롤을 통한 로그인](./media/howto-use-azure-monitor-workbooks/50.png)


각 추세에 대해 앱 및 프로토콜을 기준으로 분석을 받습니다.

![최근 로그인 분석](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>로그인 오류 분석

다음을 사용 하 여 오류를 해결 하려면 **로그인 오류 분석** 통합 문서를 사용 합니다.

- 로그인
- 조건부 액세스 정책
- 레거시 인증 


조건부 액세스 데이터를 사용 하 여 로그인에 액세스 하려면 **문제 해결** 섹션에서 **레거시 인증을 사용 하 여 로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여 줍니다.

- 모든 로그인

- Success

- 보류 중인 작업

- 실패


각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 문제 해결](./media/howto-use-azure-monitor-workbooks/52.png)


로그인 문제를 해결 하는 데 도움이 되도록 Azure Monitor는 다음 범주의 분류를 제공 합니다.

- 상위 오류

    ![상위 오류 요약](./media/howto-use-azure-monitor-workbooks/53.png)

- 사용자 작업을 기다리는 로그인

    ![사용자 작업을 기다리는 로그인의 요약](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>다음 단계

[통합 문서 모니터링을 사용 하 여 대화형 보고서를 만듭니다](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
