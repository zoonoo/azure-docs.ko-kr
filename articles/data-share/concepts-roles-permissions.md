---
title: Azure Data Share의 역할 및 요구 사항
description: Azure 데이터 공유를 사용 하 여 데이터를 공유 하 고 수신 하는 데 필요한 권한에 대해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ac528d1cfb74221a7398e390cedda08df4ea4508
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151400"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share의 역할 및 요구 사항 

이 문서에서는 Azure 데이터 공유 서비스를 사용 하 여 데이터를 공유 하 고 수신 하는 데 필요한 역할 및 사용 권한을 설명 합니다. 

## <a name="roles-and-requirements"></a>역할 및 요구 사항

Azure 데이터 공유 서비스를 사용 하면 데이터 공급자와 소비자 간에 자격 증명을 교환 하지 않고도 데이터를 공유할 수 있습니다. Azure 데이터 공유 서비스는 관리 되는 Id (이전에는 Msi로 알려짐)를 사용 하 여 Azure 데이터 저장소에 인증 합니다. 

Azure 데이터 공유 리소스의 관리 되는 id에는 Azure 데이터 저장소에 대 한 액세스 권한이 있어야 합니다. 그런 다음 Azure 데이터 공유 서비스는이 관리 되는 id를 사용 하 여 스냅숏 기반 공유의 데이터를 읽고 쓰고 내부 공유에 대 한 바로 가기 링크를 설정 합니다. 

Azure 데이터 저장소에서 데이터를 공유 하거나 받으려면 사용자에 게 최소한 다음 권한이 필요 합니다. SQL 기반 공유에는 추가 권한이 필요 합니다.

* Azure 데이터 저장소에 쓸 수 있는 권한입니다. 일반적으로이 사용 권한은 **참가자** 역할에 있습니다.
* Azure 데이터 저장소에서 역할 할당을 만들 수 있는 권한입니다. 일반적으로 역할 할당을 만들 수 있는 권한은 **소유자** 역할, 사용자 액세스 관리자 역할 또는 Microsoft의 사용자 지정 역할 (권한 부여/역할 할당/쓰기 권한이 할당 됨)에 있습니다. 데이터 공유 리소스의 관리 id에 이미 Azure 데이터 저장소에 대 한 액세스 권한이 부여 된 경우에는이 권한이 필요 하지 않습니다. 필요한 역할에 대해서는 아래 표를 참조 하세요.

다음은 데이터 공유 리소스의 관리 되는 id에 할당 된 역할에 대 한 요약입니다.

|**데이터 저장소 유형**|**Data Provider 원본 데이터 저장소**|**데이터 소비자 대상 데이터 저장소**|
|---|---|---|
|Azure Blob Storage| Storage Blob 데이터 읽기 권한자 | Storage Blob 데이터 기여자
|Azure Data Lake Gen1 | 소유자 | 지원되지 않음
|Azure Data Lake Gen2 | Storage Blob 데이터 읽기 권한자 | Storage Blob 데이터 기여자
|Azure Data Explorer 클러스터 | 참가자 | 참가자
|

SQL 기반 공유의 경우 Azure 데이터 공유 리소스와 동일한 이름을 가진 Azure SQL Database의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 이 사용자를 만들려면 관리자 권한이 필요 Azure Active Directory. 다음은 SQL 사용자가 요구 하는 사용 권한에 대 한 요약입니다.

|**SQL Database 형식**|**Data Provider SQL 사용자 권한**|**Data Consumer SQL 사용자 권한**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics(이전의 SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>데이터 공급자

Azure 데이터 공유에 데이터 집합을 추가 하려면 원본 Azure 데이터 저장소에 대 한 공급자 데이터 공유 리소스의 관리 되는 id에 대 한 액세스 권한을 부여 해야 합니다. 예를 들어 저장소 계정의 경우 데이터 공유 리소스의 관리 되는 id에 저장소 Blob 데이터 판독기 역할이 부여 됩니다. 

사용자가 Azure Portal를 통해 데이터 집합을 추가 하 고 사용자에 게 적절 한 권한이 있는 경우 Azure 데이터 공유 서비스에서 자동으로이 작업을 수행 합니다. 예를 들어 사용자는 Azure 데이터 저장소의 소유자 이거나 Microsoft. 권한 부여/역할 할당/쓰기 권한이 할당 된 사용자 지정 역할의 멤버입니다. 

또는 사용자가 Azure 데이터 저장소의 소유자를 사용 하 여 데이터 공유 리소스의 관리 되는 id를 Azure 데이터 저장소에 수동으로 추가할 수 있습니다. 이 작업은 데이터 공유 리소스 마다 한 번만 수행 해야 합니다.

데이터 공유 리소스의 관리 되는 id에 대 한 역할 할당을 수동으로 만들려면 아래 단계를 수행 합니다.  

1. Azure 데이터 저장소로 이동 합니다.
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가를**선택 합니다.
1. *역할*아래에서 위의 역할 할당 테이블에서 역할을 선택 합니다. 예를 들어 저장소 계정에서 *저장소 Blob 데이터 판독기*를 선택 합니다.
1. *선택*아래에서 Azure 데이터 공유 리소스의 이름을 입력 합니다.
1. *저장*을 클릭합니다.

역할 할당에 대해 자세히 알아보려면 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)를 참조 하세요. REST Api를 사용 하 여 데이터를 공유 하는 경우 [REST API를 사용 하 여 Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)를 참조 하 여 API를 사용 하 여 역할 할당을 만들 수 있습니다. 

