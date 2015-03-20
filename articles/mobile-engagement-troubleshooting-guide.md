<properties 
   pageTitle="Azure Mobile Engagement 문제 해결 가이드" 
   description="Azure Mobile Engagement용 문제 해결 가이드" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - 문제 해결 가이드

## 소개

다음 문제 해결 가이드에서는 Azure Mobile Engagement와 관련하여 가장 흔히 발생하는 문제에 대해 다룹니다. 단, [청구][Link 11] 관련 질문과 [미리 보기] 등록 지원에 대해서는 설명하지 않습니다[Link 7]. 이러한 문제 해결 가이드를 통해 문제를 해결할 수 없으면 [Azure 지원 계약](http://azure.microsoft.com/support/options/)이 있는 경우 Azure 서비스 요청을 개설할 수 있으며, 그렇지 않은 경우에는 [MSDN 포럼]에서 지원을 요청할 수 있습니다[Link 8]. 기술 지원 담당자가 문제를 조사할 수 있도록 서비스 요청을 개설할 때는 <a href="#SR" title="SR">SR 정보</a>의 모든 문제 해결 정보를 포함하도록 합니다.

## <a name="#ANALYTICS">분석, 모니터링, 구분, 대시보드</a>

Azure Mobile Engagement에서 응용 프로그램, 장치 및 사용자에 대한 정보를 수집하는 방법과 관련된 문제에 대해 설명합니다.

**증상 목록**

1. <a href="#ANALYTICS1">정보 누락/지연</a> 
2. <a href="#ANALYTICS2">UI에서 항목을 찾을 수 없음</a>
3. <a href="#ANALYTICS3">작동 중단 문제 해결</a>
 
<a name="#ANALYTICS1">**증상:**
1.    정보 누락/지연:</a>

- 분석, 구분 또는 대시보드에서 정보 표시가 지연됩니다.
- 모니터링에서 정보가 누락됩니다.
- 분석, 구분 또는 대시보드에서 정보가 누락됩니다.
- 구분 제한에 도달합니다.

**원인**

- 분석 API, 모니터 API 및 세그먼트 API를 사용하여 UI에서 누락된 데이터가 API에서는 표시되는지 확인할 수 있습니다.
- Azure Mobile Engagement SDK가 앱에 올바르게 통합되어 있지 않으면 분석, 구분, 모니터링 또는 대시보드에서 정보를 볼 수 없습니다.
- 세그먼트는 작성하고 나면 변경할 수 없으며 "복제"(복사) 또는 "소멸"(삭제)만 가능합니다.
세그먼트는 기준을 10개만 포함할 수 있습니다.
- 모니터링에서 정보가 누락되었는지 테스트하는 가장 효율적인 방법은 테스트 장치를 설정한 다음 해당 장치에서 앱을 제거 및/또는 다시 설치하는 것입니다.
- 분석, 구분 또는 대시보드의 경우 정보는 24시간마다 갱신됩니다.
- 세그먼트가 이전 정보를 기준으로 하더라도 새 세그먼트의 정보는 작성된 후 24시간 동안 표시되지 않을 수 있습니다.
- UI에서 분석 데이터를 필터링하면 앱 버전에 관계없이 해당 형식의 모든 예제가 표시됩니다. 즉, "작동 중지"를 이름으로 필터링하면 앱 버전 1과 2의 예제가 모두 표시됩니다.
- 분석 기간은 사용자 장치 설정의 날짜를 기준으로 하므로 휴대폰에 날짜가 잘못 설정된 사용자의 경우 잘못된 기간의 데이터가 표시됩니다.
- 푸시를 "테스트"하는 단추를 사용할 때는 서버 쪽 데이터가 기록되지 않으며 실제 푸시 캠페인에 대해서만 데이터기 기록됩니다.

**참고 항목:**

- [문제 해결 가이드 - SDK][Link 2], [API 설명서][Link 4], [UI 설명서 - 세그먼트][Link 1]

<a name="#ANALYTICS2">**증상:**
2. UI에서 항목을 찾을 수 없음</a>

- 특정 기본 제공 앱 또는 사용자 지정 앱의 정보 태그를 기준으로 세그먼트를 만들 수 없습니다.
- 분석, 모니터링 또는 대시보드에서 특정 기본 제공 앱 또는 사용자 지정 앱의 정보 태그 기준을 찾을 수 없습니다.
- 분석, 모니터링, 구분 또는 대시보드에서 데이터를 해석할 수 없습니다.

**원인**

- 일부 기본 제공 항목 및 앱 정보 태그는 푸시 기준으로만 제공되며 분석, 모니터링 또는 대시보드에서 보거나 세그먼트에 추가하지는 못할 수도 있습니다. 
- 세그먼트에 추가할 수 없는 기본 제공 항목 및 앱 정보 태그의 경우 세그먼트 기준 대상 지정과 같은 기능을 수행하도록 각 캠페인에서 대상 기준 목록을 설정해야 합니다.
- 자세한 도움말과 방법 정보는 Azure Mobile Engagement UI에서 분석, 모니터링, 구분 또는 대시보드 섹션의 상황에 맞는 메뉴를 참조하세요.

**참고 항목:**

- [UI 설명서 - 대상 지정을 위한 도달률 새 푸시 기준][Link 1]
 
<a name="#ANALYTICS3">**증상:**
3. 작동 중단 문제 해결</a>

- 분석, 모니터링 또는 대시보드에서 응용 프로그램 작동 중단 현상이 나타납니다.

**원인**

- 분석, 모니터링 또는 대시보드에서 나타나는 응용 프로그램 작동 중단 문제를 해결하려면 릴리스 정보에서 이전 버전 SDK의 알려진 문제를 확인하세요.
- 응용 프로그램 작동 중단 문제를 추가로 해결하려면 응용 프로그램이 설치된 테스트 장치에서 이벤트를 수행한 다음 Azure Mobile Engagement UI의 "모니터 - 이벤트" 섹션에서 장치 ID를 조회합니다. 그런 다음 응용 프로그램 작동 중단의 원인이 되는 이벤트를 수행하여 Azure Mobile Engagement UI의 "모니터 - 이벤트" 섹션에서 추가 정보를 조회합니다. 

**참고 항목:**

- [개념 - FAQ][Link 6], [개념 - 용어집][Link 6], [UI 설명서][Link 1], [SDK 설명서 - 릴리스 정보][Link 5], [SDK 설명서 - 업그레이드 가이드][Link 5], [방법 - 장치 ID 등록][Link 3]

## <a name="#APIS">API</a>

관리자가 API를 통해 Azure Mobile Engagement와 상호 작용하는 방법과 관련한 문제에 대해 설명합니다.

**증상 목록**

1. <a href="#APIS1">구문 문제</a>
2. <a href="#APIS2">Azure Mobile Engagement UI에서 수행 가능한 것과 같은 작업을 API를 사용하여 수행할 수 없음</a>
3. <a href="#APIS3">오류 메시지</a>
4. <a href="#APIS4">자동 오류</a>
 
<a name="#APIS1">**증상:**
1. 구문 문제</a>

- API 사용 시에 구문 오류가 발생하거나 예기치 않은 동작이 수행됩니다.

**원인**

- 구문 문제
    - 사용 중인 특정 API의 구문을 점검하여 옵션이 사용 가능한지 확인합니다.
    - API 사용 시 일반적으로 발생하는 문제 중 하나는 도달률 API와 푸시 API를 혼동하는 것입니다. 대부분의 작업은 푸시 API가 아닌 도달률 API를 사용하여 수행해야 합니다. 
    - SDK 통합 및 API 사용과 관련하여 흔히 발생하는 또 다른 문제는 SDK 키와 API 키를 혼동하는 것입니다.
    - API에 연결하는 스크립트는 최소 10분마다 데이터를 보내야 하며, 그렇지 않으면 연결 시간이 초과됩니다. 이러한 현상은 특히 데이터를 수신 대기하는 모니터 API 스크립트에서 일반적으로 발생합니다. 시간 초과를 방지하려면 서버에 대한 세션 연결을 유지하기 위해 스크립트가 10분마다 XMPP ping을 전송하도록 설정합니다.

**참고 항목:**
 
- [개념 - 용어집][Link 6], [API 설명서][Link 4], [XMPP 프로토콜 정보]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**증상:**
2. API를 사용하여 Azure Mobile Engagement UI에서 수행 가능한 것과 같은 작업을 수행할 수 없음</a>

- Azure Mobile Engagement UI에서 수행할 수 있는 작업을 관련 Azure Mobile Engagement API에서는 수행할 수 없습니다.

**원인**

- Azure Mobile Engagement UI에서 같은 작업을 수행할 수 있으면 Azure Mobile Engagement의 이 기능이 SDK와 올바르게 통합된 것입니다.

**참고 항목:**
 
- [UI 설명서][Link 1]
 
<a name="#APIS3">**증상:**
3. 오류 메시지</a>

- API 사용 시 런타임에 또는 로그에 오류 코드가 표시됩니다.

**원인**

- 아래에는 참조 및 임시 문제 해결에 사용할 수 있도록 일반적인 API 상태 코드 번호의 통합 목록이 나와 있습니다.

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**참고 항목:**

- [API 설명서 - 각 특정 API에 대한 상세 오류 정보][Link 4]
 
<a name="#APIS4">**증상:**
4. 자동 오류</a>

- API 작업이 실패하고 런타임이나 로그에 오류 메시지가 표시되지 않습니다.

**원인**

- 대부분의 항목은 올바르게 통합되지 않은 경우 Azure Mobile Engagement UI에서 사용되지 않도록 설정되지만 API에서는 자동으로 오류가 발생하므로 UI의 같은 기능을 테스트하여 작동하는지 확인해야 합니다.
- Azure Mobile Engagement 및 Azure Mobile Engagement에서 사용하려는 대다수의 고급 기능은 별도의 단계를 통해 SDK와 함께 앱에 개별적으로 통합해야 사용 가능합니다.

**참고 항목:**

- [문제 해결 가이드 - SDK][Link 2], [SDK 설명서][Link 5]
 
## <a name="#PUSH">푸시/도달률</a>

Azure Mobile Engagement에서 사용자에게 정보를 전송하는 방법과 관련된 문제에 대해 설명합니다.
 
**증상 목록**

1. <a href="#PUSH1">푸시 오류</a>
2. <a href="#PUSH2">푸시 테스트 문제</a>
3. <a href="#PUSH3">푸시 사용자 지정 문제</a>
4. <a href="#PUSH4">푸시 대상 지정 문제</a>
5. <a href="#PUSH5">푸시 예약</a>
 
<a name="#PUSH1">**증상:**
1. 푸시 오류:</a>

- 푸시가 앱 내부 또는 외부나 두 위치에서 모두 작동하지 않습니다.

**원인**

- 푸시 오류:
    - 대부분의 경우 푸시 오류는 Azure Mobile Engagement, 도달률 또는 Azure Mobile Engagement의 기타 고급 기능이 올바르게 통합되지 않았거나, SDK에서 업그레이드를 수행하여 새 OS 또는 장치 플랫폼의 알려진 문제를 해결해야 함을 나타냅니다.
    - 앱 내 또는 앱 외부에 문제가 있는지 확인하려면 앱 내 푸시 및 앱 외부 푸시만 각각 별도로 테스트해 봅니다.
    - UI와 API에서 모두 제공되는 추가 오류 정보를 확인하려면 문제 해결 단계로 UI 및 API 둘 다에서 테스트를 수행합니다.
    - Azure Mobile Engagement 및 도달률이 모두 SDK에 통합되어 있어야 앱 외부 푸시가 작동합니다.
    - 인증서가 유효하지 않거나 프로덕션 인증서와 개발 인증서 중 올바른 버전을 사용하고 있으면 앱 외부 푸시는 작동하지 않습니다(iOS에만 해당).
    - 앱 외부 푸시 수는 플랫폼별로 다르게 처리됩니다. 장치에서 네이티브 푸시를 사용하지 않도록 설정되어 있으면 iOS에서는 Android에 비해 더 적은 정보가 표시됩니다. API는 푸시 통계에 대해 UI보다 많은 정보를 제공할 수 있습니다.
    - 고객이 OS(iOS 및 Android) 수준에서 앱 외부 푸시를 차단할 수 있습니다.
    - 앱 외부 푸시는 올바르게 통합되어 있지 않으면 Azure Mobile Engagement에서 사용할 수 없는 것으로 표시되지만 API에서는 자동으로 오류가 발생할 수 있습니다.
    - Azure Mobile Engagement 및 도달률이 모두 SDK에 통합되어 있어야 앱 내 푸시가 작동합니다.
    - Azure Mobile Engagement 및 특정 서버가 SDK에 통합되어 있어야 GCM 및 ADM 푸시가 작동합니다(Android에만 해당).
    - 앱 내 푸시와 앱 외부 푸시를 개별적으로 테스트하여 문제가 푸시 문제인지 아니면 도달률 문제인지를 확인해야 합니다.
    - 앱 내 푸시를 사용하려면 앱이 푸시를 받도록 열려 있어야 합니다.
    - 앱 내 푸시는 옵트인(opt in) 또는 옵트아웃(opt out) 앱 정보 태그를 기준으로 필터링하도록 설정되는 경우가 많습니다.
    - 도달률에서 사용자 지정 범주를 사용하여 앱 내 알림을 표시하는 경우에는 알림의 올바른 수명 주기를 따라야 합니다. 그렇지 않으면 사용자가 알림을 해제해도 알림이 지워지지 않을 수 있습니다.
    - 종료 날짜가 없는 캠페인을 시작한 후 장치가 앱 내 알림을 받았지만 아직 표시하지는 않은 경우 사용자가 캠페인을 수동으로 종료해도 다음 번에 앱에 로그인할 때 해당 알림이 수신됩니다.
    - 푸시 API 관련 문제의 경우에는 도달률 API가 아닌 푸시 API를 사용하려는 것이 맞는지(일반적으로는 도달률 API가 더 많이 사용됨), 그리고 "payload" 매개 변수와 "notifier" 매개 변수를 혼동하고 있지 않은지 확인합니다.
    - Wi-Fi와 3G를 통해 연결되는 장치를 사용하여 푸시 캠페인을 테스트해 가능한 문제 원인에서 네트워크 연결을 제외할 수 있는지 확인합니다.

**참고 항목:**

- [문제 해결 가이드 - SDK][Link 2], [문제 해결 가이드 - 푸시][Link 2], [SDK 설명서 - iOS - Apple 푸시 알림을 받도록 응용 프로그램을 준비하는 방법][Link 5]
 
<a name="#PUSH2">**증상:**
2. 푸시 테스트 문제</a>

- 장치 ID를 기준으로 하여 특정 장치에 푸시를 보낼 수 있습니다.

**원인**

- 테스트 장치는 플랫폼별로 다르게 설정되지만 테스트 장치에서 앱에 이벤트를 발생시킨 다음 포털에서 장치 ID를 찾으면 모든 플랫폼에 대해 장치 ID를 찾을 수 있습니다.
- 테스트 장치가 IDFA와 IDFV에서 다르게 작동합니다(iOS에만 해당).

**참고 항목:**

- [UI 설명서 - 도달률][Link 1]
 
<a name="#PUSH3">**증상:**
3. 푸시 사용자 지정 문제</a>

- 배지, 벨소리, 진동, 사진 등의 고급 푸시 콘텐츠 항목이 작동하지 않습니다.
- 앱 외부, 앱 내부, 웹 사이트, 앱 내의 위치에 대한 푸시의 링크가 작동하지 않습니다.
- 푸시 통계에 푸시가 예상했던 수의 사용자에게 전송되지 않은 것으로 표시됩니다(너무 많거나 충분하지 않음).
- 중복 푸시가 두 번 수신됩니다.
- 직접 작성한 프로덕션 앱이나 개발 앱이 설치된 Azure Mobile Engagement 푸시용 테스트 장치를 등록할 수 없습니다.

**원인**

- 앱 내의 특정 위치에 연결하려면 "범주"가 필요합니다(Android에만 해당).
- 큰 사진이 포함된 푸시가 작동하려면 외부 이미지 서버에서 HTTP "GET" 및 "HEAD"를 사용할 수 있어야 합니다(Android에만 해당).
- 코드에서 키보드를 열 때 Azure Mobile Engagement 에이전트를 사용하지 않도록 설정할 수 있으며 키보드를 닫을 때 코드에서 Azure Mobile Engagement를 다시 활성화하도록 지정할 수 있습니다. 이렇게 하면 키보드가 알림 모양에 영향을 주지 않습니다(iOS에만 해당).
- 일부 항목은 테스트 시뮬레이션에서 작동하지 않으며 실제 캠페인(배지, 벨소리, 진동, 사진 등)에서만 작동합니다.
- 푸시를 "테스트"하는 단추를 사용할 때는 서버 쪽 데이터가 기록되지 않습니다. 데이터는 실제 푸시 캠페인에 대해서만 기록됩니다.
- 작동 방식이 조금씩 다 다르므로 문제 범위를 좁히려면 테스트, 시뮬레이트, 실제 캠페인을 각기 별도로 수행하여 문제를 해결합니다.

**참고 항목:**

- [방법 - 푸시][Link 3], [문제 해결 가이드 - 푸시][Link 2], [HTTP 프로토콜 정보]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**증상:**
4. 푸시 대상 지정 문제</a>

- 기본 제공 대상 지정 기능이 정상적으로 작동하지 않습니다.
- 앱 정보 태그 대상 지정 기능이 정상적으로 작동하지 않습니다.
- 지리적 위치 대상 지정 기능이 정상적으로 작동하지 않습니다.
- 언어 옵션이 정상적으로 작동하지 않습니다.

**원인**

- 푸시 대상 지정
    - Azure Mobile Engagement UI 또는 API를 통해 앱 정보 태그를 업로드했는지 확인합니다.
    - 응용 프로그램 수준에서 푸시 속도 또는 푸시 할당량을 제한하거나 캠페인 수준에서 대상을 제한하면 다른 대상 지정 기준을 충족하는 사용자가 특정 푸시를 받지 못할 수 있습니다. 
    - "언어"를 설정하는 것은 국가나 로캘에 따라 대상을 지정하는 것과는 다르며, 휴대폰 위치나 GPS 위치에 따라 지리적 위치를 기준으로 대상을 지정하는 것과도 다릅니다.
    - 지정한 대체 언어 중 하나로 장치를 설정하지 않은 모든 고객에게는 "기본 언어"로 된 메시지가 전송됩니다.

**참고 항목:**

- [UI 설명서 - 도달률][Link 1], [UI 설명서 - 설정][Link 1], [API 설명서 - 도달률][Link 4], [API 설명서 - 푸시][Link 4], [API 설명서 - 장치][Link 4]
 
<a name="#PUSH5">**증상:**
5. 푸시 예약</a>

- 푸시 예약이 정상적으로 작동하지 않습니다(푸시가 너무 빨리 전송되거나 전송이 지연됨).

**원인**

- 표준 시간대로 인해 예약에 문제가 발생할 수 있습니다(특히 최종 사용자의 표준 시간대를 사용하는 경우).
- 고급 푸시 기능을 사용하는 경우 푸시가 지연될 수 있습니다.
- 앱 정보 태그가 아닌 휴대폰 설정을 기준으로 대상을 지정하면 푸시가 지연될 수 있습니다. Azure Mobile Engagement에서 푸시를 보내기 전에 실시간으로 휴대폰에서 데이터를 요청해야 하기 때문입니다.
- 종료 날짜 없이 작성된 캠페인은 푸시를 장치에 로컬로 저장하며, 캠페인을 수동으로 종료하더라도 다음 번에 앱을 열 때 해당 푸시를 표시합니다.
- 둘 이상의 캠페인을 동시에 시작하면 사용자층을 검색하는 데 시간이 더 오래 걸릴 수 있습니다. 캠페인은 한 번에 하나씩 최대 4개까지만 시작하고, 이전 사용자는 검색하지 않아도 되도록 활성 사용자만을 대상으로 지정해 봅니다.
- 도달률 캠페인의 "캠페인" 섹션에서 "대상을 무시하고 API를 통해 사용자에게 푸시 전송" 옵션을 사용하는 경우에는 캠페인이 자동으로 전송되지 않으며 도달률 API를 통해 수동으로 캠페인을 전송해야 합니다.
- 도달률에서 사용자 지정 범주를 사용하여 앱 내 알림을 표시하는 경우에는 올바른 알림 수명 주기를 따라야 합니다. 그렇지 않으면 사용자가 알림을 해제해도 알림이 지워지지 않을 수 있습니다.

**참고 항목:**

- [방법 - 예약][Link 3], [UI 설명서 - 도달률 새 푸시 캠페인][Link 1]
 
## <a name="#SERVICE">서비스</a>

Azure Mobile Engagement 실행 방법과 관련된 문제에 대해 설명합니다.

**증상 목록**

1. <a href="#SERVICE1">서비스 중단</a>
2. <a href="#SERVICE2">연결 및 잘못된 정보 문제</a>
3. <a href="#SERVICE3">기능 요청</a>
 
<a name="#SERVICE1">**증상:**
1. 서비스 중단</a>

**원인**

- Azure Mobile Engagement 서비스 중단으로 인해 발생한 것으로 나타나는 문제에는 여러 가지 원인이 있을 수 있습니다.
    - 원래 모든 Azure Mobile Engagement에서 전체적으로 발생하는 것으로 확인된 문제
    - 서버 중단으로 인한 알려진 문제(서버 상태에 항상 표시되지는 않음):
o    예약 지연, 대상 지정 오류, 배지 업데이트 문제, 통계 수집 중지, 푸시 작동 중지, API 작동 중지, 새 앱이나 사용자 만들기 불가, DNS 오류, 장치의 UI/API/앱 시간 초과 오류
1.    클라우드 종속성 작동 중단
<Azure 서비스 상태><AWS(Amazon 웹 서비스) 상태>
2.    PNS(푸시 알림 서비스) 종속성 작동 중단
<Google - 서비스><Apple - 서비스><Android - Google GCM><Android - Amazon ADM><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    앱 스토어 작동 중단
<GooglePlay><iTunes><Windows Phone 스토어><Windows 스토어>

    - 클라우드 종속성 작동 중단
[Azure 서비스 상태]( http://azure.microsoft.com/status/), [AWS(Amazon 웹 서비스) 상태]( http://status.aws.amazon.com/) 
    - PNS(푸시 알림 서비스) 종속성 작동 중단
[Google - 서비스](http://www.google.com/appsstatus#hl=en&v=status), [Apple - 서비스]( http://www.apple.com/support/systemstatus/), [Android - Google GCM]( http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx), [Windows - WNS](https://developer.windows.com/)
    - 앱 스토어 작동 중단
[GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone 스토어](http://www.windowsphone.com/), [Windows 스토어](http://windows.microsoft.com/)

*문제가 Azure Mobile Engagement 전체의 문제인지 테스트하려는 경우 다음의 개별 항목에서 같은 기능을 테스트할 수 있습니다.*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*문제가 UI 또는 API에만 영향을 주는지 테스트하려면 다음을 수행합니다.*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API 설명서][Link 4], [UI 설명서][Link 1]
	
*휴대폰 네트워크 관련 문제인지를 테스트하려면 다음을 수행합니다.*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*장치 관련 문제인지를 테스트하려면 다음을 수행합니다.*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[UI 설명서 - 설정][Link 1]

*앱 관련 문제인지를 테스트하려면 다음을 수행합니다.*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

최종 사용자 장치의 OS 업그레이드 관련 문제인지를 테스트하려면 다음을 수행합니다(문제를 해결하려면 SDK를 업그레이드해야 함).

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[문제 해결 가이드 - SDK][Link 2]
 
<a name="#SERVICE2">**증상:**
2. 연결 및 잘못된 정보 문제</a>

- Azure Mobile Engagement UI에 로그인하는 데 문제가 있습니다.
- Azure Mobile Engagement API에서 연결 문제가 발생합니다.
- 장치 API를 통해 앱 정보 태그를 업로드하는 데 문제가 있습니다.
- Azure Mobile Engagement에서 로그나 내보낸 데이터를 다운로드하는 데 문제가 있습니다.
- Azure Mobile Engagement UI에 잘못된 정보가 표시됩니다.
- Azure Mobile Engagement 로그에 잘못된 정보가 표시됩니다.

**원인**

- Azure Mobile Engagement 연결 관련 문제:
    - 사용자 계정에 작업을 수행할 수 있는 권한이 있는지 확인합니다.
    - 문제가 단일 컴퓨터나 로컬 네트워크에서만 발생하지 않는지 확인합니다.
    - Azure Mobile Engagement 서비스에서 작동 중단이 보고되지 않았는지 확인합니다.
    - 앱 정보 태그 파일이 다음 규칙을 모두 따르는지 확인합니다.
        - UTF8 문자 집합만 사용합니다. ANSI 문자 집합은 지원되지 않습니다.
        - 쉼표(",")를 구분 기호 문자로 사용합니다. .csv 구문 기호 문자를 쉼표(",")에서 세미콜론(";") 등의 다른 문자로 변경하도록 요청하는 서비스 요청을 개설할 수 있습니다.
        - 부울 값 "TRUE"와 "FALSE"에 대문자만 사용합니다.
        - 최대 파일 크기인 35MB보다 작은 파일을 사용합니다.

**참고 항목:**

[API 설명서][Link 4], [UI 설명서 - 홈][Link 1]
 
<a name="#SERVICE3">**증상:**
3. 기능 요청</a>

- 사용하려는 기능이 Azure Mobile Engagement에 아직 포함되어 있지 않은 것으로 보입니다.

**원인**

Azure Mobile Engagement에 아직 포함되어 있지 않은 새 기능을 제안하려면
    - Azure Mobile Engagement에서 사용하려는 새 기능에 대해 최대한 많은 세부 정보를 포함하여 Azure Mobile Engagement 서비스 요청을 개설합니다.
 
## <a name="#SDK">SDK</a>

Azure Mobile Engagement를 응용 프로그램에 통합하는 방법과 관련된 문제에 대해 설명합니다.

**증상 목록**

1. <a href="#SDK1">응용 프로그램의 다른 영역에서 발생한 오류를 통해 검색된 Azure Mobile Engagement SDK의 문제</a>
2. <a href="#SDK2">고급 코딩 문제</a>
3. <a href="#SDK3">응용 프로그램 작동 중단 문제</a>
4. <a href="#SDK4">앱 스토어 업로드 오류</a>
 
<a name="#SDK1">**증상:**
1. 응용 프로그램의 다른 영역에서 발생한 오류를 통해 검색된 Azure Mobile Engagement SDK의 문제</a>

- 분석, 모니터링, 구분 또는 대시보드의 UI 데이터 수집 오류
- 푸시 오류(푸시가 앱 내부 또는 외부나 두 위치에서 모두 작동하지 않음)
- 고급 기능 오류(추적, 지리적 위치 또는 플랫폼별 푸시 미작동)
- API 오류(API에서 자동 오류가 자주 발생하며 오류 메시지가 표시되지 않음)
- 서비스 오류(Azure Mobile Engagement 기능이 응용 프로그램에서 작동하지 않음)

**원인**

- Azure Mobile Engagement SDK를 사용하여 해결해야 하는 대부분의 문제는 UI 데이터 수집 오류, 푸시 오류, 고급 기능 오류, API 오류, 응용 프로그램 작동 중단, 명백한 서비스 중단과 같은 응용 프로그램의 오류를 통해 검색됩니다.  
- Azure Mobile Engagement의 특정 기능이 앱에서 한 번도 정상적으로 작동한 적이 없다면 통합을 완료해야 합니다. 
- Azure Mobile Engagement의 특정 기능이 작동하다가 중지되는 경우에는 Azure Mobile Engagement SDK를 사용하여 최신 버전으로 업그레이드해야 할 수 있습니다. Azure Mobile Engagement에서 지원하는 각 플랫폼(Android, iOS, 웹, Windows, Windows Phone)에 대해 다른 버전의 Azure Mobile Engagement SDK가 제공됩니다.

*SDK 통합* 

- Azure Mobile Engagement가 SDK에 올바르게 통합되지 않았습니다(분석).
- 도달률이 SDK에 올바르게 통합되지 않았습니다(앱 내 푸시/앱 외부 푸시).
- 인증서가 만료되었거나 프로덕션/개발 버전이 잘못되었습니다(iOS에만 해당).
- GCM 또는 ADM이 SDK에 올바르게 통합되지 않았습니다(Android에만 해당 - 서비스별 푸시).
- 추적이 SDK에 올바르게 통합되지 않았습니다(설치 스토어 추적).
- 지연 위치 또는 GPS 위치가 SDK에 올바르게 통합되지 않았습니다(지리적 위치 기준 대상 지정).
[SDK 설명서 - 통합 가이드][Link 5], [문제 해결 가이드 - 푸시][Link 2]

*SDK 업그레이드*

- SDK를 업그레이드하여 이전 버전 SDK의 문제를 해결해야 합니다(대개 최신 버전 장치 OS와 관련이 있음).
- 모든 이전 버전 앱을 장치에서 제거한 다음 최신 버전 앱을 다시 설치하고 Azure Mobile Engagement UI에서 장치 ID를 다시 등록하여 장치에서 최신 버전의 앱을 사용 중임을 확인합니다.
[SDK 설명서 - 릴리스 정보](http://go.microsoft.com/fwlink/?LinkId= 525554), [SDK 설명서 - 업그레이드 가이드](http://go.microsoft.com/fwlink/?LinkId= 525554), [UI 설명서 - 설정][Link 1]

*기타 SDK 관련 문제*

- Azure Mobile Engagement 관련 코드 섹션의 오류로 인해 Azure Mobile Engagement가 작동하지 않습니다.
- 응용 프로그램 매니페스트 "AndroidManifest.xml"의 오류로 인해 Azure Mobile Engagement가 작동하지 않습니다(Android에만 해당).
- SDK 통합과 API 사용에 관한 일반적인 문제는 
- SDK 키와 API 키를 혼동한다는 점입니다.
[개념 - 용어집][Link 6]
 
<a name="#SDK2">**증상:**
2. 고급 코딩 문제</a>

-  Azure Mobile Engagement와 직접 관련되지 않은 플랫폼별 코드로 인해 iOS, Android, Windows Phone에서 문제가 발생할 수 있습니다.

**원인**

- Azure Mobile Engagement의 고급 코딩 문제는 대부분 Azure Mobile Engagement와 직접적인 관련이 없는 플랫폼별 코드를 잘못 작성하는 경우 발생합니다. 이 경우에는 Android, iOS, 웹, Windows, Windows Phone 등 개발을 진행 중인 플랫폼과 관련된 설명서 및 Azure Mobile Engagement 설명서를 모두 확인해야 합니다.
- "범주"를 잘못 구성하면 알림에서 앱 내부나 외부의 다른 위치로 연결하지 못하게 됩니다(Android에만 해당). 
- iOS 코드에서 "UIKit.framework"를 "optional"로 설정하지 않으면 "기호를 찾을 수 없음" 오류가 표시되거나 이전 버전의 iOS 장치 작동이 중단됩니다(iOS에만 해당).
- 인증서가 잘못되었거나 인증서의 개발 버전 또는 프로덕션 버전을 잘못 사용하면 푸시 문제가 발생합니다(iOS에만 해당).
- Android 및 iOS에서 시스템 센터가 앱 외부 푸시에 대해 작동하는 방식 등 Azure Mobile Engagement에서 제어할 수 없는 플랫폼의 기본적인 제한이 있습니다.
- Azure Mobile Engagement는 iOS 및 Android에 대해 참조용으로 Azure Mobile Engagement에서 사용하는 내부 패키지의 전체 목록을 게시합니다. Azure Mobile Engagement의 일부 기능은 Android, iOS, 웹, Windows, Windows Phone 등의 특정 플랫폼에만 제공됩니다.
- 각 플랫폼용 SDK는 다음 프로그래밍 언어로 작성됩니다.
    -     Android SDK - Java
    -     iOS SDK - objective C
    -     웹 SDK - JavaScript
    -     Windows SDK - C# 및 JavaScript
    -     Windows Phone SDK - C# 및 JavaScript

**참고 항목:**

 - [문제 해결 가이드 - 푸시][Link 2], [SDK 설명서 - 릴리스 정보][Link 5], [SDK 설명서 - 업그레이드 가이드][Link 5], [SDK 설명서 - Android - Azure Mobile Engagement 기술 설명서 개요][Link 5], [SDK 설명서 - iOS - Azure Mobile Engagement 기술 설명서 개요][Link 5], [SDK 설명서 - iOS - Apple 푸시 알림을 받도록 응용 프로그램을 준비하는 방법][Link 5], [Android 개발자](https://developer.android.com/), [iOS 개발자](https://developer.apple.com/), [Windows 개발자](https://developer.windows.com/) 
 
<a name="#SDK3">**증상:**
3.    응용 프로그램 작동 중단 문제</a>

- 최종 사용자 장치에서 응용 프로그램 작동이 중단됩니다.

**원인**

- 분석 UI 또는 분석 API에서 작동 중단 정보를 확인할 수 있습니다.
- 테스트 장치의 장치 ID를 찾은 다음 최종 사용자가 작동 중단 문제를 파악할 수 있도록 응용 프로그램 작동을 중단시킨 동일 작업을 수행할 수 있습니다.
- 최신 버전의 SDK로 업그레이드하면 응용 프로그램 작동 중단의 원인이 되는 Azure Mobile Engagement SDK의 알려진 문제가 해결되는 경우도 있습니다. 따라서 작동 중단 문제를 조사할 때는 사용 중인 플랫폼의 릴리스 정보를 확인하세요.

**참고 항목:**

- [개념 - FAQ][Link 6], [개념 - 용어집][Link 6], [API 설명서 - 분석 API - 작동 중단][Link 4], [UI 설명서 - 분석 - 작동 중단][Link 1], [UI 설명서 - 설정][Link 1], [SDK 설명서 - 릴리스 정보][Link 5], [SDK 설명서 - 업그레이드 가이드][Link 5]

<a name="#SDK4">**증상:**
4. 앱 스토어 업로드 오류</a>

- 최신 버전 앱을 iTunes, GooglePlay, Windows 또는 Windows Phone 스토어에 업로드할 때 발생하는 오류에 대해 설명합니다.

**원인**

- 앱 스토어에서는 특정 기능이 사용하도록 설정된 앱을 차단할 수 있습니다. 예를 들어 iTunes 스토어에서는 앱에서 IDFV 사용을 금지하며, GooglePlay 스토어에서는 앱 간의 응용 프로그램 정보 공유를 금지합니다. 
- 스토어에 앱을 업로드하는 데 문제가 있으면 최신 버전 SDK 및 사용 중인 플랫폼의 릴리스 정보를 확인하세요.

**참고 항목:**

- [SDK 설명서 - 릴리스 정보][Link 5], [SDK 설명서 - 업그레이드 가이드][Link 5] 

## <a name="#SR">SR 문제 해결 정보</a>

Azure Mobile Engagement 서비스 요청을 개설할 때는 다음 정보를 제공하세요.
 
**ID: 문제와 관련된 해당 식별자 제공**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**오류: 문제와 관련된 해당 오류 정보 제공**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [문제 해결 가이드](http://go.microsoft.com/fwlink/?LinkId=524382)


**코드: 문제와 관련된 해당 코딩 정보 제공**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
