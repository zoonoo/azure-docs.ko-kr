---
title: Azure Application Insights 가용성 테스트 문제 해결 | Microsoft Docs
description: Azure Application Insights에서 웹 테스트를 해결 합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305118"
---
# <a name="troubleshooting"></a>문제 해결

이 문서는 가용성 모니터링을 사용 하는 경우 발생할 수 있는 일반적인 문제를 해결 하는 데 도움이 됩니다.

## <a name="ssltls-errors"></a>SSL/TLS 오류

|증상/오류 메시지| 가능한 원인|
|--------|------|
|SSL/TLS 보안 채널을 만들 수 없습니다.  | SSL 버전입니다. 유일한 TLS 1.0, 1.1 및 1.2만 지원 됩니다. **SSLv3은 사용 하는 것이 없습니다.**
|TLSv1.2 레코드 계층: 경고 (수준: 심각한를 설명 합니다. 잘못 된 레코드 MAC)| 에 대 한 참조 StackExchange 스레드 [자세한 내용은](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)합니다.
|CDN (Content Delivery Network)에 실패 하는 URL은 | 이 CDN에 잘못 된 구성으로 발생할 수 있습니다. |  

### <a name="possible-workaround"></a>가능한 해결 방법

* 종속 리소스에 문제가 발생 하는 Url 항상 인 것이 좋습니다 사용 하지 않으려면 **종속 요청 구문 분석** 웹 테스트에 대 한 합니다.

## <a name="test-fails-only-from-certain-locations"></a>특정 위치 에서만에서 테스트 실패

|증상/오류 메시지| 가능한 원인|
|----|---------|
|연결 된 파티 시간이 지나면 제대로 응답 하지 않아서 연결 시도가 실패 했습니다.  | 특정 위치에서 테스트 에이전트는 방화벽에 의해 차단 되 고 됩니다.|
|    |(부하 분산 장치를 지역에서 traffic manager, Azure Express Route.)를 통해 발생 하는 특정 IP 주소의 경로 재정의 
|    |Azure ExpressRoute를 사용 하는 경우 시나리오가 있는 경우에서 패킷이 삭제할 수 있습니다 위치 [비대칭 라우팅이 발생](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)합니다.|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>프로토콜 위반 오류로 일시적 테스트 오류

|증상/오류 메시지| 가능한 원인|
|----|---------|
프로토콜 위반 CR LF가 야 합니다. | 이 잘못 된 형식의 헤더가 검색 되 면 발생 합니다. 특히 일부 헤더는 사용 하지 않을 수도 CRLF HTTP 사양을 위반 하는 줄의 끝을 나타내기 위해 및.NET WebRequest 수준에서 유효성 검사가 실패 합니다.
 || 이 부하 분산 장치 또는 Cdn에서 발생할 수도 있습니다.

> [!NOTE]
> HTTP 헤더의 유효성 검사가 완화된 브라우저에서는 URL이 실패하지 않을 수 있습니다. 이 문제에 대한 자세한 설명은 http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/ 블로그 게시물을 참조하세요.  

