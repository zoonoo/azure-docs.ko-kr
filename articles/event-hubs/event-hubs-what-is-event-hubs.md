---
title: Azure 이벤트 허브란? | Microsoft Docs
description: Azure 이벤트 허브의 개요 및 설명
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sethm

---
# Azure 이벤트 허브 정의
Azure 이벤트 허브는 초당 수백만 개의 이벤트를 수집할 수 있는 확장성이 뛰어난 데이터 수집 서비스이므로 연결된 장치와 응용 프로그램이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. 이벤트 허브는 이벤트 파이프라인에 대한 "현관"의 역할을 하고 데이터가 이벤트 허브에 수집되면 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환 및 저장될 수 있습니다. 이벤트 허브는 이러한 이벤트를 소비하는 데에서 이벤트 스트림의 프로덕션을 분리하므로 이벤트 소비자가 자신의 개인 일정에 이벤트를 액세스할 수 있습니다. 자세한 내용 및 기술적 세부 사항은 [이벤트 허브 개요](event-hubs-overview.md)를 참조하세요.

## 이벤트 허브 기능
이벤트 허브는 짧은 대기 시간 및 높은 안정성으로 대규모의 이벤트 및 원격 분석 처리를 제공하는 이벤트 처리 서비스입니다. 이 서비스는 다음과 같은 경우 특히 유용합니다.

* 응용 프로그램 계측
* 사용자 환경 또는 워크플로 처리
* IoT(사물 인터넷) 시나리오

몇 가지 다른 키 이벤트 허브 기능은 모바일 앱에서 동작 추적, 웹 팜에서 트래픽 정보, 콘솔 게임에서 게임 내 이벤트 캡처 또는 산업 컴퓨터 또는 연결된 차량에서 수집된 원격 분석 데이터를 포함합니다.

## 다음 단계
이벤트 허브에 대한 자세한 내용은 다음 항목을 참조하세요.

* [이벤트 허브 개요](event-hubs-overview.md)
* [이벤트 허브 프로그래밍 가이드](event-hubs-programming-guide.md)
* [이벤트 허브 가용성 및 지원 FAQ](event-hubs-availability-and-support-faq.md)
* [이벤트 허브 자습서][이벤트 허브 자습서]로 시작합니다.
* [이벤트 허브를 사용하는 전체 샘플 응용 프로그램][이벤트 허브를 사용하는 전체 샘플 응용 프로그램]

[이벤트 허브 자습서]: event-hubs-csharp-ephcs-getstarted.md
[이벤트 허브를 사용하는 전체 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0907_2016-->