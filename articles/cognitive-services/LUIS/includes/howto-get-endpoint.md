---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545083"
---
**관리** 섹션(오른쪽 위 메뉴)의 **Azure Resources** 페이지(왼쪽 메뉴)에서 **예제 쿼리** URL을 복사한 다음, 새 브라우저 탭에 붙여넣습니다.

엔드포인트 URL은 APP-ID 및 앱 KEY-ID를 대체하는 고유한 사용자 지정 하위 도메인, 앱 ID 및 엔드포인트 키를 사용하여 다음과 같은 형식으로 표시됩니다.

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```