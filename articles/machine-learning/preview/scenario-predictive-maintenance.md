---
title: "예측 유지 관리 실제 시나리오 | Microsoft Docs"
description: "PySpark를 사용한 예측 유지 관리 실제 시나리오"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: dc73c052ad9e0fe12af5042289f304a0e48ae413
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>예측 유지 관리 실제 시나리오.

예정되지 않은 장비 가동 중지 시간은 비즈니스에 큰 영향을 줄 수 있습니다. 따라서 비용 및 예기치 않은 가동 중지 시간을 최소화하여 사용량 및 성능을 최대화하고 현장 장비를 실행 상태로 유지하는 것이 중요합니다. 문제를 사전에 파악하므로 보다 비용 효율적인 방법으로 제한된 유지 관리 리소스를 할당하고 품질 및 공급망 프로세스를 개선할 수 있도록 합니다. 

이 시나리오에서는 상대적으로 [많은 양의 데이터](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)를 사용하여 데이터 수집, 기능 엔지니어링, 모델 구축, 그리고 마지막으로 모델 운영화 및 배포의 주요 단계를 안내합니다. 전체 프로세스의 코드는 PySpark로 작성되었으며 Azure ML Workbench 내의 Jupyter Notebook을 사용하여 구현됩니다. 최상의 모델은 프로덕션 환경에서 실시간 오류 예측을 수행할 수 있도록 Azure Machine Learning 모델 관리를 사용하여 최종적으로 운영화됩니다.   

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

다음은 공개 GitHub 리포지토리에 대한 링크입니다. [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>사용 사례 개요

많은 자산이 필요한 분야의 기업들이 직면한 주요 문제는 기계적 문제에 대한 지연과 관련된 상당한 비용입니다. 대부분의 기업은 이러한 문제가 발생하기 전에 예방하기 위해 이러한 문제가 언제 발생하는지 예측하는 데 관심이 있습니다. 목표는 가동 중지 시간을 줄여서 비용을 줄이고 잠재적으로 안전성을 높이는 것입니다. 예측 유지 관리에 사용되는 일반적인 사용 사례 및 모델링 방법에 대한 자세한 내용은 [예측 유지 관리를 위한 플레이 북](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)을 참조하세요.

이 시나리오는 여러 실제 비즈니스 문제를 통합하여 시나리오에 대한 예측 모델을 구현하는 단계를 제공하기 위해 플레이 북의 아이디어를 활용합니다. 이 예제는 많은 예측 유지 관리 사용 사례에서 관찰된 공통 데이터 요소를 결합합니다.

이 시뮬레이션된 데이터의 비즈니스 문제는 구성 요소 오류로 인한 문제를 예측하는 것입니다. 따라서 비즈니스 질문은 "*구성 요소의 실패로 인해 컴퓨터가 작동 중지될 확률은 얼마인가요*?"입니다. 이 문제는 여러 클래스 분류 문제(컴퓨터당 여러 구성 요소)로 형식이 지정되며 기계 학습 알고리즘이 예측 모델을 만드는 데 사용됩니다. 모델은 기계에서 수집된 기록 데이터에 대해 학습됩니다. 이 시나리오에서 사용자는 Azure Machine Learning Workbench 환경에서 이러한 모델을 구현하는 다양한 단계를 수행합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 계정](https://azure.microsoft.com/en-us/free/)(평가판 사용 가능)
* [빠른 시작 설치 가이드](./quickstart-installation.md)에 따라 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)의 설치 복사본으로 프로그램을 설치하고 작업 공간을 만듭니다.
* Azure Machine Learning 운용화를 사용하려면 로컬 배포 환경과 [모델 관리 계정](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)이 필요합니다.

이 예제는 모든 AML Workbench 계산 컨텍스트에서 실행할 수 있습니다. 하지만 16GB 이상의 메모리로 실행하는 것이 좋습니다. 이 시나리오는 원격 DS4_V2 표준 [Linux(Ubuntu)용 데이터 과학 가상 컴퓨터](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)을 실행하는 Windows 10 컴퓨터에서 빌드되고 테스트되었습니다.

