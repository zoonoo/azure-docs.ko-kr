---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service에 대해 알려진 문제, 해결 방법 및 문제 해결의 목록을 가져옵니다.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7d1bce7575272b7df185c4e261685d989f49436c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716545"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning 서비스의 알려진 문제 및 문제 해결

이 문서는 Azure Machine Learning 서비스를 사용할 때 발생한 오류 또는 장애를 찾아서 수정하는 데 도움을 줍니다.

## <a name="visual-interface-issues"></a>시각적 인터페이스 문제

Machine learning 서비스 문제에 대 한 시각적 인터페이스입니다.

### <a name="long-compute-preparation-time"></a>긴 계산 준비 시간

계산 후에 새 계산 또는 하기 호출할 생성 시간이 소요 되 면 몇 분 정도 걸릴 수 있습니다. 팀이 최적화를 위해 작업 중입니다.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>데이터 집합만 포함 된 실험을 실행할 수 없습니다. 

데이터 집합을 시각화 하는 데이터 집합만 포함 하는 실험을 실행할 수 있습니다. 그러나 실험을 실행할 수는 없습니다. 현재 데이터 집합만 포함 되어 있습니다. 이 문제를 적극적으로 해결 하 고 있습니다.
 
이 문제를 해결 하기 전에 데이터 집합을 데이터 변환 모듈 (데이터 집합의 열 선택, 메타 데이터 편집, 데이터 분할 등)에 연결 하 고 실험을 실행할 수 있습니다. 그런 다음 데이터 집합을 시각화할 수 있습니다. 

