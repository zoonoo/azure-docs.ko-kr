---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728564"
---
## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

 [Azure 포털](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>빈 SQL 데이터베이스 만들기

Azure SQL 데이터베이스는 정의된 [컴퓨팅 및 스토리지 리소스](../articles/sql-database/sql-database-service-tiers-dtu.md)의 세트 내에 있습니다. 데이터베이스는 [Azure 리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md) 및 [Azure SQL 데이터베이스 논리 서버](../articles/sql-database/sql-database-features.md)에서 작동합니다.

빈 SQL Database를 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

1. **새로 만들기** 페이지에서 **데이터베이스** > **SQL Database**를 선택합니다.

   ![빈 데이터베이스 만들기](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. **SQL Database** 창에서 다음 값을 입력하거나 선택합니다.

   | 설정       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **데이터베이스 이름** | *yourDatabase* | 유효한 데이터베이스 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
   | **구독** | *yourSubscription*  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   | **리소스 그룹** | *yourResourceGroup* | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **원본 선택** | 빈 데이터베이스 | 빈 데이터베이스를 만들도록 지정합니다. |

   ![데이터베이스 만들기](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. **서버**를 선택하여 새 데이터베이스에 대한 서버를 구성합니다. 그런 다음, 다음 값을 입력하거나 선택합니다.

      | 설정       | 제안 값 | 설명 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
      | **서버 관리자 로그인** | 모든 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요.|
      | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 사용해야 합니다. |
      | **위치**: | 모든 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |

      **선택**을 선택합니다.

      ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. **가격 책정 계층**을 선택하여 서비스 계층, DTU 수 및 스토리지 크기를 지정합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU 및 스토리지에 대한 옵션을 살펴봅니다.

      서버 계층, DTU 수 및 스토리지 크기를 선택한 후에 **적용**을 선택합니다.

   1. 빈 데이터베이스에 대한 **데이터 정렬**을 입력합니다(이 자습서의 경우 기본값 사용). 데이터 정렬에 대한 자세한 내용은 [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

1. 이제 **SQL Database** 양식을 완료했으므로 **만들기**를 선택하여 데이터베이스를 만듭니다. 이 단계를 완료하는 데 1분 30초가 걸릴 수 있습니다.

1. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

     ![알림](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>방화벽 규칙 만들기

SQL 데이터베이스 서비스는 외부 애플리케이션 및 도구가 서버 또는 서버의 데이터베이스에 연결되지 않도록 방지하는 서버 수준에 방화벽을 만듭니다. 다음 단계에 따라 클라이언트의 IP 주소에 대해 [SQL 데이터베이스 서버 수준 방화벽 규칙](../articles/sql-database/sql-database-firewall-configure.md)을 만듭니다. 이 프로세스는 IP 주소에 대한 SQL 데이터베이스 방화벽을 통해서만 외부 연결을 활성화합니다.

> [!NOTE]
> SQL 데이터베이스는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 관리자가 1433 포트를 열지 않으면 Azure SQL 데이터베이스 서버에 연결할 수 없습니다.

1. 배포가 완료되면 왼쪽 메뉴에서 **SQL 데이터베이스**를 선택한 다음, **SQL 데이터베이스** 페이지에서 *yourDatabase*를 선택합니다. 정규화된 서버 이름(예: *yourserver.database.windows.net*)을 표시하고 추가 구성 옵션을 제공하는 데이터베이스 **개요** 페이지가 열립니다.

1. 이후 단계에서 서버 및 해당 데이터베이스에 연결하는 데 사용할 수 있도록 정규화된 서버 이름을 복사합니다.

   ![서버 이름](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. 도구 모음에서 **서버 방화벽 설정**을 선택합니다. SQL 데이터베이스 서버의 **방화벽 설정** 페이지가 열립니다.

   ![서버 방화벽 규칙](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. 도구 모음에서 **클라이언트 IP 추가**를 선택하여 현재 IP 주소를 새 방화벽 규칙에 추가합니다. 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

   1. **저장**을 선택합니다. 논리 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 방화벽 규칙이 생성됩니다.

   1. **확인**을 선택한 다음, **방화벽 설정** 페이지를 닫습니다.

이제 방화벽을 통해 IP 주소를 전달하고 SSMS 또는 원하는 다른 도구를 사용하여 SQL 데이터베이스 서버 및 해당 데이터베이스에 연결할 수 있습니다. 이전에 만든 서버 관리자 계정을 사용해야 합니다.

> [!IMPORTANT]
> SQL 데이터베이스 방화벽을 통한 액세스는 기본적으로 모든 Azure 서비스에 대해 사용됩니다. 이 페이지에서 **끄기**를 선택하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.
