---
title: 용어집-LUIS
description: 용어집에서는 LUIS API 서비스를 사용할 때 나타날 수 있는 용어에 대해 설명합니다.
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 1513099decc21a7d219bfcb84563619640028550
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681607"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>일반적인 용어 및 개념의 언어 이해 용어집
LUIS (Language Understanding) 용어집에서는 LUIS 서비스에서 작업할 때 발생할 수 있는 용어에 대해 설명 합니다.

## <a name="active-version"></a>활성 버전

활성 버전은 LUIS 포털을 사용 하 여 모델을 변경 하는 경우 업데이트 되는 응용 프로그램의 [버전](luis-how-to-manage-versions.md) 입니다. LUIS 포털에서 활성 버전이 아닌 버전을 변경 하려면 먼저 해당 버전을 활성으로 설정 해야 합니다.

## <a name="active-learning"></a>능동적 학습

활성 학습은 컴퓨터에서 학습 한 모델을 사용 하 여 레이블에 대 한 새로운 정보를 식별 하는 기계 학습 기술입니다. LUIS에서 활성 학습은 현재 예측이 명확 하지 않은 끝점 트래픽의 길이 발언를 추가 하 여 모델을 개선 하는 것을 말합니다. "Endpoint 길이 발언 검토"를 클릭 하 여 길이 발언에 레이블을 표시 합니다.

참고 항목:
* [개념 정보](luis-concept-review-endpoint-utterances.md)
* [길이 발언 끝점 검토 자습서](luis-tutorial-review-endpoint-utterances.md)
* [엔드포인트 길이 발언을 검토](luis-how-to-review-endpoint-utterances.md) 하 여 LUIS 앱을 개선 하는 방법

## <a name="application-app"></a>응용 프로그램 (앱)

LUIS에서 응용 프로그램 또는 앱은 동일한 데이터 집합을 기반으로 하는 기계 학습 모델의 컬렉션으로, 특정 시나리오에 대 한 의도 및 엔터티를 예측 하기 위해 함께 작동 합니다. 각 응용 프로그램에는 별도의 예측 끝점이 있습니다.

HR 봇을 작성 하는 경우 단일 응용 프로그램으로 그룹화 하는 이러한 의도의 각 항목에 대 한 "일정 탈퇴 시간", "혜택에 대 한 정보" 및 "개인 정보 업데이트"와 같은 일련의 의도가 있을 수 있습니다.

## <a name="authoring"></a>작성

제작은 LUIS 포털 또는 제작 Api를 사용 하 여 LUIS 앱을 만들고, 관리 하 고, 배포할 수 있는 기능입니다.

### <a name="authoring-key"></a>제작 키

