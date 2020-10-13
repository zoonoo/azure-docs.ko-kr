---
title: Vowpal Wabbit 모델 학습
titleSuffix: Azure Machine Learning
description: Vowpal Wabbit의 인스턴스를 사용 하 여 기계 학습 모델을 만들기 위해 Vowpal Wabbit 모델 학습 모듈을 사용 하는 방법에 대해 알아봅니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 6bc9f69440be772910ea8200b5ccf7d5a5122ae6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907802"
---
# <a name="train-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 학습
이 문서에서는 Azure Machine Learning designer에서 **Vowpal Wabbit 모델 학습** 모듈을 사용 하 여 Vowpal Wabbit를 사용 하 여 기계 학습 모델을 만드는 방법을 설명 합니다.  

기계 학습에 Vowpal Wabbit를 사용 하려면 Vowpal Wabbit 요구 사항에 따라 입력 형식을 지정 하 고 필요한 형식으로 데이터를 준비 합니다. 이 모듈을 사용 하 여 Vowpal Wabbit 명령줄 인수를 지정 합니다. 

파이프라인이 실행 되 면 Vowpal Wabbit의 인스턴스가 지정 된 데이터와 함께 실험 런타임에 로드 됩니다. 학습을 완료 하면 모델은 작업 영역으로 다시 serialize 됩니다. 모델을 즉시 사용 하 여 데이터의 점수를 매길 수 있습니다. 

새 데이터에서 기존 모델을 점진적으로 학습 하려면 **학습 Vowpal Wabbit 모델**의 **미리 학습 된 Vowpal wabbit 모델** 입력 포트에 저장 된 모델을 연결 하 고 다른 입력 포트에 새 데이터를 추가 합니다.  

## <a name="what-is-vowpal-wabbit"></a>Vowpal Wabbit 정의  

VW (Vowpal Wabbit)는 Yahoo!에서 분산 컴퓨팅을 위해 개발 된 고속 병렬 기계 학습 프레임 워크입니다. John Langford(Microsoft Research)가 병렬 아키텍처의 과학적 계산을 위해 조정한 고속 병렬 기계 학습 프레임워크입니다.  

기계 학습에 중요 한 Vowpal Wabbit의 기능에는 지속적인 학습 (온라인 학습), 차원 감소 및 대화형 학습이 포함 됩니다. 또한 모델 데이터를 메모리에 맞출 수 없는 문제에 대해서도 Vowpal Wabbit을 사용할 수 있습니다.  

Vowpal Wabbit의 기본 사용자는 분류, 회귀, 토픽 모델링 또는 매트릭스 인수분해와 같은 기계 학습 작업에 대해 이전에 프레임 워크를 사용한 데이터 과학자입니다. Vowpal Wabbit 용 Azure 래퍼는 온-프레미스 버전과 매우 유사한 성능 특성을 제공 하므로 Vowpal Wabbit의 강력한 기능 및 기본 성능을 사용 하 고 학습 된 모델을 조작 가능한 서비스로 쉽게 게시할 수 있습니다.  

[기능 해싱](feature-hashing.md) 모듈에는 해싱 알고리즘을 사용 하 여 텍스트 데이터 집합을 이진 기능으로 변환할 수 있는 Vowpal Wabbit에서 제공 하는 기능도 포함 되어 있습니다.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Vowpal Wabbit 모델을 구성 하는 방법  

이 섹션에서는 새 모델을 학습 하는 방법 및 기존 모델에 새 데이터를 추가 하는 방법을 설명 합니다.

이 모듈은 디자이너의 다른 모듈과 달리 모듈 매개 변수를 지정 하 고 모델을 학습 합니다. 기존 모델이 있는 경우 선택적 입력으로 추가 하 여 모델을 점진적으로 학습할 수 있습니다.

