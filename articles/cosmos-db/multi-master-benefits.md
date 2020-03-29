---
title: Azure 코스모스 DB 멀티 마스터 혜택
description: Azure Cosmos DB의 다중 마스터의 이점, 단일 및 다중 쓰기 위치에서의 대기 시간 및 SLA 요구 사항 비교의 이점을 이해합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872012"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Azure 코스모스 DB의 멀티 마스터 이점 이해

Cosmos DB 계정 운영자는 응용 프로그램에 대한 대기 시간, 가용성 및 RTO 요구 사항을 보장하기 위해 적절한 전역 배포 구성을 선택해야 합니다. 여러 쓰기 위치로 구성된 Azure Cosmos 계정은 99.999% 쓰기 가용성 SLA, <10ms 쓰기 대기 시간 SLA 를 포함하여 단일 쓰기 위치가 있는 계정에 비해 상당한 이점을 제공합니다.

## <a name="comparison-of-features"></a>기능 비교

|애플리케이션 요구 사항|여러 쓰기 위치|단일 쓰기 위치|참고|
|---|---|---|---|
|P99에서 <10ms의 대기 시간 SLA 쓰기|**예**|예|단일 쓰기 위치가 있는 계정은 각 쓰기에 대해 추가 지역 간 네트워크 대기 시간이 발생합니다.|
|P99에서 <10ms의 대기 시간 SLA 읽기|**예**|yes| |
|99.999%의 SLA 작성|**예**|예|단일 쓰기 위치 보증 SLA99.99%를 가진 계정|
|RTO = 0|**예**|예|지역 재해 발생 시 쓰기에 대한 중지 시간 0. 단일 쓰기 위치의 계정에는 RTO가 15분입니다.|

## <a name="next-steps"></a>다음 단계

Azure 코스모스 계정에서 인에이블멀티쓰기Locations를 사용하지 않으려면 [지원 티켓을 열십시오.](https://azure.microsoft.com/support/create-ticket/)
