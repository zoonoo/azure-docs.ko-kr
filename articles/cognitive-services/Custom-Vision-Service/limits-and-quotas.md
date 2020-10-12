---
title: 한도 및 할당량 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 다양 한 유형의 라이선스 키와 Custom Vision Service에 대 한 제한 및 할당량에 대해 설명 합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: b79bf5e8ead16bbdf9c69e8d7faae43fa778ab3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316582"
---
# <a name="limits-and-quotas"></a>한도 및 할당량

Custom Vision 서비스에는 두 가지 계층의 키가 있습니다. Azure Portal을 통해 F0(무료) 또는 S0(표준) 구독을 등록할 수 있습니다. 가격 책정 및 트랜잭션에 대한 자세한 내용은 해당 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)를 참조하세요.

프로젝트당 학습 이미지 수 및 프로젝트당 태그 수는 S0 프로젝트에서 시간이 지남에 따라 증가될 것으로 예상됩니다.

|요인|**F0**|**S0**|
|-----|-----|-----|
|프로젝트|2|100|
|프로젝트당 학습 이미지 |5,000|100,000|
|예측/월|10000 |제한 없음|
|태그/프로젝트|50|500|
|반복 횟수 |10|10|
|태그 당 최소 레이블이 지정 된 이미지, 분류 (50 + 권장) |5|5|
|태그 당 최소 레이블이 지정 된 이미지, 개체 검색 (50 개 이상 권장)|15|15|
|예측 이미지 저장 기간|30일|30일|
|스토리지가 있는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|10|
|스토리지가 없는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)(초당 API 호출 수)|2|10|
|[기타 API 호출](https://go.microsoft.com/fwlink/?linkid=865446)(초당 트랜잭션 수)|10|10|
|허용 되는 이미지 형식|jpg, png, bmp, gif|jpg, png, bmp, gif|
|최소 이미지 높이/너비 (픽셀)|256 (참고 참조)|256 (참고 참조)|
|최대 이미지 높이/너비 (픽셀)|10240|10240|
|최대 이미지 크기(학습 이미지 업로드) |6 MB|6 MB|
|최대 이미지 크기(예측)|4MB|4MB|
|개체 검색 학습 이미지 당 최대 지역 수|300|300|
|분류 이미지 당 최대 태그 수|100|100|

> [!NOTE]
> 256 픽셀 보다 작은 이미지는 허용 되지만 upscaled 조정 됩니다.
> 이미지 가로 세로 비율은 25 보다 클 수 없습니다.
