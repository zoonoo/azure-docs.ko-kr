---
title: SQL database를 사용 하 여 Azure Stack에서 | Microsoft Docs
description: SQL Server 리소스 공급자 어댑터를 배포 하는 빠른 단계와 Azure Stack에서 서비스와 SQL database를 배포 하는 방법을 알아봅니다.
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: eccbd502e1fcd50524f7627abf0c7cb675672a98
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182275"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack에 SQL 데이터베이스 사용

SQL Server 리소스 공급자를 사용 하 여 SQL database의 서비스로 제공 [Azure Stack](azure-stack-poc.md)합니다. 리소스 공급자를 설치 하 고 하나 이상의 SQL Server 인스턴스에 연결 후 하 고 사용자가 만들 수 있습니다.

- 클라우드 네이티브 앱에 대 한 데이터베이스입니다.
- SQL을 사용 하는 웹 사이트입니다.
- SQL을 사용 하는 작업입니다.

리소스 공급자는 모든 데이터베이스 관리 기능을 제공 하지 않습니다 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)합니다. 예를 들어, 탄력적 풀 리소스를 자동으로 할당 하는 지원 되지 않습니다. 그러나에서 리소스 공급자에서 지 원하는 유사한 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업에는 SQL Server 데이터베이스입니다. 

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 리소스 공급자 어댑터 아키텍처

리소스 공급자는 다음 구성 요소가 구성 됩니다.

- **SQL 리소스 공급자 어댑터 가상 머신 (VM)**, 공급자 서비스를 실행 하는 Windows Server VM 인 합니다.
- **리소스 공급자**요청을 처리 하 고 데이터베이스 리소스에 액세스 합니다.
- **SQL Server를 호스팅하는 서버**, 데이터베이스에 대 한 용량을 제공 하는 호스팅 서버를 호출 합니다.

SQL Server의 인스턴스를 하나 이상 만들거나 외부 SQL Server 인스턴스에 대 한 액세스를 제공 해야 합니다.

> [!NOTE]
> Azure Stack에 설치 된 서버를 호스팅 통합된 시스템에서 테 넌 트 구독 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. 테 넌 트 포털에서 또는 적절 한 로그인을 사용 하 여 PowerShell을 사용 하 여 생성 되어야 합니다. 모든 호스팅 서버는 청구 가능한 가상 컴퓨터 및 라이선스가 있어야 합니다. 서비스 관리자 테 넌 트 구독 소유자가 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[SQL Server 리소스 공급자 배포](azure-stack-sql-resource-provider-deploy.md)
