---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131635"
---
| 메커니즘 | Scope |제한 | 지원 되는 사용 권한 수준 |
|---|---|---|---|
| RBAC | 저장소 계정, 컨테이너. <br>구독 또는 리소스 그룹 수준에서 리소스 RBAC 역할 할당 | 2000 RBAC 역할 할당을 구독에서 | RBAC 역할 (기본 제공 또는 사용자 지정) |
| ACL| 디렉터리, 파일 |32 ACL 항목 (효과적으로 28 ACL 항목) 및 파일 및 디렉터리 당. 액세스 및 기본 Acl에는 각각 고유한 32 ACL 항목 한도가 있습니다. |ACL 권한|
