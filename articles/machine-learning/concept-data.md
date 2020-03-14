---
title: Azure Machine Learning의 데이터
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 데이터와 상호 작용 하는 방법 및 기계 학습 실험에서 데이터를 활용 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: a2af1e87ce7b17183ae09fb02b2652a04f585e84
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270265"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 액세스

이 문서에서는 Machine Learning 작업에 대 한 Azure Machine Learning의 데이터 관리 및 통합 솔루션에 대해 알아봅니다. 이 문서에서는 [azure storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 및 [azure storage 서비스](https://docs.microsoft.com/azure/storage/common/storage-introduction)를 이미 만들었다고 가정 합니다.

저장소에서 데이터를 사용할 준비가 되 면 다음을 권장 합니다.

1. Azure Machine Learning 데이터 저장소를 만듭니다.
2. 데이터 저장소에서 Azure Machine Learning 데이터 집합을 만듭니다. 
3. 다음 중 하나를 사용 하 여 기계 학습 실험에서 해당 데이터 집합을 사용 합니다. 
    1. 모델 학습을 위해 실험의 계산 대상에 탑재

        **OR** 

    1. 자동화 된 기계 학습 (자동화 된 기계 학습) 실험 실행, 기계 학습 파이프라인 및 [Azure Machine Learning 디자이너](concept-designer.md)와 같은 Azure Machine Learning 솔루션에서 직접 사용 합니다.
4. 데이터 드리프트를 검색할 모델 출력 데이터 집합에 대 한 데이터 집합 모니터를 만듭니다. 
5. 데이터 드리프트가 검색 되 면 입력 데이터 집합을 업데이트 하 고 모델을 적절 하 게 다시 학습.

다음 다이어그램에서는이 권장 데이터 액세스 워크플로의 시각적 데모를 제공 합니다.

![데이터 개념-다이어그램](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>저장소의 데이터 액세스

저장소 계정의 데이터에 액세스 하기 위해 Azure Machine Learning는 데이터 저장소 및 데이터 집합을 제공 합니다. Datastores에서 질문에 대 한 대답: 내 데이터에 안전 하 게 연결 하려면 어떻게 해야 하나요? Azure Storage? Datastores는 저장소 서비스에 대 한 추상화 계층을 제공 합니다. 이를 통해 연결 정보는 데이터 저장소에 저장 되 고 스크립트에는 노출 되지 않으므로 보안을 강화 하 고 저장소에 쉽게 액세스할 수 있습니다. 

데이터 집합 질문에 대 한 대답: 내 데이터 저장소에서 특정 데이터 파일을 가져오려면 어떻게 하나요? 데이터 집합은 machine learning 실험에 사용 하려는 기본 저장소의 특정 파일을 가리킵니다. 데이터 저장소와 데이터 집합은 모두 machine learning 작업을 위한 안전 하 고 확장 가능 하며 재현 가능한 데이터 배달 워크플로를 제공 합니다.

### <a name="datastores"></a>데이터 저장소

Azure Machine Learning 데이터 저장소는 Azure storage 서비스에 대 한 저장소 추상화입니다. Azure storage 계정에 쉽게 연결 하 고 기본 Azure storage 서비스의 데이터에 액세스 하는 데이터 [저장소를 등록 하 고 만듭니다](how-to-access-data.md) .

Datastores로 등록할 수 있는 지원 되는 Azure storage 서비스:
+ Azure Blob 컨테이너
+ Azure 파일 공유
+ Azure 데이터 레이크
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks 파일 시스템
+ Azure Database for MySQL

### <a name="datasets"></a>데이터 세트

데이터 저장소의 데이터와 상호 작용 하 [는 Azure Machine Learning 데이터 집합을 만들고](how-to-create-register-datasets.md) , 기계 학습 작업을 위한 사용 가능한 개체에 데이터를 패키지할 수 있습니다. 데이터 집합을 작업 영역에 등록 하 여 데이터 수집 복잡성 없이 다른 실험에서 데이터 집합을 공유 하 고 다시 사용할 수 있습니다.

데이터 집합은 로컬 파일, 공용 url, [Azure Open 데이터 집합](#open)또는 데이터 저장소의 특정 파일에서 만들 수 있습니다. In memory pandas 데이터 프레임에서 데이터 집합을 만들려면 csv와 같은 로컬 파일에 데이터를 작성 하 고 해당 파일에서 데이터 집합을 만듭니다. 데이터 집합은 데이터의 복사본이 아니지만 저장소 서비스의 데이터를 가리키는 참조 이므로 추가 저장소 비용이 발생 하지 않습니다. 

다음 다이어그램에서는 Azure storage 서비스가 없는 경우 로컬 파일, 공용 url 또는 Azure Open 데이터 집합에서 직접 데이터 집합을 만들 수 있음을 보여 줍니다. 이렇게 하면 데이터 집합이 실험의 [Azure Machine Learning 작업 영역](concept-workspace.md)을 사용 하 여 자동으로 생성 된 기본 데이터 저장소에 연결 됩니다.

![데이터 개념-다이어그램](./media/concept-data/dataset-workflow.svg)

다음 설명서에서 추가 데이터 집합 기능을 찾을 수 있습니다.

+ [버전 및 추적](how-to-version-track-datasets.md) 데이터 집합 계보.
+ 데이터 드리프트 검색에 도움이 되도록 데이터 [집합을 모니터링](how-to-monitor-datasets.md) 합니다.
+  두 가지 유형의 데이터 집합에 대 한 설명서는 다음을 참조 하세요.
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 는 제공 된 파일 또는 파일 목록을 구문 분석 하 여 테이블 형식으로 데이터를 나타냅니다. 이를 통해 추가 조작 및 정리를 위해 데이터를 Pandas 또는 Spark 데이터 프레임으로 구체화할 수 있습니다. TabularDatasets에서 만들 수 있는 파일의 전체 목록은 [TabularDatasetFactory 클래스](https://aka.ms/tabulardataset-api-reference)를 참조 하세요.

    + [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 은 데이터 저장소 또는 public url의 단일 또는 여러 파일을 참조 합니다. 이 방법에서는 선택한 파일을 계산 대상에 FileDataset 개체로 다운로드 하거나 탑재할 수 있습니다.

## <a name="work-with-your-data"></a>데이터 작업

데이터 집합을 사용 하면 Azure Machine Learning 기능과의 원활한 통합을 통해 많은 기계 학습 작업을 수행할 수 있습니다. 

+ [기계 학습 모델을 학습](how-to-train-with-datasets.md)합니다.
+ 에서 데이터 집합 사용 
     + [자동 ML 실험](how-to-use-automated-ml-for-ml-models.md)
     + [디자이너](tutorial-designer-automobile-price-train-score.md#import-data) 
+ [기계 학습 파이프라인](how-to-create-your-first-pipeline.md)에서 일괄 처리 유추를 사용 하 여 점수 매기기를 위한 데이터 집합에 액세스 합니다.
+ [데이터 레이블 지정 프로젝트](#label)를 만듭니다.
+ [데이터 드리프트](#drift) 검색을 위한 데이터 집합 모니터를 설정 합니다.

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open Datasets

[Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. 열려 있는 데이터 집합은 Microsoft Azure의 클라우드에 있고 Azure Machine Learning에 통합 되어 있습니다. API를 통해 데이터 세트에 액세스하고 Power BI나 Azure Data Factory 같은 다른 제품에서 데이터 세트를 사용할 수도 있습니다.

Azure Open 데이터 집합은 기계 학습 모델을 학습 하 고 예측 솔루션을 보강 하는 데 도움이 되는 날씨, 인구 조사, 휴일, 공공 안전 및 위치에 대 한 공용 도메인 데이터를 포함 합니다. 또한 공개 데이터 세트를 Azure Open Datasets에 공유할 수 있습니다.

<a name="label"></a>

## <a name="data-labeling"></a>데이터 레이블 지정

많은 양의 데이터에 레이블을 지정하는 것은 기계 학습 프로젝트에서 골칫거리인 경우가 많습니다. 이미지 분류 또는 개체 검색과 같은 컴퓨터 비전 구성 요소가 있는 컴퓨터에는 일반적으로 수천 개의 이미지와 해당 레이블이 필요 합니다.

Azure Machine Learning은 레이블 지정 프로젝트를 만들고, 관리하고, 모니터링할 수 있는 중앙 위치를 제공합니다. 레이블 지정 프로젝트를 사용하면 데이터, 레이블 및 팀 멤버를 조정하여 레이블 지정 작업을 더 효율적으로 관리할 수 있습니다. 현재 지원되는 작업은 이미지 분류(다중 레이블 또는 다중 클래스) 및 경계 상자를 사용하는 개체 식별입니다.

+ [데이터 레이블 지정 프로젝트](how-to-create-labeling-projects.md)를 만들고 기계 학습 실험에서 사용할 데이터 집합을 출력 합니다.

<a name="drift"></a>

## <a name="data-drift"></a>데이터 드리프트

기계 학습의 컨텍스트에서 데이터 드리프트는 모델의 성능 저하를 초래 하는 모델 입력 데이터의 변경 내용입니다. 모델 정확도가 시간에 따라 저하 되는 가장 중요 한 이유 중 하나 이므로 데이터 드리프트를 모니터링 하면 모델 성능 문제를 감지 하는 데 도움이 됩니다.
데이터 집합의 새 데이터에 대 한 데이터 드리프트를 검색 하 고 경고 하는 방법에 대 한 자세한 내용은 데이터 [집합 모니터 만들기](how-to-monitor-datasets.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계 

+ Azure Machine Learning studio 또는 Python SDK를 사용 하 여 데이터 집합을 만들려면 [다음 단계를 사용 합니다.](how-to-create-register-datasets.md)
+ [샘플 노트북](https://aka.ms/dataset-tutorial)을 사용 하 여 데이터 집합 학습 예제를 사용해 보세요.
+ 데이터 드리프트 예제는이 [데이터 드리프트 자습서](https://aka.ms/datadrift-notebook)를 참조 하세요.
