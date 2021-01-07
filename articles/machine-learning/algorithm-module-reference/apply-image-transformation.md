---
title: 이미지 변환 적용
titleSuffix: Azure Machine Learning
description: 이미지 변환 적용 모듈을 사용 하 여 이미지 디렉터리에 이미지 변환을 적용 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898915"
---
# <a name="apply-image-transformation"></a>이미지 변환 적용 

이 문서에서는 Azure Machine Learning designer에서 이미지 변환 적용 모듈을 사용 하 여 이전에 지정 된 이미지 변환을 기반으로 입력 이미지 디렉터리를 수정 하는 방법을 설명 합니다.  

[초기화 이미지](init-image-transformation.md) 변환 모듈을 연결 하 여 변환을 지정 하 고 나 서 이미지 변환 적용 모듈의 입력 이미지 디렉터리에 이러한 변환을 적용할 수 있습니다.

## <a name="how-to-use-apply-image-transformation"></a>이미지 적용 변환 사용 방법  

1. 파이프라인에 **이미지 변환 적용** 모듈을 추가 합니다. *Computer Vision/이미지 데이터 변환* 범주에서이 모듈을 찾을 수 있습니다. 

2. **Init 이미지 변환** 의 출력을 **이미지 적용 변환**의 왼쪽 입력에 연결 합니다.

     > [!NOTE]
     > [초기화 이미지 변환](init-image-transformation.md) 모듈에 의해 생성 된 이미지 변환만이 모듈에 적용 됩니다. 다른 종류의 변환의 경우 [변환을 적용](apply-transformation.md)하려면 연결 하세요. 그렇지 않으면 ' InvalidTransformationDirectoryError '이 throw 됩니다.


3. 변환 하려는 이미지 디렉터리를 연결 합니다.

4. **모드**의 경우 입력 변환을 사용 하는 용도에 대해 ' 학습 ' 또는 ' 유추에 대해 '를 지정 합니다. 

   **학습을 위해**선택 하는 경우 초기화 이미지 변환에서 지정한 모든 변환이 적용 됩니다.

   **유추를 위해**를 선택 하는 경우 새 샘플을 만드는 것과 같은 변환이 적용 되기 전에 제외 됩니다. 그 이유는 ' 임의의 수평 대칭 이동 '과 같이 새 샘플을 임의로 만드는 변환 작업이 학습에서 데이터 확대에 사용 되기 때문입니다. 정확한 예측 및 평가를 위해 유추 샘플을 수정 해야 하기 때문에 유추에서 제거 해야 합니다.

   > [!NOTE]
   > **유추** 모드에서 제외 되는 변환은 임의의 크기 조정 된 자르기, 임의 자르기, 임의 가로 대칭 이동, 무작위 수직 대칭 이동, 임의 회전, 임의 관계, 임의 회색조, 무작위 원근, 임의 지우기입니다.

5. 새 이미지 디렉터리에 이미지 변환을 적용 하려면 파이프라인을 제출 합니다.  

### <a name="module-parameters"></a>모듈 매개 변수

| Name | 범위 | Type | 기본값                   | Description                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | 모두   | 모드 | (사용자가 지정 해야 함) | 입력 변환을 사용 하는 용도 ' 임의 ' 변환 작업을 유추에서 제외 하 고 학습에 유지 해야 합니다. |

### <a name="expected-inputs"></a>예상 입력  

| Name                       | Type                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| 입력 이미지 변환 | TransformationDirectory | 입력 이미지 변환        |
| 입력 이미지 디렉터리      | ImageDirectory          | 변형할 이미지 디렉터리입니다. |

### <a name="outputs"></a>outputs  

| Name                   | Type           | Description            |
| ---------------------- | -------------- | ---------------------- |
| 출력 이미지 디렉터리 | ImageDirectory | 출력 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
