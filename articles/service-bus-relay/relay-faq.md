---
title: "Azure 릴레이 FAQ(질문과 대답) | Microsoft Docs"
description: "Azure 릴레이에 대한 일부 자주 묻는 질문을 답변합니다."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 22781ec965decbfd33923478794a76692151c363


---
# <a name="relay-faq"></a>릴레이 FAQ
이 문서는 Microsoft Azure 릴레이에 대한 일부 자주 묻는 질문을 답변합니다. 또한 일반적인 Azure 가격 책정 및 지원 정보는 [Azure 지원 FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) 를 방문할 수 있습니다.

## <a name="general-questions"></a>일반적인 질문
### <a name="what-is-azure-relay"></a>Azure 릴레이란?
Azure [릴레이 서비스](relay-what-is-it.md)는 방화벽 연결을 열거나 회사 네트워크 인프라를 주입식으로 변경하지 않고도 회사 엔터프라이즈 네트워크 내에 있는 서비스를 공용 클라우드에 안전하게 노출할 수 있게 함으로써 이 작업을 도와줍니다.

### <a name="what-is-a-relay-namespace"></a>릴레이 네임스페이스란?
[네임스페이스](relay-create-namespace-portal.md)는 응용 프로그램 내에서 릴레이 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다. 네임스페이스를 만드는 작업은 릴레이를 사용하는 데 필요하고 시작하는 첫 번째 단계 중 하나입니다.

### <a name="what-happened-to-the-previously-named-relay-service"></a>이전에 명명된 릴레이 서비스는 어떻게 되었나요?
이전에 명명된 **릴레이** 서비스는 이제 *WCF 릴레이*라고 합니다. 평소처럼 이 서비스를 계속 사용할 수 있습니다. 하이브리드 연결은 BizTalk에서 옮겨진 서비스의 업데이트된 버전입니다. WCF 릴레이 및 하이브리드 연결은 앞으로도 모두 계속 지원될 예정입니다.

## <a name="pricing"></a>가격
이 섹션은 릴레이 가격 책정 구조에 대한 일부 자주 묻는 질문을 답변합니다. 또한 일반적인 Microsoft Azure 가격 책정 정보는 [Azure 지원 FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) 를 방문할 수 있습니다. 릴레이 가격 책정에 대한 전체 내용은 [Service Bus 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/service-bus/)를 참조하세요.

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>하이브리드 연결 및 WCF 릴레이의 요금은 어떻게 청구되나요?
릴레이 가격 책정에 대한 전체 내용은 [Service Bus 가격 정보][Pricing overview]를 참조하세요. 언급된 가격 외에도 응용 프로그램이 프로비전되는 데이터 센터의 외부에서 송신을 위해 연결된 데이터 전송에 요금이 부과됩니다.

### <a name="how-am-i-billed-for-hybrid-connections"></a>하이브리드 연결의 요금은 어떻게 청구되나요?
세 가지 예제 시나리오는 다음과 같습니다.

1. 하이브리드 연결 관리자의 인스턴스와 같은 단일 수신기가 설치되어 한 달간 계속 실행하고 3GB의 데이터를 전송하면 총 요금은 $5입니다.
2. 설치되어 한 달 전체 동안 계속 실행되는 하이브리드 연결 관리자의 인스턴스와 같은 단일 수신기가 있고 해당 월 동안 연결에서 10GB의 데이터를 전송하면 총 요금은 $7.50(연결 및 처음 5GB에 대한 5 + 추가 5GB의 데이터에 대한 $2.50)이 됩니다.
3. 설치되어 한 달 전체 동안 계속 실행되는 하이브리드 연결 관리자의 인스턴스 두 개 A와 B가 있고 연결 A에서 3GB의 데이터를 전송하고 연결 B에서 6GB의 데이터를 전송하면 총 요금은 $10.50(연결 A에 대한 $5 + 연결 B에 대한 $5 + 연결 B에서의 6번째 GB에 대한 $0.50)이 됩니다.

예제에 사용된 가격은 미리 보기 기간에만 적용되며 하이브리드 연결의 일반 공급 시 변경될 수 있습니다.

