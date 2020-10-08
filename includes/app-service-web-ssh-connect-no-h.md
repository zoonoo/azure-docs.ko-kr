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
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822790"
---
컨테이너와 직접 SSH 세션을 열려면 앱을 실행해야 합니다.

브라우저에 다음 URL을 붙여넣고 `<app-name>`을 앱 이름으로 바꿉니다.

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

아직 인증을 받지 못한 경우 연결하기 위해서는 Azure 구독에서 인증을 받아야 합니다. 인증되면 컨테이너 내부에서 명령을 실행할 수 있는 브라우저 내부 셸을 확인합니다.

![SSH 연결](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
