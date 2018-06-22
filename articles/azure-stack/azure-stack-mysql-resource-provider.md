---
title: MySQL 데이터베이스를 사용 하 여 Azure 스택에 PaaS로 | Microsoft Docs
description: MySQL 리소스 공급자를 배포 Azure 스택에 서비스로 MySQL 데이터베이스를 제공 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310051"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>MySQL 데이터베이스를 사용 하 여 Microsoft Azure 스택

MySQL 리소스 공급자 API를 Azure 스택에 배포 하는 MySQL 데이터베이스를 사용 하도록 배포할 수 있습니다. 리소스 공급자 API에 대 한 자세한 내용은 참조 [Windows Azure 팩 MySQL 리소스 공급자 REST API 참조](https://msdn.microsoft.com/library/dn528442.aspx)합니다.

MySQL 데이터베이스는 웹 사이트에 일반적인 이며 많은 웹 사이트 플랫폼을 지원 합니다. 예를 들어 웹 응용 프로그램 플랫폼 서비스 (PaaS) 추가 기능으로 사용 하 여 WordPress 웹 사이트를 만들 수 있습니다.

리소스 공급자를 배포한 후에 다음을 수행할 수 있습니다.

* MySQL server 및 Azure 리소스 관리자 배포 템플릿을 사용 하 여 데이터베이스를 만듭니다.
* MySQL 데이터베이스를 서비스로 제공 합니다.  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 리소스 공급자 어댑터 아키텍처

리소스 공급자에는 다음 구성 요소가 있습니다.

* **MySQL 리소스 공급자 어댑터 가상 컴퓨터 (VM)**, 공급자 서비스를 실행 하는 Windows Server VM 인 합니다.
* **리소스 공급자**요청을 처리 하는, 및 데이터베이스 리소스에 액세스 합니다.
* **MySQL 서버를 호스팅하는 서버**, 호스팅 서버 라고 하는 데이터베이스에 대 한 용량을 제공 하는 합니다. MySQL 인스턴스를 직접 만들 수도 있고 외부 MySQL 인스턴스에 대 한 액세스를 제공할 수 있습니다. [Azure 스택 퀵 스타트 갤러리](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) 에를 사용할 수 있는 예제 템플릿:

  * MySQL 서버를 만듭니다.
  * 다운로드 하 고 Azure Marketplace에서 MySQL 서버를 배포 합니다.

> [!NOTE]
> Azure 스택에 설치 되어 있는 서버를 호스팅 통합형된 시스템 테 넌 트 구독에서 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. 적절 한 로그인을 사용 하 여 PowerShell 세션을 또는 테 넌 트 포털에서 만들 수 있어야 합니다. 모든 호스팅 서버가 청구 한 대의 vm 및 라이선스를 보유 해야 합니다. 서비스 관리자의 테 넌 트 구독 소유자가 될 수 있습니다.

### <a name="required-privileges"></a>필요한 권한

시스템 계정에는 다음 권한이 있어야 합니다.

* **데이터베이스:** 만들기, 삭제
* **로그인:** 만들기, 설정, 삭제, 권한을 부여, 취소  

## <a name="next-steps"></a>다음 단계

[MySQL 리소스 공급자를 배포 합니다.](azure-stack-mysql-resource-provider-deploy.md)
