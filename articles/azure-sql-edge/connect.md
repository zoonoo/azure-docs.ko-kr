---
title: Azure SQL Edge 연결 및 쿼리
description: Azure SQL Edge에 연결 하 고 쿼리 하는 방법에 대해 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395209"
---
# <a name="connect-and-query-azure-sql-edge"></a>Azure SQL Edge 연결 및 쿼리

Azure SQL Edge에서 컨테이너를 배포한 후에는 다음 위치 중 하나에서 데이터베이스 엔진에 연결할 수 있습니다.

- 컨테이너 내부에서
- 동일한 호스트에서 실행 되는 다른 Docker 컨테이너에서
- 호스트 머신에서
- 네트워크에 있는 다른 클라이언트 컴퓨터에서

## <a name="tools-to-connect-to-azure-sql-edge"></a>Azure SQL Edge에 연결하기 위한 도구

다음과 같은 일반적인 도구 중 하나에서 Azure SQL Edge 인스턴스의 인스턴스에 연결할 수 있습니다.

* [sqlcmd](/sql/linux/sql-server-linux-setup-tools): sqlcmd 클라이언트 도구는 Azure SQL Edge의 컨테이너 이미지에 이미 포함 되어 있습니다. 대화형 bash 셸을 사용하여 실행 중인 컨테이너에 연결하는 경우 도구를 로컬로 실행할 수 있습니다. SQL 클라이언트 도구는 SQL Edge 컨테이너의 ARM64 버전에 포함 되지 않으므로 ARM64 플랫폼에서 사용할 수 없습니다. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

네트워크 컴퓨터에서 Azure SQL Edge 데이터베이스 엔진에 연결 하려면 다음이 필요 합니다.

- **호스트 컴퓨터의 IP 주소 또는 네트워크 이름** : Azure SQL Edge 컨테이너가 실행 되는 호스트 컴퓨터입니다.
- **AZURE SQL Edge 컨테이너 호스트 포트 매핑** : Docker 컨테이너 포트를 호스트의 포트에 매핑하는 것입니다. 컨테이너 내에서 Azure SQL Edge는 항상 포트 1433에 매핑됩니다. 원할 경우이를 변경할 수 있습니다. 포트 번호를 변경 하려면 Azure IoT Edge에서 Azure SQL Edge 모듈의 **컨테이너 만들기 옵션** 을 업데이트 합니다. 다음 예제에서는 컨테이너의 포트 1433이 호스트의 1600 포트에 매핑됩니다.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **AZURE Sql edge 인스턴스의 SA 암호** : `SA_PASSWORD` azure sql edge를 배포 하는 동안 환경 변수에 지정 된 값입니다.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>컨테이너 내에서 데이터베이스 엔진에 연결

[SQL Server 명령줄 도구](/sql/linux/sql-server-linux-setup-tools) 는 Azure SQL Edge의 컨테이너 이미지에 포함 되어 있습니다. 대화형 명령 프롬프트를 사용 하 여 컨테이너에 연결 하는 경우 도구를 로컬로 실행할 수 있습니다. SQL 클라이언트 도구는 SQL Edge 컨테이너의 ARM64 버전에 포함 되지 않으므로 ARM64 플랫폼에서 사용할 수 없습니다. 

1. `docker exec -it` 명령을 사용하여 실행 중인 컨테이너 내에서 대화형 bash 셸을 시작합니다. 다음 예제에서 `e69e056c702d` 은 컨테이너 ID입니다.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > 항상 전체 컨테이너 ID를 지정할 필요는 없습니다. 고유하게 식별하는 데 충분한 문자만 지정하면 됩니다. 따라서이 예에서는 `e6` 전체 ID가 아닌 또는를 사용 하는 것이 충분할 수 있습니다 `e69` .

2. 컨테이너 내부에 있는 경우 sqlcmd를 사용 하 여 로컬로 연결 합니다. Sqlcmd는 기본적으로 경로에 있지 않으므로 전체 경로를 지정 해야 합니다.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Sqlcmd를 마치면를 입력 `exit` 합니다.

4. 대화형 명령 프롬프트를 마치면를 입력 `exit` 합니다. 컨테이너는 대화형 bash 셸을 종료한 후에도 계속 실행됩니다.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>동일한 호스트의 다른 컨테이너에서 Azure SQL Edge에 연결

동일한 호스트에서 실행 되는 두 컨테이너는 동일한 Docker 네트워크에 있으므로 서비스의 컨테이너 이름과 포트 주소를 사용 하 여 쉽게 액세스할 수 있습니다. 예를 들어 동일한 호스트의 다른 python 모듈 (컨테이너)에서 Azure SQL Edge 인스턴스에 연결 하는 경우 다음과 유사한 연결 문자열을 사용할 수 있습니다. (이 예제에서는 Azure SQL Edge가 기본 포트에서 수신 하도록 구성 되어 있다고 가정 합니다.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>다른 네트워크 컴퓨터에서 Azure SQL Edge에 연결

네트워크의 다른 컴퓨터에서 Azure SQL Edge의 인스턴스에 연결할 수 있습니다. 이렇게 하려면 Docker 호스트의 IP 주소와 Azure SQL Edge 컨테이너가 매핑되는 호스트 포트를 사용 합니다. 예를 들어 Docker 호스트의 IP 주소가 *xxx.xxx.xxx.xxx* 이 고 Azure sql edge 컨테이너가 호스트 포트 *1600* 에 매핑된 경우 azure sql edge 인스턴스의 서버 주소는 *xxx. xxx*. xxx. xxx. xxx. xxx. xxx, 1600입니다. 업데이트 된 python 스크립트는 다음과 같습니다.

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Windows 컴퓨터에서 실행 되는 SQL Server Management Studio을 사용 하 여 Azure SQL Edge 인스턴스에 연결 하려면 [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms)를 참조 하세요.

Windows, Mac 또는 Linux 컴퓨터에서 Visual Studio Code를 사용 하 여 Azure SQL Edge 인스턴스에 연결 하려면 [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)를 참조 하세요.

Windows, Mac 또는 Linux 컴퓨터에서 Azure Data Studio를 사용 하 여 Azure SQL Edge 인스턴스에 연결 하려면 [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[연결 및 쿼리](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Linux에서 SQL Server 도구 설치](/sql/linux/sql-server-linux-setup-tools)