---
title: "Azure Portal: PostgreSQL용 Azure Database 만들기 | Microsoft Docs"
description: "Azure Portal 사용자 인터페이스를 통해 PostgreSQL용 Azure Database 서버를 만들고 관리하기 위한 빠른 시작 가이드입니다."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: e89058a500aeb542ae4c7dc6bb4a9b9ae54db7f2
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Azure Portal을 사용하여 PostgreSQL용 Azure Database 만들기

PostgreSQL용 Azure Database는 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정할 수 있게 하는 관리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 PostgreSQL용 Azure Database 서버를 만드는 방법을 살펴봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database 만들기

PostgreSQL용 Azure Database 서버는 정의된 [계산 및 저장소 리소스](./concepts-compute-unit-and-storage.md) 집합으로 만들어집니다. 서버는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에 만들어집니다.

다음 단계에 따라 PostgreSQL용 Azure Database 서버를 만듭니다.
1.  Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추(+)를 클릭합니다.
2.  **새로 만들기** 페이지에서 **데이터베이스**를 선택하고, **데이터베이스** 페이지에서 **PostgreSQL용 Azure Database**을 선택합니다.
 ![PostgreSQL용 Azure Database - 데이터베이스 만들기](./media/quickstart-create-database-portal/1-create-database.png)

3.  위 이미지와 같이 다음 정보를 사용하여 새 서버 세부 정보 양식을 채웁니다.

    설정|제안 값|설명
    ---|---|---
    서버 이름 |*mypgserver-20170401*|PostgreSQL 서버에 대한 Azure Database를 식별하는 고유한 이름을 선택합니다. 도메인 이름 *postgres.database.azure.com*이 연결할 응용 프로그램에 사용자가 제공한 서버 이름에 추가됩니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
    구독|*사용자의 구독*|서버에 사용할 Azure 구독입니다. 구독이 여러 개인 경우 리소스가 과금될 적절한 구독을 선택합니다.
    리소스 그룹|*myresourcegroup*| 새 리소스 그룹 이름을 만들거나, 구독에서 기존 이름을 사용할 수 있습니다.
    서버 관리자 로그인 |*mylogin*| 서버에 연결할 경우 사용할 자체 로그인 계정을 만듭니다. 관리자 로그인 이름은 'azure_superuser', 'azure_pg_admin', 'admin', 'administrator', 'root', 'guest' 또는 'public'일 수 없으며 'pg_'로 시작할 수 없습니다.
    암호 |*사용자 선택* | 서버 관리자 계정의 새 암호를 만듭니다. 8자에서 128자를 포함해야 합니다. 암호에는 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등) 범주 중 세 개에 해당하는 문자가 포함되어 있어야 합니다.
    위치|*사용자와 가장 가까운 지역*| 사용자에게 가장 가까운 위치를 선택합니다.
    PostgreSQL 버전|*최신 버전 선택*| 특정 요구 사항이 없을 경우 최신 버전을 선택합니다.
    가격 책정 계층 | **기본**, **50 Compute 단위** **50 GB** | **가격 책정 계층**을 클릭하고 새 데이터베이스의 서비스 계층 및 성능 수준을 지정합니다. 맨 위의 탭에서 기본 계층을 선택합니다. Compute 단위 슬라이더의 왼쪽 끝을 클릭하여 이 빠른 시작에 사용할 수 있는 최소 크기 값을 조정합니다. **확인**을 클릭하여 가격 책정 계층 선택을 저장합니다. 다음 스크린샷이 표시됩니다.
    | 대시보드에 고정 | 확인 | **대시 보드에 고정** 옵션을 선택하여 Azure Portal의 전면 대시보드 페이지에서 서버를 간편하게 추적합니다.

  > [!IMPORTANT]
  > 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인해야 합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

    ![Azure Database for PostgreSQL - 가격 책정 계층 선택](./media/quickstart-create-database-portal/2-service-tier.png)

4.  **만들기**를 클릭하여 서버를 프로비전합니다. 프로비저닝에 몇 분(최대 20분)이 소요됩니다.

