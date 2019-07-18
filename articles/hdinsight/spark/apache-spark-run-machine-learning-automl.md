---
title: Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드 실행
description: Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917049"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Azure HDInsight의 Apache Spark에서 AutoML(자동화된 기계 학습)을 사용하여 Azure Machine Learning 워크로드 실행

Azure Machine Learning 단순화 하 고 빌드, 학습 및 기계 학습 모델의 배포를 가속화 합니다. 자동화 된 컴퓨터 (AutoML) 학습는 정의 된 대상 기능을 제공 하는 학습 데이터를 사용 하 여 시작 하 고 알고리즘 및 자동으로 학습 점수를 기반으로 데이터에 대 한 최상의 모델을 선택 하 고 기능 선택의 조합이 될 때까지 반복 합니다. HDInsight에는 고객이 수백 개의 노드에 클러스터를 프로 비전 할 수 있습니다. AutoML HDInsight 클러스터에서 Spark에서 실행 되는 스케일 아웃 방식으로 교육 작업을 실행 하 고 병렬로 여러 교육 작업을 실행할 수 이러한 노드에서 계산 용량을 사용할 수 있습니다. 이 다른 빅 데이터 워크 로드를 사용 하 여 계산을 공유 하는 동안 AutoML 실험을 실행할 수가 있습니다.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 Azure Machine Learning 설치

자동화 된 기계 학습의 일반 자습서를 참조 하세요. [자습서: 자동화된 기계 학습을 사용하여 모델 빌드](../../machine-learning/service/tutorial-auto-train-models.md)를 참조하세요.
AzureML AutoML SDK와 함께 사전 설치 된 모든 새 HDInsight Spark 클러스터 제공 됩니다. 이 사용 하 여 AutoML에 HDInsight 사용 하 여 시작할 수 있습니다 [샘플 Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)합니다. 이 Jupyter 노트는 단순한 분류 문제에 자동화된 기계 학습 분류자를 사용하는 방법을 보여 줍니다.

> [!Note]
> Azure Machine Learning 패키지는 Python3 conda 환경에 설치됩니다. 설치된 Jupyter 노트는 PySpark3 커널을 사용하여 실행해야 합니다.

또는 AutoML도 사용 하 여 Zeppelin notebook을 사용할 수 있습니다.

> [!Note]
> Zeppelin에는 [알려진 문제](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) PySpark3 여기서 올바른 버전의 Python 선택 하지 않습니다. 문서화 된 해결을 사용 하십시오.

## <a name="authentication-for-workspace"></a>작업 영역 인증

작업 영역을 만들고 실험을 제출하려면 인증 토큰이 필요합니다. 이 토큰은 [Azure AD 애플리케이션](../../active-directory/develop/app-objects-and-service-principals.md)을 사용하여 생성할 수 있습니다. 계정에서 Multi-Factor Authentication을 사용하도록 설정하지 않은 경우 [Azure AD 사용자](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)를 사용하여 필수 인증 토큰을 생성할 수도 있습니다.  

다음 코드 조각은 **Azure AD 애플리케이션**을 사용하여 인증 토큰을 만듭니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
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

에 [자동화 된 machine learning 구성이](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), 속성 `spark_context` 분산된 모드에서 실행할 패키지를 설정 해야 합니다. 병렬로 실행되는 최대 반복 횟수를 나타내는 속성 `concurrent_iterations`는 Spark 앱의 실행기 코어 수보다 작은 수로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화 된 기계 학습의 동기에 대 한 자세한 내용은 참조 하세요. [Microsoft를 사용 하 여 속도 릴리스 모델에 machine learning 자동화 된!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Azure ML 자동화 된 기계 학습 기능을 사용 하 여에 대 한 자세한 내용은 참조 하세요. [새 Azure Machine Learning 서비스에 기계 학습 기능을 자동화](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research의 AutoML 프로젝트](https://www.microsoft.com/research/project/automl/)
