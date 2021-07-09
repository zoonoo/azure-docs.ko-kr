---
title: 한도 및 할당량 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 다양한 형식의 라이선스 키를 설명하고 Custom Vision Service에 대한 한도 및 할당량에 대해 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: pafarley
ms.openlocfilehash: b8dfe8711733efeb33561531c85925111799daba
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479308"
---
# <a name="limits-and-quotas"></a>한도 및 할당량

Custom Vision 서비스에 대해 2계층의 키가 있습니다. Azure Portal을 통해 F0(무료) 또는 S0(표준) 구독을 등록할 수 있습니다. 가격 책정 및 트랜잭션에 대한 자세한 내용은 해당 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)를 참조하세요.

프로젝트당 학습 이미지 수 및 프로젝트당 태그 수는 S0 프로젝트에서 시간이 지남에 따라 증가될 것으로 예상됩니다.

|요인|**F0(무료)**|**S0(표준)**|
|-----|-----|-----|
|프로젝트|2|100|
|프로젝트당 학습 이미지 |5,000|100,000|
|예측 수/월|10000 |제한 없음|
|태그/프로젝트|50|500|
|반복 횟수 |20|20|
|태그당 레이블이 지정된 최소 이미지, 분류(50개 이상 권장) |5|5|
|태그당 레이블이 지정된 최소 이미지, 개체 검색(50개 이상 권장)|15|15|
|예측 이미지 저장 기간|30일|30일|
|스토리지가 있는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|10|
|스토리지가 없는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)(초당 API 호출 수)|2|10|
|[기타 API 호출](https://go.microsoft.com/fwlink/?linkid=865446)(초당 트랜잭션 수)|10|10|
|허용되는 이미지 형식|jpg, png, bmp, gif|jpg, png, bmp, gif|
|이미지 높이/너비의 최소 픽셀|256(참고 참조)|256(참고 참조)|
|이미지 높이/너비의 최대 픽셀|10,240|10,240|
|최대 이미지 크기(학습 이미지 업로드) |6 MB|6 MB|
|최대 이미지 크기(예측)|4MB|4MB|
|이미지당 최대 영역 수(개체 감지)|300|300|
|이미지당 최대 태그 수(분류)|100|100|

> [!NOTE]
> 256픽셀보다 작은 이미지는 허용되지만 상향 조정됩니다.
> 이미지 가로 세로 비율은 25보다 크지 않아야 합니다.
