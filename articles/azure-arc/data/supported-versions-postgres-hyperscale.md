---
title: Azure Arc를 사용 하는 지원 되는 Postgres 버전 PostgreSQL Hyperscale
description: Azure Arc를 사용 하는 지원 되는 Postgres 버전 PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939184"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc를 사용 하는 Postgres의 지원 되는 버전 PostgreSQL Hyperscale

이 문서에서는 Azure Arc enabled PostgreSQL Hyperscale에서 사용할 수 있는 Postgres의 버전을 설명 합니다.
지원 되는 버전 목록은 시간이 지남에 따라 진화 합니다. 현재 지원 되는 주 버전은 다음과 같습니다.
- Postgres 12 (기본값)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>버전 간에 선택 하는 방법
응용 프로그램에서 설계 된 버전과 각 버전의 기능을 살펴보는 것이 좋습니다. 자세한 내용은 공식 Postgres 사이트에서 각 버전에 대해 읽어 보세요.
- [Postgres 12 (기본값)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc에서 특정 버전을 만드는 방법 PostgreSQL Hyperscale을 사용 하도록 설정 하는 방법
만들 때 _--engine – version_ 매개 변수를 전달 하 여 만들 버전을 지정할 수 있습니다. 버전 정보를 나타내지 않을 경우 기본적으로 Postgres version 12의 서버 그룹이 생성 됩니다.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>다른 버전을 사용할 수 있는 경우 어떤 알림이 표시 되나요?
돌아가서이 문서를 읽어 보세요. 적절 하 게 업데이트 됩니다. Kubernetes 클러스터의 Arc 데이터 컨트롤러에 있는 사용자 지정 리소스 정의 (CRD)의 종류를 나열할 수도 있습니다.
다음 명령 실행:
```console
kubectl get crds
```

다음과 같은 출력을 반환 합니다.
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

이 예제에서이 출력은 Postgres와 관련 된 두 가지 종류의 CRD를 나타냅니다.
- postgresql-12s.arcdata.microsoft.com는 Postgres 12에 대 한 CRD입니다.
- postgresql-11s.arcdata.microsoft.com는 Postgres 11에 대 한 CRD입니다.

서버 그룹이 아니라 CRDs입니다. CRD의 존재는 해당 버전의 서버 그룹을가지고 있거나 만들지 않았음을 나타냅니다.
CRD는 만들 수 있는 리소스 종류를 나타냅니다.

## <a name="next-steps"></a>다음 단계:
- [Azure Arc 사용 PostgreSQL Hyperscale 만들기에 대 한 자세한 정보](create-postgresql-hyperscale-server-group.md)
- [Arc 데이터 컨트롤러에서 만든 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 목록을 가져오는 방법에 대해 알아봅니다.](list-server-groups-postgres-hyperscale.md)
