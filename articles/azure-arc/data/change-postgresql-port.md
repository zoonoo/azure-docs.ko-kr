---
title: PostgreSQL 포트 변경
description: Azure Arc enabled PostgreSQL Hyperscale 서버 그룹이 수신 대기 중인 포트를 변경 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328759"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>서버 그룹이 수신 대기 중인 포트를 변경 합니다. 

포트를 변경 하는 작업은 서버 그룹의 표준 편집 작업입니다. 포트를 변경 하려면 다음 명령을 실행 합니다.
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

예를 들어 서버 그룹의 이름이 _postgres01_ 이 고 포트 _866_ 에서 수신 대기 하도록 하려는 경우를 가정해 보겠습니다. 다음 명령을 실행 합니다.
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>포트가 변경 되었는지 확인 합니다.

포트가 변경 되었는지 확인 하려면 다음 명령을 실행 하 여 서버 그룹의 구성을 표시 합니다.
```console
azdata arc postgres server show -n <server group name>
```

해당 명령의 출력에서 서버 그룹 사양의 "서비스" 섹션의 "포트" 항목에 대해 표시 된 포트 번호를 확인 합니다.
또는 서버 그룹 사양에서 IP 주소 뒤에 구성 된 포트 번호가 오는 상태 섹션의 externalEndpoint 항목을 확인할 수 있습니다.

위의 예제를 계속 진행 하는 경우 다음 명령을 실행 합니다.
```console
azdata arc postgres server show -n postgres01
```

여기에는 다음과 같은 포트 866이 표시 됩니다.

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
여기

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>다음 단계
- [서버 그룹에 연결 하는 방법](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)을 참조 하세요.
- 설명서의 How-to\Manage\Configure & scale 섹션 섹션에서 서버 그룹의 다른 측면을 구성 하는 방법에 대해 알아봅니다.
