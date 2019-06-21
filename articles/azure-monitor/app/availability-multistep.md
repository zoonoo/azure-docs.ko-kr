---
title: 다중 단계 웹 테스트를 사용 하 여 웹 응용 프로그램 및 Azure Application Insights 모니터링 | Microsoft Docs
description: Azure Application Insights를 사용 하 여 웹 응용 프로그램을 모니터링 하려면 다중 단계 웹 테스트 설정
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305101"
---
# <a name="multi-step-web-tests"></a>다단계 웹 테스트

기록 된 Url 시퀀스 및 다중 단계 웹 테스트를 통해 웹 사이트와의 상호 작용을 모니터링할 수 있습니다. 이 문서는 Visual Studio Enterprise를 사용 하 여 다중 단계 웹 테스트를 만드는 과정을 안내 합니다.

> [!NOTE]
> 다중 단계 웹 테스트에 연결 된 추가 비용이 있습니다. 자세한 참조를 알아보려면 합니다 [가격 책정 공식 가이드](https://azure.microsoft.com/pricing/details/application-insights/)합니다.

## <a name="pre-requisites"></a>필수 조건

* Visual Studio 2017 Enterprise 이상.
* Visual Studio 웹 성능 및 부하 테스트 도구입니다.

테스트 도구 필수를 찾으려고 합니다. 시작 합니다 **Visual Studio 설치 관리자** > **개별 구성 요소** > **디버깅 및 테스트**  >   **웹 성능 및 부하 테스트 도구**합니다.

![웹 성능 및 부하 테스트 도구에 대 한 항목 옆의 확인란을 사용 하 여 선택한 개별 구성 요소를 사용 하 여 Visual Studio 설치 관리자 UI의 스크린샷](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>다중 단계 웹 테스트 기록

다단계 테스트를 만들려면 Visual Studio Enterprise를 사용하여 시나리오를 기록한 다음 Application Insights에 기록을 업로드합니다. Application Insights 설정 된 간격 시나리오를 재생 하 고 응답을 확인 합니다.

> [!IMPORTANT]
> * 테스트에서 코딩된 함수 또는 루프를 사용할 수 없습니다. 테스트는 .webtest 스크립트에 완전히 포함되어야 합니다. 그러나 표준 플러그 인을 사용할 수 있습니다.
> * 영어 문자만 다단계 웹 테스트에서 지원됩니다. 다른 언어로 Visual Studio를 사용하는 경우 웹 테스트 정의 파일을 영어가 아닌 문자를 번역/제외하도록 업데이트하세요.

Visual Studio Enterprise를 사용하여 웹 세션을 기록합니다.

1. 웹 성능 및 부하 테스트 프로젝트를 만듭니다. **파일** > **새** > **프로젝트** > **시각적 C#**   >  **테스트**

    ![Visual Studio 새 프로젝트 UI](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. 열기는 `.webtest` 파일 및 기록을 시작 합니다.

    ![Visual Studio 테스트 UI를 기록 합니다.](./media/availability-multistep/open-web-test.png)

3. 기록의 일부로 시뮬레이션 하기 위해 테스트 하는 단계를 클릭 합니다.

    ![UI를 기록 하는 브라우저](./media/availability-multistep/record.png)

4. 테스트를 다음과 같이 편집합니다.

    * 받은 텍스트 및 응답 코드를 확인하는 유효성 검사를 추가합니다.
    * 모든 uneccesary 상호 작용을 제거 합니다. 또한 사진에 대 한 종속 요청을 제거 하 하거나 연관이 성공적인 테스트를 고려 하는 추적 사이트를 추가할 수 없습니다.
    
    테스트 스크립트를 편집할 수 있습니다-사용자 지정 코드를 추가 하거나 다른 웹 테스트를 호출할 수 있습니다 염두에서에 둡니다. 테스트에 루프를 삽입하지 마세요. 표준 웹 테스트 플러그 인을 사용할 수 있습니다.

5. 유효성을 검사 하 고 작동 하는지 확인 하려면 Visual Studio에서 테스트를 실행 합니다.

    웹 test runner에서 웹 브라우저가 열리고 기록한 작업을 반복합니다. 예상한 대로 작동 하는 모든 항목이 있는지 확인 합니다.

## <a name="upload-the-web-test"></a>웹 테스트 업로드

1. 가용성 창에서 Application Insights 포털에서 선택 **테스트 만들기** > **테스트 유형** > **다중 단계 웹 테스트**합니다.

2. 테스트 위치, 빈도 및 경고 매개 변수를 설정 합니다.

### <a name="frequency--location"></a>빈도 및 위치

|설정| 설명
|----|----|----|
|**테스트 빈도**| 각 테스트 위치에서 테스트를 실행하는 빈도를 설정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.|
|**테스트 위치**| 서버 URL로 웹 요청을 보내는 곳을 됩니다. **권장 되는 테스트 위치는 최소 수는 5 개입니다** 웹 사이트의 네트워크 문제에서 문제를 구분할 수 있는지 확인 하기 위해. 최대 16 개의 위치를 선택할 수 있습니다.

### <a name="success-criteria"></a>성공 조건

|설정| 설명
|----|----|----|
| **테스트 시간 제한** |느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석**을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.|
| **HTTP 응답** | 성공으로 계산되어 반환된 상태 코드입니다. 200은 일반적인 웹 페이지의 반환을 나타내는 코드입니다.|
| **콘텐츠 일치** | "Welcome!"와 같은 문자열 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다. **콘텐츠 일치를 사용 하 여 영어 문자만 지원 됩니다.** |

### <a name="alerts"></a>경고

|설정| 설명
|----|----|----|
|**근 실시간 (미리 보기)** | 근 실시간 경고를 사용 하는 것이 좋습니다. 이 유형의 경고를 구성 하면 가용성 테스트를 만든 후 수행 됩니다.  |
|**클래식** | 더 이상 새 가용성 테스트에 대 한 클래식 경고를 사용 하는 것이 좋습니다.|
|**경고 위치 임계값**|최소 3/5 위치를 사용하는 것이 좋습니다. 경고 위치 임계값 테스트 위치의 수와 최적의 관계가 **경고 위치 임계값** = **최소 5 개의 테스트 위치를 사용 하 여 테스트 위치-2의 번호입니다.**|

## <a name="advanced-configuration"></a>고급 구성

### <a name="plugging-time-and-random-numbers-into-your-test"></a>테스트에 시간과 난수 연결

외부 피드에서 주식과 같이 시간에 따라 변하는 데이터를 가져오는 도구를 테스트한다고 가정합니다. 웹 테스트를 기록할 때 특정 시간을 사용해야 하지만 테스트 매개 변수로 StartTime 및 EndTime을 설정할 수 있습니다.

![내 멋진 주식 앱 스크린 샷](./media/availability-multistep/app-insights-72webtest-parameters.png)

테스트를 실행하면 EndTime이 항상 현재 시간이 되고 StartTime은 15분이 됩니다.

웹 테스트 날짜 시간 플러그 인을 처리 하는 방법은 제공 시간을 매개 변수화 합니다.

1. 원하는 각 가변 매개 변수 값에 대한 웹 테스트 플러그 인을 추가합니다. 웹 테스트 도구 모음에서 **웹 테스트 플러그 인 추가**를 선택합니다.
    
    ![웹 테스트 플러그 인 추가](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    이 예에서는 날짜 시간 플러그인의 두 인스턴스를 사용합니다. 한 인스턴스는 "15분 전"이고 다른 하나는 "지금"입니다.

2. 각 플러그인의 속성을 엽니다. 이름을 지정하고 현재 시간을 사용하도록 설정합니다. 둘 중 하나에 대해 Add Minutes = -15를 설정합니다.

    ![컨텍스트 매개 변수](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 웹 테스트 매개 변수에 {{플러그 인 이름}}을(를) 사용하여 플러그 인 이름을 참조합니다.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

이제 테스트를 포털에 업로드합니다. 테스트의 모든 실행에 동적 값을 사용합니다.

### <a name="dealing-with-sign-in"></a>로그인 처리

사용자가 앱에 로그인하면 로그인 뒤에 페이지를 테스트할 수 있도록 로그인을 시뮬레이션하기 위한 여러 옵션이 있습니다. 앱에서 제공하는 보안의 형식에 따라 사용하는 방법이 달라집니다.

모든 경우에 테스트하기 위해 애플리케이션에 계정을 만들어야 합니다. 웹 테스트가 실제 사용자에 영향을 줄 가능성이 발생하지 않도록 가능하면 이 테스트 계정의 사용 권한을 제한합니다.

**간단한 사용자 이름 및 암호** 일반적인 방법으로 웹 테스트를 기록 합니다. 우선 쿠키를 삭제합니다.

**SAML 인증** 웹 테스트에 사용할 수 있는 SAML 플러그 인을 사용 합니다. 플러그 인에 액세스 하는 중...

**클라이언트 암호** 앱에 로그인 하는 경로 클라이언트 암호를 포함 하는 경우 해당 경로 사용 합니다. AAD(Azure Active Directory)는 클라이언트 암호 로그인을 제공하는 서비스의 예입니다. AAD에서 클라이언트 암호는 앱 키입니다.

앱 키를 사용하는 Azure 웹앱의 샘플 웹 테스트는 다음과 같습니다.

![샘플 스크린 샷](./media/availability-multistep/client-secret.png)

클라이언트 암호(AppKey)를 사용하여 AAD에서 토큰을 가져옵니다.
응답에서 전달자 토큰을 추출합니다.
인증 헤더에서 전달자 토큰을 사용하여 API를 호출합니다.
웹 테스트는 실제 클라이언트-AAD에 고유한 앱 즉,에 되었는지 확인 하 고 clientId + 앱을 사용 하 여 키입니다. 테스트 대상 서비스는 AAD에 고유한 앱을 가집니다. 이 앱의 appID URI는 리소스 필드의 웹 테스트에 반영됩니다.

### <a name="open-authentication"></a>공개 인증
공개 인증의 예는 Microsoft 또는 Google 계정으로 로그인하는 것입니다. OAuth를 사용하는 많은 앱은 클라이언트 암호 대안을 제공하므로 첫 번째 방법으로 해당 가능성을 조사해야 합니다.

테스트가 OAuth를 사용하여 로그인해야 하는 경우 일반적인 방법은 다음과 같습니다.

Fiddler와 같은 도구를 사용하여 웹 브라우저, 인증 사이트 및 응용 프로그램 간의 트래픽을 검사합니다.
다른 컴퓨터 또는 브라우저를 사용하거나 긴 간격으로 두 개 이상의 로그인을 수행합니다(토큰이 만료되도록 허용).
다른 세션을 비교하여 인증 사이트에서 다시 전달된 토큰을 식별하고 이는 로그인한 후에 앱 서버에 전달됩니다.
Visual Studio Online을 사용하여 웹 테스트 기록
토큰을 매개 변수화하고 토큰이 인증자에서 반환되면 매개 변수를 설정하고 사이트에 대한 쿼리에서 사용합니다. (Visual Studio는 테스트를 매개 변수화하려고 하지만 올바르게 토큰을 매개 변수화하지 않습니다.)

## <a name="troubleshooting"></a>문제 해결

Dedicated [문제 해결 문서](troubleshoot-availability.md)합니다.

## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [Url ping 웹 테스트](monitor-web-app-availability.md)
