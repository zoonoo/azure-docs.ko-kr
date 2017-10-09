--- 
title: "예측 유지 관리 실제 시나리오 | Microsoft Docs"
description: "PySpark를 사용한 예측 유지 관리 실제 시나리오"
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>예측 유지 관리 실제 시나리오


예정되지 않은 장비 가동 중지 시간은 비즈니스에 큰 영향을 줄 수 있습니다. 따라서 비용 및 예기치 않은 가동 중지 시간을 최소화하여 사용량 및 성능을 최대화하고 현장 장비를 실행 상태로 유지하는 것이 중요합니다. 문제를 사전에 파악하므로 보다 비용 효율적인 방법으로 제한된 유지 관리 리소스를 할당하고 품질 및 공급망 프로세스를 개선할 수 있도록 합니다. 

이 시나리오에서는 상대적으로 [많은 양의 데이터](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)를 사용하여 데이터 수집, 기능 엔지니어링, 모델 구축, 그리고 마지막으로 모델 운영화 및 배포의 주요 단계를 안내합니다. 전체 프로세스의 코드는 PySpark로 작성되었으며 Azure ML Workbench 내의 Jupyter Notebook을 사용하여 구현됩니다. 최상의 모델은 프로덕션 환경에서 실시간 오류 예측을 수행할 수 있도록 Azure Machine Learning 모델 관리를 사용하여 최종적으로 운영화됩니다.   

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

