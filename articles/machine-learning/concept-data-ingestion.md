---
title: 데이터 수집 옵션
titleSuffix: Azure Machine Learning
description: 기계 학습 모델 교육을 위한 데이터 수집 옵션에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086892"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Azure 기계 학습의 데이터 수집

이 문서에서는 Azure 기계 학습에서 사용할 수 있는 다음 데이터 수집 옵션의 장단점을 알아봅니다. 

1. [Azure 데이터 팩터리](#use-azure-data-factory) 파이프라인
2. [Azure 기계 학습 파이썬 SDK](#use-the-python-sdk)

데이터 수집은 하나 또는 여러 소스에서 구조화되지 않은 데이터를 추출한 다음 기계 학습 모델을 학습할 준비가 되는 프로세스입니다. 또한 특히 수동으로 수행하고 여러 원본의 많은 양의 데이터가 있는 경우 시간이 많이 많이 걸리는 경우도 있습니다. 이러한 노력을 자동화하면 리소스가 확보되고 모델이 가장 최신의 적용 가능한 데이터를 사용할 수 있습니다.

Azure 데이터 팩터리(ADF)는 데이터를 추출, 로드 및 변환하도록 특별히 빌드되었지만 Python SDK에서는 기본 데이터 수집 작업에 대한 사용자 지정 코드 솔루션을 개발해 보겠습니다. 둘 다 필요한 것이 아니라면 ADF와 Python SDK를 함께 사용하여 필요에 맞는 전반적인 데이터 수집 워크플로를 만들 수도 있습니다. 

## <a name="use-azure-data-factory"></a>Azure Data Factory 사용

[Azure Data Factory는](https://docs.microsoft.com/azure/data-factory/introduction) 데이터 원본 모니터링 및 데이터 수집 파이프라인에 대한 트리거에 대한 기본 지원을 제공합니다.  

다음 표에는 데이터 수집 워크플로에 Azure Data Factory를 사용하는 장단점이 요약되어 있습니다.

|장점|단점
---|---
데이터를 추출, 로드 및 변환하도록 특별히 구축되었습니다.|현재 제한된 Azure 데이터 팩터리 파이프라인 작업 집합을 제공합니다. 
대규모로 데이터 이동 및 변환을 오케스트레이션하기 위한 데이터 기반 워크플로를 만들 수 있습니다.|구성 및 유지 보수 비용이 많이 듭니다. 자세한 내용은 Azure 데이터 팩터리의 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 참조하십시오.
[Azure 데이터 브릭](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) 및 Azure [함수와](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) 같은 다양한 Azure 도구와 통합 | 기본적으로 스크립트를 실행 하지 않습니다., 대신 스크립트 실행에 대 한 별도 계산에 의존 
기본적으로 데이터 원본 트리거데이터 수집지원| 
데이터 준비 및 모델 학습 프로세스는 별개입니다.|
Azure 데이터 팩터리 데이터 흐름에 대한 임베디드 데이터 계보 기능|
비 스크립팅 접근 방식에 대한 낮은 코드 환경 [사용자 인터페이스](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) 제공 |

이러한 단계와 다음 다이어그램은 Azure Data Factory의 데이터 수집 워크플로를 보여 줍니다.

1. 소스에서 데이터 가져옵니다.
1. Azure 기계 학습을 위한 데이터 저장소 역할을 하는 출력 Blob 컨테이너에 데이터를 변환하고 저장합니다.
1. 준비된 데이터를 저장하면 Azure Data Factory 파이프라인은 모델 학습을 위해 준비된 데이터를 수신하는 교육 기계 학습 파이프라인을 호출합니다.


    ![ADF 데이터 수집](media/concept-data-ingestion/data-ingest-option-one.svg)
    
[Azure 데이터 팩터리에서](how-to-data-ingest-adf.md)기계 학습을 위한 데이터 수집 파이프라인을 빌드하는 방법에 대해 알아봅니다.

## <a name="use-the-python-sdk"></a>Python SDK 사용 

Python [SDK를](https://docs.microsoft.com/python/api/overview/azure/ml)사용하면 [Azure 기계 학습 파이프라인](how-to-create-your-first-pipeline.md) 단계에 데이터 수집 작업을 통합할 수 있습니다.

다음 표는 데이터 수집 작업에 대한 SDK 및 ML 파이프라인 단계를 사용하기 위한 장단점을 요약한 것입니다.

장점| 단점
---|---
나만의 파이썬 스크립트 구성 | 기본적으로 데이터 원본 변경 트리거링을 지원하지 않습니다. 논리 앱 또는 Azure 함수 구현 필요
모든 모델 교육 실행의 일부로 데이터 준비|데이터 수집 스크립트를 만들기 위해 개발 기술이 필요합니다.
[Azure 기계 학습](concept-compute-target.md#azure-machine-learning-compute-managed) 계산을 비롯한 다양한 계산 대상에 대한 데이터 준비 스크립트 지원 |생성 메커니즘을 만들기 위한 사용자 인터페이스를 제공하지 않습니다.

다음 다이어그램에서 Azure 기계 학습 파이프라인은 데이터 수집 및 모델 학습의 두 단계로 구성됩니다. 데이터 수집 단계에는 Python 라이브러리와 Python SDK를 사용하여 로컬/웹 소스에서 데이터 추출과 같은 작업을 수행하고 값 대치 누락과 같은 기본 데이터 변환을 포함합니다. 그런 다음 준비된 데이터를 교육 스크립트에 대한 입력으로 사용하여 기계 학습 모델을 학습합니다. 

![Azure 파이프라인 + SDK 데이터 수집](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 팩터링을](how-to-data-ingest-adf.md) 사용하여 기계 학습을 위한 데이터 수집 파이프라인을 빌드하는 방법 알아보기
* [Azure 파이프라인을](how-to-cicd-data-ingestion.md)사용하여 데이터 수집 파이프라인의 개발 수명 주기를 자동화하고 관리하는 방법에 대해 알아봅니다.
