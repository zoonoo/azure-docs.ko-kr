---
title: Azure SQL Data Warehouse를 이용한 Fivetran 빠른 시작 | Microsoft Docs
description: Fivetran 및 Azure SQL Data Warehouse로 빨리 시작합니다.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355295"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Fivetran 및 SQL Data Warehouse로 빠른 시작

이 빠른 SQL Data Warehouse의 기존 인스턴스가 이미 있다고 가정합니다.

## <a name="setup-connection"></a>연결 설정

1. Azure SQL Data Warehouse에 연결하기 위한 정규화된 서버 이름 및 데이터베이스 이름을 찾습니다.

   [포털에서 서버 이름과 데이터베이스 이름을 찾는 방법?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. 설치 마법사에서 데이터베이스를 직접 연결할지 또는 SSH 터널을 통해 연결할지 결정합니다.

   데이터베이스에 직접 연결하기로 결정하는 경우 액세스를 허용하는 방화벽 규칙을 만들어야 합니다. 이 방법이 가장 간단하고 가장 안전한 방법입니다.

   SSH 터널을 통해 연결하기로 결정하는 경우 Fivetran은 네트워크 내에서 데이터베이스에 SSH 터널을 제공하는 별도의 서버에 연결합니다. 이 방법은 데이터베이스가 가상 네트워크에서 액세스할 수 없는 서브넷에 있는 경우 필요합니다.

3. Fivetran에서 Azure SQL Data Warehouse에 들어오는 연결을 허용하도록 서버 수준 방화벽에서 IP 주소 "52.0.2.4"를 추가합니다.

   [서버 수준 방화벽을 추가하는 방법?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>SSH 사용자 자격 증명

SQL Server Management Studio 또는 선택한 도구를 서버 관리 사용자로 사용하여 Azure SQL Data Warehouse에 연결하고 다음 SQL 문을 실행하여 Fivetran에 대한 사용자를 만듭니다.

master 데이터베이스의 경우: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

SQL Data Warehouse 데이터베이스의 경우:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

사용자 Fivetran이 만들어진 후 웨어하우스에 다음 사용 권한을 부여합니다.

```
GRANT CONTROL to fivetran;
```

columnstore 인덱스 생성을 위한 메모리 요구 사항에 따라 만들어진 사용자에게 적합한 리소스 클래스를 추가합니다. 예를 들어 Marketo 및 Salesforce와 같은 통합에는 다수의 열/더 많은 양의 데이터 때문에 columnstore 인덱스를 만들려면 더 많은 메모리가 요구되므로 더 높은 리소스 클래스가 필요합니다.

정적 리소스 클래스를 사용하는 것이 좋습니다. 사용하는 성능 수준과 상관없이 사용자에게 200 MB를 할당하는 리소스 클래스 `staticrc20`으로 시작할 수 있습니다. 현재 리소스 클래스로 columnstore 인덱싱이 실패하면 리소스 클래스를 늘려야 합니다.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

자세한 내용은 [메모리 및 동시성 한도](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) 및 [리소스 클래스](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)에 대한 문서를 참조하세요.

PolyBase를 사용하여 Blob Storage에서 파일을 로드하는 동안 사용할 데이터베이스 범위 자격 증명을 만들려면 CONTROL 사용 권한이 필요합니다.

Azure SQL Data Warehouse에 액세스할 자격 증명 입력

1. 호스트(서버 이름)
2. 포트
3. 데이터베이스
4. 사용자(사용자 이름은 `fivetran@<server_name>`이어야 하며, 여기서 `<server_name>`은 Azure 호스트 URI의 일부인: `<server_name>.database.windows.net`입니다.)
5. 암호