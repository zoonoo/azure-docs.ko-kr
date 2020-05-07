---
title: 데이터 수집 & 자동화
titleSuffix: Azure Machine Learning
description: 기계 학습 모델을 학습 하기 위한 데이터 수집 옵션에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 475c4fd6b34996c83035c4f7ef93b9fa02ded11f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789864"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Azure Machine Learning 워크플로에 대 한 데이터 수집 옵션

이 문서에서는 Azure Machine Learning에서 사용할 수 있는 데이터 수집 옵션의 장단점에 대해 알아봅니다. 

다음 중에서 선택합니다.
+ 데이터를 추출, 로드 및 변환 하기 위해 특별히 빌드된 [Azure Data Factory](#azure-data-factory) 파이프라인

+ [PYTHON SDK를 Azure Machine Learning](#azure-machine-learning-python-sdk)하 여 기본 데이터 수집 작업에 대 한 사용자 지정 코드 솔루션을 제공 합니다.

+ 둘 다의 조합입니다.

데이터 수집은 구조화 되지 않은 데이터가 하나 이상의 원본에서 추출 된 후 기계 학습 모델 학습을 준비 하는 프로세스입니다. 특히 수동으로 수행 하는 경우와 여러 원본에서 많은 양의 데이터가 있는 경우 시간이 많이 소요 됩니다. 이러한 작업을 자동화 하면 리소스를 확보 하 고 모델에서 가장 최근의 데이터와 적용 가능한 데이터를 사용할 수 있습니다.

## <a name="azure-data-factory"></a>Azure 데이터 팩터리

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) 은 데이터 수집 파이프라인에 대 한 데이터 원본 모니터링 및 트리거에 대 한 기본 지원을 제공 합니다.  

다음 표에서는 데이터 수집 워크플로에 Azure Data Factory를 사용 하는 경우의 장단점을 요약 하 여 보여 줍니다.

|장점|단점
---|---
특히 데이터를 추출, 로드 및 변환 하기 위해 작성 되었습니다.|현재는 제한 된 Azure Data Factory 파이프라인 작업 집합을 제공 합니다. 
오케스트레이션 데이터 이동 및 변환을 위한 데이터 기반 워크플로를 만들 수 있습니다.|생성 및 유지 관리 비용이 많이 듭니다. 자세한 내용은 Azure Data Factory의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 를 참조 하세요.
[Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) 및 [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) 와 같은 다양 한 Azure 도구와 통합 | 기본적으로 스크립트를 실행 하지 않고 스크립트 실행을 위한 별도의 계산에 의존 합니다. 
기본적으로 데이터 원본에서 트리거된 데이터 수집을 지원 합니다.| 
데이터 준비 및 모델 학습 프로세스는 별도입니다.|
Azure Data Factory 데이터 흐름에 대 한 포함 된 데이터 계보 기능|
스크립팅이 아닌 방법에 대 한 낮은 코드 환경 [사용자 인터페이스](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) 를 제공 합니다. |

이러한 단계 및 다음 다이어그램에서는 Azure Data Factory의 데이터 수집 워크플로를 보여 줍니다.

1. 원본에서 데이터를 끌어옵니다.
1. 데이터를 변환 하 고 데이터를 위한 데이터 저장소 역할을 하는 출력 blob 컨테이너에 저장 Azure Machine Learning
1. 준비 된 데이터를 저장 하면 Azure Data Factory 파이프라인이 모델 학습을 위해 준비 된 데이터를 수신 하는 학습 Machine Learning 파이프라인을 호출 합니다.


    ![ADF 데이터 수집](media/concept-data-ingestion/data-ingest-option-one.svg)
    
[Azure Data Factory](how-to-data-ingest-adf.md)를 사용 하 여 Machine Learning에 대 한 데이터 수집 파이프라인을 빌드하는 방법에 대해 알아봅니다.

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK 

[PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml)를 사용 하 여 데이터 수집 작업을 [Azure Machine Learning 파이프라인](how-to-create-your-first-pipeline.md) 단계로 통합할 수 있습니다.

다음 표에는 데이터 수집 태스크에 대 한 SDK 및 ML 파이프라인 단계 사용에 대 한 장단점을 요약 하 여 설명 합니다.

장점| 단점
---|---
사용자 고유의 Python 스크립트 구성 | 에서는 기본적으로 트리거되는 데이터 원본 변경을 지원 하지 않습니다. 논리 앱 또는 Azure 함수 구현이 필요 합니다.
모든 모델 학습 실행의 일부로 데이터 준비|데이터 수집 스크립트를 만들기 위한 개발 기술이 필요 합니다.
[Azure Machine Learning 계산](concept-compute-target.md#azure-machine-learning-compute-managed) 을 포함 하 여 다양 한 계산 대상에서 데이터 준비 스크립트를 지원 합니다. |수집 메커니즘을 만들기 위한 사용자 인터페이스를 제공 하지 않습니다.

다음 다이어그램에서 Azure Machine Learning 파이프라인은 데이터 수집 및 모델 학습의 두 단계로 구성 됩니다. 데이터 수집 단계는 Python 라이브러리 및 Python SDK를 사용 하 여 수행할 수 있는 작업 (예: 로컬/웹 원본에서 데이터 추출 및 누락 된 값 대체 같은 기본 데이터 변환)을 포함 합니다. 그런 다음 학습 단계에서는 학습 스크립트에 대 한 입력으로 준비 된 데이터를 사용 하 여 machine learning 모델을 학습 합니다. 

![Azure 파이프라인 + SDK 데이터 수집](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>다음 단계

다음 방법 문서를 따르세요.
* [Azure Data Factory를 사용 하 여 데이터 수집 파이프라인 빌드](how-to-data-ingest-adf.md)

* [Azure Pipelines를 사용 하 여 데이터 수집 파이프라인을 자동화 하 고 관리](how-to-cicd-data-ingestion.md)합니다.