+ [입력 데이터를 필요한 형식 중 하나로 준비](#prepare-the-input-data)
+ [새 모델 학습](#create-and-train-a-vowpal-wabbit-model)
+ [기존 모델을 증분식으로 학습](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>입력 데이터 준비

이 모듈을 사용 하 여 모델을 학습 하려면 입력 데이터 집합은 **SVMLight** 또는 **VW**의 두 가지 지원 되는 형식 중 하나에서 단일 텍스트 열로 구성 되어야 합니다. 이는 Vowpal Wabbit가 텍스트 데이터만 분석 하 고 필요한 텍스트 파일 형식으로 기능 및 값을 준비 해야 한다는 의미는 아닙니다.  

데이터는 두 종류의 데이터 집합, 파일 데이터 집합 또는 테이블 형식 데이터 집합에서 읽을 수 있습니다. 이러한 데이터 집합은 모두 SVMLight 또는 VW 형식 이어야 합니다. Vowpal Wabbit 데이터 형식을 사용하는 경우 열 형식이 필요하지 않아 스파스 데이터 사용 시 공간이 절약된다는 이점이 있습니다. 이 형식에 대 한 자세한 내용은 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)를 참조 하세요.  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 만들기 및 학습

1. 실험에 **Vowpal Wabbit 모델 학습** 모듈을 추가 합니다. 
  
2. 학습 데이터 집합을 추가 하 고 **학습 데이터**에 연결 합니다. 학습 데이터 집합이 학습 데이터 파일을 포함 하는 디렉터리인 경우 학습 데이터 파일 **이름을 사용 하 여 학습**데이터 파일 이름을 지정 합니다. 학습 데이터 집합이 단일 파일인 경우 **학습 데이터 파일의 이름을** 비워 둡니다.

3. **VW 인수** 텍스트 상자에 Vowpal Wabbit 실행 파일에 대 한 명령줄 인수를 입력 합니다.

     예를 들어를 추가 *`–l`* 하 여 학습 률을 지정 하거나 *`-b`* 해시 비트 수를 나타낼 수 있습니다.  

     자세한 내용은 [Vowpal Wabbit 매개 변수](#supported-and-unsupported-parameters) 섹션을 참조 하세요.  

4. **학습 데이터 파일의 이름**: 입력 데이터를 포함 하는 파일의 이름을 입력 합니다. 이 인수는 학습 데이터 집합이 디렉터리인 경우에만 사용 됩니다.

5. **파일 형식 지정**: 학습 데이터에 사용 되는 형식을 표시 합니다. Vowpal Wabbit는 다음과 같은 두 입력 파일 형식을 지원 합니다.  

    - **VW** 는 Vowpal Wabbit에서 사용 하는 내부 형식을 나타냅니다. 자세한 내용은 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) 를 참조 하세요. 
    - **SVMLight** 는 다른 기계 학습 도구에서 사용 하는 형식입니다. 

6. **출력 읽을 수 있는 모델 파일**: 모듈이 읽을 수 있는 모델을 실행 레코드에 저장 하도록 하려면이 옵션을 선택 합니다. 이 인수는 `--readable_model` VW 명령줄의 매개 변수에 해당 합니다.  

7. **반전 된 출력 해시 파일**: 모듈에서 반전 된 해싱 함수를 실행 레코드의 한 파일에 저장 하려면이 옵션을 선택 합니다. 이 인수는 `--invert_hash` VW 명령줄의 매개 변수에 해당 합니다.  

8. 파이프라인을 제출합니다.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>기존 Vowpal Wabbit 모델 다시 학습

Vowpal Wabbit는 기존 모델에 새 데이터를 추가 하 여 증분 학습을 지원 합니다. 다음 두 가지 방법으로 다시 학습을 위해 기존 모델을 가져올 수 있습니다.

+ 동일한 파이프라인에서 다른 **학습 Vowpal Wabbit 모델** 모듈의 출력을 사용 합니다.  
  
+ 디자이너의 왼쪽 탐색 창에서 **데이터 집합** 범주에 저장 된 모델을 찾아 파이프라인으로 끌어 놓습니다.  

1. **Vowpal Wabbit 모델 학습** 모듈을 파이프라인에 추가 합니다.  
2. 이전에 학습 된 모델을 모듈의 **미리 학습 된 Vowpal Wabbit 모델** 입력 포트에 연결 합니다.
3. 새 학습 데이터를 모듈의 **학습 데이터** 입력 포트에 연결 합니다.
4. **학습 Vowpal Wabbit 모델**의 매개 변수 창에서 새 학습 데이터의 형식을 지정 하 고 입력 데이터 집합이 디렉터리인 경우 학습 데이터 파일 이름도 지정 합니다.
5. 실행 레코드에 해당 파일을 저장 해야 하는 경우 * * 출력 읽을 수 있는 모델 파일 * *을 선택 하 고 **반전 된 해시 파일** 옵션을 출력 합니다.

6. 파이프라인을 제출합니다.  
7. 모듈을 선택 하 고 오른쪽 창의 **출력 + 로그** 탭에서 **데이터 집합 등록** 을 선택 하 여 Azure Machine Learning 작업 영역에서 업데이트 된 모델을 유지 합니다.  새 이름을 지정 하지 않은 경우 업데이트 된 모델은 기존에 저장 된 모델을 덮어씁니다.

## <a name="technical-notes"></a>기술 정보

이 섹션에는 구현 세부 정보, 팁 및 질문과 대답 (faq)이 포함 되어 있습니다.

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit의 장점

Vowpal Wabbit은 n그램 등의 비선형 기능에 비해 학습 속도가 매우 빠릅니다.  

Vowpal Wabbit은 SGD(추측 기울기 하강) 등의 *온라인 학습* 기술을 사용하여 한 번에 레코드 하나씩 모델을 맞춥니다. 따라서 원시 데이터에 대해 매우 빠르게 반복되며, 대부분의 다른 모델에 비해 적절한 예측자를 더 빠르게 개발할 수 있습니다. 또한 이 방식에서는 모든 학습 데이터를 메모리로 읽어 들이지 않아도 됩니다.  

Vowpal Wabbit은 모든 데이터, 즉 텍스트 데이터뿐 아니라 다른 범주 변수도 해시로 변환합니다. 해시를 사용 하면 회귀 가중치를 보다 효율적으로 조회할 수 있습니다 .이는 효과적인 추계 그라데이션 디센더에 매우 중요 합니다.  

###  <a name="supported-and-unsupported-parameters"></a>지원 되거나 지원 되지 않는 매개 변수 

이 섹션에서는 Azure Machine Learning 디자이너의 Vowpal Wabbit 명령줄 매개 변수에 대 한 지원에 대해 설명 합니다. 

일반적으로 제한 된 인수 집합만 지원 합니다. 인수의 전체 목록을 보려면 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)를 사용 합니다.    

다음 매개 변수는 지원 되지 않습니다.

-   에 지정 된 입력/출력 옵션 [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     이러한 속성은 이미 모듈에 의해 자동으로 구성 됩니다.  
  
-   또한 여러 출력을 생성 하거나 여러 입력을 사용 하는 옵션은 허용 되지 않습니다. 여기에는, 및가 포함 됩니다 *`--cbt`* *`--lda`* *`--wap`* .  
  
-   감독 된 학습 알고리즘만 지원 됩니다. 따라서, 등의 옵션은 지원 되지 *`–active`* 않습니다 `--rank` . *`--search`* 

### <a name="restrictions"></a>제한

서비스의 목표가 숙련 된 Vowpal Wabbit 사용자를 지원 하기 때문에 입력 데이터는 다른 모듈에서 사용 하는 데이터 집합 형식이 아니라 Vowpal Wabbit 네이티브 텍스트 형식을 사용 하 여 미리 준비 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
