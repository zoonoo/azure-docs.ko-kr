---
title: "Azure Event Hubs API 개요 | Microsoft Docs"
description: "사용할 수 있는 Azure Event Hubs API의 개요"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: jotaub
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 2a718be9789aa4befdf2d3e1ac437fa69d6f33b8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/04/2017

---

# <a name="available-event-hubs-apis"></a>사용할 수 있는 Event Hubs API

## <a name="runtime-apis"></a>런타임 API

다음은 현재 사용 가능한 모든 Azure Event Hubs 클라이언트의 목록입니다. 이러한 라이브러리 중 일부에는 제한된 관리 기능이 포함되어 있지만 관리 작업에만 사용되는 [특정 라이브러리](#management-apis)도 있습니다. 이러한 라이브러리의 핵심적인 부분은 이벤트 허브에서 메시지를 주고 받는 것입니다.

각 런타임 라이브러리의 현재 상태에 대한 자세한 내용은 [추가 정보](#additional-information)를 참조하세요.

| 언어/플랫폼 | 클라이언트 패키지 | EventProcessorHost 패키지 | 리포지토리 |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | 해당 없음 |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| 노드 | [NPM](https://www.npmjs.com/package/azure-event-hubs) | 해당 없음 | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | 해당 없음 | 해당 없음 | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>추가 정보

#### <a name="net"></a>.NET
.NET 에코 시스템에는 여러 개의 런타임이 있으므로 Event Hubs용 .NET 라이브러리도 여러 개 있습니다. .NET Framework 라이브러리는 .NET Framework 환경에서만 실행될 수 있지만 .NET Standard 라이브러리는 .NET Core 또는 .NET Framework를 사용하여 실행할 수 있습니다. .NET Framework에 대한 자세한 내용은 [프레임워크 버전](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)을 참조하세요.

#### <a name="node"></a>노드

Node.js 라이브러리는 현재 미리 보기로 제공되며 Microsoft 직원 및 외부 참가자가 추가적인 프로젝트로 관리합니다. 소스 코드를 포함하는 모든 참가 프로그램은 언제든지 환영이며 검토해드립니다.

## <a name="management-apis"></a>관리 API

다음은 현재 사용 가능한 관리용 라이브러리의 목록입니다. 이러한 라이브러리 중 런타임 작업을 포함하는 것은 없으며 Event Hubs 엔터티를 관리하는 데만 사용됩니다.

| 언어/플랫폼 | 관리 패키지 | 리포지토리 |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [이벤트 허브 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)
