---
title: 다단계 웹 테스트로 모니터링 - Azure 응용 프로그램 인사이트
description: Azure 응용 프로그램 인사이트를 사용하여 웹 응용 프로그램을 모니터링하는 다단계 웹 테스트 설정
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655943"
---
# <a name="multi-step-web-tests"></a>다중 단계 웹 테스트

다단계 웹 테스트를 통해 기록된 URL 시퀀스 및 웹 사이트와의 상호 작용을 모니터링할 수 있습니다. 이 문서에서는 Visual Studio Enterprise를 통해 다단계 웹 테스트를 만드는 과정을 안내합니다.

> [!NOTE]
> 다단계 웹 테스트는 Visual Studio 웹테스트 파일에 따라 다릅니다. 비주얼 스튜디오 2019 웹 테스트 기능을 가진 마지막 버전이 될 것이라고 [발표되었다.](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) 새로운 기능이 추가되지는 않지만 Visual Studio 2019의 웹테스트 기능은 현재 지원되고 있으며 제품의 지원 수명 주기 동안 계속 지원됩니다. Azure Monitor 제품 팀은 여기에서 다단계 가용성 테스트의 미래에 대한 질문을 [해결했습니다.](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)  

## <a name="pre-requisites"></a>필수 구성 요소

* 비주얼 스튜디오 2017 엔터프라이즈 이상.
* Visual Studio 웹 성능 및 로드 테스트 도구.

테스트 도구를 찾기 위한 필수 구성 조건입니다. Visual **Studio 설치 관리자** > **개별 구성 요소** > **디버깅 및 테스트** > **웹 성능 및 부하 테스트 도구를**시작합니다.

