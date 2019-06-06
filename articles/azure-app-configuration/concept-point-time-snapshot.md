---
title: Azure App Configuration 지정 시간 스냅샷 | Microsoft Docs
description: Azure App Configuration에서 지정 시간 스냅샷이 작동하는 방법에 대한 개요
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 83770e8c5f415670855b5cf2502d02c4d6919440
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998076"
---
# <a name="point-in-time-snapshot"></a>지정 시간 스냅샷

Azure App Configuration은 새 키-값 쌍을 만든 후 수정할 때의 정확한 시간을 기록합니다. 이러한 레코드는 키-값 변경에 대한 전체 타임 라인을 형성합니다. 앱 구성 저장소는 모든 키-값의 기록을 다시 구성하고, 과거 특정 시점에서 현재까지의 값을 재생할 수 있습니다. 이 기능을 사용하면 역방향 "시간 이동"을 수행하고 이전 키-값을 검색할 수 있습니다. 예를 들어 어제의 구성 설정을 가장 최근의 배포 바로 앞에 가져와서 이전 구성을 복구하고 애플리케이션을 롤백할 수 있습니다.

## <a name="key-value-retrieval"></a>키-값 검색

지난 키-값을 검색하려면 REST API 호출의 HTTP 헤더에 키-값이 스냅샷으로 작성된 시간을 지정합니다. 예:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

현재 App Configuration에서는 7일간의 변경 기록을 유지합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