5.  도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.
 ![PostgreSQL용 Azure Database - 알림 확인](./media/quickstart-create-database-portal/3-notifications.png)
   
  기본적으로 **postgres** 데이터베이스가 서버 아래에 만들어집니다. [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 데이터베이스는 사용자, 유틸리티 및 타사 응용 프로그램에서 사용하는 기본 데이터베이스입니다. 

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

PostgreSQL용 Azure Database 서비스는 서버 수준 방화벽을 만듭니다. 방화벽 규칙을 만들어 특정 IP 주소에 대한 방화벽을 열지 않는 한 이 방화벽은 외부 응용 프로그램과 도구에서 서버 및 서버의 데이터베이스에 연결되지 않도록 방지합니다. 

1.  배포가 완료된 후 서버를 찾습니다. 필요한 경우 검색할 수 있습니다. 예를 들어 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 서버 이름(예: mypgserver-20170401)을 입력하여 새로 만든 서버를 검색합니다. 검색 결과에 나열된 서버 이름을 클릭합니다. 서버에 대한 개요 페이지가 열리고 추가 구성을 위한 옵션이 제공됩니다.
 
    ![PostgreSQL용 Azure Database - 서버 검색 ](./media/quickstart-create-database-portal/4-locate.png)

2.  서버 페이지에서 **연결 보안**을 선택합니다. 
    ![PostgreSQL용 Azure Database - 방화벽 규칙 만들기](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  **방화벽 규칙** 제목에서 **규칙 이름** 열의 빈 텍스트 상자를 클릭하여 방화벽 규칙을 만들기 시작합니다. 

    이 빠른 시작에서는 각 열의 텍스트 상자를 다음 값으로 채워서 모든 IP 주소를 서버로 허용해 보겠습니다.

    규칙 이름 | 시작 IP | 종료 IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. 연결 보안 페이지의 위쪽 도구 모음에서 **저장**을 클릭한 다음 **X**를 클릭하여 연결 보안 페이지를 닫습니다.

    > [!NOTE]
    > Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 5432 포트를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
  >

## <a name="get-the-connection-information"></a>연결 정보 가져오기

Azure Database for PostgreSQL 서버를 만들 때 **postgres**라는 기본 데이터베이스도 만들어집니다. 데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명을 다시 호출해야 합니다. 빠른 시작 문서의 앞부분에서 이러한 값을 기록했을 수도 있습니다. 기록하지 않은 경우에는 Azure Portal의 서버 개요 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

1. 서버의 **개요** 페이지를 엽니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다.
    각 필드 위에 커서를 올려 놓으면 텍스트 오른쪽에 복사 아이콘이 나타납니다. 필요에 따라 복사 아이콘을 클릭하여 값을 복사합니다.

 ![PostgreSQL용 Azure Database - 서버 관리자 로그인](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Cloud Shell에서 psql을 사용하여 PostgreSQL 데이터베이스에 연결

Azure Database for PostgreSQL 서버 연결하기 위해 사용할 수 있는 여러 응용 프로그램이 있습니다. 이제 psql 명령줄 유틸리티를 사용하여 서버에 연결하는 방법을 자세히 설명해 보겠습니다.  추가 소프트웨어를 설치할 필요 없이 여기에 설명된 대로 웹 브라우저 및 Azure Cloud Shell을 사용할 수 있습니다. 사용자의 컴퓨터에 로컬로 psql 유틸리티가 설치되어 있는 경우 여기에서 연결할 수도 있습니다.

1. 위쪽 탐색 창의 터미널 아이콘을 통해 Azure Cloud Shell을 시작합니다.

   ![PostgreSQL용 Azure Database - Azure Cloud Shell 터미널 아이콘](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell이 브라우저에서 열려 bash 셸 명령을 입력할 수 있습니다.

   ![PostgreSQL용 Azure Database - Azure Shell Bash 프롬프트](./media/quickstart-create-database-portal/8-bash.png)

3. Cloud Shell의 녹색 프롬프트에 psql 명령줄을 입력하여 Azure Database for PostgreSQL의 데이터베이스에 연결합니다.

    다음 형식은 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 PostgreSQL용 Azure Database 서버에 연결하는 데 사용됩니다.
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    예를 들어 다음 명령은 예제 서버에 연결합니다.

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql parameter |제안 값|설명
    ---|---|---
    --host | *서버 이름* | 이전에 Azure Database for PostgreSQL을 만들 때 사용된 서버 이름 값을 지정합니다. 표시된 예제 서버는 mypgserver-20170401.postgres.database.azure.com입니다. 예제에서 표시된 것과 같은 정규화된 도메인 이름(\*.postgres.database.azure.com)을 사용합니다. 서버 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다. 
    --port | **5432** | Azure Database for PostgreSQL에 연결 시 항상 포트 5432를 사용합니다. 
    --username | *서버 관리자 로그인 이름* |이전에 Azure Database for PostgreSQL을 만들 때 제공한 서버 관리자 로그인 사용자 이름을 입력합니다. 사용자 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다.  형식은 *username@servername*입니다.
    --dbname | **postgres** | 첫 연결 시 기본 시스템에서 생성한 데이터베이스 이름 *postgres*를 사용합니다. 나중에 데이터베이스를 직접 만듭니다.

    사용자의 매개 변수 값을 사용하여 psql 명령을 실행하면 서버 관리자 암호를 입력하라는 메시지가 표시됩니다. 서버를 만들 때 제공한 암호와 동일합니다. 

    psql parameter |제안 값|설명
    ---|---|---
    암호 | *관리자 암호* | 입력한 암호 문자는 Bash 프롬프트에 표시되지 않습니다. 모든 문자를 입력한 후 Enter 키를 눌러 인증하고 연결합니다.

4.  서버에 연결되면 다음 명령을 입력하여 프롬프트에 빈 데이터베이스를 만듭니다.
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  프롬프트에서 다음 명령을 실행하여 새로 만든 **mypgsqldb** 데이터베이스에 대한 연결로 전환합니다.
    ```bash
    \c mypgsqldb
    ```

6.  \q를 입력한 다음 ENTER 키를 눌러 psql을 중지합니다. Azure Cloud Shell을 종료할 수 있습니다.

이제 Azure Database for PostgreSQL에 연결하고 빈 사용자 데이터베이스가 생성되었습니다. 다른 일반적인 도구인 pgAdmin을 사용하여 연결하려면 다음 섹션으로 진행합니다.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>pgAdmin을 사용하여 PostgreSQL 데이터베이스에 연결

GUI 도구 _pgAdmin_을 사용하여 Azure PostgreSQL 서버에 연결하려면
1.  클라이언트 컴퓨터에서 _pgAdmin_ 응용 프로그램을 실행합니다. _pgAdmin_은 http://www.pgadmin.org/에서 설치할 수 있습니다.
2.  **빠른 링크** 메뉴에서 **새 서버 추가**를 선택합니다.
3.  **만들기-서버** 대화 상자의 **일반** 탭에서**Azure PostgreSQL 서버**처럼 서버에 대해 고유하면서 익숙한 이름을 입력합니다.
![pgAdmin 도구 - 만들기 - 서버](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  **만들기-서버** 대화 상자의 **연결** 탭에서 지정된 대로 설정을 사용한 다음 **저장**을 클릭합니다.
   ![pgAdmin - 만들기 - 서버](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **호스트 이름/주소**: mypgserver-20170401.postgres.database.azure.com 
        - 정규화된 서버 이름
    - **포트:** 5432
        - 이 데이터베이스 서버에서 사용하는 포트 번호는 5432입니다.
    - **데이터베이스 유지 관리**: postgres 
        - 기본 시스템에서 생성된 데이터베이스 이름입니다.
    - **사용자 이름:** mylogin@mypgserver-20170401 
        - 이 빠른 시작의 앞 부분에서 구한 서버 관리자 로그인(user@mypgserver)입니다.
    - **암호**: 이 빠른 시작의 앞 부분에서 서버를 만들 때 선택한 암호입니다.
    - **SSL 모드**: 필수
        - 기본적으로 모든 Azure PostgreSQL 서버는 SSL을 실행한 상태에서 만들어집니다.  SSL 실행을 해제하려면 [SSL 적용](./concepts-ssl-connection-security.md)에서 세부 사항을 참조하세요.
5.  **Save**를 클릭합니다.
6.  브라우저 왼쪽 창에서 **서버 그룹**을 확장합니다. **Azure PostgreSQL 서버**를 선택합니다.
7.  연결된 **서버**를 선택한 다음 그 아래의 **데이터베이스**를 선택합니다. 
8.  **데이터베이스**를 마우스 오른쪽 단추로 클릭하여 데이터베이스를 만듭니다.
9.  데이터베이스 이름 **mypgsqldb**를 선택하고 그에 대한 소유자를 서버 관리자 로그인 **mylogin**으로 선택합니다.
10. **저장**을 클릭하여 빈 데이터베이스를 만듭니다.
11. **브라우저**에서 **서버**를 확장합니다. 만든 서버를 확장하고 그 아래에서 **mypgsqldb** 데이터베이스를 확인합니다.
 ![pgAdmin - 만들기 - 데이터베이스](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>리소스 정리
[Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 삭제하여 이 빠른 시작에서 만든 모든 리소스를 정리합니다.

> [!TIP]
> 이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작을 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.

1.  Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**과 **myresourcegroup**을 차례로 클릭합니다.
2.  리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에 **myresourcegroup**을 입력한 후 삭제를 클릭합니다.

새로 만든 서버만 삭제하려면
1.  Azure Portal의 왼쪽 메뉴에서 PostgreSQL 서버를 클릭한 다음 방금 만든 서버를 검색합니다.
2.  개요 페이지의 맨 위 창에서 삭제 단추를 클릭합니다. ![PostgreSQL용 Azure Database - 서버 삭제](./media/quickstart-create-database-portal/12-delete.png)
3.  삭제할 서버 이름을 확인하고 적용되는 항목 아래 데이터베이스를 표시합니다. 텍스트 상자에 **mypgserver 20170401**을 입력한 다음 삭제를 클릭합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)

