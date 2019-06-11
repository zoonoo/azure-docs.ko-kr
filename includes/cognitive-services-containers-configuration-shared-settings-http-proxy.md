---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116729"
---
아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성해야 하는 경우 다음 두 인수를 사용합니다.

| 이름 | 데이터 형식 | 설명 |
|--|--|--|
|HTTP_PROXY|문자열|사용할 프록시(예: `http://proxy:8888`)<br><proxy-url>|
|HTTP_PROXY_CREDS|문자열|프록시를 인증하는 데 필요한 자격 증명(예: username:password).|
|`<proxy-user>`|문자열|프록시 사용자입니다.|
|`proxy-password`|문자열|프록시에 대해 `<proxy-user>`에 연결된 암호입니다.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```