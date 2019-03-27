---
title: Node.js 및 VS Code를 사용하여 Azure Dev Spaces로 팀 개발
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: f0fc1e693f3eccd0b6f6b69339f210785df6ea69
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773235"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>코드 변경
`mywebapi`에 대한 VS Code 창으로 이동하고, 기본 `/` GET 처리기에 대한 코드를 편집합니다. 예를 들어 다음과 같습니다.

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
