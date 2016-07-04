<properties
	pageTitle="Azure 알림 허브 - FAQ(질문과 대답)"
	description="알림 허브에서 솔루션을 디자인/구현하는 방법과 관련한 FAQ"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc7777"
	manager="dwrede"
    keywords="푸시 알림, 푸시 알림, iOS 푸시 알림, Android 푸시 알림, iOS 푸시, Android 푸시"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="wesmc" />

#Azure 알림 허브로 푸시 알림 - 질문과 대답

##일반
###1\. 알림 허브의 가격 책정 모델은 무엇인가요?
알림 허브는 다음 세 가지 계층으로 제공됩니다.

* **무료** - 한 달 구독 당 최대 1백만 개의 푸시를 가져옵니다.
* **기본** - 할당량 증가 옵션으로 한 달 구독 당 1천만 개의 푸시를 가져옵니다.
* **표준** - 할당량 증가 옵션 및 다양한 원격 분석 기능으로 한 달 구독 당 천만 개의 푸시를 가져옵니다.

최신 세부 정보는 [알림 허브 가격 책정] 페이지에서 찾을 수 있습니다. 가격 책정은 구독 수준에서 설정되며 푸시 알림 시작의 수에 따르며 Azure 구독에서 만든 네임스페이스 및 알림 허브 횟수는 중요하지 않습니다.

**무료** 계층은 개발용으로 제공되며 SLA가 보장되지 않습니다. 이 계층은 Azure 알림 허브를 통해 푸시 알림 기능을 탐색하려는 사용자에게 권장되는 출발점이지만 중간에서 대규모 응용 프로그램에 대한 최상의 선택은 아닐 수 있습니다.

**기본** 및 **표준** 계층은 프로덕션용으로 제공되며, 다음의 주요 기능은 *표준 계층에서만* 사용 가능합니다.

- *다양한 원격 분석* - 알림 허브는 원격 분석 데이터 뿐만 아니라 오프라인 보기 및 분석에 대한 푸시 알림 등록 정보를 내보내는 많은 기능을 제공합니다.
- *다중 테넌트* - 여러 테넌트를 지원하도록 알림 허브를 사용하여 모바일 앱을 만드는 경우에 적합합니다. 그러면 앱의 알림 허브 네임스페이스 수준에서 PNS(푸시 알림 서비스) 자격 증명을 설정한 다음 테넌트를 구분하여 이 공통 네임스페이스에서 개별 허브를 테넌트에 제공할 수 있습니다. 그러면 유지 관리를 쉽게 수행하는 동시에 SAS 키가 각 테넌트에 대해 구분된 알림 허브에서 푸시 알림을 보내고 받을 수 있으므로 테넌트 간에 알림이 겹치지 않습니다.
- *예약된 푸시* - 푸시 알림을 예약할 수 있으며 이는 이후에 큐에 대기되고 전송됩니다.
- *대량 가져오기* - 등록을 대량으로 가져올 수 있습니다.

###2\. 알림 허브 SLA란?
**기본** 및 **표준** 알림 허브 계층의 경우에 올바르게 구성된 응용 프로그램은 지원되는 계층 내에 배포된 알림 허브와 관련된 푸시 알림을 보내거나 등록 관리 작업을 수행할 수 있다고 99.9% 이상의 성공률로 보장합니다. SLA에 대해 자세히 알아보려면 [알림 허브 SLA] 페이지를 방문하세요.

> [AZURE.NOTE] 알림 허브는 외부 플랫폼 공급자를 사용하여 장치에 푸시 알림을 보내기 때문에 플랫폼 알림 서비스와 장치 간의 레그에 대해서는 SLA가 보장되지 않습니다.

###3\. 어떤 고객이 알림 허브를 사용하나요?
많은 고객들이 알림 허브를 사용하고 있으며, 그 중 일부 주요 고객이 아래에 나와 있습니다.

* 2014년 소치 동계 올림픽 – 수 백 개의 관심 그룹에서 300만 대 이상의 장치를 사용하여 1억 5천만 개의 알림을 2주 동안 디스패치했습니다. [사례 연구 - 소치]
* Skanska -[ 사례 연구 - Skanska]
* Seattle Times - [사례 연구 - Seattle Times]
* Mural.ly - [사례 연구 - Mural.ly]
* 7Digital - [사례 연구 - 7Digital]
* Bing Apps – 수 천만 대의 장치에서 매일 3백만 건의 알림을 전송했습니다.

