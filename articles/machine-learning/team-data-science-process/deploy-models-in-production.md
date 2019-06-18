---
title: 프로덕션에서 모델 배포 - Team Data Science Process
description: 프로덕션에 모델을 배포하여 비즈니스 의사 결정에 유용하게 사용될 수 있도록 하는 방법입니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1a75c842989cfbaf7bb1880831fda2bc6994622b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238340"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>비즈니스 의사 결정에 적극적인 역할을 수행할 수 있도록 프로덕션에 모델 배포

프로덕션 배포를 사용하여 비즈니스의 현재 역할을 수행하도록 모델링할 수 있습니다. 배포된 모델의 예측을 비즈니스 의사 결정에 사용할 수 있습니다.

## <a name="production-platforms"></a>프로덕션 플랫폼

모델을 프로덕션으로 전환하기 위한 여러 접근법과 플랫폼이 있습니다. 다음은 몇 가지 옵션입니다.

- [Azure Machine Learning Service를 사용하여 모델을 배포할 위치](../service/how-to-deploy-and-where.md)
- [SQL-Server의 모델 배포](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>배포 전에 모델 스코어링의 대기 시간이 프로덕션에 사용할 만큼 충분히 낮은지 확인해야 합니다.
>

>[!NOTE]
>Azure Machine Learning Studio를 사용하는 배포에 대해서는 [Azure Machine Learning 웹 서비스 배포](../studio/publish-a-machine-learning-web-service.md)를 참조하세요.
>

## <a name="ab-testing"></a>A/B 테스트

프로덕션에 복수의 모델이 있는 경우 [A/B 테스트](https://en.wikipedia.org/wiki/A/B_testing)를 수행하여 모델의 성능을 비교하면 유용할 수 있습니다. 
 
## <a name="next-steps"></a>다음 단계

**특정 시나리오**에 대한 프로세스의 모든 단계를 보여 주는 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 자료에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 보여 줍니다. 
