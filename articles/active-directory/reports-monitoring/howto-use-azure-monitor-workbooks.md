---
title: 보고서에 대한 Azure 모니터 통합 문서 | 마이크로 소프트 문서
description: Azure Active Directory 보고서에 Azure 모니터 통합 문서를 사용하는 방법에 대해 알아봅니다.
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
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014372"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure 활성 디렉터리 보고서에 Azure 모니터 통합 문서를 사용하는 방법

> [!IMPORTANT]
> 이 통합 문서의 기본 쿼리를 최적화하려면 "편집"을 클릭하고 설정 아이콘을 클릭하고 이러한 쿼리를 실행할 작업 영역을 선택하십시오. 기본적으로 통합 문서는 Azure AD 로그를 라우팅하는 모든 작업 영역을 선택합니다. 

다음을 수행하시겠습니까?

- [조건부 액세스 정책이](../conditional-access/overview.md) 사용자의 로그인 경험에 미치는 영향을 이해하고 있습니까?

- 로그인 실패를 해결하여 조직의 로그인 상태를 더 잘 확인하고 문제를 신속하게 해결할 수 있습니까?

- 사용자 환경에 로그인하기 위해 레거시 인증을 사용하는 사람을 알고 계십니까? (레거시 [인증을 차단하면](../conditional-access/block-legacy-authentication.md)테넌트의 보호를 향상시킬 수 있습니다.)

- 테넌트에서 조건부 액세스 정책이 미치는 영향을 이해해야 합니까?

- 로그인 로그 쿼리, 통합 문서보고 사용자 수, 리소스에 액세스할 때 조건부 액세스 정책을 우회한 사용자 수 등을 검토하시겠습니까?

- 정책별 영향을 장치 플랫폼, 장치 상태, 클라이언트 앱, 로그인 위험, 위치 및 응용 프로그램을 포함하여 조건당 컨텍스트화할 수 있도록 조건별 통합 문서 세부 정보 개발에 관심이 있습니까?

- 로그인 로그 쿼리에 대한 심층적인 통찰력을 얻고, 통합 문서에서 액세스 권한을 부여 또는 거부한 사용자 수와 리소스에 액세스할 때 조건부 액세스 정책을 우회한 사용자 수를 보고합니다.

- 이러한 질문을 해결하는 데 도움이 되는 Active Directory는 모니터링을 위한 통합 문서를 제공합니다. [Azure Monitor 통합 문서는](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) 텍스트, 분석 쿼리, 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합합니다.



이 문서의 내용:

- 모니터 통합 문서를 사용하여 대화형 보고서를 만드는 방법에 대해 잘 알고 있다고 [가정합니다.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)

- 모니터 통합 문서를 사용하여 조건부 액세스 정책의 효과를 이해하고, 로그인 실패 문제를 해결하고, 레거시 인증을 식별하는 방법을 설명합니다.
 


## <a name="prerequisites"></a>사전 요구 사항

모니터 통합 문서를 사용하려면 다음이 필요합니다.

- 프리미엄(P1 또는 P2) 라이선스가 있는 Active Directory 테넌트입니다. [프리미엄 라이센스를 얻는](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)방법에 대해 알아봅니다.