다음 이미지는 방법을 보여 ![줍니다. visulize](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK 설치 문제

**오류 메시지: ‘PyYAML’을 제거할 수 없습니다.**

Python용 Azure Machine Learning SDK: PyYAML은 distutils 설치 프로젝트입니다. 따라서 부분 제거가 있는 경우 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅을 만드는 문제

GA 릴리스 전에 Azure Portal에서 Azure Machine Learning 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 Azure Machine Learning 컴퓨팅을 만들 수 없는 경우는 거의 없습니다. 서비스에 대해 지원 요청을 제기하거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 직접 해제할 수 있습니다.

## <a name="image-building-failure"></a>이미지 빌드 실패

웹 서비스를 배포할 때 이미지 빌드가 실패했습니다. 해결 방법은 이미지 구성을 위한 Conda 파일에 "pynacl==1.2.1"을 pip 종속성으로 추가하는 것입니다.

## <a name="deployment-failure"></a>배포 실패

`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`가 나타나면 배포에 사용한 VM의 SKU를 메모리가 더 많은 SKU로 변경하세요.

## <a name="fpgas"></a>FPGA

요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai 를 작성합니다.

## <a name="automated-machine-learning"></a>자동화된 기계 학습

텐서 Flow 자동화 된 machine learning은 현재 텐서 flow 버전 1.13을 지원 하지 않습니다. 이 버전을 설치 하면 패키지 종속성이 작동 하지 않습니다. 이후 릴리스에서이 문제를 해결 하기 위해 노력 하 고 있습니다. 

### <a name="experiment-charts"></a>실험 차트

자동화 된 ML 실험 반복에 표시 되는 이진 분류 차트 (정밀도-리콜, ROC, 곡선 등)는 4/12부터 사용자 인터페이스에서 렌더링 올바르게 되지 않습니다. 차트 플롯에는 현재 더 낮은 결과가 포함 된 모델을 더 잘 수행 하는 역 결과가 표시 됩니다. 확인 중입니다.

## <a name="databricks"></a>Databricks

Databricks 및 Azure Machine Learning 문제.

### <a name="failure-when-installing-packages"></a>패키지 설치 시 실패

추가 패키지가 설치 되 면 Azure Databricks에서 Azure Machine Learning SDK 설치가 실패 합니다. `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 설치 오류를 방지 하려면 라이브러리 버전을 고정 하 여 패키지를 설치 합니다. 이 문제는 Azure Machine Learning 서비스 SDK가 아닌 Databricks와 관련이 있습니다. 다른 라이브러리 에서도이 문제가 발생할 수 있습니다. 예제:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

또는 Python 라이브러리와의 연결을 유지 하는 경우 init 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원 되지 않습니다. 자세한 내용은 [클러스터 범위 init 스크립트](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)를 참조 하세요.

### <a name="cancel-an-automated-machine-learning-run"></a>자동화 된 machine learning 실행 취소

Azure Databricks에서 자동화 된 기계 학습 기능을 사용 하는 경우 실행을 취소 하 고 새 실험 실행을 시작 하려면 Azure Databricks 클러스터를 다시 시작 합니다.

### <a name="10-iterations-for-automated-machine-learning"></a>자동화 된 machine learning에 대 한 10 회 반복 >

자동화 된 기계 학습 설정에서 10 개 이상의 반복이 있는 경우 실행을 `show_output` 제출할 `False` 때를로 설정 합니다.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Azure Machine Learning SDK/자동화 된 기계 학습에 대 한 위젯

Azure Machine Learning SDK 위젯은 Databricks 노트북에서 HTML 위젯을 구문 분석할 수 없기 때문에 지원 되지 않습니다. Azure Databricks 노트북 셀에서이 Python 코드를 사용 하 여 포털에서 위젯을 볼 수 있습니다.

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>가져오기 오류: 이름이 ' pandas ' 인 모듈이 없습니다.

자동화 된 machine learning을 사용 하는 경우이 오류가 표시 되는 경우:

1. Azure Databricks 클러스터에 두 개의 패키지를 설치 하려면이 명령을 실행 합니다. 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. 클러스터를 분리 했다가 노트북에 다시 연결 합니다. 

이러한 단계를 수행 해도 문제가 해결 되지 않으면 클러스터를 다시 시작 하십시오.

### <a name="failtosendfeather"></a>FailToSendFeather

Azure Databricks 클러스터에서 데이터 `FailToSendFeather` 를 읽는 동안 오류가 표시 되는 경우 다음 해결 방법을 참조 하십시오.

* 패키지 `azureml-sdk[automl_databricks]` 를 최신 버전으로 업그레이드 합니다.
* 버전 `azure-dataprep` 1.1.8 이상을 추가 합니다.
* 버전 `pyarrow` 0.11 이상을 추가 합니다.

## <a name="azure-portal"></a>Azure Portal

SDK 또는 포털의 공유 링크에서 작업 영역을 직접 확인하려는 경우 확장에서 구독 정보가 포함된 일반 개요 페이지를 확인할 수 없습니다. 다른 작업 영역으로 전환할 수 없습니다. 다른 작업 영역을 확인해야 하는 경우 해결 방법은 [Azure Portal](https://portal.azure.com)로 직접 이동하여 작업 영역 이름을 검색하는 것입니다.

## <a name="diagnostic-logs"></a>진단 로그

도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 일부 로그를 보려면 [Azure Portal](https://portal.azure.com) 를 방문 하 고 작업 영역으로 이동한 다음 **작업 영역 > 실험을 선택 하 > > 로그를 실행**합니다.

## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="authentication-errors"></a>인증 오류

원격 작업에서 컴퓨팅 대상에 대한 관리 작업을 수행하는 경우 다음 오류 중 하나가 나타납니다.

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

예를 들어 원격 실행을 위해 제출된 ML 파이프라인에서 컴퓨팅 대상을 만들거나 연결하려고 시도하면 오류가 나타납니다.

## <a name="overloaded-azurefile-storage"></a>오버 로드 된 AzureFile 저장소

"저장소가 오버 로드 되었으므로 AzureFile의 작업 디렉터리에 프로젝트 파일을 업로드할 수 없습니다." 라는 오류가 표시 되 면 다음 해결 방법을 적용 하십시오.

데이터 전송과 같은 다른 작업에 대해 파일 공유를 사용 하는 경우 파일 공유를 사용 하 여 실행을 제출할 수 있도록 blob을 사용 하는 것이 좋습니다. 작업을 서로 다른 두 작업 영역 간에 분할할 수도 있습니다.
