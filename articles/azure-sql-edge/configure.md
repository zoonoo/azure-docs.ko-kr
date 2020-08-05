---
title: Azure SQL Edge(미리 보기) 구성
description: Azure SQL Edge (미리 보기)를 구성 하는 방법에 대해 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551985"
---
# <a name="configure-azure-sql-edge-preview"></a>Azure SQL Edge(미리 보기) 구성

Azure SQL Edge는 다음 두 가지 옵션 중 하나를 통한 구성을 지원합니다.

- 환경 변수
- /Var/opt/mssql 폴더에 저장 된 mssql 파일

> [!NOTE]
> 환경 변수를 설정 하면 mssql 파일에 지정 된 설정이 재정의 됩니다.

## <a name="configure-by-using-environment-variables"></a>환경 변수를 사용 하 여 구성

Azure SQL Edge는 SQL Edge 컨테이너를 구성하는 데 사용할 수 있는 여러 가지 환경 변수를 노출합니다. 이러한 환경 변수는 SQL Server on Linux 사용할 수 있는 집합의 하위 집합입니다. SQL Server on Linux 환경 변수에 대 한 자세한 내용은 [환경 변수](/sql/linux/sql-server-linux-configure-environment-variables/)를 참조 하세요.

다음 SQL Server on Linux 환경 변수는 Azure SQL Edge에 대해 지원 되지 않습니다. 정의 된 경우이 환경 변수는 컨테이너를 초기화 하는 동안 무시 됩니다.

| 환경 변수 | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 가용성 그룹을 사용 하도록 설정 합니다. 예를 들어 **1** 은 사용 하도록 설정 되 고 **0** 은 사용 하지 않도록 설정 됩니다. |

> [!IMPORTANT]
> SQL Edge에 대한 **MSSQL_PID** 환경 변수는 **Premium** 및 **Developer**를 유효한 값으로 허용합니다. Azure SQL Edge는 제품 키를 사용 하 여 초기화를 지원 하지 않습니다.

> [!NOTE]
> Azure SQL Edge에 대 한 [Microsoft 소프트웨어 사용 조건](https://go.microsoft.com/fwlink/?linkid=2128283) 을 다운로드 합니다.

### <a name="specify-the-environment-variables"></a>환경 변수 지정

[Azure Portal](deploy-portal.md)를 통해 서비스를 배포할 때 SQL Edge에 대 한 환경 변수를 지정 합니다. 모듈 배포의 **환경 변수** 섹션에서 또는 **컨테이너 만들기 옵션**의 일부로 이러한 항목을 추가할 수 있습니다.

**환경 변수에**값을 추가 합니다.

![환경 변수 목록을 사용 하 여 설정](media/configure/set-environment-variables.png)

**컨테이너 만들기 옵션**에서 값을 추가 합니다.

![컨테이너 만들기 옵션을 사용 하 여 설정](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Mssql 파일을 사용 하 여 구성

Azure SQL Edge는 SQL Server on Linux와 같은 [mssql 구성 유틸리티](/sql/linux/sql-server-linux-configure-mssql-conf/) 를 포함 하지 않습니다. Mssql 파일을 수동으로 구성 하 고 SQL Edge 모듈의/var/opt/mssql/폴더에 매핑된 영구 저장소 드라이브에 저장 해야 합니다. Azure Marketplace에서 SQL Edge를 배포 하는 경우이 매핑은 **컨테이너 만들기 옵션**에서 **탑재** 옵션으로 지정 됩니다.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

다음 mssql 옵션은 SQL Edge에 적용 되지 않습니다.

|옵션|Description|
|:---|:---|
|**고객 의견** | SQL Server 사용자 의견을 Microsoft에 보낼지 여부를 선택 합니다. |
|**데이터베이스 메일 프로필** | SQL Server on Linux의 기본 데이터베이스 메일 프로필을 설정합니다. |
|**고가용성** | 가용성 그룹을 사용하도록 설정합니다. |
|**Microsoft Distributed Transaction Coordinator** | Linux에서 MSDTC를 구성하고 문제를 해결합니다. 추가 분산 트랜잭션 관련 구성 옵션은 SQL Edge에 대해 지원 되지 않습니다. 이러한 추가 구성 옵션에 대 한 자세한 내용은 [MSDTC 구성](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)을 참조 하세요. |
|**ML 서비스 Eula** | Azure Machine Learning 패키지에 R 및 Python Eula을 적용 합니다. SQL Server 2019에만 적용됩니다.|
|**outboundnetworkaccess** |[Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python 및 Java 확장의 아웃바운드 네트워크 액세스를 사용하도록 설정합니다.|

다음 샘플 mssql 파일은 SQL Edge에 대해 작동 합니다. Mssql 파일의 형식에 대 한 자세한 내용은 [mssql 파일 형식](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)을 참조 하십시오.

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>루트가 아닌 사용자로 Azure SQL Edge 실행

Azure SQL Edge CTP 2.2부터 SQL Edge 컨테이너는 루트가 아닌 사용자/그룹을 사용 하 여 실행할 수 있습니다. Azure Marketplace를 통해 배포 되는 경우 다른 사용자/그룹을 지정 하지 않으면 SQL Edge 컨테이너가 mssql (루트가 아닌) 사용자로 시작 합니다. 배포 하는 동안 다른 루트가 아닌 사용자를 지정 하려면 `*"User": "<name|uid>[:<group|gid>]"*` 컨테이너 만들기 옵션 아래에 키-값 쌍을 추가 합니다. 아래 예제에서 SQL Edge는 사용자로 시작 되도록 구성 됩니다 `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

루트가 아닌 사용자가 탑재 된 볼륨에 있는 DB 파일에 액세스할 수 있도록 하려면 컨테이너를 실행 하는 사용자/그룹에 영구 파일 저장소에 대 한 읽기 & 쓰기 권한이 있는지 확인 합니다. 아래 예제에서는 user_id 10001를 사용 하 여 루트가 아닌 사용자를 파일 소유자로 설정 합니다. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>이전 CTP 릴리스에서 업그레이드

이전 CTP의 Azure SQL Edge가 루트 사용자로 실행 되도록 구성 되었습니다. 이전 CTP에서 업그레이드 하는 경우 다음 옵션을 사용할 수 있습니다.

- 루트 사용자 계속 사용-루트 사용자를 계속 사용 하려면 `*"User": "0:0"*` 컨테이너 만들기 옵션 아래에 키-값 쌍을 추가 합니다.
- 기본 mssql 사용자 사용-기본 mssql 사용자를 사용 하려면 다음 단계를 수행 합니다.
  - Docker 호스트에 mssql 이라는 사용자를 추가 합니다. 아래 예제에서는 ID가 10001 인 사용자 mssql를 추가 합니다. 이 사용자는 루트 그룹에도 추가 됩니다.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - 데이터베이스 파일이 있는 디렉터리/탑재 볼륨에 대 한 사용 권한 변경 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- 다른 루트 이외의 사용자 계정을 사용 하 여 다른 루트가 아닌 사용자 계정을 사용 합니다.
  - 컨테이너 만들기 옵션을 업데이트 하 여 `*"User": "user_name | user_id*` 컨테이너 만들기 옵션에서 키-값 쌍 추가를 지정 합니다. User_name 또는 user_id를 docker 호스트의 실제 user_name 또는 user_id로 바꾸세요. 
  - 디렉터리/탑재 볼륨에 대 한 사용 권한을 변경 합니다.



## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에 연결](connect.md)
- [SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md)
