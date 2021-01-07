---
title: 원호의 사용 PostgreSQL Hyperscale 서버 그룹에 대 한 연결 끝점 및 폼 연결 문자열 가져오기
titleSuffix: Azure Arc enabled data services
description: 원호의 사용 PostgreSQL Hyperscale 서버 그룹에 대 한 연결 끝점 및 폼 연결 문자열 가져오기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4087d618209ab4db46f89ef4e6db7ac87ca4cf57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331015"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>원호의 사용 PostgreSQL Hyperscale 서버 그룹에 대 한 연결 끝점 및 폼 연결 문자열 가져오기

이 문서에서는 서버 그룹에 대 한 연결 끝점을 검색 하는 방법 및 응용 프로그램 및/또는 도구에서 사용 하는 연결 문자열을 구성 하는 방법을 설명 합니다.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>연결 끝점 가져오기:

### <a name="from-cli-with-azdata"></a>CLI에서 azdata 사용
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Arc 데이터 컨트롤러에 연결 합니다.
- Arc 데이터 컨트롤러의 호스트에서 세션이 이미 열려 있는 경우 다음 명령을 실행 합니다.
```console
azdata login
```

- Arc 데이터 컨트롤러의 호스트에서 세션을 열지 않은 경우 다음 명령을 실행 합니다. 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. 연결 끝점 표시
다음 명령 실행:
```console
azdata arc postgres endpoint list -n <server group name>
```
다음과 같은 출력을 반환 합니다.
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```
이러한 끝점을 사용 하 여 다음을 수행 합니다.
- 연결 문자열을 구성 하 고 클라이언트 도구나 응용 프로그램에 연결 합니다.
- 브라우저에서 Grafana 및 Kibana 대시보드 액세스합니다.

예를 들어 _PostgreSQL Instance_ 라는 끝점을 사용 하 여 psql을 서버 그룹에 연결할 수 있습니다. 예를 들면 다음과 같습니다.
```console
psql postgresql://postgres:MyPassworkd@12.345.123.456:1234
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - "_PostgreSQL Instance_" 라는 끝점에 지정 된 _postgres_ 사용자의 암호는 서버 그룹을 배포할 때 선택한 암호입니다.
> - Azdata 정보: 연결에 연결 된 임대는 약 10 시간 동안 지속 됩니다. 그런 다음 다시 연결 해야 합니다. 임대가 만료 된 경우 azdata (azdata login 제외)를 사용 하 여 명령을 실행 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다. _오류: (401)_ 
>  _이유: 권한이 없는_ 
>  _HTTP 응답 헤더: HTTPHeaderDict ({' Date ': ' Sun, 06 년 9 월 2020 16:58:38 GMT ', ' content-length ': ' 0 ', ' WWW-인증 ': '_ 
>  _기본 영역 = "로그인_ 자격 증명 필요", 전달자 오류 = "invalid_token", error_description = "토큰이 만료 됨" '}) _이 경우 위에 설명 된 대로 azdata에 다시 연결 해야 합니다.

## <a name="from-cli-with-kubectl"></a>CLI에서 kubectl 사용
- 서버 그룹이 Postgres version 12 (기본값) 인 경우 다음 명령을 수행 합니다.
```console
kubectl get postgresql-12/<server group name>
```
- 서버 그룹이 Postgres 버전 11 인 경우 다음 명령을 수행 합니다.
```console
kubectl get postgresql-11/<server group name>
```

이러한 명령은 아래와 같은 출력을 생성 합니다. 이 정보를 사용 하 여 연결 문자열을 구성할 수 있습니다.
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>양식 연결 문자열:
서버 그룹에 대 한 연결 문자열의 템플릿 아래 표를 사용 합니다. 그런 다음 필요에 따라 복사/붙여넣고 사용자 지정할 수 있습니다.

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C + + (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

### <a name="web-app"></a>웹앱

```webapp
Database=postgres; Data Source=192.168.1.121; User Id=postgres; Password={your_password_here}
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹에 대 한 [규모 확장 (작업자 노드 추가)을](scale-out-postgresql-hyperscale-server-group.md) 참조 하세요.
- 서버 그룹 [확장 또는 축소 (메모리/vcores 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 를 참조 하세요.


