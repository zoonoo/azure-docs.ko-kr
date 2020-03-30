---
title: Azure 기계 학습의 데이터
titleSuffix: Azure Machine Learning
description: Azure Machine Learning이 데이터에 안전하게 연결하고 해당 데이터를 기계 학습 작업에 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128484"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure 기계 학습의 데이터 액세스

Azure 기계 학습을 사용하면 클라우드의 데이터에 쉽게 연결할 수 있습니다.  기본 저장소 서비스에 대한 추상화 계층을 제공하므로 저장소 유형에 특정한 코드를 작성하지 않고도 데이터에 안전하게 액세스하고 작업할 수 있습니다. 또한 Azure 기계 학습은 다음과 같은 데이터 기능을 제공합니다.

*    데이터 계보 의 버전 분석 및 추적
*    데이터 라벨링 
*    데이터 드리프트 모니터링
*    팬더 및 스파크 데이터 프레임과의 상호 운용성

## <a name="data-workflow"></a>데이터 워크플로우

클라우드 기반 스토리지 솔루션에서 데이터를 사용할 준비가 되면 다음 데이터 전송 워크플로를 사용하는 것이 좋습니다. 이 워크플로에서는 [Azure의](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 클라우드 기반 저장소 서비스에 Azure 저장소 계정 및 데이터가 있다고 가정합니다. 

1. Azure [컴퓨터 학습 데이터 스토어를](#datastores) 만들어 Azure 저장소에 연결 정보를 저장합니다.

2. 해당 데이터 스토어에서 [Azure Machine Learning 데이터 집합을](#datasets) 만들어 기본 저장소의 특정 파일을 가리킵니다. 

3. 기계 학습 실험에서 해당 데이터 집합을 사용하려면 다음
    1. 모델 교육을 위해 실험의 계산 대상에 마운트합니다.

        **또는** 

    1. Azure 기계 학습 솔루션, 자동화된 기계 학습(자동화된 ML) 실험 실행, 기계 학습 파이프라인 또는 [Azure 기계 학습 디자이너와](concept-designer.md)같은 솔루션에서 직접 사용합니다.

4. 모델 출력 데이터 집합에 대한 [데이터 집합 모니터를](#data-drift) 만들어 데이터 드리프트를 검색합니다. 

5. 데이터 드리프트가 감지되면 입력 데이터 집합을 업데이트하고 그에 따라 모델을 다시 학습합니다.

다음 다이어그램은 이 권장 워크플로에 대한 시각적 데모를 제공합니다.

![데이터 개념 다이어그램](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>데이터 저장소

Azure Machine Learning 데이터 스토어는 Azure 저장소에 대한 연결 정보를 안전하게 유지하므로 스크립트에서 코딩할 필요가 없습니다. [데이터스토어를 등록하고 만들어](how-to-access-data.md) 저장소 계정에 쉽게 연결하고 기본 Azure 저장소 서비스의 데이터에 액세스합니다. 

데이터 스토어로 등록할 수 있는 Azure에서 지원되는 클라우드 기반 저장소 서비스:

+ Azure Blob 컨테이너
+ Azure 파일 공유
+ Azure 데이터 레이크
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks 파일 시스템
+ Azure Database for MySQL

## <a name="datasets"></a>데이터 세트

Azure 기계 학습 데이터 집합은 저장소 서비스의 데이터를 가리키는 참조입니다. 데이터 복사본이 아니므로 추가 저장소 비용이 발생하지 않습니다. 스토리지의 데이터와 상호 작용하려면 [데이터 집합을 만들어](how-to-create-register-datasets.md) 데이터를 기계 학습 작업을 위한 소모품에 패키징합니다. 데이터 집합을 작업 영역에 등록하여 데이터 수집 복잡성 없이 다양한 실험에서 데이터 집합을 공유하고 다시 사용할 수 있습니다.

데이터 집합은 데이터 스토어를 통해 로컬 파일, 공용 URL, [Azure Open Dataset](https://azure.microsoft.com/services/open-datasets/)또는 Azure 저장소 서비스에서 만들 수 있습니다. 메모리 팬더 데이터 프레임에서 데이터 집합을 만들려면 쪽모이 마룻바닥과 같은 로컬 파일에 데이터를 작성하고 해당 파일에서 데이터 집합을 만듭니다.  

2가지 유형의 데이터 집합을 지원합니다. 
+ [TabularDataset은](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 제공된 파일 또는 파일 목록을 구문 분석하여 테이블 형식의 데이터를 나타냅니다. 추가 조작 및 정리를 위해 표형 데이터 집합을 팬더 또는 스파크 DataFrame에 로드할 수 있습니다. 데이터 형식의 전체 목록은 에서 [TabularDataSet를](https://aka.ms/tabulardataset-api-reference)만들 수 있습니다.

+ [FileDataset은](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 데이터스토어 또는 공용 URL의 단일 또는 다중 파일을 참조합니다. FileDataset에서 참조하는 파일을 계산 대상에 [다운로드하거나 마운트할](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) 수 있습니다.

추가 데이터 집합 기능은 다음 설명서에서 찾을 수 있습니다.

+ [버전 및 추적](how-to-version-track-datasets.md) 데이터 집합 계보.
+ [데이터 집합을 모니터링하여](how-to-monitor-datasets.md) 데이터 드리프트 감지를 지원합니다.    

## <a name="work-with-your-data"></a>데이터 로 작업

데이터 집합을 사용하면 Azure 기계 학습 기능과의 원활한 통합을 통해 다양한 기계 학습 작업을 수행할 수 있습니다. 

+ 데이터 [레이블 지정 프로젝트를](#label)만듭니다.
+ 기계 학습 모델 교육:
     + [자동화된 ML 실험](how-to-use-automated-ml-for-ml-models.md)
     + [디자이너](tutorial-designer-automobile-price-train-score.md#import-data)
     + [노트북](how-to-train-with-datasets.md)
     + [Azure 기계 학습 파이프라인](how-to-create-your-first-pipeline.md)
+ 기계 학습 파이프라인에서 [일괄 추론을](how-to-use-parallel-run-step.md) 사용하여 점수 매기기 위한 데이터 [집합에 액세스합니다.](how-to-create-your-first-pipeline.md)
+ 데이터 드리프트 감지를 위해 데이터 집합 모니터를 [설정합니다.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>데이터 라벨링

많은 양의 데이터에 레이블을 지정하는 것은 기계 학습 프로젝트에서 골칫거리인 경우가 많습니다. 이미지 분류 또는 개체 감지와 같은 컴퓨터 비전 구성 요소가 있는 구성 요소는 일반적으로 수천 개의 이미지와 해당 레이블이 필요합니다.

Azure Machine Learning은 레이블 지정 프로젝트를 만들고, 관리하고, 모니터링할 수 있는 중앙 위치를 제공합니다. 레이블 지정 프로젝트를 사용하면 데이터, 레이블 및 팀 멤버를 조정하여 레이블 지정 작업을 더 효율적으로 관리할 수 있습니다. 현재 지원되는 작업은 이미지 분류(다중 레이블 또는 다중 클래스) 및 경계 상자를 사용하는 개체 식별입니다.

데이터 [레이블 지정 프로젝트를](how-to-create-labeling-projects.md)만들고 기계 학습 실험에 사용할 데이터 집합을 출력합니다.

<a name="drift"></a>

## <a name="data-drift"></a>데이터 드리프트

기계 학습의 맥락에서 데이터 드리프트는 모델 성능 저하로 이어지는 모델 입력 데이터의 변화입니다. 이는 시간이 지남에 따라 모델 정확도가 저하되는 가장 큰 이유 중 하나이므로 데이터 드리프트를 모니터링하면 모델 성능 문제를 감지하는 데 도움이 됩니다.

데이터 [집합 모니터 만들기](how-to-monitor-datasets.md) 문서를 참조하여 데이터 집합의 새 데이터에 대한 데이터 드리프트를 감지하고 경고하는 방법에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계 

+ 이러한 단계를 사용하여 Azure 기계 학습 스튜디오 또는 Python SDK로 데이터 집합을 [만듭니다.](how-to-create-register-datasets.md)
+ [샘플 전자 필기장을](https://aka.ms/dataset-tutorial)사용하여 데이터 집합 교육 예제를 사용해 보십시오.
+ 데이터 드리프트 예제는 이 [데이터 드리프트 자습서를](https://aka.ms/datadrift-notebook)참조하십시오.
