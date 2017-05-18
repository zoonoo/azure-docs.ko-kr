---
title: "MySQL용 Azure 데이터베이스 Compute 단위 및 저장 단위 설명 | Microsoft Docs"
description: "Compute 단위 및 저장 단위에 대해 설명하고 최대 Compute 단위 또는 저장 단위에 도달할 때 발생하는 현상에 대해 설명합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2c10253698864b5e1964fbf15b3484752a551c3b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>Compute 단위 및 저장 단위에 대해 설명
이 문서에서는 Compute 단위 및 저장 단위의 개념에 대해 설명하고 최대 Compute 단위 또는 저장 단위에 도달할 때 발생하는 현상에 대해 설명합니다.

## <a name="what-are-compute-units"></a>Compute 단위란?
Compute 단위는 단일 MySQL용 Azure 데이터베이스 서버에서 사용할 수 있도록 보장되는 CPU 처리량을 측정하는 단위입니다. Compute 단위는 CPU 및 메모리 리소스를 혼합해서 측정합니다. 일반적으로 50 Compute 단위는 서버에서 사용할 수 있는 보장된 처리량의 1/2 코어와 같고, 100 Compute 단위는 1 코어와 같으며, 2000 Compute 단위는 20 코어와 같습니다.

Compute 단위당 메모리 양은 기본, 표준 및 프리미엄 서비스 계층에 대해 최적화됩니다. 성능 수준을 증가시켜 Compute 단위를 두 배로 높일 경우 단일 MySQL용 Azure 데이터베이스에서 사용할 수 있는 리소스 집합이 동일하게 두 배로 높아집니다.

예를 들어 표준 2000 Compute 단위는 100 Compute 단위로 구성된 표준 계층보다 20배 더 많은 CPU 처리량 및 메모리를 제공합니다. 그러나 표준 100 Compute 단위는 기본 100 Compute 단위에 비해 동일한 CPU 처리량을 제공하지만 표준 계층에 미리 구성된 메모리 양은 기본 계층에 대해 구성된 메모리 양의 2배이므로 더 나은 작업 성능 및 트랜잭션 대기 시간을 제공합니다.

>[!IMPORTANT]
>예측 가능한 작업 성능 처리량 및 높은 사용자 동시성을 위해서는 표준 서비스 계층을 선택하는 것이 강력히 권장됩니다.

언제든지 응용 프로그램 가동 중지 시간을 거의 발생하지 않으면서 [서비스 계층](./concepts-service-tiers.md)을 변경할 수 있습니다. 많은 비즈니스 및 앱에서 각 단일 MySQL용 Azure 데이터베이스 서버 내에 일대다 데이터베이스를 만들 수 있고 필요할 때 성능을 높이거나 낮춤으로써 유연하게 비용을 관리할 수 있습니다.

>[!IMPORTANT]
>현재, 서비스 계층 내에서 성능 수준을 높이거나 낮추도록 지원됩니다. 예를 들어 표준 100 Compute 단위에서 표준 400 Compute 단위로 확장할 수 있습니다. 마찬가지로 표준 400 Compute 단위에서 표준 100 Compute 단위로 축소할 수 있습니다. 예를 들어 기본 및 표준 계층 사이 등과 같이 서비스 계층 간에 확장 또는 축소하는 기능이 향후 제공될 예정입니다.

## <a name="what-are-storage-units"></a>저장 단위란?
저장 단위는 MySQL용 단일 Azure 데이터베이스 서버에서 사용할 수 있도록 보장되는 프로비전된 저장소 용량을 측정하는 단위입니다. 각 서비스 계층에는 선택된 서비스 계층의 가격에 포함된 고정 크기의 저장소가 프로비전되어 있습니다.

필요에 따라 아래 표에 설명된 대로 허용되는 최대 저장소에 도달할 때까지 저장 단위 크기를 Compute 단위와는 별도로 125GB씩 조정할 수 있습니다.

| **서비스 계층 기능** | **Basic** | **Standard** | **프리미엄\*** |
|---------------------------|-----------|--------------|---------------|
| 포함된 저장 단위 | 50GB | 125GB | 250GB |
| 최대 총 저장소 | 1050GB | 10000GB | 4000GB |
| 저장소 IOPS 보장 | 해당 없음 | 예 | 예 |
| 최대 저장소 IOPS | 해당 없음 | 30,000 | 40,000 |

