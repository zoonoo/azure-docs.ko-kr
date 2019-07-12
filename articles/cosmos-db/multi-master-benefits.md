---
title: Azure Cosmos DB 다중 마스터 혜택
description: Azure Cosmos DB의 다중 마스터의 이점을 이해 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789294"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Azure Cosmos DB의 다중 마스터 이점을 이해합니다

Cosmos DB 계정 운영자는 대기 시간, 가용성 및 응용 프로그램에 대 한 RTO 요구 사항을 확인 하려면 적절 한 전역 배포 구성을 선택 해야 합니다. 계정을 포함 하 여 단일 쓰기 위치를 통해 상당한 이점을 제공 하는 여러 쓰기 위치를 사용 하 여 구성 된 azure Cosmos 계정, 99.999% 가용성 SLA, 쓰기 < 10ms 쓰기 대기 시간 SLA는 99 백 분 위 수 및 RTO에서 지역 재해에 0입니다.

## <a name="comparison-of-features"></a>기능 비교

|애플리케이션 요구 사항|여러 쓰기 위치|단일 쓰기 위치|참고|
|---|---|---|---|
|쓰기의 대기 시간 SLA를 < P99에서 10ms 미만의|**예**|아니요|단일 쓰기 위치를 사용 하 여 계정 각 쓰기에 대 한 추가 지역 간 네트워크 대기 시간을 발생합니다.|
|대기 시간 SLA 읽기 < 10ms p99|**예**|예| |
|99.999%의 SLA 작성|**예**|아니요|단일 쓰기 위치를 사용 하 여 계정 99.99%의 SLA를 보장합니다.|
|RTO = 0|**예**|아니요|가동 중지 시간이 쓰기 지역 재해가 발생 한 경우 0입니다. 단일 쓰기 위치를 사용 하 여 계정에는 15 분의 RTO는.|

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정의 EnableMultipleWriteLocations를 사용 하지 않도록 설정 계속 하려는 경우 [지원 티켓을 열어](https://azure.microsoft.com/support/create-ticket/)합니다.