Sql 기반 원본의 경우 Azure Active Directory 인증을 사용 하 여 SQL Database에 연결 하는 동안 Azure 데이터 공유 리소스와 동일한 이름을 가진 SQL Database의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 이 사용자에 게 *db_datareader* 권한이 있어야 합니다. SQL 기반 공유의 다른 필수 구성 요소와 함께 샘플 스크립트는 [Azure SQL Database 또는 Synapse Analytics에서 공유](how-to-share-from-sql.md) 자습서에서 찾을 수 있습니다. 

### <a name="data-consumer"></a>데이터 소비자
데이터를 받으려면 소비자 데이터 공유 리소스의 관리 id에 대상 Azure 데이터 저장소에 대 한 액세스 권한을 부여 해야 합니다. 예를 들어 저장소 계정의 경우 데이터 공유 리소스의 관리 되는 id에 저장소 Blob 데이터 참가자 역할이 부여 됩니다. 

사용자가 Azure Portal를 통해 대상 데이터 저장소를 지정 하 고 사용자에 게 적절 한 권한이 있는 경우 Azure 데이터 공유 서비스에서 자동으로이 작업을 수행 합니다. 예를 들어 사용자는 Azure 데이터 저장소의 소유자 이거나 Microsoft. 권한 부여/역할 할당/쓰기 권한이 할당 된 사용자 지정 역할의 멤버입니다. 

또는 사용자가 Azure 데이터 저장소의 소유자를 사용 하 여 데이터 공유 리소스의 관리 되는 id를 Azure 데이터 저장소에 수동으로 추가할 수 있습니다. 이 작업은 데이터 공유 리소스 마다 한 번만 수행 해야 합니다.

데이터 공유 리소스의 관리 되는 id에 대 한 역할 할당을 수동으로 만들려면 아래 단계를 수행 합니다. 

1. Azure 데이터 저장소로 이동 합니다.
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가를**선택 합니다.
1. *역할*아래에서 위의 역할 할당 테이블에서 역할을 선택 합니다. 예를 들어 저장소 계정에서 *저장소 Blob 데이터 판독기*를 선택 합니다.
1. *선택*아래에서 Azure 데이터 공유 리소스의 이름을 입력 합니다.
1. *저장*을 클릭합니다.

역할 할당에 대해 자세히 알아보려면 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)를 참조 하세요. REST Api를 사용 하 여 데이터를 수신 하는 경우 [REST API를 사용 하 여 Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)를 참조 하 여 API를 사용 하 여 역할 할당을 만들 수 있습니다. 

Sql 기반 대상의 경우 Azure Active Directory 인증을 사용 하 여 SQL Database에 연결 하는 동안 Azure 데이터 공유 리소스와 동일한 이름을 가진 SQL Database의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 이 사용자는 *db_datareader, db_datawriter db_ddladmin* 권한을 부여 받아야 합니다. SQL 기반 공유의 다른 필수 구성 요소와 함께 샘플 스크립트는 [Azure SQL Database 또는 Synapse Analytics에서 공유](how-to-share-from-sql.md) 자습서에서 찾을 수 있습니다. 

## <a name="resource-provider-registration"></a>리소스 공급자 등록 

다음 시나리오에서는 DataShare 리소스 공급자를 Azure 구독에 수동으로 등록 해야 할 수 있습니다. 

* Azure 테 넌 트에서 처음으로 Azure 데이터 공유 초대 보기
* Azure 데이터 공유 리소스에서 다른 Azure 구독의 Azure 데이터 저장소에서 데이터 공유
* Azure 데이터 공유 리소스에서 다른 Azure 구독의 Azure 데이터 저장소로 데이터를 수신 합니다.

Azure 구독에 DataShare 리소스 공급자를 등록 하려면 다음 단계를 수행 합니다. 리소스 공급자를 등록 하려면 Azure 구독에 대 한 *참가자* 액세스 권한이 필요 합니다.

1. Azure Portal에서 **구독**으로 이동 합니다.
1. Azure 데이터 공유에 사용 중인 구독을 선택 합니다.
1. **리소스 공급자**를 클릭 합니다.
1. DataShare를 검색 합니다.
1. **등록**을 클릭합니다.
 
리소스 공급자에 대 한 자세한 내용은 [Azure 리소스 공급자 및 유형](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure - [역할 정의 이해](../role-based-access-control/role-definitions.md)에서 역할에 대해 자세히 알아봅니다.