- [로그 분석 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- 로그 분석 작업 영역에 [대한 액세스](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)
- Azure Active Directory의 역할 수행(Azure Active Directory 포털을 통해 로그 분석에 액세스하는 경우)
    - 보안 관리자
    - 보안 판독기
    - 보고서 판독기
    - 전역 관리자

## <a name="roles"></a>역할
다음 역할 중 하나에 있어야 하며 통합 문서를 관리하기 위해 기본 Log Analytics 작업 영역에 액세스할 수 있어야 [합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)
-   전역 관리자
-   보안 관리자
-   보안 판독기
-   보고서 판독기
-   애플리케이션 관리자

## <a name="workbook-access"></a>통합 문서 액세스 

통합 문서에 액세스하려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. Azure **Active 디렉터리** > **모니터링** > **통합 문서로**이동합니다. 

1. 보고서 또는 템플릿을 선택하거나 도구 모음에서 **열기를**선택합니다. 

![Azure AD에서 Azure 모니터 통합 문서 찾기](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>로그인 분석

로그인 분석 통합 책에 액세스하려면 **사용** 섹션에서 로그인 을 **선택합니다.** 

이 통합 문서에는 다음과 같은 로그인 추세가 표시됩니다.

- 모든 로그인

- Success

- 보류 중인 사용자 작업

- 실패

각 추세를 다음 범주별로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 분석](./media/howto-use-azure-monitor-workbooks/43.png)


각 추세에 대해 다음과 같은 범주별로 분석이 수행됩니다.

- 위치

    ![위치별 로그인](./media/howto-use-azure-monitor-workbooks/45.png)

- 디바이스

    ![장치별 로그인](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>레거시 인증을 사용한 로그인 


사용 섹션에서 [레거시 인증을](../conditional-access/block-legacy-authentication.md)사용하는 로그인에 대한 통합 문서에 액세스하려면 **레거시** **인증을 사용하여 로그인을 선택합니다.** 

이 통합 문서에는 다음과 같은 로그인 추세가 표시됩니다.

- 모든 로그인

- Success


각 추세를 다음 범주별로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

- 프로토콜

![레거시 인증으로 로그인](./media/howto-use-azure-monitor-workbooks/47.png)


각 추세에 대해 앱 및 프로토콜별로 분석데이터를 얻을 수 있습니다.

![앱 및 프로토콜별 레거시 인증 로그인](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>조건부 액세스로 로그인 


[조건부 액세스 정책으로](../conditional-access/overview.md)로그인하기 위한 통합 문서에 액세스하려면 **조건부 액세스** 섹션에서 **조건부 액세스에 의한 로그인을**선택합니다. 

이 통합 문서에는 비활성화된 로그인의 추세를 보여 주며 있습니다. 각 추세를 다음 범주별로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![조건부 액세스를 사용하여 로그인](./media/howto-use-azure-monitor-workbooks/49.png)


비활성화된 로그인의 경우 조건부 액세스 상태에 따라 분석이 표시됩니다.

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>조건부 액세스 인사이트

### <a name="overview"></a>개요

통합 문서는 IT 관리자가 테넌트에서 조건부 액세스 정책의 영향을 모니터링하는 데 도움이 되는 로그인 로그 쿼리를 포함합니다. 액세스 권한을 부여하거나 거부했을 사용자 수를 보고할 수 있습니다. 통합 설명에는 로그인 시 해당 사용자의 특성을 기반으로 조건부 액세스 정책을 우회한 사용자 수에 대한 인사이트가 포함되어 있습니다. 여기에는 장치 플랫폼, 장치 상태, 클라이언트 앱, 로그인 위험, 위치 및 응용 프로그램을 포함하여 정책의 영향을 조건별 컨텍스트화할 수 있도록 조건별 세부 정보가 포함되어 있습니다.

### <a name="instructions"></a>Instructions 
조건부 액세스 통찰력을 위한 통합 문서에 액세스하려면 조건부 액세스 섹션에서 **조건부 액세스 인사이트** 통합 문서를 선택합니다. 이 통합 문서에는 테넌트의 각 조건부 액세스 정책이 예상한 영향을 보여 줍니다. 드롭다운 목록에서 하나 이상의 조건부 액세스 정책을 선택하고 다음 필터를 적용하여 통합 문서의 범위를 좁힐 수 있습니다. 

- **시간 범위**

- **사용자**

- **앱**

- **데이터 보기**

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/access-insights.png)


영향 요약에는 선택한 정책에 특정 결과가 있는 사용자 또는 로그인 수가 표시됩니다. 합계는 선택한 시간 범위에서 선택한 정책이 평가된 사용자 또는 로그인 수입니다. 타일을 클릭하여 해당 결과 유형별로 통합 문서의 데이터를 필터링합니다. 

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

또한 이 통합 문서에는 선택한 정책이 6가지 조건으로 세분화된 영향도 보여 주며 다음과 같은 각 정책도 보여 줄 것입니다. 
- **장치 상태**
- **장치 플랫폼**
- **클라이언트 앱**
- **로그인 위험**
- **위치**
- **애플리케이션**

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/device-platform.png)

통합 문서에서 선택한 매개 변수로 필터링된 개별 로그인을 조사할 수도 있습니다. 로그인 빈도별로 정렬된 개별 사용자를 검색하고 해당 로그인 이벤트를 봅니다. 

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>권한 부여 컨트롤에 의한 로그인

**조건부 액세스** 섹션에서 권한 부여 [컨트롤에](../conditional-access/controls.md)의한 로그인에 대한 통합 문서에 액세스하려면 **권한 부여 컨트롤의 로그인을 선택합니다.** 

이 통합 문서에는 다음과 같은 비활성화된 로그인 추세가 표시됩니다.

- MFA 요구
 
- 사용 약관 필요

- 개인 정보 보호 정보 요구

- 기타


각 추세를 다음 범주별로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![권한 부여 컨트롤에 의한 로그인](./media/howto-use-azure-monitor-workbooks/50.png)


각 추세에 대해 앱 및 프로토콜별로 분석데이터를 얻을 수 있습니다.

![최근 로그인 분석](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>로그인 실패 분석

로그인 **오류 분석** 통합 문서를 사용하여 다음과 같은 방법으로 오류를 해결할 수 있습니다.

- 로그인
- 조건부 액세스 정책
- 레거시 인증 


문제 **해결** 섹션에서 조건부 액세스 데이터로 로그인에 액세스하려면 **레거시 인증을 사용하여 로그인을 선택합니다.** 

이 통합 문서에는 다음과 같은 로그인 추세가 표시됩니다.

- 모든 로그인

- Success

- 보류 중인 작업

- 실패


각 추세를 다음 범주별로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 문제 해결](./media/howto-use-azure-monitor-workbooks/52.png)


로그인 문제를 해결하는 데 도움이 되는 Azure Monitor는 다음 범주별로 분석 결과를 제공합니다.

- 최고 오류

    ![상위 오류 요약](./media/howto-use-azure-monitor-workbooks/53.png)

- 사용자 작업을 기다리는 로그인

    ![사용자 작업을 기다리는 로그인 요약](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>다음 단계

[통합 문서 모니터를 사용하여 대화형 보고서를 만듭니다.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)
