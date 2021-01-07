---
title: 데이터 과학 작업 실행 - Team Data Science Process
description: 데이터 과학자가 추적 가능하고, 버전을 제어하고, 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법을 설명합니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e47dad8498c48a5da5307517efe493fa5c1aa590
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94748066"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>데이터 과학 작업 실행: 탐색, 모델링 및 배포

일반적인 데이터 과학 작업으로 데이터 탐색, 모델링 및 배포가 있습니다. 이 문서에서는 대화형 데이터 탐색, 데이터 분석, 보고 및 모델 작성과 같은 몇 가지 일반적인 데이터 과학 작업을 완료 하는 작업을 설명 합니다. 프로덕션 환경에 모델을 배포 하기 위한 옵션에는 다음이 포함 될 수 있습니다.

- [Azure Machine Learning](../index.yml)
- [ML 서비스 포함 SQL-Server](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> 탐색 

데이터 과학자는 다양한 방법으로, 예를 들어 Python(예: matplotlib) 또는 R(예: ggplot 또는 lattice)과 함께 사용할 수 있는 라이브러리 및 패키지를 사용하여 탐색 및 보고를 수행할 수 있습니다. 데이터 과학자는 해당 코드를 사용자 지정하여 특정 시나리오의 데이터 탐색 요구에 맞출 수 있습니다. 구조화된 데이터를 다루기 위한 요구는 텍스트나 이미지 등 구조화되지 않은 데이터의 경우와 다릅니다. 

Azure Machine Learning 등의 제품은 기능 생성을 포함 하 여 데이터 랭 글 링 및 탐색을 위한 [고급 데이터 준비](../how-to-create-register-datasets.md) 도 제공 합니다. 사용자는 자신의 요구에 가장 맞는 도구, 라이브러리 및 패키지에 대해 결정해야 합니다. 

이 단계가 종료할 때의 결과물은 데이터 탐색 보고서입니다. 보고서는 모델링 및 데이터가 모델링 단계로 진행하는 데 적합한지 여부를 평가에 사용할 데이터의 매우 포괄적인 뷰를 제공해야 합니다. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> 모델링

다양한 언어의 수많은 모델 교육용 도구 키트와 패키지가 있습니다. 데이터 과학자는 관련 비즈니스 사용 사례 및 프로덕션 시나리오에 대한 정확도 및 대기 시간에 관한 성능을 고려하여 편하게 느끼는 키트와 패키지를 자유롭게 사용할 수 있습니다.

### <a name="model-management"></a>모델 관리
복수의 모델을 작성한 후에는 일반적으로 모델을 등록하고 관리하기 위한 시스템이 있어야 합니다. 일반적으로 스크립트 또는 API와 백 엔드 데이터베이스 또는 버전 관리 시스템의 조합이 필요합니다. 이러한 관리 작업에 대해 고려할 수 있는 몇 가지 옵션은 같습니다.

1. [Azure Machine Learning 모델 관리 서비스](../index.yml)
2. [MIT의 ModelDB](http://modeldb.csail.mit.edu:3000/projects) 
3. [모델 관리 시스템으로 SQL Server 사용](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> 배포

프로덕션 배포를 사용하여 비즈니스의 현재 역할을 수행하도록 모델링할 수 있습니다. 배포된 모델의 예측을 비즈니스 의사 결정에 사용할 수 있습니다.

### <a name="production-platforms"></a>프로덕션 플랫폼
모델을 프로덕션으로 전환하기 위한 여러 접근법과 플랫폼이 있습니다. 다음은 몇 가지 옵션입니다.


- [Azure Machine Learning에서 배포 모델링](../how-to-deploy-and-where.md)
- [SQL-Server의 모델 배포](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> 배포 전에 모델 스코어링의 대기 시간이 프로덕션에 사용할 만큼 충분히 낮은지 확인해야 합니다.
>
>

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 연습에서 추가 예제를 사용할 수 있습니다. 이러한 단계는 [예제 연습](walkthroughs.md) 자료에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 보여 줍니다.

> [!NOTE]
> Azure Machine Learning Studio를 사용하는 배포에 대해서는 [Azure Machine Learning 웹 서비스 배포](../classic/deploy-a-machine-learning-web-service.md)를 참조하세요.
>
>

### <a name="ab-testing"></a>A/B 테스트
프로덕션에 복수의 모델이 있는 경우 [A/B 테스트](https://en.wikipedia.org/wiki/A/B_testing)를 수행하여 모델의 성능을 비교하면 유용할 수 있습니다. 

 
## <a name="next-steps"></a>다음 단계

[데이터 과학 프로젝트의 진행률 추적](track-progress.md)에서는 데이터 과학자가 데이터 과학 프로젝트의 진행률을 추적할 수 있는 방법을 보여 줍니다.

[모델 작업 및 CI/CD](ci-cd-flask.md)에서는 개발된 모델로 CI/CD를 수행하는 방법을 보여줍니다.
