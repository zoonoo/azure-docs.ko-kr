---
title: Azure 함수 개요
description: Azure Functions에서 강력한 서버리스 앱을 빌드하는 데 도움이 되는 방법을 알아봅니다.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97862394"
---
# <a name="introduction-to-azure-functions"></a>Azure Functions 소개

Azure Functions는 더 적은 코드를 쓰고, 인프라를 적게 유지하고, 비용을 절감할 수 있도록 하는 서버리스 솔루션입니다. 애플리케이션을 계속 실행하는 데 필요한 모든 최신 리소스를 클라우드 인프라에서 제공하므로 서버 배포 및 유지 관리에 대해 걱정할 필요가 없습니다.

가장 중요한 코드 조각에 초점을 맞춘 Azure Functions가 나머지를 처리합니다.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

일련의 중요한 이벤트에 대응하는 시스템을 빌드하는 경우가 많습니다. 웹 API를 빌드하든지, 데이터베이스 변경에 응답하든지, IoT 데이터 스트림을 처리하든지 또는 메시지 큐를 관리하든지 간에 모든 애플리케이션에는 이러한 이벤트가 발생할 때 일부 코드를 실행할 수 있는 방법이 필요합니다.

이러한 요구 사항을 충족하기 위해 Azure Functions는 두 가지 중요한 방법으로 "주문형 컴퓨팅"을 제공합니다.

첫째, Azure Functions를 사용하여 시스템 논리를 즉시 사용할 수 있는 코드 블록으로 구현할 수 있습니다. 이러한 코드 블록을 "함수"라고 합니다. 중요한 이벤트에 응답해야 할 때마다 다양한 함수가 실행될 수 있습니다.

둘째, 요청이 증가함에 따라 Azure Functions는 필요한 만큼의 리소스 및 함수 인스턴스를 사용하여 수요를 충족하지만 필요한 경우에만 충족합니다. 요청이 있으면 추가 리소스 및 애플리케이션 인스턴스가 자동으로 반환됩니다.

모든 컴퓨팅 리소스는 어디서 제공되나요? Azure Functions는 애플리케이션 수요를 충족하는 데 [필요한 만큼의 컴퓨팅 리소스를 제공](./functions-scale.md)합니다.

컴퓨팅 리소스를 주문형 방식으로 제공하는 것은 Azure Functions에서 [서버리스 컴퓨팅](https://azure.microsoft.com/solutions/serverless/)의 핵심입니다.

## <a name="scenarios"></a>시나리오

대부분의 경우 함수는 [여러 클라우드 서비스와 통합](./functions-triggers-bindings.md)되어 다양한 기능을 제공하는 구현을 제공합니다.

다음은 Azure Functions에 대한 일반적인 시나리오 집합이지만 _결코 완전한 것은 아닙니다_.

| 다음을 원하는 경우... | 구현 방법... |
| --- | --- |
| **Web API 빌드** | [HTTP 트리거](./functions-bindings-http-webhook.md)를 사용하여 웹 애플리케이션에 대한 엔드포인트를 구현합니다. |
| **파일 업로드 처리** | [Blob 스토리지](./functions-bindings-storage-blob.md)에서 파일이 업로드되거나 변경될 때 코드를 실행합니다. |
| **서버리스 워크플로 빌드** | [지속성 함수](./durable/durable-functions-overview.md)를 사용하여 일련의 함수를 모두 연결합니다. |
| **데이터베이스 변경에 응답** | [Cosmos DB](./functions-bindings-cosmosdb-v2.md)에서 문서를 만들거나 업데이트할 때 사용자 지정 논리를 실행합니다. |
| **예약된 작업 실행** | 코드를 [설정 시간](./functions-bindings-timer.md)에 실행합니다. |
| **안정적인 메시지 큐 시스템 만들기** | [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md) 또는 [Event Hubs](./functions-bindings-event-hubs.md)를 사용하여 메시지 큐를 처리합니다. |
| **IoT 데이터 스트림 분석** | [IoT 디바이스](./functions-bindings-event-iot.md)에서 데이터를 수집하고 처리합니다. |
| **실시간 데이터 처리** | [Functions 및 SignalR](./functions-bindings-signalr-service.md)을 사용하여 현재 데이터에 응답합니다. |

함수를 빌드할 때 사용할 수 있는 옵션과 리소스는 다음과 같습니다.

- **기본 설정 언어 사용**: [C#, Java, JavaScript, PowerShell 또는 Python](./supported-languages.md)에서 함수를 작성하거나 [사용자 지정 처리기](./functions-custom-handlers.md)를 통해 거의 모든 다른 언어를 사용합니다.

- **배포 자동화**: 도구 기반 방법에서 외부 파이프라인을 사용하는 방법에 이르기까지 [많은 배포 옵션](./functions-deployment-technologies.md)을 사용할 수 있습니다.

- **함수 문제 해결**: [모니터링 도구](./functions-monitoring.md) 및 [테스트 전략](./functions-test-a-function.md)을 사용하여 앱에 대한 인사이트를 얻습니다.

- **유연한 가격 책정 옵션**: [소비](./pricing.md) 요금제를 사용하면 함수가 실행되는 동안에만 비용을 지불할 수 있는 반면, [프리미엄](./pricing.md) 및 [App Service](./pricing.md) 요금제는 특별한 요구 사항에 대한 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단원, 샘플 및 대화형 자습서 시작](./functions-get-started.md)
