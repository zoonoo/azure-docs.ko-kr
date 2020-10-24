---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 345454557c1bd0df3b4e7210229c81f0149af0f3
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495103"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>클러스터로 무엇을 할 수 있나요?

Event Hubs 클러스터의 경우 수집 하 고 스트리밍할 수 있는 양은 생산자, 소비자, 수집 및 처리 속도 등 다양 한 요인에 따라 달라 집니다. 

다음 표에는 테스트를 통해 실현한 벤치마크 결과가 나와 있습니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 초당 400k 메시지 | 800MB/초 | 초당 800k 메시지 | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 초당 66.6k 메시지 | 1.33GB/초 | 초당 133k 메시지 | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 초당 34k 메시지 | 1.05GB/초 | 초당 34k 메시지 | 1,000TU | 250TU |

테스트에 사용된 조건은 다음과 같습니다.

- CUs (용량 단위)가 4 개인 전용 계층 Event Hubs 클러스터가 사용 되었습니다. 
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다. 
- 데이터는 모든 파티션으로부터 받는 두 개의 수신기 애플리케이션에서 수집되었습니다.

### <a name="can-i-scale-updown-my-cluster"></a>클러스터를 확장/축소할 수 있나요?

만든 후에는 최소 4 시간 사용에 대 한 클러스터가 청구 됩니다. 셀프 서비스 환경의 Preview 릴리스에서는 *기술 > 할당량 > 요청* 하는 Event Hubs 팀에 [지원 요청](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하 여 클러스터를 확장 하거나 축소할 수 있도록 전용 클러스터를 강화 하거나 규모를 확장할 수 있습니다. 클러스터 규모를 축소 하는 요청을 완료 하는 데 최대 7 일까 지 걸릴 수 있습니다. 

### <a name="how-will-geo-dr-work-with-my-cluster"></a>지리적 DR은 내 클러스터와 어떻게 작동 하나요?

전용 계층 클러스터 아래의 다른 네임 스페이스를 사용 하 여 전용 계층 클러스터에서 네임 스페이스를 지리적으로 쌍으로 연결할 수 있습니다. 처리량 제한이 호환 되지 않아 오류가 발생 하므로 표준 제품에서 전용 계층 네임 스페이스를 네임 스페이스와 페어링 하지 않는 것이 좋습니다. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>표준 네임 스페이스를 전용 계층 클러스터에 속하도록 마이그레이션할 수 있나요?
현재는 표준 네임 스페이스에서 전용으로 event hubs 데이터를 마이그레이션하기 위한 자동 마이그레이션 프로세스를 지원 하지 않습니다. 