표준 및 프리미엄 서비스 계층은 프로비전된 IOPS 보장도 제공합니다. 프로비전된 사용 가능한 IOPS 크기는 구성된 서비스 계층 및 저장소 용량에 따라 다릅니다. 표준 계층에 배포된 서버의 경우 IOPS는 프로비전된 저장소의 3배로 고정됩니다. 

예를 들어 프로비전된 저장소가 125GB이면 서버에서는 프로비전된 IOPS를 375GB 사용할 수 있습니다. 프리미엄 계층은 매우 낮은 대기 시간과 높은 IOPS 저장소를 제공합니다. 프리미엄 계층에 배포된 서버의 경우 프로비전된 짧은 대기 시간 IOPS는 프로비전된 저장소의 5배~10배로 확장될 수 있습니다.

>[!IMPORTANT]
>구성된 Compute 단위에서 작업 병목 상태가 발생하면 프로비전된 사용 가능한 IOPS를 완전하게 실현하지 못할 수 있습니다. Compute 단위를 모니터링하고, 프로비전된 사용 가능한 IOPS를 완전하게 이용할 수 없으면 Compute 단위 확장을 고려하는 것이 좋습니다.

프로비전된 저장소를 증가시켜 저장 단위를 확장하면 표준 및 프리미엄 계층에 대한 프로비전된 IOPS도 그에 비례해서 증가됩니다.

>[!IMPORTANT]
>기본 계층에서는 IOPS 보장을 제공하지 않습니다.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>내 작업에 필요한 Compute 단위의 수를 확인하려면 어떻게 해야 하나요?
기존 온-프레미스 또는 가상 컴퓨터에서 실행되는 MySQL을 마이그레이션하려는 경우 작업에 필요한 처리량의 코어 수를 예측하여 Compute 단위의 수를 결정할 수 있습니다. 

온-프레미스 또는 가상 컴퓨터에서 현재 4개 코어를 사용하는 경우(CPU 하이퍼스레드 제외) 먼저 MySQL용 Azure 데이터베이스에 대해 400 Compute 단위를 구성할 수 있습니다. Compute 단위는 응용 프로그램 가동 중지 시간이 거의 없이도 작업 필요량에 따라 동적으로 확장 또는 축소할 수 있습니다. 또한 포털 또는 CLI를 통해 Compute 단위 소비량을 모니터링하여 MySQL 서버의 리소스 크기를 적절히 지정할 수도 있습니다.

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>내 작업에 필요한 저장 단위의 수를 확인하려면 어떻게 해야 하나요?
먼저 필요한 저장소 용량을 확인하여 필요한 저장 단위 크기를 예측할 수 있습니다. Basic, Standard 및 Premium 계층에는 SKU에 기본 제공된 저장소가 포함되어 있습니다.

두 번째로 중요한 요소는 필요한 IOPS를 결정하는 것입니다. 기본 계층은 보장 없이 가변 IOPS를 제공합니다. 표준 계층은 프로비전된 저장소 GB당 3개 IOPS 고정 비율로 확장되고 IOPS 보장을 제공합니다. 또한 포털이나 CLI를 통해 프로비전된 IOPS의 사용량을 모니터링하여 사용량을 확인할 수 있습니다.

>[!IMPORTANT]
>일단 프로비전된 저장 단위는 동적으로 축소할 수 없습니다.

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>최대 Compute 단위 및/또는 저장 단위에 도달하면 어떻게 되나요?
선택한 서비스 계층/성능 수준에 허용되는 최대 한도까지 데이터베이스 워크로드를 실행하는 데 필요한 리소스를 제공하도록 성능 수준이 보정 및 제어됩니다.

작업량이 Compute 단위/프로비전된 IOPS 제한 중 하나에 도달할 경우 최대 허용 수준에서 계속 리소스를 수신하지만 쿼리의 대기 시간이 증가할 가능성이 큽니다. 속도 저하가 너무 심해서 쿼리 시간 초과가 시작되지 않으면 이러한 한도에 오류가 발생하지는 않지만 워크로드에서 작업 속도가 느려집니다.

허용되는 최대 연결 제한에 도달하면 명시적 오류가 표시됩니다. 리소스 제한에 대한 자세한 내용은 [MySQL용 Azure 데이터베이스 리소스 제한](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits)을 참조하세요. <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>다음 단계
[MySQL용 Azure 데이터베이스 서비스 계층](./concepts-service-tiers.md) 

