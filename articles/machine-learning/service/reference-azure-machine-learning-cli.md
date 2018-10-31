---
title: Azure Machine Learning CLI 확장 정보
description: Azure Machine Learning용 Machine Learning CLI 확장에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 53e737f35904a90bb56ec15c8a8282f8775e3c3a
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393494"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Azure Machine Learning CLI란?

Azure Machine Learning CLI(명령줄 인터페이스) 확장은 Azure Machine Learning 서비스를 사용하는 데이터 과학자와 개발자용 확장입니다. 이 확장을 사용하면 Machine Learning 워크플로를 빠르게 자동화하여 다음과 같은 방식으로 프로덕션 환경에 적용할 수 있습니다.
+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ Machine Learning 모델 패키징, 배포 및 수명 주기 추적

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)의 확장인 이 Machine Learning CLI는 Azure Machine Learning Service용 Python 기반 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>를 토대로 작성되었습니다.

> [!NOTE]
> 이 CLI는 현재 미리 보기 상태이며 업데이트될 예정입니다.

## <a name="installing-and-uninstalling"></a>설치 및 제거

미리 보기 PyPi 인덱스에서 다음 명령을 사용하여 CLI를 설치할 수 있습니다.
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

다음 명령을 사용하면 CLI를 제거할 수 있습니다.
```AzureCLI
az extension remove -n azure-cli-ml
```

위의 **제거** 및 **추가** 단계에 따라 CLI를 업데이트할 수 있습니다.

## <a name="using-the-cli-vs-the-sdk"></a>CLI 사용과 SDK 사용 비교
CLI는 개발 운영 팀 직원이 수행하는 자동화 과정에서 또는 연속 통합/제공 파이프라인의 일부분으로 사용하는 것이 더 효율적입니다. 가끔씩 수행하는 매개 변수가 있는 작업 처리용으로 최적화되어 있기 때문입니다. 

다음은 이러한 템플릿의 예입니다.
- 컴퓨터 프로비전
- 매개 변수가 있는 실험 제출
- 모델 등록, 이미지 만들기
- 서비스 배포

데이터 과학자의 경우 Azure ML SDK를 사용하는 것이 좋습니다.

## <a name="common-machine-learning-cli-commands"></a>일반적인 Machine Learning CLI 명령
> [!NOTE]
> 아래 명령을 성공적으로 실행하기 위해 사용할 수 있는 샘플 파일은 [여기](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)서 찾을 수 있습니다.

다양한 `az ml` 명령을 사용하여 Azure Portal Cloud Shell을 비롯한 모든 명령줄 환경에서 서비스와 상호 작용할 수 있습니다.

일반적인 명령의 샘플은 다음과 같습니다.

### <a name="workspace-creation--compute-setup"></a>작업 영역 만들기 및 컴퓨터 설정

+ Machine Learning을 위한 최상위 리소스인 Azure Machine Learning 서비스 작업 영역을 만듭니다.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ 기본적으로 이 작업 영역을 사용하도록 CLI를 설정합니다.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ DSVM(Data Science VM)을 만듭니다. 분산 학습용 BatchAI 클러스터 또는 배포용 AKS 클러스터를 만들 수도 있습니다.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>실험 제출
+ 실험 제출을 위한 프로젝트(실행 구성)에 연결합니다. 이 프로젝트는 실험 실행을 추적하는 데 사용됩니다.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ 원하는 계산 대상의 Azure Machine Learning 서비스에 대한 실험을 제출합니다. 이 예제에서는 로컬 계산 환경에 대해 실행합니다. conda 환경 파일이 python 종속성을 캡처해야 합니다.

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ 제출된 실험 목록을 확인합니다.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>모델 등록, 이미지 만들기 및 배포

+ Azure Machine Learning에 모델을 등록합니다.
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Machine Learning 모델 및 종속성을 포함할 이미지를 만듭니다. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ 패키지된 모델을 대상에 배포합니다(ACI/ACK 포함).
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>전체 명령 목록
```az ml COMMANDNAME -h```를 실행하면 CLI 확장의 전체 명령 목록과 명령에 지원되는 매개 변수 목록을 확인할 수 있습니다. 
