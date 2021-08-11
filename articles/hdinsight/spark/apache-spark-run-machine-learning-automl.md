---
title: HDInsight의 Apache Spark에서 Azure Machine Learning 워크로드 실행
description: Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드를 실행하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929743"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>HDInsight의 Apache Spark에서 자동화된 기계 학습을 사용하여 Azure Machine Learning 워크로드 실행

Azure Machine Learning은 기계 학습 모델의 빌드, 학습 및 배포를 간소화하고 가속화합니다. 자동화된 기계 학습(AutoML)에서 정의된 대상 기능이 있는 학습 데이터로 시작한 후 알고리즘 및 기능 선택의 조합을 반복하여 학습 점수를 기준으로 데이터에 가장 적합한 모델을 자동으로 선택합니다. HDInsight를 통해 고객은 노드가 수백 개 있는 클러스터를 프로비전할 수 있습니다. HDInsight 클러스터의 Spark에서 실행되는 AutoML을 통해 사용자는 이러한 노드에서 컴퓨팅 용량을 이용하여 스케일 아웃 방식으로 학습 작업을 실행하고, 여러 학습 작업을 병렬로 실행할 수 있습니다. 이렇게 하면 사용자가 다른 빅 데이터 워크로드와 컴퓨팅을 공유하면서 AutoML 실험을 실행할 수 있습니다.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 Azure Machine Learning 설치

자동화된 기계 학습에 대한 일반적인 자습서는 [자습서: 자동화된 기계 학습을 사용하여 회귀 모델 빌드](../../machine-learning/tutorial-auto-train-models.md)를 참조하세요.
모든 새 HDInsight-Spark 클러스터는 AzureML-AutoML SDK와 함께 사전 설치됩니다.

> [!Note]
> Azure Machine Learning 패키지는 Python3 conda 환경에 설치됩니다. 설치된 Jupyter Notebook은 PySpark3 커널을 사용하여 실행해야 합니다.

Zeppelin Notebook을 사용해도 AutoML을 사용할 수 있습니다.

> [!Note]
> Zeppelin에는 PySpark3에서 올바른 버전의 Python을 선택하지 않는 [알려진 문제](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html)가 있습니다. 문서화된 해결 방법을 사용하세요.

## <a name="authentication-for-workspace"></a>작업 영역 인증

작업 영역을 만들고 실험을 제출하려면 인증 토큰이 필요합니다. 이 토큰은 [Azure AD 애플리케이션](../../active-directory/develop/app-objects-and-service-principals.md)을 사용하여 생성할 수 있습니다. 계정에서 Multi-Factor Authentication을 사용하도록 설정하지 않은 경우 [Azure AD 사용자](/azure/python/python-sdk-azure-authenticate)를 사용하여 필수 인증 토큰을 생성할 수도 있습니다.  

다음 코드 조각은 **Azure AD 애플리케이션** 을 사용하여 인증 토큰을 만듭니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

다음 코드 조각은 **Azure AD 사용자** 를 사용하여 인증 토큰을 만듭니다.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>데이터 세트 로드

Spark의 자동화된 기계 학습은 데이터에 대해 느리게 평가되는 변경할 수 없는 작업에 해당하는 **데이터 흐름** 을 사용합니다.  데이터 흐름은 공용 읽기 액세스를 사용하여 Blob에서 또는 SAS 토큰을 사용하여 Blob URL에서 데이터 세트를 로드할 수 있습니다.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

또한 일회성 등록을 사용하여 작업 영역에 데이터 저장소를 등록할 수 있습니다.

## <a name="experiment-submission"></a>실험 제출

[자동화된 기계 학습 구성](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)에서 패키지를 분산 모드로 실행하려면 `spark_context` 속성을 설정해야 합니다. 병렬로 실행되는 최대 반복 횟수를 나타내는 속성 `concurrent_iterations`는 Spark 앱의 실행기 코어 수보다 작은 수로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화된 기계 학습의 목적에 대한 자세한 내용은 [Microsoft의 자동화된 기계 학습을 사용하여 모델 출시 간격 조정](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)을 참조하세요.
* Azure ML 자동화된 ML 기능의 사용 방법에 대한 자세한 내용은 [Azure Machine Learning에 자동화된 새로운 기계 학습 기능](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)을 참조하세요.
* [Microsoft Research의 AutoML 프로젝트](https://www.microsoft.com/research/project/automl/)
