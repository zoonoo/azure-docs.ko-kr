---
title: Azure Service Bus의 AMQP 1.0 개요
description: Azure Service Bus가 개방형 표준 프로토콜인 AMQP(고급 메시지 큐 프로토콜)를 지원하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/08/2021
ms.openlocfilehash: 006511789bfa93f8e7d578ed21a73a2563fb4c6b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304422"
---
# <a name="advanced-message-queueing-protocol-amqp-10-support-in-service-bus"></a>Service Bus에서 AMQP(고급 메시지 큐 프로토콜) 1.0 지원
Azure Service Bus 클라우드 서비스는 기본 통신 수단으로 [AMQP 1.0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)을 사용합니다. Microsoft는 지난 10년 동안 [OASIS AMQP 기술 위원회](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)에서 새로운 확장명을 개발하면서 경쟁 메시징 브로커의 고객과 공급업체를 비롯해 업계 전반의 파트너와 협력해 왔습니다. AMQP 1.0은 ISO 및 IEC 표준([ISO 19464:20149](https://www.iso.org/standard/64955.html))입니다. 

AMQP로 공급업체 중립적이고, 구현 중립적이며, 개방된 표준 프로토콜을 사용하는 플랫폼 간 하이브리드 애플리케이션을 만들 수 있습니다. 다른 언어 및 프레임워크로 빌드된 구성 요소를 사용하며 다른 운영 체제에서 실행되는 애플리케이션을 생성할 수 있습니다. 이러한 구성 요소는 모두 Service Bus에 연결할 수 있으며 구조화된 비즈니스 메시지를 효율적이고 완벽하며 원활하게 교환할 수 있습니다.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>소개: AMQP 1.0이란 무엇이며 왜 중요한가요?
일반적으로 메시지 지향 미들웨어 제품은 클라이언트 애플리케이션과 브로커 간에 통신하는 데 소유 프로토콜을 사용했습니다. 일단 특정 공급업체의 메시징 브로커를 선택했으면 해당 공급업체의 라이브러리를 사용하여 클라이언트 애플리케이션을 해당 브로커에 연결해야 합니다. 이런 경우 애플리케이션을 다른 제품에 이식하려면 연결되어 있는 모든 애플리케이션에서 코드를 변경해야 하므로 해당 공급업체에 많이 의존하게 됩니다. Java 커뮤니티에서 JMS(Java Message Service) 및 Spring Framework의 추상화와 같은 특정 언어의 API 표준은 문제를 약간은 해결하지만, 기능 범위가 좁은 한편 다른 언어를 사용하는 개발자를 배척합니다.

더구나 다른 공급업체의 메시징 브로커를 연결하는 것은 까다로우며, 일반적으로 한 시스템에서 다른 시스템으로 메시지를 이동하고 자신의 재산적 가치를 가지는 메시지 형식을 변환하려면 애플리케이션 수준의 브리징이 필요합니다. 이는 예를 들어 새 통합 인터페이스를 이전의 이종 시스템에 제공해야 하는 경우 또는 병합 후 IT 시스템을 통합하는 경우의 일반적인 요구 사항입니다. AMQP를 사용하면 가령 [Apache Qpid Dispatch Router](https://qpid.apache.org/components/dispatch-router/index.html) 같은 라우터나 [RabbitMQ](service-bus-integrate-with-rabbitmq.md) 같은 브로커-네이티브 “shovels”를 사용해 연결 브로커와 직접 상호 연결을 할 수 있습니다.

소프트웨어 산업은 빠르게 변화하는 비즈니스입니다. 새 프로그래밍 언어 및 애플리케이션 프레임워크는 종종 놀랄만한 속도로 도입됩니다. 마찬가지로 IT 시스템의 요구 사항은 점점 진화하고 개발자는 최신 플랫폼 기능을 이용하고 싶어합니다. 하지만 선택한 메시징 공급업체가 이러한 플랫폼을 지원하지 않기도 합니다. 메시징 프로토콜에 재산적 가치가 있는 경우 이러한 새 플랫폼의 라이브러리를 다른 공급업체에서 사용할 수 없습니다. 따라서 메시징 제품을 계속 사용할 수 있도록 구축 게이트웨이 또는 브리지와 같은 방식을 사용해야 합니다.

AMQP(Advanced Message Queuing Protocol) 1.0은 이러한 문제로 인해 개발되었습니다. 대부분의 금융 서비스 기업처럼 메시지 지향 미들웨어를 많이 사용하는 JP Morgan Chase에서 시작되었습니다. 다른 언어, 프레임워크 및 운영 체제로 빌드된 구성 요소를 사용하며 다양한 공급업체의 최고 구성 요소를 모두 사용하는 메시지 기반 애플리케이션을 만들 수 있는 개방형 표준 메시징 프로토콜을 만드는 것이 목표였습니다.

## <a name="amqp-10-technical-features"></a>AMQP 1.0의 기술적 기능
AMQP 1.0은 효율성과 안정성이 뛰어난 유선 수준 메시징 프로토콜로, 여러 플랫폼 간에 상호 운용되는 강력한 메시징 애플리케이션을 만들 수 있습니다. 보안성, 안정성 및 효율성이 뛰어난 방식으로 두 파티 간에 메시지를 전송하는 메커니즘을 정의하는 것이 목표입니다. 메시지는 서로 다른 유형의 발신자와 수신자가 구조화된 비즈니스 메시지를 완벽하게 교환할 수 있는 이식 가능 데이터 표현을 사용하여 자체적으로 인코딩됩니다. 다음은 가장 중요한 기능에 대한 요약입니다.

* **효율성**: AMQP 1.0은 이 프로토콜을 통해 전송되는 프로토콜 지침 및 비즈니스 메시지에 이진 인코딩을 사용하는 연결 지향 프로토콜입니다. 이 프로토콜은 정교한 흐름 제어 구성표에 통합되어 있어 네트워크 및 연결된 구성 요소를 최대로 활용할 수 있습니다. 또한 이 프로토콜은 효율성, 유연성 및 상호 운용성 간의 균형을 유지할 수 있도록 설계되었습니다.
* **안정성**: AMQP 1.0 프로토콜을 사용하면 자체 유도(fire-and-forget) 방식에서부터 안정적으로 정확히 한 번만 승인해서 배달하는 방법까지 다양한 안정성을 보장하여 메시지를 교환할 수 있습니다.
* **유연성**: AMQP 1.0은 여러 토폴로지를 지원할 수 있는 유연한 프로토콜입니다. 클라이언트 간의 통신, 클라이언트에서 브로커로의 통신 및 브로커 간의 통신에 동일한 프로토콜을 사용할 수 있습니다.
* **브로커 모델 독립적**: AMQP 1.0 사양에는 브로커에서 사용하는 메시징 모델에 대한 요구 사항이 없습니다. 따라서 기존의 메시징 브로커에 AMQP 1.0 기능을 쉽게 추가할 수 있습니다.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 표준
AMQP 1.0은 ISO 및 IEC에 의해 ISO/IEC 19464:2014로 승인된 국제 표준합니다.

AMQP 1.0은 2008년 이래로 기술 공급업체와 최종 사용자 업체를 모두 포함하여 20여 개가 넘는 핵심 회사에 의해 개발되었습니다. 이 개발 기간 동안 사용자 업체에서는 해당되는 실제 비즈니스 요구 사항을 제공했으며 기술 공급업체에서는 이러한 요구 사항에 부합되는 프로토콜을 개발했습니다. 이 과정 내내 공급업체는 워크샵 참여를 통해 여러 다른 공급업체와 더불어 구현 제품 간의 상호 운용성을 확인했습니다.

이 개발 작업은 2011년 10월에 OASIS(Organization for the Advancement of Structured Information Standards)의 기술 위원회로 이전되었으며, 2012년 10월에 OASIS AMQP 1.0 표준이 출시되었습니다. 이 표준 개발 기간 동안 다음과 같은 회사가 기술 위원회에 참여했습니다.

* **기술 공급업체**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **사용자 업체**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

[OASIS AMQP 기술 위원회](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)의 현 위원장들은 Red Hat과 Microsoft를 대표합니다.

개방형 표준의 몇 가지 주요 이점은 다음과 같습니다.

* 공급업체 교착 상태 확률이 낮아짐
* 상호 운용성
* 다양한 라이브러리 및 도구 제공
* 구식화 방지
* 숙련된 직원에 대한 가용성
* 위험성 저하 및 관리 가능한 위험

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 및 Service Bus
Azure Service Bus에 AMQP 1.0이 지원되므로 효율적인 이진 프로토콜을 사용하여 다양한 플랫폼에서 Service Bus 큐를 사용하고 조정된 메시징 기능을 게시/구독할 수 있습니다. 뿐만 아니라 여러 언어, 프레임워크 및 운영 체제가 혼합되어 사용된 구성 요소로 이루어진 애플리케이션을 만들 수 있습니다.

다음 그림에서는 표준 JMS(Java Message Service) API를 사용하여 작성되었으며 Linux에서 실행되는 Java 클라이언트와 Windows에서 실행되는 .NET 클라이언트가 AMQP 1.0이 사용된 Service Bus를 통해 메시지를 교환하는 배포 예를 보여 줍니다.

![하나의 Service Bus가 두 개의 Linux 환경 및 두 개의 Windows 환경과 메시지를 교환하는 모습을 보여 주는 다이어그램.][0]

**그림 1: Service Bus와 AMQP 1.0을 사용하는 플랫폼 간 메시징을 보여 주는 예제 배포 시나리오**

모든 지원되는 Service Bus 클라이언트 라이브러리는 AMQP 1.0을 이용하는 Azure SDK를 통해 사용할 수 있습니다.

- [.NET용 Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java용 Azure Service Bus 라이브러리](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2.0용 Azure Service Bus 공급자](how-to-use-java-message-service-20.md)
- [JavaScript 및 TypeScript용 Azure Service Bus 모듈](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python용 Azure Service Bus 라이브러리](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

또한 모든 AMQP 1.0 규격 프로토콜 스택에서 Service Bus를 사용할 수 있습니다.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>요약
* AMQP 1.0은 여러 플랫폼 간에 상호 운용되는 하이브리드 애플리케이션을 만들 수 있는 안정적인 개방형 메시징 프로토콜입니다. AMQP 1.0은 OASIS 표준입니다.

## <a name="next-steps"></a>다음 단계
자세히 알아볼 준비가 되셨습니까? 다음 링크를 방문하세요.

* [AMQP를 사용하여 .NET에서 Service Bus 사용]
* [AMQP를 사용하여 Java에서 Service Bus 사용]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[AMQP를 사용하여 .NET에서 Service Bus 사용]: service-bus-amqp-dotnet.md
[AMQP를 사용하여 Java에서 Service Bus 사용]: ./service-bus-java-how-to-use-jms-api-amqp.md

