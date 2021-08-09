---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 부록 B: ARM 템플릿'
description: 이 템플릿은 프라이빗 엔드포인트로 모든 리소스를 배포합니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 07e689b8458650ca39a0aa949504dd762621bac1
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656905"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix-b-arm-templates"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 부록 B: ARM 템플릿

### <a name="secure"></a>보안

이 템플릿은 프라이빗 엔드포인트로 모든 리소스를 배포합니다. 이는 인터넷에서 PaaS 서비스에 대한 모든 액세스를 효과적으로 제거합니다.

[ARM 템플릿](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>비보안

이 템플릿은 인터넷에서 모든 리소스를 사용할 수 있는 표준 배포를 사용하여 리소스를 배포합니다.

[ARM 템플릿](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)