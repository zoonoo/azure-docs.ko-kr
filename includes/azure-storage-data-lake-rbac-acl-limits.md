---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017685"
---
| 메커니즘 | 범위 |제한 | 지원되는 권한 수준 |
|---|---|---|---|
| Azure RBAC | 스토리지 계정, 컨테이너. <br>구독 또는 리소스 그룹 수준에서 리소스 간 Azure 역할 할당. | 구독에 2000개의 Azure 역할 할당 | Azure 역할(기본 제공 또는 사용자 지정) |
| ACL| 디렉터리, 파일 |파일 및 디렉터리당 32개 ACL 항목(효과적으로 28개 ACL 항목). 액세스 및 기본 ACL에는 각각 고유한 32개의 ACL 항목 제한이 있습니다. |ACL 권한|
