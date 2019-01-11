---
title: 릴리스의 새로운 기능이란?
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 및 기계 학습 및 데이터 준비 Python SDK에 대한 최신 업데이트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742765"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning 서비스의 릴리스 정보

이 문서에서는 Azure Machine Learning 서비스의 릴리스에 대해 알아봅니다. 

## <a name="2018-12-20"></a>2018-12-20: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Python용 Azure Machine Learning SDK v1.0.6

+ **SDK 참조 문서**: https://aka.ms/aml-sdk

+ **버그 수정**: 이 릴리스는 대부분 사소한 버그 수정을 포함합니다.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **SDK 참조 문서**: https://aka.ms/data-prep-sdk

+ **새로운 기능**
  + `to_bool` 함수는 이제 일치하지 않는 값을 오류 값으로 변환하도록 허용합니다. 이는 `to_bool` 및 `set_column_types`에 대한 새로운 기본 불일치 동작인 반면 이전 기본 동작은 일치하지 않는 값을 False로 변환하는 것이었습니다.
  + `to_pandas_dataframe`을 호출할 때 NaN으로 숫자 열에서 null/누락된 값을 해석하는 새 옵션이 있습니다.
  + 일부 식의 반환 형식을 확인하는 추가된 기능은 형식 일관성 및 초기 실패를 확인하는 것입니다.
  + 이제 `parse_json`을 호출하여 JSON 개체로 열의 값을 구문 분석하고 여러 열로 확장할 수 있습니다.

+ **버그 수정**
  + Python 3.5.2에서 `set_column_types`를 충돌한 버그가 수정되었습니다.
  + AML 이미지를 사용하여 데이터 저장소에 연결할 때 충돌한 버그가 수정되었습니다.

+ **업데이트**
  * 자습서 시작, 사례 연구 및 방법 가이드에 대한 [예제 Notebooks](https://aka.ms/aml-data-prep-notebooks)

## <a name="2018-12-04-general-availability"></a>2018-12-04: 일반 공급

이제 Azure Machine Learning Service가 일반 공급됩니다.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
이번 릴리스에서는 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute)을 통해 새로운 관리형 컴퓨팅 환경을 발표합니다. 이 컴퓨팅 대상은 Azure Machine Learning에 대한 Azure Batch AI 컴퓨팅을 대체합니다. 

이 컴퓨팅 대상:
+ 모델 학습 및 일괄 처리 추론에 사용됨
+ 단일-다중 노드 컴퓨팅
+ 클러스터 관리 및 사용자에 대한 작업 예약 수행
+ 기본적으로 자동 크기 조정
+ CPU와 GPU 리소스에 대한 지원 
+ 절감된 비용에 대해 낮은 우선 순위의 VM을 사용할 수 있음

Azure Machine Learning 컴퓨팅은 Azure Portal 또는 CLI를 사용하여 Python에서 만들 수 있습니다. 작업 영역의 지역에서 만들어야 하고 다른 작업 영역에 연결할 수 없습니다. 이 컴퓨팅 대상은 실행에 Docker 컨테이너를 사용하고 종속성을 패키지하여 모든 노드에서 동일한 환경을 복제합니다.

> [!Warning]
> Azure Machine Learning 컴퓨팅을 사용하려면 새 작업 영역을 만드는 것이 좋습니다. 기존 작업 영역에서 Azure Machine Learning 컴퓨팅을 만들려고 하는 사용자에게 오류가 표시될 수 있습니다. 작업 영역의 기존 컴퓨팅은 영향을 받지 않고 계속 작동해야 합니다.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Python용 Azure Machine Learning SDK v1.0.2
+ **주요 변경 내용**
  + 이 릴리스에서는 Azure Machine Learning에서 VM을 만들기 위한 지원을 제거합니다. 기존 클라우드 VM 또는 원격 온-프레미스 서버를 계속 연결할 수 있습니다. 
  + 또한 Batch AI에 대한 지원을 제거하며, 모든 기능은 이제 Azure Machine Learning 컴퓨팅을 통해 지원됩니다.

