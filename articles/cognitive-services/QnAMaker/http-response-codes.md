---
title: API HTTP 응답 코드 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker API에서 반환되는 HTTP 응답 코드를 이해합니다. 그러면 모든 오류를 해결하는 데 도움이 됩니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8a39e8ee783d999bf3851ef1ba564e8f24de7910
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726955"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP 응답 코드
[관리](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 및 예측 API는 HTTP 응답 코드를 반환합니다. 응답 메시지에는 요청과 관련된 정보가 포함되어 있지만 HTTP 응답 상태 코드는 일반적입니다. 

### <a name="management"></a>관리

|코드|설명|
|:--|--|
|2xx|성공|
|400|요청 매개 변수가 잘못되었습니다. 필수 매개 변수가 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|400|요청 본문이 잘못되었습니다. JSON이 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|401|잘못된 키|
|403|사용 권한 없음 - 올바른 사용 권한이 없음|
|404|기술 자료가 존재하지 않음|