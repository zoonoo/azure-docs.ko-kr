---
title: 'ML Studio (클래식): Azure Machine Learning로 마이그레이션'
description: 현대화 데이터 과학 플랫폼의 경우 Studio (클래식)에서 Azure Machine Learning로 마이그레이션합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 7cdf016d7de66c33973ec7818fa92df28c4639dd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566962"
---
# <a name="migrate-to-azure-machine-learning"></a>Azure Machine Learning으로 마이그레이션

이 문서에서는 Azure Machine Learning으로 Studio (클래식) 자산을 마이그레이션하는 방법에 대해 알아봅니다. 이번에는 리소스를 마이그레이션하기 위해 수동으로 실험을 다시 빌드해야 합니다.

Azure Machine Learning는 코드와 코드 우선 방식을 결합 하는 현대화 데이터 과학 플랫폼을 제공 합니다. 스튜디오 (클래식)와 Azure Machine Learning 간의 차이점에 대 한 자세한 내용은 [Azure Machine Learning 평가](#step-1-assess-azure-machine-learning) 섹션을 참조 하세요.


## <a name="recommended-approach"></a>권장 접근 방식

Azure Machine Learning로 마이그레이션하려면 다음 방법을 사용 하는 것이 좋습니다.

> [!div class="checklist"]
> * 1 단계: Azure Machine Learning 평가
> * 2 단계: 마이그레이션 계획 만들기
> * 3 단계: 실험 및 웹 서비스 다시 빌드
> * 4 단계: 클라이언트 앱 통합
> * 5 단계: Studio (클래식) 자산 정리


## <a name="step-1-assess-azure-machine-learning"></a>1 단계: Azure Machine Learning 평가
1. [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)에 대 한 자세한 정보 이는 이점, 비용 및 아키텍처입니다.

1. Azure Machine Learning 및 Studio (클래식)의 기능을 비교 합니다.

    >[!NOTE]
    > Azure Machine Learning의 **디자이너** 기능은 Studio (클래식)와 비슷한 끌어서 놓기 환경을 제공 합니다. 그러나 Azure Machine Learning는 또한 다른 방법으로 강력한 [코드 중심 워크플로](../concept-model-management-and-deployment.md) 를 제공 합니다. 이 마이그레이션 시리즈는 스튜디오 (클래식) 환경과 가장 유사 하므로 디자이너를 중심으로 설명 합니다.

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Azure Machine Learning designer에서 중요 한 스튜디오 (클래식) 모듈이 지원 되는지 확인 합니다. 자세한 내용은 아래의 [Studio (클래식) 및 디자이너 모듈 매핑](#studio-classic-and-designer-module-mapping) 표를 참조 하세요.

4. [Azure Machine Learning 작업 영역을 만듭니다](../how-to-manage-workspace.md?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>2 단계: 마이그레이션 계획 만들기

1. 마이그레이션할 스튜디오 (클래식) **데이터 집합**, **모델** 및 **웹 서비스** 를 식별 합니다.

1. 마이그레이션이 비즈니스에 미치는 영향을 확인 합니다.

1. 마이그레이션 계획을 만듭니다.

## <a name="step-3-rebuild-experiments-and-web-services"></a>3 단계: 실험 및 웹 서비스 다시 빌드

1. [데이터 집합을 Azure Machine Learning로 마이그레이션합니다](migrate-register-dataset.md).
1. 디자이너를 사용 하 여 [실험을 다시 빌드합니다](migrate-rebuild-experiment.md).
1. 디자이너를 사용 하 여 [웹 서비스](migrate-rebuild-web-service.md)를 다시 배포 합니다.

    >[!NOTE]
    > 또한 Azure Machine Learning는 [데이터 집합](../how-to-create-register-datasets.md), [학습](../how-to-set-up-training-targets.md)및 [배포](../how-to-deploy-and-where.md)에 대 한 코드 우선 워크플로를 지원 합니다.

## <a name="step-4-integrate-client-apps"></a>4 단계: 클라이언트 앱 통합

1. 새 [Azure Machine Learning 끝점](migrate-rebuild-integrate-with-client-app.md)을 사용 하도록 Studio (클래식) 웹 서비스를 호출 하는 클라이언트 응용 프로그램을 수정 합니다.

## <a name="step-5-cleanup-studio-classic-assets"></a>5 단계: Studio (클래식) 자산 정리

1. 추가 요금을 방지 하기 위해 [스튜디오 (클래식) 자산을 정리](export-delete-personal-data-dsr.md) 합니다. Azure Machine Learning 작업의 유효성을 검사할 때까지 대체에 대 한 자산을 유지 하려고 할 수 있습니다.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (클래식) 및 디자이너 모듈 매핑

디자이너에서 Studio (클래식) 실험을 다시 빌드하는 동안 사용할 모듈을 확인 하려면 다음 표를 참조 하세요.


> [!IMPORTANT]
> 이 디자이너는 Studio (클래식)와 같은 c # 패키지 대신 오픈 소스 Python 패키지를 통해 모듈을 구현 합니다. 이러한 차이로 인해 디자이너 모듈의 출력은 해당 스튜디오 (클래식)와 약간 다를 수 있습니다.


|범주|Studio (클래식) 모듈|대체 디자이너 모듈|
|--------------|----------------|--------------------------------------|
|데이터 입력 및 출력|-수동으로 데이터 입력 </br> -데이터 내보내기 </br> -데이터 가져오기 </br> -학습 된 모델 로드 </br> -압축 데이터 집합 압축 풀기|-수동으로 데이터 입력 </br> -데이터 내보내기 </br> -데이터 가져오기|
|데이터 형식 변환|-CSV로 변환 </br> -데이터 집합으로 변환 </br> -ARFF로 변환 </br> -SVMLight로 변환 </br> -TSV로 변환|-CSV로 변환 </br> -데이터 집합으로 변환|
|데이터 변환-조작|-열 추가</br> -행 추가 </br> -SQL 변환 적용 </br> -누락 된 데이터 정리 </br> -표시기 값으로 변환 </br> -메타 데이터 편집 </br> -조인 데이터 </br> -중복 행 제거 </br> -데이터 집합에서 열 선택 </br> -열 변환 선택 </br> -SMOTE </br> -범주 값 그룹화|-열 추가</br> -행 추가 </br> -SQL 변환 적용 </br> -누락 된 데이터 정리 </br> -표시기 값으로 변환 </br> -메타 데이터 편집 </br> -조인 데이터 </br> -중복 행 제거 </br> -데이터 집합에서 열 선택 </br> -열 변환 선택 </br> -SMOTE|
|데이터 변환 – 크기 조정 및 축소 |-클립 값 </br> -Bin으로 데이터 그룹화 </br> -데이터 정규화 </br>-주 구성 요소 분석 |-클립 값 </br> -Bin으로 데이터 그룹화 </br> -데이터 정규화|
|데이터 변환 – 샘플 및 분할|-Partition and Sample </br> -데이터 분할|-Partition and Sample </br> -데이터 분할|
|데이터 변환 – 필터 |-필터 적용 </br> -전나무 필터 </br> -IIR 필터 </br> -중앙값 필터 </br> -이동 평균 필터 </br> -임계값 필터 </br> -사용자 정의 필터||
|데이터 변환 – 개수로 학습 |-빌드 계산 변환 </br> -내보내기 수 테이블 </br> -가져오기 수 테이블 </br> -병합 수 변환</br>  -개수 테이블 매개 변수 수정||
|기능 선택 |-필터 기반 기능 선택 </br> -피셔 선형 판별 분석  </br> -순열 기능 중요도 |-필터 기반 기능 선택 </br>  -순열 기능 중요도|
| 모델 분류| -다중 클래스 의사 결정 포리스트 </br> -다중 클래스 의사 결정 정글  </br> -다중 클래스 로지스틱 회귀  </br>-다중 클래스 신경망  </br>-일대다 다중 클래스 </br>-Two-Class 평균 퍼셉트론 </br>-Two-Class Bayes Point 컴퓨터 </br>-Two-Class 승격 된 의사 결정 트리  </br> -Two-Class 의사 결정 포리스트  </br> -Two-Class 의사 결정 정글  </br> -Two-Class Locally-Deep SVM </br> -Two-Class 로지스틱 회귀  </br> -Two-Class 신경망 </br> -Two-Class 지원 벡터 컴퓨터  | -다중 클래스 의사 결정 포리스트 </br>  -다중 클래스 부스트 의사 결정 트리  </br> -다중 클래스 로지스틱 회귀 </br> -다중 클래스 신경망 </br> -일대다 다중 클래스  </br> -Two-Class 평균 퍼셉트론  </br> -Two-Class 승격 된 의사 결정 트리  </br> -Two-Class 의사 결정 포리스트 </br>-Two-Class 로지스틱 회귀 </br> -Two-Class 신경망 </br>-Two-Class 지원 벡터 컴퓨터  |
| 모델 클러스터링| -K-클러스터링을 의미 합니다.| -K-클러스터링을 의미 합니다.|
| 모델-회귀| -Bayesian 선형 회귀  </br> -승격 된 의사 결정 트리 회귀  </br>-의사 결정 포리스트 회귀  </br> -고속 포리스트 변 위치 회귀  </br> -선형 회귀 </br> -신경망 회귀 </br> -서 수 회귀 포아송 회귀| -승격 된 의사 결정 트리 회귀  </br>-의사 결정 포리스트 회귀  </br> -고속 포리스트 변 위치 회귀 </br> -선형 회귀  </br> -신경망 회귀 </br> -포아송 회귀|
| 모델 – 변칙 검색| -One-Class SVM  </br> -PCA-Based 변칙 검색 | -PCA-Based 변칙 검색|
| Machine Learning – 평가  | -모델 교차 유효성 검사  </br>-모델 평가  </br>-추천 평가 | -모델 교차 유효성 검사  </br>-모델 평가 </br> -추천 평가|
| Machine Learning – 학습| -스윕 클러스터링  </br> -변칙 검색 모델 학습 </br>-클러스터링 모델 학습  </br> -Matchbox 추천 학습-</br> 모델 학습  </br>-모델 하이퍼 매개 변수 조정| -변칙 검색 모델 학습  </br> -클러스터링 모델 학습 </br> -모델 학습-</br> -PyTorch 모델 학습  </br>-교육 .SVD 추천  </br>-전체 및 심층 추천 학습 </br>-모델 하이퍼 매개 변수 조정|
| Machine Learning – 점수| -변환 적용  </br>-클러스터에 데이터 할당  </br>-Matchbox 추천 점수 매기기 </br> -모델 점수 매기기|-변환 적용  </br> -클러스터에 데이터 할당 </br> -이미지 모델 점수 매기기  </br> -모델 점수 매기기 </br>-점수 .SVD 추천 </br> -전체 및 딥 추천 점수 매기기|
| OpenCV 라이브러리 모듈| -이미지 가져오기 </br>-미리 학습 된 계단식 이미지 분류 | |
| Python 언어 모듈| -Python 스크립트 실행| -Python 스크립트 실행  </br> -Python 모델 만들기 |
| R 언어 모듈  | -R 스크립트 실행  </br> -R 모델 만들기| -R 스크립트 실행|
| 통계 함수 | -수학 연산을 적용 합니다. </br>-계산 기본 통계  </br>-Compute 선형 상관 관계  </br>-Probability 함수를 계산 합니다.  </br>-불연속 값 바꾸기  </br>-데이터 요약  </br>-T-테스트를 사용 하 여 가설 테스트| -수학 연산을 적용 합니다.  </br>-데이터 요약|
| 텍스트 분석| -검색 언어  </br>-텍스트에서 키 구를 추출 합니다.  </br>-텍스트에서 N-문법 기능 추출  </br>-기능 해시 </br>-잠재적인 Dirichlet 할당  </br>-명명 된 엔터티 인식 </br>-전처리 텍스트  </br>-점수 Vowpal Wabbit 버전 7-10 모델  </br>-점수 Vowpal Wabbit 버전 8 모델 </br>-Vowpal Wabbit 버전 7-10 모델 학습  </br>-Vowpal Wabbit 버전 8 모델 학습 |-Word를 벡터로 변환 </br> -텍스트에서 N-문법 기능 추출 </br>-기능 해시  </br>-잠재적인 Dirichlet 할당 </br>-전처리 텍스트  </br>-점수 Vowpal Wabbit 모델 </br> -Vowpal Wabbit 모델 학습|
| 시계열| -시계열 변칙 검색 | |
| 웹 서비스 | -입력 </br> -출력 | -입력 </br>  - 출력|
| Computer Vision| | -이미지 변환 적용 </br> -이미지 디렉터리로 변환 </br> -초기화 이미지 변환 </br> -분할 이미지 디렉터리  </br> -DenseNet 이미지 분류   </br>-ResNet 이미지 분류 |

개별 디자이너 모듈을 사용 하는 방법에 대 한 자세한 내용은 [디자이너 모듈 참조](../algorithm-module-reference/module-reference.md)를 참조 하세요.

### <a name="what-if-a-designer-module-is-missing"></a>디자이너 모듈이 없는 경우 어떻게 되나요?

Azure Machine Learning 디자이너에는 Studio (클래식)에서 가장 인기 있는 모듈이 포함 되어 있습니다. 또한 최신 기계 학습 기술을 활용 하는 새로운 모듈도 포함 되어 있습니다. 

디자이너에 누락 된 모듈이 있어 마이그레이션이 차단 된 경우 [지원 티켓을 만들어](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)문의해 주세요.

## <a name="example-migration"></a>마이그레이션 예

다음 실험 마이그레이션은 스튜디오 (클래식)와 Azure Machine Learning 간의 차이점 중 일부를 강조 표시 합니다.

### <a name="datasets"></a>데이터 세트

Studio (클래식)에서 **데이터 집합** 은 작업 영역에 저장 되었으며 studio (클래식) 에서만 사용할 수 있습니다.

![자동차-가격-클래식-데이터 집합](./media/migrate-overview/studio-classic-dataset.png)

Azure Machine Learning **데이터 집합** 은 작업 영역에 등록 되며 모든 Azure Machine Learning에서 사용할 수 있습니다. Azure Machine Learning 데이터 집합의 이점에 대 한 자세한 내용은 [데이터 액세스 보안](../concept-data.md#reference-data-in-storage-with-datasets)을 참조 하세요.

![자동차-가격-aml-데이터 집합](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>파이프라인

스튜디오 (클래식)에서 **실험** 은 작업에 대 한 처리 논리를 포함 합니다. 끌어서 놓기 모듈로 실험을 만들었습니다.


![자동차-가격-클래식-실험](./media/migrate-overview/studio-classic-experiment.png)

Azure Machine Learning **파이프라인** 은 작업에 대 한 처리 논리를 포함 합니다. 끌어서 놓기 모듈이 나 코드를 작성 하 여 파이프라인을 만들 수 있습니다.

![자동차-가격-aml-파이프라인](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>웹 서비스 엔드포인트

Studio (클래식)에서 **요청/응답 API** 는 실시간 예측에 사용 되었습니다. 일괄 처리 **실행 API** 는 일괄 처리 예측 또는 재 학습에 사용 되었습니다.

![자동차-가격-클래식-웹 서비스](./media/migrate-overview/studio-classic-web-service.png)

Azure Machine Learning 실시간 **끝점** 은 실시간 예측에 사용 됩니다. **파이프라인 끝점** 은 일괄 처리 예측 또는 재 학습에 사용 됩니다.

![자동차-가격-aml-끝점](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning로 마이그레이션하기 위한 높은 수준의 요구 사항을 배웠습니다. 자세한 단계는 Studio (클래식) 마이그레이션 시리즈의 다른 문서를 참조 하세요.

1. **마이그레이션 개요**.
1. [데이터 집합을 마이그레이션합니다](migrate-register-dataset.md).
1. [Studio (클래식) 교육 파이프라인을 다시 빌드합니다](migrate-rebuild-experiment.md).
1. [Studio (클래식) 웹 서비스를 다시 빌드합니다](migrate-rebuild-web-service.md).
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션합니다](migrate-execute-r-script.md).