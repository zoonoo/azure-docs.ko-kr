---
title: 릴리스의 새로운 기능이란?
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 및 기계 학습 및 데이터 준비 파이썬 SDK에 대한 최신 업데이트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 2739e960c4bc6d35ac9ad757e80ed620e5dc04f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684660"
---
# <a name="azure-machine-learning-release-notes"></a>Azure 기계 학습 릴리스 정보

이 문서에서는 Azure 기계 학습 릴리스에 대해 알아봅니다.  전체 SDK 참조 콘텐츠는 Azure 기계 학습의 파이썬 참조 용 [**기본 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 페이지를 방문하십시오.

알려진 버그 및 해결 방법에 대해 알아 보려면 [알려진 문제 목록](resource-known-issues.md)을 참조하세요.

## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>파이썬 v1.3.0에 대한 Azure 기계 학습 SDK

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 교육 후 작업에 대한 추가 원격 분석을 추가했습니다.
    + 100보다 긴 일련의 길이에 대한 조건부 제곱(CSS) 교육을 사용하여 자동 ARIMA 교육 속도를 높입니다. 사용된 길이는 /src/azureml-automl-코어/azureml/automl/코어/공유/상수에서 TimeSeriesInternal 클래스에 있는 상수 ARIMA_TRIGGER_CSS_TRAINING_LENGTH 저장 됩니다.
    + 예측 실행의 사용자 로깅이 개선되었으므로 현재 실행 중인 단계에 대한 자세한 정보가 로그에 표시됩니다.
    + 허용되지 않는 target_rolling_window_size 값 보다 적게 설정된 다음 2로 설정하도록 허용됨
  + **azureml-자동-런타임**
    + 중복된 타임스탬프를 찾을 때 표시되는 오류 메시지가 개선되었습니다.
    + 허용되지 않는 target_rolling_window_size 값보다 적게 설정한 다음 2로 설정됩니다.
    + 지연 대치 실패를 수정했습니다. 이 문제는 계절적으로 시리즈를 분해하는 데 필요한 관측의 수가 부족하여 발생했습니다. "계절화 되지 않는" 데이터는 지연 길이를 결정 하기 위해 부분 자기 상관 함수 (PACF)를 계산 하는 데 사용 됩니다.
    + 위화 구성에 의한 작업 예측에 대한 열 목적 위화 사용자 지정을 활성화했습니다. 이제 작업을 예측하기 위한 열 목적으로 숫자 및 범주형이 지원됩니다.
    + 위화 구성에 의한 작업 예측에 대한 드롭 열 위화 사용자 지정을 활성화했습니다.
    + 위화 구성에 의한 예측 작업에 대한 대금 사용자 지정을 활성화했습니다. 대상 컬럼 및 평균, 중앙값, most_frequent 및 학습 데이터에 대한 상수 값 대치에 대한 상수 값 대치가 지원됩니다.
  + **azureml-콘티브-파이프라인-단계**
    + ParallelRunConfig에 전달될 문자열 계산 이름 수락
  + **azureml 코어**
    +  환경 개체의 복사본을 만들기 위해 환경 복제(new_name) API를 추가했습니다.
    +  환경.docker.base_dockerfile 파일 경로를 허용합니다. 파일을 확인할 수 있는 경우 콘텐츠가 base_dockerfile 환경 속성으로 읽혀집니다.
    + 사용자가 Environment.docker에서 수동으로 값을 설정할 때 base_image 및 base_dockerfile 대해 상호 배타적인 값을 자동으로 재설정합니다.
    +  데이터 집합: 유니코드 문자가 포함된 데이터 경로인 경우 데이터 집합 다운로드 실패를 수정했습니다.
    +  데이터 집합: Azure Machine Learning Compute의 최소 디스크 공간 요구 사항을 존중하기 위해 데이터 집합 마운트 캐싱 메커니즘이 개선되어 노드를 사용할 수 없게 만들고 작업이 취소되는 것을 방지합니다.
    + 환경이 사용자 또는 AzureML에서 관리되는지 여부를 나타내는 RSection에 user_managed 플래그가 추가되었습니다.
    + 데이터 집합: 타임시리즈 데이터 집합에 팬더 데이터 프레임으로 액세스할 때 타임시리즈 열에 대한 인덱스를 추가하며, 이 인덱스는 타이머 기반 데이터 액세스에 대한 액세스 속도를 높이는 데 사용됩니다.  이전에는 인덱스에 타임스탬프 열과 동일한 이름이 지정되어 사용자가 실제 타임스탬프 열이고 인덱스가 혼동되었습니다. 이제 열로 사용해서는 안 되므로 인덱스에 특정 이름을 지정하지 않습니다. 
  + **azureml-데이터 준비**
    + 주권 클라우드에서 데이터 집합 인증 문제 수정
    + Azure `Dataset.to_spark_dataframe` PostgreSQL 데이터 스토어에서 만든 데이터 집합에 대한 오류 수정
  + **azureml-interpret**
    + 지역 중요도 값이 희박한 경우 시각화에 글로벌 점수 추가
    + 해석 커뮤니티 0.9.9를 사용하도록 azureml 해석을 업데이트했습니다.
    + 평가 데이터가 희소한 다운로드 설명 문제를 수정했습니다.
    + AutoML에서 설명 개체의 스파스 형식 지원이 추가되었습니다.
  + **azureml 파이프라인 코어**
    + 파이프라인에서 계산 대상으로 계산 인스턴스 지원
  + **azureml-train-automl-client**
    + 교육 후 작업에 대한 추가 원격 분석을 추가했습니다.
    + 조기 정지시 회귀를 수정했습니다.
    + 사용되지 않게 된 azureml.dprep.Dataflow를 입력 데이터에 대한 유효한 유형으로 사용합니다.
    +  기본 AutoML 실험 시간 시간을 6일로 변경합니다.
  + **azureml-기차-자동-런타임**
    + 교육 후 작업에 대한 추가 원격 분석을 추가했습니다.
    + 스파스 오토ml e2e 지원 추가
  + **azureml-opendatasets**
    + 서비스 모니터에 대한 추가 원격 분석을 추가했습니다.
    + Blob용 프론트도어 를 사용하여 안정성 향상 
## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>파이썬 v1.2.0에 대한 Azure 기계 학습 SDK

+ **주요 변경 내용**
  + 파이썬 2.7에 대한 드롭 지원

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI의 `az ml model/computetarget/service` 명령에 "--구독 ID"를 추가합니다.
    + ACI 배포를 위한 CMK(고객 관리 키) vault_url, key_name 및 key_version 전달에 대한 지원 추가
  + **azureml-automl-core** 
    + X 및 y 데이터 예측 작업에 대해 일정한 값을 가진 사용자 지정 대치를 활성화했습니다.
    + 사용자에게 오류 메시지를 표시하는 문제를 해결했습니다.    
  + **azureml-자동-런타임**
    + 행이 하나뿐인 입자를 포함하는 데이터 집합에 대한 예측에서 문제를 수정했습니다.
    + 예측 작업에 필요한 메모리 양을 줄입니다.
    + 시간 열의 형식이 올바르지 않은 경우 오류 메시지가 더 잘 추가되었습니다.
    + X 및 y 데이터 예측 작업에 대해 일정한 값을 가진 사용자 지정 대치를 활성화했습니다.
  + **azureml 코어**
    + 환경 변수(AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID 및 AZUREML_SERVICE_PRINCIPAL_PASSWORD 에서 ServicePrincipal 로드에 대한 지원 추가
    + 에 새 `support_multi_line` 매개 `Dataset.Tabular.from_delimited_files`변수를`support_multi_line=False`도입 : 기본적으로 ()) 인용 필드 값에있는 것을 포함하여 모든 줄 바꿈은 레코드 중단으로 해석됩니다. 이러한 방식으로 데이터를 읽는 것이 더 빠르고 여러 CPU 코어에서 병렬 실행을 위해 최적화됩니다. 그러나 정렬되지 않으면 필드 값이 잘못 정렬된 레코드가 자동으로 생성될 수 있습니다. 구분된 파일에 `True` 인용줄 바꿈이 포함된 것으로 알려진 경우이 설정 해야 합니다.
    + Azure 기계 학습 CLI에 ADLS Gen2를 등록하는 기능이 추가되었습니다.
    + 매개 변수의 사용을 더 잘 반영하기 위해 TabularDataset의 with_timestamp_columns() 메서드에 대해 매개 변수 'fine_grain_timestamp'을 '타임스탬프'로, 매개 변수 'coarse_grain_timestamp'에서 'partition_timestamp'으로 변경했습니다.
    + 최대 실험 이름 길이가 255로 증가했습니다.
  + **azureml-interpret**
    + 커뮤니티 0.7을 해석하기 위해 azureml 해석을 업데이트했습니다.*
  + **azureml-sdk**
    + 시험판 및 안정적인 릴리스에서 패치를 지원하기 위해 호환 되는 버전 Tilde와 종속성으로 변경 합니다.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>파이썬 v1.1.5에 대한 Azure 기계 학습 SDK

+ **기능 사용 중단**
  + **Python 2.7**
    + 파이썬 2.7을 지원하는 마지막 버전

+ **주요 변경 내용**
  + **유의적 버전 2.0.0**
    + 버전 1.1 Azure ML 파이썬 SDK에서 시작하여 시맨틱 버전 링 2.0.0을 채택합니다. [여기에서 자세히 알아보세요](https://semver.org/). 이후의 모든 버전은 새 번호 매기기 체계 및 의미 체계 버전 전환 계약을 따릅니다. 

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 일관성을 위해 끝점 CLI 명령 이름을 'az ml 끝점 aks'에서 'az ml 끝점 실시간'으로 변경합니다.
    + 안정적이고 실험적인 분기 CLI에 대한 CLI 설치 지침을 업데이트합니다.
    + 단일 인스턴스 프로파일링은 권장 사항을 생성하도록 수정되었으며 코어 sdk에서 사용할 수 있게 되었습니다.
  + **azureml-automl-core**
    + automl ONNX 모델에 대해 일괄 처리 모드 추론(여러 행을 한 번 사용) 활성화
    + 데이터 세트의 빈도 감지, 데이터 부족 또는 불규칙한 데이터 포인트 포함 개선
    + 주요 빈도를 준수하지 않는 데이터 포인트를 제거하는 기능이 추가되었습니다.
    + 해당 열에 대 한 대입 옵션을 적용 하는 옵션 목록을 가지고 생성자의 입력을 변경 했습니다.
    + 오류 로깅이 개선되었습니다.
  + **azureml-자동-런타임**
    + 학습 세트에 없는 그레인이 테스트 세트에 나타난 경우 오류가 발생한 문제를 수정했습니다.
    + 예측 서비스에서 채점하는 동안 y_query 요구 사항이 제거되었습니다.
    + 데이터 집합에 긴 시간 간격이 있는 짧은 입자가 포함된 경우 예측 문제를 수정했습니다.
    + 자동 최대 수평선이 켜져 있고 날짜 열에 문자열 형식의 날짜가 포함되는 문제를 수정했습니다. 날짜로 변환할 수 없는 경우 적절한 변환 및 오류 메시지가 추가되었습니다.
    + FileCacheStore에 대한 중간 데이터를 직렬화하고 직렬화하기 위해 네이티브 NumPy 및 SciPy 사용(로컬 AutoML 실행에 사용)
    + 실패한 자식 실행이 실행 중인 상태에서 멈출 수 있는 버그를 수정했습니다.
    + 위화 속도가 빨라지더다.
    + 채점 하는 동안 주파수 검사를 수정, 지금 예측 작업 기차와 테스트 집합 사이 엄격한 주파수 동등성을 필요로 하지 않습니다.
    + 해당 열에 대 한 대입 옵션을 적용 하는 옵션 목록을 가지고 생성자의 입력을 변경 했습니다.
    + 지연 유형 선택과 관련된 오류를 수정했습니다.
    + 데이터 집합에서 발생한 분류되지 않은 오류가 단일 행에 입자가 있는 것으로 수정되었습니다.
    + 주파수 감지 속도가 느려지는 문제를 수정했습니다.
    + AutoML 예외 처리에서 학습 실패의 실제 원인을 AttributeError로 대체하는 버그를 수정합니다.
  + **azureml-cli-common**
    + 단일 인스턴스 프로파일링은 권장 사항을 생성하도록 수정되었으며 코어 sdk에서 사용할 수 있게 되었습니다.
  + **azureml-콘티브-미르**
    + 액세스 토큰을 검색하기 위해 MirWebservice 클래스에 기능을 추가합니다.
    + MirWebservice.run() 호출 중에 기본적으로 MirWebservice에 대한 토큰 인증 사용 - 호출에 실패한 경우에만 새로 고침
    + Mir 웹 서비스 배포에는 각각 [Ds2v2, A2v2 및 F16] 대신 적절한 Skus[Standard_DS2_v2, Standard_F16, Standard_A2_v2]가 필요합니다.
  + **azureml-콘티브-파이프라인-단계**
    + 선택적 매개 변수side_inputs 병렬 실행 단계에 추가됩니다. 이 매개 변수는 컨테이너에 폴더를 탑재하는 데 사용할 수 있습니다. 현재 지원되는 형식은 데이터 참조 및 파이프라인데이터입니다.
    + ParallelRunConfig에서 전달된 매개 변수는 이제 파이프라인 매개 변수를 전달하여 덮어쓸 수 있습니다. aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout(aml_node_count 및 aml_process_count_per_node 이미 이전 릴리스의 일부)를 지원하는 새 파이프라인 매개 변수입니다.
  + **azureml 코어**
    + 배포된 AzureML 웹 서비스는 `INFO` 이제 로깅으로 기본설정됩니다. 배포된 서비스에서 환경 `AZUREML_LOG_LEVEL` 변수를 설정하여 제어할 수 있습니다.
    + Python sdk는 검색 서비스를 사용하여 '파이프라인' 대신 'api' 끝점을 사용합니다.
    + 모든 SDK 호출에서 새 경로로 교환
    + ModelManagementService에 대한 호출 라우팅을 새 통합 구조로 변경
      + 작업 영역 업데이트 메서드를 공개적으로 사용할 수 있게 했습니다.
      + 사용자가 이미지 빌드에 대한 계산을 업데이트할 수 있도록 작업 영역 업데이트 메서드에 image_build_compute 매개 변수를 추가했습니다.
    +  이전 프로파일링 워크플로에 사용 중단 메시지가 추가되었습니다. 고정 프로파일링 CPU 및 메모리 제한
    + R 작업을 실행하기 위해 환경의 일부로 RSection을 추가했습니다.
    +  데이터 집합의 원본에 `Dataset.mount` 액세스할 수 없거나 데이터가 포함되지 않은 경우 오류를 발생시키기 위해 유효성 검사를 추가했습니다.
    + VNet 뒤에 있는 Blob 컨테이너를 등록할 수 있는 Azure Blob 컨테이너를 등록하기 위한 Datastore CLI에 대한 추가 매개 변수로 추가되었습니다. `--grant-workspace-msi-access`
    + 단일 인스턴스 프로파일링은 권장 사항을 생성하도록 수정되었으며 코어 sdk에서 사용할 수 있게 되었습니다.
    + aks.py _deploy 문제를 수정했습니다.
    + 자동 저장소 오류를 방지하기 위해 업로드중인 모델의 무결성을 확인합니다.
    + 이제 사용자는 웹 서비스에 대한 키를 다시 생성할 때 인증 키에 대한 값을 지정할 수 있습니다.
    + 대문자가 데이터 집합의 입력 이름으로 사용할 수 없는 버그를 수정했습니다.
  + **azureml 기본값**
    + `azureml-dataprep`이제 의 `azureml-defaults`일부로 설치됩니다. 더 이상 데이터 집합을 탑재하기 위해 계산 대상에 수동으로 dataprep[퓨즈]를 설치할 필요가 없습니다.
  + **azureml-interpret**
    + 커뮤니티 0.6을 해석하기 위해 azureml 해석을 업데이트했습니다.
    + 해석 커뮤니티 0.5.0에 의존하도록 azureml 해석업데이트
    + azureml 해석에 azureml 스타일 예외 추가
    + 커라스 모델에 대한 Deep채점의 설명자 직렬화가 수정되었습니다.
  + **azureml-mlflow**
    + azureml.mlflow에 주권 클라우드에 대한 지원 추가
  + **azureml 파이프라인 코어**
    + 파이프라인 일괄 처리 점수 매기기 노트북은 이제 ParallelRunStep을 사용합니다.
    + 인수 목록을 변경했음에도 불구하고 PythonScriptStep 결과가 잘못 사용될 수 있는 버그를 수정했습니다.
    + parse_* 메서드를 호출할 때 열의 형식을 설정하는 기능이 추가되었습니다.`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + 를 `AutoMLStep` 패키지로 `azureml-pipeline-steps` 이동했습니다. 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.
    + PythonScriptStep 입력으로 데이터 집합에 대한 설명서 예제 추가
  + **azureml 텐서 보드**
    + 텐서플로우 2.0을 지원하도록 업데이트된 azureml-텐서보드
    + 계산 인스턴스에서 사용자 지정 텐서보드 포트를 사용할 때 올바른 포트 번호 표시
  + **azureml-train-automl-client**
    + 원격 실행시 특정 패키지를 잘못된 버전으로 설치할 수 있는 문제를 수정했습니다.
    + 사용자 지정 위화 구성을 필터링하는 문제를 수정했습니다.
  + **azureml-기차-자동-런타임**
    + 원격 실행에서 주파수 감지 문제를 해결했습니다.
    + 패키지에서 `AutoMLStep` 이동했습니다. `azureml-pipeline-steps` 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.
  + **azureml-train-core**
    + 파이토치 추정기에서 파이토치 버전 1.4 지원
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>파이썬 v1.1.2rc0에 대한 Azure 기계 학습 SDK (시험판)

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + automl ONNX 모델에 대해 일괄 처리 모드 추론(여러 행을 한 번 사용) 활성화
    + 데이터 세트의 빈도 감지, 데이터 부족 또는 불규칙한 데이터 포인트 포함 개선
    + 지배적인 빈도를 준수하지 않는 데이터 포인트를 제거하는 기능이 추가되었습니다.
  + **azureml-자동-런타임**
    + 학습 세트에 없는 그레인이 테스트 세트에 나타난 경우 오류가 발생한 문제를 수정했습니다.
    + 예측 서비스에서 채점하는 동안 y_query 요구 사항이 제거되었습니다.
  + **azureml-콘티브-미르**
    + 액세스 토큰을 검색하기 위해 MirWebservice 클래스에 기능을 추가합니다.
  + **azureml 코어**
    + 배포된 AzureML 웹 서비스는 `INFO` 이제 로깅으로 기본설정됩니다. 배포된 서비스에서 환경 `AZUREML_LOG_LEVEL` 변수를 설정하여 제어할 수 있습니다.
    + 작업 영역에 등록된 `Dataset.get_all` 모든 데이터 집합을 반환하려면 반복을 수정합니다.
    + 잘못된 형식이 데이터 집합 생성 `path` API 인수에 전달될 때 오류 메시지를 개선합니다.
    + Python sdk는 검색 서비스를 사용하여 '파이프라인' 대신 'api' 끝점을 사용합니다.
    + 모든 SDK 호출에서 새 경로로 교환
    + ModelManagementService에 대한 호출 라우팅을 새 통합 구조로 변경
      + 작업 영역 업데이트 메서드를 공개적으로 사용할 수 있게 했습니다.
      + 사용자가 이미지 빌드에 대한 계산을 업데이트할 수 있도록 작업 영역 업데이트 메서드에 image_build_compute 매개 변수를 추가했습니다.
    +  이전 프로파일링 워크플로에 사용 중단 메시지가 추가되었습니다. 고정 프로파일링 CPU 및 메모리 제한
  + **azureml-interpret**
    + azureml 해석을 해석 커뮤니티 0.6.* 업데이트
  + **azureml-mlflow**
    + azureml.mlflow에 주권 클라우드에 대한 지원 추가
  + **azureml-pipeline-steps**
    + 을 `AutoMLStep` 로 `azureml-pipeline-steps package`이동했습니다. 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.
  + **azureml-train-automl-client**
    + 원격 실행시 특정 패키지를 잘못된 버전으로 설치할 수 있는 문제를 수정했습니다.
  + **azureml-기차-자동-런타임**
    + 원격 실행에서 주파수 감지 문제를 해결했습니다.
    + 을 `AutoMLStep` 로 `azureml-pipeline-steps package`이동했습니다. 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.
  + **azureml-train-core**
    + 을 `AutoMLStep` 로 `azureml-pipeline-steps package`이동했습니다. 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>파이썬 v1.1.1rc0에 대한 Azure 기계 학습 SDK (시험판)

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 단일 인스턴스 프로파일링은 권장 사항을 생성하도록 수정되었으며 코어 sdk에서 사용할 수 있게 되었습니다.
  + **azureml-automl-core**
    + 오류 로깅이 개선되었습니다.
  + **azureml-자동-런타임**
    + 데이터 집합에 긴 시간 간격이 있는 짧은 입자가 포함된 경우 예측 문제를 수정했습니다.
    + 자동 최대 수평선이 켜져 있고 날짜 열에 문자열 형식의 날짜가 포함되는 문제를 수정했습니다. 날짜로의 변환이 불가능한 경우 적절한 변환과 합리적인 오차를 추가했습니다
    + FileCacheStore에 대한 중간 데이터를 직렬화하고 직렬화하기 위해 네이티브 NumPy 및 SciPy 사용(로컬 AutoML 실행에 사용)
    + 실패한 자식 실행이 실행 중인 상태에서 멈출 수 있는 버그를 수정했습니다.
  + **azureml-cli-common**
    + 단일 인스턴스 프로파일링은 권장 사항을 생성하도록 수정되었으며 코어 sdk에서 사용할 수 있게 되었습니다.
  + **azureml 코어**
    + VNet 뒤에 있는 Blob 컨테이너를 등록할 수 있는 Azure Blob 컨테이너를 등록하기 위한 Datastore CLI에 대한 추가 매개 변수로 추가되었습니다. `--grant-workspace-msi-access`
    + 단일 인스턴스 프로파일링은 권장 사항을 생성하도록 수정되었으며 코어 sdk에서 사용할 수 있게 되었습니다.
    + aks.py _deploy 문제를 수정했습니다.
    + 자동 저장소 오류를 방지하기 위해 업로드중인 모델의 무결성을 확인합니다.
  + **azureml-interpret**
    + azureml 해석에 azureml 스타일 예외추가
    + 고정 된 Deep채점설명서 커짐 질렬화
  + **azureml 파이프라인 코어**
    + 파이프라인 일괄 처리 점수 매기기 노트북은 이제 ParallelRunStep을 사용합니다.
  + **azureml-pipeline-steps**
    + 패키지에서 `AutoMLStep` 이동했습니다. `azureml-pipeline-steps` 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.
  + **azureml-콘티브-파이프라인-단계**
    + 선택적 매개 변수side_inputs 병렬 실행 단계에 추가됩니다. 이 매개 변수는 컨테이너에 폴더를 탑재하는 데 사용할 수 있습니다. 현재 지원되는 형식은 데이터 참조 및 파이프라인데이터입니다.
  + **azureml 텐서 보드**
    + 텐서플로우 2.0을 지원하도록 업데이트된 azureml-텐서보드
  + **azureml-train-automl-client**
    + 사용자 지정 위화 구성을 필터링하는 문제를 수정했습니다.
  + **azureml-기차-자동-런타임**
    + 패키지에서 `AutoMLStep` 이동했습니다. `azureml-pipeline-steps` 내부를 `AutoMLStep` `azureml-train-automl-runtime`더 이상 사용되지 않습니다.
  + **azureml-train-core**
    + 파이토치 추정기에서 파이토치 버전 1.4 지원
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>파이썬 v1.1.0rc0에 대한 Azure 기계 학습 SDK (시험판)

+ **주요 변경 내용**
  + **유의적 버전 2.0.0**
    + 버전 1.1 Azure ML 파이썬 SDK에서 시작하여 시맨틱 버전 링 2.0.0을 채택합니다. [여기에서 자세히 알아보세요](https://semver.org/). 이후의 모든 버전은 새 번호 매기기 체계 및 의미 체계 버전 전환 계약을 따릅니다. 
  
+ **버그 수정 및 개선 사항**
  + **azureml-자동-런타임**
    + 위화 속도가 빨라지더다.
    + 점수 매기기 중에 주파수 검사를 수정했으며, 이제 예측 작업에서는 기차와 테스트 세트 간의 엄격한 주파수 동등성이 필요하지 않습니다.
  + **azureml 코어**
    + 이제 사용자는 웹 서비스에 대한 키를 다시 생성할 때 인증 키에 대한 값을 지정할 수 있습니다.
  + **azureml-interpret**
    + 해석 커뮤니티 0.5.0에 의존하도록 azureml 해석업데이트
  + **azureml 파이프라인 코어**
    + 인수 목록을 변경했음에도 불구하고 PythonScriptStep 결과가 잘못 사용될 수 있는 버그를 수정했습니다.
  + **azureml-pipeline-steps**
    + PythonScriptStep 입력으로 데이터 집합에 대한 설명서 예제 추가
  + **azureml-콘티브-파이프라인-단계**
    + ParallelRunConfig에서 전달된 매개 변수는 이제 파이프라인 매개 변수를 전달하여 덮어쓸 수 있습니다. aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout(aml_node_count 및 aml_process_count_per_node 이미 이전 릴리스의 일부)를 지원하는 새 파이프라인 매개 변수입니다.
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>파이썬 v1.0.85에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + **azureml 코어**
    + 지정된 작업 영역 및 구독에서 AmlCompute 리소스에 대한 현재 핵심 사용량 및 할당량 제한 가져오기
  
  + **azureml-콘티브-파이프라인-단계**
    + 사용자가 이전 단계에서 병렬 런스텝으로 중간 결과로 테이블 형식 데이터 집합을 전달할 수 있도록 합니다.

+ **버그 수정 및 개선 사항**
  + **azureml-자동-런타임**
    + 배포된 예측 서비스에 대한 요청에서 y_query 열의 요구 사항을 제거했습니다. 
    + 'y_query'은 도미닉의 오렌지 주스 노트북 서비스 요청 섹션에서 제거되었습니다.
    + 날짜 시간 열이 있는 데이터 집합에서 작업하는 배포된 모델의 예측을 방지하는 버그를 수정했습니다.
    + 이진 및 다중 클래스 분류 모두에 대해 Matthews 상관 관계 계수를 분류 메트릭으로 추가했습니다.
  + **azureml-contrib-interpret**
    + azureml-contrib-interpret에서 텍스트 설명이 텍스트 설명으로 제거된 텍스트 설명이 곧 릴리스될 해석 텍스트 리포지토리로 이동되었습니다.
  + **azureml 코어**
    + 데이터 집합: 파일 데이터 집합에 대한 사용은 더 이상 파이썬 env에 설치될 numpy 및 팬더에 의존하지 않습니다.
    + 상태 끝점을 ping하기 전에 로컬 Docker 컨테이너의 상태를 확인하도록 LocalWebservice.wait_for_deployment()를 변경하여 실패한 배포를 보고하는 데 걸리는 시간을 크게 줄였습니다.
    + LocalWebservice.reload() 에서 서비스 개체가 LocalWebservice() 생성자사용 기존 배포에서 만들 때 에 사용되는 내부 속성의 초기화를 수정했습니다.
    + 설명을 위해 편집된 오류 메시지입니다.
    + AksServiceAccessToken 개체를 반환 하는 AksServiceAccessToken 개체를 반환 하는 get_access_token() 라는 새 메서드를 추가 했습니다., 액세스 토큰을 포함 하는, 타임 스탬프 후 새로 고침, 타임 스탬프 및 토큰 형식에 만료. 
    + 새 메서드가 이 메서드가 반환하는 모든 정보를 반환할 때 AksWebservice의 기존 get_token() 메서드가 더 이상 사용되지 않습니다.
    + az ml 서비스 get-access-token 명령의 수정된 출력입니다. 액세스 토큰 및 새로 고침에 토큰 이름을 변경한 후. 추가 된 만료온 및 tokenType 속성입니다.
    + 고정 get_active_runs
  + **azureml-explain-model**
    + 0.33.0으로 업데이트되고 커뮤니티를 0.4.4로 해석합니다.*
  + **azureml-interpret**
    + 0.33.0으로 업데이트되고 커뮤니티를 0.4.4로 해석합니다.*
  + **azureml-기차-자동-런타임**
    + 이진 및 다중 클래스 분류 모두에 대해 Matthews 상관 관계 계수를 분류 메트릭으로 추가했습니다.
    + 코드에서 프로세스 플래그를 사용 해제하고 기본적으로 위화로 대체합니다.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>파이썬 v1.0.83에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + 데이터 집합: 두 `on_error` `out_of_range_datetime` 가지 `to_pandas_dataframe` 옵션을 추가하고 데이터에 `None`를 채우는 대신 오류 값이 있는 경우 실패할 수 있습니다.
  + 작업 영역: `hbi_workspace` 추가 암호화를 가능하게 하고 작업 영역에서 고급 진단을 사용하지 않도록 설정하는 중요한 데이터가 있는 작업 영역에 대한 플래그를 추가했습니다. 또한 작업 영역을 프로비저닝하는 동안 구독에 Cosmos `cmk_keyvault` DB `resource_cmk_uri` 인스턴스를 만드는 작업 영역을 만들 때 및 매개 변수를 지정하여 연결된 Cosmos DB 인스턴스에 대한 고유한 키가져오기를 지원했습니다. [자세한 내용은 여기를 참조하십시오.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **버그 수정 및 개선 사항**
  + **azureml-자동-런타임**
    + 3.5.4 미만의 파이썬 버전에서 AutoML을 실행할 때 TypeError가 발생하는 회귀가 수정되었습니다.
  + **azureml 코어**
    + 시작되지 `./` `datastore.upload_files` 않은 상대 경로가 사용할 수 없는 버그를 수정했습니다.
    + 모든 이미지 클래스 코드 경로에 대한 사용 중단 메시지가 추가되었습니다.
    + Mooncake 영역에 대한 모델 관리 URL 구성을 수정했습니다.
    + Azure Functions에 대해 source_dir 사용하는 모델을 패키징할 수 없는 문제를 수정했습니다.    
    + AzureML 작업 영역 컨테이너 레지스트리에 이미지를 푸시하는 [환경.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 옵션을 추가했습니다.
    + Azure 시냅스에서 새 토큰 라이브러리를 다시 호환되는 방식으로 사용하도록 SDK를 업데이트했습니다.
  + **azureml-interpret**
    + 설명을 다운로드할 수 없을 때 없음이 반환되지 않은 버그를 수정했습니다. 이제 다른 곳에서 동작을 일치시키는 예외를 발생시다.
  + **azureml-pipeline-steps**
    + 에서 사용될 `DatasetConsumptionConfig`때 `Estimator` `Estimator`의 `inputs` 매개 변수에 s를 `EstimatorStep`전달하는 것을 허용하지 않습니다.
  + **azureml-sdk**
    + Azureml-sdk 패키지에 AutoML 클라이언트를 추가하여 전체 AutoML 패키지를 설치하지 않고도 원격 AutoML 실행을 제출할 수 있습니다.
  + **azureml-train-automl-client**
    + 자동 실행용 콘솔 출력에 대한 정렬 수정
    + 팬더의 잘못된 버전이 원격 amlcompute에 설치될 수 있는 버그를 수정했습니다.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>파이썬 v1.0.81에 대한 Azure 기계 학습 SDK

+ **버그 수정 및 개선 사항**
  + **azureml-contrib-interpret**
    + azureml 해석에서 커뮤니티해석에 대한 shap 종속성 연기
  + **azureml 코어**
    + 이제 계산 대상을 해당 배포 구성 개체의 매개 변수로 지정할 수 있습니다. 이 이름은 특히 SDK 개체가 아닌 배포할 계산 대상의 이름입니다.
    + 생성됨 정보를 모델 및 서비스 개체에 추가했습니다. .created_by 통해 <var>액세스할 수 있습니다.
    + Docker 컨테이너의 HTTP 포트를 올바르게 설정하지 않은 컨테이너Image.run()을 수정했습니다.
    + cli `az ml dataset register` 명령에 대해 선택 사항 만들기 `azureml-dataprep`
    + 다른 판독기로 `TabularDataset.to_pandas_dataframe` 잘못 돌아가 경고를 인쇄하는 버그를 수정했습니다.
  + **azureml-explain-model**
    + azureml 해석에서 커뮤니티해석에 대한 shap 종속성 연기
  + **azureml 파이프라인 코어**
    + 파이프라인의 단계로 `NotebookRunnerStep`로컬 전자 필기장을 실행하기 위해 새 파이프라인 단계를 추가했습니다.
    + 게시된 파이프라인, 일정 및 파이프라인Endpoints에 대한 더 이상 사용되지 않은 get_all 함수 제거
  + **azureml-train-automl-client**
    + AutoML에 대한 입력으로 data_script 사용 중단을 시작했습니다.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>파이썬 v1.0.79에 대한 Azure 기계 학습 SDK

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 삭제된 위화기구성기록
      + 로깅을 "자동"/"끄기"/"사용자 지정"만 로깅하도록 업데이트되었습니다.
  + **azureml-자동-런타임**
    + 팬더에 대한 지원이 추가되었습니다. 시리즈와 팬더. 열 데이터 형식을 검색하기 위한 범주형입니다. 이전에는 지원된 numpy.ndarray만 지원되었습니다.
      + 범주형 dtype을 올바르게 처리하기 위해 관련 코드 변경 사항을 추가했습니다.
    + 예측 함수 인터페이스가 개선되었습니다 y_pred. - 문서 문자열이 개선되었습니다.
  + **azureml-contrib-dataset**
    + 레이블이 지정된 데이터 집합을 탑재할 수 없는 버그를 수정했습니다.
  + **azureml 코어**
    + 에 대한 `Environment.from_existing_conda_environment(name, conda_environment_name)`버그 수정 . 사용자는 로컬 환경의 정확한 복제본인 환경 인스턴스를 만들 수 있습니다.
    + 기본적으로 열렬 관련 데이터 `include_boundary=True` 집합 메서드를 변경했습니다.
  + **azureml-train-automl-client**
    + 표시 출력이 false로 설정될 때 유효성 검사 결과가 인쇄되지 않는 문제를 수정했습니다.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>파이썬 v1.0.76에 대한 Azure 기계 학습 SDK

+ **주요 변경 내용**
  + Azureml-기차-자동 ML 업그레이드 문제
    + azureml-train-automl>azureml-train-automl<1.0.76에서 1.0.76으로 업그레이드하면 부분 설치가 발생하여 일부 automl 가져오기가 실패할 수 있습니다. 이 문제를 해결하려면 에서 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd설정된 스크립트를 실행할 수 있습니다. 또는 pip를 직접 사용하는 경우 다음을 수행할 수 있습니다.
      + "핍 설치 --업그레이드 azureml-기차-automl"
      + "핍 설치 --무시 설치 azureml-기차-automl-클라이언트"
    + 또는 업그레이드하기 전에 이전 버전을 제거할 수 있습니다.
      + "핍 제거 azureml-기차-automl"
      + "핍 설치 azureml-기차-automl"

+ **버그 수정 및 개선 사항**
  + **azureml-자동-런타임**
    + 이제 AutoML은 이진 분류 작업에 대한 평균 스칼라 메트릭을 계산할 때 참 클래스와 거짓 클래스를 모두 고려합니다.
    + AzureML-AutoML-Core의 기계 학습 및 학습 코드를 새 패키지AzureML-AutoML-런타임으로 이동했습니다.
  + **azureml-contrib-dataset**
    + 다운로드 `to_pandas_dataframe` 옵션을 사용하여 레이블이 지정된 데이터 집합을 호출할 때 기존 파일을 덮어쓸지 여부를 지정할 수 있습니다.
    + 호출하거나 `keep_columns` `drop_columns` 이로 인해 시간대, 레이블 또는 이미지 열이 삭제되면 데이터 집합에 대해 해당 기능도 삭제됩니다.
    + 개체 검색 작업에 대한 피토치 로더문제가 수정되었습니다.
  + **azureml-contrib-interpret**
    + azureml-contrib-interpret에서 제거 된 설명 대시보드 위젯, 변경 된 패키지를 참조 하 interpret_community
    + 해석 커뮤니티의 버전을 0.2.0으로 업데이트했습니다.
  + **azureml 코어**
    + 의 `workspace.datasets`성능을 향상시킵니다.
    + 사용자 이름 및 암호 인증을 사용하여 Azure SQL Database 데이터 스토어를 등록하는 기능이 추가되었습니다.
    + 상대 경로에서 RunConfigurations로드에 대 한 수정 합니다.
    + 호출하거나 `keep_columns` `drop_columns` 이로 인해 타임시리즈 열이 삭제되면 데이터 집합에 대해해당 기능도 삭제됩니다.
  + **azureml-interpret**
    + 해석 커뮤니티의 업데이트 된 버전 0.2.0
  + **azureml-pipeline-steps**
    + Azure 기계 `runconfig_pipeline_params` 학습 파이프라인 단계에 대해 문서화된 지원값입니다.
  + **azureml 파이프라인 코어**
    + 파이프라인 명령에 대한 json 형식으로 출력을 다운로드하는 CLI 옵션이 추가되었습니다.
  + **azureml 기차 - 오토ml**
    + AzureML-Train-AutoML을 2개의 패키지로 분할하고, 클라이언트 패키지 AzureML-Train-AutoML-클라이언트 및 ML 교육 패키지 AzureML-Train-AutoML-런타임으로 분할
  + **azureml-train-automl-client**
    + 로컬로 기계 학습 종속성을 설치할 필요 없이 AutoML 실험을 제출하기 위한 씬 클라이언트를 추가했습니다.
    + 원격 실행에서 자동으로 감지된 지연, 롤링 창 크기 및 최대 수평선의 로깅을 수정했습니다.
  + **azureml-기차-자동-런타임**
    + 클라이언트에서 기계 학습 및 런타임 구성 요소를 격리하는 새 AutoML 패키지를 추가했습니다.
  + **azureml-콘티브-기차-rl**
    + SDK에서 강화 학습 지원이 추가되었습니다.
    + RL SDK에서 AmlWindowsCompute 지원이 추가되었습니다.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>파이썬 v1.0.74에 대한 Azure 기계 학습 SDK

  + **기능 미리 보기**
    + **azureml-contrib-dataset**
      + azureml-contrib 데이터 집합을 가져온 후 `Dataset.Labeled.from_json_lines` 레이블이 `._Labeled` 지정된 데이터 집합을 만드는 대신 호출할 수 있습니다.
      + 다운로드 `to_pandas_dataframe` 옵션을 사용하여 레이블이 지정된 데이터 집합을 호출할 때 기존 파일을 덮어쓸지 여부를 지정할 수 있습니다.
      + 호출하거나 `keep_columns` `drop_columns` 이로 인해 타임계, 레이블 또는 이미지 열이 삭제되면 데이터 집합에 대해 해당 기능도 삭제됩니다.
      + 호출 `dataset.to_torchvision()`할 때 PyTorch 로더와 문제를 해결했습니다.

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 미리 보기 CLI에 모델 프로파일링을 추가했습니다.
    + Azure ML CLI가 실패하도록 하는 Azure 저장소의 주요 변경 사항을 수정합니다.
    + AKS 유형에 대한 MLC에 로드 밸러서 유형 추가
  + **azureml-automl-core**
    + 타임계에서 최대 수평선을 감지하여 누락된 값과 여러 입자를 발견할 때 문제를 수정했습니다.
    + 교차 유효성 검사 분할 생성 중에 오류가 발생하면서 문제가 해결되었습니다.
    + 이 섹션을 릴리스 노트에 표시할 마크다운 형식의 메시지로 바꿉니다: -예측 데이터 집합에서 짧은 입자 처리 개선.
    + 로깅 하는 동안 일부 사용자 정보를 마스킹 하는 문제를 해결 했습니다. - 예측 실행 중에 오류로깅이 개선되었습니다.
    + 자동 생성된 yml 배포 파일에 conda 종속성으로 psutil을 추가합니다.
  + **azureml-콘티브-미르**
    + Azure ML CLI가 실패하도록 하는 Azure 저장소의 주요 변경 사항을 수정합니다.
  + **azureml 코어**
    + Azure Functions에 배포된 모델이 500을 생성하게 하는 버그를 수정합니다.
    + 스냅샷에 amlignore 파일이 적용되지 않던 문제를 수정했습니다.
    + 지정된 amlcompute에서 실행 및 큐에 대기중인 실행을 위한 생성기를 반환하는 새 API amlcompute.get_active_runs 추가했습니다.
    + AKS 유형에 대한 MLC에 로드 밸러서 유형을 추가했습니다.
    + run.py download_artifacts_from_prefix artifacts_client download_files append_prefix bool 매개 변수를 추가했습니다. 이 플래그는 원본 파일 경로를 선택적으로 병합하는 데 사용되므로 파일 또는 폴더 이름만 output_directory
    + 데이터 집합 사용과 `run_config.yml` 함께 직렬화 문제를 해결합니다.
    + 호출하거나 `keep_columns` `drop_columns` 이로 인해 타임시리즈 열이 삭제되면 데이터 집합에 대해해당 기능도 삭제됩니다.
  + **azureml-interpret**
    + 해석 커뮤니티 버전을 0.1.0.3으로 업데이트했습니다.
  + **azureml 기차 - 오토ml**
    + automl_step 유효성 검사 문제를 인쇄하지 않는 문제를 수정했습니다.
    + 모델의 환경이 로컬에서 종속성이 없는 경우에도 성공하도록 register_model 고정했습니다.
    + 일부 원격 실행이 도커를 사용할 수 없는 문제를 수정했습니다.
    + 로컬 실행이 조기에 실패하게 하는 예외의 로깅을 추가합니다.
  + **azureml-train-core**
    + 자동화된 하이퍼매개 변수 튜닝 최상의 자식 실행을 계산할 때 resume_from 실행을 고려합니다.
  + **azureml 파이프라인 코어**
    + 파이프라인 인수 생성에서 매개 변수 처리를 수정했습니다.
    + 파이프라인 설명 및 단계 유형 yaml 매개 변수를 추가했습니다.
    + 파이프라인 단계에 대한 새로운 yaml 형식과 이전 형식에 대한 사용 중단 경고가 추가되었습니다.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>웹 환경

공동 작업 영역 방문 [https://ml.azure.com](https://ml.azure.com) 페이지가 Azure 기계 학습 스튜디오(미리 보기)로 향상되고 브랜드가 변경되었습니다.

스튜디오에서 데이터 집합, 파이프라인, 모델, 끝점 등과 같은 Azure 기계 학습 자산을 학습, 테스트, 배포 및 관리할 수 있습니다.

스튜디오에서 다음 웹 기반 제작 도구에 액세스합니다.

| 웹 기반 도구 | 설명 | 버전 |
|-|-|-|
| 노트북 VM(미리 보기) | 완벽하게 관리되는 클라우드 기반 워크스테이션 | 기본 & 엔터프라이즈 |
| [자동화된 기계 학습(미리](tutorial-first-experiment-automated-ml.md) 보기) | 기계 학습 모델 개발 자동화를 위한 코드 경험 없음 | Enterprise |
| [디자이너(미리](concept-designer.md) 보기) | 이전에 디자이너로 알려진 끌어서 놓기 기계 학습 모델링 도구 | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure 기계 학습 디자이너 개선 사항

+ 이전에는 시각적 인터페이스로 알려져 있습니다. 
+    추천자, 분류자 및 기능 엔지니어링, 교차 검증 및 데이터 변환을 포함한 교육 유틸리티를 포함한 11개의 새로운 [모듈.](algorithm-module-reference/module-reference.md)

### <a name="r-sdk"></a>R SDK 
 
데이터 과학자와 AI 개발자는 [R용 Azure 기계 학습 SDK를](tutorial-1st-r-experiment.md) 사용하여 Azure 기계 학습을 사용하여 기계 학습 워크플로를 빌드하고 실행합니다.

R용 Azure 기계 학습 SDK는 `reticulate` 패키지를 사용하여 파이썬 SDK에 바인딩합니다. 파이썬에 직접 바인딩하여 R용 SDK를 사용하면 선택한 모든 R 환경에서 Python SDK에서 구현 된 핵심 개체 및 메서드에 액세스 할 수 있습니다.

SDK의 주요 기능은 다음과 같습니다.

+    기계 학습 실험을 모니터링, 로깅 및 구성하는 클라우드 리소스를 관리합니다.
+    GPU 가속 모델 교육을 포함하여 클라우드 리소스를 사용하여 모델을 교육합니다.
+    ACI(Azure 컨테이너 인스턴스) 및 AKS(Azure Kubernetes Service)에서 모델을 웹 서비스로 배포합니다.

전체 설명서를 보려면 [패키지 웹 사이트를](https://azure.github.io/azureml-sdk-for-r) 참조하십시오.

### <a name="azure-machine-learning-integration-with-event-grid"></a>이벤트 그리드를 위한 Azure 기계 학습 통합 

Azure 기계 학습은 이제 이벤트 그리드의 리소스 공급자이며 Azure 포털 또는 Azure CLI를 통해 기계 학습 이벤트를 구성할 수 있습니다. 사용자는 실행 완료, 모델 등록, 모델 배포 및 데이터 드리프트 감지를 위한 이벤트를 만들 수 있습니다. 이러한 이벤트는 소비를 위해 이벤트 그리드에서 지원하는 이벤트 처리기로 라우팅할 수 있습니다. 자세한 내용은 기계 학습 이벤트 [스키마,](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning) [개념](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) 및 [자습서](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) 문서를 참조하십시오.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>파이썬 v1.0.72에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) 패키지를 통해 데이터 집합 모니터를 추가하여 시간 시간에 따라 데이터 드리프트 또는 기타 통계 적 변화에 대한 시간계 데이터 집합을 모니터링할 수 있습니다. 드리프트가 감지되거나 데이터에 대한 다른 조건이 충족되면 경고 및 이벤트가 트리거될 수 있습니다. 자세한 내용은 [설명서를](https://aka.ms/datadrift) 참조하십시오.
  + Azure 기계 학습에서 두 개의 새 버전(SKU 와 교환)을 발표합니다. 이 릴리스를 사용하면 이제 기본 또는 엔터프라이즈 Azure 기계 학습 작업 영역을 만들 수 있습니다. 모든 기존 작업 영역은 기본 값으로 기본값으로 기본값으로 설정되며 Azure 포털 또는 스튜디오로 이동하여 언제든지 작업 영역을 업그레이드할 수 있습니다. Azure 포털에서 기본 또는 엔터프라이즈 작업 영역을 만들 수 있습니다. 자세한 내용은 [설명서를](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) 참조하십시오. SDK에서 작업 영역 의 버전은 작업 영역 개체의 "sku" 속성을 사용하여 결정할 수 있습니다.
  + 또한 Azure Machine Learning Compute를 개선했습니다 - 이제 Azure Monitor에서 클러스터에 대한 메트릭(예: 총 노드, 실행 중인 노드, 총 코어 할당량)을 볼 수 있으며 디버깅을 위한 진단 로그를 볼 수 있습니다. 또한 클러스터에서 현재 실행 중이거나 대기 중인 실행및 클러스터의 다양한 노드의 IP와 같은 세부 정보를 볼 수도 있습니다. 포털에서 또는 SDK 또는 CLI에서 해당 함수를 사용하여 이러한 기능을 볼 수 있습니다.

  + **기능 미리 보기**
    + Azure 기계 학습 계산에서 로컬 SSD의 디스크 암호화에 대한 미리 보기 지원을 릴리스합니다. 이 기능을 사용하려면 구독을 화이트리스트에 올리려면 기술 지원 티켓을 올립니다.
    + Azure 기계 학습 일괄 처리 추론의 공개 미리 보기입니다. Azure 기계 학습 일괄 처리 추론은 시간에 민감하지 않은 대규모 추론 작업을 대상으로 합니다. 일괄 처리 추론은 비동기 응용 프로그램에 대한 비교할 수 없는 처리량으로 비용 효율적인 추론 계산 크기 조정을 제공합니다. 대량의 데이터 컬렉션에 대한 높은 처리량, 화재 및 잊어버린 추론에 최적화되어 있습니다.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + 레이블이 지정된 데이터 집합에 대한 활성화된 기능
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI는 이제 모델 패키징을 지원합니다.
    + 데이터 집합 CLI를 추가했습니다. 참조 항목: `az ml dataset --help`
    + 인ferenceConfig 인스턴스 없이 지원되는 모델(ONNX, scikit-learn 및 TensorFlow)을 배포하고 패키징할 수 있는 지원이 추가되었습니다.
    + SDK 및 CLI에서 서비스 배포(ACI 및 AKS)에 대한 덮어쓰기 플래그가 추가되었습니다. 제공된 경우 이름이 있는 서비스가 이미 있는 경우 기존 서비스를 덮어씁니다. 서비스가 없으면 새 서비스가 생성됩니다.
    + 모델은 Onnx및 Tensorflow의 두 개의 새로운 프레임워크로 등록할 수 있습니다. - 모델 등록은 모델에 대한 샘플 입력 데이터, 샘플 출력 데이터 및 리소스 구성을 허용합니다.
  + **azureml-automl-core**
    + 런타임 제약 조건이 설정되는 경우에만 반복교육이 자식 프로세스에서 실행됩니다.
    + 지정된 max_horizon 지정된 컴퓨터에서 메모리 문제가 발생할지 여부를 확인하기 위해 예측 작업에 대한 가드레일을 추가했습니다. 이 경우 가드레일 메시지가 표시됩니다.
    + 2년 1개월 과 같은 복잡한 주파수에 대한 지원이 추가되었습니다. - 주파수를 확인할 수없는 경우 이해할 수있는 오류 메시지가 추가되었습니다.
    + 모델 배포 실패를 해결하기 위해 자동 생성 콘다 env에 azureml 기본값을 추가합니다.
    + Azure 기계 학습 파이프라인의 중간 데이터를 테이블 형식 데이터 집합으로 변환하여 에서 사용할 수 있도록 허용합니다. `AutoMLStep`
    + 스트리밍을 위한 열 용도 업데이트를 구현했습니다.
    + 스트리밍을 위한 Imputer 및 HashOneHotEncoder에 대한 트랜스포머 매개 변수 업데이트를 구현했습니다.
    + 유효성 검사 오류 메시지에 현재 데이터 크기와 필요한 최소 데이터 크기를 추가했습니다.
    + 각 유효성 검사 폴드에서 최소 두 개의 샘플을 보장하기 위해 교차 유효성 검사에 필요한 최소 데이터 크기를 업데이트했습니다.
  + **azureml-cli-common**
    + CLI는 이제 모델 패키징을 지원합니다.
    + 모델은 Onnx및 Tensorflow의 두 개의 새로운 프레임워크로 등록할 수 있습니다.
    + 모델 등록은 모델에 대한 샘플 입력 데이터, 샘플 출력 데이터 및 리소스 구성을 허용합니다.
  + **azureml-콘티브-gbdt**
    + 노트북의 릴리즈 채널을 수정했습니다.
    + 지원하지 않는 AmlCompute 가 아닌 계산 대상에 대한 경고가 추가되었습니다.
    + Azureml-contrib-gbdt 패키지에 LightGMB 추정기 추가
  + [**azureml 코어**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI는 이제 모델 패키징을 지원합니다.
    + 더 이상 사용되지 이상 처리된 데이터 집합 API에 대한 사용 중단 경고를 추가합니다. 데이터 집합 API 변경 https://aka.ms/tabular-dataset공지를 참조하십시오.
    + 데이터 [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) 집합이 등록된 경우 등록 이름과 버전을 반환하도록 변경합니다.
    + 데이터 집합을 인수로 ScriptRunConfig가 실험 실행을 제출하는 데 반복적으로 사용할 수 없는 버그를 수정합니다.
    + 실행 중에 검색된 데이터 집합이 추적되며 실행 세부 정보 페이지에서 [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) 또는 실행이 완료된 후 호출하여 볼 수 있습니다.
    + Azure 기계 학습 파이프라인의 중간 데이터를 테이블 형식 데이터 집합으로 변환하여 에서 사용할 수 있도록 허용합니다. [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)
    + 인ferenceConfig 인스턴스 없이 지원되는 모델(ONNX, scikit-learn 및 TensorFlow)을 배포하고 패키징할 수 있는 지원이 추가되었습니다.
    + SDK 및 CLI에서 서비스 배포(ACI 및 AKS)에 대한 덮어쓰기 플래그가 추가되었습니다. 제공된 경우 이름이 있는 서비스가 이미 있는 경우 기존 서비스를 덮어씁니다. 서비스가 없으면 새 서비스가 생성됩니다.
    +  모델은 Onnx및 Tensorflow의 두 개의 새로운 프레임워크로 등록할 수 있습니다. 모델 등록은 모델에 대한 샘플 입력 데이터, 샘플 출력 데이터 및 리소스 구성을 허용합니다.
    + MySQL용 Azure 데이터베이스에 대한 새 데이터 스토어를 추가했습니다. Azure 기계 학습 파이프라인의 데이터 전송 단계에서 MySQL에 대한 Azure 데이터베이스 사용에 대한 예제가 추가되었습니다.
    + 실험에서 태그를 추가 및 제거하는 기능이 추가되었습니다.
    + SDK 및 CLI에서 서비스 배포(ACI 및 AKS)에 대한 덮어쓰기 플래그가 추가되었습니다. 제공된 경우 이름이 있는 서비스가 이미 있는 경우 기존 서비스를 덮어씁니다. 서비스가 없으면 새 서비스가 생성됩니다.
  + [**azureml-데이터 드리프트**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 에서 `azureml-contrib-datadrift` 이동`azureml-datadrift`
    + 드리프트 및 기타 통계 측정을 위한 시간계 데이터 세트 모니터링 지원 추가
    + 새로운 `create_from_model()` 메서드와 `create_from_dataset()` [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) 클래스에. 메서드는 `create()` 더 이상 사용되지 않습니다.
    + Azure 기계 학습 스튜디오에서 파이썬 및 UI의 시각화를 조정합니다.
    + 데이터 집합 모니터에 대한 일일 뿐만 아니라 주간 및 월간 모니터 스케줄링을 지원합니다.
    + 데이터 모니터 메트릭의 백필을 지원하여 데이터 집합 모니터에 대한 기록 데이터를 분석합니다.
    + 다양한 버그 수정
  + [**azureml 파이프라인 코어**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep은 더 이상 파이프라인 파일에서 Azure 기계 `yaml` 학습 파이프라인 실행을 제출하는 데 필요하지 않습니다.
  + [**azureml 기차 - 오토ml**](/python/api/azureml-train-automl-runtime/)
    + 모델 배포 실패를 해결하기 위해 자동 생성 콘다 env에 azureml 기본값을 추가합니다.
    + AutoML 원격 학습에는 추론을 위해 교육 env를 다시 사용할 수 있도록 azureml 기본값이 포함됩니다.
  + **azureml-train-core**
    + 추정기에서 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) PyTorch 1.3 지원 추가

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>비주얼 인터페이스(미리 보기)

+ Azure 기계 학습 시각적 인터페이스(미리 보기)가 [Azure 기계 학습 파이프라인에서](concept-ml-pipelines.md)실행되도록 정비되었습니다. 시각적 인터페이스에 작성된 파이프라인(이전의 실험)은 이제 핵심 Azure 기계 학습 환경과 완전히 통합됩니다.
  + SDK 자산을 위한 통합 관리 경험
  + 시각적 인터페이스 모델, 파이프라인 및 끝점에 대한 버전 전환 및 추적
  + 새롭게 디자인된 UI
  + 추가된 일괄 처리 추론 배포
  + 추론 계산 대상에 대한 AZURE Kubernetes 서비스(AKS) 지원 추가
  + 새로운 파이썬 단계 파이프라인 작성 워크플로우
  + 시각적 작성 도구에 대한 새 [방문 페이지](https://ml.azure.com)

+ **새로운 모듈**
  + 수학 작업 적용
  + SQL 변환 적용
  + 클립 값
  + 데이터 요약
  + SQL 데이터베이스에서 가져오기

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>파이썬 v1.0.69에 대한 Azure 기계 학습 SDK

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 모든 실행에 대한 설명을 계산하는 대신 모델 설명을 가장 잘 실행하도록 제한합니다. 로컬, 원격 및 ADB에 대해 이 동작을 변경합니다.
    + UI에 대한 주문형 모델 설명에 대한 지원이 추가되었습니다.
    + amlcompute에서 `automl` 콘다 종속성으로 psutil을 종속성으로 추가하고 포함시켰습니다.
    + 예측 데이터 세트의 추론 지연 및 롤링 창 크기 로 인해 선형 대수 오류가 발생할 수 있는 일부 계열문제를 수정했습니다.
      + 예측 실행에서 추론적으로 결정된 매개변수에 대해 인쇄물을 추가했습니다.
  + **azureml-콘티브 데이터 드리프트**
    + 데이터 집합 수준 드리프트가 첫 번째 섹션에 없는 경우 출력 메트릭을 만드는 동안 보호를 추가했습니다.
  + **azureml-contrib-interpret**
    + azureml-contrib-설명-모델 패키지이름이 azureml-contrib 해석으로 변경되었습니다.
  + **azureml 코어**
    + 데이터 집합을 등록 취소하기 위해 API를 추가했습니다. `dataset.unregister_all_versions()`
    + azureml-contrib-설명-모델 패키지이름이 azureml-contrib 해석으로 변경되었습니다.
  + **[azureml 코어](https://docs.microsoft.com/python/api/azureml-core)**
    + 데이터 집합을 등록 취소하기 위해 API를 추가했습니다. Dataset. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + 데이터 변경 시간을 확인하기 위해 데이터 집합 API를 추가했습니다. `dataset.data_changed_time`.
    + 및 Azure `FileDataset` 기계 `TabularDataset` 학습 `PythonScriptStep` `HyperDriveStep` 파이프라인에서 `EstimatorStep`를 사용하고 입력으로 사용할 수 있는
    + 파일 `FileDataset.mount` 수가 많은 폴더의 성능이 향상되었습니다.
    + Azure 기계 학습 파이프라인에서 [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [추정 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)및 [하이퍼드라이브스텝에](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) 대한 입력으로 [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) 및 [TabularDataset을](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) 사용할 수 있습니다.
    + 파일 데이터 집합의 성능입니다. [마운트()가](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) 많은 파일이 있는 폴더에 대해 개선되었습니다.
    + 실행 세부 정보에서 알려진 오류 권장 사항에 URL을 추가했습니다.
    + 실행에 자식이 너무 많으면 요청이 실패하는 run.get_metrics 버그를 수정했습니다.
    + 실행에 자식이 너무 많으면 요청이 실패하는 [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) 버그를 수정했습니다.
    + Arcadia 클러스터에서 인증에 대한 지원이 추가되었습니다.
    + 실험 개체를 만들면 실행 기록 추적을 위해 Azure 기계 학습 작업 영역에서 실험을 얻거나 만듭니다. 실험 ID와 보관된 시간은 생성 시 실험 개체에 채워집니다. 예: 실험 = 실험(작업 공간, "새 실험") experiment_id = experiment.id archive() 및 reactivate())는 실험을 숨기고 복원하기 위해 실험을 URL에 표시하거나 목록 실험에 기본적으로 반환할 수 있는 함수입니다. 보관된 실험과 이름이 같은 새 실험을 만든 경우 새 이름을 전달하여 다시 활성화할 때 보관된 실험의 이름을 바꿀 수 있습니다. 지정된 이름으로 하나의 활성 실험만 있을 수 있습니다. 예: 실험1 = 실험(작업 공간, "활성 실험") experiment1.archive() # 보관된 이름과 동일한 이름으로 새 활성 실험 을 만듭니다. 실험2. = 실험(작업 공간, "활성 실험") experiment1.reactivate(new_name="이전 활성 실험") 실험의 정적 메서드 목록()은 이름 필터 및 ViewType 필터를 취할 수 있습니다. ViewType 값은 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL" 예제: archived_experiments = Experiment.list(작업 공간, view_type=ARCHIVED_ONLY") all_first_experiments = Experiment.list(작업 공간, 이름="첫 번째 실험", view_type="ALL")
    + 모델 배포 및 서비스 업데이트에 대한 환경 사용 지원
  + **azureml-데이터 드리프트**
    + DataDriftDector 클래스의 표시 특성은 더 이상 선택적 인수 'with_details'를 지원하지 않습니다. 표시 특성은 피처 열의 데이터 드리프트 계수 및 데이터 드리프트 기여만 표시합니다.
    + DataDriftDetector 특성 'get_output' 동작 변경:
      + 입력 매개 변수start_time 필수 가 아닌 선택 사항인 end_time.
      + 동일한 호출에 특정 run_id 입력된 특정 start_time 및/또는 end_time 상호 배타적이기 때문에 값 오류 예외가 발생합니다.
      + 입력된 특정 start_time 및/또는 end_time 예약된 실행의 결과만 반환됩니다.
      + 매개 변수 'daily_latest_only'은 더 이상 사용되지 않습니다.
    + 데이터 집합 기반 데이터 드리프트 출력 검색을 지원합니다.
  + **azureml-explain-model**
    + AzureML-explain-model 패키지의 이름을 AzureML-interpret로 변경하여 이전 패키지의 이전 패키지를 이전 버전과의 호환성을 유지합니다.
    + [오세연서에서 다운로드 할 때 기본적으로 회귀 대신 분류 작업으로 설정된 원시 설명으로 수정된 `automl` 버그
    + 직접 만들 `ScoringExplainer` 수 있도록 지원 추가`MimicWrapper`
  + **azureml 파이프라인 코어**
    + 대규모 파이프라인 생성을 위한 성능 향상
  + **azureml-train-core**
    + 텐서플로우 추정기에 텐서플로우 2.0 지원 추가
  + **azureml 기차 - 오토ml**
    + [실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) 개체를 만들면 실행 기록 추적을 위해 Azure 기계 학습 작업 영역에서 실험을 얻거나 만듭니다. 실험 ID와 보관된 시간은 생성 시 실험 개체에 채워집니다. 예:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) 및 [reactivate())는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) 실험을 숨기고 UX에 표시되지 않도록 복원하기 위해 실험에서 호출할 수 있는 함수로, 실험 목록 에 대한 호출에서 기본적으로 반환됩니다. 보관된 실험과 이름이 같은 새 실험을 만든 경우 새 이름을 전달하여 다시 활성화할 때 보관된 실험의 이름을 바꿀 수 있습니다. 지정된 이름으로 하나의 활성 실험만 있을 수 있습니다. 예:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        실험에서 정적 메서드 [목록()은](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) 이름 필터 및 ViewType 필터를 취할 수 있습니다. 뷰타입 값은 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL"입니다. 예:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 모델 배포 및 서비스 업데이트에 대한 환경 사용을 지원합니다.
  + **[azureml-데이터 드리프트](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) 클래스의 표시 특성은 더 이상 선택적 인수 'with_details'를 지원하지 않습니다. 표시 특성은 피처 열의 데이터 드리프트 계수 및 데이터 드리프트 기여만 표시합니다.
    + DataDriftDetector 기능 [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) 동작 변경:
      + 입력 매개 변수start_time 필수 가 아닌 선택 사항인 end_time.
      + 동일한 호출에 특정 run_id start_time 및/또는 end_time 입력하면 상호 배타적이기 때문에 값 오류 예외가 발생합니다.
      + 입력된 특정 start_time 및/또는 end_time 예약된 실행의 결과만 반환됩니다.
      + 매개 변수 'daily_latest_only'은 더 이상 사용되지 않습니다.
    + 데이터 집합 기반 데이터 드리프트 출력 검색을 지원합니다.
  + **azureml-explain-model**
    + AzureML 설명 모델 패키지의 이름을 AzureML 해석으로 변경하여 이전 패키지를 이전 패키지로 유지하여 이전 버전과의 호환성을 유지합니다.
    + 설명 클라이언트에서 다운로드에 기본적으로 회귀 대신 분류 작업으로 설정 원시 설명과 AutoML 버그를 수정했습니다.
    + MimicWrapper를 사용하여 직접 만들 수 있는 [점수 매기기 설명자에](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) 대한 지원 추가
  + **[azureml 파이프라인 코어](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 대규모 파이프라인 생성을 위한 성능 이 향상되었습니다.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + 텐서플로우 추정기에서 [텐서플로우](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) 2.0 지원이 추가되었습니다.
  + **[azureml 기차 - 오토ml](/python/api/azureml-train-automl-runtime/)**
    + 오케스트레이션이 이미 처리하므로 설정 반복이 실패하면 부모 실행이 더 이상 실패하지 않습니다.
    + AutoML 실험에 대한 로컬 도커 및 로컬 콘다 지원이 추가되었습니다.
    + AutoML 실험에 대한 로컬 도커 및 로컬 콘다 지원이 추가되었습니다.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure 기계 학습 작업 영역에 대한 새 웹 환경(미리 보기)

[새 작업 영역 포털의](https://ml.azure.com) 실험 탭이 업데이트되어 데이터 과학자가 보다 성능이 뛰어난 방식으로 실험을 모니터링할 수 있습니다. 다음 기능을 탐색할 수 있습니다.
+ 실험 메타데이터로 실험 실험 목록 쉽게 필터링 및 정렬
+ 실행을 시각화하고 비교할 수 있는 간소화되고 성능이 뛰어난 실험 세부 정보 페이지
+ 교육 실행을 이해하고 모니터링하기 위한 세부 정보 페이지를 실행하는 새로운 디자인

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>파이썬 v1.0.65에 대한 Azure 기계 학습 SDK

  + **새로운 기능**
    + 선별된 환경이 추가되었습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성되었으며 더 빠른 실행을 위해 Docker 이미지로 미리 빌드및 캐시되었습니다. 기본적으로 "AzureML" 접두사와 함께 작업 영역의 환경 목록에 나타납니다.
    + 선별된 환경이 추가되었습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성되었으며 더 빠른 실행을 위해 Docker 이미지로 미리 빌드및 캐시되었습니다. 기본적으로 [작업 영역의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)환경 목록에 나타나며 접두사 "AzureML"이 있습니다.

  + **azureml 기차 - 오토ml**
  + **[azureml 기차 - 오토ml](/python/api/azureml-train-automl-runtime/)**
    + ADB 및 HDI에 대한 ONNX 변환 지원이 추가되었습니다.

+ **기능 미리 보기**
  + **azureml 기차 - 오토ml**
  + **[azureml 기차 - 오토ml](/python/api/azureml-train-automl-runtime/)**
    + 텍스트 위화자로 지원되는 BERT 및 BiLSTM(미리 보기만)
    + 컬럼 목적 및 변압기 매개 변수에 대한 지원되는 위화 맞춤설정(미리 보기만)
    + 사용자가 교육 중에 모델 설명을 활성화할 때 지원되는 원시 설명(미리 보기만)
    + 교육 가능한 `timeseries` 파이프라인으로 예측하기 위한 예언자 추가(미리 보기만 가능)

  + **azureml-콘티브 데이터 드리프트**
    + azureml-contrib-datadrift에서 azureml-datadrift로 재배치된 패키지; 패키지는 `contrib` 향후 릴리스에서 제거됩니다.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 오토MLConfig 및 자동 ML베이스설정에 대한 위화화컨피그 도입
    + [오토MLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 및 자동 ML베이스설정에 대한 위화화컨피그 도입
      + 주어진 열 및 피처 유형으로 특성화를 위한 열 목적 재정의
      + 변압기 매개변수 재정의
    + explain_model() 및 retrieve_model_explanations()
    + 교육 가능한 파이프라인으로 선지자추가(미리 보기만)
    + explain_model() 및 retrieve_model_explanations()에 대한 사용 중단 메시지가 추가되었습니다.
    + 예언자를 학습 가능한 파이프라인으로 추가했습니다(미리 보기만).
    + 대상 지연, 롤링 윈도우 크기 및 최대 수평선의 자동 감지를 지원합니다. target_lags, target_rolling_window_size 또는 max_horizon 중 하나가 '자동'으로 설정된 경우 추론이 적용되어 학습 데이터를 기반으로 해당 매개 변수의 값을 추정합니다.
    + 데이터 집합에 하나의 그레인 열이 포함된 경우 이 그레인은 숫자 유형이며 학습과 테스트 세트 사이에 간격이 있는 경우 수정되었습니다.
    + 예측 작업에서 원격 실행의 중복 된 인덱스에 대한 오류 메시지가 수정되었습니다.
    + 데이터 집합에 하나의 그레인 열이 포함된 경우 이 그레인은 숫자 유형이며 학습과 테스트 집합 사이에 간격이 있는 경우 를 수정했습니다.
    + 예측 작업에서 원격 실행의 중복된 인덱스에 대한 오류 메시지가 수정되었습니다.
    + 데이터 집합의 불균형 여부를 확인하기 위해 가드레일을 추가했습니다. 이 경우 가드레일 메시지가 콘솔에 기록됩니다.
  + **azureml 코어**
    + 모델 개체를 통해 저장소에서 모델링할 SAS URL을 검색하는 기능이 추가되었습니다. 예: model.get_sas_url()
    + `run.get_details()['datasets']` 제출된 실행과 연결된 데이터 집합을 가져옵니다.
    + API를 `Dataset.Tabular.from_json_lines_files` 추가하여 JSON 라인 파일에서 테이블 형식데이터 집합을 만듭니다. 표형식데이터 집합의 JSON 라인 파일에서 이 테이블 형식 https://aka.ms/azureml-data 데이터에 대해 알아보려면 설명서를 방문하십시오.
    + supported_vmsizes() 기능에 VM 크기 필드(OS 디스크, GPU 수)를 추가했습니다.
    + 실행, 개인 및 공용 IP, 포트 등을 표시하는 list_nodes () 함수에 추가 된 필드가 추가되었습니다.
    + 클러스터 프로비저닝 중에 새 필드를 지정하는 기능 -remotelogin_port_public_access 클러스터를 만들 때 SSH 포트를 열어 두거나 닫을지 여부에 따라 활성화 또는 비활성화하도록 설정할 수 있습니다. 지정하지 않으면 VNet 내에 클러스터를 배포하는지 여부에 따라 서비스가 포트를 현명하게 열거나 닫습니다.
  + **azureml-explain-model**
  + **[azureml 코어](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 모델 개체를 통해 저장소에서 모델링할 SAS URL을 검색하는 기능이 추가되었습니다. 예: 모델. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 실행을 소개합니다. [제출된](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)실행과 연결된 데이터 집합을 얻으려면 [데이터 집합'을 get_details.]
    + API `Dataset.Tabular`를 추가합니다. [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) JSON 라인 파일에서 테이블 데이터 집합을 만듭니다. 표형식데이터 집합의 JSON 라인 파일에서 이 테이블 형식 https://aka.ms/azureml-data 데이터에 대해 알아보려면 설명서를 방문하십시오.
    + [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 기능에 VM 크기 필드(OS 디스크, GPU 수)를 추가했습니다.
    + 실행, 개인 및 공용 IP, 포트 등을 표시 하기 위해 [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 함수에 추가 된 필드를 추가 했습니다.
    + 클러스터 [프로비저닝](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` 중에 SSH 포트를 열어 두거나 닫아 둘지 여부에 따라 활성화 또는 비활성화하도록 설정할 수 있는 클러스터 프로비저닝 중에 새 필드를 지정하는 기능입니다. 지정하지 않으면 VNet 내에 클러스터를 배포하는지 여부에 따라 서비스가 포트를 현명하게 열거나 닫습니다.
  + **azureml-explain-model**
    + 분류 시나리오에서 설명 출력에 대한 향상된 설명서입니다.
    + 평가 예에 대한 설명에 예측된 y 값을 업로드하는 기능을 추가했습니다. 더 유용한 시각화의 잠금을 해제합니다.
    + 기본 MimicExplainer를 가져오는 데 사용할 수 있도록 MimicWrapper에 설명자 속성을 추가했습니다.
  + **azureml 파이프라인 코어**
    + 모듈, 모듈버전 및 모듈스텝을 설명하는 노트북 추가
  + **azureml-pipeline-steps**
    + AML 파이프라인을 통해 R 스크립트 실행을 지원하기 위해 RScriptStep을 추가했습니다.
    + AzureBatchStep에서 "Parameter SubscriptionId에 대한 할당이 지정되지 않음"이라는 오류 메시지를 일으키는 메타데이터 매개 변수를 구문 분석하는 수정했습니다.
  + **azureml 기차 - 오토ml**
    + 데이터 입력 형식으로 지원되는 training_data, validation_data, label_column_name weight_column_name
    + explain_model() 및 retrieve_model_explanations()
  + **[azureml 파이프라인 코어](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + [모듈,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)) [모듈버전](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) 및 [모듈스텝을](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)설명하는 [노트북을](https://aka.ms/pl-modulestep) 추가했습니다.
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + AML 파이프라인을 통해 R 스크립트 실행을 지원하기 위해 [RScriptStep을](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) 추가했습니다.
    + "Parameter SubscriptionId에 대한 할당이 지정되지 않음"이라는 오류 메시지를 일으키는 [AzureBatchStep에서](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) 분석하는 메타데이터 매개 변수를 수정했습니다.
  + **[azureml 기차 - 오토ml](/python/api/azureml-train-automl-runtime/)**
    + 지원되는 training_data, validation_data, label_column_name weight_column_name 데이터 입력 형식으로 합니다.
    + [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) 및 [retrieve_model_explanations()에](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)대한 사용 중단 메시지가 추가되었습니다.


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>파이썬 v1.0.62에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + 표형식데이터 집합에서 `timeseries` 특성을 도입했습니다. 이 특성을 사용하면 시간 범위 또는 최신 데이터 간에 모든 데이터를 가져가는 등 TabularDataset 데이터에 대한 쉬운 타임스탬프 필터링이 가능합니다. 이 에 대해 `timeseries` 자세히 알아보려면 TabularDataset의 특성에 대해 자세히 알아보려면 설명서 또는 https://aka.ms/azureml-data https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 예제 노트북을 방문하십시오.
  + 테이블 형식데이터 집합 및 파일 데이터 집합을 사용하여 교육을 활성화했습니다. 예제 https://aka.ms/dataset-tutorial 노트북을 방문하십시오.

  + **azureml-train-core**
      + PyTorch 추정기에 추가 `Nccl` 및 `Gloo` 지원

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoML 설정 'lag_length'과 지연 변압기를 더 이상 사용되지 않습니다.
    + 데이터 흐름 형식으로 지정된 경우 입력 데이터의 올바른 유효성 검사 수정
    + fit_pipeline.py를 수정하여 그래프 json을 생성하고 아티팩트에 업로드합니다.
    + 아래 `userrun` 그래프를 렌더링합니다. `Cytoscape`
  + **azureml 코어**
    + ADB 코드의 예외 처리를 다시 검토하고 새 오류 처리에 따라 변경합니다.
    + 노트북 VM에 대한 자동 MSI 인증이 추가되었습니다.
    + 실패한 재시도로 인해 손상되거나 빈 모델을 업로드할 수 있는 버그를 수정합니다.
    + `DataReference` 모드가 변경될 `DataReference` 때 이름이 변경되는 버그를 수정했습니다(예: `as_mount`호출 `as_upload`시 , `as_download`또는).
    + 및 `mount_point` `FileDataset.download` `target_path` 에 `FileDataset.mount` 대한 및 선택 사항 확인
    + 타임스탬프 열을 찾을 수 없는 경우 는 미세 타임스탬프 열을 할당하지 않고 타임직렬 관련 API를 호출하거나 할당된 타임스탬프 열을 삭제하면 삭제됩니다.
    + 시간 직렬 열은 형식이 Date인 열로 할당되어야 하며, 그렇지 않으면 예외가 예상됩니다.
    + API 'with_timestamp_columns'를 할당하는 시간 렬부 열은 이전에 할당된 타임스탬프 열을 지우는 None 값 미세/거친 타임스탬프 열 이름을 사용할 수 있습니다.
    + 삭제 목록에서 타임스탬프 열을 제외하거나 타임스탬프 열을 해제할 없음 값이 있는 호출 with_time_stamp 후 삭제할 수 있음을 사용자에게 표시하여 굵은 그레인 또는 미세한 타임스탬프 열을 삭제하면 예외가 throw됩니다.
    + 굵은 그레인 또는 미세한 타임스탬프 열이 keep 열 목록에 포함되지 않은 경우 예외가 발생하며, 유지 열 목록에 타임스탬프 열을 포함하거나 타임스탬프 열을 해제할 수 있는 없음 값을 사용하여 with_time_stamp 호출한 후 유지할 수 있음을 사용자에게 표시합니다.
    + 등록된 모델의 크기에 대한 로깅을 추가했습니다.
  + **azureml-explain-model**
    + "패키징" 파이썬 패키지가 설치되지 않은 경우 콘솔에 경고가 인쇄된 수정사항: "지원되는 lightgbm 버전보다 오래된 버전을 사용하여 2.2.1보다 큰 버전으로 업그레이드하십시오."
    + 많은 기능을 갖춘 글로벌 설명을 위해 샤딩을 통해 다운로드 모델 설명을 수정했습니다.
    + 출력 설명에서 초기화 예제가 누락된 모방 설명자 수정
    + 두 가지 유형의 모델을 사용하여 설명 클라이언트로 업로드할 때 설정 속성에 대한 불변 오류가 수정되었습니다.
    + 점수 설명자 .explain()에 get_raw 매개 변수를 추가하여 한 채점 설명자가 엔지니어링 된 값과 원시 값을 모두 반환 할 수 있습니다.
  + **azureml 기차 - 오토ml**
    + 설명 SDK에서 `automl` 설명을 지원하기 위한 AutoML의 공용 API 도입 - AutoML 위화도를 분리하여 AutoML 설명을 지원하는 새로운 방법 및 SDK 설명 - Azureml의 통합 원시 설명 지원은 AutoML 모델에 대한 SDK를 설명합니다.
    + 원격 학습 환경에서 azureml 기본값을 제거합니다.
    + 기본 캐시 저장소 위치를 FileCacheStore 기반에서 Azure Databricks 코드 경로의 AutoML에 대한 AzureFileCacheStore 로 변경했습니다.
    + 데이터 흐름 형식으로 지정된 경우 입력 데이터의 올바른 유효성 검사 수정
  + **azureml-train-core**
    + source_directory_data_store 사용 중단을 되돌렸습니다.
    + azureml 설치 패키지 버전을 재정의하는 기능이 추가되었습니다.
    + 추정기의 매개 `environment_definition` 변수에 dockerfile 지원이 추가되었습니다.
    + 추정기에서 단순화된 분산 교육 매개 변수.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure 기계 학습 작업 영역에 대한 새 웹 환경(미리 보기)
새로운 웹 경험을 통해 데이터 과학자와 데이터 엔지니어는 데이터 준비 및 시각화에서 부터 단일 위치에서 모델 교육 및 배포에 이르기까지 엔드 투 엔드 머신 러닝 라이프사이클을 완료할 수 있습니다.

![Azure 기계 학습 작업 영역 UI(미리 보기)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**주요 기능:**

이 새로운 Azure 기계 학습 인터페이스를 사용하여 다음을 수행할 수 있습니다.
+ 노트북 관리 또는 Jupyter에 연결
+ [자동화된 ML 실험 실행](tutorial-first-experiment-automated-ml.md)
+ [로컬 파일, 데이터 스토어, & 웹 파일에서 데이터 집합 만들기](how-to-create-register-datasets.md)
+ 모델 작성을 위한 데이터 집합 준비 & 탐색
+ 모델에 대한 데이터 드리프트 모니터링
+ 대시보드에서 최근 리소스 보기

크롬, 파이어 폭스, 사파리, 그리고 마이크로소프트 가장자리 미리 보기: 이 릴리스의 시간에, 다음 브라우저지원 됩니다.

**알려진 문제:**

1. 당신이 볼 경우 브라우저를 새로 고침 "뭔가 잘못되었습니다! 배포가 진행 중일 때 "청크 파일 로드 오류"

1. 전자 필기장 및 파일에서 파일을 삭제하거나 이름을 바꿀 수 없습니다. 공개 미리 보기 중에 노트북 VM의 Jupyter UI 또는 터미널을 사용하여 업데이트 파일 작업을 수행할 수 있습니다. 탑재된 네트워크 파일 시스템이므로 노트북 VM에서 변경한 모든 변경 사항이 전자 필기장 작업 영역에 즉시 반영됩니다.

1. 노트북 VM에 SSH를 입력하려면 다음을 수행합니다.
   1. VM 설정 중에 생성된 SSH 키를 찾습니다. 또는 Azure 기계 학습 작업 영역에서 키를 찾을 > 계산 탭을 열면 목록에서 전자 필기장 VM을 찾을 > > 속성을 열수 있습니다: 대화 상자에서 키를 복사합니다.
   1. 이러한 공용 및 개인 SSH 키를 로컬 컴퓨터로 가져옵니다.
   1. 이를 사용하여 전자 필기장 VM에 SSH를 넣을 수 있습니다.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>파이썬 v1.0.60에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + 데이터스토어 또는 공용 URL의 단일 또는 다중 파일을 참조하는 FileDataset을 도입했습니다. 파일은 모든 형식일 수 있습니다. FileDataset 다운로드 하거나 계산에 파일을 탑재 하는 기능을 제공 합니다. FileDataset에 대해 자세히 https://aka.ms/file-dataset알아보려면 다음을 방문하십시오.
  + 파이썬 스크립트 단계, 애들라 단계, 데이터 브릭 단계, 데이터 전송 단계 및 AzureBatch 단계에 대한 파이프 라인 Yaml 지원이 추가되었습니다.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoArima는 이제 미리 보기전용으로 제안된 파이프라인입니다.
    + 예측을 위한 향상된 오류 보고.
    + 예측 작업에서 일반 대신 사용자 지정 예외를 사용하여 로깅을 개선했습니다.
    + max_concurrent_iterations 검사에서 총 반복 횟수보다 적게 제거했습니다.
    + 이제 자동 ML 모델이 자동 ML예외를 반환합니다.
    + 이 릴리스는 자동화된 기계 학습 로컬 실행의 실행 성능을 향상시킵니다.
  + **azureml 코어**
    + 등록 이름으로 키에 있는 사전 `TabularDataset` 및 `FileDataset` 개체를 반환하는 Dataset.get_all(작업 공간)을 소개합니다.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + 에 대한 인수로 소개합니다. `parition_format` `Dataset.Tabular.from_parquet.files` `Dataset.Tabular.from_delimited_files` 각 데이터 경로의 파티션 정보는 지정된 형식에 따라 열로 추출됩니다. '{column_name}는 문자열 열을 만들고 '{column_name:yyyy/MM/dd/HH/mm/s}는 날짜 시간 열을 생성하며, 여기서 'yyyy', 'MM', 'dd', 'HH', 'mm' 및 'ss'는 날짜 유형의 연도, 월, 일, 시간, 분 및 두 번째를 추출하는 데 사용됩니다. partition_format 첫 번째 파티션 키의 위치에서 파일 경로가 끝날 때까지 시작해야 합니다. 예를 들어, 경로 '를 주어진. /USA/2019/01/01/data.csv' 파티션이 국가 및 시간별, partition_format='{국가}/{PartitionDate:yyyy/MM/dd}/data.csv'는 값 '미국'과 날짜 시간 열 'PartitionDate'를 값이 있는 문자열 열 '국가'를 만듭니다.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + 에 대한 인수로 소개합니다. `partition_format` `Dataset.Tabular.from_parquet.files` `Dataset.Tabular.from_delimited_files` 각 데이터 경로의 파티션 정보는 지정된 형식에 따라 열로 추출됩니다. '{column_name}는 문자열 열을 만들고 '{column_name:yyyy/MM/dd/HH/mm/s}는 날짜 시간 열을 생성하며, 여기서 'yyyy', 'MM', 'dd', 'HH', 'mm' 및 'ss'는 날짜 유형의 연도, 월, 일, 시간, 분 및 두 번째를 추출하는 데 사용됩니다. partition_format 첫 번째 파티션 키의 위치에서 파일 경로가 끝날 때까지 시작해야 합니다. 예를 들어, 경로 '를 주어진. /USA/2019/01/01/data.csv' 파티션이 국가 및 시간별, partition_format='{국가}/{PartitionDate:yyyy/MM/dd}/data.csv'는 값 '미국'과 날짜 시간 열 'PartitionDate'를 값이 있는 문자열 열 '국가'를 만듭니다.
    + `to_csv_files`메서드가 `to_parquet_files` 추가되었습니다. `TabularDataset` 이러한 방법을 사용하면 `TabularDataset` 데이터를 `FileDataset` 지정된 형식의 파일로 변환하여 a와 a 간에 변환할 수 있습니다.
    + Model.package()에서 생성된 Dockerfile파일을 저장할 때 기본 이미지 레지스트리에 자동으로 로그인합니다.
    + 'gpu_support'은 더 이상 필요하지 않습니다. 이제 AML이 사용 가능한 경우 nvidia 도커 확장을 자동으로 감지하고 사용합니다. 후속 릴리스에서 제거될 예정입니다.
    + 파이프라인 초안을 생성, 업데이트 및 사용하기 위한 지원이 추가되었습니다.
    + 이 릴리스는 자동화된 기계 학습 로컬 실행의 실행 성능을 향상시킵니다.
    + 사용자는 실행 기록에서 메트릭을 이름으로 쿼리할 수 있습니다.
    + 예측 작업에서 일반 대신 사용자 지정 예외를 사용하여 로깅을 개선했습니다.
  + **azureml-explain-model**
    + 새 MimicWrapper에 feature_maps 매개 변수를 추가하여 사용자가 원시 기능 설명을 얻을 수 있도록 했습니다.
    + 데이터 집합 업로드는 이제 설명 업로드를 위해 기본적으로 꺼져 있으며 upload_datasets=True로 다시 사용할 수 있습니다.
    + 설명 목록 및 다운로드 기능에 "is_law" 필터링 매개 변수를 추가했습니다.
    + 전역 `get_raw_explanation(feature_maps)` 및 로컬 설명 개체에 메서드를 추가합니다.
    + 아래 지원되는 버전이면 인쇄된 경고와 함께 lightgbm에 버전 확인 추가
    + 설명 일괄 처리 시 메모리 사용량 최적화
    + 이제 자동 ML 모델이 자동 ML예외를 반환합니다.
  + **azureml 파이프라인 코어**
    + 파이프라인Drafts를 생성, 업데이트 및 사용하기 위한 추가 지원 - 변경 가능한 파이프라인 정의를 유지하고 대화형으로 실행하기 위해 사용할 수 있습니다.
  + **azureml 기차 - 오토ml**
    + 원격 파이썬 런타임 환경에서 BERT / XLNet을 활성화하는 :::no-loc text="cuda"::: 데 필요한 GPU 지원 pytorch v1.1.0, 툴킷 9.0, 파이토치 변압기의 특정 버전을 설치하는 기능을 만들었습니다.
  + **azureml-train-core**
    + 일부 하이퍼 매개 변수 공간 정의 오류의 초기 실패는 서버 측 대신 sdk에서 직접.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure 기계 학습 데이터 준비 SDK v1.1.14
+ **버그 수정 및 개선 사항**
  + 원시 경로 및 자격 증명을 사용하여 ADLS/ADLSGen2에 쓰기를 사용하도록 설정했습니다.
  + 에 대해 `read_parquet`작동하지 `include_path=True` 않는 버그를 수정했습니다.
  + 예외 `to_pandas_dataframe()` "잘못된 속성 값: hostSecret"로 인한 오류를 수정했습니다.
  + Spark 모드에서 DBFS에서 파일을 읽을 수 없는 버그를 수정했습니다.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>파이썬 v1.0.57에 대한 Azure 기계 학습 SDK
+ **새로운 기능**
  + `TabularDataset` AutomatedML에서 사용할 수 있습니다. 에 대한 `TabularDataset`자세한 내용은 https://aka.ms/azureml/howto/createdatasets다음을 방문하십시오.

+ **버그 수정 및 개선 사항**
  + **자동 클라이언트 코어 네이티브 클라이언트**
    + 학습 및/또는 유효성 검사 레이블(y 및 y_valid)이 팬더 데이터 프레임의 형태로 제공되지만 numpy 배열로 제공되지 않을 때 발생하는 오류를 수정했습니다.
    + 데이터 및 개체만 필요로 하는 a를 `RawDataContext` 만들도록 인터페이스를 `AutoMLBaseSettings` 업데이트했습니다.
    +  AutoML 사용자가 예측할 때 충분히 길지 않은 교육 시리즈를 삭제할 수 있도록 허용합니다. - AutoML 사용자가 예측 할 때 학습 세트에 존재하지 않는 테스트 집합에서 그레인을 삭제할 수 있도록 허용합니다.
  + **azure-cli-ml**
    + 이제 Microsoft에서 생성된 인증서와 고객 인증서 모두에 대해 AKS 클러스터에 배포된 점수 매기기 끝점에 대한 TLS/SSL 인증서를 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + 레이블이 없는 행이 제대로 제거되지 않은 AutoML에서 문제가 해결되었습니다.
    + AutoML에서 향상된 오류 로깅; 이제 전체 오류 메시지가 항상 로그 파일에 기록됩니다.
    + 을 포함하도록 `azureml-defaults` `azureml-explain-model` `azureml-dataprep`패키지 고정을 업데이트했습니다. AutoML은 더 이상 패키지 불일치에 `azureml-train-automl` 대해 경고하지 않습니다(패키지 제외).
    + CV 분할의 `timeseries` 크기가 같지 않은 문제를 해결하여 Bin 계산에 실패하게 했습니다.
    + 교차 유효성 검사 학습 유형에 대한 앙상블 반복을 실행할 때 전체 데이터 집합에서 학습된 모델을 다운로드하는 데 문제가 발생하면 모델 가중치와 투표 앙상블에 공급되는 모델 간에 불일치가 발생했습니다.
    + 학습 및/또는 유효성 검사 레이블(y 및 y_valid)이 팬더 데이터 프레임의 형태로 제공되지만 numpy 배열로 제공되지 않을 때 발생하는 오류를 수정했습니다.
    + 입력 테이블의 부울 열에서 None이 발생했을 때 예측 작업문제를 수정했습니다.
    + AutoML 사용자가 예측할 때 충분히 길지 않은 교육 시리즈를 삭제할 수 있도록 허용합니다. - AutoML 사용자가 예측 할 때 학습 세트에 존재하지 않는 테스트 집합에서 그레인을 삭제할 수 있도록 허용합니다.
  + **azureml 코어**
    + blob_cache_timeout 매개 변수 순서에 문제가 해결되었습니다.
    + 외부 맞춤 및 예외 유형을 시스템 오류로 변환했습니다.
    + 원격 실행을 위한 키 볼트 비밀에 대한 지원이 추가되었습니다. azureml.corevault.Keyvault 클래스를 추가하여 작업 영역과 연결된 키볼트에서 비밀을 추가, 가져옵니다 및 나열합니다. 지원되는 작업은 다음과 같습니다.
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.keyvault.set_secret(이름, 값)
      + azureml.core.keyvault.keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.keyvault.get_secret(이름)
      + azureml.core.keyvault.keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.keyvault.list_secrets()
    + 기본 키 볼트를 가져오고 원격 실행 중에 비밀을 가져오는 추가 방법:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.run.get_secret(이름)
      + azureml.core.run.run.get_secrets(secrets_list)
    + 제출 하이퍼드라이브 CLI 명령에 추가 재정의 매개 변수를 추가했습니다.
    + API 호출의 안정성을 향상하면 공통 요청 라이브러리 예외로 재시도를 확장할 수 있습니다.
    + 제출된 실행에서 실행 을 제출하기 위한 지원을 추가합니다.
    + FileWatcher에서 만료된 SAS 토큰 문제를 수정하여 초기 토큰이 만료된 후 파일 이 업로드가 중지되었습니다.
    + 데이터 집합 파이썬 SDK에서 HTTP CSV / tsv 파일을 가져 오기지원.
    + 작업 영역을 더 이상 사용되지 않습니다.setup() 메서드입니다. 사용자에게 표시되는 경고 메시지는 create() 또는 get()/from_config())를 대신 사용하는 것이 좋습니다.
    + 추가 Environment.add_private_pip_wheel())을 추가하여 개인 사용자 `whl`지정 파이썬 패키지를 작업 영역에 업로드하고 이를 사용하여 환경을 구축/구체화할 수 있습니다.
    + 이제 Microsoft에서 생성된 인증서와 고객 인증서 모두에 대해 AKS 클러스터에 배포된 점수 매기기 끝점에 대한 TLS/SSL 인증서를 업데이트할 수 있습니다.
  + **azureml-explain-model**
    + 업로드시 설명에 모델 ID를 추가하기 위해 매개 변수를 추가했습니다.
    + 메모리 `is_raw` 및 업로드의 설명에 태그 추가.
    + azureml 설명 모델 패키지에 대한 pytorch 지원 및 테스트가 추가되었습니다.
  + **azureml-opendatasets**
    + 자동 테스트 환경 검색 및 로깅을 지원합니다.
    + 카운티와 우편으로 미국 인구를 얻기 위해 클래스를 추가했습니다.
  + **azureml 파이프라인 코어**
    + 입력 및 출력 포트 정의에 레이블 속성을 추가했습니다.
  + **azureml 원격 분석**
    + 잘못된 원격 분석 구성을 수정했습니다.
  + **azureml 기차 - 오토ml**
    + 설정 실패시 오류가 설치 실행에 대한 "오류" 필드에 기록되지 않아 부모 실행 "오류"에 저장되지 않은 버그를 수정했습니다.
    + 레이블이 없는 행이 제대로 제거되지 않은 AutoML에서 문제가 해결되었습니다.
    + AutoML 사용자가 예측할 때 충분히 길지 않은 교육 시리즈를 삭제할 수 있도록 허용합니다.
    + AutoML 사용자가 예측 할 때 학습 세트에 존재하지 않는 테스트 집합에서 그레인을 삭제할 수 있도록 허용합니다.
    + 이제 AutoMLStep은 `automl` 새 구성 매개 변수의 변경 또는 추가에 대한 문제를 피하기 위해 구성을 백 엔드로 전달합니다.
    + AutoML 데이터 가드레일은 이제 공개 미리 보기상태입니다. 사용자는 교육 후 데이터 가드레일 보고서(분류/회귀 작업)를 볼 수 있으며 SDK API를 통해 액세스할 수도 있습니다.
  + **azureml-train-core**
    + PyTorch 추정기에서 토치 1.2 지원이 추가되었습니다.
  + **azureml 위젯**
    + 분류 교육을 위한 혼동 매트릭스 차트가 개선되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure 기계 학습 데이터 준비 SDK v1.1.12
+ **새로운 기능**
  + 이제 문자열 목록을 `read_*` 메서드에 대한 입력으로 전달할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + 스파크에서 `read_parquet` 실행할 때 성능이 크게 향상되었습니다.
  + 모호한 날짜 `column_type_builder` 형식의 단일 열의 경우 실패한 문제를 수정했습니다.

### <a name="azure-portal"></a>Azure portal
+ **미리 보기 기능**
  + 이제 실행 세부 정보 페이지에 로그 및 출력 파일 스트리밍을 사용할 수 있습니다. 미리 보기 토글이 켜져 있을 때 파일이 실시간으로 업데이트를 스트리밍합니다.
  + 작업 영역 수준에서 할당량을 설정하는 기능은 미리 보기에서 릴리스됩니다. AmlCompute 할당량은 구독 수준에서 할당되지만 이제 작업 영역 간에 해당 할당량을 분산하고 공정한 공유 및 거버넌스를 위해 할당할 수 있습니다. 작업 영역의 왼쪽 탐색 모음에서 **사용법+할당량** 블레이드를 클릭하고 **할당량 구성** 탭을 선택하기만 하면 됩니다.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>파이썬 v1.0.55에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + 이제 AKS에 배포된 채점 끝점에 대한 호출에 대해 토큰 기반 인증이 지원됩니다. 현재 키 기반 인증을 계속 지원하며 사용자는 한 번에 이러한 인증 메커니즘 중 하나를 사용할 수 있습니다.
  + 가상 네트워크(VNet) 뒤에 있는 Blob 저장소를 데이터 스토어로 등록하는 기능입니다.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + CV 분할에 대한 유효성 검사 크기가 작고 회귀 및 예측에 대한 실제 차트와 불량 예측이 발생하는 버그를 수정합니다.
    + 원격 실행에서 예측 작업의 로깅이 개선되었으므로 실행이 실패한 경우 사용자에게 포괄적인 오류 메시지가 제공됩니다.
    + 프로세스 전처리 `Timeseries` 플래그가 True인 경우 의 수정된 실패입니다.
    + 일부 예측 데이터 유효성 검사 오류 메시지를 보다 실행 가능하게 만들었습니다.
    + 특히 프로세스 스폰 사이에 데이터 집합을 삭제 및/또는 지연 로드하여 AutoML 실행의 메모리 소비 감소
  + **azureml-콘티브 설명 모델**
    + 사용자가 모델 유형에 대한 기본 자동 추론 논리를 재정의할 수 있도록 설명자에 model_task 플래그를 추가했습니다.
    + 위젯 변경 사항: `contrib`자동으로 설치하지 `nbextension` 않고 더 이상 설치/활성화 - 전역 기능 중요도(예: Permutative)를 사용하여 설명 지원
    + 대시 보드 변경 : - 상자 플롯과 `beeswarm` 바이올린 플롯 요약 페이지에 플롯뿐만 `beeswarm` 아니라 - 'Top-k'슬라이더 변경에 플롯의 훨씬 빠른 재 렌더링 - 상단 k가 계산되는 방법을 설명하는 유용한 메시지 - 데이터가 제공되지 않을 때 차트 대신 유용한 사용자 정의 메시지
  + **azureml 코어**
    + 모델.package() 메서드를 추가하여 모델과 해당 종속성을 캡슐화하는 Docker 이미지와 Dockerfiles를 만듭니다.
    + 환경 개체를 포함하는 InferenceConfigs를 허용하도록 로컬 웹 서비스를 업데이트했습니다.
    + 모델.register() ''.' 때 잘못된 모델을 생성하는 수정 (현재 디렉터리)는 model_path 매개 변수로 전달됩니다.
    + Run.submit_child 추가하면 기능이 Experiment.submit를 미러링하고 실행을 제출된 자식 실행의 부모로 지정합니다.
    + Run.register_model Model.register의 구성 옵션을 지원합니다.
    + 기존 클러스터에서 JAR 작업을 실행할 수 있습니다.
    + 이제 instance_pool_id 및 cluster_log_dbfs_path 매개 변수를 지원합니다.
    + 웹 서비스에 모델을 배포할 때 환경 개체를 사용하기 위한 지원이 추가되었습니다. 이제 환경 개체를 InferenceConfig 개체의 일부로 제공할 수 있습니다.
    + 새로운 지역에 대한 appinsifht 매핑 추가 - 중앙 - westus - 북중부
    + 모든 Datastore 클래스의 모든 특성에 대한 설명서를 추가했습니다.
    + blob_cache_timeout 매개 `Datastore.register_azure_blob_container`변수를 추가했습니다.
    + azureml.core.environment.environment에 save_to_directory 및 load_from_directory 메서드를 추가했습니다.
    + CLI에 "az ml 환경 다운로드" 및 "az ml 환경 레지스터" 명령을 추가했습니다.
    + 추가 환경.add_private_pip_wheel 메서드입니다.
  + **azureml-explain-model**
    + 데이터 집합 서비스(미리 보기)를 사용하여 설명에 데이터 집합 추적을 추가했습니다.
    + 전역 설명을 스트리밍할 때 기본 일괄 처리 크기가 10k에서 100으로 감소했습니다.
    + 설명자에 model_task 플래그를 추가하여 사용자가 모델 유형에 대한 기본 자동 추론 논리를 재정의할 수 있도록 했습니다.
  + **azureml-mlflow**
    + 중첩된 디렉터리에서 무시되는 mlflow.azureml.build_image 버그를 수정했습니다.
  + **azureml-pipeline-steps**
    + 기존 Azure Databricks 클러스터에서 JAR 작업을 실행하는 기능이 추가되었습니다.
    + DatabricksStep 단계에 대한 지원 instance_pool_id 및 cluster_log_dbfs_path 매개 변수를 추가했습니다.
    + DatabricksStep 단계에서 파이프라인 매개 변수에 대한 지원이 추가되었습니다.
  + **azureml 기차 - 오토ml**
    + 앙상블 `docstrings` 관련 파일에 추가되었습니다.
    + 문서를 보다 적절한 언어로 `max_cores_per_iteration` 업데이트하고`max_concurrent_iterations`
    + 원격 실행에서 예측 작업의 로깅이 개선되었으므로 실행이 실패한 경우 사용자에게 포괄적인 오류 메시지가 제공됩니다.
    + 파이프라인 `automlstep` 노트북에서 get_data 제거되었습니다.
    + 에서 `dataprep` 지원을 `automlstep`시작했습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure 기계 학습 데이터 준비 SDK v1.1.10

+ **새로운 기능**
  + 이제 특정 열에 대해 특정 검사기(예: 히스토그램, 분산형 플롯 등)를 실행하도록 요청할 수 있습니다.
  + 에 병렬 화 `append_columns`인수를 추가했습니다. True이면 데이터가 메모리에 로드되지만 실행은 병렬로 실행됩니다. False이면 실행이 스트리밍되지만 단일 스레드가 됩니다.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>파이썬 v1.0.53에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + 자동화된 기계 학습은 이제 원격 컴퓨팅 대상에 대한 ONNX 모델 교육을 지원합니다.
  + 이제 Azure 기계 학습은 이전 실행, 검사점 또는 모델 파일에서 교육을 다시 시작할 수 있는 기능을 제공합니다.
    + [추정기를 사용하여 이전 실행에서 교육을 다시 시작하는](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) 방법 알아보기

+ **버그 수정 및 개선 사항**
  + **자동 클라이언트 코어 네이티브 클라이언트**
    + 변환 후 열 유형(버그 연결)에 대한 버그 를 수정합니다.
    + y_query 시작(#459519)에 None(들)을 포함하는 개체 유형이 되도록 허용합니다.
  + **azure-cli-ml**
    + CLI 명령은 "모델 배포" 및 "서비스 업데이트"를 통해 매개 변수, 구성 파일 또는 이 둘의 조합을 허용합니다. 매개 변수는 파일의 특성보다 우선합니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + NimbusML 종속성을 1.2.0 버전(최신 버전)으로 업데이트합니다.
    + NimbusML 추정기에 대한 지원을 추가하여 AutoML 추정기 내에서 사용할 파이프라인을 &.
    + 앙상블 선택 절차에서 버그를 수정하면 점수가 일정하게 유지되더라도 결과 앙상블이 불필요하게 증가했습니다.
    + 작업을 예측하기 위해 CV 분할에서 일부 업적을 다시 사용할 수 있습니다. 이렇게 하면 지연 및 롤링 창과 같은 값비싼 위업에 대해 n_cross_validations 정도의 비율로 설치 실행 의 런타임 속도를 높일 수 있습니다.
    + 팬더 지원 시간 범위를 벗어난 경우 문제를 해결합니다. 이제 시간이 pd보다 적으면 DataException을 올립니다. 타임스탬프.min 또는 PD보다 큽 타임스탬프.최대
    + 이제 예측을 통해 정렬할 수 있는 경우 기차 및 테스트 세트에서 서로 다른 주파수를 사용할 수 있습니다. 예를 들어 '1월부터 분기별'과 '10월에 시작되는 분기별'을 정렬할 수 있습니다.
    + 속성 "매개 변수"가 TimeSeriesTransformer에 추가되었습니다.
    + 이전 예외 클래스를 제거합니다.
    + 예측 작업에서 매개 `target_lags` 변수는 이제 단일 정수 값 또는 정수 목록을 허용합니다. 정수가 제공된 경우 하나의 지연만 생성됩니다. 목록이 제공되면 지연의 고유 값이 수행됩니다. target_lags =[1, 2, 2, 4]는 1, 2 및 4 마침표의 지연을 만듭니다.
    + 변환 후 열 유형 손실에 대한 버그 수정(버그 연결).
    + 에서 `model.forecast(X, y_query)`y_query 시작(#459519)에 None(들)을 포함하는 개체 유형이 되도록 허용합니다.
    + 출력에 `automl` 예상 값 추가
  + **azureml-콘티브 데이터 드리프트**
    +  데이터를 보강할 때 azureml-contrib-opendataset 대신 azureml-opendataset으로 전환 및 성능 향상을 포함 하 여 예제 노트북 향상
  + **azureml-콘티브 설명 모델**
    + azureml-contrib-설명-모델 패키지에서 원시 기능 중요도에 대 한 LIME 설명자에 대 한 수정 된 변환 인수
    + AzureML-contrib-설명-모델 패키지에 대 한 이미지 설명에서 이미지 설명에 세그먼트를 추가 했습니다.
    + 라임 해설자에 대한 scipy 스파스 지원 추가
    + DecisionTree설명 가능한 `include_local=False`Model의 실행 시간을 개선하기 위해 일괄 처리로 전역 설명을 스트리밍할 때 설명자 모방에 추가되었습니다. `batch_size`
  + **azureml-contrib-특징 공학**
    + set_featurizer_timeseries_params() : dict 값 유형 변경 및 null `timeseries` 검사 호출 수정 - featurizer용 노트북 추가
    + NimbusML 종속성을 1.2.0 버전(최신 버전)으로 업데이트합니다.
  + **azureml 코어**
    + AzureML CLI에 DBFS 데이터 스토어를 연결하는 기능이 추가되었습니다.
    + 데이터스토어 업로드로 빈 폴더가 만들어지는 `target_path` 버그를 수정했습니다.`/`
    + 서비스 `deepcopy` 보안 원칙 인증에서 문제가 해결되었습니다.
    + CLI에 "az ml 환경 표시" 및 "az ml 환경 목록" 명령을 추가했습니다.
    + 이제 환경은 이미 빌드된 base_image 대한 대안으로 base_dockerfile 지정하는 것을 지원합니다.
    + 사용되지 않는 RunConfiguration 설정 auto_prepare_environment 더 이상 사용되지 않는 것으로 표시되었습니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있습니다.
    + Bugfix: 모델 및 이미지 삭제는 이제 업스트림 종속성으로 인해 삭제가 실패할 경우 종속된 업스트림 개체를 검색하는 방법에 대한 자세한 정보를 제공합니다.
    + 일부 환경에 대한 작업 영역을 만들 때 발생하는 배포에 대해 빈 기간을 인쇄하는 버그를 수정했습니다.
    + 작업 영역이 개선되면 실패 예외가 생성됩니다. 사용자에게 "작업 영역을 만들 수 없습니다. 찾을 수 없습니다..." 대신 실제 생성 실패를 볼 수 있습니다.
    + AKS 웹 서비스에서 토큰 인증에 대한 지원을 추가합니다.
    + 개체에 `Webservice` 메서드를 추가합니다. `get_token()`
    + 기계 학습 데이터 집합을 관리하기 위한 CLI 지원이 추가되었습니다.
    + `Datastore.register_azure_blob_container`이제 선택적으로 `blob_cache_timeout` blobfuse의 마운트 매개 변수를 구성하여 이 데이터스토어에 대한 캐시 만료를 사용하도록 구성하는 값(초)이 선택적으로 사용됩니다. 기본값은 시간 설정이 없으며, 즉 Blob을 읽을 때 작업이 완료될 때까지 로컬 캐시에 유지됩니다. 대부분의 작업은 이 설정을 선호하지만 일부 작업은 노드에 맞는 것보다 큰 데이터 집합에서 더 많은 데이터를 읽어야 합니다. 이러한 작업의 경우 이 매개 변수를 조정하면 성공하는 데 도움이 됩니다. 이 매개 변수를 튜닝할 때 주의: 값을 너무 낮게 설정하면 다시 사용되기 전에 시대에 사용된 데이터가 만료될 수 있기 때문에 성능이 저하될 수 있습니다. 즉, 모든 읽기는 교육 시간에 부정적인 영향을 주는 로컬 캐시가 아닌 Blob 저장소(즉, 네트워크)에서 수행됩니다.
    + 이제 등록 후 모델 설명을 올바르게 업데이트할 수 있습니다.
    + 이제 모델 및 이미지 삭제가 삭제실패의 원인이 되는 업스트림 객체에 대한 자세한 정보를 제공합니다.
    + azureml.mlflow를 사용하여 원격 실행의 리소스 활용도를 개선합니다.
  + **azureml-explain-model**
    + azureml-contrib-설명-모델 패키지에서 원시 기능 중요도에 대 한 LIME 설명자에 대 한 수정 된 변환 인수
    + 라임 해설자에 대한 scipy 스파스 지원 추가
    + 선형 모델을 설명하기 위한 테이블 형식 설명에 대한 또 다른 수준뿐만 아니라 셰이프 선형 설명 래퍼를 추가했습니다.
    + 설명 모델 라이브러리의 모방 설명자, 희소 데이터 입력에 대해 include_local=False일 때 수정된 오류
    + 출력에 `automl` 예상 값 추가
    + 원시 피쳐 중요도를 얻기 위해 변환 인수가 제공될 때 고정된 순열 피쳐 중요도
    + DecisionTree설명 가능한 `include_local=False`모델의 실행 시간을 개선하기 위해 일괄 처리로 전역 설명을 스트리밍할 때 설명자 모방에 추가됨 `batch_size`
    + 모델 설명 가능성 라이브러리의 경우 예측에 팬더 데이터 프레임 입력이 필요한 고정 블랙박스 설명자
    + 플로트에 `explanation.expected_values` float가 있는 목록이 아닌 플로트에 반환되는 버그를 수정했습니다.
  + **azureml-mlflow**
    + mlflow.set_experiment(experiment_name)의 성능 향상
    + mlflow tracking_uri 대한 대화형 로그인 인증 사용 버그 수정
    + azureml.mlflow를 사용하여 원격 실행의 리소스 활용도를 개선합니다.
    + azureml-mlflow 패키지의 문서 개선
    + mlflow.log_artifacts("my_dir")이 "> <아티팩트 경로" 대신 "my_dir/<아티팩트 경로>"에서 아티팩트를 저장하는 패치 버그
  + **azureml-opendatasets**
    + 메모리 문제로 인해 이전 버전 (<0.14.0)에 핀이 `pyarrow` 새로 도입되었습니다. `opendatasets`
    + azureml-콘티브-오픈 데이터 집합을 azureml-오픈 데이터 집합으로 이동합니다.
    + 개방형 데이터 집합 클래스를 Azure 기계 학습 작업 영역에 등록하고 AML 데이터 집합 기능을 원활하게 활용할 수 있도록 허용합니다.
    + NoaaisdWeather 비 SPARK 버전에서 성능을 크게 향상.
  + **azureml-pipeline-steps**
    + 이제 데이터브릭스스스스텝의 입력 및 출력에 대해 DBFS 데이터스토어가 지원됩니다.
    + 입력/출력과 관련하여 Azure 일괄 처리 단계에 대한 설명서가 업데이트되었습니다.
    + AzureBatchStep에서 기본값을 *true로* *delete_batch_job_after_finish.*
  + **azureml 원격 분석**
    +  azureml-콘티브-오픈 데이터 집합을 azureml-오픈 데이터 집합으로 이동합니다.
    + 개방형 데이터 집합 클래스를 Azure 기계 학습 작업 영역에 등록하고 AML 데이터 집합 기능을 원활하게 활용할 수 있도록 허용합니다.
    + NoaaisdWeather 비 SPARK 버전에서 성능을 크게 향상.
  + **azureml 기차 - 오토ml**
    + 실제 반환 유형을 반영하고 키 속성 검색에 대한 추가 메모를 제공하기 위해 get_output 대한 설명서를 업데이트했습니다.
    + NimbusML 종속성을 1.2.0 버전(최신 버전)으로 업데이트합니다.
    + 출력에 `automl` 예상 값 추가
  + **azureml-train-core**
    + 이제 문자열이 자동 하이퍼매개 변수 튜닝의 계산 대상으로 허용됩니다.
    + 사용되지 않는 RunConfiguration 설정 auto_prepare_environment 더 이상 사용되지 않는 것으로 표시되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure 기계 학습 데이터 준비 SDK v1.1.9

+ **새로운 기능**
  + http 또는 https URL에서 직접 파일을 읽기 위한 지원이 추가되었습니다.

+ **버그 수정 및 개선 사항**
  + 원격 소스에서 마루 데이터 집합을 읽으려고 할 때 오류 메시지가 개선되었습니다(현재 지원되지 않음).
  + ADLS Gen 2의 Parquet 파일 형식으로 작성하고 경로에서 ADLS Gen 2 컨테이너 이름을 업데이트할 때 버그를 수정했습니다.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>시각적 인터페이스
+ **기능 미리 보기**
  + 시각적 인터페이스에 "R 스크립트 실행" 모듈이 추가되었습니다.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>파이썬 v1.0.48에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + **azureml-opendatasets**
    + **azureml-contrib-오픈 데이터 집합은** 이제 **azureml-오픈 데이터 집합으로**사용할 수 있습니다. 이전 패키지는 계속 작동할 수 있지만 보다 풍부한 기능과 향상된 기능을 위해 **azureml-opendataset을** 사용하는 것이 좋습니다.
    + 이 새로운 패키지를 사용하면 열려 있는 데이터 집합을 Azure 기계 학습 작업 영역에서 데이터 집합으로 등록하고 Dataset에서 제공하는 모든 기능을 활용할 수 있습니다.
    + 또한 팬더/SPARK 데이터 프레임으로 열린 데이터 집합을 사용 하 고 날씨와 같은 일부 데이터 집합에 대 한 위치 조인 같은 기존 기능 도 포함 됩니다.

+ **기능 미리 보기**
    + HyperDriveConfig는 이제 파이프라인을 사용하여 하이퍼매개 변수 튜닝을 지원하는 매개 변수로 파이프라인 개체를 허용할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + **azureml 기차 - 오토ml**
    + 변환 후 열 유형 손실에 대한 버그를 수정했습니다.
    + y_query 처음에 None(none)을 포함하는 개체 유형이 되도록 버그를 수정했습니다.
    + 점수가 일정하게 유지되더라도 결과 앙상블을 불필요하게 성장시키는 앙상블 선택 절차에서 문제를 수정했습니다.
    + AutoMLStep에서 whitelist_models 및 blacklist_models 설정으로 문제가 해결되었습니다.
    + Azure ML 파이프라인의 컨텍스트에서 AutoML이 사용되었을 때 전처리 사용을 방해하는 문제를 수정했습니다.
  + **azureml-opendatasets**
    + azureml-콘티브-오픈 데이터 집합을 azureml-오픈 데이터 집합으로 이동했습니다.
    + 개방형 데이터 집합 클래스를 Azure 기계 학습 작업 영역에 등록하고 AML 데이터 집합 기능을 원활하게 활용할 수 있습니다.
    + 개선 된 NoaaisdWeather 크게 비 SPARK 버전에서 성능을 풍부하게.
  + **azureml-explain-model**
    + 해석 가능 개체에 대한 온라인 설명서가 업데이트되었습니다.
    + 전역 `batch_size` 설명을 일괄 `include_local=False`처리로 스트리밍하여 모델 설명 가능성 라이브러리에 대한 DecisionTreeExplainableModel의 실행 시간을 개선할 때 설명기를 모방하는 데 추가되었습니다.
    + float가 있는 `explanation.expected_values` 목록이 아닌 플로트에 가끔 반환되는 문제를 수정했습니다.
    + 설명 모델 `automl` 라이브러리에서 모방 설명에 대해 출력에 예상 값을 추가했습니다.
    + 원시 피쳐 중요성을 얻기 위해 변환 인수가 제공될 때 순열 피쳐 중요성을 수정했습니다.
  + **azureml 코어**
    + AzureML CLI에 DBFS 데이터 저장소를 연결하는 기능이 추가되었습니다.
    + `/`을 `target_path` 시작하면 빈 폴더가 만들어지는 데이터스토어 업로드 문제가 해결되었습니다.
    + 두 데이터 집합의 비교를 활성화했습니다.
    + 이제 모델 및 이미지 삭제는 업스트림 종속성으로 인해 삭제가 실패할 경우 종속된 업스트림 개체검색에 대한 자세한 정보를 제공합니다.
    + auto_prepare_environment 사용되지 않는 RunConfiguration 설정을 더 이상 사용하지 않는 것으로 설정했습니다.
  + **azureml-mlflow**
    + azureml.mlflow를 사용하는 원격 실행의 리소스 활용도가 향상되었습니다.
    + azureml-mlflow 패키지의 설명서를 개선했습니다.
    + mlflow.log_artifacts("my_dir")이 "아티팩트 경로"가 아닌 "my_dir/아티팩트 경로" 아래에 아티팩트를 저장하는 문제를 수정했습니다.
  + **azureml 파이프라인 코어**
    + 모든 파이프라인 단계에 대한 매개 변수 hash_paths 더 이상 사용되지 않으며 나중에 제거됩니다. 기본적으로 source_directory 내용은 해시됩니다(.amlignore 또는 .gitignore에 나열된 파일 제외).
    + 모듈 및 ModuleStep을 지속적으로 개선하여 컴퓨팅 유형 별 모듈을 지원하고, RunConfiguration 통합 및 기타 변경 사항을 준비하여 파이프라인에서 계산 유형 별 모듈 사용의 잠금을 해제합니다.
  + **azureml-pipeline-steps**
    + AzureBatchStep: 입력/출력과 관련하여 향상된 설명서를 제공합니다.
    + AzureBatchStep: 기본값을 true로 delete_batch_job_after_finish 변경했습니다.
  + **azureml-train-core**
    + 이제 문자열이 자동 하이퍼매개 변수 튜닝의 계산 대상으로 허용됩니다.
    + auto_prepare_environment 사용되지 않는 RunConfiguration 설정을 더 이상 사용하지 않는 것으로 설정했습니다.
    + 더 이상 사용되지 `conda_dependencies_file_path` 않으며 `pip_requirements_file_path` 각각찬성합니다. `conda_dependencies_file` `pip_requirements_file`
  + **azureml-opendatasets**
    + NoaaisdWeather 비 SPARK 버전에서 성능을 크게 향상.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure 기계 학습 데이터 준비 SDK v1.1.8

+ **새로운 기능**
 + 이제 데이터 흐름 개체를 반복하여 레코드 시퀀스를 생성할 수 있습니다. 에 대한 `Dataflow.to_record_iterator`설명서를 참조하십시오.
  + 이제 데이터 흐름 개체를 반복하여 레코드 시퀀스를 생성할 수 있습니다. 에 대한 `Dataflow.to_record_iterator`설명서를 참조하십시오.

+ **버그 수정 및 개선 사항**
 + 데이터프렙 SDK의 견고성을 높였습니다.
 + 문자열이 아닌 열 인덱스를 사용하여 팬더 DataFrames의 처리가 개선되었습니다.
 + 데이터 집합의 `to_pandas_dataframe` 성능을 향상했습니다.
 + 다중 노드 환경에서 실행할 때 데이터 집합의 Spark 실행이 실패하는 버그를 수정했습니다.
  + 데이터프렙 SDK의 견고성을 높였습니다.
  + 문자열이 아닌 열 인덱스를 사용하여 팬더 DataFrames의 처리가 개선되었습니다.
  + 데이터 집합의 `to_pandas_dataframe` 성능을 향상했습니다.
  + 다중 노드 환경에서 실행할 때 데이터 집합의 Spark 실행이 실패하는 버그를 수정했습니다.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure 기계 학습 데이터 준비 SDK v1.1.7

Azure Databricks를 사용하는 일부 고객에게 문제가 발생했기 때문에 성능이 향상된 변경 사항이 되돌아갔습니다. Azure Databricks에 문제가 발생한 경우 아래 방법 중 하나를 사용하여 버전 1.1.7로 업그레이드할 수 있습니다.
1. 이 스크립트를 실행하여 업그레이드합니다.`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 최신 데이터 준비 SDK 버전을 설치할 클러스터를 다시 만듭니다.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>파이썬 v1.0.45에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + azureml 설명 모델 패키지에서 설명기를 모방하는 의사 결정 트리 서로게이트 모델 추가
  + 추론 이미지에 설치할 CUDA 버전을 지정하는 기능입니다. CUDA 9.0, 9.1 및 10.0에 대한 지원.
  + Azure ML 교육 기본 이미지에 대한 정보는 [Azure ML 컨테이너 GitHub 리포지토리](https://github.com/Azure/AzureML-Containers) 및 [DockerHub에서](https://hub.docker.com/_/microsoft-azureml) 사용할 수 있습니다.
  + 파이프라인 일정에 대한 CLI 지원이 추가되었습니다. "az ml 파이프라인 -h"를 실행하여 자세히 알아보기
  + AKS 웹 서비스 배포 구성 및 CLI에 사용자 지정 Kubernetes 네임스페이스 매개 변수를 추가했습니다.
  + 모든 파이프라인 단계에 대해 더 이상 사용되지 hash_paths 매개 변수
  + Model.register는 이제 `child_paths` 매개 변수를 사용하여 여러 개별 파일을 단일 모델로 등록하는 것을 지원합니다.

+ **기능 미리 보기**
    + 점수 설명자는 이제 보다 안정적인 직렬화 및 직렬화를 위해 conda 및 pip 정보를 선택적으로 저장할 수 있습니다.
    + 자동 기능 선택기에 대한 버그 수정.
    + mlflow.azureml.build_image 새 api에 업데이트되었습니다.

+ **버그 수정 및 개선 사항**
  + azureml `paramiko` 코어에서 종속성을 제거했습니다. 레거시 계산 대상 연결 방법에 대한 사용 중단 경고가 추가되었습니다.
  + run.create_children 성능 향상
  + 이진 분류기를 사용한 모방 설명자에서는 셰이프 값 의 크기 조정에 교사 확률이 사용될 때 확률 순서를 수정합니다.
  + 자동화된 기계 학습을 위한 향상된 오류 처리 및 메시지.
  + 자동화된 기계 학습에 대한 반복 시간 시간 문제를 수정했습니다.
  + 자동화된 기계 학습을 위한 타임시리즈 변환 성능을 개선했습니다.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure 기계 학습 데이터 준비 SDK v1.1.6

+ **새로운 기능**
  + 상단 값 ()`SummaryFunction.TOPVALUES`및 아래쪽 값`SummaryFunction.BOTTOMVALUES`()에 대한 요약 함수를 추가했습니다.

+ **버그 수정 및 개선 사항**
  + 의 `read_pandas_dataframe`성능을 크게 향상시고 있습니다.
  + 이진 파일을 가리키는 `get_profile()` Dataflow에서 실패하는 버그를 수정했습니다.
  + 원격 `set_diagnostics_collection()` 분석 컬렉션의 프로그래밍 방식 사용/비활성화를 허용하기 위해 노출되었습니다.
  + 의 `get_profile()`동작을 변경했습니다. 이제 팬더의 동작과 일치하는 최소, 평균, Std 및 Sum에 대해 NaN 값이 무시됩니다.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>파이썬 v1.0.43에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + 이제 Azure 기계 학습은 인기 있는 기계 학습 및 데이터 분석 프레임워크 Scikit-learn에 대한 일류 지원을 제공합니다. 추정기사용으로 사용자는 Scikit 학습 모델을 쉽게 학습하고 배포할 수 있습니다. [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + [하이퍼드라이브를 사용하여 Scikit-learn을 사용하여 하이퍼매개 변수 튜닝을 실행하는](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)방법에 대해 알아봅니다.
  + 모듈 및 ModuleVersion 클래스와 함께 파이프라인에서 ModuleStep을 생성하여 재사용 가능한 컴퓨팅 단위를 관리할 수 있도록 지원이 추가되었습니다.
  + 이제 ACI 웹 서비스는 업데이트를 통해 지속적인 scoring_uri 지원합니다. scoring_uri IP에서 FQDN으로 변경됩니다. FQDN에 대한 Dns 이름 레이블은 deploy_configuration dns_name_label 설정하여 구성할 수 있습니다.
  + 자동화된 기계 학습 새로운 기능:
    + 예측을 위한 STL 위화기
    + 기능 스위핑을 위해 KMeans 클러스터링이 활성화되었습니다.
  + AmlCompute 할당량 승인이 더 빨라졌습니다! 이제 임계값 내에서 할당량 요청을 승인하는 프로세스를 자동화했습니다. 할당량 작동 방식에 대한 자세한 내용은 [할당량을 관리하는 방법을](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)알아봅니다.

+ **기능 미리 보기**
    + Azureml-mlflow 패키지[(예 : 노트북)를](https://aka.ms/azureml-mlflow-examples)통해 [MLflow](https://mlflow.org) 1.0.0 추적과 통합.
    + 실행으로 주피터 노트북을 제출합니다. [API 참조 문서](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + azureml-contrib-datadrift 패키지[(예 노트북)를](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/monitor-models/data-drift)통해 [데이터 드리프트 검출기의](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) 공개 미리보기. 데이터 드리프트는 시간이 지남에 따라 모델 정확도가 저하되는 가장 큰 이유 중 하나입니다. 프로덕션 환경에서 모델에 제공된 데이터가 모델이 학습된 데이터와 다른 경우에 발생합니다. AML 데이터 드리프트 검출기는 고객이 데이터 드리프트를 모니터링하고 드리프트가 감지될 때마다 경고를 전송하는 데 도움이 됩니다.

+ **주요 변경 내용**

+ **버그 수정 및 개선 사항**
  + RunConfiguration 로드 및 저장 지원 은 이전 동작에 대한 전체 백 컴파트가있는 전체 파일 경로를 지정합니다.
  + ServicePrincipalauthentication에서 캐싱을 추가했으며 기본적으로 꺼졌습니다.
  + 동일한 메트릭 이름으로 여러 플롯의 로깅을 활성화합니다.
  + 이제 azureml.core ()에서`from azureml.core import Model`클래스를 올바르게 가져올 수 있습니다.
  + 파이프라인 단계에서 `hash_path` 매개 변수는 더 이상 사용되지 않습니다. 새로운 동작은 .amlignore 또는 .gitignore에 나열된 파일을 제외하고 source_directory 완료하는 것입니다.
  + 파이프라인 패키지에서 다양한 `get_all` `get_all_*` 메서드와 메서드가 각각 더 `list` 이상 `list_*`사용되지 않습니다.
  + azureml.core.get_run 원래 실행 형식을 반환하기 전에 더 이상 클래스를 가져올 필요가 없습니다.
  + WebService Update에 대한 일부 호출이 업데이트를 트리거하지 않는 문제를 수정했습니다.
  + AKS 웹 서비스의 시간 시간 시간은 5ms에서 300000ms 사이여야 합니다. 최대 허용 scoring_timeout_ms 득점 요청에 대한 1 분에서 5 분까지 충돌되었습니다.
  + 이제 LocalWebservice `scoring_uri` 개체와 `swagger_uri` 속성이 있습니다.
  + 사용자 프로세스에서 출력 디렉터리 생성 및 출력 디렉터리 업로드를 이동했습니다. 모든 사용자 프로세스에서 실행 기록 SDK를 사용하도록 설정했습니다. 이렇게 하면 분산 교육 실행에서 발생하는 일부 동기화 문제가 해결됩니다.
  + 이제 사용자 프로세스 이름에서 작성된 azureml 로그의 이름에는 프로세스 이름(분산 교육의 경우)과 PID가 포함됩니다.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure 기계 학습 데이터 준비 SDK v1.1.5

+ **버그 수정 및 개선 사항**
  + 2자리 연도 형식의 해석된 datetime 값의 경우 Windows 5월 릴리스와 일치하도록 유효한 연도 범위가 업데이트되었습니다. 범위가 1930-2029에서 1950-2049로 변경되었습니다.
  + 파일 및 설정에서 `handleQuotedLineBreaks=True`읽을 `\r` 때 새 줄로 처리됩니다.
  + 경우에 따라 오류가 `read_pandas_dataframe` 발생하는 버그를 수정했습니다.
  + 의 `get_profile`성능이 향상되었습니다.
  + 향상된 오류 메시지입니다.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure 기계 학습 데이터 준비 SDK v1.1.4

+ **새로운 기능**
  + 이제 다음 식 언어 함수를 사용하여 datetime 값을 추출하고 새 열로 구문 분석할 수 있습니다.
    + `RegEx.extract_record()`날짜 시간 요소를 새 열로 추출합니다.
    + `create_datetime()`별도의 날짜 시간 요소에서 날짜 시간 개체를 만듭니다.
  + 호출 `get_profile()`할 때, 이제 분량체 열이 (est.)로 레이블이 지정되어 값이 근사치임을 명확하게 나타낼 수 있습니다.
  + 이제 Azure Blob 저장소에서 읽을 때 ** 흔들림을 사용할 수 있습니다.
    + 예: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **버그 수정**
  + 원격 소스(Azure Blob)에서 마루 파일을 읽는 데 관련된 버그를 수정했습니다.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>파이썬 v1.0.39에 대한 Azure 기계 학습 SDK
+ **변경**
  + 실행 구성 auto_prepare_environment 옵션이 더 이상 사용되지 않고 자동 준비가 기본값이 됩니다.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure 기계 학습 데이터 준비 SDK v1.1.3

+ **새로운 기능**
  + postgresSQL 데이터베이스에서 read_postgresql 호출하거나 데이터 스토어를 사용하여 읽을 수 있는 지원이 추가되었습니다.
    + 방법 가이드의 예제를 참조하십시오.
      + [데이터 수집 노트북](https://aka.ms/aml-data-prep-ingestion-nb)
      + [데이터스토어 노트북](https://aka.ms/aml-data-prep-datastore-nb)

+ **버그 수정 및 개선 사항**
  + 열 유형 변환 문제를 수정했습니다.
  + 이제 부울 또는 숫자 열을 부울 열로 올바르게 변환합니다.
  + 이제 날짜 열을 날짜 유형으로 설정하려고 할 때 실패하지 않습니다.
  + 향상된 조인 유형 유형 및 함께 제공되는 참조 설명서. 두 데이터 흐름을 조인할 때 다음 유형 중 하나를 지정할 수 있습니다.
    + 없음, 경기, 내부, 타의 추종을 불허, 왼쪽, 왼쪽, 타의 추종을 불허하는, 오른쪽 안티, 오른쪽 외부, 풀 안티, 전체.
  + 더 많은 날짜 형식을 인식하도록 데이터 형식이 추론되었습니다.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 다음을 수행할 수 있습니다.
+ 자동화된 ML 실험 생성 및 실행
+ 노트북 VM을 만들어 샘플 Jupyter 노트북 또는 직접 사용해 보십시오.
+ 자동화된 기계 학습, 시각적 인터페이스 및 호스팅된 노트북 VM을 포함하는 Azure 기계 학습 작업 영역의 새로운 작성 섹션(미리 보기)
    + 자동 기계 학습을 사용하여 모델 자동 생성
    + 드래그 앤 드롭 비주얼 인터페이스를 사용하여 실험을 실행
    + 노트북 VM을 만들어 데이터를 탐색하고 모델을 만들고 서비스를 배포합니다.
+ 실행 보고서에서 실시간 차트 및 메트릭 업데이트 및 세부정보 페이지 실행
+ 실행 세부 정보 페이지에서 로그, 출력 및 스냅숏에 대한 파일 뷰어를 업데이트했습니다.
+ 실험 탭에서 새롭고 개선된 보고서 만들기 환경을 제공합니다.
+ Azure 기계 학습 작업 영역의 개요 페이지에서 config.json 파일을 다운로드하는 기능이 추가되었습니다.
+ Azure Databricks 작업 영역에서 Azure 기계 학습 작업 영역 만들기를 지원합니다.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>파이썬 v1.0.33에 대한 Azure 기계 학습 SDK
+ **새로운 기능**
  + _Workspace.create_ 메서드는 이제 CPU 및 GPU 클러스터에 대한 기본 클러스터 구성을 허용합니다.
  + 작업 영역 만들기에 실패하면 종속 리소스가 정리됩니다.
  + 기본 Azure 컨테이너 레지스트리 SKU가 기본으로 전환되었습니다.
  + Azure 컨테이너 레지스트리는 실행 또는 이미지 생성에 필요한 경우 느리게 만들어집니다.
  + 교육 실행을 위한 환경 지원.

### <a name="notebook-virtual-machine"></a>노트북 가상 머신 

기계 학습 실험을 프로그래밍하고, 모델을 웹 엔드포인트로 배포하고, Python을 사용하여 Azure 기계 학습 SDK에서 지원하는 다른 모든 작업을 수행할 수 있는 Jupyter 노트북을 위한 안전한 엔터프라이즈 지원 호스팅 환경으로 Notebook VM을 사용합니다.그것은 몇 가지 기능을 제공합니다 :
+ [Quickly spin up a preconfigured notebook VM](tutorial-1st-experiment-sdk-setup.md) Azure 기계 학습 SDK 및 관련 패키지의 최신 버전이 있는 미리 구성된 노트북 VM을 빠르게 스핀업합니다.
+ 액세스는 HTTPS, Azure Active Directory 인증 및 권한 부여와 같은 입증된 기술을 통해 보호됩니다.
+ Azure 기계 학습 작업 영역 Blob 저장소 계정에서 노트북 및 코드를 신뢰할 수 있는 클라우드 저장소입니다.작업을 잃지 않고 노트북 VM을 안전하게 삭제할 수 있습니다.
+ Azure 기계 학습 기능을 탐색하고 실험할 수 있는 사전 설치된 샘플 전자 필기장입니다.
+ Azure VM, 모든 VM 유형, 모든 패키지, 모든 드라이버의 전체 사용자 지정 기능입니다. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>파이썬 v1.0.33에 대한 Azure 기계 학습 SDK 출시.

+ [FPGA에서](how-to-deploy-fpga-web-service.md) Azure ML 하드웨어 가속 모델을 일반적으로 사용할 수 있습니다.
  + 이제 [azureml-accel-model 패키지를 사용하여](how-to-deploy-fpga-web-service.md) 다음을 수행할 수 있습니다.
    + 지원되는 심층 신경망(ResNet 50, ResNet 152, DenseNet-121, VGG-16 및 SSD-VGG)의 가중치 를 학습합니다.
    + 지원되는 DNN에서 전송 학습 사용
    + 모델 관리 서비스에 모델을 등록하고 모델을 컨테이너화합니다.
    + AZURE Kubernetes 서비스(AKS) 클러스터에서 FPGA를 사용하여 Azure VM에 모델을 배포합니다.
  + [Azure 데이터 상자 가장자리](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) 서버 장치에 컨테이너 배포
  + 이 [샘플로](https://github.com/Azure-Samples/aml-hardware-accelerated-models) gRPC 끝점으로 데이터 점수 매기기

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

+ 성능 최적화를 위해 :::no-loc text="featurizers"::: 동적으로 추가할 수 있는 기능 스위핑입니다. 신규 :::no-loc text="featurizers":::: 작업 포함, 증거의 무게, 대상 인코딩, 텍스트 대상 인코딩, 클러스터 거리
+ 자동화 된 ML 내부의 기차 / 유효한 분할을 처리하는 스마트 이력서
+ 메모리 최적화 변경 및 런타임 성능 향상
+ 모델 설명의 성능 향상
+ 로컬 실행을 위한 ONNX 모델 변환
+ 추가된 하위 샘플링 지원
+ 종료 기준이 정의되지 않은 지능형 중지
+ 누적 앙상블

+ 시계열 예측
  + 새로운 예측 예측 기능
  + 이제 열계 데이터에 롤링 원원 교차 유효성 검사를 사용할 수 있습니다.
  + 새로운 기능이 추가되어 시간계 지연을 구성할 수 있습니다.
  + 롤링 윈도우 집계 기능을 지원하기 위해 추가된 새로운 기능
  + 실험 설정에서 국가 코드가 정의된 경우 새로운 휴일 감지 및 위화기

+ Azure Databricks
  + 활성화된 열렬 예측 및 모델 설명/해석 가능성 기능
  + 이제 자동화된 ML 실험을 취소하고 다시 시작할 수 있습니다( 계속)합니다.
  + 멀티코어 처리를 위한 추가 지원

### <a name="mlops"></a>MLOps
+ **점수 매기기 컨테이너를 위한 로컬 배포 & 디버깅**<br/> 이제 ML 모델을 로컬로 배포하고 점수 매기기 파일 및 종속성에 빠르게 반복하여 예상대로 작동할 수 있습니다.

+ **인비설스콘피그 & Model.deploy() 도입**<br/> 이제 모델 배포는 RunConfig와 동일한 항목 스크립트를 가진 원본 폴더 지정을 지원합니다.  또한 모델 배포가 단일 명령으로 단순화되었습니다.

+ **Git 참조 추적**<br/> 고객은 종단 간 감사 추적을 유지하는 데 도움이 되기 때문에 몇 시간 동안 기본 Git 통합 기능을 요청해 왔습니다. Git 관련 메타데이터(리포지토리, 커밋, 정리 상태)에 대한 Azure ML의 주요 엔터티에 대한 추적을 구현했습니다. 이 정보는 SDK 및 CLI에 의해 자동으로 수집됩니다.

+ **모델 프로파일링 & 검증 서비스**<br/> 고객은 추론 서비스와 관련된 계산의 크기를 적절히 조정하는 데 어려움을 호소하는 경우가 많습니다. 모델 프로파일링 서비스를 통해 고객은 샘플 입력을 제공할 수 있으며 16가지 CPU/메모리 구성을 프로파일링하여 배포를 위한 최적의 크기를 결정합니다.

+ **추론을 위해 자신의 기본 이미지를 가져오기**<br/> 또 다른 일반적인 불만은 실험에서 추론 RE 공유 종속성으로 이동하는 데 어려움이 있었습니다. 새로운 기본 이미지 공유 기능을 사용하면 이제 추론을 위해 실험 기본 이미지, 종속성 및 모든 이미지를 재사용할 수 있습니다. 이렇게 하면 배포 속도가 빨라지고 내부 루프에서 외부 루프까지의 간격이 줄어듭니다.

+ **향상된 Swagger 스키마 생성 경험**<br/> 이전의 swagger 생성 방법은 오류가 발생하기 쉽고 자동화가 불가능했습니다. 우리는 데코레이터를 통해 모든 파이썬 함수에서 스웨거 스키마를 생성하는 새로운 인라인 방법을 가지고 있습니다. 이 코드를 오픈 소스로 제공했으며 스키마 생성 프로토콜은 Azure ML 플랫폼에 결합되지 않습니다.

+ **Azure ML CLI는 일반적으로 사용할 수 있습니다(GA)**<br/> 이제 단일 CLI 명령으로 모델을 배포할 수 있습니다. Jupyter 노트북에서 ML 모델을 배포하는 사람은 아무도 없다는 일반적인 고객 피드백을 받았습니다. [**CLI 참조 문서가**](https://aka.ms/azmlcli) 업데이트되었습니다.


## <a name="2019-04-22"></a>2019-04-22

파이썬 v1.0.30에 대한 Azure 기계 학습 SDK 출시.

동일한 [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) 끝점을 유지하면서 게시된 파이프라인의 새 버전을 추가하기 위해 도입되었습니다.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure 기계 학습 데이터 준비 SDK v1.1.2

참고 : 데이터 준비 파이썬 SDK는 더 이상 설치 및 패키지를 설치하지 `numpy` `pandas` 않습니다. [업데이트된 설치 지침을](https://github.com/Microsoft/AMLDataPrepDocs)참조하십시오.

+ **새로운 기능**
  + 이제 피벗 변환을 사용할 수 있습니다.
    + 방법 가이드: [피벗 노트북](https://aka.ms/aml-data-prep-pivot-nb)
  + 이제 네이티브 함수에서 정규식을 사용할 수 있습니다.
    + 예제:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 이제 표현식 `to_upper`   `to_lower`  언어로 함수를 사용하고 사용할 수 있습니다.
  + 이제 데이터 프로필에서 각 열의 고유 값 수를 볼 수 있습니다.
  + 일반적으로 사용되는 일부 판독기 단계의 경우 `infer_column_types` 이제 인수를 전달할 수 있습니다. `True`데이터 준비로 설정된 경우 열 유형을 감지하고 자동으로 변환하려고 시도합니다.
    + `inference_arguments`이제 더 이상 사용되지 않습니다.
  + 이제 를 `Dataflow.shape`호출할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + `keep_columns` 이제 추가 `keep_columns` 선택적 인수를 `validate_column_exists`수락합니다.
  + 이제 파일에서 읽은 모든 판독기 단계는 추가 `verify_exists`선택적 인수를 허용합니다.
  + 팬더 데이터 프레임에서 읽기 및 데이터 프로필 얻기의 성능을 향상.
  + 데이터 흐름에서 한 단계를 잘라내는 버그가 단일 인덱스로 실패한 것을 수정했습니다.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure portal
  + 이제 기존 원격 계산 클러스터에서 기존 스크립트 실행을 다시 제출할 수 있습니다.
  + 이제 파이프라인 탭에서 새 매개 변수를 사용 하 고 게시 된 파이프라인을 실행할 수 있습니다.
  + 이제 세부 정보 실행이 새 스냅샷 파일 뷰어를 지원합니다. 특정 실행을 제출할 때 디렉터리 스냅샷을 볼 수 있습니다. 제출된 전자 필기장을 다운로드하여 실행을 시작할 수도 있습니다.
  + 이제 Azure 포털에서 부모 실행을 취소할 수 있습니다.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>파이썬 v1.0.23에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + Azure 기계 학습 SDK는 이제 파이썬 3.7을 지원합니다.
  + Azure 기계 학습 DNN 추정기는 이제 기본 제공 다중 버전 지원을 제공합니다. 예를 들어, `TensorFlow`  추정기는 `framework_version` 이제 매개 변수를 허용하고 사용자는 버전 '1.10' 또는 '1.12'를 지정할 수 있습니다. 현재 SDK 릴리스에서 지원하는 버전 목록의 경우 `get_supported_versions()` 원하는 프레임워크 클래스(예: `TensorFlow.get_supported_versions()`)를 호출합니다.
  최신 SDK 릴리스에서 지원하는 버전 목록은 [DNN 추정설명서를](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)참조하십시오.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure 기계 학습 데이터 준비 SDK v1.1.1

+ **새로운 기능**
  + read_* 변환을 사용하여 여러 데이터스토어/DataPath/DataReference 원본을 읽을 수 있습니다.
  + 열에서 다음 작업을 수행하여 분할, 바닥, 조절, 전원, 길이 등 새 열을 만들 수 있습니다.
  + 데이터 준비는 이제 Azure ML 진단 제품군의 일부이며 기본적으로 진단 정보를 기록합니다.
    + 이 작업을 해제하려면 이 환경 변수를 true로 설정 DISABLE_DPREP_LOGGER합니다.

+ **버그 수정 및 개선 사항**
  + 일반적으로 사용되는 클래스 및 함수에 대한 코드 설명서가 개선되었습니다.
  + Auto_read_file Excel 파일을 읽지 못하는 버그를 수정했습니다.
  + read_pandas_dataframe 폴더를 덮어쓰는 옵션이 추가되었습니다.
  + dotnetcore2 종속성 설치의 성능이 향상되고 페도라 27/28 및 우분투 1804에 대한 지원이 추가되었습니다.
  + Azure Blob에서 읽기 의 성능을 향상했습니다.
  + 이제 열 유형 검색은 Long 형식의 열을 지원합니다.
  + 일부 날짜 값이 Python datetime 개체 대신 타임스탬프로 표시되는 버그를 수정했습니다.
  + 일부 유형 카운트가 정수 대신 복식으로 표시되는 버그를 수정했습니다.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>파이썬 v1.0.21에 대한 Azure 기계 학습 SDK

+ **새로운 기능**
  + *azureml.core.Run.create_children* 메서드를 사용하면 단일 호출로 여러 자식 실행을 짧은 대기 시간으로 만들 수 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure 기계 학습 데이터 준비 SDK v1.1.0

+ **주요 변경 내용**
  + 데이터 준비 패키지의 개념은 더 이상 사용되지 않으며 더 이상 지원되지 않습니다. 하나의 패키지에 여러 데이터 흐름을 유지하는 대신 데이터 흐름을 개별적으로 유지시킬 수 있습니다.
    + 방법 가이드: [데이터 흐름 노트북 열기 및 저장](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **새로운 기능**
  + 이제 데이터 준비는 특정 시맨틱 유형과 일치하는 열을 인식하고 그에 따라 분할할 수 있습니다. 현재 지원되는 SType에는 이메일 주소, 지리적 좌표(경도 & 위도), IPv4 및 IPv6 주소, 미국 전화 번호 및 미국 우편 번호가 포함됩니다.
    + 방법 가이드: [시맨틱 유형 노트북](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 이제 데이터 준비는 다음 작업을 지원하여 빼기, 곱하기, 분할 및 조절의 두 숫자 열에서 결과 열을 생성합니다.
  + Dataflow를 `verify_has_data()` 호출하여 데이터 흐름이 실행되는 경우 레코드를 생성할지 여부를 확인할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + 이제 숫자 열 프로파일에 대해 히스토그램에서 사용할 저장소 수를 지정할 수 있습니다.
  + `read_pandas_dataframe` 이제 변환에는 DataFrame에 문자열 또는 바이트 형식의 열 이름이 있어야 합니다.
  + 열이 누락된 `fill_nulls` 경우 값이 올바르게 채워지지 않은 변환에서 버그를 수정했습니다.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>파이썬 v1.0.18에 대한 Azure 기계 학습 SDK

 + **변경**
   + azureml-텐서보드 패키지는 azureml-contrib-텐서보드를 대체합니다.
   + 이 릴리스에서는 관리되는 계산 클러스터(amlcompute)에서 사용자 계정을 설정하는 동시에 사용자 계정을 만들 수 있습니다. 이 작업은 프로비저닝 구성에서 이러한 속성을 전달하여 수행할 수 있습니다. 자세한 내용은 [SDK 참조 설명서에서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)확인할 수 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure 기계 학습 데이터 준비 SDK v1.0.17

+ **새로운 기능**
  + 이제 두 개의 숫자 열을 추가하여 식 언어를 사용하여 결과 열을 생성할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + random_split 대한 설명서 및 매개 변수 검사를 개선했습니다.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure 기계 학습 데이터 준비 SDK v1.0.16

+ **버그 수정**
  + API 변경으로 인해 발생한 서비스 주체 인증 문제를 수정했습니다.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>파이썬 v1.0.17에 대한 Azure 기계 학습 SDK

+ **새로운 기능**

  + Azure 기계 학습은 이제 인기 있는 DNN 프레임워크 Chainer에 대한 일류 지원을 제공합니다. 클래스 [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 사용자를 사용하면 Chainer 모델을 쉽게 학습하고 배포할 수 있습니다.
    + [ChainerMN을 사용하여 분산 교육을 실행하는](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb) 방법 알아보기
    + [하이퍼드라이브를 사용하여 Chainer로 하이퍼파라미터 튜닝을 실행하는](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) 방법 알아보기
  + Azure 기계 학습 파이프라인은 데이터스토어 수정을 기반으로 파이프라인 실행을 트리거하는 기능을 추가했습니다. 파이프라인 [일정 전자 필기장이](https://aka.ms/pl-schedule) 이 기능을 보여 주도록 업데이트됩니다.

+ **버그 수정 및 개선 사항**
  + 우리는 Source_directory_data_store 속성을 원하는 데이터 스토어 (예 : Blob 저장소)에 설정하기 위한 Azure 기계 학습 파이프라인에 [PythonScriptStep에](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)제공되는 [RunConfigurations에](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 대한 지원을 추가했습니다. 기본적으로 단계는 Azure File 저장소를 백업 데이터 스토어로 사용하며, 많은 수의 단계가 동시에 실행될 때 제한 문제가 발생할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

+ **새로운 기능**
  + 보고서에 대한 새로운 드래그 앤 드롭 테이블 편집기 환경. 사용자는 웰에서 테이블 미리 보기가 표시되는 테이블 영역으로 열을 끌 수 있습니다. 열을 다시 정렬할 수 있습니다.
  + 새 로그 파일 뷰어
  + 활동 탭에서 실험 실행, 계산, 모델, 이미지 및 배포에 대한 링크

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure 기계 학습 데이터 준비 SDK v1.0.15

+ **새로운 기능**
  + 이제 데이터 준비는 데이터 흐름에서 파일 스트림 작성을 지원합니다. 또한 파일 스트림 이름을 조작하여 새 파일 이름을 만드는 기능도 제공합니다.
    + 방법 가이드: [파일 스트림 노트북으로 작업](https://aka.ms/aml-data-prep-file-stream-nb)

+ **버그 수정 및 개선 사항**
  + 대규모 데이터 세트에서 t-다이제스트 성능이 향상되었습니다.
  + 이제 데이터 준비는 DataPath의 데이터 읽기를 지원합니다.
  + 하나의 핫 인코딩은 이제 부울 및 숫자 열에서 작동합니다.
  + 기타 기타 버그 수정.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Python용 Azure Machine Learning SDK v1.0.15

+ **새로운 기능**
  + Azure 기계 학습 파이프라인은[AzureBatchStep(노트북),](https://aka.ms/pl-azbatch)하이퍼드라이브스텝(노트북) 및 시간 기반 일정 기능(노트북)을 추가했습니다.[notebook](https://aka.ms/pl-schedule)
  +  DataTranferStep이 Azure SQL Server 및 Azure Database for PostgreSQL에서 작동하도록 업데이트되었습니다([Notebook](https://aka.ms/pl-data-trans)).

+ **변경**
  + `PublishedPipeline.get_published_pipeline`이 사용되지 않는 대신 `PublishedPipeline.get`이 사용됩니다.
  + `Schedule.get_schedule`이 사용되지 않는 대신 `Schedule.get`이 사용됩니다.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 데이터 준비 SDK v1.0.12

+ **새로운 기능**
  + 데이터 준비 SDK는 이제 데이터 저장소를 사용하여 Azure SQL 데이터베이스에서 읽을 수 있도록 지원합니다.

+ **변경**
  + 대용량 데이터에 대한 특정 작업의 메모리 성능을 향상했습니다.
  + `read_pandas_dataframe()`에는 이제 `temp_folder`를 지정해야 합니다.
  + `ColumnProfile`의 `name` 속성은 더 이상 사용되지 않습니다. 대신 `column_name`을 사용합니다.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Python용 Azure Machine Learning SDK v1.0.10

+ **변경 사항**:
  + Azure ML SDK에는 azure-cli 패키지가 더 이상 종속성으로 포함되지 않습니다. 특히 azure-cli-core 및 azure-cli-profile 종속성이 azureml-core에서 제거되었습니다. 사용자에게 영향을 미치는 변경 내용은 다음과 같습니다.
      + "az 로그인"을 수행한 다음 azureml-sdk를 사용하는 경우 SDK는 브라우저 또는 장치 코드 로그온을 한 번 더 수행합니다. “az login”에서 생성된 자격 증명 상태는 사용되지 않습니다.
    + “az login”을 사용하는 것과 같은 Azure CLI 인증의 경우 _azureml.core.authentication.AzureCliAuthentication_ 클래스를 사용합니다. Azure CLI 인증의 경우 azureml-sdk를 설치한 Python 환경에서 _pip install azure-cli_를 수행합니다.
    + 자동화를 위해 서비스 주체를 사용하여 “az login”을 수행하는 경우에는 Azure CLI에서 생성된 _azureml.core.authentication.ServicePrincipalAuthentication_ 클래스를 사용하는 것이 좋습니다. azureml-sdk는 azure CLI에서 만든 자격 증명 상태를 사용하지 않기 때문입니다.

+ **버그 수정**: 이 릴리스에는 주로 사소한 버그 수정이 포함되어 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **버그 수정**
  + 데이터 프로파일을 가져오는 성능을 향상했습니다.
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

+ **버그 수정**: 이 릴리스에는 주로 사소한 버그 수정이 포함되어 있습니다.

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
+ **버그 수정**: 이 릴리스에는 주로 사소한 버그 수정이 포함되어 있습니다.

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

이제 Azure 기계 학습을 일반적으로 사용할 수 있습니다.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
이번 릴리스에서는 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute)을 통해 새로운 관리형 컴퓨팅 환경을 발표합니다. 이 컴퓨팅 대상은 Azure Machine Learning에 대한 Azure Batch AI 컴퓨팅을 대체합니다.

이 컴퓨팅 대상:
+ 모델 학습 및 일괄 처리 추론/점수 매기기에 사용됩니다.
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

+ **신규**
  + 기계 학습 파이프라인:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **업데이트**
  + 기계 학습 파이프라인:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)이 이제 runconfig를 허용함
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)이 이제 SQL 데이터 원본 간에 복사됨
    + 게시된 파이프라인을 실행하기 위한 일정을 만들고 업데이트하는 SDK의 일정 기능

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **주요 변경 내용**
  * `SummaryFunction.N`의 이름이 `SummaryFunction.Count`로 바뀌었습니다.

+ **버그 수정**
  * 원격 실행시 데이터 스토어에서 읽고 쓸 때 최신 AML 실행 토큰을 사용합니다. 이전에는 AML 실행 토큰이 Python에서 업데이트되는 경우 Data Prep 런타임이 업데이트된 AML 실행 토큰으로 업데이트되지 않습니다.
  * 추가적인 더 분명한 오류 메시지
  * Spark가 `Kryo` serialization을 사용할 때 to_spark_dataframe()이 더 이상 충돌하지 않음
  * Value Count Inspector가 1000개 이상의 고유 값을 표시할 수 있음
  * 원래 데이터 흐름에 이름이 없는 경우 임의 분할이 더 이상 실패하지 않습니다.

+ **자세한 정보**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>문서 및 Notebook
+ ML 파이프라인
  + 파이프라인, 일괄 처리 범위 및 스타일 전송 예제로 시작하기 위한 신규 및 업데이트된 Notebook: https://aka.ms/aml-pipeline-notebooks
  + [첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md) 방법 알아보기
  + [파이프라인을 사용하여 일괄 처리 예측을 실행](how-to-use-parallel-run-step.md)하는 방법 알아보기
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
   * 이제 AML 토큰을 사용하여 원격 계산 대상을 읽을 때 더 이상 실패하지 않습니다.
   * 이제 Linux DSVM에서 더 이상 오류가 발생하지 않습니다.
   * 이제 문자열 조건자에 문자열이 아닌 값이 있는 경우에도 더 이상 충돌하지 않습니다.
   * 이제 Dataflow에서 올바르게 실패해야 하는 경우 어설션 오류를 처리합니다.
   * 이제 Azure Databricks에서 dbutils 탑재 스토리지 위치를 지원합니다.

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure portal
Azure 기계 학습용 Azure 포털에는 다음과 같은 업데이트가 있습니다.
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
  * 바꾸기를 사용할 때 "찾기" 공백을 두면 더 이상 실패하지 않습니다.

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

+ **자동화된 기계 학습**
  * 오류 처리 및 설명서가 향상되었습니다.
  * 실행 속성 검색 성능 문제가 해결되었습니다.
  * 계속 실행 문제가 해결되었습니다.
  * 반복 :::no-loc text="ensembling"::: 문제를 수정했습니다.
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

+ **버그 수정:**
  * 모든 Tornado 버전에서 작동하므로 Tornado 버전을 다운그레이드할 필요가 없습니다.
  * Value에서 상위 3개 외에도 모든 값의 개수를 계산합니다.

## <a name="2018-09-public-preview-refresh"></a>2018-09(공개 미리 보기 새로 고침)

Azure 기계 학습의 새로 새로 고쳐진 릴리스: 이 릴리스에 대해 자세히 알아보세요.https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
