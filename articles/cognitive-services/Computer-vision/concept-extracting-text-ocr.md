---
title: OCR을 사용하여 텍스트 추출 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하는 OCR(광학 문자 인식)을 사용하여 텍스트를 추출하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b1fc2e18dd5fc8e995c2d997683a4c5e5c501087
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188969"
---
# <a name="extracting-text-with-optical-character-recognition"></a>광학 문자 인식을 사용하여 텍스트 추출

Computer Vision의 OCR(광학 문자 인식) 기술은 이미지에서 텍스트 콘텐츠를 감지하고 식별된 텍스트를 기계로 판독 가능한 문자 스트림으로 추출합니다. 검색과 의료 기록, 보안, 뱅킹 등의 기타 다양한 용도로 결과를 사용할 수 있습니다. OCR은 언어를 자동으로 감지합니다. OCR을 사용하면 텍스트를 받아쓰는 대신 사진을 찍으면 되므로 간편하고 시간도 절약됩니다.

OCR은 25개 언어를 지원합니다. 해당 언어는 아랍어, 중국어 간체, 중국어 번체, 체코어, 덴마크어, 네덜란드어, 영어, 핀란드어, 프랑스어, 독일어, 그리스어, 헝가리어, 이탈리아어, 일본어, 한국어, 노르웨이어, 폴란드어, 포르투갈어, 루마니아어, 러시아어, 세르비아어(키릴 문자 및 라틴 문자), 슬로바키아어, 스페인어, 스웨덴어 및 터키어입니다.

필요한 경우, OCR은 가로 이미지 축을 중심으로 인식된 텍스트의 회전(도)을 수정합니다. OCR은 다음 일러스트레이션과 같이 각 단어의 프레임 좌표를 제공합니다.

![이미지가 회전되고 텍스트가 읽고 설명되고 있음을 나타내는 다이어그램](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>OCR 요구 사항

Computer Vision에서는 이미지에서 OCR을 사용하여 다음 요구 사항을 충족하는 텍스트를 추출할 수 있습니다.

* 이미지가 JPEG, PNG, GIF 또는 BMP 형식으로 제공되어야 합니다.
* 입력 이미지의 크기는 50x50 픽셀에서 4200x4200 픽셀 사이여야 합니다.


90도의 배수에 최대 40도의 작은 각도를 더한 값만큼 입력 이미지를 회전할 수 있습니다.

## <a name="improving-ocr-accuracy"></a>OCR 정확도 향상

텍스트 인식의 정확도는 이미지 품질에 따라 다릅니다. 다음과 같은 상황에서는 값을 부정확하게 읽을 수 있습니다.

* 흐린 이미지
* 필기체 또는 흘림체 텍스트
* 꾸밈 글꼴 스타일
* 작은 텍스트 크기
* 복잡한 배경, 텍스트 위의 그림자 또는 눈부심, 원근 왜곡
* 단어 시작 부분에 있는 너무 크거나 누락된 대문자
* 아래 첨자, 위 첨자 또는 취소선 텍스트

### <a name="ocr-limitations"></a>OCR 제한 사항

텍스트가 많은 사진에서는 부분적으로 인식된 단어에서 가양성이 발생할 수 있습니다. 일부 사진, 특히 텍스트가 없는 사진에서는 이미지 형식에 따라 정밀도에 큰 차이가 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[인쇄되고 필기된 텍스트 인식](concept-recognizing-text.md)에 대한 개념을 알아봅니다.
