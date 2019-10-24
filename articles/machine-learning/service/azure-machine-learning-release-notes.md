---
title: 릴리스의 새로운 기능이란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 최신 업데이트 및 기계 학습 및 데이터 준비 Python Sdk에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: afad2648ec73b02d4e06ad55f850a518d2488f68
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756047"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning 릴리스 정보

이 문서에서는 Azure Machine Learning 릴리스에 대해 알아봅니다.  전체 SDK 참조 콘텐츠는 Azure Machine Learning의 [**Python 용 기본 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 페이지를 참조 하세요. 

알려진 버그 및 해결 방법에 대해 알아 보려면 [알려진 문제 목록](resource-known-issues.md)을 참조하세요.

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>시각적 인터페이스 (미리 보기)

+ Azure Machine Learning 시각적 인터페이스 (미리 보기)는 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)에서 실행 점검 했습니다 되었습니다. 시각적 인터페이스에서 작성 된 파이프라인 (이전에는 실험 이라고 함)은 이제 핵심 Azure Machine Learning 환경과 완벽 하 게 통합 됩니다.
  + SDK 자산과 통합 된 관리 환경
  + 시각적 인터페이스 모델, 파이프라인 및 끝점에 대 한 버전 관리 및 추적 
  + 다시 디자인 한 UI
  + Batch 추론 배포 추가 됨
  + 추론 계산 대상에 대 한 AKS (Azure Kubernetes Service) 지원 추가
  + 새 Python-단계 파이프라인 제작 워크플로
  + 시각적 제작 도구에 대 한 새 [방문 페이지](https://ml.azure.com)

+ **새 모듈**
  + 수학 연산 적용
  + SQL 변환 적용
  + 값 자르기
  + 데이터 요약
  + SQL database에서 가져오기  

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK for Python v 1.0.69

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + 모든 실행에 대 한 설명을 계산 하지 않고 모델 설명을 최상의 실행으로 제한 로컬, 원격 및 ADB에 대해이 동작을 변경 합니다.
    + UI에 대 한 요청 시 모델 설명에 대 한 지원 추가
    + Psutil을 automl의 종속성으로 추가 하 고 amlcompute에서 conda 종속성으로 포함 된 psutil을 추가 했습니다.
    + 예측 데이터 집합에서 추론 지연 및 롤링 창 크기에 대 한 문제를 해결 했습니다. 일부 시리즈는 선형 대 수 오류를 일으킬 수 있습니다.
      + 예측 실행에서 결정 된 발견적 매개 변수에 대 한 출력을 추가 했습니다.
  + **azureml-datadrift**
    + 첫 번째 섹션에 데이터 집합 수준 드리프트가 없는 경우 출력 메트릭을 만드는 동안 보호 기능이 추가 되었습니다.
  + **azureml-인-해석**
    + azureml-설명-설명-모델 패키지의 이름이 azureml로 바뀌었습니다.
  + **azureml-코어**
    + 데이터 집합의 등록을 취소 하는 API가 추가 되었습니다. `dataset.unregister_all_versions()`
    + 데이터 변경 시간을 확인 하는 데이터 집합 API를 추가 했습니다. `dataset.data_changed_time`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다.
    + @No__t_4 파이프라인에서 `PythonScriptStep`, `EstimatorStep` 및 Azure Machine Learning에 대 한 입력으로 `FileDataset` 및 `TabularDataset`를 사용할 수 있습니다.
    + 많은 수의 파일이 있는 폴더에 대 한 `FileDataset.mount`의 성능이 향상 되었습니다.
    + 실행 세부 정보에 알려진 오류 권장 사항에 대 한 URL이 추가 되었습니다.
    + 실행 중인 버그를 수정 했습니다. 실행에 자식이 너무 많이 있는 경우 요청이 실패 하는 메트릭을 가져옵니다 (_s).
    + Arcadia 클러스터에서 인증에 대 한 지원이 추가 되었습니다.
    + 실험 개체를 만들면 실행 기록 추적을 위한 Azure Machine Learning 작업 영역에서 실험을 가져오거나 만듭니다. 실험 id 및 보관 된 시간은 생성 시 실험 개체에 채워집니다. 예: 실험 = 실험 (작업 영역, "New 실험") experiment_id = experiment.id archive () 및 다시 활성화 ()는 실험에서 실험을 숨기고 복원 하 여 실험에 표시 되지 않도록 하 고 호출에서 기본적으로 반환 되는 함수입니다. 실험을 나열 합니다. 보관 된 실험과 동일한 이름으로 새 실험을 만든 경우 새 이름을 전달 하 여 다시 활성화할 때 보관 된 실험의 이름을 바꿀 수 있습니다. 지정 된 이름을 가진 활성 실험은 하나만 있을 수 있습니다. 예: experiment1 = 실험 (작업 영역, "활성 실험") experiment1 () # 보관 된와 동일한 이름을 사용 하 여 새 활성 실험을 만듭니다. experiment2. = 실험 (작업 영역, "활성 실험") experiment1 (new_name = "이전 활성 실험") 실험에서 정적 메서드 목록 ()은 이름 필터 및 ViewType 필터를 사용할 수 있습니다. ViewType 값은 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL" 예: archived_experiments = (workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = 실험 (작업 영역, 이름 = "First 실험", view_type = "ALL")
    + 모델 배포 및 서비스 업데이트를 위한 환경 사용 지원
  + **azureml-datadrift**
    + Datadriftbariclass의 show 특성은 선택적 인수 ' with_details '을 더 이상 지원 하지 않습니다. 표시 특성은 기능 열의 데이터 드리프트 계수 및 데이터 드리프트 기여도만 제공 합니다.
    + DataDriftDetector 특성 ' get_output ' 동작 변경:
      + 입력 매개 변수 start_time, end_time는 필수 항목이 아닌 선택적 요소입니다.
      + 입력 및/또는 end_time를 특정 run_id와 동일한 호출에 배치 하면 함께 사용할 수 없으므로 값 오류 예외가 발생 합니다. 
      + 입력 특정 start_time 및/또는 end_time을 통해 예약 된 실행 결과만 반환 됩니다. 
      + ' Daily_latest_only ' 매개 변수는 사용 되지 않습니다.
    + 데이터 집합 기반 데이터 드리프트 출력 검색을 지원 합니다.
  + **azureml-설명-모델**
    + Azureml-설명 모델의 이름을 AzureML로 해석 하 여 이전 패키지를 현재 이전 버전과의 호환성을 유지 합니다.
    + ExplanationClient에서 다운로드 시 기본적으로 회귀 대신 분류 태스크로 설정 된 원시 설명이 포함 된 automl 버그를 수정 했습니다.
    + @No__t_1를 사용 하 여 직접 만들 `ScoringExplainer`에 대 한 지원을 추가 합니다.
  + **azureml 파이프라인-코어**
    + 대량 파이프라인 생성을 위한 향상 된 성능
  + **azureml-교육-코어**
    + TensorFlow 평가기에서 TensorFlow 2.0 지원을 추가 했습니다.
  + **azureml-학습-automl**
    + 오케스트레이션이 이미 처리 하 고 있으므로 설치 반복에 실패 하는 경우 부모 실행은 더 이상 실패 하지 않습니다.
    + AutoML 실험에 대 한 로컬 docker 및 로컬 conda 지원 추가


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역에 대 한 새로운 웹 환경 (미리 보기)

데이터 과학자에서 실험을 보다 효율적으로 모니터링할 수 있도록 [새 작업 영역 포털](http://ml.azure.com) 의 실험 탭이 업데이트 되었습니다. 다음 기능을 탐색할 수 있습니다.
+ 실험 목록을 쉽게 필터링 하 고 정렬 하는 실험 메타 데이터
+ 실행을 시각화 하 고 비교 하는 데 사용할 수 있는 간소화 된 고성능 실험 세부 정보 페이지
+ 학습 실행을 이해 하 고 모니터링 하기 위해 세부 정보 페이지를 실행 하는 새로운 디자인

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK for Python v 1.0.65

  + **새로운 기능**
    + 큐 레이트 환경을 추가 했습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성 되었으며 더 빠른 실행을 위해 Docker 이미지로 미리 빌드 및 캐시 되었습니다. 기본적으로 작업 영역의 환경 목록에 접두사 "AzureML"와 함께 표시 됩니다.
  
  + **azureml-학습-automl**
    + ADB 및 HDI에 대 한 ONNX 변환 지원을 추가 했습니다.

+ **미리 보기 기능**  
  + **azureml-학습-automl**
    + 지원 되는 BERT 및 BiLSTM as text featurizer (미리 보기 전용)
    + 열 용도 및 변환기 매개 변수에 대해 지원 되는 기능화 사용자 지정 (미리 보기 전용)
    + 사용자가 학습 중에 모델 설명을 사용 하도록 설정 하는 경우 지원 되는 원시 설명 (미리 보기 전용)
    + 학습 가능 파이프라인으로 시계열 예측에 대 한 Prophet 추가 됨 (미리 보기 전용)
  
  + **azureml-datadrift**
    + Azureml에서 재배치 되는 패키지-datadrift-datadrift; 이후 릴리스에서는이 패키지를 제거 합니다. 

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoMLConfig 및 AutoMLBaseSettings에 FeaturizationConfig를 도입 했습니다.
      + 지정 된 열 및 기능 유형으로 기능화의 열 용도를 재정의 합니다.
      + 변환기 매개 변수 재정의
    + Explain_model () 및 retrieve_model_explanations ()에 대 한 사용 중단 메시지를 추가 했습니다.
    + 학습 가능 파이프라인으로 Prophet 추가 됨 (미리 보기 전용)
    + 대상 지연, 롤링 창 크기 및 최대 가로선의 자동 검색에 대 한 지원이 추가 되었습니다. Target_lags, target_rolling_window_size 또는 max_horizon 중 하나를 ' 자동 '으로 설정 하면 학습 데이터를 기반으로 해당 매개 변수의 값을 예측 하기 위해 추론을 적용 합니다.
    + 예측 수정-데이터 집합에 하나의 그레인 열이 포함 된 경우이 수준은 숫자 유형이 며 학습 및 테스트 집합 사이에 간격이 있습니다.
    + 예측 작업에서의 원격 실행에서 중복 된 인덱스에 대 한 오류 메시지를 수정 했습니다.
    + 데이터 집합이 불균형 하 게 분산 되었는지 여부를 확인 하는 guardrail이 추가 되었습니다. 이 경우 guardrail 메시지가 콘솔에 기록 됩니다.
  + **azureml-코어**
    + 모델 개체를 통해 저장소에서 모델에 대 한 SAS URL을 검색 하는 기능을 추가 했습니다. 예: model. get _sas_url ()
    + 전송 된 실행에 연결 된 데이터 집합을 가져오는 `run.get_details()['datasets']` 소개
    + API `Dataset.Tabular.from_json_lines_files` 추가 하 여 JSON 줄 파일에서 TabularDataset를 만듭니다. TabularDataset의 JSON 줄 파일에서이 테이블 형식 데이터에 대 한 자세한 내용은 설명서를 보려면 https://aka.ms/azureml-data 를 참조 하세요.
    + Supported_vmsizes () 함수에 추가 VM 크기 필드 (OS 디스크, Gpu 수)를 추가 했습니다.
    + List_nodes () 함수에 추가 필드를 추가 하 여 실행, 개인 및 공용 IP, 포트 등을 표시 합니다.
    + 클러스터를 만들 때 SSH 포트를 열어 둘지 여부에 따라 사용 또는 사용 안 함으로 설정할 수 있는 클러스터 프로 비전 중에 새 필드를 지정 하는 기능--remotelogin_port_public_access 지정 하지 않으면 서비스는 VNet 내에 클러스터를 배포 하 고 있는지 여부에 따라 포트를 열거나 현명 합니다.
  + **azureml-설명-모델**
    + 분류 시나리오의 설명 출력에 대 한 설명서를 개선 했습니다.
    + 평가 예제에 대 한 설명에 예측 된 y 값을 업로드 하는 기능이 추가 되었습니다. 더 유용한 시각화의 잠금을 해제 합니다.
    + 설명 속성을 MimicWrapper에 추가 하 여 기본 MimicExplainer를 가져올 수 있도록 합니다.
  + **azureml 파이프라인-코어**
    + 모듈, ModuleVersion 및 ModuleStep를 설명 하는 노트북을 추가 했습니다.
  + **azureml 파이프라인-단계**
    + AML 파이프라인을 통해 R 스크립트 실행을 지 원하는 RScriptStep 추가
    + "매개 변수 SubscriptionId가 지정 되지 않았습니다." 라는 오류 메시지를 발생 시킨 AzureBatchStep에서 메타 데이터 매개 변수 구문 분석이 수정 되었습니다.
  + **azureml-학습-automl**
    + 데이터 입력 형식으로 지원 되는 training_data, validation_data, label_column_name, weight_column_name
    + Explain_model () 및 retrieve_model_explanations ()에 대 한 사용 중단 메시지를 추가 했습니다.

  
## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK for Python v 1.0.62

+ **새로운 기능**
  + TabularDataset에 시계열 특성을 도입 했습니다. 이러한 특성을 사용 하 여 TabularDataset 데이터에 대 한 쉽게 타임 스탬프를 필터링 할 수 있습니다. 예를 들어 시간 범위 또는 최신 데이터의 모든 데이터를 가져올 수 있습니다. TabularDataset에서이 시계열 특성에 대해 알아보려면 https://aka.ms/azureml-data 설명서를 참조 하거나 https://aka.ms/azureml-tsd-notebook 을 참조 하 여 예를 들어 보세요. 
  + TabularDataset 및 FileDataset으로 학습을 사용 하도록 설정 했습니다. 예제 노트북은 https://aka.ms/dataset-tutorial 을 참조 하세요. 
  
  + **azureml-교육-코어**
    + PyTorch 평가기에서 Nccl 및 Gloo 지원이 추가 되었습니다.
  
+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoML 설정 ' lag_length ' 및 LaggingTransformer를 사용 하지 않습니다.
    + 데이터 흐름 형식으로 지정 된 경우 입력 데이터의 올바른 유효성 검사를 수정 했습니다.
    + Fit_pipeline py를 수정 하 여 그래프 json을 생성 하 고 아티팩트에 업로드 합니다. 
    + Cytoscape를 사용 하 여 userrun에서 그래프를 렌더링 했습니다.
  + **azureml-코어**
    + ADB 코드에서 예외 처리를 확인 하 고 새 오류 처리에 따라를 변경 합니다.
    + 노트북 Vm에 대 한 자동 MSI 인증을 추가 했습니다.
    + 실패 한 재시도로 인해 손상 되었거나 비어 있는 모델을 업로드할 수 있는 버그를 수정 합니다.
    + @No__t_1 모드가 변경 될 때 `DataReference` 이름이 변경 되는 버그를 수정 했습니다 (예: `as_upload`, `as_download` 또는 `as_mount`를 호출 하는 경우).
    + @No__t_2 및 `FileDataset.download`에 대해 `mount_point` 하 고 선택적으로 `target_path` 합니다.
    + 할당 된 타임 스탬프 열 없이 시간 serials 관련 API를 호출 하거나 할당 된 타임 스탬프 열을 삭제 한 경우에는 타임 스탬프 열을 찾을 수 없습니다. 예외가 throw 됩니다.
    + Serials 열에는 형식이 Date 인 열이 할당 되어야 합니다. 그렇지 않으면 예외가 예상 됩니다.
    + ' With_timestamp_columns ' API를 할당 하는 시간 serials 열에는 None 값을 사용 하 여 이전에 할당 된 타임 스탬프 열을 지울 수 있습니다.
    + 목록 삭제 시 타임 스탬프 열을 제외 하 고 삭제할 수 있는 사용자에 대 한 표시를 사용 하 여 with_time_stamp를 호출 하거나 해제 하는 값이 없는 값을 해제 하 여 릴리스 타임 스탬프를 해제 한 후에 예외가 발생 합니다. 세로
    + 열 목록 유지 또는 없음을 사용 하 여 with_time_stamp 호출에서 timestamp 열을 포함 한 후에 수행할 수 있는 사용자에 대 한 표시가 있는 열을 열 목록에 포함 목록에 포함 되지 않은 경우 예외가 발생 합니다. 타임 스탬프 열을 해제 하는 값입니다.
    + 등록 된 모델의 크기에 대 한 로깅을 추가 했습니다.
  + **azureml-설명-모델**
    + "패키지" python 패키지가 설치 되지 않은 경우 콘솔에 수정 된 경고가 발생 했습니다. "lightgbm의 지원 되는 버전 보다 오래 된 버전을 사용 하려면 2.2.1 보다 큰 버전으로 업그레이드 하세요."
    + 다양 한 기능을 갖춘 글로벌 설명에 대 한 분할를 사용 하 여 다운로드 모델 설명 수정
    + 출력 설명에 대 한 설명 누락 된 초기화 예제를 수정 했습니다.
    + 두 가지 모델 유형을 사용 하는 설명 클라이언트를 사용 하 여 업로드할 때 설정 속성에 대 한 변경할 수 없는 오류 해결
    + Get_raw 매개 변수를 추가 하 여 설명 ()를 계산 하 고, 하나의 점수 매기기 설명는 엔지니어링 된 값과 원시 값을 모두 반환할 수 있습니다.
  + **azureml-학습-automl**
    + Automl의 지원 설명을 위한 automl의 공용 Api를 도입 했습니다. automl 기능화를 분리 하 여 AutoML 설명을 지원 하 고, 자동 ml 용 azureml 설명 SDK의 SDK 통합 원시 설명 지원을 설명 합니다. 모델인.
    + 원격 학습 환경에서 azureml 기본값 제거
    + FileCacheStore의 기본 캐시 저장소 위치를 AzureFileCacheStore에서 AutoML on AzureDatabricks 코드 경로로 변경 했습니다.
    + 데이터 흐름 형식으로 지정 된 경우 입력 데이터의 올바른 유효성 검사를 수정 했습니다.
  + **azureml-교육-코어**
    + Source_directory_data_store 사용 중단을 되돌립니다.
    + Azureml 설치 패키지 버전을 재정의 하는 기능이 추가 되었습니다. 
    + 추정의 `environment_definition` 매개 변수에 dockerfile 지원이 추가 되었습니다.
    + 추정의 단순화 된 분산 교육 매개 변수
         ```py 
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer 
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역에 대 한 새로운 웹 환경 (미리 보기) 
새로운 웹 환경을 통해 데이터 과학자 및 데이터 엔지니어가 prepping에서 종단 간 기계 학습 수명 주기를 완료 하 고 데이터를 시각화 하 여 단일 위치에서 모델을 학습 하 고 배포할 수 있습니다. 

![Azure Machine Learning 작업 영역 UI (미리 보기)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**주요 기능:**

이제이 새로운 Azure Machine Learning 인터페이스를 사용 하 여 다음을 수행할 수 있습니다.
+ 노트북 관리 또는 Jupyter에 연결
+ [자동화 된 ML 실험 실행](tutorial-first-experiment-automated-ml.md)
+ [로컬 파일, datastores, & 웹 파일에서 데이터 집합 만들기](how-to-create-register-datasets.md)
+ 모델을 만들기 위한 데이터 집합 준비 & 탐색
+ 모델에 대 한 데이터 드리프트 모니터링 
+ 대시보드에서 최근 리소스 보기

이 릴리스 시점에는 Chrome, Firefox, Safari 및 Microsoft Edge Preview 브라우저가 지원 됩니다.

**알려진 문제:**

1. "문제가 발생 했습니다." 라는 메시지가 표시 되 면 브라우저를 새로 고칩니다. 배포가 진행 중일 때 "청크 파일을 로드 하는 동안 오류가 발생 했습니다.  

1. 노트북 및 파일에서 파일을 삭제 하거나 이름을 바꿀 수 없습니다. 공개 미리 보기 중에는 노트북 VM에서 Jupyter UI 또는 터미널을 사용 하 여 업데이트 파일 작업을 수행할 수 있습니다. 탑재 된 네트워크 파일 시스템 이므로, 노트북 VM에서 변경한 내용은 모두 노트북 작업 영역에 즉시 반영 됩니다. 

1. 노트북 VM으로 SSH를 수행 하려면:
   1. VM을 설치 하는 동안 생성 된 SSH 키를 찾습니다. 또는 Azure ML Azure Portal에서 키를 찾고, 목록에서 노트북 VM을 찾고, 목록에서 노트북 VM을 찾고, 속성: 대화 상자에서 키를 복사 > > > 합니다.
   1. 이러한 공개 및 개인 SSH 키를 로컬 컴퓨터로 가져옵니다.
   1. 이를 사용 하 여 노트북 VM에 SSH 합니다. 

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK for Python v 1.0.60

+ **새로운 기능**
  + 에는 데이터 저장소 또는 public url의 단일 또는 여러 파일을 참조 하는 filedataset이 도입 되었습니다. 파일은 모든 형식일 수 있습니다. FileDataset은 계산에 파일을 다운로드 하거나 탑재 하는 기능을 제공 합니다. FileDataset에 대해 알아보려면 https://aka.ms/file-dataset 을 참조 하세요.
  + PythonScript Step, Adla Step, Databricks Step, DataTransferStep 및 AzureBatch 단계에 대 한 파이프라인 Yaml 지원 추가 됨

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + 이제 AutoArima는 미리 보기용 으로만 suggestable 파이프라인입니다.
    + 예측에 대 한 오류 보고 기능이 향상 되었습니다.
    + 예측 작업에서 제네릭이 아닌 사용자 지정 예외를 사용 하 여 로깅이 향상 되었습니다.
    + Max_concurrent_iterations에 대 한 검사를 전체 반복 횟수 보다 작게 제거 했습니다.
    + AutoML 모델은 이제 AutoMLExceptions을 반환 합니다.
    + 이 릴리스는 자동화 된 machine learning 로컬 실행의 실행 성능을 향상 시킵니다.
  + **azureml-코어**
    + 데이터 집합 소개. (작업 영역) get (작업 영역)을 반환 합니다 .이를 통해 `TabularDataset` 사전을 반환 하 고 등록 이름으로 키가 지정 된 개체 `FileDataset`. 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + @No__t_1 및 `Dataset.Tabular.from_parquet.files`에 `parition_format` 인수를 소개 합니다. 각 데이터 경로의 파티션 정보는 지정 된 형식에 따라 열로 추출 됩니다. ' {column_name} '은 (는) 문자열 열을 만들며 ' {column_name: yyyy/MM/dd/HH/MM/ss} '는 datetime 열을 만듭니다. 여기에서 ' yyyy ', ' MM ', ' dd ', ' HH ', ' mm ' 및 ' ss '는 datetime 형식의 연도, 월, 일, 시간 Partition_format는 파일 경로가 끝날 때까지 첫 번째 파티션 키의 위치부터 시작 해야 합니다. 예를 들어 '.. 경로를 지정 합니다. /USA/2019/01/01/data.csv ' 파티션이 국가 및 시간별 이며, partition_format = '/{Country}/{PartitionDate: yyyy/MM/dd}/m a s t '는 값이 ' USA ' 인 ' Country ' 문자열 열과 ' 2019-01-01 ' 값이 포함 된 datetime 열을 만듭니다.
    + `to_csv_files` 및 `to_parquet_files` 메서드가 `TabularDataset`에 추가 되었습니다. 이러한 메서드는 데이터를 지정 된 형식의 파일로 변환 하 여 `TabularDataset`과 `FileDataset` 사이의 변환을 가능 하 게 합니다.
    + 모델인 ()에서 생성 된 Dockerfile을 저장할 때 기본 이미지 레지스트리에 자동으로 로그인 합니다.
    + ' gpu_support '은 더 이상 필요 하지 않습니다. 이제 AzureML에서 nvidia docker 확장을 자동으로 검색 하 고 사용 합니다 (사용 가능한 경우). 이후 릴리스에서 제거 될 예정입니다.
    + PipelineDrafts을 만들고, 업데이트 하 고, 사용할 수 있는 지원이 추가 되었습니다.
    + 이 릴리스는 자동화 된 machine learning 로컬 실행의 실행 성능을 향상 시킵니다.
    + 사용자는 이름으로 실행 기록에서 메트릭을 쿼리할 수 있습니다.
    + 예측 작업에서 제네릭이 아닌 사용자 지정 예외를 사용 하 여 로깅이 향상 되었습니다.
  + **azureml-설명-모델**
    + 새 MimicWrapper에 feature_maps 매개 변수를 추가 하 여 사용자가 원시 기능 설명을 가져올 수 있도록 합니다.
    + 이제 설명 업로드에 대 한 데이터 집합 업로드가 기본적으로 해제 되어 있으며 upload_datasets = True를 사용 하 여 다시 활성화할 수 있습니다.
    + 설명 목록에 "is_law" 필터링 매개 변수를 추가 하 고 함수를 다운로드 합니다.
    + 전역 및 로컬 설명 개체 모두에 메서드 `get_raw_explanation(feature_maps)`를 추가 합니다.
    + 아래 지원 되는 버전의 경우 인쇄 된 경고로 lightgbm에 버전 검사가 추가 되었습니다.
    + 일괄 처리에 대 한 설명을 일괄 처리할 때 최적화 된 메모리 사용
    + AutoML 모델은 이제 AutoMLExceptions을 반환 합니다.
  + **azureml 파이프라인-코어**
    + PipelineDrafts 만들기, 업데이트 및 사용에 대 한 추가 지원은 변경 가능한 파이프라인 정의를 유지 관리 하 고 대화형으로 실행 하는 데 사용할 수 있습니다.
  + **azureml-학습-automl**
    + 원격 python 런타임 환경에서 BERT/XLNet을 사용 하도록 설정 하는 데 필요한 특정 버전의 gpu 지원 pytorch v 1.1.0, udda toolkit 9.0, pytorch를 설치 하는 기능을 만들었습니다.
  + **azureml-교육-코어**
    + 서버 쪽 대신 sdk에서 직접 일부 하이퍼 매개 변수 공간 정의 오류가 발생 합니다.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.14
+ **버그 수정 및 향상 된 기능**
  + 원시 경로 및 자격 증명을 사용 하 여 ADLS/ADLSGen2에 쓰기가 가능 합니다.
  + @No__t_1에 대해 `include_path=True` 작동 하지 않는 버그를 수정 했습니다.
  + "잘못 된 속성 값: hostSecret" 예외가 발생 하 여 `to_pandas_dataframe()` 오류가 수정 되었습니다.
  + Spark 모드의 DBFS에서 파일을 읽을 수 없는 버그를 수정 했습니다.
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK for Python v 1.0.57
+ **새로운 기능**
  + AutomatedML에서 사용 되는 `TabularDataset`을 사용 합니다. @No__t_0에 대 한 자세한 내용은 https://aka.ms/azureml/howto/createdatasets 를 참조 하세요.
  
+ **버그 수정 및 향상 된 기능**
  + **automl-nativeclient**
    + 학습 및/또는 유효성 검사 레이블 (y 및 y_valid)이 pandas 데이터 프레임 형식으로 제공 될 때 발생 하는 오류를 수정 했지만 numpy array로 제공 되지 않습니다.
    + 데이터 및 `AutoMLBaseSettings` 개체만 필요로 하는 `RawDataContext`을 만들기 위한 인터페이스를 업데이트 했습니다.
    +  자동 Ml 사용자가 예측 시 충분 하지 않은 학습 시리즈를 삭제할 수 있습니다. -자동 Ml 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직를 삭제할 수 있습니다.
  + **azure-cli-ml**
    + 이제 Microsoft에서 생성 한 및 고객 인증서 모두 AKS 클러스터에 배포 된 점수 매기기 끝점에 대 한 SSL 인증서를 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + 레이블이 누락 된 행이 제대로 제거 되지 않은 AutoML에서 문제가 해결 되었습니다.
    + AutoML에서 오류 로깅이 개선 됨 이제 전체 오류 메시지는 항상 로그 파일에 기록 됩니다.
    + AutoML은 `azureml-defaults`, `azureml-explain-model` 및 `azureml-dataprep`를 포함 하도록 패키지 고정을 업데이트 했습니다. AutoML은 패키지 불일치 (`azureml-train-automl` 패키지 제외)에 대해 더 이상 경고 하지 않습니다.
    + Cv 분할 크기가 동일 하지 않아 bin 계산이 실패 하는 시계열의 문제를 해결 했습니다.
    + 교차 유효성 검사 학습 유형에 대해 앙상블 반복을 실행할 때 전체 데이터 집합에 대해 학습 된 모델을 다운로드 하는 데 문제가 있는 경우 모델 가중치와 투표에 제공 된 모델 간에 불일치가 발생 했습니다. 앙상블.
    + 학습 및/또는 유효성 검사 레이블 (y 및 y_valid)이 pandas 데이터 프레임 형식으로 제공 될 때 발생 하는 오류를 수정 했지만 numpy array로 제공 되지 않습니다.
    + 입력 테이블의 부울 열에서 아무것도 발견 되지 않은 경우 예측 태스크에 대 한 문제를 해결 했습니다.
    + 자동 Ml 사용자가 예측 시 충분 하지 않은 학습 시리즈를 삭제할 수 있습니다. -자동 Ml 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직를 삭제할 수 있습니다.
  + **azureml-코어**
    + Blob_cache_timeout 매개 변수 순서 지정 문제를 해결 했습니다.
    + 외부 맞춤 및 변환 예외 유형을 시스템 오류에 추가 했습니다.
    + 원격 실행에 대 한 Key Vault 암호에 대 한 지원이 추가 되었습니다. 작업 영역과 연결 된 keyvault의 암호를 추가, 가져오기 및 나열 하려면 azureml를 추가 합니다. 지원 되는 작업은 다음과 같습니다.
      + azureml. 작업 영역입니다. get _default_keyvault ()
      + azureml. keyvault. Keyvault. set secret (name, value)
      + azureml. 키 자격 증명 모음. 암호 설정 (_s) (secrets_dict)
      + azureml. 키 자격 증명 모음. 암호 가져오기 (이름) (_s)
      + azureml. 키 자격 증명 모음. 암호 가져오기 (secrets_list) (_s)
      + list_secrets () (영문)
    + 원격 실행 중에 기본 키 자격 증명 모음을 가져오고 암호를 가져오는 추가 방법:
      + azureml. 작업 영역입니다. get _default_keyvault ()
      + azureml. 실행 합니다. get _secret (name)
      + secrets_list ()를 실행 합니다.
    + 전송-hyperdrive CLI 명령에 추가 재정의 매개 변수를 추가 했습니다.
    + API 호출의 안정성 향상은 일반적인 요청 라이브러리 예외에 대 한 다시 시도를 확장 합니다.
    + 제출 된 실행에서 실행을 제출 하기 위한 지원을 추가 합니다.
    + FileWatcher의 만료 된 SAS 토큰 문제를 수정 했습니다 .이로 인해 초기 토큰이 만료 된 후 파일의 업로드가 중지 됩니다.
    + 데이터 집합 python SDK에서 HTTP csv/tsv 파일 가져오기를 지원 합니다.
    + 작업 영역. setup () 메서드를 사용 하지 않습니다. 사용자에 게 create () 또는 get ()/from_config ()를 대신 사용 하는 경고 메시지가 표시 됩니다.
    + 환경을 추가 했습니다. 추가 _private_pip_wheel ()를 사용 하 여 개인 사용자 지정 python 패키지 (whl)를 작업 영역에 업로드 하 고이를 안전 하 게 사용 하 여 환경을 빌드하거나 구체화할 수 있습니다.
    + 이제 Microsoft에서 생성 한 및 고객 인증서 모두 AKS 클러스터에 배포 된 점수 매기기 끝점에 대 한 SSL 인증서를 업데이트할 수 있습니다.
  + **azureml-설명-모델**
    + 업로드에 대 한 설명에 모델 ID를 추가 하는 매개 변수를 추가 했습니다.
    + 메모리의 설명에 태그 지정 `is_raw` 추가 하 고 업로드 합니다.
    + Pytorch 패키지에 대 한 지원 및 테스트가 추가 되었습니다.
  + **azureml-opendatasets**
    + 자동 테스트 환경 검색 및 로깅을 지원 합니다.
    + 국가 및 zip을 기준으로 인구를 가져오도록 클래스를 추가 했습니다.
  + **azureml 파이프라인-코어**
    + 입력 및 출력 포트 정의에 레이블 속성을 추가 했습니다.
  + **azureml-원격 분석**
    + 잘못 된 원격 분석 구성을 수정 했습니다.
  + **azureml-학습-automl**
    + 설치 오류가 발생 하는 동안 오류가 발생 하 여 설치 실행에 대 한 "오류" 필드가 기록 되지 않아 부모 실행 "오류"로 저장 되지 않은 버그를 수정 했습니다.
    + 레이블이 누락 된 행이 제대로 제거 되지 않은 AutoML에서 문제가 해결 되었습니다.
    + 자동 Ml 사용자가 예측 시 충분 하지 않은 학습 시리즈를 삭제할 수 있습니다.
    + 자동 Ml 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직를 삭제할 수 있도록 허용 합니다.
    + 이제 AutoMLStep가 새 구성 매개 변수의 변경 또는 추가에 대 한 문제를 방지 하기 위해 automl config를 백 엔드에 전달 합니다.
    + AutoML 데이터 Guardrail은 현재 공개 미리 보기로 제공 됩니다. 사용자는 학습 후에 데이터 Guardrail 보고서 (분류/회귀 작업)를 보고 SDK API를 통해 액세스할 수 있습니다.
  + **azureml-교육-코어**
    + PyTorch 평가기에서 torch 1.2 지원을 추가 했습니다.
  + **azureml 위젯**
    + 분류 학습을 위한 향상 된 혼동 행렬 차트

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.12
+ **새로운 기능**
  + 이제 문자열 목록을 `read_*` 메서드에 대 한 입력으로 전달할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + Spark에서 실행 하는 경우 `read_parquet` 성능이 크게 향상 되었습니다.
  + 모호한 날짜 형식의 단일 열에 대 한 `column_type_builder` 실패 하는 문제를 해결 했습니다.

### <a name="azure-portal"></a>Azure Portal
+ **미리 보기 기능**
  + 이제 실행 정보 페이지에 대 한 로그 및 출력 파일 스트리밍을 사용할 수 있습니다. 이 파일은 미리 보기 토글이 설정 된 경우 실시간으로 업데이트를 스트리밍합니다.
  + 작업 영역 수준에서 할당량을 설정 하는 기능은 미리 보기로 릴리스됩니다. AmlCompute 할당량은 구독 수준에서 할당 되지만 이제 작업 영역 간에 해당 할당량을 배포 하 고 공평 하 게 공유 및 거 버 넌 스에 할당할 수 있습니다. 작업 영역의 왼쪽 탐색 모음에서 사용 **+ 할당량** 블레이드를 클릭 하 고 **할당량 구성** 탭을 선택 하기만 합니다. 작업 영역 이므로 작업 영역 수준에서 할당량을 설정할 수 있으려면 구독 관리자 여야 합니다. 연산의.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK for Python v 1.0.55

+ **새로운 기능**
  + 이제 AKS에 배포 된 점수 매기기 끝점에 대 한 호출에 대해 토큰 기반 인증이 지원 됩니다. 현재 키 기반 인증을 계속 지원 하 고 사용자가 다음 인증 메커니즘 중 하나를 한 번에 사용할 수 있습니다.
  + VNet (가상 네트워크) 뒤에 있는 blob 저장소를 데이터 저장소로 등록 하는 기능입니다.
  
+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + CV 분할에 대 한 유효성 검사 크기가 작고 회귀 및 예측에 대 한 잘못 된 예측 및 실제 차트를 생성 하는 버그를 수정 합니다.
    + 원격 실행에 대 한 예측 작업 로깅이 향상 되었습니다. 이제 실행이 실패 한 경우에는 포괄적인 오류 메시지와 함께 사용자가 제공 됩니다.
    + 전처리 플래그가 True 인 경우 시계열의 오류가 수정 되었습니다.
    + 일부 예측 데이터 유효성 검사 오류 메시지를 보다 효율적으로 수행할 수 있습니다.
    + 데이터 집합을 삭제 및/또는 지연 로드 하 여 자동 Ml 실행의 메모리 소비가 감소 합니다. 특히 프로세스 생성 사이에 있습니다.
  + **azureml-설명-모델**
    + 사용자가 모델 형식에 대 한 기본 자동 유추 논리를 재정의할 수 있도록 explainers에 model_task 플래그를 추가 했습니다.
    + 위젯 변경: nbextension에 자동으로 설치 되 고, 더 이상 사용 되지 않는 설치/사용 지원 설명이 글로벌 기능 중요도 (예: Permutative)로 제공 됩니다.
    + 대시보드 변경: 요약 페이지의 beeswarm 플롯 외에도, Box 플롯 및 바이올린 플롯-' rerendering ' 슬라이더에서 beeswarm 플롯을 훨씬 빠르게 수행할 수 있습니다.-k가 계산 된 방법을 설명 하는 유용한 메시지 데이터가 제공 되지 않음
  + **azureml-코어**
    + 모델 및 해당 종속성을 캡슐화 하는 Docker 이미지 및 Dockerfiles을 만들기 위한 package () 메서드를 추가 했습니다.
    + 환경 개체를 포함 하는 InferenceConfigs를 허용 하도록 로컬 webservices 업데이트 되었습니다.
    + 모델을 수정 했습니다. register () '. '로 잘못 된 모델을 생성 합니다. (현재 디렉터리에 대 한)는 model_path 매개 변수로 전달 됩니다.
    + Submit_child를 추가 합니다 .이 기능은 실행을 전송 된 자식 실행의 부모로 지정 하는 동안 실행을 미러링합니다.
    + Register_model에서 구성 옵션을 지원 합니다.
    + 기존 클러스터에서 JAR 작업을 실행할 수 있습니다.
    + 이제 instance_pool_id 및 cluster_log_dbfs_path 매개 변수를 지원 합니다.
    + 웹 서비스에 모델을 배포할 때 환경 개체 사용에 대 한 지원이 추가 되었습니다. 이제 환경 개체를 InferenceConfig 개체의 일부로 제공할 수 있습니다.
    + 새 지역에 대 한 appinsifht 매핑 추가-centralus-westus-northcentralus
    + 모든 데이터 저장소 클래스의 모든 특성에 대 한 설명서를 추가 했습니다.
    + @No__t_0에 blob_cache_timeout 매개 변수를 추가 했습니다.
    + Save_to_directory 및 load_from_directory 메서드를 azureml에 추가 했습니다.
    + CLI에 "az ml environment download" 및 "az ml environment register" 명령이 추가 되었습니다.
    + 환경을 추가 했습니다. _private_pip_wheel 메서드를 추가 합니다.
  + **azureml-설명-모델**
    + 데이터 집합 서비스 (미리 보기)를 사용 하 여 설명에 데이터 집합 추적을 추가 했습니다.
    + 10k에서 100로 글로벌 설명을 스트리밍할 때 기본 일괄 처리 크기를 줄였습니다.
    + 사용자가 모델 형식에 대 한 기본 자동 유추 논리를 재정의할 수 있도록 explainers에 model_task 플래그를 추가 했습니다.
  + **azureml-mlflow**
    + 중첩 된 디렉터리가 무시 되는 mlflow. build_image에서 버그가 수정 되었습니다.
  + **azureml 파이프라인-단계**
    + 기존 Azure Databricks 클러스터에서 JAR 작업을 실행 하는 기능이 추가 되었습니다.
    + DatabricksStep step에 대 한 support instance_pool_id 및 cluster_log_dbfs_path 매개 변수가 추가 되었습니다.
    + DatabricksStep 단계에서 파이프라인 매개 변수에 대 한 지원이 추가 되었습니다.
  + **azureml-학습-automl**
    + 앙상블 관련 파일에 대 한 docstrings를 추가 했습니다.
    + @No__t_0 및 `max_concurrent_iterations`에 대 한 더 적절 한 언어로 문서 업데이트
    + 원격 실행에 대 한 예측 작업 로깅이 향상 되었습니다. 이제 실행이 실패 한 경우에는 포괄적인 오류 메시지와 함께 사용자가 제공 됩니다.
    + 파이프라인 automlstep 노트북에서 get_data 제거 되었습니다.
    + 에서 automlstep의 dataprep 지원이 시작 되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.10

+ **새로운 기능**
  + 이제 특정 열에 대해 특정 검사기 (예: 히스토그램, 산 점도 등)를 실행 하도록 요청할 수 있습니다.
  + @No__t_0에 병렬 처리 인수를 추가 했습니다. True 이면 데이터가 메모리에 로드 되지만 실행은 병렬로 실행 됩니다. False 이면 실행은 스트리밍 이지만 단일 스레드는 됩니다.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK for Python v 1.0.53

+ **새로운 기능**
  + 이제 자동화 된 Machine Learning에서 원격 계산 대상의 교육 ONNX 모델을 지원 합니다.
  + Azure Machine Learning은 이제 이전 실행, 검사점 또는 모델 파일에서 학습을 다시 시작할 수 있는 기능을 제공 합니다.
    + [추정를 사용 하 여 이전 실행에서 학습을 다시 시작](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) 하는 방법을 알아봅니다.

+ **버그 수정 및 향상 된 기능**
  + **automl-nativeclient**
    + 변환 후 (버그 연결) 끊어질 열 형식에 대 한 버그 수정 
    + Y_query는 begin (#459519)에서 None (s)을 포함 하는 개체 유형이 될 수 있습니다.
  + **azure-cli-ml**
    + CLI 명령 "모델 배포" 및 "서비스 업데이트"는 이제 매개 변수, 구성 파일 또는 둘의 조합을 허용 합니다. 매개 변수는 파일의 특성 보다 우선 합니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + NimbusML 종속성을 1.2.0 버전 (현재 최신 버전)으로 업데이트 합니다.
    + AutoML 추정 내에서 사용할 Nimbus ML 추정 & 파이프라인에 대 한 지원을 추가 합니다.
    + 앙상블 선택 프로시저에서 점수가 일정 하 게 유지 된 경우에도 결과 앙상블를 불필요 하 게 증가 시키는 버그를 수정 합니다.
    + 예측 작업에 대해 CV 분할에서 일부 featurizations를 다시 사용 하도록 설정 합니다. 이는 지연 및 롤링 창과 같은 고가의 featurizations에 대 한 n_cross_validations의 대략적인 비율을 통해 설치 프로그램을 실행 하는 데 걸리는 시간을 단축 합니다.
    + 시간이 pandas 지원 되는 시간 범위를 벗어난 경우 문제를 해결 합니다. 이제 시간이 pd 보다 작은 경우 DataException이 발생 합니다. Timestamp. min 또는 pd 보다 큽니다. Timestamp. 최대
    + 이제 예측을 통해 학습 및 테스트 집합에서 서로 다른 빈도로 정렬할 수 있습니다. 예를 들어 "분기별 1 월부터 시작 하 고" 분기별 10 월에 시작 "은 정렬할 수 있습니다.
    + "Parameters" 속성이 TimeSeriesTransformer에 추가 되었습니다.
    + 이전 예외 클래스를 제거 합니다.
    + 예측 작업에서 `target_lags` 매개 변수는 이제 단일 정수 값 이나 정수 목록을 허용 합니다. 정수를 제공한 경우에는 한 개의 지연만 생성 됩니다. 목록이 제공 되는 경우에는 고유한 지연 값이 사용 됩니다. target_lags = [1, 2, 2, 4]은 1, 2, 4 기간의 지연 시간을 만듭니다.
    + 변환 후 열 유형 손실에 대 한 버그 수정 (버그 링크)
    + @No__t_0에서 y_query는 begin (#459519)에서 None (s)을 포함 하는 개체 유형이 될 수 있습니다.
    + Automl output에 예상 값 추가
  + **azureml-datadrift**
    +  데이터를 보강 때 azureml (azureml)를 포함 하는 예제 노트북의 향상 된 기능-opendatasets 및 성능 향상
  + **azureml-설명-모델**
    + Azureml의 원시 기능 중요도에 대 한 라임 설명의 수정 된 변환 인수-설명-모델 패키지
    + segmentations에 대 한 이미지 설명의 이미지 설명에 추가 되었습니다.
    + LimeExplainer에 대 한 scipy sparse 지원 추가
    + DecisionTreeExplainableModel의 실행 시간을 향상 시키기 위해 일괄 처리의 전역 설명을 스트리밍하는 데 include_local = False 인 경우 batch_size를 설명에 추가 합니다.
  + **azureml-featureengineering**
    + Set_featurizer_timeseries_params () 호출에 대 한 수정: dict 값 형식 변경 및 null 검사-시계열 featurizer에 대 한 전자 필기장 추가
    + NimbusML 종속성을 1.2.0 버전 (현재 최신 버전)으로 업데이트 합니다.
  + **azureml-코어**
    + AzureML CLI에서 dbfs 데이터 저장소를 연결 하는 기능이 추가 됨 
    + @No__t_0 시작 하는 경우 빈 폴더가 만들어지는 데이터 저장소 업로드를 사용 하 여 버그를 수정 했습니다 `/`
    + ServicePrincipalAuthentication에서 deepcopy 문제를 수정 했습니다.
    + CLI에 "az ml environment show" 및 "az ml environment list" 명령이 추가 되었습니다.
    + 이제 환경에서 이미 빌드된 base_image의 대 안으로 base_dockerfile 지정을 지원 합니다.
    + 사용 하지 않는 RunConfiguration 설정 auto_prepare_environment가 사용 되지 않는 것으로 표시 되었습니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있습니다.
    + 버그 수정: 이제 모델 및 이미지 삭제는 업스트림 종속성으로 인해 삭제가 실패할 경우 해당 개체에 종속 된 업스트림 개체를 검색 하는 방법에 대 한 자세한 정보를 제공 합니다.
    + 일부 환경에 대 한 작업 영역을 만들 때 발생 하는 배포에 대해 빈 기간을 인쇄 한 버그가 수정 되었습니다.
    + 작업 영역 만들기 오류 예외를 개선 했습니다. 사용자가 "작업 영역을 만들 수 없습니다. 찾을 수 없음 ... " 메시지를 표시 하 고 대신 실제 생성 오류를 표시 합니다.
    + AKS webservices에서 토큰 인증에 대 한 지원을 추가 합니다. 
    + @No__t_0 메서드를 `Webservice` 개체에 추가 합니다.
    + Machine learning 데이터 집합을 관리 하기 위해 CLI 지원이 추가 되었습니다.
    + 이제 `Datastore.register_azure_blob_container`는이 데이터 저장소에 대 한 캐시 만료를 사용 하도록 blobfuse의 탑재 매개 변수를 구성 하는 `blob_cache_timeout` 값 (초)을 선택적으로 사용 합니다. 기본값은 시간 제한이 없습니다. 즉, blob을 읽을 때 작업이 완료 될 때까지 로컬 캐시에 유지 됩니다. 대부분의 작업은이 설정을 선호 하지만, 일부 작업은 해당 노드에 적합 한 것 보다 큰 데이터 집합에서 더 많은 데이터를 읽어야 합니다. 이러한 작업의 경우이 매개 변수를 조정 하면 성공할 수 있습니다. 이 매개 변수를 튜닝할 때 주의 해야 합니다. 값을 너무 낮게 설정 하면 epoch에서 사용 된 데이터가 다시 사용 되기 전에 만료 될 수 있으므로 성능이 저하 될 수 있습니다. 즉, 모든 읽기는 로컬 캐시 대신 blob 저장소 (즉, 네트워크)에서 수행 되므로 학습 시간에 부정적인 영향을 줍니다.
    + 이제 등록 후 모델 설명을 올바르게 업데이트할 수 있습니다.
    + 이제 모델 및 이미지 삭제는 해당 개체에 종속 된 업스트림 개체에 대 한 자세한 정보를 제공 하 여 삭제를 실패 시킵니다.
    + Azureml mlflow를 사용 하 여 원격 실행의 리소스 사용률을 향상 시킵니다.
  + **azureml-설명-모델**
    + Azureml의 원시 기능 중요도에 대 한 라임 설명의 수정 된 변환 인수-설명-모델 패키지
    + LimeExplainer에 대 한 scipy sparse 지원 추가
    + 선형 모델을 설명 하는 shape 선형 설명 래퍼 뿐만 아니라 테이블 형식 설명에 다른 수준 추가
    + 모델 라이브러리 설명의 설명 모방의 경우 스파스 데이터 입력에 대해 include_local = False 인 경우 오류 해결
    + Automl output에 예상 값 추가
    + 원시 기능 중요도를 얻기 위해 변환 인수를 제공 했을 때 순열 기능 중요도 수정 됨
    + DecisionTreeExplainableModel의 실행 시간을 향상 시키기 위해 일괄 처리의 전역 설명을 스트리밍하는 데 include_local = False 인 경우 batch_size를 설명에 추가 합니다.
    + model explainability library의 경우 예측에 pandas 데이터 프레임 input이 필요한 explainers 고정 된 블랙 박스
    + @No__t_0 때때로 float가 있는 목록이 아니라 부동을 반환 하는 버그를 수정 했습니다.
  + **azureml-mlflow**
    + Mlflow의 성능을 향상 시킵니다. set 실험 (experiment_name)
    + Mlflow tracking_uri에 대해 InteractiveLoginAuthentication를 사용 하 여 버그 수정
    + Azureml mlflow를 사용 하 여 원격 실행의 리소스 사용률을 향상 시킵니다.
    + Azureml-mlflow 패키지의 설명서 개선
    + Mlflow .log ("my_dir")가 "< 아티팩트-경로 >" 대신 "my_dir/< 아티팩트-경로 >" 아래에 아티팩트를 저장 하는 패치 버그
  + **azureml-opendatasets**
    + 새로 도입 된 메모리 문제로 인해 opendatasets의 pyarrow을 이전 버전 (< 0.14.0)에 고정 합니다.
    +  Azureml를 azureml-opendatasets 집합으로 이동 합니다. -열려 있는 데이터 집합 클래스를 AML 작업 영역에 등록 하 고 AML 데이터 집합 기능을 원활 하 게 활용할 수 있습니다. -비 SPARK 버전에서 NoaaIsdWeather 보강 성능을 크게 개선 합니다.
  + **azureml 파이프라인-단계**
    + DBFS 데이터 저장소는 이제 DatabricksStep의 입력 및 출력에 대해 지원 됩니다.
    + 입력/출력과 관련 하 여 Azure Batch 단계에 대 한 업데이트 된 설명서입니다.
    + AzureBatchStep에서 *delete_batch_job_after_finish* default 값을 *true*로 변경 합니다.
  + **azureml-원격 분석**
    +  Azureml를 azureml-opendatasets 집합으로 이동 합니다. -열려 있는 데이터 집합 클래스를 AML 작업 영역에 등록 하 고 AML 데이터 집합 기능을 원활 하 게 활용할 수 있습니다. -비 SPARK 버전에서 NoaaIsdWeather 보강 성능을 크게 개선 합니다.
  + **azureml-학습-automl**
    + 실제 반환 형식을 반영 하 고 키 속성 검색에 대 한 추가 정보를 제공 하기 위해 get_output에 대 한 설명서를 업데이트 했습니다.
    + NimbusML 종속성을 1.2.0 버전 (현재 최신 버전)으로 업데이트 합니다.
    + Automl output에 예상 값 추가
  + **azureml-교육-코어**
    + 이제 문자열은 자동화 된 하이퍼 매개 변수 튜닝을 위한 계산 대상으로 수락 됩니다.
    + 사용 하지 않는 RunConfiguration 설정 auto_prepare_environment가 사용 되지 않는 것으로 표시 되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.9

+ **새로운 기능**
  + Http 또는 https url에서 직접 파일을 읽기 위한 지원이 추가 되었습니다.

+ **버그 수정 및 향상 된 기능**
  + 원격 원본에서 Parquet 데이터 집합을 읽으려고 할 때 (현재 지원 되지 않음) 오류 메시지가 개선 되었습니다.
  + ADLS Gen 2에서 Parquet 파일 형식에 쓸 때 버그를 수정 하 고 경로에서 ADLS Gen 2 컨테이너 이름을 업데이트 합니다.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>시각적 인터페이스
+ **미리 보기 기능**
  + 시각적 인터페이스에서 "R 스크립트 실행" 모듈을 추가 했습니다.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v 1.0.48

+ **새로운 기능**
  + **azureml-opendatasets**
    + **azureml-및 리브-opendatasets** 은 이제 **azureml-opendatasets**으로 사용할 수 있습니다. 이전 패키지는 계속 작동할 수 있지만 더 풍부한 기능과 향상 된 기능을 위해 **azureml-opendatasets** 을 사용 하는 것이 좋습니다.
    + 이 새 패키지를 사용 하면 열려 있는 데이터 집합을 AML 작업 영역에서 데이터 집합으로 등록 하 고 데이터 집합에서 제공 하는 모든 기능을 활용할 수 있습니다.
    + 또한 Pandas/SPARK 데이터 프레임로 개방형 데이터 집합을 사용 하는 것과 같은 기존 기능과 날씨와 같은 일부 데이터 집합에 대 한 위치 조인을 포함 합니다.

+ **미리 보기 기능**
    + 이제 HyperDriveConfig는 파이프라인을 사용 하 여 하이퍼 매개 변수 튜닝을 지원 하기 위해 파이프라인 개체를 매개 변수로 허용할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + **azureml-학습-automl**
    + 변환 후 열 유형 손실에 대 한 버그를 수정 했습니다.
    + Y_query가 시작 부분에 없음 (s)을 포함 하는 개체 형식일 수 있도록 버그를 수정 했습니다. 
    + 앙상블 선택 프로시저에서 점수가 일정 하 게 유지 된 경우에도 결과 앙상블를 불필요 하 게 증가 시키는 문제를 해결 했습니다.
    + AutoMLStep에서 whitelist_models 및 blacklist_models 설정에 대 한 문제를 해결 했습니다.
    + Azure ML 파이프라인의 컨텍스트에서 AutoML을 사용 하는 경우 전처리 사용을 방해 하는 문제를 해결 했습니다.
  + **azureml-opendatasets**
    + Azureml 이동-opendatasets 집합을 azureml-opendatasets으로 이동 했습니다.
    + AML 작업 영역에 등록 하 고 AML 데이터 집합 기능을 원활 하 게 활용할 수 있는 개방형 데이터 집합 클래스를 허용 합니다.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능이 크게 향상 되었습니다.
  + **azureml-설명-모델**
    + Interpretability 개체에 대 한 온라인 설명서가 업데이트 되었습니다.
    + DecisionTreeExplainableModel의 실행 시간을 향상 시키기 위해 일괄 처리에서 전역 설명을 스트리밍하는 데 include_local = False 인 경우 batch_size가 설명에 추가 되었습니다.
    + @No__t_0 때때로 부동 소수점이 포함 된 목록이 아니라 부동을 반환 하는 문제를 해결 했습니다.
    + 설명 모델 라이브러리에서 모방 설명의 automl 출력에 예상 값을 추가 했습니다.
    + 원시 기능 중요도를 얻기 위해 변환 인수를 제공 하면 순열 기능 중요도가 수정 되었습니다.
    + 모델 explainability library에 대 한 DecisionTreeExplainableModel의 실행 시간을 향상 시키기 위해 일괄 처리의 전역 설명을 스트리밍하는 데 include_local = False 인 경우 설명 batch_size 추가 되었습니다.
  + **azureml-코어**
    + AzureML CLI에서 dbfs 데이터 저장소를 연결 하는 기능이 추가 되었습니다.
    + @No__t_1으로 시작 `target_path` 경우 빈 폴더가 만들어지는 데이터 저장소 업로드 문제를 해결 했습니다.
    + 두 데이터 집합의 비교를 사용 합니다.
    + 이제 모델 및 이미지 삭제는 업스트림 종속성으로 인해 삭제가 실패할 경우 해당 개체에 종속 된 업스트림 개체를 검색 하는 방법에 대 한 자세한 정보를 제공 합니다.
    + Auto_prepare_environment에서 사용 되지 않는 RunConfiguration 설정을 사용 하지 않습니다.
  + **azureml-mlflow**
    + Azureml mlflow를 사용 하는 원격 실행의 리소스 사용률이 향상 되었습니다.
    + Azureml-mlflow 패키지의 설명서가 개선 되었습니다.
    + Mlflow .log ("my_dir")가 "아티팩트 경로" 대신 "my_dir/아티팩트 경로"로 아티팩트를 저장 하는 문제를 해결 했습니다.
  + **azureml 파이프라인-코어**
    + 모든 파이프라인 단계에 대 한 hash_paths 매개 변수는 더 이상 사용 되지 않으며 나중에 제거 될 예정입니다. Source_directory 또는. .gitignore에 나열 된 파일을 제외 하 고 기본적으로의 내용이 해시 됩니다.
    + RunConfiguration 통합을 준비 하 고 파이프라인에서 사용을 잠금 해제 하기 위해 추가로 변경 하 여 계산 형식 특정 모듈을 지 원하는 모듈 및 ModuleStep 향상을 계속 합니다.
  + **azureml 파이프라인-단계**
    + AzureBatchStep: 입력/출력과 관련 된 향상 된 설명서입니다.
    + AzureBatchStep: delete_batch_job_after_finish default 값이 true로 변경 되었습니다.
  + **azureml-교육-코어**
    + 이제 문자열은 자동화 된 하이퍼 매개 변수 튜닝을 위한 계산 대상으로 허용 됩니다.
    + Auto_prepare_environment에서 사용 되지 않는 RunConfiguration 설정을 사용 하지 않습니다.
    + 사용 되지 않는 매개 변수는 각각 `conda_dependencies_file` 및 `pip_requirements_file`을 위해 `conda_dependencies_file_path` 및 `pip_requirements_file_path`.
  + **azureml-opendatasets**
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능을 크게 개선 합니다.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.8

+ **새로운 기능**
 + 이제 데이터 흐름 개체를 반복 하 여 일련의 레코드를 생성할 수 있습니다. @No__t_0에 대 한 설명서를 참조 하세요.

+ **버그 수정 및 향상 된 기능**
 + DataPrep SDK의 견고성을 향상 시켰습니다.
 + 문자열이 아닌 열 인덱스를 사용 하는 pandas 데이터 프레임의 처리 기능이 향상 되었습니다.
 + 데이터 집합의 `to_pandas_dataframe` 성능이 향상 되었습니다.
 + 다중 노드 환경에서 실행 하는 경우 데이터 집합의 Spark 실행에 실패 하는 버그가 수정 되었습니다.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.7

Azure Databricks를 사용 하는 일부 고객에 게 문제가 발생 하 여 성능 향상에 대 한 변경 내용을 되돌렸습니다. Azure Databricks에서 문제가 발생 한 경우 아래 방법 중 하나를 사용 하 여 버전 1.1.7로 업그레이드할 수 있습니다.
1. 다음 스크립트를 실행 하 여 업그레이드: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 최신 데이터 준비 SDK 버전을 설치 하는 클러스터를 다시 만듭니다.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK for Python v 1.0.45

+ **새로운 기능**
  + Azureml-설명-모델 패키지에서 설명 모방 하는 의사 결정 트리 서로게이트 모델 추가
  + 추론 이미지에 설치할 VERDA 버전을 지정 하는 기능입니다. , 9.0, 9.1 및 10.0에 대 한 지원.
  + Azure ml 학습 기본 이미지에 대 한 정보는 이제 [AZURE Ml 컨테이너 GitHub 리포지토리](https://github.com/Azure/AzureML-Containers) 및 [dockerhub](https://hub.docker.com/_/microsoft-azureml) 에서 사용할 수 있습니다.
  + 파이프라인 일정에 대 한 CLI 지원을 추가 했습니다. 자세히 알아보려면 "az ml pipeline-h"를 실행 합니다.
  + AKS webservice 배포 구성 및 CLI에 사용자 지정 Kubernetes namespace 매개 변수를 추가 했습니다.
  + 모든 파이프라인 단계에 대해 사용 되지 않는 hash_paths 매개 변수
  + 모델. 이제 register는 `child_paths` 매개 변수를 사용 하 여 여러 개별 파일을 단일 모델로 등록할 수 있도록 지원 합니다.
  
+ **미리 보기 기능**
    + 이제 점수 매기기 explainers 더 안정적인 serialization 및 deserialization을 위해 conda 및 pip 정보를 저장할 수 있습니다.
    + 자동 기능 선택기에 대 한 버그 수정
    + 새 구현에 의해 노출 되는 새로운 api 및 패치 된 버그로 build_image 업데이트 되었습니다.

+ **버그 수정 및 향상 된 기능**
  + Azureml 코어에서 paramiko 종속성을 제거 했습니다. 레거시 계산 대상 연결 방법에 대 한 사용 중단 경고가 추가 되었습니다.
  + Create_children의 성능 향상
  + 이진 분류자를 사용 하는 설명 모방에서 교사 확률이 셰이프 값 크기 조정에 사용 되는 경우 확률의 순서를 수정 합니다.
  + 자동화 된 machine learning에 대 한 오류 처리 및 메시지가 개선 되었습니다. 
  + 자동화 된 machine learning에 대 한 반복 시간 제한 문제를 수정 했습니다.
  + 자동화 된 기계 학습에 대 한 시계열 변환 성능이 개선 되었습니다.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.6

+ **새로운 기능**
  + 상위 값 (`SummaryFunction.TOPVALUES`) 및 하위 값 (`SummaryFunction.BOTTOMVALUES`)에 대 한 요약 함수를 추가 했습니다.

+ **버그 수정 및 향상 된 기능**
  + @No__t_0 성능이 크게 향상 되었습니다.
  + 이진 파일을 가리키는 데이터 흐름에 대 한 `get_profile()` 실패할 수 있는 버그를 수정 했습니다.
  + 원격 분석 컬렉션을 프로그래밍 방식으로 사용 하거나 사용 하지 않도록 설정할 수 있는 `set_diagnostics_collection()` 노출 됩니다.
  + @No__t_0 동작이 변경 되었습니다. 이제 Pandas의 동작에 맞게 정렬 되는 Min, Mean, Std 및 Sum에 대해 NaN 값이 무시 됩니다.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK for Python v 1.0.43

+ **새로운 기능**
  + 이제 Azure Machine Learning는 인기 있는 기계 학습 및 데이터 분석 프레임 워크 Scikit에 대 한 최고 수준의 지원을 제공 합니다. 사용자는 [`SKLearn` 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)를 사용 하 여 Scikit 모델 학습 모델을 쉽게 학습 하 고 배포할 수 있습니다.
    + [Scikit를 사용 하 여 하이퍼 매개 변수 튜닝을 실행 하는 방법을 알아봅니다. 하이퍼 드라이브를 사용 하 여 알아봅니다](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + 재사용 가능한 계산 단위를 관리 하기 위해 모듈 및 ModuleVersion 클래스와 함께 파이프라인에서 ModuleStep를 만들기 위한 지원이 추가 되었습니다.
  + 이제 ACI webservices는 업데이트를 통해 영구적 scoring_uri 지원 합니다. Scoring_uri이 IP에서 FQDN으로 변경 됩니다. FQDN에 대 한 Dns 이름 레이블은 deploy_configuration의 dns_name_label을 설정 하 여 구성할 수 있습니다. 
  + 자동화 된 machine learning 새로운 기능:
    + 예측을 위한 STL featurizer
    + KMeans 기능 해제에 대해 클러스터링을 사용할 수 있음을 의미 합니다.
  + AmlCompute 할당량 승인이 빨라졌습니다. 이제 임계값 내에서 할당량 요청을 승인 하는 프로세스를 자동화 했습니다. 할당량의 작동 방식에 대 한 자세한 내용은 [할당량을 관리 하는 방법을](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)알아보세요.

+ **미리 보기 기능**
    + Azureml-mlflow 패키지를 통해 [Mlflow](https://mlflow.org) 1.0.0 추적과 통합 ([예: 노트북](https://aka.ms/azureml-mlflow-examples)).
    + Jupyter 노트북을 실행으로 제출 합니다. [API 참조 설명서](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Azureml-datadrift 패키지 ([예: 노트북](https://aka.ms/azureml-datadrift-example))를 통한 [데이터 드리프트 탐지기](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) 의 공개 미리 보기입니다. 데이터 드리프트는 시간이 지남에 따라 모델 정확성이 저하 되는 가장 중요 한 이유 중 하나입니다. 프로덕션 환경에서 모델에 제공 되는 데이터가 모델에서 학습 된 데이터와 다를 때 발생 합니다. AML 데이터 드리프트 탐지기는 고객이 데이터 드리프트를 모니터링 하 고 드리프트가 검색 될 때마다 경고를 보내는 데 도움이 됩니다. 

+ **주요 변경 내용**

+ **버그 수정 및 향상 된 기능**
  + RunConfiguration 로드 및 저장은 이전 동작에 대해 완전히 호환 되는 전체 파일 경로를 지정 하는 것을 지원 합니다.
  + ServicePrincipalAuthentication에 추가 된 캐싱이 기본적으로 해제 되어 있습니다.
  + 동일한 메트릭 이름에서 여러 플롯의 로깅을 사용 하도록 설정 합니다.
  + 이제 모델 클래스를 azureml (`from azureml.core import Model`)에서 올바르게 가져올 있습니다.
  + 파이프라인 단계에서 `hash_path` 매개 변수는 이제 사용 되지 않습니다. 새 동작은 amlignore 또는. .gitignore에 나열 된 파일을 제외 하 고 전체 source_directory를 해시 하는 것입니다.
  + 파이프라인 패키지에서 다양 한 `get_all` 및 `get_all_*` 메서드는 각각 `list` 및 `list_*`을 위해 더 이상 사용 되지 않습니다.
  + _run. get는 원래 실행 유형을 반환 하기 전에 더 이상 클래스를 가져올 필요가 없습니다.
  + WebService 업데이트에 대 한 일부 호출이 업데이트를 트리거하지 않은 문제를 해결 했습니다.
  + AKS webservices에 대 한 점수 매기기 제한은 5ms와 300000ms 사이 여야 합니다. 점수 매기기 요청에 허용 되는 최대 scoring_timeout_ms는 1 분에서 5 분으로 만큼 증가 되었습니다.
  + 이제 LocalWebservice 개체에 `scoring_uri` 및 `swagger_uri` 속성이 있습니다.
  + 이동 된 출력 디렉터리를 만들고 사용자 프로세스에서 디렉터리 업로드를 출력 합니다. 실행 기록 SDK를 모든 사용자 프로세스에서 실행할 수 있습니다. 이렇게 하면 분산 학습 실행에서 발생 하는 일부 동기화 문제를 해결 해야 합니다.
  + 사용자 프로세스 이름에서 작성 된 azureml 로그의 이름에는 이제 프로세스 이름 (분산 된 교육이 전용) 및 PID가 포함 됩니다.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.5

+ **버그 수정 및 향상 된 기능**
  + 2 자리 연도 형식이 포함 된 해석 된 datetime 값의 경우 유효한 연도의 범위가 Windows 릴리스와 일치 하도록 업데이트 되었습니다. 범위가 1930-2029에서 1950-2049로 변경 되었습니다.
  + 파일에서 읽고 `handleQuotedLineBreaks=True`을 설정 하면 `\r`이 새 줄로 처리 됩니다.
  + 일부 경우에 `read_pandas_dataframe` 실패 하는 버그를 수정 했습니다.
  + @No__t_0의 성능이 개선 되었습니다.
  + 향상 된 오류 메시지.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v 1.1.4

+ **새로운 기능**
  + 이제 다음 식 언어 함수를 사용 하 여 datetime 값을 추출 하 고 새 열로 구문 분석할 수 있습니다.
    + `RegEx.extract_record()`는 datetime 요소를 새 열로 추출 합니다.
    + `create_datetime()` 별도의 datetime 요소에서 datetime 개체를 만듭니다.
  + @No__t_0를 호출할 때 이제 변 위치 열에 (est.) 라는 레이블이 지정 되어 값이 근사치 임을 알 수 있습니다.
  + 이제 Azure Blob Storage에서 읽을 때 * * 와일드 카드 사용를 사용할 수 있습니다.
    + 예: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **버그 수정**
  + 원격 원본 (Azure Blob)에서 Parquet 파일 읽기와 관련 된 버그를 수정 했습니다.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK for Python v 1.0.39
+ **변경된 기능**
  + 실행 구성 auto_prepare_environment 옵션은 사용 되지 않으며 자동 준비를 통해 기본값이 됩니다.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.3

+ **새로운 기능**
  + Read_postgresql를 호출 하거나 데이터 저장소를 사용 하 여 PostgresSQL 데이터베이스에서 읽을 수 있는 지원이 추가 되었습니다.
    + 방법 가이드의 예제를 참조 하세요.
      + [데이터 수집 노트북](https://aka.ms/aml-data-prep-ingestion-nb)
      + [데이터 저장소 노트북](https://aka.ms/aml-data-prep-datastore-nb)

+ **버그 수정 및 향상 된 기능**
  + 열 형식 변환의 문제 해결:
  + 이제 부울 또는 숫자 열을 부울 열로 올바르게 변환 합니다.
  + 이제 날짜 열을 날짜 형식으로 설정 하려고 할 때 오류가 발생 하지 않습니다.
  + 향상 된 JoinType 형식 및 함께 제공 되는 참조 설명서입니다. 두 데이터 흐름를 조인할 때 이제 이러한 조인 유형 중 하나를 지정할 수 있습니다.
    + NONE, MATCH, INNER, UNMATCHLEFT, 왼쪽 앤티, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTANTI, FULLANTI, FULL입니다.
  + 더 많은 날짜 형식을 인식 하도록 추론 데이터 형식이 개선 되었습니다.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

Azure Portal에서 이제 다음을 수행할 수 있습니다.
+ 자동화 된 ML 실험 만들기 및 실행 
+ 샘플 Jupyter 노트북 또는 나만의 노트북을 사용해 볼 수 있는 노트북 VM을 만듭니다.
+ 자동화 된 Machine Learning, 시각적 인터페이스 및 호스트 된 노트북 Vm을 포함 하는 Machine Learning 서비스 작업 영역에서 새 제작 섹션 (미리 보기)
    + 자동 machine learning을 사용 하 여 자동으로 모델 만들기 
    + 끌어서 놓기 시각적 인터페이스를 사용 하 여 실험 실행
    + 노트북 VM을 만들어 데이터를 탐색 하 고, 모델을 만들고, 서비스를 배포 합니다.
+ 보고서 실행 및 실행 세부 정보 페이지에서 라이브 차트 및 메트릭 업데이트
+ 실행 세부 정보 페이지에서 로그, 출력 및 스냅숏에 대 한 파일 뷰어를 업데이트 했습니다.
+ 실험 탭에서 새롭게 향상 된 보고서 생성 환경을 제공 합니다. 
+ Azure Machine Learning 작업 영역의 개요 페이지에서 config.xml 파일을 다운로드 하는 기능이 추가 되었습니다.
+ Azure Databricks 작업 영역에서 Machine Learning 서비스 작업 영역 만들기 지원 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK for Python v 1.0.33
+ **새로운 기능**
  + 이제 _작업 영역. create_ 메서드는 CPU 및 GPU 클러스터에 대 한 기본 클러스터 구성을 허용 합니다.
  + 작업 영역을 만드는 데 실패 하면 종속 된 리소스가 정리 됩니다.
  + 기본 Azure Container Registry SKU가 basic으로 전환 되었습니다.
  + 실행 또는 이미지를 만드는 데 필요한 경우 Azure Container Registry 지연 됩니다.
  + 학습 실행 환경을 위한 지원.

### <a name="notebook-virtual-machine"></a>노트북 가상 머신 

기계 학습 실험을 프로그래밍 하 고, 모델을 웹 끝점으로 배포 하 고, Python을 사용 하 여 Azure Machine Learning SDK에서 지 원하는 모든 기타 작업을 수행할 수 있는 Jupyter 노트북에 대 한 안전한 엔터프라이즈급 호스팅 환경으로 노트북 VM을 사용 합니다. 다음과 같은 여러 기능을 제공 합니다.
+ 최신 버전의 Azure Machine Learning SDK 및 관련 패키지를 포함 하는 [미리 구성 된 노트북 VM](tutorial-1st-experiment-sdk-setup.md) 로 신속 하 게 실행 합니다.
+ 액세스는 HTTPS, Azure Active Directory 인증 및 권한 부여와 같은 입증 된 기술을 통해 보안이 유지 됩니다.
+ Azure Machine Learning 작업 영역 blob storage 계정에서 노트북 및 코드의 신뢰할 수 있는 클라우드 저장소입니다. 작업을 잃지 않고 노트북 VM을 안전 하 게 삭제할 수 있습니다.
+ Azure Machine Learning 기능을 탐색 하 고 시험해 볼 수 있는 미리 설치 된 샘플 노트북.
+ Azure Vm의 전체 사용자 지정 기능, 모든 VM 유형, 패키지, 모든 드라이버. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v 1.0.33가 릴리스 되었습니다.

+ [Fpgas](concept-accelerate-with-fpgas.md) Azure ML 하드웨어 가속 모델은 일반적으로 사용할 수 있습니다.
  + 이제 [azureml 가속 패키지를 사용](how-to-deploy-fpga-web-service.md) 하 여 다음을 수행할 수 있습니다.
    + 지원 되는 심층 신경망 (ResNet 50, ResNet 152, DenseNet-121, VGG-16 및 SSD-VGG)의 가중치를 학습 합니다.
    + 지원 되는 DNN로 전송 학습 사용
    + 모델 관리 서비스를 사용 하 여 모델 등록 및 모델 컨테이너 화
    + AKS (Azure Kubernetes Service) 클러스터에서 FPGA을 사용 하 여 Azure VM에 모델 배포
  + 컨테이너를 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) 서버 장치에 배포
  + 이 [샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models) 에서 grpc 끝점을 사용 하 여 데이터 점수 매기기

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

+ 성능 최적화를 위해 동적으로 featurizers을 추가할 수 있는 기능 스윕. New featurizers: work 포함, 증거의 무게, 대상 인코딩, 텍스트 대상 인코딩, 클러스터 거리
+ 자동화 된 ML 내에서 학습/유효한 분할을 처리 하는 스마트 CV
+ 몇 가지 메모리 최적화 변경 및 런타임 성능 향상
+ 모델 설명의 성능 향상
+ 로컬 실행에 대 한 ONNX 모델 변환
+ 추가 된 서브 샘플링 지원
+ 종료 조건이 정의 되지 않은 경우 지능형 중지
+ 누적 앙상블

+ 시계열 예측
  + 새 예측 예측 함수   
  + 이제 시계열 데이터에 대 한 롤링 원본 교차 유효성 검사를 사용할 수 있습니다.
  + 시계열 지연 구성에 추가 된 새로운 기능 
  + 롤링 창 집계 기능을 지원 하기 위해 추가 된 새 기능
  + 실험 설정에서 국가 코드가 정의 된 경우 새 휴일 검색 및 featurizer

+ Azure Databricks
  + 시계열 예측 및 모델 explainabilty/interpretability 기능 사용
  + 이제 자동화 된 ML 실험을 취소 하 고 다시 시작할 수 있습니다 (계속).
  + 다중 코어 처리에 대 한 지원 추가

### <a name="mlops"></a>MLOps
+ **점수 매기기 컨테이너에 대 한 로컬 배포 & 디버깅**<br/> 이제 ML 모델을 로컬로 배포 하 고 점수 매기기 파일 및 종속성을 빠르게 반복 하 여 예상 대로 동작 하는지 확인할 수 있습니다.

+ **InferenceConfig () &에 도입 되었습니다.**<br/> 모델 배포에서는 이제 .Runconfig와 동일한 항목 스크립트가 포함 된 원본 폴더를 지정할 수 있습니다.  또한 모델 배포는 단일 명령으로 간소화 되었습니다.

+ **Git 참조 추적**<br/> 고객은 종단 간 감사 내역을 유지 하는 데 도움이 되므로 일정 시간 동안 기본 Git 통합 기능을 요청 했습니다. Git 관련 메타 데이터 (리포지토리, 커밋, 정리 상태)에 대해 Azure ML의 주요 엔터티 간 추적을 구현 했습니다. 이 정보는 SDK 및 CLI에서 자동으로 수집 됩니다.

+ **모델 프로 파일링 & 유효성 검사 서비스**<br/> 고객은 유추 서비스와 관련 된 계산의 크기를 적절 하 게 조정 하는 데 자주 어려움이 있습니다. 모델 프로 파일링 서비스를 사용 하는 경우 고객은 샘플 입력을 제공할 수 있으며, 배포에 대 한 최적의 크기 조정을 결정 하기 위해 16 개의 다른 CPU/메모리 구성에서 프로 파일링 합니다.

+ **유추를 위한 고유한 기본 이미지 가져오기**<br/> 또 다른 일반적인 불만은 실험에서 유추 다시 공유 종속성으로 이동 하는 데 어려움이 있습니다. 이제 새로운 기본 이미지 공유 기능을 사용 하 여 유추를 위해 실험 기본 이미지, 종속성 및 모두를 재사용할 수 있습니다. 이렇게 하면 배포 속도가 향상 되 고 내부에서 외부 루프로의 간격이 줄어듭니다.

+ **향상 된 Swagger 스키마 생성 환경**<br/> 이전 swagger 생성 방법은 오류가 발생 하기 쉬우며 자동화할 수 없습니다. 데코레이터를 통해 Python 함수에서 swagger 스키마를 생성 하는 새로운 인라인 방법이 있습니다. 이 코드는 오픈 소스 이며, 스키마 생성 프로토콜은 Azure ML 플랫폼과 결합 되지 않습니다.

+ **GA (일반 공급) Azure ML CLI**<br/> 이제 단일 CLI 명령을 사용 하 여 모델을 배포할 수 있습니다. Jupyter 노트북에서 ML 모델을 배포 하지 않는 일반적인 고객 의견이 있습니다. [**CLI 참조 설명서**](https://aka.ms/azmlcli) 가 업데이트 되었습니다.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v 1.0.30가 릴리스 되었습니다.

[@No__t_1](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) 는 동일한 끝점을 유지 하면서 게시 된 파이프라인의 새 버전을 추가 하기 위해 도입 되었습니다.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.2

참고: 데이터 준비 Python SDK는 더 이상 `numpy` 및 `pandas` 패키지를 설치 하지 않습니다. [업데이트 된 설치 지침](https://aka.ms/aml-data-prep-installation)을 참조 하세요.

+ **새로운 기능**
  + 이제 피벗 변환을 사용할 수 있습니다.
    + 방법 가이드: [피벗 노트북](https://aka.ms/aml-data-prep-pivot-nb)
  + 이제 네이티브 함수에서 정규식을 사용할 수 있습니다.
    + 예시:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 이제 식 언어로  functions  and `to_lower` `to_upper`를 사용할 수 있습니다.
  + 이제 데이터 프로필의 각 열에 대 한 고유 값 수를 볼 수 있습니다.
  + 일반적으로 사용 되는 일부 판독기 단계에서는 이제 `infer_column_types` 인수를 전달할 수 있습니다. @No__t_0로 설정 된 경우 데이터 준비는 열 유형을 검색 하 고 자동으로 변환 하려고 합니다.
    + `inference_arguments`은 이제 사용 되지 않습니다.
  + 이제 `Dataflow.shape`을 호출할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + `keep_columns`  now는 추가 선택적 인수 `validate_column_exists`를 수락 하며이는 `keep_columns` 결과에 열이 포함 되는지 여부를 확인 합니다.
  + 모든 판독기 단계 (파일에서 읽기)는 이제 `verify_exists` 추가 선택적 인수를 수락 합니다.
  + Pandas 데이터 프레임에서 읽기의 성능 향상 및 데이터 프로필 가져오기.
  + 단일 인덱스를 사용 하 여 데이터 흐름에서 단일 단계를 조각화 하지 못한 버그가 수정 되었습니다.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + 이제 기존 원격 계산 클러스터에서 기존 스크립트 실행을 다시 제출할 수 있습니다. 
  + 이제 파이프라인 탭에서 새 매개 변수를 사용 하 여 게시 된 파이프라인을 실행할 수 있습니다. 
  + 이제 실행 세부 정보에서 새로운 스냅숏 파일 뷰어를 지원 합니다. 특정 실행을 제출할 때 디렉터리의 스냅숏을 볼 수 있습니다. 전송 된 노트북을 다운로드 하 여 실행을 시작할 수도 있습니다.
  + 이제 Azure Portal에서 부모 실행을 취소할 수 있습니다.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v 1.0.23

+ **새로운 기능**
  + Azure Machine Learning SDK는 이제 Python 3.7를 지원 합니다.
  + Azure Machine Learning DNN 추정는 이제 기본 제공 다중 버전 지원 기능을 제공 합니다. 예를 들어 `TensorFlow`  estimator에서 `framework_version` 매개 변수를 수락 하 고 사용자가 버전 ' 1.10 ' 또는 ' 1.12 '를 지정할 수 있습니다. 현재 SDK 릴리스에서 지원 되는 버전 목록을 보려면 원하는 프레임 워크 클래스 (예: `TensorFlow.get_supported_versions()`)에서 `get_supported_versions()`를 호출 합니다.
  최신 SDK 릴리스에서 지원 되는 버전 목록은 [DNN 평가기 설명서](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)를 참조 하세요.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.1

+ **새로운 기능**
  + Read_ * 변환을 사용 하 여 여러 개의 데이터 저장소/데이터 경로/DataReference 원본을 읽을 수 있습니다.
  + 열에 대해 다음 작업을 수행 하 여 나누기, 층, 모듈로, 거듭제곱, 길이 등의 새 열을 만들 수 있습니다.
  + 데이터 준비는 이제 Azure ML 진단 도구 모음에 포함 되어 있으며 기본적으로 진단 정보를 기록 합니다.
    + 이 기능을 해제 하려면이 환경 변수를 true로 설정 합니다. DISABLE_DPREP_LOGGER

+ **버그 수정 및 향상 된 기능**
  + 일반적으로 사용 되는 클래스 및 함수에 대 한 향상 된 코드 설명서입니다.
  + Excel 파일을 읽지 못한 auto_read_file의 버그를 수정 했습니다.
  + Read_pandas_dataframe에서 폴더를 덮어쓰는 옵션이 추가 되었습니다.
  + Dotnetcore2 종속성 설치의 성능이 향상 되었으며 Fedora 27/28 및 Ubuntu 1804에 대 한 지원이 추가 되었습니다.
  + Azure Blob에서 읽기 성능이 개선 되었습니다.
  + 열 형식 검색은 이제 Long 형식의 열을 지원 합니다.
  + 일부 날짜 값이 Python datetime 개체가 아닌 타임 스탬프로 표시 되는 버그를 수정 했습니다.
  + 일부 형식 수가 정수 대신 double로 표시 되는 버그를 수정 했습니다.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v 1.0.21

+ **새로운 기능**
  + *Create_children* 메서드를 사용 하면 한 번의 호출로 여러 자식 실행을 짧은 대기 시간으로 만들 수 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.0

+ **주요 변경 내용**
  + 데이터 준비 패키지의 개념은 더 이상 사용 되지 않으며 더 이상 지원 되지 않습니다. 하나의 패키지에 여러 데이터 흐름를 유지 하는 대신 데이터 흐름를 개별적으로 유지할 수 있습니다.
    + 방법 가이드: [데이터 흐름 노트북 열기 및 저장](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **새로운 기능**
  + 이제 데이터 준비는 특정 의미 체계 형식과 일치 하는 열을 인식 하 고 적절 하 게 분할할 수 있습니다. 현재 지원 되는 STypes에는 전자 메일 주소, 지리적 좌표 (위도 & 경도), IPv4 및 IPv6 주소, 미국 전화 번호, 미국 우편 번호 등이 포함 됩니다.
    + 방법 가이드: [의미 유형 노트북](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 이제 데이터 준비에서는 두 개의 숫자 열 빼기, 곱하기, 나누기 및 모듈로 결과 열을 생성 하는 다음 작업을 지원 합니다.
  + 데이터 흐름에 대 한 `verify_has_data()`를 호출 하 여 데이터 흐름에서 실행 된 경우 레코드를 생성할지 여부를 확인할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + 이제 숫자 열 프로필에 대 한 히스토그램에서 사용할 계급의 수를 지정할 수 있습니다.
  + @No__t_0 변환에는 이제 데이터 프레임에 문자열 또는 바이트 형식의 열 이름을 사용 해야 합니다.
  + 열이 누락 된 경우 값이 올바르게 채워지지 않은 `fill_nulls` 변환의 버그를 수정 했습니다.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v 1.0.18

 + **변경된 기능**
   + Azureml tensorboard 패키지는 azureml-tensorboard를 대체 합니다.
   + 이 릴리스에서는 생성 하는 동안 관리 되는 계산 클러스터 (amlcompute)에서 사용자 계정을 설정할 수 있습니다. 프로 비전 구성에서 이러한 속성을 전달 하 여이 작업을 수행할 수 있습니다. 자세한 내용은 [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)에서 확인할 수 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 데이터 준비 SDK v 1.0.17

+ **새로운 기능**
  + 에서는 이제 식 언어를 사용 하 여 결과 열을 생성 하는 두 개의 숫자 열을 추가할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + Random_split에 대 한 설명서 및 매개 변수 확인이 개선 되었습니다.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 데이터 준비 SDK v 1.0.16

+ **버그 수정**
  + API 변경으로 인해 발생 한 서비스 주체 인증 문제를 수정 했습니다.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v 1.0.17

+ **새로운 기능**

  + 이제 Azure Machine Learning는 인기 있는 DNN framework 체 이너를 위한 첫 번째 클래스 지원을 제공 합니다. [@No__t_1](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 클래스를 사용 하 여 체 이너 모델을 쉽게 학습 하 고 배포할 수 있습니다.
    + [ChainerMN를 사용 하 여 분산 교육을 실행](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb) 하는 방법 알아보기
    + [하이퍼 매개 변수를 사용 하 여 체 매개 변수 튜닝을 사용](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) 하 여 체를 실행 하는 방법 알아보기
  + Azure Machine Learning 파이프라인은 데이터 저장소 수정에 따라 파이프라인 실행을 트리거하는 기능을 추가 했습니다. 이 기능을 보여 주기 위해 파이프라인 [일정 노트북이](https://aka.ms/pl-schedule) 업데이트 됩니다.

+ **버그 수정 및 향상 된 기능**
  + [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)에 제공 되는 [runconfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 에서 source_directory_data_store 속성을 원하는 데이터 저장소 (예: blob storage)로 설정 하기 위한 Azure Machine Learning 지원 파이프라인이 추가 되었습니다. 기본적으로 Azure 파일 저장소는 백업 데이터 저장소로 사용 됩니다 .이는 많은 수의 단계가 동시에 실행 될 때 제한 문제가 발생할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

+ **새로운 기능**
  + 새 보고서 끌어서 놓기 테이블 편집기 환경 사용자는 테이블의 미리 보기가 표시 되는 테이블 영역에서 열을 끌 수 있습니다. 열을 다시 정렬할 수 있습니다.
  + 새 로그 파일 뷰어
  + 작업 탭에서 실험 실행, 계산, 모델, 이미지 및 배포에 대 한 링크

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning 데이터 준비 SDK v 1.0.15

+ **새로운 기능**
  + 이제 데이터 준비는 데이터 흐름에서 파일 스트림 작성을 지원 합니다. 는 파일 스트림 이름을 조작 하 여 새 파일 이름을 만드는 기능도 제공 합니다.
    + 방법 가이드: [파일 스트림 노트 작업](https://aka.ms/aml-data-prep-file-stream-nb)

+ **버그 수정 및 향상 된 기능**
  + 대량 데이터 집합에 대 한 t-다이제스트의 성능이 개선 되었습니다.
  + 이제 데이터 준비에서는 데이터 경로에서 데이터를 읽을 수 있습니다.
  + 이제 하나의 핫 인코딩은 부울 및 숫자 열에서 작동 합니다.
  + 기타 기타 버그 수정

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Python용 Azure Machine Learning SDK v1.0.15

+ **새로운 기능**
  + Azure Machine Learning 파이프라인은 AzureBatchStep ([노트북](https://aka.ms/pl-azbatch)), Hyper드라이브 estep (노트북) 및 시간 기반 예약 기능 ([노트북](https://aka.ms/pl-schedule))을 추가 했습니다.
  +  DataTranferStep이 Azure SQL Server 및 Azure Database for PostgreSQL에서 작동하도록 업데이트되었습니다([Notebook](https://aka.ms/pl-data-trans)).

+ **변경된 기능**
  + `PublishedPipeline.get_published_pipeline`이 사용되지 않는 대신 `PublishedPipeline.get`이 사용됩니다.
  + `Schedule.get_schedule`이 사용되지 않는 대신 `Schedule.get`이 사용됩니다.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 데이터 준비 SDK v1.0.12

+ **새로운 기능**
  + 데이터 준비 SDK는 이제 데이터 저장소를 사용하여 Azure SQL 데이터베이스에서 읽을 수 있도록 지원합니다.
 
+ **변경된 기능**
  + 대량 데이터에 대 한 특정 작업의 메모리 성능이 향상 되었습니다.
  + `read_pandas_dataframe()`에는 이제 `temp_folder`를 지정해야 합니다.
  + `ColumnProfile`의 `name` 속성은 더 이상 사용되지 않습니다. 대신 `column_name`을 사용합니다.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Python용 Azure Machine Learning SDK v1.0.10

+ **변경 내용**: 
  + Azure ML SDK에는 azure-cli 패키지가 더 이상 종속성으로 포함되지 않습니다. 특히 azure-cli-core 및 azure-cli-profile 종속성이 azureml-core에서 제거되었습니다. 사용자에게 영향을 미치는 변경 내용은 다음과 같습니다.
    + "Az login"을 수행한 다음 azureml-sdk를 사용 하는 경우 SDK는 브라우저 또는 장치 코드 로그를 한 번 더 수행 합니다. “az login”에서 생성된 자격 증명 상태는 사용되지 않습니다.
    + “az login”을 사용하는 것과 같은 Azure CLI 인증의 경우 _azureml.core.authentication.AzureCliAuthentication_ 클래스를 사용합니다. Azure CLI 인증의 경우 azureml-sdk를 설치한 Python 환경에서 _pip install azure-cli_를 수행합니다.
    + 자동화를 위해 서비스 주체를 사용하여 “az login”을 수행하는 경우에는 Azure CLI에서 생성된 _azureml.core.authentication.ServicePrincipalAuthentication_ 클래스를 사용하는 것이 좋습니다. azureml-sdk는 azure CLI에서 만든 자격 증명 상태를 사용하지 않기 때문입니다. 

+ **버그 수정**:이 릴리스에는 주로 사소한 버그 수정이 포함 되어 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **버그 수정**
  + 데이터 프로필을 가져오는 성능이 개선 되었습니다.
  + 오류 보고와 관련한 사소한 버그를 수정했습니다.
  
### <a name="azure-portal-new-features"></a>Azure Portal: 새로운 기능
+ 보고서에 적용할 수 있는 새로운 끌어서 놓기 차트 환경. 사용자가 웰의 열 또는 특성을 차트 영역으로 끌어오면 데이터 형식에 따라 사용자에게 적절한 차트 종류가 자동으로 선택됩니다. 사용자는 차트 종류를 적용 가능한 다른 종류로 변경하거나 다른 특성을 추가할 수 있습니다.

    지원되는 차트 종류:
    - 꺾은선형 차트
    - 히스토그램
    - 누적 가로 막대형 차트
    - 상자 플롯
    - 산점도
    - 거품형 플롯
+ 이제 포털에서는 실험 보고서를 동적으로 생성합니다. 사용자가 실행을 실험에 제출하면 서로 다른 실행 간에 비교할 수 있도록 기록된 메트릭과 그래프가 있는 보고서가 자동으로 생성됩니다. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Python용 Azure Machine Learning SDK v1.0.8

+ **버그 수정**:이 릴리스에는 주로 사소한 버그 수정이 포함 되어 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **새로운 기능**
  + 데이터 저장소 기능 향상([데이터 저장소 방법 가이드](https://aka.ms/aml-data-prep-datastore-nb)에 설명되어 있음)
    + 강화 시 Azure 파일 공유 및 ADLS 데이터 저장소에서 데이터를 읽고 쓰는 기능이 추가되었습니다.
    + 이제 데이터 저장소 사용 시 Data Prep에서 대화형 인증 대신 서비스 주체 인증 사용을 지원합니다.
    + wasb 및 wasbs url 지원이 추가되었습니다.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **버그 수정**
  + Spark의 공용 읽기 가능 Azure Blob 컨테이너에서 읽기 관련 버그를 수정했습니다.

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Python용 Azure Machine Learning SDK v1.0.6
+ **버그 수정**:이 릴리스에는 주로 사소한 버그 수정이 포함 되어 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

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

Azure Machine Learning 이제 일반 공급 됩니다.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
이번 릴리스에서는 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute)을 통해 새로운 관리형 컴퓨팅 환경을 발표합니다. 이 컴퓨팅 대상은 Azure Machine Learning에 대한 Azure Batch AI 컴퓨팅을 대체합니다. 

이 컴퓨팅 대상:
+ 모델 학습 및 일괄 처리 유추/점수 매기기에 사용 됩니다.
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

+ **새로 만들기**
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
  + 이제 [샘플 Notebook](https://aka.ms/aml-notebooks)이 새 관리형 컴퓨팅을 사용하도록 업데이트되었습니다.
  + [이 컴퓨팅에 대해 알아보기](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure Portal: 새로운 기능
+ 포털에서 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute) 형식을 만들고 관리합니다.
+ Azure Machine Learning 컴퓨팅에 대한 할당량 사용 및 [요청 할당량](how-to-manage-quotas.md)을 모니터링합니다.
+ Azure Machine Learning 컴퓨팅 클러스터 상태를 실시간으로 확인합니다.
+ Azure Machine Learning 컴퓨팅 및 Azure Kubernetes Service 만들기에 대한 가상 네트워크 지원이 추가되었습니다.
+ 기존 매개 변수를 사용하여 게시된 파이프라인을 다시 실행합니다.
+ 분류 모델(리프트, 게인, 보정, 모델 확장성 포함 기능 중요성 차트) 및 회귀 모델(오차 및 모델 확장성 포함 기능 중요성 차트)에 대한 새로운 [자동화된 기계 학습 차트](how-to-understand-automated-ml.md). 
+ Azure Portal에서 파이프라인을 볼 수 있음




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Python용 Azure Machine Learning SDK v0.1.80

+ **주요 변경 내용** 
  * *azureml.train.widgets* 네임스페이스가 *azureml.widgets*로 전환되었습니다.
  * *azureml.core.compute.AmlCompute*는 *azureml.core.compute.BatchAICompute* 및 *azureml.core.compute.DSVMCompute* 클래스를 더 이상 사용하지 않습니다. 후자의 클래스는 이후 릴리스에서 제거됩니다. AmlCompute 클래스는 이제 더 쉽게 정의할 수 있으며, vm_size 및 max_nodes만 필요하고, 작업이 제출되면 클러스터가 자동으로 0에서 max_nodes로 확장됩니다. [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training)이 이 정보로 업데이트되었으며 사용 예제를 제공합니다. 이 간소화와 나중에 릴리스될 많은 흥미로운 기능이 마음에 들기를 바랍니다!

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

### <a name="azure-portal"></a>Azure Portal 
Azure Machine Learning에 대 한 Azure Portal에는 다음 업데이트가 포함 됩니다.
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

새로 새로 고친 Azure Machine Learning 릴리스:이 릴리스에 대 한 자세한 내용은 https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](../service/overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
