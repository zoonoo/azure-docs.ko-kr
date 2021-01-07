---
title: '자습서: Cognitive Services를 사용하여 변칙 검색'
description: Synapse에서 변칙 검색을 위해 Cognitive Services를 활용하는 방법에 대한 자습서
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464460"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>자습서: Cognitive Services를 사용하여 변칙 검색(미리 보기)

이 자습서에서는 [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492)를 사용하여 Azure Synapse에서 데이터를 쉽게 보강하는 방법에 대해 알아봅니다. [Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2147493)를 사용하여 변칙 검색을 수행합니다. Azure Synapse의 사용자는 변칙 검색을 위해 보강할 테이블을 선택하기만 하면 됩니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
> - 시계열 데이터가 포함된 Spark 테이블 데이터 세트를 가져오는 단계입니다.
> - Azure Synapse의 마법사 환경을 사용하여 Anomaly Detector Cognitive Service를 통해 데이터를 보강합니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>필수 구성 요소

- 기본 스토리지로 구성된 ADLS Gen2 스토리지 계정이 있는 [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md) 작업하려는 ADLS Gen2 파일 시스템의 **Storage Blob 데이터 기여자** 여야 합니다.
- Azure Synapse Analytics 작업 영역의 Spark 풀. 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../quickstart-create-sql-pool-studio.md)를 참조하세요.
- 이 자습서를 사용하려면 이 자습서에서 설명하는 사전 구성 단계도 완료해야 합니다. [Azure Synapse에서 Cognitive Services를 구성](tutorial-configure-cognitive-services-synapse.md)합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Spark 테이블 만들기

이 자습서에는 Spark 테이블이 필요합니다.

1. Spark 테이블을 생성하는 코드가 포함된 [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577) Notebook 파일을 다운로드합니다.

1. Azure Synapse 작업 영역에 파일을 업로드합니다.
![Notebook 업로드](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Notebook 파일을 열고 **모두 실행** 셀을 선택합니다.
![Spark 테이블 만들기](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. 이제 **anomaly_detector_testing_data** 라는 Spark 테이블이 기본 Spark 데이터베이스에 표시됩니다.

## <a name="launch-cognitive-services-wizard"></a>Cognitive Services 마법사 시작

1. 이전 단계에서 만든 Spark 테이블을 마우스 오른쪽 단추로 클릭합니다. "Machine Learning-> 기존 모델 보강"을 선택하여 마법사를 엽니다.
![점수 매기기 마법사 시작](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. 구성 패널이 표시되고 Cognitive Services 모델을 선택하라는 메시지가 표시됩니다. Anomaly Detector를 선택합니다.

![점수 매기기 마법사 시작 - 1단계](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>인증 세부 정보 제공

Cognitive Services에 인증하려면 Key Vault에서 사용할 비밀을 참조해야 합니다. 아래 입력은 이 단계 전에 완료해야 하는 [필수 구성 요소 단계](tutorial-configure-cognitive-services-synapse.md)에 따라 달라집니다.

- **Azure 구독**: Key Vault가 속한 Azure 구독을 선택합니다.
- **Cognitive Services 계정**: 연결하려는 Text Analytics 리소스입니다.
- **Azure Key Vault 연결 서비스**: 필수 구성 요소 단계의 일부로 Text Analytics 리소스에 연결된 서비스를 만들었습니다. 여기에서 선택하세요.
- **비밀 이름**: Cognitive Services 리소스에 인증하기 위한 키가 포함된 키 자격 증명 모음의 비밀 이름입니다.

![Azure Key Vault 세부 정보 제공](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>변칙 검색 구성

다음으로, 변칙 검색을 구성해야 합니다. 다음 세부 정보를 제공하세요.

- 세분성: 데이터가 샘플링되는 속도입니다. 예를 들어 데이터에 1분마다 값이 있으면 세분화는 미세합니다. **월간** 선택 

- 타임스탬프: 계열의 시간을 나타내는 열입니다. 열 **타임스탬프** 선택

- 시계열 값: Timestamp 열로 지정된 시간의 계열 값을 나타내는 열입니다. 열 **값** 선택

- 그룹화: 계열을 그룹화하는 열입니다. 즉, 이 열에 동일한 값이 있는 모든 행은 하나의 시계열을 형성해야 합니다. 열 **그룹** 선택

작업이 완료되면 **Notebook 열기** 를 선택합니다. 그러면 Azure Cognitive Services를 사용하여 변칙 검색을 수행하는 PySpark 코드가 포함된 Notebook이 생성됩니다.

![변칙 탐지기 구성](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Notebook 열기 및 실행

방금 연 Notebook은 [mmlspark 라이브러리](https://github.com/Azure/mmlspark)를 사용하여 Cognitive Services에 연결합니다.

제공된 Azure Key Vault 세부 정보를 통해 이 환경에서 비밀을 노출하지 않고도 안전하게 참조할 수 있습니다.

이제 **모두 실행** 셀을 통해 변칙 검색을 수행할 수 있습니다. [Cognitive Services - Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2147493)에 대해 자세히 알아보세요.

![변칙 검색 실행](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Cognitive Services를 사용한 감정 분석](tutorial-cognitive-services-sentiment.md)
- [자습서: Azure Synapse 전용 SQL 풀에서 기계 학습 모델 점수 매기기](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)