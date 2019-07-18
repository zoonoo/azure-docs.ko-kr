---
title: 첫 번째 관계형 데이터베이스 디자인 - C# - Azure SQL Database| Microsoft Docs
description: Azure SQL Database에서 C#과 ADO.NET을 사용하여 단일 데이터베이스의 첫 번째 관계형 데이터베이스를 디자인하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 02/08/2019
ms.openlocfilehash: 31246f44be5645715c5c7041d0cf9bcff9c0fa52
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303295"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>자습서: Azure SQL Database C&#x23; 및 ADO.NET 내에서 단일 데이터베이스의 관계형 데이터베이스 디자인

Azure SQL 데이터베이스는 Microsoft Cloud(Azure)의 관계형 DBaaS(Database-As-A-Service)입니다. 이 자습서에서는 Visual Studio에서 Azure Portal 및 ADO.NET을 사용하여 다음과 같은 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 단일 데이터베이스 만들기*
> * Azure Portal을 사용하여 서버 수준 IP 방화벽 규칙 설정
> * ADO.NET 및 Visual Studio를 사용하여 데이터베이스에 연결
> * ADO.NET을 사용하여 테이블 만들기
> * ADO.NET을 사용하여 데이터 삽입, 업데이트 및 삭제
> * ADO.NET 데이터 쿼리

*Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

[Visual Studio 2019](https://www.visualstudio.com/downloads/) 이상 설치

## <a name="create-a-blank-single-database"></a>빈 단일 데이터베이스 만들기

Azure SQL Database의 단일 데이터베이스는 정의된 컴퓨팅 및 스토리지 리소스 세트를 사용하여 생성됩니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에 만들어지고 [데이터베이스 서버](sql-database-servers.md)를 사용하여 관리됩니다.

빈 단일 데이터베이스를 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **새로 만들기** 페이지의 Azure Marketplace 섹션에서 **데이터베이스**를 선택한 다음, **추천** 섹션에서 **SQL Database**를 클릭합니다.

   ![빈 데이터베이스 만들기](./media/sql-database-design-first-database/create-empty-database.png)

3. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 **SQL Database** 형식을 작성합니다.

    | 설정       | 제안 값 | 설명 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **데이터베이스 이름** | *yourDatabase* | 유효한 데이터베이스 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
    | **구독** | *yourSubscription*  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
    | **리소스 그룹** | *yourResourceGroup* | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
    | **원본 선택** | 빈 데이터베이스 | 빈 데이터베이스를 만들도록 지정합니다. |

4. **서버**를 클릭하여 기존 데이터베이스 서버를 사용하거나, 새 데이터베이스 서버를 만들어서 구성합니다. 기존 서버를 선택하거나 **새 서버 만들기**를 클릭하고 **새 서버** 양식에 다음 정보를 입력합니다.

    | 설정       | 제안 값 | 설명 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
    | **서버 관리자 로그인** | 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
    | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 사용해야 합니다. |
    | **위치**: | 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. **선택**을 클릭합니다.
6. **가격 책정 계층**을 클릭하여 서비스 계층, DTU나 vCore 개수 및 저장소 크기를 지정합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU/vCore 및 스토리지 수에 대한 옵션을 살펴볼 수 있습니다.

    서버 계층, DTU 또는 vCore 수, 스토리지 양을 선택한 후 **적용**을 클릭합니다.

7. 빈 데이터베이스에 대한 **데이터 정렬**을 입력합니다(이 자습서의 경우 기본값 사용). 데이터 정렬에 대한 자세한 내용은 [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

8. **SQL Database** 양식을 완료했으면, **만들기**를 클릭하여 단일 데이터베이스를 프로비저닝합니다. 이 단계를 완료하는 데 몇 분이 걸릴 수 있습니다.

9. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

   ![알림](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>서버 수준 IP 방화벽 규칙 만들기

SQL Database 서비스는 서버 수준에서 IP 방화벽을 만듭니다. 방화벽 규칙에서 특정 IP가 방화벽을 통과하도록 허용하지 않는 한 이 방화벽은 외부 애플리케이션과 도구가 서버 및 서버의 데이터베이스에 연결하지 못하게 차단합니다. 단일 데이터베이스에 대한 외부 연결을 사용하려면 먼저 IP 주소(또는 IP 주소 범위)에 대한 IP 방화벽 규칙을 추가해야 합니다. 다음 단계에 따라 [SQL Database 서버 수준 IP 방화벽 규칙](sql-database-firewall-configure.md)을 만듭니다.

> [!IMPORTANT]
> SQL Database 서비스는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 이 서비스에 연결을 시도하면 포트 1433을 통한 아웃바운드 트래픽을 네트워크 방화벽에서 허용하지 않을 수 있습니다. 이 경우 관리자가 1433 포트를 열지 않으면 단일 데이터베이스에 연결할 수 없습니다.

1. 배포가 완료되면 왼쪽 메뉴에서 **SQL 데이터베이스**를 클릭한 다음, **SQL 데이터베이스** 페이지에서 *yourDatabase*를 클릭합니다. 정규화된 **서버 이름**(예: *yourserver.database.windows.net*)을 표시하고 추가 구성 옵션을 제공하는 데이터베이스 개요 페이지가 열립니다.

2. SQL Server Management Studio에서 서버 및 데이터베이스에 연결하는 데 사용할 수 있도록 이 정규화된 서버 이름을 복사합니다.

   ![서버 이름](./media/sql-database-design-first-database/server-name.png)

3. 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다.

   ![서버 수준 IP 방화벽 규칙](./media/sql-database-design-first-database/server-firewall-rule.png)

4. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 현재 IP 주소를 새 IP 방화벽 규칙에 추가합니다. IP 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

5. **저장**을 클릭합니다. SQL Database 서버에서 1433 포트를 여는 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

6. **확인**을 클릭한 후 **방화벽 설정** 페이지를 닫습니다.

이제 IP 주소가 IP 방화벽을 통과할 수 있습니다. 이제 SQL Server Management Studio 또는 원하는 다른 도구를 사용하여 단일 데이터베이스에 연결할 수 있습니다. 이전에 만든 서버 관리자 계정을 사용해야 합니다.

> [!IMPORTANT]
> 기본적으로 모든 Azure 서비스에는 SQL Database IP 방화벽을 통한 액세스가 사용됩니다. 이 페이지에서 **끄기**를 클릭하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 데이터베이스 및 테이블 만들기, 데이터베이스에 연결, 데이터 로드 및 쿼리 실행과 같은 기본적인 데이터베이스 작업에 대해 배웠습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터베이스 만들기
> * 방화벽 규칙 설정
> * [Visual Studio 및 C#](sql-database-connect-query-dotnet-visual-studio.md)을 사용하여 데이터베이스에 연결
> * 테이블 만들기
> * 데이터 삽입, 업데이트, 삭제 및 쿼리

데이터 마이그레이션에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [DMS를 사용하여 오프라인에서 SQL Server를 Azure SQL Database로 마이그레이션](../dms/tutorial-sql-server-to-azure-sql.md)
