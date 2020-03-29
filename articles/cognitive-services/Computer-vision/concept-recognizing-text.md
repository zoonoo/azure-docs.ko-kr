---
title: 인쇄, 필기 텍스트 인식 - 컴퓨터 비전
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 인쇄되고 필기된 텍스트를 인식하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221360"
---
# <a name="recognize-printed-and-handwritten-text"></a>인쇄되고 필기된 텍스트 인식

Computer Vision은 이미지에 나타나는 인쇄된 텍스트 또는 필기 텍스트를 감지하고 추출하는 다양한 서비스를 제공합니다. 이 기능은 메모 작성, 의료 기록, 보안 및 뱅킹과 같은 다양한 시나리오에서 유용합니다. 다음 세 섹션에서는 서로 다른 사용 사례에 최적화된 세 가지 텍스트 인식 API를 자세히 설명합니다.

## <a name="read-api"></a>API 읽기

Read API는 최신 인식 모델을 사용하여 이미지의 텍스트 콘텐츠를 감지하고 식별된 텍스트를 기계가 읽을 수 있는 문자 스트림으로 변환합니다. 텍스트가 많은 이미지(예: 디지털 로 스캔한 문서)와 시각적 노이즈가 많은 이미지에 최적화되어 있습니다. 각 텍스트 줄에 사용할 인식 모델을 결정하여 인쇄된 텍스트와 필기 텍스트로 이미지를 지원합니다. 읽기 API는 큰 문서가 결과를 반환하는 데 몇 분 정도 걸릴 수 있으므로 비동기적으로 실행됩니다.

Read 작업은 출력에서 인식된 단어의 원래 줄 그룹을 유지 관리합니다. 각 줄에는 경계 상자 좌표가 함께 제공되며 줄 내의 각 단어에는 고유한 좌표도 있습니다. 신뢰도가 낮은 단어를 인식한 경우 해당 정보도 전달됩니다. 자세한 내용은 [API 참조 문서 읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) 및 API 미리 보기 참조 문서 [읽기를](https://go.microsoft.com/fwlink/?linkid=2118322) 참조하십시오.

> [!NOTE]
> 이 기능은 영어및스페인어(미리 보기) 텍스트에서만 사용할 수 있습니다.

### <a name="image-requirements"></a>이미지 요구 사항

읽기 API는 다음 요구 사항을 충족하는 이미지와 함께 작동합니다.

- 이미지는 JPEG, PNG, BMP, PDF 또는 TIFF 형식으로 표시되어야 합니다.
- 이미지의 크기는 50 x 50에서 10000 x 10000 픽셀 사이여야 합니다. PDF 페이지는 17 x 17인치 이상이어야 합니다.
- 이미지의 파일 크기는 MB(20MB) 미만이어야 합니다.

### <a name="limitations"></a>제한 사항

프리 티어 구독을 사용하는 경우 읽기 API는 PDF 또는 TIFF 문서의 처음 두 페이지만 처리합니다. 유료 구독을 사용하면 최대 200페이지까지 처리됩니다. 또한 API는 페이지당 최대 300개의 줄을 감지합니다.

## <a name="ocr-optical-character-recognition-api"></a>OCR(광학 문자 인식) API

컴퓨터 비전의 광학 문자 인식(OCR) API는 Read API와 유사하지만 동기적으로 실행되며 대규모 문서에 최적화되지 않습니다. 이전 인식 모델을 사용하지만 더 많은 언어에서 작동합니다. 지원되는 [언어의](language-support.md#text-recognition) 전체 목록은 언어 지원을 참조하십시오.

필요한 경우, OCR은 가로 이미지 축을 중심으로 회전 오프셋(도)을 반환하여 인식된 텍스트의 회전을 정정합니다. OCR은 다음 그림에서 볼 수 있듯이 각 단어의 프레임 좌표도 제공합니다.

![회전중인 이미지와 해당 텍스트를 읽고 설명하는 이미지](./Images/vision-overview-ocr.png)

자세한 내용은 [OCR 참조 문서를](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 참조하십시오.

### <a name="image-requirements"></a>이미지 요구 사항

OCR API는 다음 요구 사항을 충족하는 이미지에서 작동합니다.

* 이미지는 JPEG, PNG, GIF 또는 BMP 형식으로 표시되어야 합니다.
* 입력 이미지의 크기는 50 x 50에서 4200 x 4200 픽셀 사이여야 합니다.
* 90도의 배수에 최대 40도의 작은 각도를 더한 값만큼 이미지의 텍스트를 회전할 수 있습니다.

### <a name="limitations"></a>제한 사항

텍스트가 많은 사진에서는 부분적으로 인식된 단어에서 가양성이 발생할 수 있습니다. 일부 사진, 특히 텍스트가없는 사진의 경우 이미지 유형에 따라 정밀도가 다를 수 있습니다.

## <a name="recognize-text-api"></a>텍스트 API 인식

> [!NOTE]
> 텍스트 인식 API는 읽기 API를 위해 더 이상 사용되지 않습니다. 읽기 API는 유사한 기능을 가지며 PDF, TIFF 및 다중 페이지 파일을 처리하도록 업데이트됩니다.

텍스트 인식 API는 OCR과 유사하지만 비동기적으로 실행되고 업데이트된 인식 모델을 사용합니다. 자세한 내용은 [텍스트 API 참조 문서 인식을](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 참조하세요.

### <a name="image-requirements"></a>이미지 요구 사항

텍스트 인식 API는 다음 요구 사항을 충족하는 이미지와 함께 작동합니다.

- 이미지는 JPEG, PNG 또는 BMP 형식으로 표시되어야 합니다.
- 이미지의 크기는 50 x 50에서 4200 x 4200 픽셀 사이여야 합니다.
- 이미지의 파일 크기는 MB(4MB) 미만이어야 합니다.

## <a name="limitations"></a>제한 사항

텍스트 인식 작업의 정확도는 이미지의 품질에 따라 다릅니다. 다음과 같은 요인으로 인해 부정확한 판독이 발생할 수 있습니다.

* 흐린 이미지
* 필기체 또는 흘림체 텍스트
* 꾸밈 글꼴 스타일
* 작은 텍스트 크기
* 복잡한 배경, 텍스트 위의 그림자 또는 눈부심, 원근 왜곡
* 단어의 시작 부분에 큰 또는 누락 된 대문자입니다.
* 아래 첨자, 위 첨자 또는 취소선 텍스트

## <a name="next-steps"></a>다음 단계

간단한 C# 앱에서 텍스트 인식을 구현하려면 [텍스트 추출(읽기)](./QuickStarts/CSharp-hand-text.md) 퀵스타트를 따릅니다.
