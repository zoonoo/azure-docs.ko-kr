---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944057"
---
함수를 로컬로 개발하는 경우, 터미널 또는 명령 프롬프트에서 Azure Functions 핵심 도구를 사용하여 필요한 확장을 설치할 수 있습니다. 

함수에 필요한 모든 바인딩을 포함하도록 *function.json* 파일을 업데이트한 후에 프로젝트 폴더에서 `func extensions install` 명령을 실행합니다. 이 명령은 *function.json* 파일을 읽어 필요한 패이지를 확인한 후 설치합니다.

특정 버전의 패키지를 설치하려고 하거나 *function.json* 파일을 편집하기 전에 패키지를 설치하려는 경우 다음 예제와 같이 패키지 이름에 `func extensions install` 명령을 사용합니다.

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

`<target_version>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다.
