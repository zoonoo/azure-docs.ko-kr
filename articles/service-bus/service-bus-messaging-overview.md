<properties
	pageTitle="서비스 버스 메시징 개요 | Microsoft Azure"
	description="서비스 버스 메시징: 클라우드에서 유연한 데이터 배달"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sethm"/>


# 서비스 버스 메시징: 클라우드에서 유연한 데이터 배달

Microsoft Azure 서비스 버스 메시징은 안정적인 정보 배달 서비스로, 보다 손쉬운 통신을 위해 제공됩니다. 둘 이상의 당사자가 정보를 교환하려면 통신 메커니즘이 필요합니다. 서비스 버스 메시징은 조정된 또는 타사 통신 메커니즘으로, 실제 세상에서의 우편 서비스와 비슷합니다. 우편 서비스를 사용하면 원하는 배달 보장 수준을 선택하여 다양한 종류의 서신과 소포를 전 세계 어디로나 매우 쉽게 보낼 수 있습니다.

이처럼 편지를 배달하는 우편 서비스와 마찬가지로 Azure 서비스 버스 메시징은 보낸 사람과 받는 사람이 모두 정보를 유동적으로 배달할 수 있도록 합니다. 메시징 서비스를 사용하면 두 당사자가 동시에 온라인 상태가 아니거나 정확히 동일한 시간에 대화가 가능하지 않더라도 정보를 배달할 수 있습니다. 위에서 설명한 것처럼 이러한 메시징 방식은 편지를 보내는 것과 비슷한 반면 조정되지 않은 통신은 기존의 전화를 거는 것(또는 조정된 메시징과 훨씬 비슷한, 통화 대기 및 발신자 번호 기능이 있기 전의 예전 전화 걸기 방식)과 비슷합니다.

메시지 보낸 사람은 트랜잭션, 중복 항목 검색, 시간 기반 만료, 일괄 처리 등의 여러 배달 특성을 필수 옵션으로 지정할 수도 있습니다. 이러한 패턴 역시 우편 서비스의 반복 배달, 서명 요청, 주소 변경, 회수 등과 비슷합니다.

서비스 버스 메시징에는 큐와 토픽이라는 두 가지 기능이 있습니다. 이 두 메시징 엔터티는 위에서 설명한 모든 개념은 물론 다양한 추가 기능도 지원합니다. 두 엔터티의 가장 큰 차이점은, 토픽의 경우 여러 받는 사람에게 보내는 기능을 비롯하여 복잡한 콘텐츠 기반 라우팅 및 배달 논리에 사용할 수 있는 게시/구독 기능을 지원한다는 것입니다.

## 다음 단계

서비스 버스 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Azure 서비스 버스 아키텍처 개요](fundamentals-service-bus-hybrid-solutions.md)

- [서비스 버스 큐를 사용하는 방법](service-bus-dotnet-how-to-use-queues.md)

- [서비스 버스 토픽을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=August15_HO7-->