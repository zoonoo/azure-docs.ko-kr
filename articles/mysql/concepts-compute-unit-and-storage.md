---
title: MySQL용 Azure Database의 Compute 단위 설명 | Microsoft Docs
description: 'MySQL용 Azure DB: 이 문서에서는 Compute 단위의 개념과 워크로드가 최대 Compute 단위에 도달할 때 발생하는 현상에 대해 설명합니다.'
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678364"
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>MySQL용 Azure Database의 Compute 단위 설명
이 항목에서는 Compute 단위의 개념과 워크로드가 최대 Compute 단위에 도달할 때 발생하는 현상에 대해 설명합니다.

## <a name="what-are-compute-units"></a>Compute 단위란?
Compute 단위는 MySQL 서버용 단일 Azure Database에서 사용할 수 있도록 보장되는 CPU 처리량을 측정하는 단위입니다. Compute 단위는 CPU 및 메모리 리소스를 혼합해서 측정합니다. 일반적으로 50 Compute 단위는 코어의 절반과 같습니다. 100 Compute 단위는 하나의 코어와 같습니다. 2000 Compute 단위는 서버에서 사용할 수 있도록 보장되는 처리량의 20개 코어와 같습니다.

Compute 단위당 메모리 양은 기본 및 표준 가격 책정 계층에 대해 최적화됩니다. 성능 수준을 증가시켜 Compute 단위를 두 배로 높일 경우 단일 MySQL용 Azure Database에서 사용할 수 있는 리소스 집합이 동일하게 두 배로 높아집니다.

예를 들어 표준 800 Compute 단위는 표준 100 Compute 단위 구성보다 8배 더 많은 CPU 처리량 및 메모리를 제공합니다. 그러나 표준 100 Compute 단위는 기본 100 Compute 단위에 비해 동일한 CPU 처리량을 제공하지만 표준 가격 책정 계층에 미리 구성된 메모리 양은 기본 가격 책정 계층에 대해 구성된 메모리 양의 2배입니다. 따라서 표준 가격 책정 계층은 동일한 Compute 단위가 선택된 기본 가격 책정 계층보다 더 나은 워크로드 성능과 더 낮은 트랜잭션 대기 시간을 제공합니다.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>내 작업에 필요한 Compute 단위의 수를 확인하려면 어떻게 해야 하나요?
온-프레미스 또는 가상 머신에서 실행되는 기존 MySQL 서버를 마이그레이션하려는 경우 워크로드에 필요한 처리량의 코어 수를 예측하여 Compute 단위의 수를 결정할 수 있습니다. 

기존 온-프레미스 또는 가상 머신 서버에서 현재 4개 코어를 사용하는 경우(CPU 하이퍼스레드 제외) 먼저 MySQL 서버용 Azure Database에 대해 400 Compute 단위를 구성하여 시작합니다. Compute 단위는 응용 프로그램 가동 중지 시간이 거의 없이도 작업 필요량에 따라 동적으로 확장 또는 축소할 수 있습니다. 

Azure Portal에서 메트릭 그래프를 모니터링하거나 Azure CLI 명령을 작성하여 Compute 단위를 측정합니다. 모니터링할 관련 메트릭은 Compute 단위 백분율 및 Compute 단위 제한입니다.

>[!IMPORTANT]
> 저장소 IOPS가 최대한 활용되지 않는다는 것을 알게 되면 Compute 단위 사용률도 모니터링하는 것이 좋습니다. Compute 단위를 올려서 CPU 또는 메모리 제한으로 인한 성능 병목 현상을 줄임으로써 IO 처리량을 더 높일 수 있습니다.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>내 최대 Compute 단위에 도달한 경우 어떻게 되나요?
선택한 가격 책정 계층 및 성능 수준의 최대 한도까지 데이터베이스 워크로드를 실행하는 리소스를 제공하도록 성능 수준이 보정 및 제어됩니다. 

워크로드가 Compute 단위 또는 프로비전된 IOPS 제한 중 최대 제한에 도달할 경우 최대 허용 수준에서 계속 리소스를 활용할 수 있지만 쿼리의 대기 시간이 증가할 가능성이 큽니다. 속도 저하가 너무 심해서 쿼리 시간이 초과되지 않는 한, 이러한 제한으로 인해 오류가 발생하지는 않지만 워크로드 속도가 느려집니다. 

워크로드가 연결 수의 최대 제한에 도달할 경우 명시적 오류가 발생됩니다. 리소스 제한에 대한 자세한 내용은 [MySQL용 Azure Database의 제한 사항](concepts-limits.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
가격 책정 계층에 대한 자세한 내용은 [MySQL용 Azure Database 가격 책정 계층](./concepts-service-tiers.md)을 참조하세요.
