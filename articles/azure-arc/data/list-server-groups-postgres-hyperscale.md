---
title: Azure arc Data Controller에서 만든 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 나열 합니다.
description: Azure arc Data Controller에서 만든 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 나열 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940792"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Azure arc Data Controller에서 만든 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 나열 합니다.

이 문서에서는 Arc 데이터 컨트롤러에서 만든 서버 그룹 목록을 검색 하는 방법을 설명 합니다.

이 목록을 검색 하려면 Arc 데이터 컨트롤러에 연결 된 후 다음 방법 중 하나를 사용 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>CLI에서 azdata 사용
명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres server list
```

다음과 같은 출력을 반환 합니다.
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
이 명령에 사용할 수 있는 매개 변수에 대 한 자세한 내용을 보려면 다음을 실행 하십시오.
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>CLI에서 kubectl 사용
다음 명령 중 하나를 실행 합니다.

**Postgres의 버전과 관계 없이 서버 그룹을 나열 하려면 다음을 실행 합니다.**
```console
kubectl get postgresqls
```
다음과 같은 출력을 반환 합니다.
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Postgres의 특정 버전에 대 한 서버 그룹을 나열 하려면 다음을 실행 합니다.**
```console
kubectl get postgresql-12
```

Postgres 버전 11을 실행 하는 서버 그룹을 나열 하려면 _postgresql-12_ 를 _postgresql-11_로 바꿉니다.

## <a name="next-steps"></a>다음 단계:

* [연결 끝점을 가져오고 연결 문자열을 형성 하 여 서버 그룹에 연결 하는 방법에 대 한 문서를 참조 하세요.](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 구성 표시에 대 한 문서를 참조 하십시오.](show-configuration-postgresql-hyperscale-server-group.md)
