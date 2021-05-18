---
title: 사용자 이름 및 암호를 검색하여 Arc 데이터 컨트롤러에 연결
description: 사용자 이름 및 암호를 검색하여 Arc 데이터 컨트롤러에 연결
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761705"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>사용자 이름 및 암호를 검색하여 Arc 데이터 컨트롤러에 연결

데이터 컨트롤러의 사용자 이름 및 암호를 검색해야 하는 경우가 있을 수 있습니다. 다음은 실행할 때 필요한 명령입니다. 

```console
azdata login
```

클러스터의 Kubernetes 관리자인 경우 명령을 실행하여 Kubernetes 비밀 저장소에서 Azure Arc가 유지하는 정보를 검색할 수 있는 권한이 있습니다.

> [!NOTE]
>  데이터 컨트롤러를 만든 네임스페이스에 다른 이름을 사용한 경우 데이터 컨트롤러를 만든 네임스페이스의 이름을 사용하도록 아래 명령의 `-n arc` 매개 변수를 변경해야 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

사용자 이름을 검색하려면 다음 명령을 실행합니다.

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

암호를 검색하려면 다음 명령을 실행합니다.

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

사용자 이름을 검색하려면 다음 명령을 실행합니다.

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

암호를 검색하려면 다음 명령을 실행합니다.

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>다음 단계

다른 [시나리오](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)를 사용해 봅니다.
