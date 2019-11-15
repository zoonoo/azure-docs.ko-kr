---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648119"
---
**관리** 섹션(오른쪽 위 메뉴)의 **Azure Resources** 페이지(왼쪽 메뉴)에서 **예제 쿼리** URL을 복사한 다음, 새 브라우저 탭에 붙여넣습니다. 

엔드포인트 URL은 APP-ID 및 앱 KEY-ID를 대체하는 고유한 앱 ID와 엔드포인트 키를 사용하여 다음과 같은 형식으로 표시됩니다.

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```