+ **New**
  + 기계 학습 파이프라인:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **업데이트됨**
  + 기계 학습 파이프라인:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)이 이제 runconfig를 허용함
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)이 이제 SQL 데이터 원본 간에 복사됨
    + 게시된 파이프라인을 실행하기 위한 일정을 만들고 업데이트하는 SDK의 일정 기능

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **주요 변경 내용** 
  * `SummaryFunction.N`의 이름이 `SummaryFunction.Count`로 바뀌었습니다.
  
+ **버그 수정**
  * 원격 실행 시 데이터 저장소에 읽고 쓸 때 최신 AML 실행 토큰을 사용합니다. 이전에는 AML 실행 토큰이 Python에서 업데이트되는 경우 Data Prep 런타임이 업데이트된 AML 실행 토큰으로 업데이트되지 않습니다.
  * 추가적인 더 분명한 오류 메시지
  * Spark가 `Kryo` serialization을 사용할 때 to_spark_dataframe()이 더 이상 충돌하지 않음
  * Value Count Inspector가 1000개 이상의 고유 값을 표시할 수 있음
  * 원래 데이터 흐름에 이름이 없는 경우 임의 분할이 더 이상 실패하지 않음  

+ **자세한 정보**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>문서 및 Notebook
+ ML 파이프라인
  + 파이프라인, 일괄 처리 범위 및 스타일 전송 예제로 시작하기 위한 신규 및 업데이트된 Notebook: https://aka.ms/aml-pipeline-notebooks
  + [첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md) 방법 알아보기
  + [파이프라인을 사용하여 일괄 처리 예측을 실행](how-to-run-batch-predictions.md)하는 방법 알아보기
