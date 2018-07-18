---
title: SQL 데이터베이스를 사용 하 여 Azure 스택에 | Microsoft Docs
description: SQL 데이터베이스에서 SQL Server 리소스 공급자 어댑터를 배포 하는 Azure 스택 및 빠른 단계 서비스로 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307828"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL 데이터베이스를 사용 하 여 Microsoft Azure 스택

SQL Server 리소스 공급자 어댑터 API를 사용 하 여 SQL 데이터베이스의 서비스로 노출할 [Azure 스택](azure-stack-poc.md)합니다. 리소스 공급자를 설치 하 고 하나 이상의 SQL Server 인스턴스에 연결 하 고 사용자가 만들 수 있습니다.:

- 클라우드-네이티브 앱에 대 한 데이터베이스입니다.
- SQL을 사용 하는 웹 사이트입니다.
- SQL을 사용 하는 작업입니다.

리소스 공급자는 모든 데이터베이스의 관리 기능이 제공 하지 않습니다 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)합니다. 예를 들어 탄력적 풀 리소스를 자동으로 할당 하는 지원 되지 않습니다. 그러나에서 리소스 공급자에서 지 원하는 유사한 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업에는 SQL Server 데이터베이스입니다. 리소스 공급자 API에 대 한 자세한 내용은 참조 [Windows Azure 팩 SQL Server 리소스 공급자 REST API 참조](https://msdn.microsoft.com/library/dn528529.aspx)합니다.

>[!NOTE]
SQL Server 리소스 공급자 API는 Azure SQL 데이터베이스와 호환 되지 않습니다.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 리소스 공급자 어댑터 아키텍처

리소스 공급자는 다음 구성 요소가 구성 됩니다.

- **SQL 리소스 공급자 어댑터 가상 컴퓨터 (VM)**, 공급자 서비스를 실행 하는 Windows Server VM 인 합니다.
- **리소스 공급자**요청을 처리 하는, 및 데이터베이스 리소스에 액세스 합니다.
- **SQL Server를 호스트 하는 서버**, 데이터베이스에 대 한 용량을 제공 하는 호스팅 서버를 호출 합니다.

SQL Server의 인스턴스를 하나 이상 만들거나 외부 SQL Server 인스턴스에 대 한 액세스를 제공 해야 합니다.

> [!NOTE]
> Azure 스택에 설치 되어 있는 서버를 호스팅 통합형된 시스템 테 넌 트 구독에서 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. PowerShell 사용 하 여 적절 한 로그인 또는 테 넌 트 포털에서 만들 수 해야 합니다. 모든 호스팅 서버가 청구 가능한 가상 컴퓨터 및 라이선스를 보유 해야 합니다. 서비스 관리자의 테 넌 트 구독 소유자가 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[SQL Server 리소스 공급자를 배포 합니다.](azure-stack-sql-resource-provider-deploy.md)