###4\. 알림 허브를 업그레이드 또는 다운그레이드하여 서비스 계층을 변경하려면 어떻게 해야 하나요?
[Azure 클래식 포털]로 이동하여 서비스 버스를 클릭한 다음 해당 네임스페이스를 클릭하고 알림 허브를 클릭합니다. 크기 조정 탭에서 알림 허브 서비스 계층을 변경할 수 있습니다.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##디자인 및 개발
###1\. 어떤 서버 쪽 플랫폼을 지원하나요?
.NET, Java, PHP, Python, Node.js용 SDK와 [전체 샘플]이 제공되므로 이러한 모든 플랫폼을 사용하여 알림 허브와 통신하도록 앱 백 엔드를 설정할 수 있습니다. 알림 허브 API는 REST 인터페이스에 기반하므로 추가 종속성을 추가하지 않으려는 경우 대신 직접 통신하는 것을 선택할 수 있습니다. 자세한 내용은 [NH - REST API] 페이지에서 찾을 수 있습니다.

###2\. 어떤 클라이언트 플랫폼이 지원되나요?
[Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows 유니버설](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China(Baidu 제공)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin([iOS](xamarin-notification-hubs-ios-push notification-apns-get-started.md) 및 [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome 앱](notification-hubs-chrome-push-notifications-get-started.md), [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) 플랫폼 등으로 푸시 알림을 보낼 수 있습니다. 이러한 플랫폼에서 푸시 알림을 보내는것을 방지하기 위한 자습서를 시작하는 전체 목록은 [NH - 시작된 자습서] 페이지를 방문하세요.

###3\. 문자 메시지/전자 메일/웹 알림이 지원되나요?
알림 허브는 기본적으로 위에 나열된 플랫폼을 사용하여 모바일 앱에 알림을 보내는 데 사용됩니다. 따라서 전자 메일이나 문자 메시지 알림를 보내는 기능은 제공되지 않습니다. 그러나 [Azure 모바일 앱]을 사용하여 네이티브 푸시 알림을 보내도록 이러한 기능을 제공하는 타사 플랫폼을 알림 허브와 통합할 수 있습니다.

또한 알림 허브는 브라우저 내 푸시 알림 배달 서비스를 기본적으로 제공하지 않습니다. 고객은 지원되는 서버 쪽 플랫폼에서 SignalR을 사용하여 이를 구현하도록 선택할 수 있습니다. Chrome 샌드박스에서 브라우저 앱에 알림을 보내는 방법을 찾으려는 경우 [Chrome 앱 자습서]를 확인합니다.

###4\. Azure 모바일 앱과 Azure 알림 허브 간에는 어떤 관련성이 있으며 이 두 항목은 각각 어떤 경우에 사용해야 하나요?
기존 모바일 앱 백 엔드가 있으며 푸시 알림을 보내는 기능만 추가하려는 경우 Azure 알림 허브를 사용할 수 있습니다니다. 모바일 앱 백 엔드를 처음부터 설정하려면 Azure 모바일 앱을 사용하는 것이 좋습니다. Azure 모바일 앱은 모바일 앱 백 엔드에서 푸시 알림을 쉽게 보낼 수 있도록 알림 허브를 자동으로 프로비전합니다. Azure 모바일 앱의 가격 책정에는 알림 허브의 기본 요금이 포함되며, 푸시 사용량이 기본으로 포함된 푸시를 초과하는 경우에만 요금을 지불하면 됩니다. 비용에 대한 자세한 정보는 [앱 서비스 가격 책정] 페이지에서 제공됩니다.

###5\. 알림 허브를 통해 푸시 알림을 보낼 경우 얼마나 많은 장치를 지원할 수 있나요?
지원되는 장치 수에 대한 내용은 [알림 허브 가격 책정] 페이지를 참조합니다.

특정 시나리오의 경우 천 만 개 이상의 등록된 장치에 대한 지원이 필요한 경우 [문의처](https://azure.microsoft.com/overview/contact-us/) 직접 수 문의하고 솔루션을 확장하도록 합니다.

###6\. 보낼 수 있는 푸시 알림 수는 몇 개인가요?
선택한 계층에 따라 Azure는 시스템에서 전송되는 알림 수에 따라 알림 허브가 자동으로 확장합니다.

>[AZURE.NOTE] 전체 사용 비용은 제공되는 푸시 알림 수에 따라 증가될 수 있습니다. [알림 허브 가격 책정] 페이지에 설명된 기존 계층 제한을 알고 있는지 확인합니다.

기존 고객들은 알림 허브를 사용하여 매일 수백만 건의 푸시 알림을 보내고 있습니다. Azure 알림 허브를 사용하는 한 푸시 알림 도달률의 크기를 조정하기 위해 특별한 작업을 수행할 필요가 없습니다.

###7\. 푸시 알림이 장치에 도달하는 데 시간이 얼마나 걸리나요?
수신 부하가 비교적 일정하며 기본적으로 수신량이 급증하지 않는 일반적인 사용 시나리오에서 Azure 알림 허브는 분당 **1백만 건 이상의 푸시 알림 전송**을 처리할 수 있습니다. 이 속도는 태그 수, 들어오는 보내기의 특성 및 기타 외부 요인에 따라 달라질 수 있습니다.

예상된 배달 시간 동안 서비스는 플랫폼당 대상 수를 계산한 다음 등록된 태그/태그 식을 기준으로 개별 푸시 알림 배달 서비스에 메시지를 라우팅할 수 있습니다. 그러면 PNS(푸시 알림 서비스)가 알림을 장치로 전송합니다.

PNS는 알림 배달에 대한 SLA를 보장하지 않습니다. 그러나 일반적으로 대부분의 푸시 알림은 Azure 알림 허브 플랫폼으로 전송된 시점에서 몇 분 이내에(일반적으로 10분이라는 제한 내에) 대상 장치로 배달됩니다. 배달 시간이 더 오래 걸리는 일부 이상값이 있을 수도 있습니다.

>[AZURE.NOTE] Azure 알림 허브에서는 30분 이내에 PNS로 배달할 수 없는 푸시 알림은 삭제하는 내부 정책을 사용합니다. 이러한 지연은 여러 가지 원인으로 인해 나타날 수 있는데, 그 중 가장 일반적인 이유는 PNS에서 응용 프로그램을 제한하는 경우입니다.

###8\. 대기 시간 보장이 있나요?
푸시 알림은 외부 플랫폼별 푸시 알림 서비스를 통해 배달되는 특성이 있으므로 대기 시간이 보장되지 않습니다. 대부분의 푸시 알림은 보통 몇 분 이내에 배달됩니다.

###9\. 네임스페이스와 알림 허브를 사용하는 솔루션을 디자인할 때 고려해야 하는 사항은 무엇인가요?

####모바일 앱/환경

* 환경당 모바일 앱마다 알림 허브가 하나씩 있어야 합니다.
* 다중 테넌트 시나리오에서는 각 테넌트에 별도의 허브가 있어야 합니다.
* 같은 알림 허브를 테스트 환경과 프로덕션 환경에서 공유해서는 안 됩니다. 이렇게 하면 알림을 보내는 중에 문제가 발생할 수 있기 때문입니다. 각기 별도의 자격 증명을 사용하는 샌드박스 및 프로덕션 푸시 끝점을 제공하는 Apple의 경우를 예로 들어 보겠습니다.
* 기본적으로 Azure 포털 또는 Visual Studio의 Azure 통합 구성 요소를 통해 등록된 장치에 테스트 알림을 보낼 수 있습니다. 임계값은 등록 풀에서 임의로 선택된 10개의 장치에 설정됩니다.

>[AZURE.NOTE] 원래 Apple 샌드박스 인증서를 사용하여 허브를 구성했다가 Apple 프로덕션 인증서를 사용하도록 허브를 다시 구성하면 이전 장치 토큰을 새 인증서에서 사용할 수 없게 되므로 푸시가 실패합니다. 따라서 프로덕션 환경과 테스트 환경을 분리하고 각 환경에 서로 다른 허브를 사용하는 것이 가장 좋습니다.

####PNS 자격 증명

Apple, Google 등의 플랫폼 개발자 포털에 모바일 앱을 등록하면 앱 식별자 및 보안 토큰이 제공됩니다. 장치에 푸시 알림을 보내려면 앱 백 엔드에서 플랫폼의 푸시 알림 서비스에 이 식별자와 토큰을 제공해야 합니다. 이러한 보안 토큰은 인증서(Apple iOS, Windows Phone) 또는 보안 키(Google Android, Windows) 형식일 수 있으며, 알림 허브에서 구성해야 합니다. 일반적으로는 알림 허브 수준에서 토큰을 구성하지만 다중 테넌트 시나리오에서는 네임스페이스 수준에서 구성할 수도 있습니다.

####네임스페이스

네임스페이스를 배포 그룹화에 사용할 수 있습니다. 또한 다중 테넌트 시나리오에서 같은 앱의 모든 테넌트에 대해 모든 알림 허브를 표시하는 데 사용할 수도 있습니다.

####지역 배포

지역 배포가 푸시 알림 시나리오에서 반드시 중요한 것은 아닙니다. 푸시 알림을 장치로 최종 배달하는 APNS, GCM 등의 여러 푸시 알림 서비스도 균일하게 분산되어 있지 않습니다.

응용 프로그램이 전 세계에서 사용되는 경우에는 각기 다른 네임스페이스로 여러 허브를 만들어 전 세계 여러 Azure 지역에서 알림 허브 서비스를 활용할 수 있습니다.

>[AZURE.NOTE] 이 방식은 관리 비용, 특히 등록 관련 비용을 증가시키므로 권장하지 않으며 명시적으로 필요한 경우에만 사용해야 합니다.

###10\. 등록은 앱 백 엔드에서 하나요? 아니면 클라이언트 장치를 통해 직접 하나요?
등록 작성 전에 클라이언트 인증을 거쳐야 하거나 특정 앱 논리에 따라 앱 백 엔드에서 태그를 만들어야 하는 경우 앱 백 엔드에서 등록하는 것이 유용합니다. 자세한 내용은 [백 엔드 등록 지침] 및 [백 엔드 등록 지침 - 2] 페이지에서 자세히 알아볼 수 있습니다.

###11\. 푸시 알림 배달 보안 모델이란?
Azure 알림 허브에서는 [SAS(공유 액세스 서명)](../storage/storage-dotnet-shared-access-signature-part-1.md) 기반 보안 모델을 사용합니다. 루트 네임스페이스 수준 또는 상세한 알림 허브 수준에서 SAS 토큰을 사용할 수 있습니다. 메시지 보내기 권한, 알림 수신 대기 권한 등의 여러 권한 부여 규칙을 사용하여 이러한 SAS 토큰을 설정할 수 있습니다. 자세한 내용은 [NH 보안 모델] 문서에 있습니다.

###12\. 푸시 알림의 중요한 페이로드를 어떻게 처리해야 하나요?
모든 알림은 플랫폼의 PNS(푸시 알림 서비스)를 통해 대상 장치로 배달됩니다. 보낸 사람이 Azure 알림 허브로 보내는 알림은 알림 허브에서 처리된 다음 개별 PNS로 전달됩니다.

보낸 사람으로부터 Azure 알림 허브를 거쳐 PNS로 가는 모든 연결에서는 HTTPS를 사용합니다.

>[AZURE.NOTE] Azure 알림 허브는 어떤 방식으로도 메시지 페이로드를 기록하지 않습니다.

그러나 기밀 페이로드를 보내는 경우 보안 푸시 패턴을 사용하는 것이 좋습니다. 이 패턴에서는 보낸 사람이 기밀 페이로드를 포함하지 않고 메시지 식별자가 들어 있는 'ping' 알림을 장치로 전송하며 장치의 앱이 해당 페이로드를 수신한 경우 안전한 API를 직접 호출하여 메시지 세부 정보를 가져올 수 있습니다. 위에서 설명한 패턴을 구현하는 방법에 대한 가이드는 [NH - 안전한 푸시 자습서] 페이지에서 제공됩니다.

##작업
###1\. DR(재해 복구)은 어떻게 진행되나요?
끝에서 알림 허브 이름, 연결 문자열 및 기타 중요한 정보에 대한 메타데이터 재해 복구를 제공합니다. DR 시나리오가 트리거되면 등록 데이터는 손실될 알림 허브 인프라의 **유일한 세그먼트**입니다. 솔루션을 구현하여 이 데이터를 새 허브 사후 복구에 다시 채워야 합니다.

- *1단계* - 다른 데이터 센터에서 보조 알림 허브를 만듭니다. DR 이벤트 발생 시에 보조 알림 허브를 즉시 만들 수도 있고 처음부터 만들어 둘 수도 있습니다. 알림 허브 프로비전은 몇 초 단위인 비교적 빠른 프로세스이므로 선택하는 옵션으로 큰 차이가 나지 않습니다. 그러나 보조 알림 허브를 처음에 만들면 DR 이벤트가 관리 기능에 영향을 주지 않으므로 미리 만들어 두는 옵션을 선택하는 것이 좋습니다.

- *2단계* - 주 알림 허브의 등록 정보를 보조 알림 허브에 입력합니다. 두 허브의 등록을 동시에 유지 관리하면서 등록이 수신되는 즉시 두 허브를 동기화 상태로 유지하는 방식은 사용하지 않는 것이 좋습니다. PNS에서 만료되는 등록의 기본적인 성향 때문에 이러한 방식은 원활히 작동하지 않습니다. 만료되거나 잘못된 등록에 대한 PNS 피드백이 수신되면 알림 허브에서는 해당 등록을 정리합니다.

따라서 다음과 같은 앱 백 엔드를 사용하는 것이 좋습니다.

- DR 시 보조 알림 허브로의 대량 삽입을 수행할 수 있도록 끝에 지정된 등록 집합을 자체적으로 유지 관리

**또는**

- 주 허브의 일반 등록 덤프를 백업으로 가져온 다음 보조 NH로 대량 삽입하는 백 엔드

>[AZURE.NOTE] 표준 계층에서 제공되는 등록 내보내기/가져오기는 [등록 내보내기/가져오기] 문서에서 설명합니다.

백 엔드가 없는 경우 앱이 대상 장치에서 시작될 때 보조 알림 허브에 새 등록을 수행하고 보조 알림 허브에 등록된 활성 장치가 모두 있게 됩니다.

단점은 앱을 열어 두지 않은 장치가 알림을 받을 때 시간 간격이 있다는 점입니다.

###2\. 감사 로그 기능이 있나요?
모든 알림 허브 관리 작업은 [Azure 클래식 포털]에 표시되는 작업 로그에 기록됩니다.

##모니터링 및 문제 해결
###1\. 어떤 문제 해결 기능이 제공되나요?
Azure 알림 허브에서는 일반적인 문제 해결을 수행할 수 있는 여러 기능을 제공하며, 특히 알림 삭제와 관련한 가장 일반적인 시나리오에서 사용 가능한 기능을 제공합니다. 자세한 내용은 [NH - 문제 해결] 백서를 참조하세요.

###2\. 어떤 원격 분석 기능이 제공되나요?
Azure 알림 허브에서는 [Azure 클래식 포털]의 원격 분석 데이터를 확인할 수 있습니다. 사용 가능한 메트릭에 대한 자세한 내용은 [NH - 메트릭] 페이지에서 제공됩니다.

>[AZURE.NOTE] 정상적인 알림은 푸시 알림이 외부 푸시 알림 서비스(예: Apple의 APNS, Google의 GCM 등)에 배달되었음을 의미합니다. 대상 장치에 알림을 제공하는 PNS가 담당합니다. 일반적으로 PNS는 제 3자에게 배달 메트릭을 노출하지 않습니다.

**표준** 계층에서는 프로그래밍 방식으로 원격 분석 데이터를 내보내는 기능도 제공합니다. 자세한 내용은 [NH - 메트릭 샘플]을 참조하세요.

[Azure 클래식 포털]: https://manage.windowsazure.com
[알림 허브 가격 책정]: http://azure.microsoft.com/pricing/details/notification-hubs/
[알림 허브 SLA]: http://azure.microsoft.com/support/legal/sla/
[사례 연구 - 소치]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[ 사례 연구 - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[사례 연구 - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[사례 연구 - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[사례 연구 - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - 시작된 자습서]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome 앱 자습서]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[백 엔드 등록 지침]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[백 엔드 등록 지침 - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH 보안 모델]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - 안전한 푸시 자습서]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - 문제 해결]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - 메트릭]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - 메트릭 샘플]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[등록 내보내기/가져오기]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[전체 샘플]: https://github.com/Azure/azure-notificationhubs-samples
[Azure 모바일 앱]: https://azure.microsoft.com/ko-KR/services/app-service/mobile/
[앱 서비스 가격 책정]: https://azure.microsoft.com/ko-KR/pricing/details/app-service/

<!---HONumber=AcomDC_0622_2016-->