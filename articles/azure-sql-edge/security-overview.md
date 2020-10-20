---
title: Azure SQL Edge 보안
description: Azure SQL Edge의 보안에 대 한 자세한 정보
keywords: SQL Edge, 보안
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 737c7e61a7ae0573ca6de0d6daa8288313f70741
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201905"
---
# <a name="securing-azure-sql-edge"></a>Azure SQL Edge 보안

산업 전반에서 IoT 및 Edge 컴퓨팅을 채택 하는 경우에는 장치 수와 이러한 장치에서 생성 되는 데이터가 증가 합니다. 데이터의 양과 장치 끝점의 수가 증가 하면 데이터와 장치의 보안 측면에서 상당한 과제가 발생 합니다. 

Azure SQL Edge는 SQL Server 데이터베이스 내에서 IoT 데이터를 비교적 쉽게 보호할 수 있도록 하는 여러 기능과 기능을 제공 합니다. Azure SQL Edge는 동일한 보안 기능을 공유 하는 동일한 보안 기능을 공유 하는 동일한 SQL Microsoft SQL Server 엔진을 사용 하 여 구축 되어 클라우드에서 동일한 보안 정책 및 사례를 더 쉽게 확장할 수 있습니다.

Microsoft SQL Server 및 Azure SQL과 마찬가지로, Azure SQL Edge 배포의 보안을 유지 하는 것은 플랫폼, 인증, 개체 (데이터 포함) 및 시스템에 액세스 하는 응용 프로그램의 네 가지 영역과 관련 된 일련의 단계로 볼 수 있습니다. 

## <a name="platform-and-system-security"></a>플랫폼 및 시스템 보안

Azure SQL Edge에 대 한 플랫폼에는 물리적 docker 호스트, 호스트의 운영 체제 및 물리적 장치를 응용 프로그램 및 클라이언트에 연결 하는 네트워킹 시스템이 포함 됩니다. 

플랫폼 보안 구현은 인증 되지 않은 사용자를 네트워크에서 분리 하 여 시작 합니다. 몇 가지 모범 사례에는 다음이 포함 되지만이에 국한 되지 않습니다.
- 조직 보안 정책을 위해 방화벽 규칙을 구현 합니다. 
- 물리적 장치의 운영 체제에 최신 보안 업데이트가 적용 되었는지 확인 합니다. 
- Azure SQL Edge에를 사용 하는 호스트 포트 지정 및 제한
- Azure SQL Edge 데이터를 호스트 하는 모든 데이터 볼륨에 적절 한 액세스 제어가 적용 되도록 합니다. 

