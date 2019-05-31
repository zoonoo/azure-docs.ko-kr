---
title: Azure Service Fabric에서 지원 되는 클러스터 버전 | Microsoft Docs
description: Azure Service Fabric 클러스터 버전에 알아봅니다.
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
ms.date: 05/24/2019
ms.author: aljo
ms.openlocfilehash: 606b14fba093b6ec8039c646a49bc3bf7d24eb51
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66296778"
---
# <a name="supported-service-fabric-versions"></a>지원되는 Service Fabric 버전

클러스터는 지원 되는 Azure Service Fabric 버전을 항상 실행 해야 합니다. Service Fabric에서 이전 버전에 대 한 지원의 새 버전의 릴리스를 발표 후 60 일의 최소 종료 됩니다. 새 릴리스 발표에 찾을 수 있습니다 합니다 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)합니다.

지원 되는 Service Fabric 버전을 실행 하 여 클러스터를 유지 하는 방법에 대 한 내용은 다음 문서를 참조 하세요.

- [Azure Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade.md)
- [독립 실행형 Windows Server 클러스터에서 실행 되는 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>지원되는 버전

다음 표에서 Service Fabric 및 지원 종료 날짜의 버전을 나열 합니다.

| 클러스터의 service Fabric 런타임 | 클러스터 버전에서 직접 업그레이드할 수 있습니다. |호환 되는 SDK 또는 NuGet 패키지 버전 | 지원 종료 |
| --- | --- |--- | --- |
| 5.3.121 이전의 모든 클러스터 버전 | 5.1.158.* |버전 2.3보다 작거나 같음 |2017년 1월 20일 |
| 5.3.* | 5.1.158.* |버전 2.3보다 작거나 같음 |2017년 2월 24일 |
| 5.4.* | 5.1.158.* |버전 2.4보다 작거나 같음 |2017년 5월 10일       |
| 5.5.* | 5.4.164.* |버전 2.5보다 작거나 같음 |2017년 8월 10일    |
| 5.6.* | 5.4.164.* |버전 2.6보다 작거나 같음 |2017년 10월 13일   |
| 5.7.* | 5.4.164.* |버전 2.7보다 작거나 같음 |2017 년 12 월 15 일  |
| 6.0.* | 5.6.205.* |버전 2.8보다 작거나 같음 |2018 년 3 월 30 일     |
| 6.1.* | 5.7.221.* |버전 3.0보다 작거나 같음 |2018년 7월 15일      |
| 6.2.* | 6.0.232.* |버전 3.1보다 작거나 같음 |2018 년 10 월 26 일   |
| 6.3.* | 6.1.480.* |버전 3.2보다 작거나 같음 |2019 년 3 월 31 일  |
| 6.4.* | 6.2.301.* |버전 3.3보다 작거나 같음 |현재 버전, 종료 날짜 |

## <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에서 지원 되는 Service Fabric 버전에 대 한 지원 되는 운영 체제를 나열합니다.

| 운영 체제 | 가장 오래 된 지원 되는 Service Fabric 버전 |
| --- | --- |
| Windows Server 2012 R2 | 모든 버전 |
| Windows Server 2016 | 모든 버전 |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>지원 되는 버전 이름

다음 표에서 Service Fabric 버전 이름과 해당 버전 번호를 나열합니다.

| 버전 이름 | Windows 버전 번호 | Linux 버전 번호 |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | 해당 없음 |
| 5.3 CU1 | 5.3.204.9494 | 해당 없음 |
| 5.3 CU2 | 5.3.301.9590 | 해당 없음 |
| 5.3 CU3 | 5.3.311.9590 | 해당 없음 |
| 5.4 CU2 | 5.4.164.9494 | 해당 없음 |
| 5.5 CU1 | 5.5.216.0    | 해당 없음 |
| 5.5 CU2 | 5.5.219.0    | 해당 없음 |
| 5.5 CU3 | 5.5.227.0    | 해당 없음 |
| 5.5 CU4 | 5.5.232.0    | 해당 없음 |
| 5.6 RTO | 5.6.204.9494 | 해당 없음 |
| 5.6 CU2 | 5.6.210.9494 | 해당 없음 |
| 5.6 CU3 | 5.6.220.9494 | 해당 없음 |
| 5.7 RTO | 5.7.198.9494 | 해당 없음 |
| 5.7 CU4 | 5.7.221.9494 | 해당 없음 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | 해당 없음 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | 해당 없음 |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | 해당 없음 |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |