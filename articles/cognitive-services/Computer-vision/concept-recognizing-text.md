---
title: 인쇄, 필기 텍스트 인식 Computer Vision
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
ms.openlocfilehash: 5d0a9771e5b999028996676ea72f8def3c5d63cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589859"
---
# <a name="recognize-printed-and-handwritten-text"></a>인쇄되고 필기된 텍스트 인식

Computer Vision는 이미지에 표시 되는 인쇄 또는 필기 텍스트를 검색 하 고 추출 하는 다양 한 서비스를 제공 합니다. 이 기능은 메모 가져오기, 의료 기록, 보안 및 은행 등 다양 한 시나리오에서 유용 합니다. 다음 세 섹션에서는 각각 다른 사용 사례에 대해 최적화 된 세 가지 텍스트 인식 Api에 대해 자세히 설명 합니다.

## <a name="read-api"></a>읽기 API

읽기 API는 최신 인식 모델을 사용 하 여 이미지의 텍스트 콘텐츠를 검색 하 고 식별 된 텍스트를 컴퓨터에서 읽을 수 있는 문자 스트림으로 변환 합니다. 텍스트를 많이 사용 하는 이미지 (예: 디지털 스캔 한 문서) 및 시각적 노이즈가 많은 이미지에 최적화 되어 있습니다. 각 텍스트 줄에 사용할 인식 모델을 결정 하 고 인쇄 된 텍스트 및 필기 텍스트를 모두 포함 하는 이미지를 지원 합니다. 큰 문서에서 결과를 반환 하는 데 몇 분 정도 걸릴 수 있으므로 읽기 API는 비동기적으로 실행 됩니다.

읽기 작업은 인식 된 단어의 원래 줄 그룹화를 출력에 유지 합니다. 각 줄에는 경계 상자 좌표가 제공 되며 줄 내의 각 단어에도 고유한 좌표가 있습니다. 낮은 신뢰도로 단어를 인식 하는 경우에도 해당 정보를 전달 합니다. 자세히 알아보려면 [api v2.0 참조 문서 읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) 또는 [api v 3.0 참조 문서](https://aka.ms/computer-vision-v3-ref) 를 참조 하세요.

읽기 작업은 영어, 스페인어, 독일어, 프랑스어, 이탈리아어, 포르투갈어 및 네덜란드어로 텍스트를 인식할 수 있습니다.

### <a name="image-requirements"></a>이미지 요구 사항

읽기 API는 다음 요구 사항을 충족 하는 이미지와 함께 작동 합니다.

- 이미지는 JPEG, PNG, BMP, PDF 또는 TIFF 형식으로 표시 되어야 합니다.
- 이미지의 크기는 50 x 50에서 1만 x 1만 픽셀 사이 여야 합니다. PDF 페이지는 17 x 17 인치이 하 여야 합니다.
- 이미지의 파일 크기는 20mb 미만 이어야 합니다.

### <a name="limitations"></a>제한 사항

무료 계층 구독을 사용 하는 경우 읽기 API는 PDF 또는 TIFF 문서의 처음 두 페이지만 처리 합니다. 유료 구독을 사용 하면 최대 200 페이지까지 처리할 수 있습니다. 또한 API는 페이지당 최대 300 줄을 검색 합니다.

## <a name="ocr-optical-character-recognition-api"></a>OCR (광학 문자 인식) API

Computer Vision의 OCR (광학 문자 인식) API는 읽기 API와 비슷하지만 동기적으로 실행 되며 규모가 많은 문서에 최적화 되지 않습니다. 이전 인식 모델을 사용 하지만 더 많은 언어에서 작동 합니다. 지원 되는 언어의 전체 목록은 [언어 지원](language-support.md#text-recognition) 을 참조 하세요.

필요한 경우, OCR은 가로 이미지 축을 중심으로 회전 오프셋(도)을 반환하여 인식된 텍스트의 회전을 정정합니다. 또한 OCR은 다음 그림에 표시 된 것 처럼 각 단어의 프레임 좌표를 제공 합니다.

![회전 중인 이미지와 해당 텍스트를 읽고 구분 하 고 있습니다.](./Images/vision-overview-ocr.png)

자세히 알아보려면 [OCR 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 를 참조 하세요.

### <a name="image-requirements"></a>이미지 요구 사항

OCR API는 다음 요구 사항을 충족 하는 이미지에서 작동 합니다.

* 이미지는 JPEG, PNG, GIF 또는 BMP 형식으로 표시 되어야 합니다.
* 입력 이미지의 크기는 50 x 50에서 4200 x 4200 픽셀 사이 여야 합니다.
* 90도의 배수에 최대 40도의 작은 각도를 더한 값만큼 이미지의 텍스트를 회전할 수 있습니다.

### <a name="limitations"></a>제한 사항

텍스트가 많은 사진에서는 부분적으로 인식된 단어에서 가양성이 발생할 수 있습니다. 일부 사진, 특히 텍스트가 없는 사진에서 전체 자릿수는 이미지의 유형에 따라 달라질 수 있습니다.

## <a name="recognize-text-api"></a>텍스트 인식 API

> [!NOTE]
> 텍스트 인식 API는 읽기 API를 위해 더 이상 사용 되지 않습니다. 읽기 API는 비슷한 기능을 포함 하며 PDF, TIFF 및 다중 페이지 파일을 처리 하도록 업데이트 되었습니다.

텍스트 인식 API는 OCR과 유사 하지만 비동기적으로 실행 되 고 업데이트 된 인식 모델을 사용 합니다. 자세히 알아보려면 [텍스트 인식 API 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 를 참조 하세요.

### <a name="image-requirements"></a>이미지 요구 사항

텍스트 인식 API는 다음 요구 사항을 충족 하는 이미지와 함께 작동 합니다.

- 이미지는 JPEG, PNG 또는 BMP 형식으로 표시 되어야 합니다.
- 이미지의 크기는 50 x 50에서 4200 x 4200 픽셀 사이 여야 합니다.
- 이미지의 파일 크기는 4mb 보다 작아야 합니다.

## <a name="limitations"></a>제한 사항

텍스트 인식 작업의 정확도는 이미지의 품질에 따라 달라 집니다. 다음 요소는 부정확 한 읽기를 발생 시킬 수 있습니다.

* 흐린 이미지
* 필기체 또는 흘림체 텍스트
* 꾸밈 글꼴 스타일
* 작은 텍스트 크기
* 복잡한 배경, 텍스트 위의 그림자 또는 눈부심, 원근 왜곡
* 단어의 첫 글자에서 대/소문자가 크거나 없습니다.
* 아래 첨자, 위 첨자 또는 취소선 텍스트

## <a name="next-steps"></a>다음 단계

[텍스트 추출 (읽기)](./QuickStarts/CSharp-hand-text.md) 빠른 시작을 따라 간단한 c # 앱에서 텍스트 인식을 구현 합니다.
