---
title: "예측 유지 관리 실제 시나리오에 대한 딥 러닝 - Azure | Microsoft Docs"
description: "Azure Machine Learning Workbench를 사용하여 예측 유지 관리 딥 러닝에 대한 자습서를 복제하는 방법을 알아봅니다."
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 7d4fe98b5c45767fb06391218e80789fc0c96a3b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>예측 유지 관리 실제 시나리오에 대한 딥 러닝.

딥 러닝은 기계 학습에서 가장 인기 있는 추세 중 하나이며, 다음과 같은 여러 영역에 대한 응용 프로그램이 있습니다.
- 무인 자동차 및 로봇 공학
- 음성 및 이미지 인식
- 재무 예측.

DNN(Deep Neural Network)이라고도 알려진 이러한 방법은 뇌 안에 있는 개별 뉴런(생물학적 신경망)에서 영감을 받았습니다.

예정되지 않은 장비 가동 중지 시간은 비즈니스에 큰 영향을 줄 수 있습니다. 현장 장비를 실행 상태로 유지하여 사용량 및 성능을 최대화하고 비용 및 예기치 않은 가동 중지 시간을 최소화하는 것이 중요합니다. 문제를 사전에 파악하므로 보다 비용 효율적인 방법으로 제한된 유지 관리 리소스를 할당하고 품질 및 공급망 프로세스를 개선할 수 있도록 합니다. 

예측 유지 관리(PM) 전략은 부정적인 컴퓨터 성능을 방지하기 위해 머신 러닝 방법을 통해 장비의 상태를 확인하여 사전에 유지 관리를 수행합니다. PM에서 컴퓨터의 상태를 모니터링하기 위해 시간별로 수집된 데이터는 오류를 예측하는 데 사용할 수 있는 패턴을 찾는 분석입니다. [LSTM(Long Short Term Memory)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) 네트워크는 데이터 시퀀스에서 학습하도록 설계되었기 때문에 이 설정에 유리합니다.

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

