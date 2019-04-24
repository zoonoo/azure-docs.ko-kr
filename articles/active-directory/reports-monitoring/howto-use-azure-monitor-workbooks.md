---
title: Azure Active Directory 보고에 대 한 Azure Monitor 통합 문서를 사용 하 여 | Microsoft Docs
description: Azure Active Directory 보고서에 대 한 Azure Monitor 통합 문서를 사용 하는 방법 알아보기
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
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287323"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>방법: Azure Monitor 통합 문서를 사용 하 여 Azure Active Directory 보고서

하 시겠습니까?

- 영향을 파악 하 [조건부 액세스 정책을](../conditional-access/overview.md) 사용자 로그인 환경에서?

- 문제를 신속 하 게 해결할 수 있을 뿐만 아니라 조직 로그인 상태를 더 자세히 보려면 로그인 실패 문제 해결?

- 사용자 환경에 로그온 할 레거시 인증을 사용 하는 사람을 알고 있습니까? 하 여 [레거시 인증 차단](../conditional-access/block-legacy-authentication.md), 테 넌 트의 보호를 향상 시킬 수 있습니다.


[Azure Monitor 통합 문서](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) 풍부한 대화형 보고서에 텍스트, Analytics 쿼리, Azure 메트릭 및 매개 변수를 결합 합니다. Azure Active Directory 모니터링에 대 한 위의 질문에 대 한 답변을 찾을 수 있도록 도와주는 통합 문서를 제공 합니다.

이 문서의 내용:

- 하는 방법을 잘 알고 있다고 가정 [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서를 만들고](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)합니다.

- 모니터링에 대 한 Azure Monitor 통합 문서를 사용 하 여 위의 질문에 답변 하는 방법을 설명 합니다.
 


## <a name="prerequisites"></a>필수 조건

이 기능을 사용하려면 다음이 필요합니다.

- Azure Active Directory 테 넌 트 (P1/P2) premium 라이선스를 사용 하 여 합니다. 에 대해 알아봅니다 하는 방법 [premium 라이선스 가져오기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)합니다.

- A [Log Analytics 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)합니다.

## <a name="access-workbooks"></a>통합 문서 액세스 

에 액세스 하려면 통합 문서:

1. 에 로그인 하 여 [Azure portal](https://portal.azure.com)합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

3. 에 **모니터링** 섹션에서 클릭 **Insights**합니다. 

    ![자세한 정보](./media/howto-use-azure-monitor-workbooks/41.png)

4. 보고서 또는 템플릿을 클릭 하거나 클릭 **열고** 도구 모음에서 합니다. 

    ![갤러리](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>로그인 분석

로그인 분석 통합 문서에 액세스 하려면 클릭 **로그인** 에 **사용량** 섹션입니다. 

이 통합 문서는 다음 로그인 한 추세를 보여 줍니다.

- 모든 로그인

- 성공

- 보류 중인 사용자 작업

- 실패

각 추세에 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![갤러리](./media/howto-use-azure-monitor-workbooks/43.png)


각 추세에 대 한 하 여 분석을 가져옵니다.

- Location

    ![갤러리](./media/howto-use-azure-monitor-workbooks/45.png)

- 디바이스

    ![갤러리](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>레거시 인증을 사용 하 여 로그인 


로그인 액세스를 사용 하 여 [레거시 인증](../conditional-access/block-legacy-authentication.md) 통합 문서 클릭 **로그인 레거시 인증을 사용 하 여** 에 **사용량** 섹션. 

이 통합 문서는 다음 로그인 한 추세를 보여 줍니다.

- 모든 로그인

- 성공


각 추세에 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

- 프로토콜 

![갤러리](./media/howto-use-azure-monitor-workbooks/47.png)


각 추세에 대 한 앱 및 프로토콜에 대 한 분석을 얻을 수 있습니다.

![갤러리](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>조건부 액세스 하 여 로그인 


에 액세스 하 여 로그인 하려면 [조건부 액세스 정책을](../conditional-access/overview.md) 통합 문서를 클릭 **조건부 액세스에 의해 로그인** 에 **조건부 액세스** 섹션. 

이 통합 문서 비활성된 로그인에 대 한 추세를 보여 줍니다.

각 추세에 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![갤러리](./media/howto-use-azure-monitor-workbooks/49.png)


비활성화 된 로그인에 대 한 조건부 액세스 상태별에 대 한 분석을 가져옵니다.

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>로그인 하 여 권한 부여 컨트롤

에 액세스 하 여 로그인 하려면 [권한 부여 컨트롤](../conditional-access/controls.md) 통합 문서 클릭 **권한 부여 컨트롤에서 로그인** 에 **조건부 액세스** 섹션. 

이 통합 문서의 다음 비활성화 된 로그인 추세를 보여 줍니다.

- MFA 요구
 
- 사용 약관 필요

- 개인정보취급방침 필요

- 기타


각 추세에 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![갤러리](./media/howto-use-azure-monitor-workbooks/50.png)


각 추세에 대 한 앱 및 프로토콜에 대 한 분석을 얻을 수 있습니다.

![갤러리](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>로그인 실패 분석

사용 된 **로그인 실패 분석** 오류 문제를 해결 하려면 통합 문서:

- 로그인
- 조건부 액세스 정책
- 레거시 인증 합니다. 


로그인 하 여 조건부 액세스 데이터에 액세스 하려면 클릭 **로그인 레거시 인증을 사용 하 여** 에 **문제 해결** 섹션입니다. 

이 통합 문서는 다음 로그인 한 추세를 보여 줍니다.

- 모든 로그인

- 성공

- 보류 중인 작업

- 실패


각 추세에 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![갤러리](./media/howto-use-azure-monitor-workbooks/52.png)


로그인의 문제를 해결 하 여 분석을 가져옵니다.

- 상위 오류

    ![갤러리](./media/howto-use-azure-monitor-workbooks/53.png)

- 로그인 사용자 작업에서 대기 중

    ![갤러리](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>다음 단계

* [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)