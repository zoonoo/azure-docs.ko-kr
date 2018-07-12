---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 9f865897ee478f25a44fe876d44aec253e84eb62
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38731915"
---
_로컬 터미널 창_으로 돌아와서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. _&lt;deploymentLocalGitUrl-from-create-step>_ 을 [웹앱 만들기](#create)에 저장된 Git 원격의 URL로 바꿉니다.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. Git 자격 증명 관리자로 자격 증명을 입력하라는 메시지가 표시되는 경우 Azure Portal에 로그인할 때 사용하는 자격 증명이 아니라 [배포 사용자 구성](#configure-a-deployment-user)에서 만든 자격 증명을 입력해야 합니다.

```bash
git push azure master
```

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.
