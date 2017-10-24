---
title: "지속성 함수의 작업 허브 - Azure"
description: "Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 작업 허브 구성 방법을 알아봅니다."
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
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>지속성 함수의 작업 허브(Azure Functions)

[지속성 함수](durable-functions-overview.md)의 *작업 허브*는 단일 Azure 저장소 계정의 컨텍스트 내에서 수행되는 오케스트레이션 및 작업에 대한 논리적 컨테이너입니다. 여러 함수 및 함수 앱이 동일한 작업 허브에 존재할 수 있으며, 작업 허브는 종종 응용 프로그램 컨테이너 역할을 합니다.

작업 허브는 명시적으로 만들 필요가 없습니다. *host.json* 파일에 선언된 이름을 사용하여 런타임에서 자동으로 초기화됩니다. 각각의 작업 허브에는 단일 저장소 계정 내에 자체의 저장소 큐, 테이블 및 Blob 집합이 있습니다. 지정된 작업 허브에서 실행되는 모든 함수 앱은 동일한 저장소 리소스를 공유합니다. 오케스트레이터 및 작업 함수는 동일한 작업 허브에 속할 때만 상호 작용할 수 있습니다.

## <a name="configuring-a-task-hub-in-hostjson"></a>host.json에서 작업 허브 구성

작업 허브 이름은 함수 앱의 *host.json* 파일에서 구성할 수 있습니다.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정되지 않은 경우 함수 앱의 기본 작업 허브 이름은 **DurableFunctionsHub**입니다.

> [!NOTE]
> 저장소 계정을 공유하는 함수 앱이 여러 개 있는 경우 각 함수 앱마다 다른 작업 허브 이름을 구성하는 것이 좋습니다. 이렇게 하면 각 함수 앱이 서로 제대로 격리됩니다.

## <a name="azure-storage-resources"></a>Azure 저장소 리소스

작업 허브는 다음과 같은 몇 가지 Azure Storage 리소스로 구성됩니다.

* 하나 이상의 제어 큐
* 하나의 작업 항목 큐
* 하나의 기록 테이블
* 하나 이상의 저장소 Blob을 포함하는 하나의 저장소 컨테이너

오케스트레이터 또는 작업 함수가 실행되거나 실행되도록 예약된 경우 이러한 모든 리소스는 기본 Azure Storage 계정에 자동으로 만들어집니다. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)

