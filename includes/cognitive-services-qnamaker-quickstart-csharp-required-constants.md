---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 523ce8a709c0ea4be361b92c65a61ed017f0ccc7
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019117"
---
프로그램 클래스의 맨 위에서 QnA Maker에 액세스하기 위한 다음 상수를 추가합니다.

```csharp
// Represents the various elements used to create HTTP request URIs
// for QnA Maker operations.
static string host = "https://westus.api.cognitive.microsoft.com";
static string service = "/qnamaker/v4.0";
static string method = "/knowledgebases/create";

// NOTE: Replace this value with a valid QnA Maker subscription key.
static string key = "<your-qna-maker-subscription-key>";
```

처음 세 개의 상수는 API에 대한 전체 URL을 만들기 위해 사용됩니다. 마지막 상수는 API에 대한 인증을 제공합니다. 