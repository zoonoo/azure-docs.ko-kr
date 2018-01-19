---
title: "예측 유지 관리 실제 시나리오에 대한 딥 러닝 - Azure | Microsoft Docs"
description: "Azure Machine Learning Workbench를 사용하여 예측 유지 관리 딥 러닝에 대한 자습서를 복제하는 방법을 알아봅니다."
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>예측 유지 관리 실제 시나리오에 대한 딥 러닝

딥 러닝은 기계 학습에서 가장 인기 있는 추세 중 하나입니다. 딥 러닝은 무인 자동차, 음성 및 이미지 인식, 로봇 공학 및 금융을 포함한 많은 분야와 응용 프로그램에서 사용됩니다. 딥 러닝은 뇌의 모양(생물학적 신경망)과 기계 학습에서 영감을 얻은 일단의 알고리즘입니다. 인지 과학자는 대개 ANN(인공 신경망)과 같은 딥 러닝을 참조합니다.

또한 예측 유지 관리도 인기가 있습니다. 예측 유지 관리에서 다양한 기술이 장비의 상태를 확인하고 유지 관리를 수행해야 할 시기를 예측하는 데 도움이 되도록 설계되었습니다. 예측 유지 관리의 일반적인 몇 가지 용도로 실패 예측, 실패 진단(근본 원인 분석), 실패 검색, 실패 유형 분류, 실패 후 완화 또는 유지 관리 작업에 대한 권장 사항 등이 있습니다.

예측 유지 관리 시나리오에서 데이터는 시간 지남에 따라 수집되어 장비 상태를 모니터링합니다. 목표는 실패를 예측하고 궁극적으로 예방할 수 있는 패턴을 찾는 것입니다. [LSTM(장단기 기억)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) 네트워크를 사용하는 것은 특히 예측 유지 관리에 매력적인 딥 러닝 방법입니다. LSTM 네트워크는 시퀀스에서 학습하는 데 유용합니다. 시계열 데이터를 사용하여 실패 패턴을 검색하는 데 더 오랜 시간을 다시 살펴볼 수 있습니다.

이 자습서에서는 [예측 유지 관리](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)에서 설명한 데이터 집합 및 시나리오에 대한 LSTM 네트워크를 작성합니다. 여기서는 네트워크를 사용하여 항공기 엔진의 잔여 수명을 예측합니다. 템플릿은 시뮬레이션된 항공기 센서 값을 사용하여 향후에 항공기 엔진이 실패할 시기를 예측합니다. 이 예측을 사용하면 실패를 방지하도록 유지 관리를 미리 계획할 수 있습니다.

