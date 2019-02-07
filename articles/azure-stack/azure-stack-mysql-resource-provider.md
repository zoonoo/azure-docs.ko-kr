---
title: MySQL 데이터베이스를 사용 하 여 Azure Stack에 PaaS로 | Microsoft Docs
description: MySQL 리소스 공급자 배포 서비스로 Azure Stack에서 MySQL 데이터베이스를 제공 하는 방법에 대해 알아봅니다.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 6922571e4e816d04f6e116db582922cecc85542c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765469"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack에서 MySQL 데이터베이스를 사용 합니다.

MySQL 데이터베이스 websites에 공통적인 및 다양 한 웹 사이트 플랫폼을 지원 합니다. 예를 들어 WordPress 웹 사이트 서비스 (PaaS) 추가 기능으로 Web Apps 플랫폼을 사용 하 여 만들 수 있습니다.

리소스 공급자를 배포한 후에 다음을 수행할 수 있습니다.

* MySQL 서버 및 Azure Resource Manager 배포 템플릿을 사용 하 여 데이터베이스를 만듭니다.
* MySQL 데이터베이스를 서비스로 제공 합니다.  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 리소스 공급자 어댑터 아키텍처

리소스 공급자에는 다음 구성 요소에 있습니다.

* **MySQL 리소스 공급자 어댑터 가상 머신 (VM)**, 공급자 서비스를 실행 하는 Windows Server VM 인 합니다.
* **리소스 공급자**요청을 처리 하 고 데이터베이스 리소스에 액세스 합니다.
* **MySQL 서버를 호스팅하는 서버**, 호스팅 서버 호출 되는 데이터베이스에 대 한 용량을 제공 하는 합니다. MySQL 인스턴스를 직접 만들 수도 있고 외부 MySQL 인스턴스에 대 한 액세스를 제공할 수 있습니다. 합니다 [Azure Stack 빠른 시작 갤러리](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) 에를 사용할 수 있는 예제 템플릿:

  * MySQL 서버를 만듭니다.
  * 다운로드 하 고 Azure Marketplace에서 MySQL 서버를 배포 합니다.

> [!NOTE]
> Azure Stack에 설치 된 서버를 호스팅 통합된 시스템에서 테 넌 트 구독 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. 테 넌 트 포털에서 또는 적절 한 로그인을 사용 하 여 PowerShell 세션에서 생성 되어야 합니다. 모든 호스팅 서버는 청구 가능한 Vm 및 라이선스가 있어야 합니다. 서비스 관리자 테 넌 트 구독 소유자가 될 수 있습니다.

### <a name="required-privileges"></a>필요한 권한

시스템 계정에는 다음 권한이 있어야 합니다.

* **데이터베이스:** 만들기, 삭제
* **로그인:** 만들기, 설정, 삭제, 권한을 부여, 취소  

## <a name="next-steps"></a>다음 단계

[MySQL 리소스 공급자 배포](azure-stack-mysql-resource-provider-deploy.md)
