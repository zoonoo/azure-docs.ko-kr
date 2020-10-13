---
title: 이미지 디렉터리 분할
titleSuffix: Azure Machine Learning
description: Azure Machine Learning designer (미리 보기)에서 이미지 분할 모듈을 사용 하 여 이미지 디렉터리의 이미지를 두 개의 고유 집합으로 나누는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: fe5ba25904298fe1a394a4b01d6bdacc72d599c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448685"
---
# <a name="split-image-directory"></a>이미지 디렉터리 분할

이 항목에서는 Azure Machine Learning 디자이너에서 이미지 분할 모듈을 사용 하 여 이미지 디렉터리의 이미지를 두 개의 고유 집합으로 나누는 방법에 대해 설명 합니다.

이 모듈은 이미지 데이터를 학습 및 테스트 집합으로 분리 해야 하는 경우에 특히 유용 합니다. 

## <a name="how-to-configure-split-image-directory"></a>분할 이미지 디렉터리를 구성 하는 방법

1. 파이프라인에 **분할 이미지 디렉터리** 모듈을 추가 합니다. ' Computer Vision/이미지 데이터 변환 ' 범주에서이 모듈을 찾을 수 있습니다.

2. 출력이 이미지 디렉터리인 모듈에 연결 합니다.

3. **첫 번째 출력에서 이미지의** 비율을 입력 하 여 왼쪽 분할에 포함할 데이터의 비율을 지정 합니다. 기본값은 0.9입니다. 분수 결과가 정수가 아닌 경우 모듈은 가까운 작은 정수를 사용 합니다.


## <a name="technical-notes"></a>기술 정보

### <a name="expected-inputs"></a>예상 입력

| Name                  | 유형           | 설명              |
| --------------------- | -------------- | ------------------------ |
| 입력 이미지 디렉터리 | ImageDirectory | 분할할 이미지 디렉터리입니다. |

### <a name="module-parameters"></a>모듈 매개 변수

| Name                                   | Type  | 범위 | 옵션 | Description                            | 기본값 |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| 첫 번째 출력에서 이미지의 비율 | Float | 0-1   | 필수 | 첫 번째 출력에서 이미지의 비율 | 0.9     |

### <a name="outputs"></a>outputs

| Name                    | 유형           | 설명                              |
| ----------------------- | -------------- | ---------------------------------------- |
| 출력 이미지 directory1 | ImageDirectory | 선택한 이미지를 포함 하는 이미지 디렉터리 |
| 출력 이미지 directory2 | ImageDirectory | 다른 모든 이미지를 포함 하는 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

