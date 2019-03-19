---
title: Azure Service Fabric 클러스터 버전 살펴보기 | Microsoft Docs
description: 지원되는 Azure Service Fabric 클러스터 버전에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: de5522e68d1329ce2b80a4d3c7045d38c13169e5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191650"
---
# <a name="supported-service-fabric-versions"></a>지원되는 Service Fabric 버전

클러스터가 지원되는 Service Fabric 버전을 항상 실행하도록 해야 합니다. 새로운 버전의 Service Fabric 릴리스를 발표하면 이전 버전은 해당 날짜부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다.

클러스터에서 지원되는 Service Fabric 버전을 계속 실행하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 클러스터에서 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade.md)
- [독립 실행형 windows server 클러스터에서 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)

다음은 지원되는 Service Fabric 버전과 지원 종료 날짜 목록입니다.

| **클러스터의 Service Fabric 런타임** | **클러스터 버전에서 직접 업그레이드할 수 있습니다.** |**호환 가능한 SDK / NuGet 패키지 버전** | **지원 종료 날짜** |
| --- | --- |--- | --- |
| 5.3.121 이전의 모든 클러스터 버전 | 5.1.158* |버전 2.3보다 작거나 같음 |2017년 1월 20일 |
| 5.3.* | 5.1.158.* |버전 2.3보다 작거나 같음 |2017년 2월 24일 |
| 5.4.* | 5.1.158.* |버전 2.4보다 작거나 같음 |2017년 5월 10일       |
| 5.5.* | 5.4.164.* |버전 2.5보다 작거나 같음 |2017년 8월 10일    |
| 5.6.* | 5.4.164.* |버전 2.6보다 작거나 같음 |2017년 10월 13일   |
| 5.7.* | 5.4.164.* |버전 2.7보다 작거나 같음 |2017년 12월 15일  |
| 6.0.* | 5.6.205.* |버전 2.8보다 작거나 같음 |2018년 3월 30일     |
| 6.1.* | 5.7.221.* |버전 3.0보다 작거나 같음 |2018년 7월15일      |
| 6.2.* | 6.0.232.* |버전 3.1보다 작거나 같음 |2018년 10월 26일   |
| 6.3.* | 6.1.480.* |버전 3.2보다 작거나 같음 |31,2019 년 3 월  |
| 6.4.* | 6.2.301.* |버전 3.3보다 작거나 같음 |현재 버전 및 종료 날짜 |