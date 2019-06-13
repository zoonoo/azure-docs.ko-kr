---
title: 인쇄/필기 텍스트 인식, Computer Vision
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
ms.openlocfilehash: bcaa990cc2186a5f1eecdbbca91804c92370277c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357193"
---
# <a name="recognize-printed-and-handwritten-text"></a>인쇄, 필기된 텍스트 인식

Computer Vision는 이미지에 표시되는 인쇄 또는 필기 텍스트를 감지하고 추출하는 여러 서비스를 제공합니다. 이는 노트와 의료 기록, 보안, 뱅킹과 같은 다양한 시나리오에서 유용합니다. 다음 세 섹션에서는 각각 다른 사용 사례에 최적화된 세 가지 텍스트 인식 API를 상세히 설명합니다.

## <a name="read-api"></a>읽기 API

읽기 API는 최신 인식 모델을 사용하여 이미지에서 텍스트 콘텐츠를 감지하고 컴퓨터가 읽을 수 있는 문자 스트림으로 식별된 텍스트를 변환합니다. 시각적 노이즈가 많은 이미지 및 텍스트가 많은 이미지(예: 디지털 스캔된 문서)에 최적화되어 있습니다. 인쇄 및 필기된 텍스트를 사용하여 이미지를 지원하는 각 텍스트 행에 사용할 인식 모델을 결정합니다. 큰 문서는 결과를 반환하는데 몇 분 정도 걸릴 수 있으므로 읽기 API는 비동기적으로 실행됩니다.

읽기 작업은 출력에 인식된 단어의 원래 줄 그룹 유지합니다. 각 줄에는 경계 상자 좌표가 있고 줄 안의 각 단어에도 자체 좌표가 있습니다. 낮은 신뢰도로 단어가 인식된 경우 해당 정보도 전달됩니다. 자세히 알아보려면 [읽기 API 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)를 확인합니다.

> [!NOTE]
> 이 기능은 영어 텍스트에 사용할 수만 있습니다.

### <a name="image-requirements"></a>이미지 요구 사항

읽기 API는 다음 요구 사항을 충족하는 이미지를 사용하여 작동합니다.

- 이미지를 JPEG, PNG, BMP, PDF 또는 TIFF 형식으로 제공 해야 합니다.
- 이미지의 크기는 50 x 50와 4200 x 4200 픽셀 사이여야 합니다. PDF 페이지는 17 x 17 인치이거나 더 작아야 합니다.
- 이미지의 파일 크기는 20메가바이트(MB) 미만이어야 합니다.

### <a name="limitations"></a>제한 사항

무료 계층 구독을 사용하는 경우 읽기 API는 PDF 또는 TIFF 문서의 처음 두 페이지만 처리합니다. 유료 구독은 최대 200 페이지까지 처리합니다. API는 페이지당 최대 300줄까지 감지한다는 점을 유념합니다.

## <a name="ocr-optical-character-recognition-api"></a>OCR(광학 문자 인식) API

Computer Vision의 OCR(광학 문자 인식) API는 읽기 API와 유사하지만 동기적으로 실행되고 큰 문서에 대해 최적화되지 않습니다. 이전 인식 모델을 사용하지만 더 많은 언어를 인식합니다. 지원되는 언어의 전체 목록에 대해서는 [언어 지원](language-support.md#text-recognition)을 확인합니다.

필요한 경우, OCR은 가로 이미지 축을 중심으로 회전 오프셋(도)을 반환하여 인식된 텍스트의 회전을 정정합니다. OCR는 또한 다음 그림에 표시된 대로 각 단어의 프레임 좌표를 제공합니다.

![회전할 이미지 및 읽고 구분 된 텍스트](./Images/vision-overview-ocr.png)

자세히 알아보려면 [OCR 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)를 확인합니다.

### <a name="image-requirements"></a>이미지 요구 사항

OCR API는 다음 요구 사항을 충족 하는 이미지에서 작동 합니다.

* 이미지를 JPEG, PNG, GIF 또는 BMP 형식으로 제공 해야 합니다.
* 입력된 이미지의 크기는 50 x 50와 4200 x 4200 픽셀 사이여야 합니다.
* 이미지의 크기는 50 x 50와 4200 x 4200 픽셀 사이여야 합니다.
* 90도의 배수에 최대 40도의 작은 각도를 더한 값만큼 이미지의 텍스트를 회전할 수 있습니다.

### <a name="limitations"></a>제한 사항

텍스트가 많은 사진에서는 부분적으로 인식된 단어에서 오탐이 발생할 수 있습니다. 일부 사진, 특히 텍스트가 없는 사진에서는 이미지의 유형에 따라 정밀도가 달라질 수 있습니다.

## <a name="recognize-text-api"></a>텍스트 인식 API

> [!NOTE]
> 읽기 API가 사용되며 텍스트 인식 API는 사용되지 않습니다. 읽기 API는 비슷한 기능을 제공하며 PDF, TIFF 및 다중 페이지 파일을 처리하도록 업데이트 되었습니다.

텍스트 인식 API는 OCR과 비슷하지만 비동기적으로 실행되고 업데이트된 인식 모델을 사용합니다. 자세히 알아보려면 [텍스트 인식 API 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)를 확인합니다.

### <a name="image-requirements"></a>이미지 요구 사항

텍스트 인식 API는 다음 요구 사항을 충족하는 이미지를 사용하여 작동합니다.

- 이미지를 JPEG, PNG 또는 BMP 형식으로 제공 해야 합니다.
- 이미지의 크기는 50 x 50와 4200 x 4200 픽셀 사이여야 합니다.
- 이미지의 파일 크기는 4메가바이트(MB) 미만이어야 합니다.

## <a name="limitations"></a>제한 사항

텍스트 인식 작업의 정확도는 이미지의 품질에 따라 달라집니다. 다음의 요소로 인해 읽기 정확도가 떨어질 수 있습니다.

* 흐린 이미지
* 필기체 또는 흘림체 텍스트
* 꾸밈 글꼴 스타일
* 작은 텍스트 크기
* 복잡한 배경, 텍스트 위의 그림자 또는 눈부심, 원근 왜곡
* 너무 크거나 단어 시작에 대문자가 없는 경우
* 아래 첨자, 위 첨자 또는 취소선 텍스트

## <a name="next-steps"></a>다음 단계

간단한 C# 앱에서 텍스트 인식을 구현하기 위해서는 [인쇄된 텍스트 추출(OCR)](./quickstarts/csharp-print-text.md) 빠른 시작을 수행합니다.
