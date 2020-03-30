---
title: Azure Data Share의 역할 및 요구 사항
description: Azure 데이터 공유를 사용하여 데이터를 공유하고 받는 데 필요한 사용 권한에 대해 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265507"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share의 역할 및 요구 사항 

이 문서에서는 Azure Data Share 서비스를 사용하여 데이터를 공유하고 받는 데 필요한 역할 및 사용 권한을 설명합니다. 

## <a name="roles-and-requirements"></a>역할 및 요구 사항

Azure 데이터 공유 서비스를 사용하면 데이터 공급자와 소비자 간에 자격 증명을 교환하지 않고도 데이터를 공유할 수 있습니다. Azure 데이터 공유 서비스는 관리되는 ID(이전 MSI)를 사용하여 Azure 데이터 저장소에 인증합니다. 

Azure 데이터 공유 리소스의 관리되는 ID는 Azure 데이터 저장소에 대한 액세스 권한을 부여받아야 합니다. 그런 다음 Azure Data Share 서비스는 이 관리되는 ID를 사용하여 스냅숏 기반 공유를 위한 데이터를 읽고 쓰고, 현재 공유를 위한 기호 링크를 설정합니다. 

Azure 데이터 저장소에서 데이터를 공유하거나 받으려면 최소한 다음 권한이 필요합니다. SQL 기반 공유에는 추가 권한이 필요합니다.
* Azure 데이터 저장소에 쓸 수 있는 권한입니다. 일반적으로 이 권한은 **기여자** 역할에 존재합니다.
* Azure 데이터 저장소에서 역할 할당을 만들 수 있는 권한입니다. 일반적으로 역할 할당을 만들 수 있는 권한은 **소유자** 역할, 사용자 액세스 관리자 역할 또는 Microsoft.권한 부여/역할 할당/쓰기 권한이 할당된 사용자 지정 역할에 있습니다. 데이터 공유 리소스의 관리되는 ID가 Azure 데이터 저장소에 대한 액세스 권한을 이미 부여한 경우에는 이 권한이 필요하지 않습니다. 필요한 역할은 아래 표를 참조하십시오.

다음은 데이터 공유 리소스의 관리되는 ID에 할당된 역할에 대한 요약입니다.

| |  |  |
|---|---|---|
|**데이터 저장소 유형**|**데이터 공급자 소스 데이터 저장소**|**데이터 소비자 대상 데이터 저장소**|
|Azure Blob Storage| Storage Blob 데이터 읽기 권한자 | Storage Blob 데이터 기여자
|Azure Data Lake Gen1 | 소유자 | 지원되지 않음
|Azure Data Lake Gen2 | Storage Blob 데이터 읽기 권한자 | Storage Blob 데이터 기여자
|Azure SQL Server | SQL DB 참가자 | SQL DB 참가자
|Azure 데이터 탐색기 클러스터 | 참가자 | 참가자
|

SQL 기반 공유의 경우 Azure 데이터 공유 리소스와 이름이 같은 SQL 데이터베이스의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 다음은 SQL 사용자가 요구하는 권한에 대한 요약입니다.

