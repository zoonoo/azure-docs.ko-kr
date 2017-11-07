---
title: "지속성 함수의 성능 및 크기 조정 - Azure"
description: "Azure Functions의 지속성 함수 확장을 소개합니다."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>지속성 함수의 성능 및 크기 조정(Azure Functions)

성능 및 확장성을 최적화하려면 [지속성 함수](durable-functions-overview.md)의 고유한 크기 조정 특징을 이해하는 것이 중요합니다.

크기 조정 동작을 이해하려면 지속성 함수에서 사용하는 기본 Azure Storage 공급자의 세부 정보 중 일부를 이해해야 합니다.

## <a name="history-table"></a>기록 테이블

기록 테이블은 모든 오케스트레이션 인스턴스에 대한 기록 이벤트가 포함된 Azure Storage 테이블입니다. 인스턴스가 실행되면 새 행이 이 테이블에 추가됩니다. 이 테이블의 파티션 키는 오케스트레이션의 인스턴스 ID에서 파생됩니다. 이러한 값은 대부분의 경우 임의적이며, 이는 Azure Storage에서 내부 파티션을 최적으로 배포할 수 있도록 합니다.

## <a name="internal-queue-triggers"></a>내부 큐 트리거

오케스트레이터 함수 및 작업 함수는 모두 함수 앱의 기본 저장소 계정에 있는 내부 큐에서 트리거됩니다. 지속성 함수에는 **제어 큐** 및 **작업 항목 큐**의 두 가지 유형의 큐가 있습니다.

### <a name="the-work-item-queue"></a>작업 항목 큐

지속성 함수에는 작업 허브마다 하나의 작업 항목 큐가 있습니다. 이는 기본 큐이며 Azure Functions의 다른 모든 `queueTrigger` 큐와 비슷하게 동작합니다. 이 큐는 상태 비저장 *작업 함수*를 트리거하는 데 사용됩니다. 지속성 함수 응용 프로그램이 여러 VM으로 확장하는 경우 이러한 VM은 모두 작업 항목 큐에서 작업을 가져오려고 경쟁합니다.

### <a name="control-queues"></a>제어 큐

*제어 큐*는 간단한 작업 항목 큐보다 더 복잡합니다. 상태 저장 오케스트레이터 함수를 트리거하는 데 사용됩니다. 오케스트레이터 함수 인스턴스는 상태 저장 단일 항목이므로 경쟁하는 소비자 모델을 사용하여 VM 간에 부하를 분산하는 것이 불가능합니다. 대신 오케스트레이터 메시지는 여러 제어 큐에서 부하 분산됩니다. 다음 섹션에서는 이에 대해 자세히 설명합니다.

제어 큐에는 다양한 오케스트레이션 수명 주기 메시지 유형이 포함됩니다. 예를 들어 [오케스트레이터 제어 메시지](durable-functions-instance-management.md), 작업 함수 *응답* 메시지 및 타이머 메시지가 있습니다.

## <a name="orchestrator-scale-out"></a>오케스트레이터 확장

작업 함수는 상태 비저장이며, VM을 추가하여 자동으로 확장됩니다. 반면 오케스트레이터 함수는 하나 이상의 제어 큐에서 *분할됩니다*. 제어 큐의 수는 고정되며 부하를 만들기 시작한 후에는 변경할 수 없습니다.

여러 함수 호스트 인스턴스(일반적으로 서로 다른 VM에 있음)로 확장하는 경우 각 인스턴스는 제어 큐 중 하나에 대한 잠금을 얻습니다. 이 잠금은 오케스트레이션 인스턴스가 한 번에 하나의 VM에서만 실행되도록 합니다. 즉 작업 허브에 세 개의 제어 큐가 구성되어 있으면 오케스트레이션 인스턴스를 세 개의 VM에서 부하를 분산할 수 있습니다. 작업 함수 실행을 위한 용량을 늘리기 위해 추가 VM을 추가할 수 있습니다.  그러나 추가 리소스는 오케스트레이터 함수를 실행하는 데 사용되지 않습니다.

다음 다이어그램에서는 Azure Functions 호스트에서 확장된 환경의 저장소 엔터티와 상호 작용하는 방법을 보여 줍니다.

![크기 조정 다이어그램](media/durable-functions-perf-and-scale/scale-diagram.png)

여기서 볼 수 있듯이 모든 VM은 작업 항목 큐에서 메시지를 위해 경쟁할 수 있습니다. 그러나 세 개의 VM만 제어 큐에서 메시지를 얻을 수 있으며 각 VM에서 단일 제어 큐를 잠급니다.

오케스트레이션 인스턴스는 오케스트레이션의 인스턴스 ID에 대해 내부 해시 함수를 실행하여 제어 큐 인스턴스 간에 분산됩니다. 기본적으로 인스턴스 ID는 사용 가능한 모든 제어 큐에서 인스턴스의 부하를 분산하도록 임의로 자동 생성됩니다. 현재 지원되는 제어 큐 파티션의 기본 수는 **4**입니다.

> [!NOTE]
> 현재 Azure Functions에서는 파티션 수를 구성할 수 없습니다. [이 구성 옵션을 지원하기 위한 작업이 추적되고 있습니다](https://github.com/Azure/azure-functions-durable-extension/issues/73).

일반적으로 오케스트레이터 함수는 간단하게 설계되므로 많은 컴퓨팅 기능이 필요하지 않습니다. 이러한 이유로 많은 처리량을 달성하기 위해 많은 수의 제어 큐 파티션을 만들 필요가 없습니다. 대신 많은 작업 대부분은 무한정 확장될 수 있는 상태 비저장 작업 함수에서 수행됩니다.

## <a name="auto-scale"></a>자동 크기 조정

소비 계획에서 실행 중인 모든 Azure Functions와 마찬가지로 지속성 함수는 [Azure Functions 크기 조정 컨트롤러](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling)를 통해 자동 크기 조정을 지원합니다. 크기 조정 컨트롤러는 작업 항목 큐와 각 제어 큐의 길이를 모니터링하고, 이에 따라 VM 리소스를 추가하거나 제거합니다. 제어 큐 길이가 시간이 지남에 따라 증가하는 경우 제어 큐 파티션 수에 도달할 때까지 크기 조정 컨트롤러에서 인스턴스를 계속 추가합니다. 작업 항목 큐 길이가 시간이 지남에 따라 증가하는 경우 제어 큐 파티션 수에 관계 없이 부하와 일치할 때까지 크기 조정 컨트롤러에서 VM 리소스를 계속 추가합니다.

## <a name="thread-usage"></a>스레드 사용

오케스트레이터 함수는 단일 스레드에서 실행됩니다. 이는 오케스트레이터 함수 실행이 결정적인지 확인하는 데 필요합니다. 이를 고려하여 오케스트레이터 함수 스레드가 I/O(다양한 이유로 금지되어 있음), 차단 또는 회전 작업과 같은 작업으로 불필요하게 바쁘게 작동하지 않도록 유지하는 것이 중요합니다. I/O, 차단 또는 다중 스레드가 필요한 작업은 작업 함수로 이동해야 합니다.

작업 함수에는 일반 큐 트리거 함수와 동일한 모든 동작이 있습니다. 즉 I/O를 안전하게 수행하고, CPU 집약적 작업을 실행하며, 여러 스레드를 사용할 수 있습니다. 작업 트리거는 상태 비저장이므로 VM 수에 관계 없이 자유롭게 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 함수 확장 및 샘플 설치](durable-functions-install.md)
