---
title: Azure 데이터 브릭을 사용하여 SQL 서버 Linux 도커 컨테이너 쿼리
description: 이 문서에서는 VNet 주입이라고도 하는 가상 네트워크에 Azure Databricks를 배포하는 방법을 설명합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747658"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>자습서: Azure Databricks 노트북에서 가상 네트워크에서 SQL Server Linux Docker 컨테이너쿼리

이 자습서에서는 Azure Databricks를 가상 네트워크의 SQL Server Linux Docker 컨테이너와 통합하는 방법을 설명합니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Databricks 작업 영역을 가상 네트워크에 배포
> * 공용 네트워크에 Linux 가상 시스템 설치
> * Docker 설치
> * 리눅스 도커 컨테이너에 마이크로 소프트 SQL 서버 설치
> * Databricks 노트북에서 JDBC를 사용하여 SQL 서버를 쿼리합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 가상 [네트워크에서 Databricks 작업 영역을](quickstart-create-databricks-workspace-vnet-injection.md)만듭니다.

* [윈도우 우분투를](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)설치 .

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)를 다운로드합니다.

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

1. Azure 포털에서 가상 컴퓨터의 아이콘을 **선택합니다.** 그런 다음, **+ 추가**를 선택합니다.

    ![새 Azure 가상 시스템 추가](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. **기본** 탭에서 우분투 서버 18.04 LTS를 선택하고 VM 크기를 B2로 변경합니다. 관리자 사용자 이름과 암호를 선택합니다.

    ![새로운 가상 시스템 구성의 기본 탭](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. **네트워킹** 탭으로 이동합니다. Azure Databricks 클러스터를 포함하는 가상 네트워크 및 공용 서브넷을 선택합니다. **검토 + 만들기를**선택한 다음 가상 컴퓨터를 배포하려면 **만들기를** 선택합니다.

    ![새로운 가상 시스템 구성의 네트워킹 탭](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 배포가 완료되면 가상 컴퓨터로 이동합니다. **개요에서**공용 IP 주소 및 가상 네트워크/서브넷을 확인합니다. 공용 **IP 주소** 선택

    ![가상 머신 개요](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. **할당을** **정적으로** 변경하고 **DNS 이름 레이블을**입력합니다. **저장을**선택하고 가상 컴퓨터를 다시 시작합니다.

    ![공용 IP 주소 구성](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. **설정**에서 **네트워킹** 탭을 선택합니다. Azure Databricks 배포 중에 만들어진 네트워크 보안 그룹은 가상 컴퓨터와 연결되어 있습니다. **인바운드 포트 규칙 추가를**선택합니다.

7. SSH에 대해 포트 22를 여는 규칙을 추가합니다. 다음 설정을 사용합니다.
    
    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |원본|IP 주소|IP 주소는 특정 소스 IP 주소에서 들어오는 트래픽이 이 규칙에 의해 허용되거나 거부되도록 지정합니다.|
    |원본 IP 주소|<공개 IP\>|공용 IP 주소를 입력합니다. [bing.com](https://www.bing.com/) 방문하여 **"my IP"를**검색하여 공용 IP 주소를 찾을 수 있습니다.|
    |원본 포트 범위|*|모든 포트에서 트래픽을 허용합니다.|
    |대상|IP 주소|IP 주소는 특정 소스 IP 주소에 대한 나가는 트래픽이 이 규칙에 의해 허용되거나 거부되도록 지정합니다.|
    |대상 IP 주소|<vm 공개 IP\>|가상 컴퓨터의 공용 IP 주소를 입력합니다. 가상 시스템의 **개요** 페이지에서 찾을 수 있습니다.|
    |대상 포트 범위|22|SSH에 대한 포트 22를 엽니다.|
    |우선 순위|290|규칙에 우선 순위를 부여합니다.|
    |이름|ssh-데이터 브릭스-튜토리얼-VM|규칙에 이름을 지정합니다.|


    ![포트 22에 대한 인바운드 보안 규칙 추가](./media/vnet-injection-sql-server/open-port.png)

8. 다음 설정을 사용하여 SQL용 포트 1433을 여는 규칙을 추가합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |원본|모두|소스는 특정 소스 IP 주소에서 들어오는 트래픽이 이 규칙에 의해 허용되거나 거부되도록 지정합니다.|
    |원본 포트 범위|*|모든 포트에서 트래픽을 허용합니다.|
    |대상|IP 주소|IP 주소는 특정 소스 IP 주소에 대한 나가는 트래픽이 이 규칙에 의해 허용되거나 거부되도록 지정합니다.|
    |대상 IP 주소|<vm 공개 IP\>|가상 컴퓨터의 공용 IP 주소를 입력합니다. 가상 시스템의 **개요** 페이지에서 찾을 수 있습니다.|
    |대상 포트 범위|1433|SQL Server에 대해 포트 22를 엽니다.|
    |우선 순위|300|규칙에 우선 순위를 부여합니다.|
    |이름|sql-databricks-튜토리얼-VM|규칙에 이름을 지정합니다.|

    ![포트 1433에 대한 인바운드 보안 규칙 추가](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Docker 컨테이너에서 SQL 서버 실행

1. [윈도우 우분투를](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)엽니 다 , 또는 가상 머신에 SSH를 할 수 있도록 다른 도구. Azure 포털에서 가상 컴퓨터로 이동한 후 **연결을** 선택하여 연결해야 하는 SSH 명령을 가져옵니다.

    ![가상 머신에 연결](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. 우분투 터미널에 명령을 입력 하 고 가상 컴퓨터를 구성 할 때 만든 관리자 암호를 입력 합니다.

    ![우분투 터미널 SSH 로그인](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 다음 명령을 사용하여 가상 컴퓨터에 Docker를 설치합니다.

    ```bash
    sudo apt-get install docker.io
    ```

    다음 명령을 통해 Docker 설치를 확인합니다.

    ```bash
    sudo docker --version
    ```

4. 이미지를 설치합니다.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    이미지를 확인합니다.

    ```bash
    sudo docker images
    ```

5. 이미지에서 컨테이너를 실행합니다.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    컨테이너가 실행 중인지 확인합니다.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

1. SQL Server 관리 Studio를 열고 서버 이름 및 SQL 인증을 사용하여 서버에 연결합니다. 로그인 사용자 이름은 **SA이고** 암호는 Docker 명령에 설정된 암호입니다. 예제 명령의 암호는 `Password1234`.

    ![SQL 서버 관리 스튜디오를 사용하여 SQL 서버에 연결](./media/vnet-injection-sql-server/ssms-login.png)

2. 성공적으로 연결되면 **새 쿼리를** 선택하고 다음 코드 조각을 입력하여 데이터베이스, 테이블을 만들고 테이블에 일부 레코드를 삽입합니다.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![SQL Server 데이터베이스를 만들려면 쿼리](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Azure 데이터 브릭에서 SQL 서버 쿼리

1. Azure Databricks 작업 영역으로 이동하여 필수 구성 조건의 일부로 클러스터를 만들했는지 확인합니다. 그런 다음 **전자 필기장 만들기를**선택합니다. 전자 필기장에 이름을 지정하고 *파이썬을* 언어로 선택하고 만든 클러스터를 선택합니다.

    ![새로운 데이터 브릭스 노트북 설정](./media/vnet-injection-sql-server/create-notebook.png)

2. 다음 명령을 사용하여 SQL Server 가상 시스템의 내부 IP 주소를 ping합니다. 이 ping은 성공해야 합니다. 그렇지 않은 경우 컨테이너가 실행 중인지 확인하고 NSG(네트워크 보안 그룹) 구성을 검토합니다.

    ```python
    %sh
    ping 10.179.64.4
    ```

    nslookup 명령을 사용하여 검토할 수도 있습니다.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. SQL Serverping을 성공적으로 ping한 후에는 데이터베이스와 테이블을 쿼리할 수 있습니다. 다음 파이썬 코드를 실행합니다.

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, Azure Databricks 작업 영역 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 불필요한 요금 청구를 방지합니다. 나중에 Azure Databricks 작업 영역을 사용하려는 경우 클러스터를 중지하고 나중에 다시 시작할 수 있습니다. 이 Azure Databricks 작업 영역을 계속 사용하지 않으려면 다음 단계를 사용하여 이 자습서에서 만든 모든 리소스를 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음, 만든 리소스 그룹의 이름을 클릭합니다.

2. 리소스 그룹 페이지에서 **삭제**를 선택하고, 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음, **삭제**를 다시 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Databricks를 사용하여 데이터를 추출, 변환 및 로드하는 방법을 알아보려면 다음 문서로 이동합니다.
> [!div class="nextstepaction"]
> [자습서: Azure Databricks를 사용하여 데이터를 추출, 변환 및 로드합니다.](databricks-extract-load-sql-data-warehouse.md)
