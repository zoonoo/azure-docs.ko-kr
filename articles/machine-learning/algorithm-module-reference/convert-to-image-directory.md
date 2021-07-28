---
title: 이미지 디렉터리로 변환
titleSuffix: Azure Machine Learning
description: 이미지 디렉터리로 변환 모듈을 사용하여 데이터 세트를 이미지 디렉터리 형식으로 변환하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 2bf30e636fc1bf1031f6c379a998979d92909b81
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890495"
---
# <a name="convert-to-image-directory"></a>이미지 디렉터리로 변환

이 문서에서는 이미지 디렉터리로 변환 모듈을 사용하여 이미지 데이터 세트를 Azure Machine Learning 디자이너의 이미지 분류와 같은 이미지 관련 작업에서 표준화된 데이터 형식인 *이미지 디렉터리* 데이터 형식으로 변환하는 방법을 설명합니다.

## <a name="how-to-use-convert-to-image-directory"></a>이미지 디렉터리로 변환 사용 방법  

1. 먼저 이미지 데이터 세트를 준비합니다. 

    감독된 학습의 경우 학습 데이터 세트의 레이블을 지정해야 합니다. 이미지 데이터 세트 파일의 구조는 다음과 같아야 합니다.
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    이미지 데이터 세트 폴더에는 여러 하위 폴더가 있습니다. 각 하위 폴더에는 각각 하나의 범주에 대한 이미지가 포함됩니다. 하위 폴더의 이름은 이미지 분류와 같은 작업에 대한 레이블로 간주됩니다. 자세한 내용은 [torchvision 데이터 세트](https://pytorch.org/vision/stable/datasets.html#imagefolder)를 참조하세요.

    > [!WARNING]
    > 현재 데이터 레이블에서 내보낸 레이블이 지정된 데이터 세트는 디자이너에서 지원되지 않습니다.

    '.jpg', '.jpeg', '.png', '.ppm', '.bmp', '.pgm', '.tif', '.tiff', '.webp'와 같은 확장명(소문자로 표시됨)이 있는 이미지가 지원됩니다. 또한 한 폴더에 여러 유형의 이미지가 있을 수 있습니다. 각 범주 폴더에 동일한 수의 이미지를 포함할 필요는 없습니다.

    확장명이 '.zip', '.tar', '.gz' 및 '.bz2'인 압축된 파일 또는 폴더를 사용할 수 있습니다. **더 나은 성능을 위해서는 압축 파일을 사용하는 것이 좋습니다.** 
    
    ![이미지 샘플 데이터 세트](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > 유추의 경우 이미지 데이터 세트 폴더에는 미분류 이미지만 포함되어야 합니다.

1. 이미지 디렉터리로 변환 모듈의 입력은 **파일 데이터 세트** 여야 하므로 작업 영역에 [이미지 데이터 세트를 파일 데이터 세트로 등록](../how-to-create-register-datasets.md)합니다.

1. 등록된 이미지 데이터 세트를 캔버스에 추가합니다. 캔버스 왼쪽에 있는 모듈 목록의 **데이터 세트** 카테고리에서 등록된 데이터 세트를 찾을 수 있습니다. 현재 디자이너는 이미지 데이터 세트 시각화를 지원하지 않습니다.

    > [!WARNING]
    > **데이터 가져오기** 모듈의 출력 유형이 파일 경로 문자열만 포함하는 DataFrame Directory이기 때문에 **데이터 가져오기** 모듈을 사용하여 이미지 데이터 세트를 가져올 수 **없습니다**.

1. 캔버스에 **이미지 디렉터리로 변환** 모듈을 추가합니다. 모듈 목록의 'Computer Vision/이미지 데이터 변환' 범주에서 이 모듈을 찾을 수 있습니다. 이미지 데이터 세트에 연결합니다.
    
3.  파이프라인을 제출합니다. 이 모듈은 GPU 또는 CPU에서 실행할 수 있습니다.

## <a name="results"></a>결과

**이미지 디렉터리로 변환** 모듈의 출력은 **이미지 디렉터리** 형식이며 입력 포트 형식이 이미지 디렉터리인 다른 이미지 관련 모듈에 연결할 수 있습니다.

![이미지 디렉터리로 변환 출력](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>기술 정보 

###  <a name="expected-inputs"></a>예상 입력  

| Name          | 유형                  | Description   |
| ------------- | --------------------- | ------------- |
| 입력 데이터 세트 | AnyDirectory, ZipFile | 입력 데이터 세트 |

###  <a name="output"></a>출력  

| Name                   | 유형           | Description            |
| ---------------------- | -------------- | ---------------------- |
| 이미지 디렉터리 출력 | ImageDirectory | 이미지 디렉터리 출력 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요.