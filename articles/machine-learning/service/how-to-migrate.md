---
title: Workbench에서 마이그레이션
titleSuffix: Azure Machine Learning service
description: 이전 버전에서 최신 버전의 Azure Machine Learning 서비스로 업그레이드하거나 마이그레이션하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 22e7e513c7ec015b070ae37c1dbdd168404ee768
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140754"
---
# <a name="migrate-from-workbench-to-the-latest-version-of-azure-machine-learning-service"></a>Workbench에서 최신 버전의 Azure Machine Learning 서비스로 마이그레이션 

**Workbench 애플리케이션을 설치했거나 실험 및 모델 관리 미리 보기 계정이 있는 경우 이 문서의 내용을 참조하여 최신 버전으로 마이그레이션하세요.**  미리 보기 Workbench를 설치하지 않았거나 실험 및/또는 모델 관리 계정이 없으면 마이그레이션을 수행할 필요가 없습니다.

## <a name="what-can-i-migrate"></a>마이그레이션 가능한 항목
Azure Machine Learning 서비스의 첫 번째 미리 보기에서 만든 대부분의 아티팩트는 사용자의 로컬 또는 클라우드 저장소에 저장됩니다. 이러한 아티팩트는 그대로 유지됩니다. 마이그레이션하려면 업데이트된 Azure Machine Learning 서비스에 아티팩트를 다시 등록합니다. 

