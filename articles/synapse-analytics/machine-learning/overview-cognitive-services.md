---
title: Azure Synapse Analytics의 Cognitive Services
description: Azure Cognitive Services의 미리 학습된 모델을 사용하여 Azure Synapse Analytics의 AI(인공 지능)로 데이터를 보강합니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: c9db8c1cf508d05dfafae672e5f03ff55bb046c0
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114581"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Cognitive Services

Azure Cognitive Services의 미리 학습된 모델을 사용하면 Azure Synapse Analytics의 AI(인공 지능)로 데이터를 보강할 수 있습니다.

[Azure Cognitive Services](/azure/cognitive-services/what-are-cognitive-services)는 AI 또는 데이터 과학 기술이 없는 경우에도 데이터에 인지 인텔리전스를 추가하는 클라우드 기반 서비스입니다. Synapse Analytics의 데이터에서 이러한 서비스를 사용할 수 있는 몇 가지 방법이 있습니다.

- Synapse Analytics의 Cognitive Services 마법사는 Spark 테이블의 데이터를 사용하여 인식 서비스에 연결하는 Synapse Notebook에서 PySpark 코드를 생성합니다. 그런 다음, 이 서비스에서 미리 학습된 기계 학습 모델을 사용하여 데이터에 AI를 추가하는 작업을 수행합니다.

- [Microsoft Machine Learning for Apache Spark(미리 보기)를 사용하여 기계 학습 애플리케이션 빌드](tutorial-build-applications-use-mmlspark.md) 자습서에서는 MMLSpark(Microsoft Machine Learning for Apache Spark)를 사용하여 여러 인지 서비스를 호출하는 방법을 보여줍니다.

- 마법사에서 생성된 PySpark 코드 또는 자습서에 제공된 예제 MMLSpark 코드부터는 데이터에 다른 인지 서비스를 사용하는 사용자 고유의 코드를 작성할 수 있습니다. 사용 가능한 서비스에 대한 자세한 내용은 [Azure Cognitive Services 무엇인가요?](/azure/cognitive-services/what-are-cognitive-services)를 참조하세요.

## <a name="get-started"></a>시작하기

[Azure Synapse의 Cognitive Services를 사용하기 위한 사전 필수 구성 요소](tutorial-configure-cognitive-services-synapse.md) 자습서에서는 Synapse Analytics의 Cognitive Services를 사용하기 전에 수행해야 하는 몇 가지 단계를 안내합니다.

## <a name="tutorials"></a>자습서

다음 자습서에서는 Synapse Analytics의 Cognitive Services를 사용하는 전체 예제를 제공합니다.

- [Cognitive Services를 사용한 감정 분석](tutorial-cognitive-services-sentiment.md) - 고객 의견의 예제 데이터 세트를 사용하여 각 행의 주석 감정을 나타내는 열이 있는 Spark 테이블을 빌드합니다.

- [Cognitive Services를 통한 변칙 검색](tutorial-cognitive-services-anomaly.md) - 시계열 데이터의 예제 데이터 세트를 사용하여 각 행의 데이터가 변칙인지 여부를 나타내는 열이 있는 Spark 테이블을 빌드합니다.

- [Microsoft Machine Learning for Apache Spark를 사용하여 기계 학습 애플리케이션 빌드(미리 보기)](tutorial-build-applications-use-mmlspark.md) - 이 자습서에서는 MMLSpark를 사용하여 Cognitive Services의 여러 모델에 액세스하는 방법을 보여줍니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)
- [Cognitive Services란?](/azure/cognitive-services/what-are-cognitive-services)
- [Synapse Analytics 갤러리의 샘플 Notebook 사용](quickstart-gallery-sample-notebook.md)