## <a name="common-troubleshooting-questions"></a>일반 문제 해결 질문

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>사이트는 괜찮아 보이지만 표시 테스트 실패? 이유는 Application Insights 경고 me?

   * 테스트 있습니까 **종속 요청 구문 분석** 사용? 결국 스크립트, 이미지 등과 같은 리소스에 대한 엄격한 검사가 실시됩니다. 이러한 유형의 오류는 브라우저에서 눈에 띄지 않을 수 있습니다. 모든 이미지, 스크립트, 스타일 시트 및 페이지에 의해 로드된 다른 파일을 확인합니다. 그 중 하나라도 실패 하면 테스트는 실패로 보고 문제 없이 기본 HTML 페이지를 로드 하는 경우에 합니다. 테스트에 이러한 리소스 오류에 둔감 해 지도록 하려면 단순히 테스트 구성에서 종속 요청 구문 분석을 선택 취소 합니다.

   * 노이즈가에서 일시적인 네트워크 블 립 등을 줄이기 위해 구성을 선택 해야 하는 테스트 실패에 대 한 활성화 다시 시도 확인 합니다. 또한 더 많은 위치에서 테스트하고 그에 따라 경고 규칙 임계값을 관리하여 과도한 경고를 유발하는 특정 위치의 문제를 방지할 수 있습니다.

   * 검색 탐색기에서 모든 가용성 오류 또는 가용성 환경에서 모든 빨간색 점을 클릭하여 해당 오류를 보고한 이유에 대한 세부 정보를 확인합니다. 테스트 결과는 상호 관련된 서버 쪽 원격 분석(사용하도록 설정된 경우)과 함께 테스트가 실패한 이유를 이해하는 데 도움이 되어야 합니다. 일시적인 문제의 일반적인 원인은 네트워크 또는 연결 문제에 있습니다.

   * 테스트에 시간 제한이 있었나요? 2분 후 테스트를 중단합니다. Ping 또는 다단계 테스트에 2분이 초과하면 실패로 보고합니다. 테스트를 더 짧은 기간에 완료할 수 있도록 여러 세션으로 분할하는 것이 좋습니다.

   * 모든 위치 또는 그 중 일부에서만 실패를 보고했나요? 일부에서만 실패를 보고한 경우는 네트워크/CDN 문제일 수도 있습니다. 마찬가지로 빨간색 점을 클릭하면 그 위치에서 실패를 보고한 이유를 파악하는 데 도움이 됩니다.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>경고가 트리거되면 또는 해결 하는 경우 전자 메일 또는 둘 다 가져오지 않은 있나요?

클래식 경고 구성을 선택하여 이메일을 직접 나열하거나 배포 목록에서 알림을 수신하도록 구성되었는지 확인합니다. 그렇다면 배포 목록 구성을 선택하여 해당 목록에서 외부 이메일을 수신할 수 있는지 확인합니다. 또한 메일 관리자에게 이 문제를 일으킬 수 있도록 구성된 정책이 있는지 확인합니다.

### <a name="i-did-not-receive-the-webhook-notification"></a>웹 후크 알림을 받지 못했습니다?

webhook 알림을 수신하는 애플리케이션이 사용 가능하며 webhook 요청을 성공적으로 처리하는지 확인합니다. 자세한 내용은 [이것](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)을 참조하세요.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>프로토콜 위반 오류로 일시적 테스트 오류?

이 오류("프로토콜 위반, CR 뒤에 LF가 와야 함")는 서버(또는 종속성)와 관련된 문제를 나타내며 잘못된 헤더가 응답에 설정된 경우에 발생합니다. 부하 분산 장치 또는 CDN으로 인해 발생할 수 있습니다. 특히 일부 헤더 사용 하지 않을 수도 CRLF HTTP 사양을 위반 하는 줄의 끝을 표시 하 여.NET WebRequest 수준에서 유효성 검사가 실패 합니다. 위반에서 될 수 있는 위반한 헤더에 대 한 응답을 검사 합니다.

> [!NOTE]
> HTTP 헤더의 유효성 검사가 완화된 브라우저에서는 URL이 실패하지 않을 수 있습니다. 이 문제에 대한 자세한 설명은 http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/ 블로그 게시물을 참조하세요.  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>테스트 실패를 진단 하는 서버 쪽 원격 분석 관련 보이지? *

서버 쪽 애플리케이션에 대해 Application Insights를 설정한 경우, [샘플링](../../azure-monitor/app/sampling.md)이 작동 중이기 때문일 수 있습니다. 다른 가용성 결과를 선택합니다.

### <a name="can-i-call-code-from-my-web-test"></a>웹 테스트에서 코드를 호출할 수 있나요?

아니요. 테스트 단계는 .webtest 파일에 포함되어야 합니다. 또한 다른 웹 테스트를 호출하거나 루프를 사용할 수 없습니다. 그러나 몇 가지 유용한 플러그 인이 있습니다.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>"웹 테스트" 및 "가용성 테스트" 간의 차이?

