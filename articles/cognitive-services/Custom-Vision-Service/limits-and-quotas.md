---
title: 한도 및 할당량 - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Custom Vision Service의 한도 및 할당량에 대해 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 9cff5fdac39be2338305cd37a4b2328a28a48255
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269266"
---
# <a name="limits-and-quotas"></a>한도 및 할당량

Custom Vision service에는 두 개의 키 계층이 있으며 Azure Portal을 통해 F0(무료) 또는 S0(표준) 구독을 등록할 수 있습니다. 가격 책정 및 트랜잭션에 대한 자세한 내용은 해당 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)를 참조하세요.

프로젝트당 학습 이미지 수 및 프로젝트당 태그 수는 S0 프로젝트에서 시간이 지남에 따라 증가될 것으로 예상됩니다.

||**F0**|**S0**|
|-----|-----|-----|
|프로젝트|2|100|
|프로젝트당 학습 이미지 |5,000|100,000|
|예측 수/월|10000 |Unlimited|
|태그/프로젝트|50|500|
|반복 횟수 |10|10|
|태그 당 최소 레이블이 지정된 이미지, 분류(50개 이상 권장) |5|5|
|태그 당 최소 레이블이 지정된 이미지, 개체 감지(50개 이상 권장)|15|15|
|예측 이미지 저장 기간|30일|30일|
|저장소가 있는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|10|
|저장소가 없는 [예측](https://go.microsoft.com/fwlink/?linkid=865445) 작업(초당 트랜잭션 수)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)(초당 API 호출 수)|2|10|
|[기타 API 호출](https://go.microsoft.com/fwlink/?linkid=865446)(초당 트랜잭션 수)|10|10|
|최대 이미지 크기(학습 이미지 업로드) |6MB|6MB|
|최대 이미지 크기(예측)|4MB|4MB|
|개체 검색 학습 이미지 당 최대 영역|200|200|
|이미지 분류 당 최대 태그 수|30|30|
