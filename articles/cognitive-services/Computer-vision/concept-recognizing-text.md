---
title: 인쇄/필기 한 텍스트, Computer Vision 인식
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
# <a name="recognize-printed-and-handwritten-text"></a>인쇄되고 필기된 텍스트 인식

Computer Vision 감지 하 고 이미지에 표시 되는 인쇄 하거나 필기 한 텍스트를 추출 하는 서비스의 수를 제공 합니다. 다양 한 노트와 의료 기록, 보안, 뱅킹 시나리오에서에서 유용 합니다. 다음 세 가지 섹션 3 세부 다양 한 텍스트 인식 Api는 각각에 서로 다른 사용 사례에 대 한 최적화 합니다.

## <a name="read-api"></a>읽기 API

읽기 API는 최신 인식 모델을 사용 하 여 이미지에서 텍스트 콘텐츠를 감지 하 고 컴퓨터가 읽을 수 있는 문자 스트림으로 식별 된 텍스트를 변환 합니다. 지저분한 많이 사용 하 여 이미지 및 텍스트가 많은 이미지 (예: 디지털 검색 않은 문서)에 대 한 최적화 되었습니다. 각 줄의 텍스트를 인쇄 하 고 필기 한 텍스트를 사용 하 여 이미지를 지 원하는 데 인식 모델으로 결정 됩니다. 큰 문서에는 결과 반환 하는 데 몇 분 정도 시간이 걸릴 수 있으므로 읽기 API를 비동기적으로 실행 합니다.

읽기 작업에서 출력에 인식 된 단어의 원래 줄 그룹 유지 관리합니다. 경계 상자 좌표를 사용 하 여 제공 되는 각 줄 및 줄 내에서 각 단어에는 역시 자체 좌표입니다. 낮은 신뢰도 사용 하 여 단어를 인식 하는 경우 해당 정보는도 전달 됩니다. 참조를 [읽기 API 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) 에 대해 자세히 알아보세요.

> [!NOTE]
> 이 기능은 영어 텍스트에 사용할 수만 있습니다.

### <a name="image-requirements"></a>이미지 요구 사항

읽기 API는 다음 요구 사항을 충족 하는 이미지를 사용 하 여 작동 합니다.

- 이미지를 JPEG, PNG, BMP, PDF 또는 TIFF 형식으로 제공 해야 합니다.
- 이미지의 크기는 50 x 50 4200 x 4200 픽셀 사이 여야 합니다. PDF 페이지 또는 더 작은 17 인치 x 17 이어야 합니다.
- 이미지의 파일 크기는 20mb 미만 (MB) 여야 합니다.

### <a name="limitations"></a>제한 사항

무료 계층 구독을 사용 하는 경우 읽기 API를 PDF 또는 TIFF 문서 처음 두 페이지와만 처리 됩니다. 유료 구독을 최대 200 개의 페이지 처리 합니다. API는 페이지당 300 선의 최대 감지는 참고도 합니다.

## <a name="ocr-optical-character-recognition-api"></a>OCR (광학 문자 인식) API

Computer Vision OCR (광학 문자 인식) API는 읽기 API와 유사한 있지만 동기적으로 실행 하 고 큰 문서에 대 한 최적화 되지 않은 것입니다. 작동 하지만 이전 인식 모델을 사용 하 여 추가 언어; 참조 [언어 지원](language-support.md#text-recognition) 지원 되는 언어의 전체 목록입니다.

필요한 경우, OCR은 가로 이미지 축을 중심으로 회전 오프셋(도)을 반환하여 인식된 텍스트의 회전을 정정합니다. OCR는 또한 다음 그림에 표시 된 대로 각 단어의 프레임 좌표를 제공 합니다.

![회전할 이미지 및 읽고 구분 된 텍스트](./Images/vision-overview-ocr.png)

참조를 [OCR 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 에 대해 자세히 알아보세요.

### <a name="image-requirements"></a>이미지 요구 사항

OCR API는 다음 요구 사항을 충족 하는 이미지에서 작동 합니다.

* 이미지를 JPEG, PNG, GIF 또는 BMP 형식으로 제공 해야 합니다.
* 입력된 이미지의 크기는 50 x 50 4200 x 4200 픽셀 사이 여야 합니다.
* 90도의 배수에 최대 40도의 작은 각도를 더한 값만큼 이미지의 텍스트를 회전할 수 있습니다.

### <a name="limitations"></a>제한 사항

텍스트가 많은 사진에서는 부분적으로 인식된 단어에서 가양성이 발생할 수 있습니다. 전체 자릿수는 일부 사진, 텍스트, 없이 특히 사진에 이미지의 형식에 따라 달라질 수 있습니다.

## <a name="recognize-text-api"></a>Recognize Text API

> [!NOTE]
> 텍스트 인식 API는 API를 읽기 위해 되지 않습니다. 읽기 API 비슷한 기능이 고 PDF, TIFF 및 다중 페이지 파일을 처리 하도록 업데이트 됩니다.

OCR, 텍스트 인식 API 비슷합니다 하지만 비동기적으로 실행 하 고 업데이트 된 인식 모델을 사용 합니다. 참조를 [인식 Text API 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 자세한 합니다.

### <a name="image-requirements"></a>이미지 요구 사항

텍스트 인식 API는 다음 요구 사항을 충족 하는 이미지를 사용 하 여 작동 합니다.

- 이미지를 JPEG, PNG 또는 BMP 형식으로 제공 해야 합니다.
- 이미지의 크기는 50 x 50 4200 x 4200 픽셀 사이 여야 합니다.
- 이미지의 파일 크기 보다 작은 4 메가바이트 (MB) 여야 합니다.

## <a name="limitations"></a>제한 사항

텍스트 인식 작업의 정확도 이미지의 품질에 따라 달라 집니다. 다음 요소를 정확 하지 않은 읽기는 발생할 수 있습니다.

* 흐린 이미지
* 필기체 또는 흘림체 텍스트
* 꾸밈 글꼴 스타일
* 작은 텍스트 크기
* 복잡한 배경, 텍스트 위의 그림자 또는 눈부심, 원근 왜곡
* 너무 큰 없거나 대문자 단어의 처음과 끝에 있습니다.
* 아래 첨자, 위 첨자 또는 취소선 텍스트

## <a name="next-steps"></a>다음 단계

수행 합니다 [인쇄 된 텍스트 (OCR)를 추출](./quickstarts/csharp-print-text.md) 간단한 텍스트 인식 구현 하는 빠른 시작 C# 앱.
