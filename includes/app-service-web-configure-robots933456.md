---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78255817"
---
## <a name="robots933456-in-logs"></a>로그의 robots933456

컨테이너 로그에 다음 메시지가 표시될 수 있습니다.

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

이 메시지는 무시해도 괜찮습니다. `/robots933456.txt`는 App Service에서 컨테이너가 요청을 처리할 수 있는지 확인하기 위해 사용하는 더미 URL 경로입니다. 404 응답은 단순히 경로가 존재하지 않는다는 것을 나타내지만 컨테이너가 정상 상태이고 요청에 응답할 준비가 되었음을 App Service에 알려줍니다.