다음은 공개 GitHub 리포지토리에 대한 링크입니다. [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>사용 사례 개요

많은 자산이 필요한 분야의 기업들이 직면한 주요 문제는 기계적 문제에 대한 지연과 관련된 상당한 비용입니다. 대부분의 기업은 이러한 문제가 발생하기 전에 예방하기 위해 이러한 문제가 언제 발생하는지 예측하는 데 관심이 있습니다. 이렇게 하면 가동 중지 시간을 줄이고 경우에 따라 안전성을 높이면서 비용을 절감할 수 있습니다. 예측 유지 관리를 위한 일반적인 사용 사례 및 모델링 방법에 대한 자세한 내용은 [예측 유지 관리를 위한 플레이 북](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)을 참조하세요.

이 시나리오는 여러 실제 비즈니스 문제를 통합하여 시나리오에 대한 예측 모델을 구현하는 단계를 제공하기 위해 플레이 북의 아이디어를 활용합니다. 이 예제는 많은 예측 유지 관리 사용 사례에서 관찰된 공통 데이터 요소를 결합합니다.

이 시뮬레이션된 데이터의 비즈니스 문제는 구성 요소 오류로 인한 문제를 예측하는 것입니다. 따라서 비즈니스 질문은 "*구성 요소의 실패로 인해 컴퓨터가 작동 중지될 확률은 얼마인가요*?"입니다. 이 문제는 여러 클래스 분류 문제(컴퓨터당 여러 구성 요소)로 형식이 지정되며 기계 학습 알고리즘이 예측 모델을 만드는 데 사용됩니다. 모델은 기계에서 수집된 기록 데이터에 대해 학습됩니다. 이 시나리오에서 사용자는 Azure Machine Learning Workbench 환경에서 이러한 모델을 구현하는 다양한 단계를 수행합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 계정](https://azure.microsoft.com/en-us/free/)(평가판 사용 가능)
* [빠른 시작 설치 가이드](./quickstart-installation.md)에 따라 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)의 설치 복사본으로 프로그램을 설치하고 작업 공간을 만듭니다.
* 이 시나리오에서는 Jupyter Notebook에서 사용되는 중간 결과가 Azure Blob Storage 컨테이너에 저장됩니다. Azure Storage 계정 설정에 대한 지침은 이 [링크](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage)를 참조하세요. 
* 모델 [운영화](https://github.com/Azure/Machine-Learning-Operationalization)의 경우 사용자가 로컬로 설치되고 실행되는 [Docker 엔진](https://www.docker.com/)을 실행하는 것이 가장 좋습니다. 그러지 않은 경우 클러스터 옵션을 사용할 수 있지만 [ACS(Azure Container Service)](https://azure.microsoft.com/en-us/services/container-service/)를 실행하는 데 비용이 많이 들 수 있습니다.
* 이 시나리오에서는 사용자가 Docker 엔진이 로컬로 설치된 Windows 10 컴퓨터에서 Azure ML Workbench를 실행 중이라고 가정합니다. 
* 이 시나리오는 Intel Core i7-4600U CPU, 2.10GHz, 8GB RAM, 64비트 OS, Docker 버전 17.06.0-ce-win19가 있는 x64 기반 프로세서(12801) 사양의 Windows 10 컴퓨터에서 작성 및 테스트되었습니다. 
* 모델 운영화는 이 Azure ML CLI 버전(azure-cli-ml == 0.1.0a22)을 사용하여 수행되었습니다.

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 "예측 유지 관리"를 입력하고 템플릿을 선택합니다.
5.  **만들기**

## <a name="data-description"></a>데이터 설명

[시뮬레이트된 데이터](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)는 5개의 쉼표로 구분된 값(.csv) 파일로 구성됩니다. 

* [컴퓨터](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): 각 컴퓨터를 구분하는 기능입니다. 예를 들어 연식 및 모델이 있습니다.
* [오류](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): 오류 로그에는 기계가 여전히 작동 중일 때 발생하는 줄 바꿈하지 않는 오류가 포함됩니다. 이러한 오류는 향후 실패 이벤트를 예측할 수는 있지만 실패로 간주되지 않습니다. 오류 날짜-시간은 원격 분석 데이터가 시간당 속도로 수집된 이후 가장 가까운 시간으로 반올림됩니다.
* [유지 관리](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): 유지 관리 로그에는 예정된 유지 관리 레코드와 예정되지 않은 유지 관리 레코드가 모두 들어 있습니다. 예된 유지 관리는 구성 요소의 정기 검사에 해당하며약, 예정되지 않은 유지 관리는 기계적 고장 또는 기타 성능 저하로 인해 발생할 수 있습니다. 유지 관리 날짜-시간은 원격 분석 데이터가 시간당 속도로 수집된 이후 가장 가까운 시간으로 반올림됩니다.
* [원격 분석](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): 원격 분석 시계열 데이터는 각 컴퓨터에서 실시간으로 수집한 전압, 회전, 압력 및 진동 센서 측정으로 구성됩니다. 데이터는 한 시간 동안의 평균이 계산되어 원격 분석 로그에 저장됩니다.
* [오류](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): 오류는 유지 관리 로그 내의 구성 요소 교체에 해당합니다. 각 레코드에는 컴퓨터 ID, 구성 요소 유형 및 대체 날짜와 시간이 포함됩니다. 이러한 레코드는 모델이 예측하려고 하는 기계 학습 레이블을 작성하는 데 사용됩니다.

GitHub 리포지토리에서 원시 데이터 집합을 다운로드하고 이 분석을 위해 PySpark 데이터 집합을 만들려면 코드 섹션의 [데이터 수집](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook 시나리오를 참조하세요.

## <a name="scenario-structure"></a>시나리오 구조
이 시나리오의 콘텐츠는 [GitHub 리포지토리](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)에서 확인할 수 있습니다. 

리포지토리에는 데이터 준비부터 몇 가지 모델을 빌드하고 마지막으로 최상의 모델 중 하나를 운영할 때까지의 프로세스를 요약한 [추가 정보](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) 파일이 있습니다. 리포지토리의 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 폴더에서 Jupyter Notebook 4개를 사용할 수 있습니다.   

다음은 단계별 시나리오 워크플로를 설명합니다. 종단 간 시나리오는 PySpark로 작성되었으며 아래에 설명된 것처럼 4개의 Notebook으로 나뉩니다.

* [데이터 수집](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): 이 Notebook은 다섯 개 입력 .csv 파일의 데이터 수집을 처리하고, 일부 예비 정리를 수행하며, 데이터 다운로드를 확인하기 위한 일부 요약 그래픽을 만들고, 다음 Notebook에 사용하기 위해 최종 결과 데이터 집합을 Azure Blob 컨테이너에 저장합니다.

* [기능 엔지니어링](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): 이전 단계에서 정리된 데이터 집합을 사용하여 원격 분석 센서에 대한 지연 기능이 만들어지며 마지막으로 교체한 후 일과 같은 변수를 생성하는 추가 기능 엔지니어링이 만들어집니다. 마지막으로, 오류는 관련 레코드에 태그로 지정되어 최종 데이터 집합을 만들고 이는 다음 단계를 위해 Azure Blob에 저장됩니다. 

* [모델 빌드](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): 최종 기능 엔지니어링 데이터 집합의 학습과 날짜-시간 스탬프를 기반으로 하는 테스트 데이터 집합으로 나뉩니다. 그런 다음 임의 포리스트 분류자와 의사 결정 트리 분류자의 두 모델이 학습 데이터 집합에 빌드된 다음 테스트 데이터 집합에 대해 점수가 매겨집니다. 

* [모델 운영화 및 배포](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): 이전 단계에서 빌드된 최상의 모델은 배포를 위해 관련 .json 구성표 파일과 함께 .model 파일로 저장됩니다. init() 및 run() 함수는 프로덕션 환경에서 오류 예측을 수행할 수 있도록 Azure Machine Learning 모델 관리 환경을 사용하여 모델을 운영화하기 전에 로컬에서 먼저 테스트를 거칩니다.  

## <a name="conclusion"></a>결론

이 시나리오는 Azure ML Workbench의 Jupyter Notebook 환경에서 PySpark를 사용하여 종단 간 예측 유지 관리 솔루션을 빌드하는 방법에 대한 개요를 제공합니다. 이 시나리오는 또한 프로덕션 환경에서 실시간 오류 예측을 수행할 수 있도록 Azure Machine Learning 모델 관리를 사용하여 최상의 모델을 쉽게 운영화하고 배포할 수 있는 방법을 안내합니다. 그런 다음 시나리오의 관련 부분을 편집하여 비즈니스 요구에 맞게 조정할 수 있습니다.  

## <a name="references"></a>참조

이 사용 사례는 아래 나열된 것처럼 여러 플랫폼에서 개발되었습니다.

* [예측 유지 관리 솔루션 템플릿](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R Services를 사용한 예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [예측 유지 관리 모델링 가이드 Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark를 사용한 예측 유지 관리](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



