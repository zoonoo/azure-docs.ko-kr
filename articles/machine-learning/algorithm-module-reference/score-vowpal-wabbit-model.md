---
title: Vowpal Wabbit 모델 점수 매기기
titleSuffix: Azure Machine Learning
description: Vowpal Wabbit 모델 점수 매기기 모듈을 사용 하 여 기존의 학습 된 Vowpal Wabbit 모델을 통해 입력 데이터 집합에 대 한 점수를 생성 하는 방법에 대해 알아봅니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 02f4fe4f97d3e976675757835e3931666d1c6410
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857742"
---
# <a name="score-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 점수 매기기
이 문서에서는 기존 학습 된 Vowpal Wabbit 모델을 사용 하 여 Azure Machine Learning 디자이너 (미리 보기)에서 **Vowpal Wabbit 모델 점수 매기기** 모듈을 사용 하 여 입력 데이터 집합에 대 한 점수를 생성 하는 방법을 설명 합니다.  

이 모듈은 Vowpal Wabbit 프레임 워크 버전 8.8.1의 최신 버전을 제공 합니다. 이 모듈을 사용 하 여 VW 버전 8 형식으로 저장 된 학습 된 모델을 사용 하 여 데이터의 점수를 매길 수 있습니다.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 점수를 구성 하는 방법

1.  **Vowpal Wabbit 모델 점수 매기기** 모듈을 실험에 추가 합니다.  
  
2.  학습 된 Vowpal Wabbit 모델을 추가 하 고 왼쪽 입력 포트에 연결 합니다. 동일한 실험에서 만든 학습 된 모델을 사용 하거나 디자이너의 왼쪽 탐색 창에서 **데이터 집합** 범주에 저장 된 모델을 찾을 수 있습니다. 그러나 모델은 Azure Machine Learning 디자이너에서 사용할 수 있어야 합니다.  
  
    > [!NOTE]
    > Vowpal Wabbit 8.8.1 모델만 지원 됩니다. 다른 알고리즘을 사용 하 여 학습 된 저장 된 모델은 연결할 수 없습니다.
  
3.  테스트 데이터 집합을 추가 하 고 오른쪽 입력 포트에 연결 합니다. 테스트 데이터 집합이 테스트 데이터 파일을 포함 하는 디렉터리인 경우 테스트 데이터 **파일 이름으로**테스트 데이터 파일 이름을 지정 합니다. 테스트 데이터 집합이 단일 파일이 면 **테스트 데이터 파일의 이름을** 비워 둡니다.

4. **VW 인수** 텍스트 상자에 Vowpal Wabbit 실행 파일에 대 한 올바른 명령줄 인수 집합을 입력 합니다.  

    Azure Machine Learning에서 지원 되 고 지원 되지 않는 Vowpal Wabbit 인수에 대 한 자세한 내용은 [기술 참고 사항](#technical-notes) 섹션을 참조 하세요.  

5.  **테스트 데이터 파일의 이름**: 입력 데이터를 포함 하는 파일의 이름을 입력 합니다. 이 인수는 테스트 데이터 집합이 디렉터리인 경우에만 사용 됩니다.

6. **파일 형식 지정**: 학습 데이터에 사용 되는 형식을 표시 합니다. Vowpal Wabbit는 다음과 같은 두 입력 파일 형식을 지원 합니다.  

   - **VW** 는 Vowpal Wabbit에서 사용 하는 내부 형식을 나타냅니다. 자세한 내용은 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) 를 참조 하세요. 
   - **SVMLight** 는 다른 기계 학습 도구에서 사용 하는 형식입니다. 

7. 점수와 함께 레이블을 출력 하려면 **레이블을 포함 하는 추가 열을 포함**하는 옵션을 선택 합니다.  

   일반적으로 텍스트 데이터를 처리할 때 Vowpal Wabbit는 레이블이 필요 하지 않으며 데이터의 각 행에 대 한 점수를 반환 합니다.  

8. 결과와 함께 원시 점수를 출력 하려면 **원시 점수를 포함 하는 추가 열을 포함**하는 옵션을 선택 합니다.  

9. 파이프라인을 제출합니다.

## <a name="results"></a>결과

학습 완료 후:

+ 결과를 시각화 하려면 [Vowpal Wabbit 모델 점수 매기기](score-vowpal-wabbit-model.md) 모듈의 출력을 마우스 오른쪽 단추로 클릭 합니다. 출력은 0에서 1 사이의 예측 점수를 나타냅니다. 

+ 결과를 평가 하려면 출력 데이터 집합에 모델 모듈 요구 사항 평가를 충족 하는 특정 점수 열 이름이 포함 되어야 합니다.

  + 회귀 태스크의 경우 평가할 데이터 집합에는 `Regression Scored Labels` 점수가 매겨진 레이블을 나타내는 라는 하나의 열이 있어야 합니다.
  + 이진 분류 태스크의 경우 평가할 데이터 집합에는 `Binary Class Scored Labels` `Binary Class Scored Probabilities` 각각 점수가 매겨진 레이블 및 확률을 나타내는 라는 두 개의 열이 있어야 합니다.
  + 다중 분류 태스크의 경우 평가할 데이터 집합에는 `Multi Class Scored Labels` 점수가 매겨진 레이블을 나타내는 라는 하나의 열이 있어야 합니다.

  점수 Vowpal Wabbit 모델 모듈의 결과는 직접 평가할 수 없습니다. 평가 하기 전에 위의 요구 사항에 따라 데이터 집합을 수정 해야 합니다.

##  <a name="technical-notes"></a>기술 정보

이 섹션에는 구현 세부 정보, 팁 및 질문과 대답 (faq)이 포함 되어 있습니다.

### <a name="parameters"></a>매개 변수

Vowpal Wabbit에는 알고리즘을 선택 하 고 조정 하는 데 사용할 수 있는 많은 명령줄 옵션이 있습니다. 이러한 옵션에 대 한 자세한 설명은 여기에서 사용할 수 없습니다. [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)를 보는 것이 좋습니다.  

다음 매개 변수는 Azure Machine Learning Studio (클래식)에서 지원 되지 않습니다.  

-   에 지정 된 입력/출력 옵션[https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     이러한 속성은 이미 모듈에 의해 자동으로 구성 됩니다.  
  
-   또한 여러 출력을 생성 하거나 여러 입력을 사용 하는 옵션은 허용 되지 않습니다. 여기에는, 및가 포함 됩니다 *`--cbt`* *`--lda`* *`--wap`* .  
  
-   감독 된 학습 알고리즘만 지원 됩니다. 이렇게 하면, 등의 옵션이 허용 되지 *`–active`* `--rank` *`--search`* 않습니다.  

위에 설명 된 인수 이외의 모든 인수를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 