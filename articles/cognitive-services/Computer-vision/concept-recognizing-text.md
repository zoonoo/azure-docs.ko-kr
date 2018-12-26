---
title: 인쇄되고 필기된 텍스트 인식 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 인쇄되고 필기된 텍스트를 인식하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 6827bf5f983834dc5222a3f3028386f8bbcb253a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338103"
---
# <a name="recognizing-printed-and-handwritten-text"></a>인쇄되고 필기된 텍스트 인식

Computer Vision은 영수증, 포스터, 명함, 편지 및 화이트보드와 같은 서로 다른 표면과 배경이 있는 다양한 사물의 이미지에서 인쇄되거나 필기된 텍스트를 검색하고 추출할 수 있습니다.

텍스트 인식을 통해 시간과 노력을 줄일 수 있습니다. 전사하지 않고 텍스트의 이미지를 만들어서 생산성을 높일 수 있습니다. 텍스트 인식을 통해 메모를 디지털화할 수 있습니다. 이러한 디지털화를 통해 빠르고 쉬운 검색을 구현할 수 있습니다. 또한 종이 문서를 줄여줍니다.

## <a name="text-recognition-requirements"></a>텍스트 인식 요구 사항

Computer Vision은 다음 요구 사항을 충족하는 이미지에서 인쇄하고 필기된 텍스트를 인식할 수 있습니다.

- 이미지가 JPEG, PNG 또는 BMP 형식으로 제공되어야 합니다.
- 이미지의 파일 크기가 4MB보다 작아야 합니다.
- 이미지의 크기는 50x50픽셀에서 4200x4200픽셀 사이여야 합니다.

> [!NOTE]
> 이 기술은 현재 미리 보기 상태로 제공되고 있으며, 영어 텍스트에만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[OCR을 사용하여 텍스트 추출](concept-extracting-text-ocr.md)에 대한 개념을 알아봅니다.
