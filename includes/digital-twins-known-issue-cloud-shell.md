---
author: baanders
description: Azure Digital Twins용 포함 파일 - Cloud Shell 인증과 관련된 알려진 문제 인용
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290103"
---
>[!NOTE]
>Cloud Shell에는 `az dt route`, `az dt model`, `az dt twin` 명령 그룹에 영향을 주는 **알려진 문제**가 있습니다.
>
>이 문제를 해결하려면 명령을 실행하기 전에 Cloud Shell에서 `az login`을 실행하거나 Cloud Shell 대신 [로컬 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용합니다. 이에 대한 자세한 내용은 [*문제 해결: Azure Digital Twins의 알려진 문제*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)를 참조하세요.