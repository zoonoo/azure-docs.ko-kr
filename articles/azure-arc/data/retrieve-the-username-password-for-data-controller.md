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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761705"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>사용자 이름 및 암호를 검색하여 Arc 데이터 컨트롤러에 연결

데이터 컨트롤러에 대 한 사용자 이름 및 암호를 검색 해야 하는 경우가 있을 수 있습니다. 을 실행할 때 필요한 명령입니다. 

```console
azdata login
```

클러스터에 대 한 Kubernetes 관리자 인 경우 Kubernetes 암호에서 검색할 명령을 실행할 수 있는 권한은 Azure Arc가 유지 하는 정보를 저장 합니다.

> [!NOTE]
>  데이터 컨트롤러를 만든 네임 스페이스에 다른 이름을 사용한 경우 `-n arc` 데이터 컨트롤러를 만든 네임 스페이스의 이름을 사용 하도록 아래 명령의 매개 변수를 변경 해야 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

다음 명령을 실행 하 여 사용자 이름을 검색 합니다.

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

다음 명령을 실행 하 여 암호를 검색 합니다.

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

다음 명령을 실행 하 여 사용자 이름을 검색 합니다.

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

다음 명령을 실행 하 여 암호를 검색 합니다.

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>다음 단계

다른 [시나리오](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md) 사용해 보기