### <a name="how-are-wcf-relay-hours-calculated"></a>WCF 릴레이 시간은 어떻게 계산되나요?
릴레이 시간은 각 Service Bus Relay가 "열려 있는" 동안의 누적 시간 양에 대해 청구됩니다. 릴레이는 릴레이를 사용 가능한 WCF 서비스 또는 "릴레이 수신"이 먼저 해당 주소에 연결될 때 암시적으로 인스턴스화되고 지정된 Service Bus 주소(서비스 네임스페이스 URL)에서 열립니다. 마지막 리스너가 해당 주소에서의 연결을 해제하는 경우에만 닫힙니다. 따라서 요금 청구의 경우 릴레이는 첫 번째 릴레이 수신기가 연결한 시간에서 해당 릴레이의 Service Bus 주소에서 마지막 릴레이 수신기의 연결이 끊어진 시간까지 "열린" 것으로 간주됩니다.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>지정된 릴레이에 둘 이상의 수신기가 연결된 경우 어떻게 하나요?
경우에 따라 단일 릴레이에 연결된 수신기가 여러 개 있을 수 있습니다. 하나 이상의 릴레이 수신기가 연결된 경우 릴레이는 "열린" 것으로 간주됩니다. 열린 릴레이에 수신기를 추가하면 릴레이 시간이 추가됩니다. 또한 릴레이에 연결된 릴레이 발신자(메시지를 릴레이에 호출하거나 전송하는 클라이언트)의 수는 릴레이 시간을 계산하는 데 영향을 주지 않습니다.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>WCF 릴레이 대한 메시지 측정기를 어떻게 계산하나요?
**이는 WCF 릴레이에만 적용되며 하이브리드 연결에 대한 비용이 아닙니다.**

일반적으로 조정된 엔터티(큐, 항목 및 구독)에 대해 위에서 설명한 동일한 메서드를 사용하여 릴레이에 대한 청구 가능한 메시지를 계산합니다. 하지만 몇 가지 주목할 차이점이 있습니다.

Service Bus Relay로 메시지를 보내면 릴레이 수신기로 전달하는 데 이어진 Service Bus Relay에 전달하지 않고 메시지를 받는 릴레이 수신기에 "전체 스루" 보내기로 처리됩니다. 따라서 릴레이 수신기에 대한 요청-응답 형식 서비스 호출(최대 64KB)은 두 청구 가능 메시지를 발생시킵니다. 하나는 요청에 대한 청구 가능 메시지이며 다른 하나는 응답에 대한 청구 가능 메시지입니다(응답이 \<= 64KB이라고 가정). 하나는 요청을 위한 청구 가능 메시지이고 다른 하나는 응답을 위한 청구 가능 메시지입니다.(또한 응답이 <=&64;KB임을 가정) 큐를 사용하여 달라져서 클라이언트와 서비스 간을 조정합니다. 후자의 경우 동일한 요청-회신 패턴은 요청을 큐로 보내야 하며 큐에서 서비스로 큐 제거/전달한 다음, 응답을 다른 큐로 보낸 다음, 큐에서 클라이언트로 큐 제거/전달합니다. 동일한(\<=64KB) 크기의 가정을 사용하여 조정된 큐 패턴은 네 개의 청구 가능한 메시지를 발생시키며 이는 릴레이를 사용하여 동일한 패턴을 구현하는 요금 청구의 두 배입니다. 물론 지속성 및 부하 평준화 등 패턴을 달성하기 위해 큐를 사용할 수 있다는 이점이 있습니다. 이러한 이점은 추가 비용을 정당화할 수 있습니다.

netTCPRelay WCF 바인딩을 사용하여 열린 릴레이는 개별 메시지가 아니라 시스템을 통과하는 데이터의 스트림으로서 메시지를 처리합니다. 즉, 발신자와 리스너만이 이 바인딩을 사용하여 보내거나 받은 개별 메시지의 프레임에 대한 가시성이 있습니다. 따라서 netTCPRelay 바인딩을 사용하는 릴레이의 경우 청구 가능한 메시지를 계산하기 위해 모든 데이터를 스트림으로 처리합니다. 이 경우에 Service Bus는 5분 단위로 각 개별 릴레이를 통해 전송 또는 수신되는 총 데이터 양을 계산하고 해당 기간 동안 릴레이에 대한 청구 가능한 메시지 수를 결정하기 위해 해당 합계를 64KB로 나눕니다.

