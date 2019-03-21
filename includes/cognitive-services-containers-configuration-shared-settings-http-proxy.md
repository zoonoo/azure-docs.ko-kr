---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 0745e4987ca602a41f2d93ddc0a93cb061ce127d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58052263"
---
아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성해야 하는 경우 다음 두 인수를 사용합니다.

| 이름 | 데이터 형식 | 설명 |
|--|--|--|
|HTTP_PROXY|문자열|사용할 프록시. 예: `http://proxy:8888`|
|HTTP_PROXY_CREDS|문자열|프록시를 인증하는 데 필요한 자격 증명. 예: username:password|

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
Logging:Disk:LogLevel=Debug Logging:Disk:Format=json
```