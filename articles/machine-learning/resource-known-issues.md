---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에 대한 알려진 문제, 해결 및 문제 해결 목록을 가져옵니다.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619368"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>알려진 문제 및 Azure 기계 학습 문제 해결

이 문서는 Azure 기계 학습을 사용할 때 발생할 수 있는 오류 또는 오류를 찾아 수정하는 데 도움이 됩니다.

## <a name="diagnostic-logs"></a>진단 로그

도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 일부 로그를 보려면 다음을 수행하십시오. 
1. [Azure 기계 학습 스튜디오를](https://ml.azure.com)방문하십시오. 
1. 왼쪽에서 **실험을** 선택합니다. 
1. 실험을 선택합니다.
1. 달리기를 선택합니다.
1. 맨 위에 출력 **+ 로그를 선택합니다.**

> [!NOTE]
> Azure 기계 학습은 학습 작업을 실행하는 AutoML 또는 Docker 컨테이너와 같은 교육 중에 다양한 소스의 정보를 기록합니다. 이러한 로그의 대부분은 문서화되지 않습니다. 문제가 발생하여 Microsoft 지원에 문의하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.


## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="installation-and-import"></a>설치 및 가져오기

* **오류 메시지: ‘PyYAML’을 제거할 수 없습니다.**

    파이썬용 Azure 기계 학습 SDK: `distutils` PyYAML은 설치된 프로젝트입니다. 따라서 부분 제거가 있는 경우 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **패키지 설치 시 데이터 브릭 실패**

    더 많은 패키지를 설치할 때 Azure 컴퓨터 학습 SDK 설치가 Azure Databricks에서 실패합니다. `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 설치 오류를 방지하려면 라이브러리 버전을 고정하여 패키지를 설치합니다. 이 문제는 Azure 기계 학습 SDK가 아닌 데이터 브릭과 관련이 있습니다. 다른 라이브러리에서도 이 문제가 발생할 수 있습니다. 예제:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    또는 Python 라이브러리에서 설치 문제를 계속 직면하는 경우 init 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원되지 않습니다. 자세한 내용은 [클러스터 범위의 init 스크립트를](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)참조하십시오.

* **Databricks 가져오기 오류: 'pandas._libs.tslibs'에서 이름 'Timedelta'를 가져올 수 없습니다:** 자동화된 기계 학습을 사용할 때 이 오류가 표시되면 노트북에서 다음 두 줄을 실행합니다.
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks 가져오기 오류: 'pandas.core.indexes'라는 모듈이 없습니다:** 자동화된 기계 학습을 사용할 때 이 오류가 표시되는 경우:

    1. 이 명령을 실행하여 Azure Databricks 클러스터에 두 개의 패키지를 설치합니다.
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 클러스터를 분리한 다음 전자 필기장에 다시 연결합니다.
    
    이러한 단계를 수행해도 문제가 해결되지 않으면 클러스터를 다시 시작해 보십시오.

* **Databricks FailToSendFeather**: Azure `FailToSendFeather` Databricks 클러스터에서 데이터를 읽을 때 오류가 표시되면 다음 해결 방법을 참조하십시오.
    
    * 패키지를 `azureml-sdk[automl]` 최신 버전으로 업그레이드합니다.
    * 버전 `azureml-dataprep` 1.1.8 이상을 추가합니다.
    * 버전 `pyarrow` 0.11 이상을 추가합니다.

## <a name="create-and-manage-workspaces"></a>작업 영역 만들기 및 관리

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

* **Azure 포털**: SDK 또는 포털의 공유 링크에서 작업 영역을 직접 보려면 확장의 구독 정보가 있는 일반 **개요** 페이지를 볼 수 없습니다. 다른 작업 영역으로 전환할 수 없습니다. 다른 작업 영역을 확인해야 하는 경우 [Azure 기계 학습 스튜디오로](https://ml.azure.com) 직접 이동하여 작업 영역 이름을 검색합니다.

## <a name="set-up-your-environment"></a>환경 설정

* **AmlCompute 만들기 문제**: GA 릴리스 전에 Azure 포털에서 Azure 기계 학습 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 AmlCompute를 만들지 못할 수 있는 드문 경우도 있습니다. 서비스에 대한 지원 요청을 발생시키거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 해제할 수 있습니다.

## <a name="work-with-data"></a>데이터 작업

### <a name="overloaded-azurefile-storage"></a>오버로드된 AzureFile 저장소

오류가 `Unable to upload project files to working directory in AzureFile because the storage is overloaded`발생하면 다음 해결 방법을 적용합니다.

데이터 전송과 같은 다른 워크로드에 파일 공유를 사용하는 경우 파일 공유를 실행 을 제출하는 데 자유롭게 사용할 수 있도록 Blob을 사용하는 것이 좋습니다. 워크로드를 서로 다른 두 작업 영역으로 나눌 수도 있습니다.

### <a name="passing-data-as-input"></a>데이터를 입력으로 전달

*  **TypeError: FileNot: 이러한 파일 또는 디렉터리 없음**: 제공한 파일 경로가 파일이 있는 위치가 아닌 경우 이 오류가 발생합니다. 파일을 참조하는 방식이 계산 대상에 데이터 집합을 탑재한 위치와 일치하는지 확인해야 합니다. 결정적 상태를 보장하려면 데이터 집합을 계산 대상에 장착할 때 추상 경로를 사용하는 것이 좋습니다. 예를 들어 다음 코드에서는 계산 대상의 파일 시스템의 루트 아래에 데이터 집합을 `/tmp`탑재합니다. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    선행 방향 슬래시 '/'를 포함하지 않으면 데이터 집합을 탑재할 위치를 나타내기 위해 `/mnt/batch/.../tmp/dataset` 계산 대상에 작업 디렉토리를 접두사를 지정해야 합니다.

### <a name="data-labeling-projects"></a>데이터 라벨링 프로젝트

|문제  |해결 방법  |
|---------|---------|
|Blob 데이터 스토어에서 만든 데이터 집합만 사용할 수 있습니다.     |  이는 현재 릴리스의 알려진 제한 사항입니다.       |
|생성 후 프로젝트는 오랜 시간 동안 "초기화"를 표시합니다.     | 페이지를 수동으로 새로 고칩니다. 초기화는 초당 약 20개의 데이터 포인트에서 진행되어야 합니다. 자동 새로 고침의 부족은 알려진 문제입니다.         |
|이미지를 검토할 때 새로 레이블이 지정된 이미지는 표시되지 않습니다.     |   레이블이 지정된 모든 이미지를 로드하려면 **첫 번째** 단추를 선택합니다. **첫 번째** 단추는 목록의 맨 앞으로 돌아가지만 레이블이 지정된 모든 데이터를 로드합니다.      |
|개체 감지에 레이블을 지정하는 동안 Esc 키를 누르면 왼쪽 위 모서리에 0 크기 레이블이 만들어집니다. 이 상태에서 레이블을 제출하지 못합니다.     |   옆에 있는 크로스 마크를 클릭하여 레이블을 삭제합니다.  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

알려진 문제:

* **긴 계산 준비 시간**: 처음 연결하거나 계산 대상을 만들 때 몇 분 또는 더 오래 될 수 있습니다. 

## <a name="train-models"></a>모델 학습

* **ModuleErrors (명명된 모듈 없음)**: Azure ML에서 실험을 제출하는 동안 ModuleErrors를 실행하는 경우 학습 스크립트가 패키지를 설치할 것으로 예상하지만 추가되지 는 않습니다. 패키지 이름을 제공하면 Azure ML은 학습 실행에 사용되는 환경에 패키지를 설치합니다. 

    [추정기를](concept-azure-machine-learning-architecture.md#estimators) 사용하여 실험을 제출하는 경우 패키지를 설치할 소스에 `pip_packages` `conda_packages` 따라 추정기에서 패키지 이름 또는 매개 변수를 지정할 수 있습니다. 매개 `pip_requirements_file` 변수를 사용하여 txt 파일에 모든 pip `conda_dependencies_file`요구 사항을 나열하거나 모든 종속성을 사용하는 yml 파일을 지정할 수도 있습니다. 추정자가 사용하는 기본 이미지를 재정의하려는 자체 Azure ML 환경 개체가 있는 경우 추정기 `environment` 생성자의 매개 변수를 통해 해당 환경을 지정할 수 있습니다.

    또한 Azure ML은 텐서플로우, 파이토치, 체인어 및 SKLearn에 대한 프레임워크별 추정기도 제공합니다. 이러한 추정기는 교육에 사용되는 환경에서 핵심 프레임워크 종속성이 사용자 대신 설치되는지 확인합니다. 위에서 설명한 대로 추가 종속성을 지정하는 옵션이 있습니다. 
 
    Azure ML 유지 관리 도커 이미지와 해당 내용은 [AzureML 컨테이너에서](https://github.com/Azure/AzureML-Containers)볼 수 있습니다.
    프레임 워크 별 종속성은 각 프레임 워크 설명서에 나열 됩니다- [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks) [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > 특정 패키지가 Azure ML 유지 관리 이미지 및 환경에 추가될 정도로 일반적이라고 생각되면 [AzureML 컨테이너에서](https://github.com/Azure/AzureML-Containers)GitHub 문제를 발생시킵니다. 
 
* **NameError (이름이 정의되지 않음), AttributeError (개체에는 특성이 없음)**: 이 예외는 학습 스크립트에서 제공되어야합니다. Azure 포털의 로그 파일을 보고 정의되지 않은 특정 이름 또는 특성 오류에 대한 자세한 정보를 얻을 수 있습니다. SDK에서 오류 메시지를 `run.get_details()` 보는 데 사용할 수 있습니다. 이렇게 하면 실행에 대해 생성된 모든 로그 파일도 나열됩니다. 실행을 다시 제출하기 전에 교육 스크립트를 살펴보고 오류를 수정하십시오. 

* **Horovod종료되었습니다**: 대부분의 경우 "중단 오류 : 호로 보드는 종료되었습니다"이 예외는 Horovod가 종료된 프로세스 중 하나에 근본적인 예외가 있음을 의미합니다. MPI 작업의 각 순위는 Azure ML에서 전용 로그 파일을 소유하게 됩니다. 이러한 로그는 `70_driver_logs`이름이 지정됩니다. 분산 교육의 경우 로그 이름을 쉽게 `_rank` 구분할 수 있도록 로그 이름이 접미사가 붙어 있습니다. Horovod가 종료된 정확한 오류를 찾으려면 모든 로그 파일을 살펴보고 `Traceback` driver_log 파일의 끝을 찾습니다. 이러한 파일 중 하나는 실제 기본 예외를 제공합니다. 

* **실행 또는 실험 삭제**: [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) 메서드를 사용하거나 "실험 기록" 단추를 통해 Azure 기계 학습 스튜디오 클라이언트의 실험 탭 보기에서 실험을 보관할 수 있습니다. 이 작업은 목록 쿼리 및 보기에서 실험을 숨기지만 삭제하지는 않습니다.

    개별 실험 또는 실행의 영구 삭제는 현재 지원되지 않습니다. 작업 영역 자산 삭제에 대한 자세한 내용은 [기계 학습 서비스 작업 영역 데이터 내보내기 또는 삭제를](how-to-export-delete-data.md)참조하십시오.

* **메트릭 문서가 너무 큽**: Azure 기계 학습에는 학습 실행에서 한 번에 기록할 수 있는 메트릭 개체의 크기에 대한 내부 제한이 있습니다. 목록 값 메트릭을 로깅할 때 "메트릭 문서가 너무 큽" 오류가 발생하는 경우 목록을 더 작은 청크로 분할해 보십시오.

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    내부적으로 Azure ML은 동일한 메트릭 이름을 가진 블록을 연속 목록에 연결합니다.

## <a name="automated-machine-learning"></a>자동화된 기계 학습

* **텐서 흐름**: 자동화 된 기계 학습은 현재 텐서 흐름 버전 1.13을 지원하지 않습니다. 이 버전을 설치하면 패키지 종속성이 작동을 중지합니다. 향후 릴리스에서 이 문제를 해결하기 위해 노력하고 있습니다.

* **실험 차트**: 자동화된 ML 실험 반복에 표시된 이진 분류 차트(정밀 도면, ROC, 게인 곡선 등)는 4/12 이후 사용자 인터페이스에서 올바르게 렌더링되지 않습니다. 차트 플롯은 현재 역결과를 나타내고 있으며, 더 나은 성능의 모델은 더 낮은 결과로 표시됩니다. 해결 방법이 조사 중입니다.

* **Databricks 자동화된 컴퓨터 학습 실행을 취소:** Azure Databricks에서 자동화된 기계 학습 기능을 사용하여 실행을 취소하고 새 실험 실행을 시작하는 경우 Azure Databricks 클러스터를 다시 시작합니다.

* **Databricks는 자동화된 기계 학습을 위한 10개의 반복을 >:** 자동화된 기계 `show_output` 학습 `False` 설정에서 10회 이상의 반복이 있는 경우 실행을 제출할 때로 설정됩니다.

* **Azure 기계 학습 SDK 및 자동화된 기계 학습용 Databricks 위젯:** Azure 기계 학습 SDK 위젯은 노트북이 HTML 위젯을 구문 분석할 수 없기 때문에 Databricks 전자 필기에서 지원되지 않습니다. Azure Databricks 노트북 셀에서 이 파이썬 코드를 사용하여 포털에서 위젯을 볼 수 있습니다.

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>모델 배포 및 제공

다음 오류에 대해 다음 작업을 수행합니다.

|Error  | 해결 방법  |
|---------|---------|
|웹 서비스 배포 시 이미지 구축 실패     |  이미지 구성을 위해 Conda 파일에 핍 종속성으로 "pynacl=1.2.1"을 추가합니다.       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   배포에 사용되는 VM에 대한 SKU를 메모리가 더 많은 VM으로 변경합니다. |
|FPGA 실패     |  요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS 클러스터에서 Azure 기계 학습 구성 요소 업데이트

Azure Kubernetes 서비스 클러스터에 설치된 Azure 기계 학습 구성 요소에 대한 업데이트를 수동으로 적용해야 합니다. 

Azure 기계 학습 작업 영역에서 클러스터를 분리한 다음 클러스터를 작업 영역에 다시 연결하여 이러한 업데이트를 적용할 수 있습니다. 클러스터에서 TLS를 사용하도록 설정한 경우 클러스터를 다시 연결할 때 TLS/SSL 인증서와 개인 키를 제공해야 합니다. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

더 이상 TLS/SSL 인증서 및 개인 키가 없거나 Azure Machine Learning에서 생성된 인증서를 사용하는 경우 비밀을 `kubectl` `azuremlfessl`사용하여 클러스터에 연결하고 검색하여 클러스터를 분리하기 전에 파일을 검색할 수 있습니다.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes는 기본 64 인코딩 된 형식으로 비밀을 저장합니다. 을 제공하기 전에 비밀의 구성 `cert.pem` 요소와 `key.pem` 기본 64를 디코딩해야 합니다. `attach_config.enable_ssl` 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes 서비스 오류의 웹 서비스

Azure Kubernetes 서비스의 많은 웹 서비스 오류는 을 사용하여 `kubectl`클러스터에 연결하여 디버깅할 수 있습니다. 실행하여 Azure `kubeconfig.json` Kubernetes 서비스 클러스터를 사용할 수 있습니다.

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>인증 오류

원격 작업에서 컴퓨팅 대상에 대한 관리 작업을 수행하는 경우 다음 오류 중 하나가 나타납니다.

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

예를 들어 원격 실행을 위해 제출된 ML 파이프라인에서 컴퓨팅 대상을 만들거나 연결하려고 시도하면 오류가 나타납니다.
