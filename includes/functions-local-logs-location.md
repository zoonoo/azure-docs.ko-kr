---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739499"
---
Functions 호스트가 로컬로 실행되면 다음 경로에 로그를 씁니다.

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Windows에서 `<DefaultTempDirectory>`는 TMP, TEMP, USERPROFILE 환경 변수 또는 Windows 디렉터리에서 찾은 첫 번째 값입니다.
MacOS 또는 Linux에서 `<DefaultTempDirectory>`는 TMPDIR 환경 변수입니다.

> [!NOTE]
> Functions 호스트가 시작되면 디렉터리의 기존 파일 구조를 덮어씁니다.