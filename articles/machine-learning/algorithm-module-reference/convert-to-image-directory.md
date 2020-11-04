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
ms.date: 10/09/2020
ms.openlocfilehash: 8abcbc74506599e7896ad6d41b9444e946172283
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324903"
---
# <a name="convert-to-image-directory"></a>이미지 디렉터리로 변환

이 문서에서는 이미지 디렉터리로 변환 모듈을 사용 하 여 이미지 데이터 집합을 이미지 *디렉터리* 데이터 형식으로 변환 하는 방법에 대해 설명 합니다 .이 데이터 형식은 Azure Machine Learning 디자이너의 이미지 분류와 같은 이미지 관련 작업에서 표준화 된 데이터 형식입니다.

## <a name="how-to-use-convert-to-image-directory"></a>이미지 디렉터리로 변환 사용 방법  

1. 먼저 이미지 데이터 집합을 준비 합니다. 

    감독 된 학습의 경우 학습 데이터 집합의 레이블을 지정 해야 합니다. 이미지 데이터 집합 파일의 구조는 다음과 같아야 합니다.
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    이미지 데이터 집합 폴더에는 여러 하위 폴더가 있습니다. 각 하위 폴더에는 각각 하나의 범주에 대 한 이미지가 포함 됩니다. 하위 폴더의 이름은 이미지 분류와 같은 작업에 대 한 레이블로 간주 됩니다. 자세한 내용은 [torchvision 데이터 집합](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 을 참조 하세요.

    > [!WARNING]
    > 현재 데이터 레이블에서 내보낸 레이블이 지정 된 데이터 집합은 디자이너에서 지원 되지 않습니다.

    이러한 확장 (소문자)의 이미지가 지원 됩니다. ' .jpg ', ' .jpeg ', ' .png ', '. ppm ', ' .bmp ', '. r u n ', ' .tif ', ' tiff ', '. webp '. 또한 한 폴더에 여러 유형의 이미지가 있을 수 있습니다. 각 범주 폴더에 동일한 수의 이미지를 포함할 필요는 없습니다.

    확장명이 ' .zip ', ' tar ', '. release.tar.gz ' 및 '. bz2 ' 인 폴더 또는 압축 된 파일을 사용할 수 있습니다. **압축 된 파일은 성능 향상을 위해 권장 됩니다.** 
    
    ![이미지 샘플 데이터 집합](./media/module/image-sample-dataset.png)

    점수 매기기를 위해 이미지 데이터 집합 폴더에는 미분류 이미지만 포함 되어야 합니다.

1. 이미지 디렉터리로 변환 모듈의 입력이 **파일 데이터 집합** 이어야 하므로 이미지 데이터 집합을 작업 영역에서 [파일 데이터 집합으로 등록](../how-to-create-register-datasets.md) 합니다.

1. 등록 된 이미지 데이터 집합을 캔버스에 추가 합니다. 캔버스 왼쪽에 있는 모듈 목록의 **데이터 집합** 범주에서 등록 된 데이터 집합을 찾을 수 있습니다. 현재 디자이너는 이미지 데이터 집합 시각화를 지원 하지 않습니다.

    > [!WARNING]
    > 데이터 **가져오기** 모듈의 출력 유형이 파일 경로 문자열만 포함 하는 데이터 프레임 Directory 이므로 **데이터 가져오기** 모듈을 사용 하 여 이미지 데이터 집합을 가져올 **수 없습니다** .

1. **이미지 디렉터리로 변환** 모듈을 캔버스에 추가 합니다. 모듈 목록의 ' Computer Vision/이미지 데이터 변환 ' 범주에서이 모듈을 찾을 수 있습니다. 이미지 데이터 집합에 연결 합니다.
    
3.  파이프라인을 제출합니다. 이 모듈은 GPU 또는 CPU에서 실행할 수 있습니다.

## <a name="results"></a>결과

**이미지 디렉터리로 변환** 모듈의 출력은 **이미지** 디렉터리 형식이 며 입력 포트 형식이 이미지 디렉터리인 다른 이미지 관련 모듈에 연결할 수 있습니다.

![이미지 디렉터리로 변환 출력](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>기술 정보 

###  <a name="expected-inputs"></a>예상 입력  

| 이름          | 유형                  | 설명   |
| ------------- | --------------------- | ------------- |
| 입력 데이터 세트 | AnyDirectory, ZipFile | 입력 데이터 세트 |

###  <a name="output"></a>출력  

| 이름                   | 유형           | 설명            |
| ---------------------- | -------------- | ---------------------- |
| 출력 이미지 디렉터리 | ImageDirectory | 출력 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요.