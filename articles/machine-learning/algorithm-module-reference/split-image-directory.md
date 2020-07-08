---
title: 이미지 디렉터리 분할
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 이미지 점수 매기기 모듈을 사용 하 여 학습 된 이미지 모델을 사용 하 여 예측을 생성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 9673b3260425fd9244c635beaf77d367a14cac54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809920"
---
# <a name="split-image-directory"></a>이미지 디렉터리 분할

이 항목에서는 Azure Machine Learning designer (미리 보기)에서 이미지 분할 모듈을 사용 하 여 이미지 디렉터리의 이미지를 두 개의 고유 집합으로 나누는 방법에 대해 설명 합니다.

이 모듈은 이미지 데이터를 학습 및 테스트 집합으로 분리 해야 하는 경우에 특히 유용 합니다. 

## <a name="how-to-configure-split-image-directory"></a>분할 이미지 디렉터리를 구성 하는 방법

1. 파이프라인에 **분할 이미지 디렉터리** 모듈을 추가 합니다. ' Computer Vision/이미지 데이터 변환 ' 범주에서이 모듈을 찾을 수 있습니다.

2. 출력이 이미지 디렉터리인 모듈에 연결 합니다.

3. **첫 번째 출력에서 이미지의** 비율을 입력 하 여 왼쪽 분할에 포함할 데이터의 비율을 지정 합니다. 기본값은 0.9입니다.


## <a name="technical-notes"></a>기술 정보

### <a name="expected-inputs"></a>예상 입력

| 이름                  | 유형           | 설명              |
| --------------------- | -------------- | ------------------------ |
| 입력 이미지 디렉터리 | ImageDirectory | 분할할 이미지 디렉터리입니다. |

### <a name="module-parameters"></a>모듈 매개 변수

| 이름                                   | Type  | 범위 | 옵션 | Description                            | 기본값 |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| 첫 번째 출력에서 이미지의 비율 | Float | 0-1   | 필요한 공간 | 첫 번째 출력에서 이미지의 비율 | 0.9     |

### <a name="outputs"></a>출력

| 이름                    | 유형           | 설명                              |
| ----------------------- | -------------- | ---------------------------------------- |
| 출력 이미지 directory1 | ImageDirectory | 선택한 이미지를 포함 하는 이미지 디렉터리 |
| 출력 이미지 directory2 | ImageDirectory | 다른 모든 이미지를 포함 하는 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

