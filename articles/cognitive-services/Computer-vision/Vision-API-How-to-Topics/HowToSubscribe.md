---
title: Computer Vision API에 대한 구독 키 가져오기
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 Computer Vision API 호출에 대한 구독 키를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.openlocfilehash: db4d589bb0c7611e632a90f2174ad8e9c415bf6a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985425"
---
# <a name="how-to-obtain-subscription-keys"></a>구독 키를 가져오는 방법

Computer Vision 서비스에는 특별한 구독 키가 필요합니다. Computer Vision API를 호출할 때마다 구독 키가 필요합니다. 이 키는 쿼리 문자열 매개 변수를 통해 전달되거나 요청 헤더에서 지정되어야 합니다.

구독 키를 등록하려면 [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 무료로 등록할 수 있습니다. 이러한 서비스에 대한 가격 책정은 변경될 수 있습니다.

>[!NOTE]
구독 키는 이러한 [Microsoft Azure 지역](https://azure.microsoft.com/regions/) 중 하나에 대해서만 유효합니다. 

| 지역 | 주소 |
|---|---|
| 미국 서부 | westus.api.cognitive.microsoft.com |
| 미국 동부 2 | eastus2.api.cognitive.microsoft.com |
| 미국 중서부 | westcentralus.api.cognitive.microsoft.com |
| 서유럽 | westeurope.api.cognitive.microsoft.com |
| 동남아시아 | southeastasia.api.cognitive.microsoft.com |

Computer Vision 무료 평가판을 사용하여 등록하는 경우 구독 키는 **westcentral** 지역(`https://westcentralus.api.cognitive.microsoft.com/`)에 대해서만 유효합니다. 가장 일반적인 경우입니다. 그러나 [https://azure.microsoft.com/](https://azure.microsoft.com/) 웹 사이트를 통해 Microsoft Azure 계정을 Computer Vision에 등록한 경우 이전 목록의 지역 중에서 평가판에 대한 지역을 지정합니다.

예를 들어 Computer Vision에서 Microsoft Azure 계정을 등록하고 해당 지역에 `westus`를 지정한 경우 REST API 호출에 대해 `westus` 지역을 사용해야 합니다(`https://westus.api.cognitive.microsoft.com/`).

평가판 키를 가져온 후에 구독 키에 대한 지역을 잊은 경우 [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 해당 지역을 찾을 수 있습니다.

### <a name="related-links"></a>관련 링크:

* [Azure Cognitive Services API의 가격 책정 옵션](https://azure.microsoft.com/pricing/details/cognitive-services/)
