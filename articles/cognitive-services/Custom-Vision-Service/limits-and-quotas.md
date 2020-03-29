---
title: 한도 및 할당량 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 다양한 유형의 라이선싱 키와 사용자 지정 비전 서비스의 제한 및 할당량에 대해 설명합니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081780"
---
# <a name="limits-and-quotas"></a>한도 및 할당량

사용자 지정 비전 서비스에는 두 계층의 키가 있습니다. Azure Portal을 통해 F0(무료) 또는 S0(표준) 구독을 등록할 수 있습니다. 가격 책정 및 트랜잭션에 대한 자세한 내용은 해당 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)를 참조하세요.

프로젝트당 학습 이미지 수 및 프로젝트당 태그 수는 S0 프로젝트에서 시간이 지남에 따라 증가될 것으로 예상됩니다.

||**F0**|**S0**|
|-----|-----|-----|
|프로젝트|2|100|
|프로젝트당 학습 이미지 |5,000|100,000|
|예측 / 월|10000 |제한 없음|
|태그 / 프로젝트|50|500|
|반복 횟수 |10|10|
|태그, 분류당 최소 레이블 이미지(50+ 권장) |5|5|
|태그당 최소 레이블이미지, 객체 감지(50+ 권장)|15|15|
|예측 이미지 저장 기간|30일|30일|
|스토리지가 있는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|10|
|스토리지가 없는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)(초당 API 호출 수)|2|10|
|[기타 API 호출](https://go.microsoft.com/fwlink/?linkid=865446)(초당 트랜잭션 수)|10|10|
|허용된 이미지 유형|jpg, png, bmp, GIF|jpg, png, bmp, GIF|
|이미지 높이/너비를 픽셀 단위로 최소|256 (참고 참조)|256 (참고 참조)|
|픽셀 의 최대 이미지 높이 / 너비|무제한|무제한|
|최대 이미지 크기(학습 이미지 업로드) |6MB|6MB|
|최대 이미지 크기(예측)|4MB|4MB|
|개체 감지 교육 이미지당 최대 영역|300|300|
|분류 이미지당 최대 태그|100|100|

> [!NOTE]
> 256픽셀보다 작은 이미지는 허용되지만 고급화됩니다.
