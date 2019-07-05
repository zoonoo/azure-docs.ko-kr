---
title: Text Analytics API에 대한 액세스 키 가져오기
titleSuffix: Azure Cognitive Services
description: Text Analytics 작업에 대한 액세스 키를 가져와서 Cognitive Services에서 처리할 원시 텍스트를 제출합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 813659b9b1a25e03bf599b0aa1dc9d6af67f1935
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829131"
---
# <a name="how-to-find-endpoints-and-access-keys-for-the-text-analytics-cognitive-service"></a>Text Analytics Cognitive Services에 사용할 엔드포인트 및 액세스 키를 찾는 방법

Text Analytics에 등록하면 구독에 고유한 개인 설정 액세스 키가 제공됩니다. 이 키는 Text Analytics API를 호출할 때마다 필요합니다. [등록](text-analytics-how-to-signup.md)하지 않았으면 지금 등록하여 키를 얻습니다. 

키를 찾는 데 도움이 필요하거나 Text Analytics가 이미 구독에 있는지 확인하려면 다음 지침에 따라 필요한 정보를 가져옵니다. 

## <a name="find-your-service-endpoint-and-access-key"></a>서비스 엔드포인트 및 액세스 키 찾기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 탐색 창에서 **모든 서비스**를 선택합니다.

3. [필터]에서 *Cognitive Services*를 입력합니다. 구독에 Text Analytics가 있는 경우 목록에 **Text Analytics API**라는 API 형식이 표시됩니다.

4. 링크를 클릭하여 서비스 블레이드를 엽니다. 이제 **리소스 > 키**로 차례로 이동하거나 [기본 정보] 창에서 **액세스 키 표시**를 클릭하여 키를 가져올 수 있습니다. 또한 엔드포인트도 [기본 정보] 창에 표시됩니다.

   ![엔드포인트와 키가 있는 포털 페이지](../media/portal-keys-endpoint.png)

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Text Analytics API 호출](text-analytics-how-to-call-api.md)