아래 표와 문서에서는 최신 버전의 Azure Machine Learning 서비스로 이전하기 전이나 이전한 후에 기존 자산과 리소스에 대해 수행할 수 있는 작업에 대해 설명합니다. 일정 기간 동안 이전 버전과 자산을 계속 사용할 수도 있습니다([전환 지원 일정 참조](overview-what-happened-to-workbench.md#timeline)).

|이전 버전의 자산 또는 리소스|마이그레이션 가능 여부|작업|
|-----------------|:-------------:|-------------|
|Machine Learning 모델(로컬 파일로)|yes|없음. 이전과 동일하게 작동합니다.|
|모델 종속성 및 스키마(로컬 파일로)|yes|없음. 이전과 동일하게 작동합니다.|
|프로젝트|yes|[새 작업 영역에 로컬 폴더를 연결](#projects)합니다.|
|실행 기록|아니요|당분간 [다운로드 가능](#history)합니다.|
|데이터 준비 파일|아니요|새로운 Azure Machine Learning Data Prep SDK 또는 Azure Databricks를 사용하여 모델링할 [원하는 크기의 데이터 집합을 준비](#dataprep)합니다.|
|계산 대상|아니요|새 작업 영역에서 등록합니다.|
|등록된 모델|아니요|새 작업 영역에서 모델을 다시 등록합니다.|
|등록된 매니페스트|아니요|없음. 매니페스트는 새 작업 영역에 더 이상 개념으로 포함되어 있지 않습니다.|
|등록된 이미지|아니요|새 작업 영역에서 배포 Docker 이미지를 다시 만듭니다.|
|배포된 웹 서비스|아니요|없음. 그대로 계속 작동합니다. <br/>또는 [최신 버전을 사용하여 다시 배포](#services)합니다.|
|실험 및 <br/>모델 관리 계정|아니요|대신 [작업 영역을 생성](#resources)합니다.|
|Machine learning CLI 및 SDK|아니요|새 작업에는 새 [CLI](reference-azure-machine-learning-cli.md) 및 [SDK](https://aka.ms/aml-sdk)를 사용합니다.|


[이 릴리스의 변경 내용](overview-what-happened-to-workbench.md)에 대해 자세히 알아보세요.

>[!Warning]
>Azure Machine Learning Studio 사용자는 이 문서의 정보를 참조하면 안 됩니다. 이 문서에서는 Workbench(미리 보기) 응용 프로그램을 설치했거나 실험 및 모델 관리 미리 보기 계정이 있는 Azure Machine Learning 서비스 고객을 위한 정보를 제공합니다.

<a name="resources"></a>

## <a name="azure-resources"></a>Azure 리소스

실험 계정, 모델 관리 계정, Machine Learning 계산 환경 등의 리소스는 최신 버전의 Azure Machine Learning 서비스로 마이그레이션할 수 없습니다. 자산이 계속 작동하는 기간은 [지원 일정](overview-what-happened-to-workbench.md#timeline)을 참조하세요.

[Azure Portal](quickstart-get-started.md)에서 Azure Machine Learning 서비스 작업 영역을 만들어 최신 버전 사용을 시작하세요. Portal의 작업 영역 대시보드는 Edge, Chrome 및 Firefox 브라우저에서만 지원됩니다.

최상위 서비스 리소스인 이 새 작업 영역에서는 Azure Machine Learning 서비스의 최신 기능을 모두 사용할 수 있습니다. 이 [작업 영역 및 아키텍처](concept-azure-machine-learning-architecture.md)에 대해 자세히 알아보세요.

<a name="projects"></a>

## <a name="projects"></a>프로젝트

최신 릴리스에서는 작업 영역의 프로젝트가 클라우드에 저장되는 것이 아니라 로컬 컴퓨터의 디렉터리로 생성됩니다. [최신 아키텍처](concept-azure-machine-learning-architecture.md) 다이어그램을 살펴보세요. 

파일과 스크립트가 포함된 로컬 디렉터리를 계속 사용하려면 ['experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python 명령에서 디렉터리 이름을 지정하거나 ‘az ml project attach’ CLI 명령을 사용합니다.

예: 
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>배포된 웹 서비스

웹 서비스를 마이그레이션하려면 새 SDK 또는 CLI를 사용하여 새 배포 대상으로 모델을 다시 배포합니다. 원래 점수 매기기 파일, 모델 파일 종속성 파일, 환경 파일 및 스키마 파일은 변경할 필요가 없습니다. 

최신 버전에서 모델은 ACI(Azure Container Instances) 또는 AKS(Azure Kubernetes Service) 클러스터에 웹 서비스로 배포됩니다. 

다음 문서에서 자세한 내용을 알아보세요.
+ [배포 방법 및 위치](how-to-deploy-and-where.md)
+ [자습서: Azure Machine Learning Service를 사용하여 모델 배포](tutorial-deploy-models-with-aml.md)

[이전 CLI 지원이 종료](overview-what-happened-to-workbench.md#timeline)되면 모델 관리 계정을 사용하여 원래 배포했던 웹 서비스를 관리할 수 없습니다. 그러나 ACS(Azure Container Service)가 계속 지원된다면 이러한 웹 서비스는 계속 작동합니다.

<a name="history"></a>

## <a name="run-history-records"></a>실행 기록 레코드

이전 작업 영역 아래의 기존 실행 기록에 항목을 계속 추가할 수는 없지만, 이전 CLI를 사용하여 현재 기록을 내보낼 수는 있습니다. [이전 CLI 지원이 종료](overview-what-happened-to-workbench.md#timeline)되면 이러한 실행 기록을 더 이상 내보낼 수 없습니다.

새 CLI 및 SDK를 사용하여 모델 학습 및 실행 기록 추적을 시작하세요. 해당 방법은 [자습서: Azure Machine Learning 서비스를 사용하여 모델 학습시키기](tutorial-train-models-with-aml.md)에서 확인할 수 있습니다.

이전 CLI를 사용하여 실행 기록을 내보내려면 다음 코드를 실행합니다.

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>데이터 준비 파일
Workbench가 없으면 데이터 준비 파일을 이식할 수 없습니다. 하지만 새 Azure Machine Learning Data Prep SDK 또는 Azure Databricks(큰 데이터 집합의 경우)를 사용하여 모델링할 원하는 크기의 데이터 집합을 계속 준비할 수는 있습니다. [Data Prep SDK를 가져오는 방법을 알아보세요](https://aka.ms/data-prep-sdk).

## <a name="next-steps"></a>다음 단계

최신 버전의 Azure Machine Learning 서비스를 사용하여 작업 영역과 프로젝트를 만들고, 스크립트를 실행하고, 스크립트의 실행 기록을 탐색하는 방법을 설명하는 빠른 시작을 확인하려면 [Azure Machine Learning 시작](quickstart-get-started.md)을 살펴보세요.

이 워크플로에 대해 더 자세히 알아보려면 Azure Machine Learning 서비스를 사용하여 모델을 학습시키고 배포하는 자세한 단계가 포함된 전체 자습서의 과정을 진행해 보세요. 

> [!div class="nextstepaction"]
> [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
