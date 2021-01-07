---
title: Azure Arc에서 PostgreSQL Hyperscale 서버 그룹에 대 한 Postgres engine 서버 매개 변수 구성
titleSuffix: Azure Arc enabled data services
description: Azure Arc에서 PostgreSQL Hyperscale 서버 그룹에 대 한 Postgres engine 서버 매개 변수 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311056"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에 대한 데이터베이스 엔진 설정 지정

이 문서에서는 PostgreSQL 하이퍼스케일 서버 그룹의 데이터베이스 엔진 설정을 사용자 지정 값(기본값 아님)으로 설정하는 단계에 대해 설명합니다. 설정할 수 있는 데이터베이스 엔진 매개 변수에 대 한 자세한 내용 및 기본값은 [여기](https://www.postgresql.org/docs/current/runtime-config.html)에서 PostgreSQL 설명서를 참조 하세요.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> 미리 보기는 다음 매개 변수를 설정 하는 기능을 지원 하지 않습니다. 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>구문

데이터베이스 엔진 설정을 구성 하는 명령의 일반적인 형식은 다음과 같습니다.

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>현재 사용자 지정 값 표시

### <a name="with-azure-data-cli-azdata-command"></a>With [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 명령

```console
azdata arc postgres server show -n <server group name>
```

예를 들면 다음과 같습니다.

```console
azdata arc postgres server show -n postgres01
```

이 명령은 사용자가 설정한 매개 변수를 확인 하는 서버 그룹의 사양을 반환 합니다. Engine\settings 섹션이 없는 경우 모든 매개 변수가 해당 기본값에서 실행 됨을 의미 합니다.

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>With kubectl 명령

아래 단계를 수행 합니다.

1. 서버 그룹에 대 한 사용자 지정 리소스 정의의 종류를 검색 합니다.

   다음을 실행합니다.

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   예를 들면 다음과 같습니다.

   ```console
   azdata arc postgres server show -n postgres01
   ```

   이 명령은 사용자가 설정한 매개 변수를 확인 하는 서버 그룹의 사양을 반환 합니다. Engine\settings 섹션이 없는 경우 모든 매개 변수가 해당 기본값에서 실행 됨을 의미 합니다.

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   출력 결과에서 필드를 찾아 `kind` 값을 따로 설정 합니다 (예:) `postgresql-12` .

2. 서버 그룹에 해당 하는 Kubernetes 사용자 지정 리소스 설명 

   명령의 일반적인 형식은 다음과 같습니다.

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   예를 들면 다음과 같습니다.

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   엔진 설정에 설정 된 사용자 지정 값이 있는 경우 해당 값을 반환 합니다. 예를 들면 다음과 같습니다.

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   엔진 설정에 대 한 사용자 지정 값을 설정 하지 않은 경우의 엔진 설정 섹션은 `resultset` 다음과 같이 비어 있게 됩니다.

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>엔진 설정에 대 한 사용자 지정 값 설정

아래 명령은 코디네이터 노드의 매개 변수와 PostgreSQL Hyperscale의 작업자 노드를 동일한 값으로 설정 합니다. 서버 그룹에는 역할 당 매개 변수를 설정할 수 없습니다. 즉, 지정 된 매개 변수를 코디네이터 노드의 특정 및 작업자 노드에 대 한 다른 값으로 구성할 수 없습니다.

### <a name="set-a-single-parameter"></a>단일 매개 변수 설정

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

예를 들면 다음과 같습니다.

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>단일 명령을 사용 하 여 여러 매개 변수 설정

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

예를 들면 다음과 같습니다.

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>매개 변수를 기본값으로 다시 설정 합니다.

매개 변수를 기본값으로 다시 설정 하려면 값을 표시 하지 않고 매개 변수를 설정 합니다. 

예를 들면 다음과 같습니다.

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>모든 매개 변수를 기본값으로 다시 설정

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

예를 들면 다음과 같습니다.

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>특별 고려 사항

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>쉼표, 공백 또는 특수 문자를 포함 하는 값을 매개 변수로 설정 합니다.

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

예를 들면 다음과 같습니다.

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>매개 변수 값에 환경 변수를 전달 합니다.

환경 변수는 설정 되기 전에 확인 되지 않도록 "' '" 안에 래핑해야 합니다.

예를 들면 다음과 같습니다. 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹에 대 한 [규모 확장 (작업자 노드 추가)을](scale-out-postgresql-hyperscale-server-group.md) 참조 하세요.
- 서버 그룹 [확장 또는 축소 (메모리/vcores 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 를 참조 하세요.
