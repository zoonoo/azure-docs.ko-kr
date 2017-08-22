---
title: "빠른 시작: Azure Database for MySQL 서버 만들기 - Azure Portal | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for MySQL 서버 샘플을 만드는 과정을 안내합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: ko-kr
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal을 사용한 Azure Database for MySQL 서버 만들기
Azure Database for MySQL은 클라우드에서 항상 사용 가능한 MySQL 데이터베이스를 실행, 관리 및 크기 조정할 수 있게 하는 관리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 약 5분만에 Azure Database for MySQL 서버를 만드는 방법을 살펴봅니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
먼저 웹 브라우저를 열고 [Microsoft Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기
Azure Database for MySQL 서버는 정의된 [계산 및 저장소 리소스](./concepts-compute-unit-and-storage.md) 집합을 사용하여 만들어집니다. 서버는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에 만들어집니다.

다음 단계에 따라 Azure Database for MySQL 서버를 만듭니다.

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추(+)를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **데이터베이스** 페이지에서 **Azure Database for MySQL**을 선택합니다. 또한 새 페이지 검색 상자에서 **MySQL**을 입력하여 해당 서비스를 찾을 수도 있습니다.
![Azure Portal - 새로 만들기 - 데이터베이스 - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 위 이미지와 같이 다음 정보를 사용하여 새 서버 세부 정보 양식을 채웁니다.

    **설정** | **제안 값** | **필드 설명** 
    ---|---|---
    서버 이름 | myserver4demo | Azure Database for MySQL 서버를 식별하는 고유한 이름을 선택합니다. 도메인 이름 *mysql.database.azure.com*이 연결할 응용 프로그램에 사용자가 제공한 서버 이름에 추가됩니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
    구독 | 사용자의 구독 | 서버에 사용할 Azure 구독입니다. 구독이 여러 개인 경우 리소스가 과금될 적절한 구독을 선택합니다.
    리소스 그룹 | myresourcegroup | 새 리소스 그룹 이름을 만들거나, 구독에서 기존 이름을 사용할 수 있습니다.
    서버 관리자 로그인 | myadmin | 서버에 연결할 경우 사용할 사용자 고유의 로그인 계정을 만듭니다. 관리자 로그인 이름은 'azure_superuser', 'admin', 'administrator', 'root', 'guest' 또는 'public'일 수 없습니다.
    암호 | *사용자 선택* | 서버 관리자 계정의 새 암호를 만듭니다. 8자에서 128자를 포함해야 합니다. 암호에는 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등) 범주 중 세 개에 해당하는 문자가 포함되어 있어야 합니다.
    암호 확인 | *사용자 선택*| 관리자 계정 암호를 확인합니다.
    위치 | *사용자와 가장 가까운 지역*| 사용자 또는 다른 Azure 응용 프로그램에 가장 가까운 위치를 선택합니다.
    버전 | *최신 버전 선택*| 특정 요구 사항이 없을 경우 최신 버전을 선택합니다.
    가격 책정 계층 | **기본**, **50 Compute 단위** **50 GB** | **가격 책정 계층**을 클릭하고 새 데이터베이스의 서비스 계층 및 성능 수준을 지정합니다. 맨 위의 탭에서 **기본 계층**을 선택합니다. **Compute 단위** 슬라이더의 왼쪽 끝을 클릭하여 이 빠른 시작에 사용할 수 있는 최소 크기 값을 조정합니다. **확인**을 클릭하여 가격 책정 계층 선택을 저장합니다. 다음 스크린샷이 표시됩니다.
    대시보드에 고정 | 확인 | **대시 보드에 고정** 옵션을 선택하여 Azure Portal의 전면 대시보드 페이지에서 서버를 간편하게 추적합니다.

    > [!IMPORTANT]
    > 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인해야 합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.
    > 

    ![Azure Portal - 필요한 양식 입력을 통해 MySQL 만들기](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  **만들기**를 클릭하여 서버를 프로비전합니다. 프로비저닝에 몇 분(최대 20분)이 소요됩니다.
   
5.  도구 모음에서 **알림**(종 아이콘)을 클릭하여 배포 프로세스를 모니터링합니다.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

Azure Database for MySQL 서비스는 서버 수준 방화벽을 만듭니다. 방화벽 규칙을 만들어 특정 IP 주소에 대한 방화벽을 열지 않는 한 이 방화벽은 외부 응용 프로그램과 도구에서 서버 및 서버의 데이터베이스에 연결되지 않도록 방지합니다. 

1.  배포가 완료된 후 서버를 찾습니다. 필요한 경우 검색할 수 있습니다. 예를 들어 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 서버 이름(예: *myserver4demo*)을 입력하여 새로 만든 서버를 검색합니다. 검색 결과에 나열된 서버 이름을 클릭합니다. 서버에 대한 **개요** 페이지가 열리고 추가 구성을 위한 옵션이 제공됩니다.

2. 서버 페이지에서 **연결 보안**을 선택합니다.

3.  **방화벽 규칙** 제목에서 **규칙 이름** 열의 빈 텍스트 상자를 클릭하여 방화벽 규칙을 만들기 시작합니다. 

    이 빠른 시작에서는 각 열의 텍스트 상자를 다음 값으로 채워서 모든 IP 주소를 서버로 허용해 보겠습니다.

    규칙 이름 | 시작 IP | 종료 IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. **연결 보안** 페이지의 위쪽 도구 모음에서 **저장**을 클릭합니다. 몇 분 동안 기다렸다가 연결 보안 업데이트가 성공적으로 완료되었다는 알림이 표시된 후 진행합니다.

    > [!NOTE]
    > Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 3306 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열지 않으면 서버에 연결할 수 없습니다
    > 

## <a name="get-the-connection-information"></a>연결 정보 가져오기
데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명을 기억해야 합니다. 빠른 시작 문서의 앞부분에서 이러한 값을 기록했을 수도 있습니다. 기록하지 않은 경우에는 Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

1. 서버의 **개요** 페이지를 엽니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다. 
    각 필드 위에 커서를 올려 놓으면 텍스트 오른쪽에 복사 아이콘이 나타납니다. 필요에 따라 복사 아이콘을 클릭하여 값을 복사합니다.

    이 예제에서 서버 이름은 *myserver4demo.mysql.database.azure.com*이고, 서버 관리자 로그인은 *myadmin@myserver4demo*입니다.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>mysql 명령줄 도구를 사용하여 MySQL에 연결
Azure Database for MySQL 서버 연결하기 위해 사용할 수 있는 여러 응용 프로그램이 있습니다. 이제 [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) 명령줄 도구를 사용하여 서버에 연결하는 방법을 자세히 설명해 보겠습니다.  추가 소프트웨어를 설치할 필요 없이 여기에 설명된 대로 웹 브라우저 및 Azure Cloud Shell을 사용할 수 있습니다. 사용자의 컴퓨터에 로컬로 mysql 유틸리티가 설치되어 있는 경우 여기에서 연결할 수도 있습니다.

1. Azure Portal 웹 페이지의 오른쪽 위에서 터미널 아이콘( >_ )을 통해 Azure Cloud Shell을 시작합니다.

2. Azure Cloud Shell이 브라우저에서 열려 bash 셸 명령을 입력할 수 있습니다.

    ![명령 프롬프트 - mysql 명령줄 예](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. Cloud Shell 프롬프트의 녹색 프롬프트에서 mysql 명령줄을 입력하여 Azure Database for MySQL 서버에 연결합니다.

    다음 형식은 mysql 유틸리티를 사용하여 Azure Database for MySQL 서버에 연결하는 데 사용됩니다.
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    예를 들어 다음 명령은 예제 서버에 연결합니다.
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql 매개 변수 |제안 값|설명
    ---|---|---
    --host | *서버 이름* | 이전에 Azure Database for MySQL을 만들 때 사용된 서버 이름 값을 지정합니다. 표시된 예제 서버는 myserver4demo.mysql.database.azure.com입니다. 예제에서 표시된 것과 같은 정규화된 도메인 이름(\*.mysql.database.azure.com)을 사용합니다. 서버 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다. 
    --user | *서버 관리자 로그인 이름* |이전에 Azure Database for MySQL을 만들 때 제공한 서버 관리자 로그인 사용자 이름을 입력합니다. 사용자 이름을 기억하지 못하는 경우 이전 섹션의 단계에 따라 연결 정보를 가져옵니다.  형식은 *username@servername*입니다.
    --password | *메시지가 표시될 때까지 대기* | 명령을 입력하면 [암호 입력] 메시지가 표시됩니다. 메시지가 표시되면 서버를 만들 때 제공한 암호와 동일한 암호를 입력합니다.  입력한 암호 문자는 입력하는 동안 Bash 프롬프트에 표시되지 않습니다. 모든 문자를 입력한 후 Enter 키를 눌러 인증하고 연결합니다.

   연결된 후에는 Mysql 유틸리티에 명령을 입력하라는 `mysql>` 프롬프트가 표시됩니다. 

    mysql 출력의 예:
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Azure Cloud Shell의 IP 주소를 허용하도록 방화벽이 구성되지 않은 경우 다음과 같은 오류가 발생합니다.
    >
    > 오류 2003(28000): IP 주소가 123.456.789.0인 클라이언트는 서버에 액세스할 수 없습니다.
    >
    > 이 오류를 해결하려면 서버 구성이 문서의 *서버 수준 방화벽 규칙 구성* 섹션에 있는 단계와 일치하는지 확인합니다.

4. 서버 상태를 확인하여 연결이 작동하는지 확인합니다. 연결된 후에는 mysql> 프롬프트에서 `status`를 입력합니다.
    ```sql
    status
    ```

   > [!TIP]
   > 다른 명령은 [MySQL 5.7 참조 설명서 - 4.5.1장](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)을 참조하세요.

5.  다음 명령을 mysql> 프롬프트에서 입력하여 빈 데이터베이스를 만듭니다.
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    명령을 완료하려면 몇 분 정도 걸릴 수 있습니다. 

    Azure Database for MySQL 서버 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 만들 수 있는 데이터베이스의 수에는 제한이 없으나 여러 데이터베이스가 동일한 서버 리소스를 공유하는 것입니다. 

6. 다음 명령을 mysql> 프롬프트에서 입력하여 데이터베이스를 나열합니다.

    ```sql
    SHOW DATABASES;
    ```

7.  `\q`를 입력한 후 ENTER 키를 눌러 mysql 도구를 중지합니다. 완료한 후에는 Azure Cloud Shell을 닫습니다.

이제 Azure Database for MySQL에 연결하고 빈 사용자 데이터베이스가 생성되었습니다. 다른 일반적인 도구인 MySQL Workbench를 사용하여 다음 섹션을 진행하고 동일한 서버에 연결하는 비슷한 과정을 반복합니다.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI 도구로 서버에 연결
GUI 도구 MySQL Workbench를 사용하여 Azure MySQL 서버에 연결하려면

1.  클라이언트 컴퓨터에서 MySQL Workbench 응용 프로그램을 실행합니다. MySQL Workbench는 [여기](https://dev.mysql.com/downloads/workbench/)에서 다운로드할 수 있습니다.

2.  **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

    ![새 연결 설정](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **설정** | **제안 값** | **필드 설명** |
    |---|---|---|
    |   연결 이름 | 데모 연결 | 이 연결에 대한 레이블을 지정합니다. |
    | 연결 방법 | 표준(TCP/IP) | 표준(TCP/IP)이면 충분합니다. |
    | 호스트 이름 | *서버 이름* | 이전에 Azure Database for MySQL을 만들 때 사용된 서버 이름 값을 지정합니다. 표시된 예제 서버는 myserver4demo.mysql.database.azure.com입니다. 예제에서 표시된 것과 같은 정규화된 도메인 이름(\*.mysql.database.azure.com)을 사용합니다. 서버 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다.  |
    | 포트 | 3306 | Azure Database for MySQL에 연결 시 항상 포트 3306을 사용합니다. |
    | 사용자 이름 |  *서버 관리자 로그인 이름* | 이전에 Azure Database for MySQL을 만들 때 제공한 서버 관리자 로그인 사용자 이름을 입력합니다. 이 예제에서는 사용자 이름이 myadmin@myserver4demo입니다. 사용자 이름을 기억하지 못하는 경우 이전 섹션의 단계에 따라 연결 정보를 가져옵니다. 형식은 *username@servername*입니다.
    | 암호 | 사용자 암호 | [Store in Vault(자격 증명 모음에 저장)...] 단추를 클릭하여 암호를 저장합니다. |

    **연결 테스트**를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다. [확인]을 클릭하여 연결을 저장합니다. 

    > [!NOTE]
    > 서버에서 기본적으로 적용되는 SSL은 연결의 성공을 위해 추가적인 구성이 필요합니다. [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.  이 빠른 시작에 대해 SSL을 사용하지 않도록 하려면 Azure Portal을 방문하고 연결 보안 페이지를 클릭하여 SSL 연결 적용 토글 단추를 사용하지 않도록 설정합니다.

## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹에 모든 리소스를 포함하는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 삭제하거나, 하나의 서버 리소스만 삭제하여(다른 리소스를 그대로 유지하려는 경우) 빠른 시작에서 만든 리소스를 정리합니다.

> [!TIP]
> 이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작을 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.
>

새로 만든 서버를 비롯하여 전체 리소스 그룹을 삭제하려면
1.  Azure Portal에서 리소스 그룹을 찾습니다. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 리소스 그룹의 이름(예: **myresourcegroup**)을 클릭합니다.
2.  리소스 그룹 페이지에서 **삭제**를 클릭합니다. 그런 다음 텍스트 상자에 리소스 그룹의 이름(예: **myresourcegroup**)을 입력하고 삭제를 확인한 후 **삭제**를 클릭합니다.

또는 대신 새로 만든 서버를 삭제하려면
1.  Azure Portal에서 서버를 찾습니다(열려 있지 않은 경우). Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 만든 서버를 검색합니다.
2.  **개요** 페이지의 맨 위 창에서 **삭제** 단추를 클릭합니다.
![Azure Database for MySQL - 서버 삭제](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  삭제할 서버 이름을 확인하고 적용되는 항목 아래 데이터베이스를 표시합니다. 텍스트 상자에 서버 이름(예: **myserver4demo**)을 입력한 후 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 Azure Database for MySQL 데이터베이스 디자인](./tutorial-design-database-using-portal.md)