Azure SQL Edge 네트워크 프로토콜 및 TDS 끝점에 대 한 자세한 내용은 [네트워크 프로토콜 및 Tds 끝점](https://docs.microsoft.com//previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105))을 참조 하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여 

### <a name="authentication"></a>인증  
인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Edge는 현재 메커니즘도 지원 `SQL Authentication` 합니다.

- *SQL 인증*:

    SQL 인증은 사용자 이름 및 암호를 사용 하 여 Azure SQL Edge에 연결할 때 사용자의 인증을 나타냅니다. Sql Edge 배포 중에 SQL **sa** 로그인 암호를 지정 해야 합니다. 그리고 나면 서버 관리자는 추가 SQL 로그인 및 사용자를 만들 수 있으며, 그러면 사용자가 사용자 이름과 암호를 사용하여 연결할 수 있습니다.

    SQL Edge에서 로그인 및 사용자를 만들고 관리 하는 방법에 대 한 자세한 내용은 [로그인 만들기](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) 및 [데이터베이스 사용자 만들기](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-database-user)를 참조 하세요.

### <a name="authorization"></a>권한 부여   

권한 부여는 Azure SQL Edge의 데이터베이스 내에서 사용자에 게 할당 된 사용 권한을 나타내며 사용자가 수행할 수 있는 작업을 결정 합니다. 권한은 [데이터베이스 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) 에 사용자 계정을 추가 하 고 해당 역할에 데이터베이스 수준 사용 권한을 할당 하거나 사용자에 게 특정 [개체 수준 사용 권한을](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)부여 하 여 제어 됩니다. 자세한 내용은 [로그인 및 사용자](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)를 참조 하세요.

필요한 경우 사용자 지정 역할을 만드는 것이 가장 좋습니다. 작업 기능을 수행 하는 데 필요한 최소한의 권한만 가진 사용자를 역할에 추가 합니다. 사용자에 게 직접 사용 권한을 할당 하지 마십시오. 서버 관리자 계정은 다양 한 권한이 있는 기본 제공 db_owner 역할의 구성원이 며, 관리 업무를 사용 하는 소수의 사용자 에게만 부여 되어야 합니다. 응용 프로그램의 경우 [EXECUTE AS](https://docs.microsoft.com/sql/t-sql/statements/execute-as-clause-transact-sql) 를 사용 하 여 호출 된 모듈의 실행 컨텍스트를 지정 하거나 제한 된 권한으로 [응용 프로그램 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) 을 사용 합니다. 이렇게 하면 데이터베이스에 연결 하는 응용 프로그램에 응용 프로그램에 필요한 최소한의 권한만 부여 됩니다. 이러한 모범 사례를 따르면 발전시키도 구분 됩니다.

## <a name="database-object-security"></a>데이터베이스 개체 보안

보안 주체는 SQL Edge에 대 한 액세스 권한을 부여 받은 개인, 그룹 및 프로세스입니다. "보안 개체"는 서버, 데이터베이스 및 데이터베이스에 포함된 개체입니다. 각에는 노출 영역을 줄이는 데 도움이 되도록 구성할 수 있는 권한 집합이 있습니다. 다음 표에서는 보안 주체 및 보안 개체에 대한 자세한 정보를 제공합니다.

|원하는 정보|참조 항목|  
|---------------------------|---------|  
|서버와 데이터베이스 사용자, 역할 및 프로세스|[보안 주체 데이터베이스 엔진](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|서버 및 데이터베이스 개체 보안|[보안 개체](https://docs.microsoft.com/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>암호화 및 인증서  
 
암호화를 통해 액세스 제어 문제를 해결할 수는 없습니다. 그러나 암호화를 사용하면 드물게 발생하긴 하지만 액세스 제어가 무시되는 경우에도 데이터 손실을 제한하여 보안이 향상됩니다. 예를 들어 데이터베이스 호스트 컴퓨터가 잘못 구성되어 악의적인 사용자가 신용 카드 번호와 같은 중요한 데이터를 얻는 경우 해당 정보가 암호화되어 있으면 도난 당한 정보를 사용할 수 없을 수도 있습니다. 다음 표에는 Azure SQL Edge의 암호화에 대 한 자세한 정보가 나와 있습니다.  
  
|원하는 정보|참조 항목|  
|---------------------------|---------|  
|안전한 연결 구현|[연결 암호화](https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections)|  
|암호화 함수|[암호화 함수&#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|휴지 상태의 데이터 암호화|[투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> [SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-security-overview) 에 대해 설명 된 보안 제한 사항은 Azure SQL Edge에도 적용 됩니다. 


> [!NOTE]
> Azure SQL Edge에는 mssql 회의 유틸리티가 포함 되지 않습니다. 암호화 관련 구성을 비롯 한 모든 구성은 [mssql 파일](configure.md#configure-by-using-an-mssqlconf-file) 또는 [환경 변수](configure.md#configure-by-using-environment-variables)를 통해 수행 해야 합니다. 


Azure sql 및 Microsoft SQL Server와 유사 하 게 Azure SQL Edge는 인증서를 만들고 사용 하 여 개체 및 연결 보안을 향상 하는 동일한 메커니즘을 제공 합니다. 자세한 내용은 [CREATE CERTIFICATE (transact-sql)](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql)를 참조 하세요.


## <a name="application-security"></a>애플리케이션 보안

### <a name="client-programs"></a>클라이언트 프로그램

Azure SQL Edge 보안 모범 사례에는 보안 클라이언트 응용 프로그램 작성이 포함 됩니다. 네트워킹 계층에서 클라이언트 애플리케이션의 보안을 설정하는 방법은 [Client Network Configuration](https://docs.microsoft.com/sql/database-engine/configure-windows/client-network-configuration)을 참조하십시오.

### <a name="security-catalog-views-and-functions"></a>보안 카탈로그 뷰 및 함수  
보안 정보는 성능 및 유틸리티에 대해 최적화 된 여러 가지 뷰와 함수에서 제공 됩니다. 다음 표에는 Azure SQL Edge의 보안 뷰 및 함수에 대 한 정보가 포함 되어 있습니다.  
  
|함수 및 뷰|링크|  
|---------------------------|---------|  
|데이터베이스 수준 및 서버 수준 사용 권한, 보안 주체, 역할 등에 대 한 정보를 반환 하는 보안 카탈로그 뷰. 암호화 키, 인증서 및 자격 증명에 대한 정보를 제공하는 카탈로그 뷰도 있습니다.|[보안 카탈로그 뷰&#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|현재 사용자, 사용 권한 및 스키마에 대 한 정보를 반환 하는 보안 기능입니다.|[보안 함수&#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/functions/security-functions-transact-sql)|  
|보안 동적 관리 뷰|[보안 관련 동적 관리 뷰 및 함수&#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>감사 

Azure SQL Edge는 SQL Server와 동일한 감사 메커니즘을 제공 합니다. 자세한 내용은 [SQL Server 감사 (데이터베이스 엔진)](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [보안 기능 시작](https://docs.microsoft.com/sql/linux/sql-server-linux-security-get-started)
- [루트가 아닌 사용자로 Azure SQL Edge 실행](configure.md#run-azure-sql-edge-as-non-root-user)
- [IoT에 대 한 Azure Security Center](https://docs.microsoft.com/azure/asc-for-iot/overview)

