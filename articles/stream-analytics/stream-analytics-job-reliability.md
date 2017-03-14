---
title: "Azure Stream Analytics 작업과 관련한 서비스 중단 방지 | Microsoft Docs"
description: "Stream Analytics 작업 업그레이드의 복원력을 높이기 위한 지침."
services: stream-analytics
documentationCenter: 
authors: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/27/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: a2892343432f7dced535efb3917d915736580dfb
ms.openlocfilehash: cf8eba0f68e1e803026079f02b91f1bbaec189da
ms.lasthandoff: 03/01/2017

---

# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>서비스 업데이트 도중 Stream Analytics 작업 안정성 보장

완전히 관리되는 서비스가 되는 방법 중 하나는 새로운 서비스 기능 및 향상 기능을 빠른 속도로 도입하는 것입니다. 따라서 Stream Analytics는 매주(또는 더 자주) 서비스 업데이트 배포가 이루어질 수 있습니다. 얼마나 많은 테스트를 수행하든 관계없이 기존에 실행 중인 작업은 버그의 도입으로 인해 중단될 수 있는 위험이 있습니다. 중요한 스트리밍 처리 작업을 실행하는 고객은 이러한 위험을 방지해야 합니다. 고객이 이러한 위험을 줄이는 데 사용할 수 있는 메커니즘은 Azure의 **[지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** 모델입니다. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure 지역 쌍은 이러한 문제를 어떻게 해결할까요?

Stream Analytics는 지역 쌍의 작업이 별도의 일괄 처리로 업데이트되도록 보장합니다. 따라서 잠재적인 주요 버그를 식별하고 재구성하기 위해 업데이트 간에 충분 한 간격을 둡니다.

Stream Analytics 업데이트 배포는 지역 쌍 집합에서 동시에 발생하지 않습니다. 단, 인도 남부가 지역 쌍이지만 Stream Analytics 상태가 없는 _인도 중부는 예외_입니다. **동일한 그룹에 속하는** 여러 지역에서는 배포가 **동시에** 발생할 수 있습니다.

다음 차트에서 쌍 그룹 목록을 참조하세요.

그룹 A 지역 |  | 그룹 B 지역
------- | ------- | -------
일본 동부 | 지역 쌍 | 일본 서부
북유럽 |  | 서유럽
미국 중부 |  | 미국 동부2
동아시아 |  | 동남아시아
미국 중북부 |  | 미국 중남부
오스트레일리아 동부 |  | 오스트레일리아 동남부
미국 동부 |  | 미국 서부
브라질 남부 |  | 미국 중남부
중국 북부 |  | 중국 동부
독일 북동부 |  | 독일 중부

고객은 두 지역 쌍 모두에 동일한 작업을 배포하는 것이 좋습니다. 또한 고객은 Stream Analytics의 내부 모니터링 외에도 작업이 **둘 다** 프로덕션 작업인 것처럼 모니터링하는 것이 좋습니다. 중단이 발생한 이유가 Stream Analytics 서비스 업데이트 때문이라면 적절히 에스컬레이션하고 다운스트림 소비자를 정상 작업 출력으로 장애 조치(failover)합니다. 지원으로 에스컬레이션하면 지역 쌍이 새 배포의 영향을 받지 않게 되고 지역 쌍의 무결성이 유지됩니다.