## <a name="quotas"></a>할당량
| 할당량 이름 | 범위 | 유형 | 초과 시 동작 | 값 |
| --- | --- | --- | --- | --- |
| 릴레이의 동시 수신기 |엔터티 |정적 |추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |25 |
| 동시 릴레이 수신기 |시스템 수준 |정적 |추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |2,&000; |
| 서비스 네임스페이스의 모든 릴레이 끝점당 동시 릴레이 연결 |시스템 수준 |정적 |- |5,&000; |
| 서비스 네임스페이스당 릴레이 끝점 |시스템 수준 |정적 |- |10000 |
| [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) and [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) 릴레이의 메시지 크기 |시스템 수준 |정적 |이러한 할당량을 초과하는 들어오는 메시지가 거부되며 호출 코드에서 예외를 수신합니다. |64KB |
| [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) 및 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 릴레이의 메시지 크기 |시스템 수준 |정적 |- |무제한 |

### <a name="does-relay-have-any-usage-quotas"></a>릴레이에는 사용 할당량이 있습니까?
기본적으로 모든 클라우드 서비스의 경우 Microsoft는 모든 고객의 구독에 대해 계산되는 월별 사용 할당량을 집계합니다. 이러한 제한 보다 사용자의 필요가 많을 수도 있다는 것을 이해하기 때문에 사용자의 요구를 이해하고 이러한 제한을 적절하게 조정할 수 있도록 언제든지 서비스에 문의하세요. 서비스 버스의 경우 집계 사용 할당량은 다음과 같습니다.

* 50억 개의 메시지
* 2백만 릴레이 시간

지정된 달에 사용 할당량을 초과하는 고객의 계정을 사용하지 않도록 설정하는 권한을 보유하지만 메일 알림을 제공하며 조치를 취하기 전에 고객에게 연락을 여러 번 시도합니다. 이러한 할당량을 초과하는 고객은 할당량을 초과하는 요금을 지불해야 합니다.

### <a name="naming-restrictions"></a>명명 제한
릴레이 네임스페이스 이름의 길이는 6-50자여야 합니다.

## <a name="subscription-and-namespace-management"></a>구독 및 네임스페이스 관리
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>다른 Azure 구독으로 네임스페이스를 마이그레이션하려면 어떻게 해야 하나요?
PowerShell 명령([여기](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription) 문서에 위치)을 사용하여 Azure 구독 간에 네임스페이스를 이동할 수 있습니다. 작업을 실행하기 위해 네임스페이스가 활성화되어야 합니다. 또한 명령을 실행하는 사용자는 원본 및 대상 구독에 대한 관리자여야 합니다.

## <a name="troubleshooting"></a>문제 해결
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Azure 릴레이 API 및 해당 제안된 작업에 의해 생성된 일부 예외는 무엇인가요?
[릴레이 예외][Relay exceptions] 문서에서는 몇 가지 예외와 권장 조치를 설명합니다.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>공유 액세스 서명이란 무엇이고 어떤 언어가 서명 생성을 지원하나요?
공유 액세스 서명은 SHA – 256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. 노드, PHP, Java 및 C\#에서 자신의 서명을 생성하는 방법에 대한 내용은 [공유 액세스 서명][Shared Access Signatures] 문서를 참조하세요.

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas-overview.md

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>릴레이 끝점을 허용 목록에 추가할 수 있나요?
예. 릴레이 클라이언트는 FQDN(정규화된 도메인 이름)을 사용하여 릴레이 서비스에 연결합니다. 이렇게 하면 고객이 DNS 허용 목록을 지원하는 방화벽에 `*.servicebus.windows.net`에 대한 항목을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [네임스페이스 만들기](relay-create-namespace-portal.md)
* [.NET 시작](relay-hybrid-connections-dotnet-get-started.md)
* [노드 시작](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Jan17_HO4-->


