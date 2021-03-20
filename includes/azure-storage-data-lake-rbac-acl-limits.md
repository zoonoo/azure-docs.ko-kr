---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017685"
---
| 메커니즘 | 범위 |제한 | 지원 되는 사용 권한 수준 |
|---|---|---|---|
| Azure RBAC | 저장소 계정, 컨테이너. <br>구독 또는 리소스 그룹 수준에서 리소스 간 Azure 역할 할당 | 2000 구독의 Azure 역할 할당 | Azure 역할 (기본 제공 또는 사용자 지정) |
| ACL| 디렉터리, 파일 |32 ACL 항목 (효과적으로 28 ACL 항목) 및 파일 및 디렉터리 당. 액세스 및 기본 Acl에는 각각 고유한 32 ACL 항목 한도가 있습니다. |ACL 권한|
