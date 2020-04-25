---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 알려진 문제, 해결 방법 및 문제 해결 목록을 가져옵니다.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 58fd9225298b4322567f4feb02629e3ad4e0f00d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127562"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>알려진 문제 및 문제 해결 Azure Machine Learning

이 문서는 Azure Machine Learning을 사용할 때 발생할 수 있는 오류 또는 오류를 찾고 수정 하는 데 도움이 됩니다.

## <a name="diagnostic-logs"></a>진단 로그

도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 일부 로그를 보려면 다음을 수행 합니다. 
1. [Azure Machine Learning studio](https://ml.azure.com)를 방문 합니다. 
1. 왼쪽에서 **실험** 을 선택 합니다. 
1. 실험을 선택 합니다.
1. 실행을 선택 합니다.
1. 위쪽에서 **출력 + 로그**를 선택 합니다.

> [!NOTE]
> 자동 Ml 또는 교육 작업을 실행 하는 Docker 컨테이너와 같은 학습 중 다양 한 원본에서 정보를 기록 Azure Machine Learning 합니다. 이러한 로그는 대부분 문서화 되어 있지 않습니다. 문제가 발생 하 고 Microsoft 지원에 문의 하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.


## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="installation-and-import"></a>설치 및 가져오기

* **Pip 설치: 종속성이 단일 줄 설치와 일치 하지**않을 수 있습니다. 

   이는 단일 선으로를 설치할 때 작동 하는 종속성 해결 프로그램이 없기 때문에 pip의 알려진 제한 사항입니다. 첫 번째 고유 종속성은 보이는 유일한 종속성입니다. 

   다음 코드 `azure-ml-datadrift` 에서 및 `azureml-train-automl` 는 모두 한 줄 pip 설치를 사용 하 여 설치 됩니다. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   이 예에서는 > 1.0 버전이 `azure-ml-datadrift` 필요 하며 `azureml-train-automl` , < 1.2 버전이 필요 합니다. 최신 버전의 `azure-ml-datadrift` 가 1.3 인 경우 이전 버전의 `azureml-train-automl` 패키지 요구 사항에 관계 없이 두 패키지가 모두 1.3로 업그레이드 됩니다. 

   패키지에 대 한 적절 한 버전이 설치 되었는지 확인 하려면 다음 코드와 같이 여러 줄을 사용 하 여를 설치 합니다. Pip는 다음 줄 호출의 일부로 명시적으로 다운 그레이드 때문에 순서가 문제가 되지 않습니다. 그러면 적절 한 버전 종속성이 적용 됩니다.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **오류 메시지: ‘PyYAML’을 제거할 수 없습니다.**

    Python 용 Azure Machine Learning SDK: PyYAML는 `distutils` 설치 된 프로젝트입니다. 따라서 부분 제거가 있는 경우 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **패키지를 설치할 때 Databricks 오류 발생**

    추가 패키지가 설치 되 면 Azure Databricks에서 Azure Machine Learning SDK 설치가 실패 합니다. `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 설치 오류를 방지 하려면 라이브러리 버전을 고정 하 여 패키지를 설치 합니다. 이 문제는 Azure Machine Learning SDK가 아닌 Databricks와 관련이 있습니다. 다른 라이브러리 에서도이 문제가 발생할 수 있습니다. 예제:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    또는 Python 라이브러리와의 연결을 유지 하는 경우 init 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원 되지 않습니다. 자세한 내용은 [클러스터 범위 init 스크립트](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)를 참조 하세요.

* **Databricks 가져오기 오류: ' pandas _libs. tslibs '에서 ' Timedelta ' 이름을 가져올 수 없습니다**. 자동화 된 machine learning을 사용 하는 경우이 오류가 표시 되 면 노트북에서 다음 두 줄을 실행 합니다.
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks 가져오기 오류: ' pandas ' 모듈이 없습니다**. 자동화 된 machine learning을 사용 하는 경우이 오류가 표시 됩니다.

    1. Azure Databricks 클러스터에 두 개의 패키지를 설치 하려면이 명령을 실행 합니다.
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 클러스터를 분리 했다가 노트북에 다시 연결 합니다.
    
    이러한 단계를 수행 해도 문제가 해결 되지 않으면 클러스터를 다시 시작 하십시오.

* **Databricks FailToSendFeather**: Azure Databricks 클러스터에서 데이터 `FailToSendFeather` 를 읽는 동안 오류가 표시 되는 경우 다음 해결 방법을 참조 하세요.
    
    * 패키지 `azureml-sdk[automl]` 를 최신 버전으로 업그레이드 합니다.
    * 버전 `azureml-dataprep` 1.1.8 이상을 추가 합니다.
    * 버전 `pyarrow` 0.11 이상을 추가 합니다.
    
## <a name="create-and-manage-workspaces"></a>작업 영역 만들기 및 관리

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동 하거나 소유 하는 구독을 새 테 넌 트로 이동 하는 것은 지원 되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

* **Azure Portal**: SDK 또는 포털의 공유 링크에서 작업 영역을 직접 확인 하는 경우 확장에서 구독 정보가 포함 된 일반 **개요** 페이지를 볼 수 없습니다. 다른 작업 영역으로 전환할 수 없습니다. 다른 작업 영역을 확인 해야 하는 경우 [Azure Machine Learning studio](https://ml.azure.com) 로 직접 이동 하 여 작업 영역 이름을 검색 합니다.

## <a name="set-up-your-environment"></a>환경 설정

* **AmlCompute 생성 문제**: GA 릴리스 전에 Azure Portal에서 Azure Machine Learning 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 AmlCompute를 만들지 못할 수 있습니다. 서비스에 대 한 지원 요청을 발생 시키거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 해제할 수 있습니다.

## <a name="work-with-data"></a>데이터 작업

### <a name="overloaded-azurefile-storage"></a>오버 로드 된 AzureFile 저장소

오류가 `Unable to upload project files to working directory in AzureFile because the storage is overloaded`표시 되 면 다음 해결 방법을 적용 합니다.

데이터 전송과 같은 다른 작업에 대해 파일 공유를 사용 하는 경우 파일 공유를 사용 하 여 실행을 제출할 수 있도록 blob을 사용 하는 것이 좋습니다. 작업을 서로 다른 두 작업 영역 간에 분할할 수도 있습니다.

### <a name="passing-data-as-input"></a>데이터를 입력으로 전달

*  **TypeError: FileNotFound: 해당 파일 또는 디렉터리가 없습니다**.이 오류는 사용자가 제공 하는 파일 경로가 파일이 있는 위치가 아닌 경우에 발생 합니다. 계산 대상에서 데이터 집합을 탑재 한 위치와 파일을 참조 하는 방법이 일치 하는지 확인 해야 합니다. 결정적 상태를 보장 하려면 계산 대상에 데이터 집합을 탑재할 때 추상 경로를 사용 하는 것이 좋습니다. 예를 들어 다음 코드에서 계산 대상의 파일 시스템 루트 아래에 데이터 집합을 `/tmp`탑재 합니다. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    선행 슬래시 ('/')를 포함 하지 않는 경우 데이터 집합을 탑재할 위치를 나타내기 위해 계산 대상에서 `/mnt/batch/.../tmp/dataset` 작업 디렉터리 (예:)에 접두사를 추가 해야 합니다.

### <a name="data-labeling-projects"></a>데이터 레이블 지정 프로젝트

|문제  |해결 방법  |
|---------|---------|
|Blob 데이터 저장소에서 만든 데이터 집합만 사용할 수 있습니다.     |  현재 릴리스의 알려진 제한 사항입니다.       |
|프로젝트를 만든 후에는 오랜 시간 동안 "초기화 중"이 표시 됩니다.     | 페이지를 수동으로 새로 고칩니다. 초기화는 초당 약 20 datapoints 진행 되어야 합니다. Autorefresh의 부족은 알려진 문제입니다.         |
|이미지를 검토할 때 새로 레이블이 지정 된 이미지는 표시 되지 않습니다.     |   레이블이 지정 된 모든 이미지를 로드 하려면 **첫 번째** 단추를 선택 합니다. **첫 번째** 단추는 목록 맨 앞으로 다시 이동 하지만 레이블이 지정 된 모든 데이터를 로드 합니다.      |
|개체 검색에 대 한 레이블을 지정 하는 동안 Esc 키를 누르면 왼쪽 위 모퉁이에 크기가 0 인 레이블이 생성 됩니다. 이 상태의 레이블 전송에 실패 합니다.     |   옆의 십자 표시를 클릭 하 여 레이블을 삭제 합니다.  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

알려진 문제:

* **긴 계산 준비 시간**: 계산 대상에 처음 연결 하거나 만들 때 몇 분이 걸릴 수 있습니다. 

## <a name="train-models"></a>모델 학습

* **Moduleerrors (이름이 지정 된 모듈 없음)**: Azure ML에서 실험을 제출 하는 동안 moduleerrors를 실행 하는 경우 학습 스크립트는 패키지를 설치 해야 하지만 추가 되지 않았음을 의미 합니다. 패키지 이름을 제공 하면 Azure ML은 교육 실행에 사용 되는 환경에 패키지를 설치 합니다. 

    [추정](concept-azure-machine-learning-architecture.md#estimators) 를 사용 하 여 실험을 제출 하는 경우 패키지를 설치 하려는 원본 `pip_packages` 에 `conda_packages` 기반 하 여 평가기에서 또는 매개 변수를 통해 패키지 이름을 지정할 수 있습니다. 를 사용 하 `conda_dependencies_file`여 모든 종속성이 포함 된 iisnode.yml 파일을 지정 하거나 매개 변수를 사용 하 여 `pip_requirements_file` txt 파일에 모든 pip 요구 사항을 나열할 수도 있습니다. 평가기에서 사용 하는 기본 이미지를 재정의 하려는 고유한 Azure ML Environment 개체가 있는 경우 평가기 생성자의 `environment` 매개 변수를 통해 해당 환경을 지정할 수 있습니다.

    또한 Azure ML은 Tensorflow, PyTorch, 체 이너 및 추정에 대 한 프레임 워크 관련 제공 합니다. 이러한 추정을 사용 하 여 학습에 사용 되는 환경에서 핵심 프레임 워크 종속성을 대신 설치 하도록 합니다. 위에서 설명한 대로 추가 종속성을 지정 하는 옵션이 있습니다. 
 
    Azure ML에서 관리 하는 docker 이미지와 해당 콘텐츠는 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 볼 수 있습니다.
    프레임 워크 관련 종속성은 해당 프레임 워크 설명서- [체](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), 고 지 사항 [배우기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)에 나열 되어 있습니다.

    > [!Note]
    > 특정 패키지가 Azure 기계 학습에서 유지 관리 되는 이미지 및 환경에 추가할 수 있는 것으로 생각 되는 경우 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 GitHub 문제를 제기 하세요. 
 
* **Nameerror (이름이 정의 되지 않음), attributeerror (개체에 특성이 없음)**:이 예외는 학습 스크립트에서 제공 되어야 합니다. Azure Portal에서 로그 파일을 확인 하 여 지정 되지 않은 특정 이름 또는 특성 오류에 대 한 자세한 정보를 볼 수 있습니다. SDK에서를 사용 `run.get_details()` 하 여 오류 메시지를 확인할 수 있습니다. 또한 실행을 위해 생성 된 모든 로그 파일을 나열 합니다. 실행을 다시 전송 하기 전에 학습 스크립트를 확인 하 고 오류를 수정 하세요. 

* **Horovod 종료**됨: 대부분의 경우 "AbortedError: Horovod가 종료 되었습니다."이 예외는 Horovod 종료를 일으킨 프로세스 중 하나에 기본 예외가 있음을 의미 합니다. MPI 작업의 각 순위는 Azure ML의 전용 로그 파일을 가져옵니다. 이러한 로그의 이름은 `70_driver_logs`입니다. 분산 교육의 경우 로그를 쉽게 구분할 수 있도록 로그 이름의 `_rank` 접미사가 지정 됩니다. Horovod가 종료 되는 정확한 오류를 찾으려면 모든 로그 파일을 확인 하 고 driver_log 파일의 끝에 `Traceback` 있는을 찾습니다. 이러한 파일 중 하나는 실제 기본 예외를 제공 합니다. 

* **실행 또는 실험 삭제**: 실험을 사용 하 [여 보관 하거나](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) "실험 보관" 단추를 통해 Azure Machine Learning studio 클라이언트의 실험 탭 보기에서 보관할 수 있습니다. 이 동작을 수행 하면 쿼리 및 뷰 목록에서 실험을 숨길 수 있지만 삭제 하지는 않습니다.

    개별 실험 또는 실행을 영구적으로 삭제 하는 것은 현재 지원 되지 않습니다. 작업 영역 자산을 삭제 하는 방법에 대 한 자세한 내용은 [Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제](how-to-export-delete-data.md)를 참조 하세요.

* **메트릭 문서가 너무 큼**: Azure Machine Learning 학습 실행에서 한 번에 기록할 수 있는 메트릭 개체 크기에 대 한 내부 제한이 있습니다. 목록 값 메트릭을 로깅할 때 "메트릭 문서가 너무 커서" 오류가 발생 하는 경우 목록을 더 작은 청크로 분할 해 보세요. 예를 들면 다음과 같습니다.

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    내부적으로 Azure ML은 동일한 메트릭 이름을 가진 블록을 연속 된 목록에 연결 합니다.

## <a name="automated-machine-learning"></a>자동화된 기계 학습

* **텐서 flow**: 자동화 된 machine learning은 현재 텐서 Flow 버전 1.13을 지원 하지 않습니다. 이 버전을 설치 하면 패키지 종속성이 작동 하지 않습니다. 이후 릴리스에서이 문제를 해결 하기 위해 노력 하 고 있습니다.

* **실험 차트**: 자동화 된 ML 실험 반복에 표시 되는 이진 분류 차트 (정밀도-리콜, ROC, 게인 곡선 등)는 4/12 이후 사용자 인터페이스에서 올바르게 렌더링 되지 않습니다. 차트 플롯에는 현재 더 낮은 결과가 포함 된 모델을 더 잘 수행 하는 역 결과가 표시 됩니다. 확인 중입니다.

* Databricks에서 자동화 된 machine learning **실행 취소**: Azure Databricks에서 자동화 된 machine learning 기능을 사용 하는 경우 실행을 취소 하 고 새 실험 실행을 시작 하려면 Azure Databricks 클러스터를 다시 시작 합니다.

* **자동화 된 machine learning에 대 한 Databricks >10 회 반복**: 자동화 된 기계 학습 설정에서 10 개 이상의 반복이 `show_output` 있는 `False` 경우 실행을 제출할 때를로 설정 합니다.

* **AZURE MACHINE LEARNING sdk 및 자동화 된 기계 학습을 위한 Databricks 위젯**: Azure Machine Learning SDK 위젯은 Databricks 노트북에서 HTML 위젯을 구문 분석할 수 없기 때문에 지원 되지 않습니다. Azure Databricks 노트북 셀에서이 Python 코드를 사용 하 여 포털에서 위젯을 볼 수 있습니다.

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>모델 배포 및 제공

다음 오류에 대해이 작업을 수행 합니다.

|Error  | 해결 방법  |
|---------|---------|
|웹 서비스 배포 시 이미지 작성 오류     |  이미지 구성을 위해 "pConda acl = = 1.2.1"을 파일에 대 한 pip 종속성으로 추가 합니다.       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   배포에 사용 되는 Vm의 SKU를 메모리를 더 많이 포함 하는 Vm으로 변경 합니다. |
|FPGA 오류     |  요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS 클러스터의 Azure Machine Learning 구성 요소 업데이트

Azure Kubernetes Service 클러스터에 설치 된 Azure Machine Learning 구성 요소에 대 한 업데이트를 수동으로 적용 해야 합니다. 

Azure Machine Learning 작업 영역에서 클러스터를 분리 하 여 이러한 업데이트를 적용 한 다음 클러스터를 작업 영역에 다시 연결할 수 있습니다. 클러스터에서 TLS를 사용 하는 경우 클러스터를 다시 연결할 때 TLS/SSL 인증서와 개인 키를 제공 해야 합니다. 

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

TLS/SSL 인증서와 개인 키가 더 이상 없거나 Azure Machine Learning에서 생성 한 인증서를 사용 하는 경우를 사용 하 `kubectl` 여 클러스터에 연결 하 고 암호 `azuremlfessl`를 검색 하 여 클러스터를 분리 하기 전에 파일을 검색할 수 있습니다.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes는 암호를 base-64로 인코딩된 형식으로 저장 합니다. 에 `cert.pem` `attach_config.enable_ssl`제공 하기 전에 비밀의 및 `key.pem` 구성 요소를 64으로 디코드 해야 합니다. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes 서비스 실패의 Webservices

를 사용 하 `kubectl`여 클러스터에 연결 하 여 Azure Kubernetes Service에서 많은 webservice 오류를 디버그할 수 있습니다. 을 실행 하 여 `kubeconfig.json` Azure Kubernetes 서비스 클러스터에 대 한를 가져올 수 있습니다.

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
