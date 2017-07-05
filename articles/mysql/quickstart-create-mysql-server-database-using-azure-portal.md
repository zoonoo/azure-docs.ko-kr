---
title: "빠른 시작: Azure Database for MySQL 서버 만들기 - Azure Portal | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for MySQL 서버 샘플을 만드는 과정을 안내합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 3e0aa1ecccdb55b75dd332818e09b21431716fdc
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal을 사용한 Azure Database for MySQL 서버 만들기
이 문서에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for MySQL 서버를 만드는 과정을 안내합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
먼저 웹 브라우저를 열고 [Microsoft Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기
1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **데이터베이스** 페이지에서 **Azure Database for MySQL**을 선택합니다. 또한 새 페이지 검색 상자에서 **MySQL**을 입력하여 해당 서비스를 찾을 수도 있습니다.
![Azure Portal - 새로 만들기 - 데이터베이스 - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 위 이미지와 같이 다음 정보를 사용하여 새 서버 세부 정보 양식을 채웁니다.

| **설정** | **제안 값** | **필드 설명** |
|---|---|---|
| *서버 이름* | myserver4demo  | 서버 이름은 전역적으로 고유해야 합니다. |
| *구독* | mysubscription | 드롭다운에서 구독을 선택합니다. |
| *리소스 그룹* | myresourcegroup | 리소스 그룹을 만들거나 기존 그룹을 사용합니다. |
| *서버 관리자 로그인* | myadmin | MySQL 엔진에서 관리자가 될 계정 이름을 지정합니다. |
| *암호* |  | 강력한 관리자 계정 암호를 설정합니다. |
| *암호 확인* |  | 관리자 계정 암호를 확인합니다. |
| *위치* |  | 사용 가능한 지역을 선택합니다. |
| *버전* | 5.7 | 최신 버전을 선택합니다. |
| *가격 책정 계층* | 기본, 50 계산 단위, 50 저장소(GB)  | **가격 책정 계층**, **계산 단위**, **저장소(GB)**를 선택한 다음 **확인**을 클릭합니다. |
| *대시보드에 고정* | 확인 | 나중에 쉽게 서버를 찾을 수 있도록 이 확인란을 선택하는 것이 좋습니다. |

   **가격 책정 계층**을 클릭하고 새 데이터베이스의 가격 책정 계층 및 성능 수준을 지정합니다. 이 빠른 시작을 위해 기본 계층, 50개 계산 단위 및 50GB의 포함된 저장소를 선택합니다. 그런 후 **확인**을 클릭하여 가격 책정 계층을 저장합니다.
   
   ![Azure Portal - 필요한 양식 입력을 통해 MySQL 만들기](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   그런 다음에 **만들기**를 클릭합니다. 1, 2분 안에 새 Azure Database for MySQL 서버가 클라우드에서 실행됩니다. 도구 모음에서 **알림**(종 아이콘) 단추를 클릭하여 배포 프로세스를 모니터링합니다.

## <a name="configure-the-firewall"></a>방화벽 구성
처음으로 Azure Database for MySQL에 연결하기 전에 먼저 방화벽을 구성하고 클라이언트의 공용 네트워크 IP 주소(또는 범위)를 허용 목록에 추가합니다.

1. 배포가 완료되면 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 **myserver4demo** 이름을 입력하여 새로 만든 서버를 검색합니다. 검색 결과에 나열된 서버 이름을 클릭합니다. 서버에 대한 개요 페이지가 열리고 추가 구성을 위한 옵션이 제공됩니다.

2. 서버 블레이드에서 **연결 보안**을 선택합니다.

3. **내 IP 추가**를 클릭하여 로컬 컴퓨터의 IP 주소를 추가하거나 IP 주소 범위를 구성합니다. 규칙을 만든 후에 **저장**을 클릭해야 합니다.
  ![Azure Portal - 방화벽 규칙 추가 및 저장](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>연결 정보 가져오기
Azure Portal에서 Azure MySQL 서버의 정규화된 도메인 이름을 가져옵니다. 정규화된 도메인 이름을 사용하여 **mysql.exe** 명령줄 도구를 통해 서버에 연결합니다.

1.  [Azure Portal](https://portal.azure.com/)의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 Azure Database for MySQL 서버를 클릭합니다.

2.  **속성**을 클릭합니다. **서버 이름** 및 **서버 관리자 로그인**을 기록해 둡니다.
이 예제에서 서버 이름은 *myserver4demo.mysql.database.azure.com*이고, 서버 관리자 로그인은 *myadmin@myserver4demo*입니다.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>mysqlexe 명령줄 도구를 사용하여 서버에 연결
[mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)를 사용하여 Azure Database for MySQL 서버로의 연결을 설정합니다. Azure Cloud Shell을 사용하여 브라우저에서 mysql 명령줄 도구를 실행하거나 로컬로 설치된 mysql 도구를 사용하여 사용자 고유의 컴퓨터에서 시작할 수 있습니다. Azure Cloud Shell을 시작하려면 이 문서의 코드 블록에서 `Try It` 단추를 클릭하거나 [Azure Portal](https://portal.azure.com)을 방문하고 도구 모음 오른쪽 위에서 `>_` 아이콘을 클릭합니다. 

1. 연결할 명령을 입력합니다.
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. 서버 상태를 확인하여 연결이 작동하는지 확인합니다. 연결된 후에는 mysql> 프롬프트에서 `status`를 입력합니다.
```sql
status
```

   ![명령 프롬프트 - mysql 명령줄 예](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > 다른 명령은 [MySQL 5.7 참조 설명서 - 4.5.1장](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)을 참조하세요.

3. mysql> 프롬프트에서 `CREATE DATABASE` 명령을 입력하여 빈 데이터베이스를 만듭니다.

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   Azure Database for MySQL 서버 내에서 하나 이상의 데이터베이스를 포함할 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 만들 수 있는 데이터베이스의 수에는 제한이 없으나 여러 데이터베이스가 동일한 서버 리소스를 공유하는 것입니다.  

4. mysql> 프롬프트에서 `SHOW DATABASES` 명령을 입력하여 데이터베이스를 나열합니다.

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI 도구로 서버에 연결
1.  클라이언트 컴퓨터에서 MySQL Workbench 응용 프로그램을 실행합니다. MySQL Workbench는 [여기](https://dev.mysql.com/downloads/workbench/)에서 다운로드할 수 있습니다.

2.  **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

   ![새 연결 설정](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **설정** | **제안 값** | **필드 설명** |
|---|---|---|
|   *연결 이름* | 데모 연결| 이 연결에 대한 레이블을 지정합니다. |
| *연결 방법* | 표준(TCP/IP) | 표준(TCP/IP)이면 충분합니다. |
| *호스트 이름* | myserver4demo.mysql.database.azure.com | 서버에 대해 정규화된 서버 이름을 사용합니다. |
| *포트* | 3306 | 기본 포트는 3306입니다. |
| *사용자 이름* | myadmin@myserver4demo  | @ 문자와 서버 이름으로 이전에 설명한 서버 관리자 로그인을 사용합니다. |
| *암호* | 사용자 암호 | [Store in Vault(자격 증명 모음에 저장)...] 단추를 클릭하여 암호를 저장합니다. |

**연결 테스트**를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다. [확인]을 클릭하여 연결을 저장합니다. 

> [!NOTE]
> 서버에서 기본적으로 적용되는 SSL은 연결의 성공을 위해 추가적인 구성이 필요합니다. [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.  이 빠른 시작에 대해 SSL을 사용하지 않도록 하려면 Azure Portal을 방문하고 연결 보안 페이지를 클릭하여 SSL 연결 적용 토글 단추를 사용하지 않도록 설정합니다.

## <a name="clean-up-resources"></a>리소스 정리
[Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 삭제하여 이 빠른 시작에서 만든 모든 리소스를 정리합니다.

> [!TIP]
> 이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작을 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.

1.  Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**과 **myresourcegroup**을 차례로 클릭합니다.
2.  리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에 **myresourcegroup**을 입력한 후 삭제를 클릭합니다.

새로 만든 서버만 삭제하려면:
1.  Azure Portal의 왼쪽 메뉴에서 PostgreSQL 서버를 클릭한 다음 방금 만든 서버를 검색합니다.
2.  개요 페이지의 맨 위 창에서 삭제 단추를 클릭합니다. ![Azure Database for MySQL - 서버 삭제](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  삭제할 서버 이름을 확인하고 적용되는 항목 아래 데이터베이스를 표시합니다. 텍스트 상자에 **myserver4demo**를 입력한 다음 [삭제]를 클릭합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 Azure Database for MySQL 데이터베이스 디자인](./tutorial-design-database-using-portal.md)


