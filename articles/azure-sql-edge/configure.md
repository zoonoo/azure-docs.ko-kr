---
title: Azure SQL Edge(미리 보기) 구성
description: Azure SQL Edge(미리 보기) 구성에 대해 자세히 알아보기
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a28724e00f59fe049d1d9d6dfbcbc5a3f9556124
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235155"
---
# <a name="configure-azure-sql-edge-preview"></a>Azure SQL Edge(미리 보기) 구성

Azure SQL Edge는 다음 두 가지 옵션 중 하나를 통한 구성을 지원합니다.

- 환경 변수 사용.
- /var/opt/mssql 폴더에 배치된 mssql.conf 파일 사용.

> [!NOTE]
> 환경 변수를 설정하면 mssql.conf 파일에 지정된 설정이 재정의됩니다.

## <a name="configure-using-environment-variables"></a>환경 변수를 사용한 구성

Azure SQL Edge는 SQL Edge 컨테이너를 구성하는 데 사용할 수 있는 여러 가지 환경 변수를 노출합니다. 이러한 환경 변수는 SQL Server on Linux에서 사용할 수 있는 환경 변수의 하위 집합입니다. SQL Server on Linux 환경 변수에 대한 자세한 내용은 [환경 변수](/sql/linux/sql-server-linux-configure-environment-variables/)를 참조하세요.

다음 SQL Server on Linux 환경 변수는 Azure SQL Edge에 대해 지원되지 않습니다. 정의된 경우 이러한 환경 변수는 컨테이너를 초기화하는 동안 무시됩니다.

| 환경 변수 | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 가용성 그룹을 사용하도록 설정합니다. 예를 들어 '1'은 사용이고 '0'은 사용 안 함입니다. |

> [!IMPORTANT]
> SQL Edge에 대한 *MSSQL_PID* 환경 변수는 **Premium** 및 **Developer**를 유효한 값으로 허용합니다. Azure SQL Edge는 제품 키를 사용한 초기화를 지원하지 않습니다.

> [!NOTE]
> Azure SQL Edge 최종 사용자 사용권 계약을 다운로드하려면 [최종 사용자 사용권 계약](https://go.microsoft.com/fwlink/?linkid=2128283)을 참조하세요.

### <a name="specifying-the-environment-variables"></a>환경 변수 지정

SQL Edge에 대한 환경 변수는 [Azure Portal](deploy-portal.md)을 통해 Azure SQL Edge를 배포할 때 지정할 수 있습니다. 이는 아래에 설명된 대로 모듈 배포의 “환경 변수” 섹션 또는 컨테이너 만들기 옵션의 일부로 추가할 수 있습니다.

*환경 변수 옵션을 사용한 설정*

![환경 변수 목록을 사용한 설정](media/configure/set-environment-variables.png)

*컨테이너 만들기 옵션을 사용한 설정*

![컨테이너 만들기 옵션을 사용한 설정](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>mssql.conf 파일을 사용한 구성

Azure SQL Edge는 SQL Server on Linux와 같은 [mssql-conf 구성 유틸리티](/sql/linux/sql-server-linux-configure-mssql-conf/)를 포함하지 않습니다. 따라서 mssql.conf 파일을 수동으로 구성하고 SQL Edge 모듈의 /var/opt/mssql/ 폴더에 매핑된 영구 스토리지 드라이브에 배치해야 합니다. Azure Marketplace에서 SQL Edge를 배포할 때 이 매핑은 컨테이너 만들기 옵션에서 **Mounts” 옵션으로 지정됩니다.

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

다음 mssql.conf 옵션은 SQL Edge에 적용할 수 없습니다.

|옵션|Description|
|:---|:---|
|**고객 의견** | SQL Server에서 사용자 의견을 Microsoft에 보낼지 여부를 선택합니다. |
|**데이터베이스 메일 프로필** | SQL Server on Linux의 기본 데이터베이스 메일 프로필을 설정합니다. |
|**고가용성** | 가용성 그룹을 사용하도록 설정합니다. |
|**Microsoft Distributed Transaction Coordinator** | Linux에서 MSDTC를 구성하고 문제를 해결합니다. 또한 추가 분산 트랜잭션 관련 구성 옵션은 SQL Edge에 대해 지원되지 않습니다. 이러한 추가 구성 옵션에 대한 자세한 내용은 [MSDTC 구성](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)을 참조하세요. |
|**MLServices EULA** | Machine Learning Services 패키지에 대한 R 및 Python EULA에 동의합니다. SQL Server 2019에만 적용됩니다.|
|**outboundnetworkaccess** |[Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python 및 Java 확장의 아웃바운드 네트워크 액세스를 사용하도록 설정합니다.|

SQL Edge에 대해 작동하는 샘플 mssql.conf 파일이 아래에 나와 있습니다. mssql.conf 파일의 형식에 대한 자세한 내용은 [mssql.conf 형식](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)을 참조하세요.

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

## <a name="next-step"></a>다음 단계

- [Azure SQL Edge에 연결](connect.md)
- [SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md)
