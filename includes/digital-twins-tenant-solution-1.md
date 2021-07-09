---
author: baanders
description: Azure Digital Twins를 사용하여 테넌트 사이의 제한에 대한 토큰 솔루션을 설명하는 파일을 포함하기
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 08c48b3600a975f43a5ad94e2cc6efd88047d0d8
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205662"
---
이 작업을 수행하는 한 가지 방법은 다음 CLI 명령을 사용하여 수행하는 것입니다. 이 명령에서 `<home-tenant-ID>`은 Azure Digital Twins 인스턴스를 포함하는 Azure AD 테넌트 ID입니다.

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

이 명령을 요청하면 ID는 `https://digitaltwins.azure.net` Azure AD 리소스에 대해 발급된 토큰을 받게 됩니다. 여기에는 Azure Digital Twins 인스턴스와 일치하는 테넌트 ID 클레임이 있습니다. API 요청 또는 `Azure.Identity` 코드에서 이 토큰을 사용하면 페더레이션 ID가 Azure Digital Twins 리소스에 액세스할 수 있습니다.
