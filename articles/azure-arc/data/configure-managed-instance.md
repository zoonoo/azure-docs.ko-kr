---
title: Azure Arc 사용 SQL 관리 되는 인스턴스 구성
description: Azure Arc 사용 SQL 관리 되는 인스턴스 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940636"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 사용 SQL 관리 되는 인스턴스 구성

이 문서에서는 Azure Arc 사용 SQL 관리 되는 인스턴스를 구성 하는 방법을 설명 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 사용 SQL Managed Instance에 대 한 리소스 구성

### <a name="configure-using-azdata"></a>Azdata를 사용 하 여 구성

CLI를 사용 하 여 Azure Arc 사용 SQL 관리 되는 인스턴스의 구성을 편집할 수 있습니다 `azdata` . 구성 옵션을 보려면 다음 명령을 실행 합니다. 

```
azdata arc sql mi edit --help
```

다음 예에서는 cpu 코어 및 메모리 요청 및 제한을 설정 합니다.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

SQL 관리 되는 인스턴스에 대 한 변경 내용을 보려면 다음 명령을 사용 하 여 구성 yaml 파일을 볼 수 있습니다.

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>서버 옵션 구성

만든 시간 이후에 Azure Arc 사용 SQL 관리 되는 인스턴스의 서버 구성 설정을 구성할 수 있습니다. 이 문서에서는 mssql Agent를 사용 하거나 사용 하지 않도록 설정 하는 등의 설정을 구성 하는 방법을 설명 하 고, 문제 해결 시나리오에 특정 추적 플래그

이러한 설정을 변경하려면 다음 단계를 수행합니다.

1. 대상 설정을 포함하는 사용자 지정 `mssql-custom.conf` 파일을 만듭니다. 다음 예에서는 SQL 에이전트를 사용 하도록 설정 하 고 추적 플래그 1204을 사용 하도록 설정 합니다.

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. `mssql-custom.conf` 파일을 `master-0` Pod의 `mssql-miaa` 컨테이너에 있는 `/var/opt/mssql`에 복사합니다. `<namespaceName>`을 빅 데이터 클러스터 이름으로 바꿉니다.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. SQL Server 인스턴스를 다시 시작합니다.  `<namespaceName>`을 빅 데이터 클러스터 이름으로 바꿉니다.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**알려진 제한 사항**
- 위의 단계를 수행하려면 Kubernetes cluster 관리자 권한이 필요합니다.
- 미리 보기 전체에서 변경 될 수 있습니다.