두 용어는 같은 의미로 참조할 수 있습니다. 가용성 테스트는 다단계 웹 테스트 외에 단일 URL ping 테스트를 포함한 보다 일반적인 용어입니다.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>방화벽 뒤에서 실행 되는 내부 서버에서 가용성 테스트를 사용 하려고 합니다.

   가능한 해결 방법으로 다음 두 가지가 있습니다.

   * [웹 테스트 에이전트의 IP 주소](../../azure-monitor/app/ip-addresses.md)에서 들어오는 요청을 허용하도록 방화벽을 구성합니다.
   * 내부 서버를 정기적으로 테스트하는 사용자 고유의 코드를 작성합니다. 방화벽 뒤의 테스트 서버에서 코드를 백그라운드 프로세스로 실행합니다. 테스트 프로세스는 코어 SDK 패키지의 [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API를 사용하여 Application Insights에 결과를 보낼 수 있습니다. 이렇게 하려면 테스트 서버에 Application Insights 수집 엔드포인트에 대한 송신 액세스 권한이 있어야 하지만, 들어오는 요청을 허용하는 대체 방법보다 보안 위험이 훨씬 낮습니다. 결과는 가용성 웹 테스트 블레이드에 표시되지 않지만, 분석 탐색기, 검색 탐색기 및 메트릭 탐색기에는 가용성 결과로 표시됩니다.

### <a name="uploading-a-multi-step-web-test-fails"></a>다중 단계 웹 테스트 업로드 실패

이 실패가 발생하는 이유는 다음과 같습니다.
   * 300K의 크기 제한이 있습니다.
   * 루프는 지원되지 않습니다.
   * 다른 웹 테스트에 대한 참조는 지원되지 않습니다.
   * 데이터 원본은 지원되지 않습니다.

### <a name="my-multi-step-test-doesnt-complete"></a>다중 단계 테스트가 완료 되지 않으면

테스트당 100개 요청의 제한이 있습니다. 또한 테스트가 2분을 초과해 실행되는 경우에도 중지됩니다.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>클라이언트 인증서를 사용 하 여 테스트를 실행할 수는 방법

현재는 지원되지 않습니다.

## <a name="who-receives-the-classic-alert-notifications"></a>누가 (클래식) 경고 알림을 받나요?

이 섹션은 클래식 경고에만 적용되며, 원하는 수신자만 알림을 받도록 경고 알림을 최적화하는 데 도움이 됩니다. [클래식 경고](../platform/alerts-classic.overview.md)와 새 경고 환경 간의 차이점에 대해 자세히 알아보려면 [경고 개요 문서](../platform/alerts-overview.md)를 참조하세요. 새 경고 환경에서 경고 알림을 제어하려면 [작업 그룹](../platform/action-groups.md)을 사용합니다.

* 클래식 경고 알림에는 특정 수신자를 사용하는 것이 좋습니다.

* Y 위치에서 X에서 발생한 오류에 대한 경고의 경우 **대량/그룹** 확인란 옵션을 사용하도록 설정하면 관리자/공동 관리자 역할의 사용자에게 경고가 전송됩니다.  기본적으로 _구독_에 대한 _모든_ 관리자는 알림을 받게 됩니다.

* 가용성 메트릭에 대 한 경고에 대 한 합니다 **대량/그룹** 구독의 소유자, 참가자 또는 읽기 권한자 역할을 사용 하 여 사용자에 게 보내는 사용 하도록 설정 하는 경우 확인란 옵션입니다. 실제로 구독의 Application Insights 리소스에 대한 액세스 권한이 있는 _모든_ 사용자가 범위에 포함되며 알림을 받습니다. 

> [!NOTE]
> 현재 **대량/그룹** 확인란 옵션을 사용하고 있고 사용 안 함으로 설정하는 경우에는 변경 내용을 되돌릴 수 없습니다.

역할에 따라 사용자에게 알려야 하는 경우 새 경고 환경/거의 실시간 경고를 사용합니다. [작업 그룹](../platform/action-groups.md)을 사용하면 기여자/소유자/읽기 권한자 역할(단일 옵션으로 결합되지 않음)의 사용자에 대해 이메일 알림을 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [다중 단계 웹 테스트](availability-multistep.md)
* [URL ping 테스트](monitor-web-app-availability.md)
