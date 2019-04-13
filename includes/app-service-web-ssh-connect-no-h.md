---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550005"
---
컨테이너를 사용 하 여 직접 SSH 세션을 열고 확인, 앱이 실행 되어야 합니다.

브라우저 및 대체에 다음 URL을 붙여 넣습니다. \<앱 이름 > 앱 이름:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

아직 인증 하는 경우 연결할 Azure 구독을 사용 하 여 인증 해야 하 고 있습니다. 인증되면 컨테이너 내부에서 명령을 실행할 수 있는 브라우저 내부 셸을 확인합니다.

![SSH 연결](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