다음은 문제 보고서 및 기고물의 공개 GitHub 리포지토리에 대한 링크입니다. [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

## <a name="use-case-overview"></a>사용 사례 개요

이 자습서에서는 시뮬레이션된 항공기 엔진 실행-오류 이벤트의 예를 사용하여 예측 유지 관리 모델링 프로세스를 보여 줍니다. 이 시나리오는 [예측 유지 관리](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)에 설명되어 있습니다.

이 설정의 주된 가정은 수명 주기 동안 점진적으로 성능이 저하되는 엔진입니다. 엔진 센서 측정값에서 성능 저하가 발견될 수 있습니다. PM은 이러한 센서 값과 이전의 오류에서 변경된 사항 간의 관계에 대한 모델링을 시도합니다. 그런 다음, 이 모델은 센서 측정값의 현재 상태에 따라 나중에 엔진이 실패할 시기를 예측할 수 있습니다.

이 시나리오는 센서 값 기록을 사용하여 비행기 엔진의 잔여 수명(RUL)을 예측하는 LSTM 네트워크를 만듭니다. [Tensorflow](https://www.tensorflow.org/) 딥 러닝 프레임워크를 지원하는 [Keras](https://keras.io/)를 계산 엔진으로 사용하는 이 시나리오는 하나의 엔진 집합을 사용하여 LSTM을 교육하고, 보이지 않는 엔진 집합에서 네트워크를 테스트합니다.

## <a name="prerequisites"></a>필수 조건
- [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
- Azure Machine Learning Workbench(만든 작업 영역 포함)
- 모델 운영화용: Azure Machine Learning 운영화(로컬 배포 환경 설정 포함) 및 [Azure Machine Learning 모델 관리 계정](model-management-overview.md)

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.

1. Machine Learning Workbench를 엽니다.
2. **프로젝트** 페이지에서 **+**, **새 프로젝트**를 차례로 선택합니다.
3. **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4. **프로젝트 템플릿 검색** 검색 상자에 "예측 유지 관리"를 입력하고 **예측 유지 관리를 위한 딥 러닝 시나리오** 템플릿을 선택합니다.
5. **만들기** 단추를 클릭합니다.

## <a name="prepare-the-notebook-server-computation-target"></a>노트북 서버 계산 대상 준비

로컬 컴퓨터에서 실행하려면 AML Workbench `File` 메뉴에서 `Open Command Prompt` 또는 `Open PowerShell CLI`를 선택합니다. CLI 인터페이스에서는 `az` 명령을 사용하여 Azure 서비스에 액세스할 수 있습니다. 먼저 다음 명령을 사용하여 Azure 계정에 로그인합니다.

```
az login
``` 

이 명령은 `https:\\aka.ms\devicelogin` URL과 함께 사용할 인증 키를 제공합니다. CLI는 장치 로그인 작업이 반환되고 일부 연결 정보를 제공할 때까지 기다립니다. 그런 다음 로컬 [docker](https://www.docker.com/get-docker)가 설치되어 있는 경우 다음 명령을 사용하여 로컬 계산 환경을 준비합니다.

```
az ml experiment prepare --target docker --run-configuration docker
```

메모리 및 디스크 요구 사항을 충족하기 위해 [Linux(Ubuntu)용 데이터 과학 가상 머신](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)에서 실행하는 것이 좋습니다. DSVM이 구성되면 다음 두 가지 명령으로 원격 docker 환경을 준비합니다.

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

원격 docker 컨테이너에 연결되면 다음을 사용하여 DSVM docker 계산 환경을 준비합니다. 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Docker 계산 환경이 준비되면 AML Workbench 노트북 탭에서 Jupyter 노트북 서버를 열거나 다음을 실행하여 브라우저 기반 서버를 시작합니다. 
```
az ml notebook start
```

예제 노트북은 `Code` 디렉터리에 저장됩니다. 노트북은 첫 번째(`Code\1_data_ingestion.ipynb`) 노트북부터 순차적으로 실행되도록 설정됩니다. 각 노트북을 열면 계산 커널을 선택하라는 메시지가 표시됩니다. 이전에 구성한 DSVM에서 실행할 `[Project_Name]_Template [Connection_Name]` 커널을 선택합니다.

## <a name="data-description"></a>데이터 설명

템플릿은 **PM_train.txt**, **PM_test.txt** 및 **PM_truth.txt** 파일에 세 개의 데이터 집합을 입력으로 사용합니다. 

-  **학습 데이터**: 항공기 엔진 실행-오류 데이터입니다. 학습 데이터(PM_train.txt)는 시간 단위의 *주기*가 있는 여러 다변량 시계열로 구성됩니다. 각 주기마다 21개의 센서 판독 값이 포함됩니다. 

    - 각 시계열은 동일한 유형의 다른 엔진에서 생성됩니다. 각 엔진은 다른 정도의 초기 마모 및 제조 편차로 시작됩니다. 사용자는 이 정보를 알 수 없습니다. 

    - 이 시뮬레이션된 데이터에서 엔진은 각 시계열의 시작에서 정상적으로 작동하는 것으로 가정합니다. 작동 주기 계열 중 특정 시점에서 성능이 저하되기 시작합니다. 성능 저하가 진행되고 값의 크기가 증가합니다. 

    - 미리 정의된 임계값에 도달하면 엔진은 추가 작동에 안전하지 않은 것으로 간주됩니다. 각 시계열의 마지막 주기는 해당 엔진의 실패 지점입니다.

-   **테스트 데이터**: 실패 이벤트가 기록되지 않은 항공기 엔진 작동 데이터입니다. 테스트 데이터(PM_test.txt)에는 학습 데이터와 동일한 데이터 스키마가 있습니다. 유일한 차이점은 데이터에서 실패가 발생한 시기를 나타내지 않는다는 것입니다(마지막 기간은 실패 지점을 *나타내지 않음*). 이 엔진이 실패하기 전에 지속될 수 있는 주기의 수는 알 수 없습니다.

- **실제 데이터**: 테스트 데이터의 각 엔진에 대한 실제 잔여 주기 정보입니다. 실제 데이터는 테스트 데이터의 엔진에 대한 잔여 작동 주기의 수를 제공합니다.

## <a name="scenario-structure"></a>시나리오 구조

시나리오 워크플로는 세 가지 단계로 구분되며, 각각 Jupyter 노트북에서 실행됩니다. 각 노트북은 다음과 같은 노트북에서 사용하기 위해 로컬에 유지되는 데이터 아티팩트를 생성합니다. 

### <a name="task-1-data-ingestion-and-preparation"></a>작업 1: 데이터 수집 및 준비

`Code/1_data_ingestion_and_preparation.ipnyb`의 데이터 수집 Jupyter 노트북은 세 개의 입력 데이터 집합을 Pandas 데이터 프레임 형식으로 로드합니다. 다음으로 모델링을 위한 데이터를 준비하고 일부 예비 데이터의 시각화를 수행합니다. 데이터 집합은 모델 빌드 노트북에서 사용하기 위해 계산 컨텍스트에 로컬로 저장됩니다.

### <a name="task-2-model-building-and-evaluation"></a>작업 2: 모델 작성 및 평가

`Code/2_model_building_and_evaluation.ipnyb`의 모델 빌드 Jupyter 노트북은 디스크에서 학습 및 테스트 데이터 집합을 읽고 학습 데이터 집합에 LSTM 네트워크를 빌드합니다. 모델 성능은 테스트 집합에서 측정됩니다. 결과 모델은 직렬화되고 로컬 계산 컨텍스트에 저장되어 운영화 작업에서 사용됩니다.

### <a name="task-3-operationalization"></a>작업 3: 운영화

`Code/3_operationalization.ipnyb`의 운영화 Jupyter 노트북은 저장된 모델을 사용하여 Azure에서 호스팅되는 웹 서비스에서 모델을 호출하는 데 필요한 함수와 스키마를 작성합니다. 이 노트북은 함수를 테스트한 후 배포를 위해 Azure Storage 컨테이너에 로드되는 `LSTM_o16n.zip` 파일에 자산을 압축합니다.

`LSTM_o16n.zip` 배포 파일에는 다음과 같은 아티팩트가 포함됩니다.

- `webservices_conda.yaml`은 배포 대상에서 LSTM 모델을 실행하는 데 필요한 python 패키지를 정의합니다.  
- `service_schema.json`은 LSTM 모델에 필요한 데이터 스키마를 정의합니다.     
- `lstmscore.py`는 배포 대상이 새 데이터에 점수를 매기기 위해 실행하는 함수를 정의합니다.    
- `modellstm.json`은 LSTM 아키텍처를 정의합니다. lstmscore.py 함수는 아키텍처와 가중치를 읽어 모델을 초기화합니다.
- `modellstm.h5`는 모델 가중치를 정의합니다.
- `test_service.py` 테스트 데이터 레코드를 사용하여 배포 엔드포인트를 호출하는 테스트 스크립트입니다. 
- `PM_test_files.pkl` `test_service.py` 스크립트는 `PM_test_files.pkl` 파일에서 기록 엔진 데이터를 읽고 LSTM에 대한 웹 서비스 충분 주기를 보내 엔진 실패의 확률을 반환합니다.

노트북은 배포용 운영화 자산을 패키지하기 전에 모델 정의를 사용하여 함수를 테스트합니다. 웹 서비스를 설정하고 테스트하는 방법에 대한 지침은 `Code/3_operationalization.ipnyb` 노트북의 끝 부분에 포함되어 있습니다.

## <a name="conclusion"></a>결론

이 자습서에서는 센서 값을 사용하여 예측을 수행하는 간단한 시나리오를 사용합니다. [예측 유지 관리 모델링 가이드 R 노트북](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)과 같은 고급 예측 유지 관리 시나리오에서는 기록 유지 관리 레코드, 오류 로그 및 컴퓨터 기능과 같은 여러 데이터 원본을 사용할 수 있습니다. 추가 데이터 원본에는 딥 러닝에서 사용되는 다양한 처리가 필요할 수 있습니다.


## <a name="references"></a>참조

다양한 플랫폼에서 사용할 수 있는 예측 유지 관리 사용 사례 예제가 있습니다.

* [예측 유지 관리 솔루션 템플릿](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R Services를 사용한 예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [예측 유지 관리 모델링 가이드 Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark를 사용한 예측 유지 관리](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

* [예측 유지 관리 실제 시나리오](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>다음 단계

Azure Machine Learning Workbench에는 제품의 추가 기능을 보여주는 많은 다른 예제 시나리오가 있습니다. 