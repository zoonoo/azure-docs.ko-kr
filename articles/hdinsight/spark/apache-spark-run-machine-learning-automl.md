---
title: Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드 실행
description: Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드를 실행하는 방법을 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762554"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드 실행

Azure Machine Learning은 데이터에 대한 예측 분석 솔루션을 빌드, 테스트 및 배포하는 데 사용할 수 있는 공동 끌어서 놓기 도구입니다. Azure Machine Learning은 Excel과 같은 BI 도구 또는 사용자 지정 앱에서 쉽게 사용할 수 있는 웹 서비스로 모델을 게시합니다. AutoML(자동화된 기계 학습)은 지능형 자동화 및 최적화를 사용하여 고품질 기계 학습 모델을 만들 수 있도록 합니다. AutoML은 특정 문제 유형에 사용할 적합한 알고리즘 및 하이퍼 매개 변수를 결정합니다.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 Azure Machine Learning 설치

> [!Note]
> Azure ML 작업 영역은 현재 eastus, eastus2 및 westcentralus 지역에서 사용할 수 있습니다. HDInsight 클러스터도 이러한 지역 중 하나에 만들어야 합니다.

Azure Machine Learning 및 자동화된 기계 학습에 대한 일반 자습서를 보려면 [자습서: Azure Machine Learning Studio에서 첫 번째 데이터 과학 실험 만들기](../../machine-learning/studio/create-experiment.md) 및 [자습서: 자동화된 기계 학습을 사용하여 모델 빌드](../../machine-learning/service/tutorial-auto-train-models.md)를 참조하세요.
Azure HDInsight 클러스터에 AzureML을 설치하려면 HDInsight 3.6 Spark 2.3.0 클러스터(권장)의 헤드 노드 및 작업자 노드에서 스크립트 동작 [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh)을 실행합니다. 이 스크립트 동작은 클러스터 생성 프로세스의 일부로 또는 Azure Portal을 통해 기존 클러스터에서 실행할 수 있습니다.

스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](../hdinsight-hadoop-customize-cluster-linux.md)를 읽어보세요. 이 스크립트는 Azure Machine Learning 패키지 및 종속성을 설치할 뿐만 아니라 샘플 Jupyter 노트를 기본 저장소의 경로 `HdiNotebooks/PySpark`으로 다운로드합니다. 이 Jupyter 노트는 단순한 분류 문제에 자동화된 기계 학습 분류자를 사용하는 방법을 보여 줍니다.

> [!Note]
> Azure Machine Learning 패키지는 Python3 conda 환경에 설치됩니다. 설치된 Jupyter 노트는 PySpark3 커널을 사용하여 실행해야 합니다.

## <a name="authentication-for-workspace"></a>작업 영역 인증

작업 영역을 만들고 실험을 제출하려면 인증 토큰이 필요합니다. 이 토큰은 [Azure AD 애플리케이션](../../active-directory/develop/app-objects-and-service-principals.md)을 사용하여 생성할 수 있습니다. 계정에서 Multi-Factor Authentication을 사용하도록 설정하지 않은 경우 [Azure AD 사용자](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)를 사용하여 필수 인증 토큰을 생성할 수도 있습니다.  

다음 코드 조각은 **Azure AD 애플리케이션**을 사용하여 인증 토큰을 만듭니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
다음 코드 조각은 **Azure AD 사용자**를 사용하여 인증 토큰을 만듭니다.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>데이터 세트 로드

Spark의 자동화된 기계 학습은 데이터에 대해 느리게 평가되는 변경할 수 없는 작업에 해당하는 **데이터 흐름**을 사용합니다.  데이터 흐름은 공용 읽기 액세스를 사용하여 Blob에서 또는 SAS 토큰을 사용하여 Blob URL에서 데이터 세트를 로드할 수 있습니다.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

또한 일회성 등록을 사용하여 작업 영역에 데이터 저장소를 등록할 수 있습니다.

## <a name="experiment-submission"></a>실험 제출

자동화된 기계 학습 구성에서 패키지를 분산 모드로 실행하려면 속성 `spark_context`를 설정해야 합니다. 병렬로 실행되는 최대 반복 횟수를 나타내는 속성 `concurrent_iterations`는 Spark 앱의 실행기 코어 수보다 작은 수로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화된 기계 학습의 목적에 대한 자세한 내용은 [Microsoft의 자동화된 기계 학습을 사용하여 모델 출시 간격 조정](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)을 참조하세요.
* [Microsoft Research의 AutoML 프로젝트](https://www.microsoft.com/research/project/automl/)