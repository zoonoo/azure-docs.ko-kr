---
title: "Azure Government 데이터베이스 | Microsoft 문서"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Azure Government 데이터베이스
## <a name="sql-database"></a>SQL 데이터베이스
메타데이터 표시 여부 구성에 대한 추가 지침 및 보호 모범 사례는 <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">SQL Database 엔진에 대한 Microsoft 보안 센터</a> 및 [Azure SQL Database 공용 설명서](../sql-database/index.md)를 참조하세요.

### <a name="variations"></a>변형
SQL 데이터베이스 V12는 일반적으로 Azure Government에서 사용할 수 있습니다.

Azure Government에서 SQL Azure 서버의 주소는 서로 다릅니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| SQL 데이터베이스 |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>고려 사항
다음 정보는 Azure SQL에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Microsoft Azure SQL에서 저장되고 처리된 모든 데이터는 Azure Government 규제된 데이터를 포함할 수 있습니다. Azure Government 규제된 데이터의 데이터 전송을 위해 데이터베이스 도구를 사용해야 합니다. |Azure SQL 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 저장소 제품을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다.  다음 필드에 규제된/제어된 데이터인 데이터베이스 이름, 구독 이름, 리소스 그룹, 서버 이름, 서버 도메인 로그인, 배포 이름, 리소스 이름, 리소스 태그를 입력하지 마세요. |

## <a name="azure-redis-cache"></a>Azure Redis 캐시(영문)
이 서비스에 대한 자세한 내용 및 사용 방법은 [Azure Redis Cache 공개 문서](../redis-cache/index.md)를 참조하세요.

### <a name="variations"></a>변형
Azure Government에서 Azure Redis Cache에 액세스하여 관리하는 URL은 서로 다릅니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| 캐시 끝점 |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Azure 포털 |https://portal.azure.com/ |https://portal.azure.us |

> [!NOTE]
> 모든 스크립트와 코드는 적절한 끝점과 환경을 고려해야 합니다. 자세한 내용은 [Azure Government 클라우드에 연결하는 방법](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)을 참조하세요.
> 
> 

### <a name="considerations"></a>고려 사항
다음 정보는 Azure Redis Cache에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Azure Redis Cache에 저장되고 처리되는 모든 데이터는 Azure Government 규제된 데이터를 포함할 수 있습니다. |Azure Redis Cache 메타데이터는 내보내기 제어된 데이터를 포함할 수 없습니다. 캐시 이름, VNET 이름, 구독 이름, 리소스 그룹, 리소스 태그 및 Redis 속성 필드에는 규제된/제어된 데이터를 입력하지 마세요. |

## <a name="next-steps"></a>다음 단계
부가 정보 및 업데이트를 보려면 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.




<!--HONumber=Nov16_HO3-->