![웹 성능 및 로드 테스트 도구에 대한 항목 옆에 있는 확인란으로 개별 구성 요소를 선택한 Visual Studio 설치 관리자 UI의 스크린샷](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 다단계 웹 테스트에는 관련 비용이 추가로 듭니다. 자세한 내용은 [공식 가격 가이드를](https://azure.microsoft.com/pricing/details/application-insights/)참조하십시오.

## <a name="record-a-multi-step-web-test"></a>다단계 웹 테스트 기록 

> [!WARNING]
> 더 이상 다단계 레코더를 사용하지 않는 것이 좋습니다. 레코더는 기본적인 상호 작용을 가진 정적 HTML 페이지를 위해 개발되었으며 최신 웹 페이지에 대한 기능적 환경을 제공하지 않습니다.

Visual Studio 웹 테스트 생성에 대한 지침은 [공식 Visual Studio 2019 설명서를](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)참조하십시오.

## <a name="upload-the-web-test"></a>웹 테스트 업로드

1. 가용성 창의 응용 프로그램 인사이트 포털에서 테스트 > **테스트 유형** > **다중 단계 웹 테스트** **만들기를**선택합니다.

2. 테스트 위치, 빈도 및 경고 매개 변수를 설정합니다.

### <a name="frequency--location"></a>주파수 & 위치

|설정| 설명
|----|----|----|
|**테스트 빈도**| 각 테스트 위치에서 테스트를 실행하는 빈도를 설정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.|
|**테스트 위치**| 당사 서버가 귀하의 URL로 웹 요청을 보내는 장소는 다음과 같습니다. 웹 사이트의 문제를 네트워크 문제와 구별할 수 있도록 **권장 테스트 위치의 최소 수는 5개입니다.** 최대 16 개의 위치를 선택할 수 있습니다.

### <a name="success-criteria"></a>성공 기준

|설정| 설명
|----|----|----|
| **테스트 시간 시간** |느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석**을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.|
| **HTTP 응답** | 성공으로 계산되어 반환된 상태 코드입니다. 200은 일반적인 웹 페이지의 반환을 나타내는 코드입니다.|
| **콘텐츠 일치** | 문자열, 같은 "환영합니다!" 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다. **콘텐츠 일치시 영어 문자만 지원됩니다.** |

### <a name="alerts"></a>경고

|설정| 설명
|----|----|----|
|**거의 실시간(미리 보기)** | 실시간 에 가까운 경고를 사용하는 것이 좋습니다. 가용성 테스트를 만든 후 이러한 유형의 경고를 구성합니다.  |
|**고전적인** | 더 이상 새 가용성 테스트에 클래식 경고를 사용하는 것이 좋습니다.|
|**경고 위치 임계값**|최소 3/5 위치를 사용하는 것이 좋습니다. 경고 위치 임계값과 테스트 위치 수 간의 최적의 관계는 최소 5개의 테스트 위치가 있는 **경고 위치 테스트** = **위치 수-2입니다.**|

## <a name="configuration"></a>Configuration

### <a name="plugging-time-and-random-numbers-into-your-test"></a>테스트에 시간과 난수를 연결

외부 피드에서 주식과 같이 시간에 따라 변하는 데이터를 가져오는 도구를 테스트한다고 가정합니다. 웹 테스트를 기록할 때 특정 시간을 사용해야 하지만 테스트 매개 변수로 StartTime 및 EndTime을 설정할 수 있습니다.

![내 멋진 주식 응용 프로그램 스크린 샷](./media/availability-multistep/app-insights-72webtest-parameters.png)

테스트를 실행하면 EndTime이 항상 현재 시간이 되고 StartTime은 15분이 됩니다.

웹 테스트 날짜 시간 플러그인은 매개 변수화 시간을 처리하는 방법을 제공합니다.

1. 원하는 각 가변 매개 변수 값에 대한 웹 테스트 플러그 인을 추가합니다. 웹 테스트 도구 모음에서 **웹 테스트 플러그 인 추가**를 선택합니다.
    
    ![웹 테스트 플러그인 추가](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    이 예에서는 날짜 시간 플러그인의 두 인스턴스를 사용합니다. 한 인스턴스는 "15분 전"이고 다른 하나는 "지금"입니다.

2. 각 플러그인의 속성을 엽니다. 이름을 지정하고 현재 시간을 사용하도록 설정합니다. 둘 중 하나에 대해 Add Minutes = -15를 설정합니다.

    ![컨텍스트 매개 변수](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 웹 테스트 매개 변수에 {{플러그 인 이름}}을(를) 사용하여 플러그 인 이름을 참조합니다.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

이제 테스트를 포털에 업로드합니다. 테스트의 모든 실행에 동적 값을 사용합니다.

### <a name="dealing-with-sign-in"></a>로그인 처리

사용자가 앱에 로그인하면 로그인 뒤에 페이지를 테스트할 수 있도록 로그인을 시뮬레이션하기 위한 여러 옵션이 있습니다. 앱에서 제공하는 보안의 형식에 따라 사용하는 방법이 달라집니다.

모든 경우에 테스트하기 위해 애플리케이션에 계정을 만들어야 합니다. 웹 테스트가 실제 사용자에 영향을 줄 가능성이 발생하지 않도록 가능하면 이 테스트 계정의 사용 권한을 제한합니다.

**간단한 사용자 이름 및 암호** 일반적인 방법으로 웹 테스트를 기록합니다. 우선 쿠키를 삭제합니다.

**SAML 인증**

|속성 이름| 설명|
|----|-----|
| 청중 우리 | SAML 토큰의 잠재 고객 URI입니다.  ACS 네임스페이스 및 호스트 이름을 포함하여 ACS(액세스 제어 서비스)에 대한 URI입니다. |
| 인증서 암호 | 포함된 개인 키에 대한 액세스 권한을 부여하는 클라이언트 인증서의 암호입니다. |
| 클라이언트 인증서  | Base64 인코딩 된 형식의 개인 키가 있는 클라이언트 인증서 값입니다. |
| 이름 식별자 | 토큰의 이름 식별자 |
| 다음 날짜까지 | 토큰이 유효한 기간입니다.  기본값은 5분입니다. |
| 이전이 아님 | 과거에 생성된 토큰의 기간은 유효합니다(시간 왜곡을 해결하기 위해).  기본값은 (음수) 5분입니다. |
| 대상 컨텍스트 매개 변수 이름 | 생성된 어설션을 수신할 컨텍스트 매개 변수입니다. |


**클라이언트 비밀** 앱에 클라이언트 보안 이 포함된 로그인 경로가 있는 경우 해당 경로를 사용합니다. AAD(Azure Active Directory)는 클라이언트 암호 로그인을 제공하는 서비스의 예입니다. AAD에서 클라이언트 암호는 앱 키입니다.

앱 키를 사용하는 Azure 웹앱의 샘플 웹 테스트는 다음과 같습니다.

![샘플 스크린샷](./media/availability-multistep/client-secret.png)

클라이언트 암호(AppKey)를 사용하여 AAD에서 토큰을 가져옵니다.
응답에서 전달자 토큰을 추출합니다.
인증 헤더에서 전달자 토큰을 사용하여 API를 호출합니다.
웹 테스트가 실제 클라이언트인지 확인합니다( 즉, AAD에 자체 앱이 있는지) 및 clientId + 앱 키를 사용합니다. 테스트 대상 서비스는 AAD에 고유한 앱을 가집니다. 이 앱의 appID URI는 리소스 필드의 웹 테스트에 반영됩니다.

### <a name="open-authentication"></a>공개 인증
공개 인증의 예는 Microsoft 또는 Google 계정으로 로그인하는 것입니다. OAuth를 사용하는 많은 앱은 클라이언트 암호 대안을 제공하므로 첫 번째 방법으로 해당 가능성을 조사해야 합니다.

테스트가 OAuth를 사용하여 로그인해야 하는 경우 일반적인 방법은 다음과 같습니다.

Fiddler와 같은 도구를 사용하여 웹 브라우저, 인증 사이트 및 응용 프로그램 간의 트래픽을 검사합니다.
다른 컴퓨터 또는 브라우저를 사용하거나 긴 간격으로 두 개 이상의 로그인을 수행합니다(토큰이 만료되도록 허용).
다른 세션을 비교하여 인증 사이트에서 다시 전달된 토큰을 식별하고 이는 로그인한 후에 앱 서버에 전달됩니다.
Visual Studio Online을 사용하여 웹 테스트 기록
토큰을 매개 변수화하고 토큰이 인증자에서 반환되면 매개 변수를 설정하고 사이트에 대한 쿼리에서 사용합니다. (Visual Studio는 테스트를 매개 변수화하려고 하지만 올바르게 토큰을 매개 변수화하지 않습니다.)

## <a name="troubleshooting"></a>문제 해결

전용 [문제 해결 문서](troubleshoot-availability.md).

## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [Url 핑 웹 테스트](monitor-web-app-availability.md)