| |  |  |
|---|---|---|
|**SQL 데이터베이스 유형**|**데이터 공급자 SQL 사용자 권한**|**데이터 소비자 SQL 사용자 권한**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics(이전의 SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>데이터 공급자 
Azure Data Share에 데이터 집합을 추가하려면 공급자 데이터 공유 리소스의 관리되는 ID에 원본 Azure 데이터 저장소에 대한 액세스 권한을 부여해야 합니다. 예를 들어 저장소 계정의 경우 데이터 공유 리소스의 관리되는 ID에는 저장소 Blob 데이터 리더 역할이 부여됩니다. 

이 작업은 사용자가 Azure 포털을 통해 데이터 집합을 추가하고 사용자에게 적절한 권한이 있을 때 Azure 데이터 공유 서비스에 의해 자동으로 수행됩니다. 예를 들어 사용자는 Azure 데이터 저장소의 소유자이거나 Microsoft.권한 부여/역할 할당/쓰기 권한이 할당된 사용자 지정 역할의 구성원입니다. 

또는 사용자가 Azure 데이터 저장소의 소유자가 Azure 데이터 저장소에 데이터 공유 리소스의 관리되는 ID를 수동으로 추가하도록 할 수 있습니다. 이 작업은 데이터 공유 리소스당 한 번만 수행하면 됩니다.

데이터 공유 리소스의 관리되는 ID에 대한 역할 할당을 만들려면 다음 단계를 따르십시오.

1. Azure 데이터 저장소로 이동합니다.
1. **액세스 제어(IAM)를**선택합니다.
1. **역할 할당 추가를**선택합니다.
1. *역할*에서 위의 역할 할당 테이블에서 역할을 선택합니다(예: 저장소 계정의 경우 *저장소 Blob 데이터 판독기*선택).
1. *선택*에서 Azure 데이터 공유 리소스의 이름을 입력합니다.
1. *저장*을 클릭합니다.

SQL 기반 원본의 경우 위의 단계 외에도 Azure 데이터 공유 리소스와 이름이 같은 SQL 데이터베이스의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 이 사용자에게 *db_datareader* 권한이 부여되어야 합니다. SQL 기반 공유를 위한 다른 필수 구성 조건과 함께 샘플 스크립트는 [데이터 공유](share-your-data.md) 자습서에서 찾을 수 있습니다. 

### <a name="data-consumer"></a>데이터 소비자
데이터를 수신하려면 소비자 데이터 공유 리소스의 관리되는 ID에 대상 Azure 데이터 저장소에 대한 액세스 권한을 부여해야 합니다. 예를 들어 저장소 계정의 경우 데이터 공유 리소스의 관리되는 ID에는 저장소 Blob 데이터 기여자 역할이 부여됩니다. 

사용자가 Azure 포털을 통해 대상 데이터 저장소를 지정하고 사용자에게 적절한 권한이 있는 경우 Azure 데이터 공유 서비스에 의해 자동으로 수행됩니다. 예를 들어 사용자는 Azure 데이터 저장소의 소유자이거나 Microsoft.권한 부여/역할 할당/쓰기 권한이 할당된 사용자 지정 역할의 구성원입니다. 

또는 사용자가 Azure 데이터 저장소의 소유자가 Azure 데이터 저장소에 데이터 공유 리소스의 관리되는 ID를 수동으로 추가하도록 할 수 있습니다. 이 작업은 데이터 공유 리소스당 한 번만 수행하면 됩니다.

데이터 공유 리소스의 관리되는 ID에 대한 역할 할당을 수동으로 만들려면 다음 단계를 따르십시오.

1. Azure 데이터 저장소로 이동합니다.
1. **액세스 제어(IAM)를**선택합니다.
1. **역할 할당 추가를**선택합니다.
1. *역할*에서 위의 역할 할당 테이블에서 역할을 선택합니다(예: 저장소 계정의 경우 *저장소 Blob 데이터 판독기*선택).
1. *선택*에서 Azure 데이터 공유 리소스의 이름을 입력합니다.
1. *저장*을 클릭합니다.

SQL 기반 대상의 경우 위의 단계 외에도 Azure 데이터 공유 리소스와 이름이 같은 SQL 데이터베이스의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 이 사용자에게 db_datareader *db_datawriter db_ddladmin* 권한이 부여되어야 합니다. SQL 기반 공유를 위한 다른 필수 구성 조건과 함께 샘플 스크립트는 [데이터 수락 및 수신](subscribe-to-data-share.md) 자습서에서 찾을 수 있습니다. 

REST API를 사용하여 데이터를 공유하는 경우 이러한 역할 할당을 수동으로 만들어야 합니다. 

역할 할당을 추가하는 방법에 대한 자세한 내용은 [이 설명서를](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)참조하십시오. 

## <a name="resource-provider-registration"></a>리소스 공급자 등록 

Azure 테넌트에서 처음으로 Azure 데이터 공유 초대를 보려면 Microsoft.DataShare 리소스 공급자를 Azure 구독에 수동으로 등록해야 할 수 있습니다. 다음 단계에 따라 Microsoft.DataShare 리소스 공급자를 Azure 구독에 등록합니다. 리소스 공급자를 등록하려면 Azure 구독에 대한 *기여자* 액세스가 필요합니다.

1. Azure 포털에서 구독 으로 **이동합니다.**
1. Azure 데이터 공유에 사용 하는 구독을 선택 합니다.
1. 리소스 **공급자를 클릭합니다.**
1. Microsoft.DataShare를 검색합니다.
1. **등록**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- Azure - [역할 정의 이해](../role-based-access-control/role-definitions.md)에서 역할에 대해 자세히 알아봅니다.