모델 운용화는 이 Azure ML CLI 버전 0.1.0a22를 사용하여 구현되었습니다.

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 "예측 유지 관리"를 입력하고 템플릿을 선택합니다.
5.  **만들기**

## <a name="prepare-the-notebook-server-computation-target"></a>노트북 서버 계산 대상 준비

로컬 컴퓨터에서 실행하려면 AML Workbench `File` 메뉴에서 `Open Command Prompt` 또는 `Open PowerShell CLI`를 선택합니다. CLI 창에서 다음 명령을 실행합니다.

`az ml experiment prepare --target docker --run-configuration docker`

Linux(Ubuntu)용 데이터 과학 가상 컴퓨터에서 실행하는 것이 좋습니다. DSVM이 구성되면 다음 두 가지 명령을 실행합니다.

`az ml computetarget attach --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword] --type remotedocker`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Docker 이미지가 준비되면 AML Workbench 노트북 탭에서 Jupyter 노트북 서버를 열거나 브라우저 기반 서버를 시작하려면 `az ml notebook start`를 실행합니다.

노트북은 Jupyter 환경의 `Code` 디렉터리에 저장됩니다. 첫 번째 (`Code\1_data_ingestion.ipynb`) 노트북부터 순차적으로 노트북을 실행합니다. 각 노트북을 열면 계산 커널을 선택하라는 메시지가 표시됩니다. [Project_Name]_Template [Desired_Connection_Name]을 선택하고 커널 설정을 클릭합니다.

## <a name="data-description"></a>데이터 설명

[시뮬레이트된 데이터](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)는 5개의 쉼표로 구분된 값(.csv) 파일로 구성됩니다. 데이터 집합에 대한 자세한 설명을 보려면 링크를 클릭합니다.

* [컴퓨터](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): 각 컴퓨터를 구분하는 기능입니다. 예를 들어 연식 및 모델이 있습니다.
* [오류](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): 오류 로그에는 기계가 여전히 작동 중일 때 발생하는 줄 바꿈하지 않는 오류가 포함됩니다. 이러한 오류는 향후 실패 이벤트를 예측할 수는 있지만 실패로 간주되지 않습니다. 오류 날짜-시간은 원격 분석 데이터가 시간당 속도로 수집된 이후 가장 가까운 시간으로 반올림됩니다.
* [유지 관리](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): 유지 관리 로그에는 예정된 유지 관리 레코드와 예정되지 않은 유지 관리 레코드가 모두 들어 있습니다. 예된 유지 관리는 구성 요소의 정기 검사에 해당하며약, 예정되지 않은 유지 관리는 기계적 고장 또는 기타 성능 저하로 인해 발생할 수 있습니다. 유지 관리 날짜-시간은 원격 분석 데이터가 시간당 속도로 수집된 이후 가장 가까운 시간으로 반올림됩니다.
* [원격 분석](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): 원격 분석 시계열 데이터는 각 컴퓨터에서 실시간으로 수집한 전압, 회전, 압력 및 진동 센서 측정으로 구성됩니다. 데이터는 한 시간 동안의 평균이 계산되어 원격 분석 로그에 저장됩니다.
* [오류](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): 오류는 유지 관리 로그 내의 구성 요소 교체에 해당합니다. 각 레코드에는 컴퓨터 ID, 구성 요소 유형 및 대체 날짜와 시간이 포함됩니다. 이러한 레코드는 모델이 예측하려고 하는 기계 학습 레이블을 작성하는 데 사용됩니다.