+ Azure Machine Learning 컴퓨팅 대상
  + [샘플 Notebooks](https://aka.ms/aml-notebooks)가 새 관리형 컴퓨팅을 사용하도록 업데이트되었습니다.
  + [이 컴퓨팅에 대해 알아보기](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure Portal: 새로운 기능
+ 포털에서 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute) 형식을 만들고 관리합니다.
+ Azure Machine Learning 컴퓨팅에 대한 할당량 사용 및 [요청 할당량](how-to-manage-quotas.md)을 모니터링합니다.
+ Azure Machine Learning 컴퓨팅 클러스터 상태를 실시간으로 확인합니다.
+ Azure Machine Learning 컴퓨팅 및 Azure Kubernetes Service 만들기에 대한 가상 네트워크 지원이 추가되었습니다.
+ 기존 매개 변수를 사용하여 게시된 파이프라인을 다시 실행합니다.
+ 분류 모델(리프트, 게인, 보정, 모델 확장성 포함 기능 중요성 차트) 및 회귀 모델(오차 및 모델 확장성 포함 기능 중요성 차트)에 대한 새로운 [자동화된 기계 학습 차트](how-to-track-experiments.md#auto). 
+ Azure Portal에서 파이프라인을 볼 수 있음




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Python용 Azure Machine Learning SDK v0.1.80

+ **주요 변경 내용** 
  * *azureml.train.widgets* 네임스페이스가 *azureml.widgets*로 전환되었습니다.
  * *azureml.core.compute.AmlCompute*는 *azureml.core.compute.BatchAICompute* 및 *azureml.core.compute.DSVMCompute* 클래스를 더 이상 사용하지 않습니다. 후자의 클래스는 이후 릴리스에서 제거됩니다. AmlCompute 클래스는 이제 더 쉽게 정의할 수 있으며, vm_size 및 max_nodes만 필요하고, 작업이 제출되면 클러스터가 자동으로 0에서 max_nodes로 확장됩니다. [샘플 Notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/training)는 이 정보로 업데이트되었으며, 사용 예제를 제공해야 합니다. 이 간소화와 나중에 릴리스될 많은 흥미로운 기능이 마음에 들기를 바랍니다!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning 데이터 준비 SDK v0.5.1 

[참고 문서](https://aka.ms/data-prep-sdk)를 참조하여 데이터 준비 SDK에 대해 자세히 알아봅니다.
+ **새로운 기능**
   * DataPrep 패키지를 실행하고 데이터 세트 또는 데이터 흐름에 대한 데이터 프로필을 볼 수 있는 새 DataPrep CLI가 만들어졌습니다.
   * 유용성이 향상되도록 SetColumnType API가 다시 설계되었습니다.
   * smart_read_file의 이름이 auto_read_file로 변경되었습니다.
   * 기울이기 및 첨도가 이제 데이터 프로필에 포함되었습니다.
   * 계층화된 샘플링을 통해 샘플링할 수 있습니다.
   * CSV 파일이 포함된 zip 파일에서 읽을 수 있습니다.
   * 임의 분할을 사용하여 데이터 세트를 행 단위로 분할할 수 있습니다(예: 테스트 학습 세트로 분할).
   * `.dtypes`를 호출하여 데이터 흐름 또는 데이터 프로필에서 모든 열 데이터 형식을 가져올 수 있습니다.
   * `.row_count`를 호출하여 데이터 흐름 또는 데이터 프로필에서 행 수를 가져올 수 있습니다.

+ **버그 수정**
   * long에서 double로 변환할 수 있도록 수정되었습니다. 
   * 열 추가 이후의 어설션이 수정되었습니다. 
   * 경우에 따라 그룹을 검색하지 않는 FuzzyGrouping 문제가 해결되었습니다.
   * 다중 열 정렬 순서를 따르도록 정렬 함수가 수정되었습니다.
   * `pandas`에서 처리하는 방식과 비슷하게 처리하도록 식이 수정되었습니다.
   * dbfs 경로에서 읽을 수 있도록 수정되었습니다.
   * 오류 메시지를 더 쉽게 이해할 수 있습니다. 
   * 이제 AML 토큰을 사용하여 원격 컴퓨팅 대상을 읽을 때 더 이상 오류가 발생하지 않습니다.
   * 이제 Linux DSVM에서 더 이상 오류가 발생하지 않습니다.
   * 이제 문자열 조건자에 문자열이 아닌 값이 있는 경우에도 더 이상 충돌하지 않습니다.
   * 이제 Dataflow에서 올바르게 실패해야 하는 경우 어설션 오류를 처리합니다.
   * 이제 Azure Databricks에서 dbutils 탑재 스토리지 위치를 지원합니다.

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure portal 
Azure Machine Learning 서비스에 대한 Azure Portal은 다음과 같이 업데이트되었습니다.
  * 게시된 파이프라인에 대한 새 **파이프라인** 탭.
  * 기존 HDInsight 클러스터를 컴퓨팅 대상으로 연결할 수 있도록 지원이 추가되었습니다.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Python용 Azure Machine Learning SDK v0.1.74

+ **주요 변경 내용** 
  * *Workspace.compute_targets, datastores, experiments, images, models 및 *webservices*는 메서드가 아니라 속성입니다. 예를 들어 *Workspace.compute_targets()* 를 *Workspace.compute_targets*로 바꿉니다.
  * *Run.get_context*는 더 이상 *Run.get_submitted_run*을 사용하지 않습니다. 두 번째 메서드는 후속 릴리스에서 제거될 예정입니다.
  * *PipelineData* 클래스는 이제 데이터 저장소 개체를 datastore_name이 아닌 매개 변수로 예상합니다. 마찬가지로, *파이프라인*은 default_datastore_name 대신 default_datastore를 수락합니다.

+ **새로운 기능**
  * Azure Machine Learning 파이프라인 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb)은 이제 MPI 단계를 사용합니다.
  * Jupyter Notebook용 RunDetails 위젯은 파이프라인의 시각화를 표시하도록 업데이트되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning 데이터 준비 SDK v0.4.0 
 
+ **새로운 기능**
  * 데이터 프로필에 유형 개수 추가 
  * 이제 값 개수 및 히스토그램 사용 가능
  * 데이터 프로필의 백분위수 추가
  * 요약에서 중앙값 사용 가능
  * 이제 Python 3.7 지원
  * 데이터 저장소를 포함하는 데이터 흐름을 DataPrep 패키지에 저장하면 데이터 저장소 정보가 DataPrep 패키지의 일부로 유지됩니다.
  * 데이터 저장소에 쓰기 지원 
        
+ **버그 수정**
  * 이제 64비트 부호 없는 정수 오버플로가 Linux에서 올바르게 처리됨
  * smart_read에서 일반 텍스트 파일의 잘못된 텍스트 레이블 수정
  * 이제 문자열 열 형식을 메트릭 보기로 표시
  * ValueKinds를 개별 항목이 아닌 단일 FieldType에 매핑된 것으로 표시하도록 유형 개수 수정
  * 이제는 경로를 문자열로 입력해도 Write_to_csv가 실패하지 않음
  * 바꾸기를 사용할 때 “찾기”를 비워 두어도 더 이상 실패하지 않음 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Python용 Azure Machine Learning SDK v0.1.68

+ **새로운 기능**
  * 새 작업 영역을 만들 때 다중 테넌트를 지원합니다.

+ **수정된 버그**
  * 웹 서비스를 배포할 때 pynacl 라이브러리 버전을 더 이상 고정할 필요가 없습니다.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning 데이터 준비 SDK v0.3.0

+ **새로운 기능**
  * 사용자가 실행할 Python 파일의 경로를 전달할 수 있는 transform_partition_with_file(script_path) 메서드가 추가되었습니다.

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Python용 Azure Machine Learning SDK v0.1.65
[버전 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65)에는 새로운 기능, 더 많은 설명서, 버그 수정 및 더 많은 [노트북 샘플](https://aka.ms/aml-notebooks)이 포함되었습니다.

알려진 버그 및 해결 방법에 대해 알아 보려면 [알려진 문제 목록](resource-known-issues.md)을 참조하세요.

+ **주요 변경 내용**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services에서 이전에 반환된 목록인 사전을 반환합니다. [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 설명서를 참조하세요.

  * 자동화된 Machine Learning에서 기본 메트릭의 정규화된 평균 제곱 오차가 제거되었습니다.

+ **HyperDrive**
  * Bayesian에 대한 다양한 HyperDrive 버그가 수정되고, 메트릭 호출 가져오기 성능이 향상되었습니다. 
  * Tensorflow 1.9에서 1.10으로 업그레이드 
  * 콜드 부팅용 Docker 이미지 최적화 
  * 0이 아닌 오류 코드로 인해 종료하는 경우에도 작업에서 올바른 상태를 보고합니다. 
  * SDK에서 RunConfig 특성 유효성 검사 
  * HyperDrive 실행 개체에서 일반 실행과 비슷한 방식으로 취소를 지원합니다(매개 변수를 전달할 필요가 없음). 
  * 분산 실행 및 HyperDrive 실행에 대한 드롭다운 값의 상태를 유지하도록 위젯이 향상되었습니다. 
  * TensorBoard 및 다른 로그 파일에서 매개 변수 서버에 대한 수정을 지원합니다. 
  * 서비스 쪽에서 인텔(R) MPI 지원 
  * BatchAI의 유효성 검사 중에 분산 실행 수정에 대한 매개 변수 튜닝 버그가 수정되었습니다. 
  * 컨텍스트 관리자에서 기본 인스턴스를 식별합니다. 

+ **Azure Portal 환경**
  * log_table() 및 log_row()가 [실행] 세부 정보에서 지원됩니다. 
  * 1, 2 또는 3개의 숫자 열과 선택적 범주별 열이 있는 테이블과 행에 대한 그래프를 자동으로 만듭니다.

+ **자동화된 Machine Learning**
  * 오류 처리 및 설명서가 향상되었습니다. 
  * 실행 속성 검색 성능 문제가 해결되었습니다. 
  * 계속 실행 문제가 해결되었습니다. 
  * ensembling 반복 문제가 해결되었습니다.
  * MAC OS의 학습 중단 버그가 수정되었습니다.
  * 사용자 지정 유효성 검사 시나리오에서 매크로 평균 PR/ROC 곡선의 저해상도가 처리되었습니다.
  * 추가 인덱스 논리가 제거되었습니다.
  * get_output API에서 필터가 제거되었습니다.

+ **파이프라인**
  * 실행 작업을 먼저 실행할 필요 없이 파이프라인을 직접 게시하는 Pipeline.publish() 메서드가 추가되었습니다.   
  * 게시된 파이프라인에서 생성된 파이프라인 실행을 가져오는 PipelineRun.get_pipeline_runs() 메서드가 추가되었습니다.

+ **Project Brainwave**
  * FPGA에서 사용할 수 있는 새 AI 모델에 대한 지원이 업데이트되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning 데이터 준비 SDK v0.2.0
[버전 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/)에 포함된 기능과 버그 수정은 다음과 같습니다.

+ **새로운 기능**
  * One Hot 인코딩 지원
  * 변 위치 변환 지원
   
+ **수정된 버그:**
  * 모든 Tornado 버전에서 작동하므로 Tornado 버전을 다운그레이드할 필요가 없습니다.
  * Value에서 상위 3개 외에도 모든 값의 개수를 계산합니다.

## <a name="2018-09-public-preview-refresh"></a>2018-09(공개 미리 보기 새로 고침)

새로 고친 Azure Machine Learning 릴리스: 이 릴리스에 대한 자세한 내용: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>이전 릴리스 정보: 2017년 9월~2018년 6월
### <a name="2018-05-sprint-5"></a>2018-05(스프린트 5)

이 릴리스의 Azure Machine Learning에서 수행할 수 있는 작업은 다음과 같습니다.
+ ResNet 50의 양자화된 버전으로 이미지를 기능화하고, 이러한 기능에 따라 분류자를 학습하고, 초단기 대기 시간 추론을 위해 [Azure에서 FPGA에 해당 모델을 배포](../service/how-to-deploy-fpga-web-service.md)합니다.

+ [사용자 지정 Azure Machine Learning 패키지](../desktop-workbench/reference-python-package-overview.md)를 사용하여 고정밀 기계 학습 및 딥 러닝 모델을 빠르게 빌드하고 배포합니다.

### <a name="2018-03-sprint-4"></a>2018-03(스프린트 4)
**버전 번호**: 0.1.1801.24353 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 찾기](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

다음 업데이트 중 많은 부분이 여러분이 보내 주신 피드백에 따른 직접적인 결과입니다. 앞으로도 이러한 결과를 얻을 수 있도록 여러분의 피드백을 계속 보내주십시오!

**주목할 만한 새로운 기능 및 변경 사항**

- 기본적으로 원격 Docker 기반 실행 외에도 고유한 환경의 원격 Ubuntu VM에서 스크립트를 실행하도록 지원합니다.
- 워크벤치 앱에서 새로운 환경을 사용하면 계산 대상을 만들고 CLI 기반 환경 외에도 구성을 실행할 수 있습니다.
![환경 탭](media/azure-machine-learning-release-notes/environment-page.png)
- 사용자 지정 가능한 실행 기록 보고서 ![새로운 실행 기록 보고서의 이미지](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**자세한 업데이트**

다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

#### <a name="workbench-ui"></a>워크벤치 UI
- 사용자 지정 가능한 실행 기록 보고서
  - 실행 기록 보고서에 대한 향상된 차트 구성
    - 사용되는 진입점을 변경할 수 있습니다.
    - 최상위 필터를 추가하고 수정할 수 있습니다. ![필터 추가](media/azure-machine-learning-release-notes/add-filters.jpg)
    - 차트 및 통계를 추가하거나 수정(및 끌어서 놓기를 다시 정렬)할 수 있습니다.
    ![새 차트 만들기](media/azure-machine-learning-release-notes/configure-charts.png)

  - 실행 기록 보고서의 CRUD
  - 선택한 진입점에서 실행 시 파이프라인과 같은 역할을 담당하는 서버 쪽 보고서로 모든 기존 실행 기록 목록 보기 구성 파일을 이동합니다.

- 환경 탭
  - 프로젝트에 새로운 계산 대상 및 실행 구성 파일을 쉽게 추가합니다. ![새 계산 대상](media/azure-machine-learning-release-notes/add-new-environments.png)
  - 간단한 양식 기반 UX를 사용하여 구성 파일을 관리하고 업데이트합니다.
  - 실행할 환경을 준비하기 위한 새 단추입니다.

- 세로 막대에 있는 파일 목록에 대한 성능 향상

#### <a name="data-preparation"></a>데이터 준비 
- 이제 Azure Machine Learning Workbench를 사용하면 알려진 열의 이름을 사용하여 열을 검색할 수 있습니다.


#### <a name="experimentation"></a>실험
- 이제 Azure Machine Learning Workbench는 기본적으로 고유한 Python 또는 PySpark 환경에서 스크립트를 실행하도록 지원합니다. 이 기능으로 사용자는 원격 VM에서 고유한 환경을 만들고 관리하고, Azure Machine Learning Workbench를 사용하여 해당 대상에서 해당 스크립트를 실행합니다. [Azure Machine Learning 실험 서비스 구성](../desktop-workbench/experimentation-service-configuration.md)을 참조하세요. 

#### <a name="model-management"></a>모델 관리
- 배포된 컨테이너를 사용자 지정하도록 지원: apt-get 등을 사용하는 외부 라이브러리를 설치하도록 허용하여 컨테이너 이미지를 사용자 지정할 수 있습니다. 더 이상 pip 설치 가능 라이브러리로 제한되지 않습니다. 자세한 내용은 [문서](../desktop-workbench/model-management-custom-container.md)를 참조하세요.
  - 매니페스트, 이미지 또는 서비스 생성 명령이 포함된 `--docker-file myDockerStepsFilename` 플래그 및 파일 이름을 사용합니다.
  - 기본 이미지가 Ubuntu이며 수정할 수 없습니다.
  - 예제 명령: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01(스프린트 3) 
**버전 번호**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 찾기](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

다음은 이번 스프린트의 업데이트 및 개선 사항입니다. 아래 업데이트 중 많은 부분은 사용자가 보내 주신 피드백에 따른 직접적인 결과입니다. 

### <a name="2017-12-sprint-2"></a>2017-12(스프린트 2)
**버전 번호**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 찾기](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

이 릴리스는 세 번째 Azure Machine Learning 업데이트입니다. 이 업데이트는 Workbench 앱, CLI(명령줄 인터페이스) 및 백 엔드 서비스의 향상된 기능을 포함합니다. 웃는 얼굴과 찡그린 얼굴을 보내 주시어 대단히 감사합니다. 다음 업데이트 중 많은 부분이 여러분이 보내 주신 피드백에 따른 직접적인 결과입니다. 

### <a name="2017-11-sprint-1"></a>2017-11(스프린트 1) 
**버전 번호**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 찾기](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

이 릴리스에서는 워크벤치 앱, CLI 및 백 엔드 서비스 계층에서 보안, 안정성 및 유지 관리 기능이 향상되었습니다. 

### <a name="2017-10-sprint-0"></a>2017-10(스프린트 0) 
**버전 번호**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 찾기](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

이 릴리스는 Microsoft Ignite 2017 컨퍼런스에서 최초 공용 미리 보기를 선보인 후에 출시된 Azure Machine Learning Workbench 최초 업데이트입니다. 이 릴리스에서는 주로 안정성이 개선되고 안정화 문제가 수정되었습니다. 

## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](../service/overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
