---
title: 인쇄되고 필기된 텍스트 인식 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 인쇄되고 필기된 텍스트를 인식하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313180"
---
# <a name="recognizing-printed-and-handwritten-text"></a>인쇄되고 필기된 텍스트 인식

Computer Vision은 영수증, 포스터, 명함, 편지 및 화이트보드와 같은 서로 다른 표면과 배경이 있는 다양한 사물의 이미지에서 인쇄되거나 필기된 텍스트를 검색하고 추출할 수 있습니다.

텍스트 인식 기능은 [OCR(광학 문자 인식)](concept-extracting-text-ocr.md)과 비슷하지만, OCR과는 달리 비동기적으로 실행되며 업데이트된 인식 모델을 사용합니다.

> [!NOTE]
> 이 기술은 현재 미리 보기 상태로 제공되고 있으며, 영어 텍스트에만 사용할 수 있습니다.

## <a name="text-recognition-requirements"></a>텍스트 인식 요구 사항

Computer Vision은 다음 요구 사항을 충족하는 이미지에서 인쇄하고 필기된 텍스트를 인식할 수 있습니다.

- 이미지가 JPEG, PNG 또는 BMP 형식으로 제공되어야 합니다.
- 이미지의 파일 크기가 4MB보다 작아야 합니다.
- 이미지의 크기는 50x50픽셀에서 4200x4200픽셀 사이여야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [텍스트 인식 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)를 참조하세요.