[제작 키](luis-concept-keys.md) 는 앱을 작성 하는 데 사용 됩니다. 프로덕션 수준 엔드포인트 쿼리에는 사용되지 않습니다. 자세한 내용은 [키 제한](luis-limits.md#key-limits)을 참조하세요.

### <a name="authoring-resource"></a>제작 리소스

LUIS [authoring resource](luis-concept-keys.md#azure-resources-for-luis) 는 Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 리소스는 Azure 서비스의 연결 된 제작, 학습 및 게시 기능에 액세스할 수 있습니다. 리소스에는 연결 된 Azure 서비스에 액세스 하는 데 필요한 인증, 권한 부여 및 보안 정보가 포함 되어 있습니다.

제작 리소스에는의 Azure "종류"가 있습니다 `LUIS-Authoring` .

## <a name="batch-test"></a>테스트 일괄 처리

일괄 처리 테스트는 사용자 길이 발언의 일관 되 고 알려진 테스트 집합을 사용 하 여 현재 LUIS 앱 모델의 유효성을 검사 하는 기능입니다. 일괄 처리 테스트는 [JSON 형식의 파일](luis-concept-batch-test.md#batch-file-format)에서 정의 됩니다.

참고 항목:
* [개념](luis-concept-batch-test.md)
* 일괄 처리 테스트를 실행 하 [는 방법](luis-how-to-batch-test.md)
* [자습서](luis-tutorial-batch-testing.md) -batch 테스트 만들기 및 실행

### <a name="f-measure"></a>F 측정값

일괄 테스트에서 테스트 정확도의 측정값입니다.

### <a name="false-negative-fn"></a>False 음수 (FN)

일괄 테스트에서 데이터 요소가 앱이 대상 의도/엔터티가 없음을 잘못 예측한 발언을 나타냅니다.

### <a name="false-positive-fp"></a>가양성 (FP)

일괄 테스트에서 데이터 요소가 앱이 대상 의도/엔터티가 있음을 잘못 예측한 발언을 나타냅니다.

### <a name="precision"></a>전체 자릿수
일괄 테스트에서 정밀도(긍정적 예측 값이라고도 함)는 검색된 발언 중에 관련된 발언의 일부입니다.

동물 일괄 처리 테스트에 대 한 예는 총 동물 수 (양 및 하 한가)로 나누어 예측 된 양입니다.

### <a name="recall"></a>회수

일괄 테스트에서 재현율(민감도라고도 함)은 LUIS가 일반화하는 기능입니다.

동물 배치 테스트에 대 한 예제는 사용 가능한 총 양 수로 나누어 예측 된 양입니다.

### <a name="true-negative-tn"></a>참 부정(TN)

True 음수는 앱에서 일치 하는 항목을 올바르게 예측 하지 못하는 경우입니다. 일괄 처리 테스트에서 앱이 해당 의도 나 엔터티로 레이블이 지정 되지 않은 예제에 대해 의도 또는 엔터티를 예측 하는 경우 진정한 음수가 발생 합니다.

### <a name="true-positive-tp"></a>참 긍정(TP)

진정한 긍정 (TP)는 앱에서 일치 하는 항목을 올바르게 예측 하는 경우입니다. 일괄 처리 테스트에서 응용 프로그램이 의도 또는 엔터티를 사용 하 여 레이블이 지정 된 예제에 대해 의도 또는 엔터티를 예측할 때 진정한 긍정적인 현상이 발생 합니다.

## <a name="classifier"></a>분류자

분류자는 입력이 들어가는 범주나 클래스를 예측 하는 기계 학습 모델입니다.

[의도](#intent) 는 분류자의 한 예입니다.

## <a name="collaborator"></a>협력자

협력자는 개념적으로 [기여자](#contributor)와 동일 합니다. 소유자가 RBAC (역할 기반 액세스)로 제어 되지 않는 앱에 협력자의 전자 메일 주소를 추가할 때 협력자에 게 액세스 권한이 부여 됩니다. 아직 협력자를 사용 하는 경우 LUIS 계정을 마이그레이션하고 LUIS authoring 리소스를 사용 하 여 RBAC로 참가자를 관리 해야 합니다.

## <a name="contributor"></a>참가자

참가자는 앱의 [소유자](#owner) 가 아니지만 의도, 엔터티, 길이 발언를 추가, 편집 및 삭제할 수 있는 권한이 있습니다. 참가자는 LUIS 앱에 RBAC (역할 기반 액세스)를 제공 합니다.

참고 항목:
* 참가자를 추가 하 [는 방법](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

## <a name="descriptor"></a>설명자

설명자는 machine learning [기능](#features)에 이전에 사용 된 용어입니다.

## <a name="domain"></a>도메인

LUIS 컨텍스트에서 도메인은 정보 영역입니다. 사용자의 도메인은 시나리오에 따라 다릅니다. 도메인은 도메인의 컨텍스트에서 의미가 있는 특정 언어와 용어를 사용 합니다. 예를 들어 음악을 재생 하는 응용 프로그램을 빌드하는 경우 응용 프로그램에는 음악과 관련 된 단어, 즉 "노래, 트랙, 앨범, 가사, b 측, 음악가"와 같은 언어가 있습니다. 도메인의 예는 [미리 빌드된 도메인](#prebuilt-domain)을 참조 하세요.

## <a name="endpoint"></a>엔드포인트

### <a name="authoring-endpoint"></a>제작 끝점

LUIS 제작 끝점 URL은 앱을 작성, 학습 및 게시 하는 위치입니다. 끝점 URL에는 게시 된 앱의 지역 또는 사용자 지정 하위 도메인 및 앱 ID가 포함 됩니다.

[개발자 참조](developer-reference-resource.md#rest-endpoints) 에서 프로그래밍 방식으로 앱을 제작 하는 방법에 대해 자세히 알아보세요.

### <a name="prediction-endpoint"></a>예측 끝점

LUIS 예측 끝점 URL은 [LUIS 앱](#application-app) 이 작성 되 고 게시 된 후 LUIS 쿼리를 제출 하는 위치입니다. 끝점 URL에는 게시 된 앱의 지역 또는 사용자 지정 하위 도메인 및 앱 ID가 포함 됩니다. 앱의 **[Azure 리소스](luis-how-to-azure-subscription.md)** 페이지에서 끝점을 찾거나 [앱 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API에서 끝점 URL을 가져올 수 있습니다.

예측 끝점에 대 한 액세스 권한은 LUIS 예측 키로 인증 됩니다.

## <a name="entity"></a>엔터티

[엔터티](luis-concept-entity-types.md) 는 의도를 충족 하거나 식별 하는 데 사용 되는 정보를 설명 하는 길이 발언의 단어입니다. 엔터티가 복잡 하 고 모델이 특정 파트를 식별 하 게 하려는 경우 모델을 하위 엔터티로 나눌 수 있습니다. 예를 들어 주소를 예측 하는 모델을 만들고, 주소, 구/군/시, 시/도 및 zipcode의 하위 엔터티만 확인할 수 있습니다. 엔터티는 모델에 대 한 기능으로도 사용할 수 있습니다. LUIS 앱의 응답에는 예측 된 의도 및 모든 엔터티가 포함 됩니다.

### <a name="entity-extractor"></a>엔터티 추출기

LUIS는 엔터티를 예측 하는 데 사용 하는 기계 학습 모델의 유형인 추출기로만 알려져 있습니다.

### <a name="entity-schema"></a>엔터티 스키마

엔터티 스키마는 하위 엔터티를 사용 하 여 컴퓨터에서 학습 한 엔터티를 정의 하는 구조입니다. 예측 끝점은 스키마에 정의 된 모든 추출 된 엔터티 및 하위 엔터티를 반환 합니다.

### <a name="entitys-subentity"></a>엔터티의 하위 엔터티

하위 엔터티는 기계 학습 엔터티의 자식 엔터티입니다.

### <a name="non-machine-learning-entity"></a>비 기계어 학습 엔터티

텍스트 일치를 사용 하 여 데이터를 추출 하는 엔터티입니다.
* 목록 엔터티
* 정규식 엔터티

### <a name="list-entity"></a>목록 엔터티

[목록 엔터티](reference-entity-list.md) 는 동의어와 함께 고정 된 닫힌 관련 단어 집합을 나타냅니다. 목록 엔터티는 머시닝된 학습 된 엔터티와 달리 정확히 일치 합니다.

목록 엔터티의 단어가 목록에 포함 되 면 엔터티가 예측 됩니다. 예를 들어 "size" 라는 목록 엔터티가 있고 목록에 "small, medium, large" 라는 단어가 있는 경우, 컨텍스트에 관계 없이 "small", "medium" 또는 "large" 단어가 사용 되는 모든 길이 발언에 대해 크기 엔터티가 예측 됩니다.

### <a name="regular-expression"></a>정규식

정규식 [엔터티](reference-entity-regular-expression.md) 는 정규식을 나타냅니다. 정규식 엔터티는 머시닝된 학습 된 엔터티와 달리 정확히 일치 합니다.
### <a name="prebuilt-entity"></a>미리 빌드된 엔터티

미리 빌드된 [엔터티에](#prebuilt-entity) 대 한 미리 빌드된 모델 항목을 참조 하세요.

## <a name="features"></a>기능

기계 학습에서 기능은 모델이 특정 개념을 인식 하는 데 도움이 되는 특징입니다. LUIS는 사용할 수 있지만 하드 규칙은 사용할 수 없다는 힌트입니다.

이 용어를 **[기계 학습 기능이](luis-concept-feature.md)** 라고도 합니다.

이러한 힌트는 레이블과 함께 사용 되어 새 데이터를 예측 하는 방법을 알아봅니다. LUIS는 구 목록과 다른 모델을 기능으로 사용 하는 기능을 모두 지원 합니다.

### <a name="required-feature"></a>필수 기능

필수 기능은 LUIS 모델의 출력을 제한 하는 방법입니다. 엔터티에 대 한 기능이 필수로 표시 되는 경우이 기능은 컴퓨터에서 얻은 모델이 예측 하는 것에 관계 없이 예측 될 엔터티의 예제에 있어야 합니다.

메뉴 순서 봇의 quantity 엔터티에 필요한 것으로 표시 한 미리 작성 된 숫자 기능이 있는 경우를 예로 들어 보겠습니다. 봇이 표시 되 면 `I want a bajillion large pizzas?` bajillion가 표시 되는 컨텍스트에 관계 없이 수량으로 예측 되지 않습니다. Bajillion은 유효한 숫자가 아니며 미리 작성 된 엔터티 수로 예측 되지 않습니다.

## <a name="intent"></a>Intent

[의도](luis-concept-intent.md) 는 사용자가 수행 하려는 작업 또는 작업을 나타냅니다. 비행 예약 또는 청구서 지불과 같은 사용자의 입력으로 표현 되는 목적 또는 목표입니다. LUIS에서 전체적으로 utterance는 의도 한 것으로 분류 되지만 utterance의 부분은 엔터티로 추출 됩니다.

## <a name="labeling-examples"></a>레이블 지정 예제

레이블 지정 또는 표시는 모델에 긍정 또는 부정 예를 연결 하는 프로세스입니다.

### <a name="labeling-for-intents"></a>의도에 대 한 레이블 지정
LUIS에서 앱 내의 의도는 함께 사용할 수 없습니다. 즉, 의도에 utterance를 추가 하면 해당 의도에 대 한 _긍정_ 예와 다른 모든 의도에 대 한 _부정적_ 예가 고려 됩니다. 음수 예는 앱 범위 밖에 있는 길이 발언을 나타내는 "없음" 의도로 혼동 해서는 안 됩니다.

### <a name="labeling-for-entities"></a>엔터티에 대 한 레이블 지정
LUIS에서는 의도의 예제 utterance에 있는 단어 또는 구의 레이블을 _긍정_ 예제로 [레이블을](label-entity-example-utterance.md) 만듭니다. 레이블 지정은 해당 utterance에 대해 예측 해야 하는 것을 보여 줍니다. 레이블 길이 발언은 의도를 학습 하는 데 사용 됩니다.

## <a name="luis-app"></a>LUIS 앱

응용 프로그램에 대 한 정의 [(앱)](#application-app)를 참조 하세요.

## <a name="model"></a>모델

(알려진 컴퓨터) 모델은 입력 데이터에 대해 예측을 수행 하는 함수입니다. LUIS에서는 일반적으로 "모델" 이라고 하는 의도 분류자 및 엔터티 추출기를 지칭 하며, "앱"으로 함께 학습, 게시 및 쿼리 되는 모델 컬렉션을 지칭 합니다.

## <a name="normalized-value"></a>정규화 된 값

[목록](#list-entity) 엔터티에 값을 추가 합니다. 이러한 각 값에는 하나 이상의 동의어 목록이 있을 수 있습니다. 정규화 된 값만 응답에서 반환 됩니다.

## <a name="overfitting"></a>과잉 맞춤

과잉 맞춤는 모델이 특정 예제에 고정 되어 있어 효율적으로 사용할 수 없을 때 발생 합니다.

## <a name="owner"></a>소유자

각 앱에는 앱을 만든 사용자인 하나의 소유자가 있습니다. 소유자는 Azure Portal에서 응용 프로그램에 대 한 사용 권한을 관리 합니다.

## <a name="phrase-list"></a>구문 목록

[구 목록은](luis-concept-feature.md) 동일한 클래스에 속하고 유사한 (예: 도시 또는 제품의 이름)로 처리 해야 하는 값 (단어 또는 구)의 그룹을 포함 하는 특정 유형의 기계 학습 기능입니다.

## <a name="prebuilt-model"></a>미리 빌드된 모델

미리 작성 된 [모델](luis-concept-prebuilt-model.md) 은 레이블 예제와 함께 의도, 엔터티 또는 둘 다의 컬렉션입니다. 앱에 필요한 모델 개발 작업을 줄이기 위해 이러한 공통 미리 작성 된 모델을 앱에 추가할 수 있습니다.

### <a name="prebuilt-domain"></a>미리 빌드된 도메인

미리 빌드된 도메인은 홈 자동화(HomeAutomation) 또는 음식점 예약(RestaurantReservation)과 같이 특정 도메인에 구성된 LUIS 앱입니다. 의도, 발화 및 엔터티가 이 도메인에 구성됩니다.

### <a name="prebuilt-entity"></a>미리 빌드된 엔터티

미리 빌드된 엔터티는 LUIS가 번호, URL 및 메일과 같은 일반적인 정보 유형에 제공하는 엔터티입니다. 이러한 데이터는 공용 데이터를 기반으로 생성 됩니다. 미리 작성 된 엔터티를 독립 실행형 엔터티로 추가 하거나 엔터티에 기능으로 추가 하도록 선택할 수 있습니다.

### <a name="prebuilt-intent"></a>미리 작성 한 의도

미리 작성 된 의도는 일반적인 유형의 정보를 제공 하 고 고유한 레이블이 지정 된 예제 길이 발언을 제공 하는 의도 LUIS.

## <a name="prediction"></a>예측

예측은 새 데이터 (사용자 utterance)를 사용 하는 Azure LUIS 예측 서비스에 대 한 REST 요청으로, 학습 된 응용 프로그램 및 게시 된 응용 프로그램을 해당 데이터에 적용 하 여 어떤 의도 및 엔터티를 찾을 것인지 결정 합니다.

### <a name="prediction-key"></a>예측 키

[예측 키](luis-concept-keys.md) (이전에는 구독 키)는 예측 끝점 사용에 대 한 권한을 부여 하는 Azure에서 만든 LUIS 서비스와 연결 된 키입니다.

이 키는 작성 키가 아닙니다. 예측 끝점 키가 있는 경우 제작 키 대신 모든 끝점 요청에 사용 해야 합니다. LUIS 웹 사이트의 Azure 리소스 페이지 맨 아래에 있는 끝점 URL 내에서 현재 예측 키를 볼 수 있습니다. 이 값은 구독 키 이름/값 쌍의 값입니다.

### <a name="prediction-resource"></a>예측 리소스

LUIS 예측 리소스는 Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 리소스는 Azure 서비스의 연결 된 예측에 액세스할 수 있습니다. 리소스에 예측이 포함 됩니다.

예측 리소스에는의 Azure "kind"가 있습니다 `LUIS` .

### <a name="prediction-score"></a>예측 점수

[점수](luis-concept-prediction-score.md) 는 0에서 1 사이의 숫자로, 특정 입력 utterance 특정 의도와 일치 하는 시스템의 확신을 측정 한 것입니다. 점수가 1에 가까울수록 시스템이 출력에 대해 매우 확신 하 고 점수가 0에 가까울수록 시스템이 입력이 특정 출력과 일치 하지 않는다는 것을 의미 합니다. 중간 점수는 시스템에서 결정 하는 방법에 대해 확실히 알지 못합니다.

예를 들어 일부 고객 텍스트에 음식 주문이 포함 되는지 여부를 식별 하는 데 사용 되는 모델을 가져옵니다. "커피를 주문 하 고 싶습니다." (시스템은 주문 임을 확신 함), "내 팀이 마지막 밤에 승리 했습니다." 라는 점수가 0 인 경우 점수를 1로 지정할 수 있습니다 (시스템은 주문이 아닌 것으로 확신 함). "Tea를 포함 하 고 있습니다." 라는 점수가 0.5 것일 수 있습니다 (이것이 주문 인지 여부를 알 수 없음).

## <a name="programmatic-key"></a>프로그래밍 방식 키

이름이 [작성 키](#authoring-key)로 바뀌었습니다.

## <a name="publish"></a>게시

[게시](luis-how-to-publish-app.md) 는 스테이징 또는 프로덕션 [끝점](#endpoint)에서 LUIS 활성 버전을 사용할 수 있도록 하는 것을 의미 합니다.

## <a name="quota"></a>할당량

LUIS 할당량은 Azure 구독 계층의 제한 사항입니다. LUIS 할당량은 초당 요청 수(HTTP 상태 429) 및 월별 총 요청 수(HTTP 상태 403)를 통해 제한할 수 있습니다.

## <a name="schema"></a>스키마

스키마는 하위 엔터티와 함께 사용자의 의도 및 엔터티를 포함 합니다. 스키마는 초기에 계획 된 후 시간이 지남에 따라 반복 됩니다. 이 스키마에는 앱 설정, 기능 또는 예제 길이 발언 포함 되지 않습니다.

## <a name="sentiment-analysis"></a>감정 분석
감정 분석은 [Text Analytics](../text-analytics/overview.md)에서 제공되는 발화의 긍정적 또는 부정적 값을 제공합니다.

## <a name="speech-priming"></a>음성 초기화

Speech 준비는 [음성 서비스](../speech-service/overview.md)를 사용 하는 시나리오에서 자주 사용 되는 음성 단어 및 문구 인식을 향상 시킵니다. Speech 준비 enabled 응용 프로그램의 경우이 특정 응용 프로그램에 대 한 사용자 지정 음성 모델을 만들어 음성 인식 정확도를 향상 시키기 위해 모든 LUIS 레이블이 지정 된 예를 사용 합니다. 예를 들어, 체스 게임에서 사용자에 게 "기사 이동"이 표시 되는지 확인 하려는 경우 "야간 이동"으로 해석 되지 않습니다. LUIS 앱에는 "knight"가 엔터티로 레이블이 지정된 예제가 포함되어야 합니다.

## <a name="starter-key"></a>시작 키

LUIS를 사용 하 여 처음 시작할 때 사용할 수 있는 무료 키입니다.

## <a name="synonyms"></a>동의어

LUIS [list 엔터티](reference-entity-list.md)에서 정규화 된 값을 만들 수 있습니다 .이 값에는 각각 동의어 목록이 있습니다. 예를 들어, small, medium, large 및 특대의 정규화 된 값이 있는 크기 엔터티를 만들 수 있습니다. 다음과 같이 각 값에 대 한 동의어를 만들 수 있습니다.

|Nomalized 값| 동의어|
|--|--|
|소형| 작은 1, 8 온스|
|중간| 일반, 12 온스|
|대형| 큼, 16 온스|
|Xtra 큼| 가장 큰 1, 24 온스|

모델은 입력에 동의어가 표시 될 때 엔터티에 대 한 정규화 된 값을 반환 합니다.

## <a name="test"></a>테스트

LUIS 앱을 [테스트](luis-concept-test.md) 하면 모델 예측을 볼 것입니다.

## <a name="timezone-offset"></a>표준 시간대 오프셋

끝점에는 [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)가 포함 됩니다. 이는 datetimeV2 미리 빌드된 엔터티에서 추가하거나 제거하려는 시간(분)입니다. 예를 들어, 발화가 “몇 시인가요?”인 경우 반환된 datetimeV2는 클라이언트 요청의 현재 시간입니다. 클라이언트 요청을 봇에서 가져오거나 봇의 사용자와 동일하지 않은 다른 애플리케이션에서 가져오는 경우에는 봇과 사용자 간의 오프셋을 전달해야 합니다.

[미리 빌드된 datetimeV2 엔터티의 표준 시간대 변경](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)을 참조하세요.

## <a name="token"></a>토큰
[토큰](luis-language-support.md#tokenization) 은 LUIS에서 인식할 수 있는 텍스트의 가장 작은 단위입니다. 이는 여러 언어에서 약간씩 다릅니다.

**영어**의 경우 토큰은 문자 및 숫자의 연속 범위 (공백 또는 문장 부호 없음)입니다. 공백이 토큰이 아닙니다.

|문장을|토큰 수|설명|
|--|--|--|
|`Dog`|1|문장 부호가 나 공백이 없는 단일 단어입니다.|
|`RMT33W`|1|레코드 로케이터 번호입니다. 숫자와 문자를 포함할 수 있지만 문장 부호는 없습니다.|
|`425-555-5555`|5|전화 번호입니다. 각 문장 부호는 단일 토큰 이므로 `425-555-5555` 5 개의 토큰입니다.<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>학습

[학습](luis-how-to-train.md) 은 마지막 학습 이후 활성 버전의 변경 내용에 대 한 LUIS을 교육 하는 프로세스입니다.

### <a name="training-data"></a>학습 데이터

데이터 학습은 모델을 학습 하는 데 필요한 정보 집합입니다. 여기에는 길이 발언, 기능 및 응용 프로그램 설정 이라는 레이블이 지정 된 스키마가 포함 됩니다.

### <a name="training-errors"></a>학습 오류

학습 오류는 레이블과 일치 하지 않는 학습 데이터의 예측입니다.

## <a name="utterance"></a>발화

[Utterance](luis-concept-utterance.md) 은 대화의 문장에 대 한 짧은 텍스트인 사용자 입력입니다. 이는 "시애틀 다음 화요일 까지의 책 2 티켓"과 같은 자연어 문구입니다. 예 길이 발언를 추가 하 여 모델을 학습 하 고, 런타임에 new utterance에서 모델을 예측 합니다.

## <a name="version"></a>버전

LUIS [버전](luis-how-to-manage-versions.md) 은 LUIS app ID 및 게시 된 끝점과 연결 된 LUIS 응용 프로그램의 특정 인스턴스입니다. 모든 LUIS 앱에는 하나 이상의 버전이 있습니다.
