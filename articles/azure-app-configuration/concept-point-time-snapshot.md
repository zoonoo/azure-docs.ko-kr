---
title: Azure App Configuration 지정 시간 스냅숏 | Microsoft Docs
description: Azure App Configuration에서 지정 시간 스냅숏이 작동하는 방법에 대한 개요
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
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885139"
---
# <a name="point-in-time-snapshot"></a>지정 시간 스냅숏

Azure App Configuration은 새로운 키-값 쌍이 생성되고 나중에 수정되는 정확한 시간 레코드를 보관합니다. 이러한 레코드는 키-값 변경에 대한 전체 타임 라인을 형성합니다. 앱 구성 저장소는 키-값의 기록을 다시 구성하고 과거 특정 시점에서 현재까지의 값을 재생할 수 있습니다. 이 기능을 사용하면 과거로 “시간 이동”이 가능하고 이전 키-값을 검색할 수 있습니다. 예를 들어 애플리케이션을 롤백해야 하는 경우, 과거 구성을 복구할 수 있도록 가장 최근 배포 직전인 어제에 해당하는 구성 설정을 가져올 수 있습니다.

## <a name="key-value-retrieval"></a>키-값 검색

지난 키-값을 검색하려면 REST API 호출의 HTTP 헤더에 키-값 스냅샷이 작성된 시간을 지정해야 합니다. 예: 

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

현재 App Configuration에는 7일간의 변경 기록이 유지됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: ASP.NET 웹앱 만들기](quickstart-aspnet-core-app.md)  
