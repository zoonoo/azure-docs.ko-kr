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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450116"
---
# <a name="convert-to-image-directory"></a>이미지 디렉터리로 변환

이 문서에서는 이미지 디렉터리로 변환 모듈을 사용 하 여 이미지 데이터 집합을 Azure Machine Learning 디자이너의 이미지 분류와 같은 이미지 관련 작업에서 표준화 된 데이터 형식으로 변환 하는 데 도움이 되는 방법을 설명 합니다.

## <a name="how-to-use-convert-to-image-directory"></a>이미지 디렉터리로 변환 사용 방법  

1.  **이미지 디렉터리로 변환** 모듈을 캔버스에 추가 합니다. 모듈 목록의 ' Computer Vision/이미지 데이터 변환 ' 범주에서이 모듈을 찾을 수 있습니다. 

2.  **이미지 디렉터리로 변환** 모듈의 입력은 파일 데이터 집합 이어야 합니다. [이미지 데이터 집합을 등록](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) 하 고 모듈 입력 포트에 연결 합니다. 입력 데이터 집합에 이미지가 있는지 확인 하세요. 현재 디자이너는 이미지 데이터 집합 시각화를 지원 하지 않습니다.
 
    지원 되는 데이터 집합 형식은 다음과 같습니다.

    - 다음 확장의 압축 파일: ' .zip ', ' tar ', '. release.tar.gz ', '. bz2 '.
    - 이미지를 포함 하는 폴더입니다. **이러한 폴더를 먼저 압축 한 다음 압축 된 파일을 데이터 집합으로 사용 하는 것이 좋습니다**.

    > [!WARNING]
    > 데이터 **가져오기** 모듈의 출력 유형이 파일 경로 문자열만 포함 하는 데이터 프레임 Directory 이므로 **데이터 가져오기** 모듈을 사용 하 여 이미지 데이터 집합을 가져올 **수 없습니다** .
    

    > [!NOTE]
    > - 감독 된 학습에서 이미지 데이터 집합을 사용 하는 경우 학습 데이터 집합의 레이블을 지정 해야 합니다.
    > - 이미지 분류 작업의 경우이 이미지 데이터 집합이 torchvision ImageFolder 형식으로 구성 된 경우 모듈 출력에서 ' category ' 이미지로 레이블을 생성할 수 있습니다. 그렇지 않은 경우에는 이미지만 레이블 없이 저장 됩니다. 다음은 레이블을 가져오도록 이미지 데이터 집합을 구성 하는 방법의 예입니다. 이미지 범주를 하위 폴더 이름으로 사용 합니다. 
    > - 각 범주 폴더에서 동일한 수의 이미지를 업로드할 필요가 없습니다.
    > - 이러한 확장 (소문자)의 이미지가 지원 됩니다. ' .jpg ', ' .jpeg ', ' .png ', '. ppm ', ' .bmp ', '. r u n ', ' .tif ', ' tiff ', '. webp '. 또한 한 폴더에 여러 유형의 이미지가 있을 수 있습니다.    
    > - 자세한 내용은 [torchvision 데이터 집합](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 을 참조 하세요.
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - 점수 매기기에 이미지 데이터 집합을 사용 하는 경우이 모듈의 입력 파일 데이터 집합은 분류 되지 않은 이미지를 포함 해야 합니다.
    
3.  파이프라인을 제출합니다. 이 모듈은 GPU 또는 CPU에서 실행할 수 있습니다.

## <a name="results"></a>결과

**이미지 디렉터리로 변환** 모듈의 출력은 이미지 디렉터리 형식이 며 입력 포트 형식이 이미지 디렉터리인 다른 이미지 관련 모듈에 연결할 수 있습니다.

## <a name="technical-notes"></a>기술 정보 

###  <a name="expected-inputs"></a>예상 입력  

| 속성          | 유형                  | 설명   |
| ------------- | --------------------- | ------------- |
| 입력 데이터 세트 | AnyDirectory, ZipFile | 입력 데이터 세트 |

###  <a name="output"></a>출력  

| 속성                   | 유형           | 설명            |
| ---------------------- | -------------- | ---------------------- |
| 출력 이미지 디렉터리 | ImageDirectory | 출력 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
