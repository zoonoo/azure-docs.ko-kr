---
title: Azure SignalR이란? | Microsoft Docs
description: Azure SignalR Service에 대해 간략히 설명합니다.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: a159833936ec4762213f063e235fa4f9237af95b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951103"
---
# <a name="what-is-azure-signalr-service"></a>Azure SignalR Service란?

Azure SignalR Service는 HTTP를 통해 응용 프로그램에 실시간 웹 기능을 추가하는 프로세스를 간소화합니다. 이 실시간 기능을 사용하면 서비스가 연결된 클라이언트(예: 단일 페이지 웹 또는 모바일 응용 프로그램)에 콘텐츠 업데이트를 푸시할 수 있습니다. 결과적으로 클라이언트가 서버를 폴링하거나 업데이트에 대한 새 HTTP 요청을 제출하지 않고도 업데이트됩니다.

이 문서에서는 Azure SignalR Service에 대한 개요를 제공합니다.

## <a name="what-is-azure-signalr-service-used-for"></a>Azure SignalR Service의 용도는? 

실시간 콘텐츠 업데이트가 필요한 응용 프로그램 종류는 많습니다. Azure SignalR Service를 사용하기에 적합한 예는 다음과 같습니다.

* 서버에서 자주 업데이트해야 하는 앱. 예를 들어 게임, 투표, 경매, 지도, GPS 앱 등이 있습니다.
* 대시보드 및 모니터링 앱. 예를 들어 회사 대시보드, 즉석 판매 업데이트 등이 있습니다.
* 공동 작업 앱. 예를 들어 화이트보드 앱 및 팀 회의 소프트웨어가 있습니다.
* 알림이 필요한 앱. 소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱에서 알림을 사용합니다.

SignalR은 실시간 웹 응용 프로그램을 빌드하는 데 사용되는 다양한 기술에 대한 추상화를 제공합니다. [Websocket](https://wikipedia.org/wiki/WebSocket)이 최적의 전송이지만, 다른 옵션을 사용할 수 없는 경우 [SSE(Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) 및 Long Polling과 같은 다른 기술이 사용됩니다. SignalR은 서버와 클라이언트에서 지원되는 기능에 따라 적절한 전송을 자동으로 감지하고 초기화합니다.

또한 SignalR은 서버가 모든 연결 또는 특정 사용자에 속하거나 임의의 그룹에 배치된 연결의 하위 집합에 메시지를 보낼 수 있게 하는 실시간 응용 프로그램에 대한 프로그래밍 모델을 제공합니다.

## <a name="how-to-use-azure-signalr-service"></a>Azure SignalR Service 사용 방법

현재 Azure SignalR Service를 사용하는 방법에는 다음 세 가지가 있습니다.

- **[ASP.NET Core SignalR 앱 크기 조정](signalr-overview-scale-aspnet-core.md)** - Azure SignalR Service를 ASP.NET Core SignalR 응용 프로그램과 통합하여 수십만 개의 연결까지 확장합니다.
- **[서버리스 실시간 앱 빌드](signalr-overview-azure-functions.md)** - Azure SignalR Service와 Azure Functions의 통합을 사용하여 JavaScript, C# 및 Java와 같은 언어로 서버리스 실시간 응용 프로그램을 빌드합니다.
- **[REST API를 통해 서버에서 클라이언트로 메시지 보내기](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - Azure SignalR Service는 REST API를 제공하여 응용 프로그램이 모든 REST 지원 프로그래밍 언어로 SignalR Service와 연결된 클라이언트에 메시지를 게시할 수 있도록 합니다.

