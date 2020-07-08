---
title: 이미지 디렉터리로 변환
titleSuffix: Azure Machine Learning
description: 이미지 디렉터리로 변환 모듈을 사용 하 여 데이터 집합을 이미지 디렉터리 형식으로 변환 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 41724753df0d529e4c44344e8e975e68ee5eafd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84904595"
---
# <a name="convert-to-image-directory"></a>이미지 디렉터리로 변환

이 문서에서는 이미지 디렉터리로 변환 모듈을 사용 하 여 이미지 데이터 집합을 ' 이미지 디렉터리 ' 데이터 형식으로 변환 하는 방법을 설명 합니다 .이 데이터 형식은 Azure Machine Learning designer (미리 보기)의 이미지 분류와 같은 이미지 관련 작업에서 표준화 된 데이터 형식입니다.

## <a name="how-to-use-convert-to-image-directory"></a>이미지 디렉터리로 변환 사용 방법  

1.  **이미지 디렉터리로 변환** 모듈을 실험에 추가 합니다. 모듈 목록의 ' Computer Vision/이미지 데이터 변환 ' 범주에서이 모듈을 찾을 수 있습니다. 

2.  이미지 데이터 집합을 입력으로 연결 합니다. 입력 데이터 집합에 이미지가 있는지 확인 하세요.
    지원 되는 데이터 집합 형식은 다음과 같습니다.

    - 다음 확장의 압축 파일: ' .zip ', ' tar ', '. release.tar.gz ', '. bz2 '.
    - 이미지를 포함 하는 폴더입니다. **이러한 폴더를 먼저 압축 한 다음 압축 된 파일을 데이터 집합으로 사용 하는 것이 좋습니다**.

    > [!NOTE]
    > 감독 학습에서 이미지 데이터 집합을 사용 하는 경우 레이블이 필요 합니다.
    > 이미지 분류 작업의 경우이 이미지 데이터 집합이 torchvision ImageFolder 형식으로 구성 된 경우 모듈 출력에서 ' category ' 이미지로 레이블을 생성할 수 있습니다. 그렇지 않은 경우에는 이미지만 레이블 없이 저장 됩니다. 다음은 레이블을 가져오도록 이미지 데이터 집합을 구성 하는 방법의 예입니다. 이미지 범주를 하위 폴더 이름으로 사용 합니다. 자세한 내용은 [torchvision 데이터 집합](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 을 참조 하세요.
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  파이프라인을 제출합니다.

## <a name="results"></a>결과

**이미지 디렉터리로 변환** 모듈의 출력은 이미지 디렉터리 형식이 며 입력 포트 형식이 이미지 디렉터리인 다른 이미지 관련 모듈에 연결할 수 있습니다.

## <a name="technical-notes"></a>기술 정보 

###  <a name="expected-inputs"></a>예상 입력  

| 이름          | Type                  | 설명   |
| ------------- | --------------------- | ------------- |
| 입력 데이터 세트 | AnyDirectory, ZipFile | 입력 데이터 세트 |

###  <a name="output"></a>출력  

| 이름                   | Type           | 설명            |
| ---------------------- | -------------- | ---------------------- |
| 출력 이미지 디렉터리 | ImageDirectory | 출력 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
