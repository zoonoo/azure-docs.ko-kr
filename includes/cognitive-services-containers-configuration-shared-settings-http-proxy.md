---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630092"
---
아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성해야 하는 경우 다음 두 인수를 사용합니다.

| Name | 데이터 형식 | Description |
|--|--|--|
|HTTP_PROXY|문자열|사용할 프록시(예: `http://proxy:8888`)<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|문자열|프록시를 인증하는 데 필요한 자격 증명(예: `username:password`). 이 값은 **소문자여야 합니다**. |
|`<proxy-user>`|문자열|프록시 사용자입니다.|
|`<proxy-password>`|문자열|프록시에 대해 `<proxy-user>`에 연결된 암호입니다.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