이 자습서에서는 [Keras](https://keras.io/) 딥 러닝 라이브러리와 Microsoft Cognitive Toolkit [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras)를 백 엔드로 사용합니다.

이 자습서를 위한 샘플이 있는 공용 GitHub 리포지토리는 [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)에 있습니다.

## <a name="use-case-overview"></a>사용 사례 개요

이 자습서에서는 시뮬레이션된 항공기 엔진 실행-오류 이벤트의 예를 사용하여 예측 유지 관리 모델링 프로세스를 보여 줍니다. 

여기서 설명하는 모델링 데이터의 암시적 가정은 자산에 점진적인 저하 패턴이 있다는 것입니다. 패턴은 자산의 센서 측정값에 반영됩니다. 시간 경과에 따른 자산의 센서 값을 검사하면 기계 학습 알고리즘에서 센서 값, 센서 값의 변화 및 과거의 실패 간의 관계를 학습할 수 있습니다. 이 관계는 나중에 실패를 예측하는 데 사용됩니다. 

샘플 데이터를 사용자 고유의 날짜로 바꾸기 전에 데이터 형식을 검사하고 템플릿의 세 단계를 모두 완료하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
- Azure Machine Learning Workbench(만든 작업 영역 포함)
- 모델 운영화용: Azure Machine Learning 운영화(로컬 배포 환경 설정 포함) 및 [Azure Machine Learning 모델 관리 계정](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.

1. Machine Learning Workbench를 엽니다.
2. **프로젝트** 페이지에서 **+**, **새 프로젝트**를 차례로 선택합니다.
3. **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4. **프로젝트 템플릿 검색** 검색 상자에서 **예측 유지 관리**를 입력한 다음 템플릿을 선택합니다.
5. **만들기**를 선택합니다.

## <a name="prepare-the-notebook-server-computation-target"></a>노트북 서버 계산 대상 준비

로컬 컴퓨터의 Machine Learning Workbench **파일** 메뉴에서 **명령 프롬프트 열기** 또는 **PowerShell 열기**를 선택합니다. 선택한 옵션의 명령 프롬프트 창에서 다음 명령을 실행합니다.

`az ml experiment prepare --target docker --run-configuration docker`

DS4_V2 표준 [Linux용 DSVM(데이터 과학 가상 머신)(Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)에서 노트북 서버를 실행하는 것이 좋습니다. DSVM이 구성되면 다음 명령을 실행하여 Docker 이미지를 준비합니다.

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Docker 이미지가 준비되면 Jupyter 노트북 서버를 엽니다. Jupyter 노트북 서버를 열려면 Machine Learning Workbench **노트북** 탭으로 이동하거나 `az ml notebook start`를 사용하여 브라우저 기반 서버를 시작합니다.

노트북은 Jupyter 환경의 Code 디렉터리에 저장됩니다. Code\1_data_ingestion_and_preparation.ipynb로 시작하는 번호가 매겨진 노트북을 순차적으로 실행합니다.

[project_name]_Template [connection_name]과 일치하는 커널을 선택한 다음 **커널 설정**을 선택합니다.

## <a name="data-description"></a>데이터 설명

템플릿은 PM_train.txt, PM_test.txt 및 PM_truth.txt 파일에 세 개의 데이터 집합을 입력으로 사용합니다.

-  **학습 데이터**: 항공기 엔진 실행-오류 데이터입니다. 학습 데이터(PM_train.txt)는 시간 단위의 *주기*가 있는 여러 다변량 시계열로 구성됩니다. 각 주기마다 21개의 센서 판독 값이 포함됩니다. 

    각 시계열은 동일한 유형의 다른 엔진에서 생성된 것으로 가정할 수 있습니다. 각 엔진은 다른 정도의 초기 마모 및 제조 편차로 시작한다고 가정합니다. 사용자는 이 정보를 알 수 없습니다. 

    이 시뮬레이션된 데이터에서 엔진은 각 시계열의 시작에서 정상적으로 작동하는 것으로 가정합니다. 작동 주기 계열 중 특정 시점에서 성능이 저하되기 시작합니다. 성능 저하가 진행되고 값의 크기가 증가합니다. 

    미리 정의된 임계값에 도달하면 엔진은 추가 작동에 안전하지 않은 것으로 간주됩니다. 각 시계열의 마지막 주기는 해당 엔진의 실패 지점으로 간주될 수 있습니다.

-   **테스트 데이터**: 실패 이벤트가 기록되지 않은 항공기 엔진 작동 데이터입니다. 테스트 데이터(PM_test.txt)에는 학습 데이터와 동일한 데이터 스키마가 있습니다. 유일한 차이점은 데이터에서 실패가 발생한 시기를 나타내지 않는다는 것입니다(마지막 기간은 실패 지점을 *나타내지 않음*). 이 엔진이 실패하기 전에 지속될 수 있는 주기의 수는 알 수 없습니다.

- **실제 데이터**: 테스트 데이터의 각 엔진에 대한 실제 잔여 주기 정보입니다. 실제 데이터는 테스트 데이터의 엔진에 대한 잔여 작동 주기의 수를 제공합니다.

## <a name="scenario-structure"></a>시나리오 구조

시나리오에 대한 콘텐츠는 [GitHub 리포지토리](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)에서 사용할 수 있습니다. 

리포지토리의 [추가 정보](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) 파일은 데이터 준비에서 모델 작성 및 운영에 이르는 프로세스를 간략하게 설명합니다. 세 개의 Jupyter 노트북은 리포지토리의 [Code](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) 폴더에 있습니다. 

다음은 단계별 시나리오 워크플로를 설명합니다.

### <a name="task-1-data-ingestion-and-preparation"></a>작업 1: 데이터 수집 및 준비

Code/1_data_ingestion_and_preparation.ipnyb 데이터 수집 Jupyter Notebook은 세 개의 입력 데이터 집합을 Pandas 데이터 프레임 형식으로 로드합니다. 다음으로 모델링을 위한 데이터를 준비하고 일부 예비 데이터의 시각화를 수행합니다. 그런 다음 데이터는 PySpark 형식으로 변환되고 Azure 구독의 Azure Blob 저장소 컨테이너에 저장되어 다음 모델링 작업에 사용됩니다.

### <a name="task-2-model-building-and-evaluation"></a>작업 2: 모델 작성 및 평가

Code/2_model_building_and_evaluation.ipnyb 모델 작성 Jupyter Notebook은 Blob 저장소에서 PySpark 학습 및 테스트 데이터 집합을 읽습니다. 그런 다음 LSTM 네트워크가 학습 데이터 집합으로 작성됩니다. 모델 성능은 테스트 집합에서 측정됩니다. 결과 모델은 직렬화되고 로컬 계산 컨텍스트에 저장되어 운영화 작업에서 사용됩니다.

### <a name="task-3-operationalization"></a>작업 3: 운영화

Code/3_operationalization.ipnyb 운영화 Jupyter Notebook은 저장된 모델을 사용하여 Azure에서 호스팅되는 웹 서비스에서 모델을 호출하는 데 필요한 함수와 스키마를 작성합니다. 노트북은 함수를 테스트한 다음 운영화 자산을 .zip 파일로 압축합니다.

압축 파일에 포함되는 파일은 다음과 같습니다.

- **modellstm.json**: 배포할 스키마 정의 파일 
- **lstmscore.py**: Azure 웹 서비스에 필요한 **init()** 및 **run()** 함수
- **lstm.model**: 모델 정의 디렉터리

노트북은 배포용 운영화 자산을 패키지하기 전에 모델 정의를 사용하여 함수를 테스트합니다. 배포 지침은 노트북의 끝에 있습니다.


## <a name="conclusion"></a>결론

이 자습서에서는 하나의 데이터 원본(센서 값)만 사용하여 예측을 수행하는 간단한 시나리오를 사용합니다. 더 많은 고급 예측 유지 관리 시나리오(예: [예측 유지 관리 모델링 가이드 R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1))에서 여러 데이터 원본을 사용할 수 있습니다. 다른 데이터 원본에는 과거의 유지 관리 레코드, 오류 로그 및 컴퓨터 및 운영자 기능이 포함될 수 있습니다. 추가 데이터 원본에는 딥 러닝 네트워크에서 사용되는 다양한 유형의 처리가 필요할 수 있습니다. 창 크기와 같이 올바른 매개 변수에 대한 모델을 튜닝하는 것도 중요합니다. 

이 시나리오의 관련 부분을 편집하고, 여러 다른 데이터 원본이 포함된 [예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)에서 설명한 것과 같은 다른 문제 시나리오를 시도할 수 있습니다.


## <a name="references"></a>참조

- [예측 유지 관리 솔루션 템플릿](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [예측 유지 관리 모델링 가이드 Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [PySpark를 사용한 예측 유지 관리](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