GitHub 리포지토리에서 원시 데이터 집합을 다운로드하고 이 분석을 위해 PySpark 데이터 집합을 만들려면 코드 섹션의 [데이터 수집](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook 시나리오를 참조하세요.

## <a name="scenario-structure"></a>시나리오 구조
이 시나리오의 콘텐츠는 [GitHub 리포지토리](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)에서 확인할 수 있습니다. 

리포지토리에는 데이터 준비부터 몇 가지 모델을 빌드하고 마지막으로 최상의 모델 중 하나를 운영할 때까지의 프로세스를 요약한 [추가 정보](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) 파일이 있습니다. 리포지토리의 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 폴더에서 Jupyter Notebook 4개를 사용할 수 있습니다.   

다음은 단계별 시나리오 워크플로를 설명합니다. 종단 간 시나리오는 PySpark로 작성되었으며 4개의 노트북으로 나뉩니다.

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): 이 노트북은 5개의 입력 .csv 파일을 다운로드하고 예비 데이터 정리 및 시각화를 수행합니다. 노트북은 데이터를 PySpark 형식으로 변환하고 기능 엔지니어링 작업에 사용하도록 결과를 Azure BLOB 컨테이너에 저장합니다.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): 이전 단계에서 정리된 데이터 집합을 사용하여 원격 분석 센서에 대해 지연 및 집계된 기능이 생성되고 오류 수, 구성 요소 대체, 컴퓨터 정보가 원격 분석 데이터에 결합됩니다. 오류 관련 구성 요소 대체는 어떤 구성 요소에 오류가 있는지 설명하는 레이블을 구성하는 데 사용됩니다. 레이블이 지정된 기능 데이터는 모델 작성 작업을 위해 Azure BLOB에 저장됩니다.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): 모델링 노트북은 레이블이 지정된 기능 데이터 집합을 사용하여 데이터를 날짜-시간 스탬프와 함께 train 및 dev 데이터 집합으로 분할합니다. 노트북은 `pyspark.ml.classification` 모델을 사용한 설정 집합 실험입니다. 교육 데이터는 벡터화되고 사용자는 `DecisionTreeClassifier` 또는 `RandomForestClassifier`로 실험하고 하이퍼 매개 변수를 조작하여 최상의 성능 모델을 찾을 수 있습니다. 성능은 dev 데이터 집합에 대한 측정값 통계를 평가하여 결정됩니다. 이러한 통계는 추적을 위해 AML Workbench 실행 시간 화면에 다시 기록됩니다. 노트북은 각 실행의 결과 모델을 Jupyter 노트북 커널을 실행하는 로컬 디스크에 저장합니다. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): 이 노트북은 로컬(Jupyter 노트북 커널) 디스크에 저장된 마지막 모델을 사용하여 모델을 Azure 웹 서비스로 운용화하기 위한 구성 요소를 빌드합니다. 전체 운용 자산은 다른 Azure BLOB 컨테이너에 저장된 `o16n.zip` 파일로 압축됩니다. 압축 파일에는 다음이 포함됩니다.

* `service_schema.json` 배포용 스키마 정의 파일. 
* `pdmscore.py` Azure 웹 서비스에 필요한 init() 및 run() 함수
* `pdmrfull.model` 모델 정의 디렉터리.
    
 노트북은 배포를 위해 운용 자산을 패키징하기 전에 모델 정의를 사용하여 함수를 테스트합니다. 배포 지침은 노트북의 끝에 있습니다.

## <a name="conclusion"></a>결론

이 시나리오는 Azure ML Workbench의 Jupyter Notebook 환경에서 PySpark를 사용하여 종단 간 예측 유지 관리 솔루션을 빌드하는 방법에 대한 개요를 제공합니다. 이 시나리오는 또한 프로덕션 환경에서 실시간 오류 예측을 수행할 수 있도록 Azure Machine Learning 모델 관리를 사용하여 최상의 모델을 쉽게 운영화하고 배포할 수 있는 방법을 안내합니다. 그런 다음 시나리오의 관련 부분을 편집하여 비즈니스 요구에 맞게 조정할 수 있습니다.  

## <a name="references"></a>참조

이 사용 사례는 이전에 여러 플랫폼에서 개발되었습니다.

* [예측 유지 관리 솔루션 템플릿](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R Services를 사용한 예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [예측 유지 관리 모델링 가이드 Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark를 사용한 예측 유지 관리](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>다음 단계

Azure Machine Learning Workbench에는 제품의 추가 기능을 보여주는 많은 다른 예제 시나리오가 있습니다. 