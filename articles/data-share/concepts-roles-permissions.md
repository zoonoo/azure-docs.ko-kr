---
title: Azure Data Share의 역할 및 요구 사항
description: Azure Data Share를 사용하여 데이터를 공유하고 수신하는 데 필요한 권한에 관해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644260"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share의 역할 및 요구 사항 

이 문서에서는 Azure Data Share 서비스를 사용하여 데이터를 공유하고 수신하는 데 필요한 역할 및 사용 권한을 설명합니다. 

## <a name="roles-and-requirements"></a>역할 및 요구 사항

Azure Data Share 서비스를 사용하면 데이터 공급자와 소비자 간에 자격 증명을 교환하지 않고도 데이터를 공유할 수 있습니다. 스냅샷 기반 공유의 경우 Azure Data Share 서비스는 관리 ID(이전에는 MSI라고 함)를 사용하여 Azure 데이터 저장소에 인증합니다. 데이터를 읽거나 쓰려면 Azure Data Share 리소스의 관리 ID에 Azure 데이터 저장소에 대한 액세스 권한을 부여해야 합니다.

Azure 데이터 저장소에서 데이터를 공유하거나 받으려면 사용자에게 최소한 다음 권한이 있어야 합니다. 

* Azure 데이터 저장소에 쓸 수 있는 권한. 일반적으로 이 권한은 **기여자** 역할에 있습니다.

스토리지 및 데이터 레이크 스냅샷 기반 공유의 경우 Azure 데이터 저장소에서 역할 할당을 만들 수 있는 권한도 필요합니다. 일반적으로 역할 할당을 만들 수 있는 권한은 **소유자** 역할, 사용자 액세스 관리자 역할 또는 *Microsoft.Authorization/역할 할당/쓰기* 권한이 할당된 사용자 지정 역할에 있습니다. 데이터 공유 리소스의 관리 ID에 이미 Azure 데이터 저장소에 대한 액세스 권한이 부여된 경우에는 이 권한이 필요하지 않습니다. 다음은 Data Share 리소스의 관리 ID에 할당된 역할의 요약입니다.

|**데이터 저장소 형식**|**데이터 공급자 원본 데이터 저장소**|**데이터 소비자 대상 데이터 저장소**|
|---|---|---|
|Azure Blob Storage| Storage Blob 데이터 읽기 권한자 | Storage Blob 데이터 기여자
|Azure Data Lake Gen1 | 소유자 | 지원되지 않음
|Azure Data Lake Gen2 | Storage Blob 데이터 읽기 권한자 | Storage Blob 데이터 기여자
|

SQL 스냅샷 기반 공유의 경우 Azure Data Share 리소스와 이름이 같은 Azure SQL Database의 외부 공급자에서 SQL 사용자를 만들어야 합니다. 이 사용자를 만들려면 Azure Active Directory 관리자 권한이 필요합니다. 다음은 SQL 사용자에 필요한 사용 권한에 대한 요약입니다.

|**SQL Database 형식**|**데이터 공급자 SQL 사용자 권한**|**데이터 소비자 SQL 사용자 권한**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>데이터 공급자
스토리지 및 데이터 레이크 스냅샷 기반 공유의 경우 Azure Data Share에 데이터 세트를 추가하려면 공급자 데이터 공유 리소스의 관리 ID에 원본 Azure 데이터 저장소에 대한 액세스 권한을 부여해야 합니다. 예를 들어 스토리지 계정의 경우 데이터 공유 리소스의 관리 ID에 *Storage Blob 데이터 읽기 권한자* 역할이 부여됩니다. 이 작업은 사용자가 Azure Portal을 통해 데이터 세트를 추가하고 사용자에게 적절한 권한이 있을 때 Azure Data Share 서비스를 통해 자동으로 수행됩니다. 예를 들어 사용자는 Azure 데이터 저장소의 소유자이거나 *Microsoft.Authorization/역할 할당/쓰기* 권한이 할당된 사용자 지정 역할의 구성원입니다. 

또는 데이터 공유 리소스의 관리 ID를 Azure 데이터 저장소에 수동으로 추가하도록 Azure 데이터 저장소의 소유자 권한이 있을 수 있습니다. 이 작업은 데이터 공유 리소스마다 한 번만 수행해야 합니다. 데이터 공유 리소스의 관리 ID의 역할 할당을 수동으로 만들려면 아래 단계를 수행합니다.  

1. Azure 데이터 저장소로 이동합니다.
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가** 를 선택합니다.
1. *역할* 에 있는 위의 역할 할당 표에서 역할을 선택합니다. 예를 들어 스토리지 계정에서 *Storage Blob 데이터 읽기 권한자* 를 선택합니다.
1. *선택* 아래에서 Azure Data Share 리소스의 이름을 입력합니다.
1. *저장* 을 클릭합니다.

역할 할당에 관해 자세히 알아보려면 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요. REST API를 사용하여 데이터를 공유하는 경우 [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)을 참조하여 API로 역할 할당을 만들 수 있습니다. 

SQL 스냅샷 기반 공유의 경우 Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결하는 동안 Azure Data Share 리소스와 이름이 같은 SQL Database의 외부 공급자로부터 SQL 사용자를 만들어야 합니다. 이 사용자에게 *db_datareader* 권한이 부여되어야 합니다. SQL 기반 공유를 위한 다른 필수 구성 요소와 함께 샘플 스크립트는 [Azure SQL Database 또는 Azure Synapse Analytics에서 공유](how-to-share-from-sql.md) 자습서에서 찾을 수 있습니다. 

### <a name="data-consumer"></a>데이터 소비자
스토리지 계정에 데이터를 수신하려면 소비자 데이터 공유 리소스의 관리 ID에 대상 스토리지 계정에 대한 액세스 권한을 부여해야 합니다. 데이터 공유 리소스의 관리 ID에는 *Storage Blob 데이터 기여자* 역할을 부여해야 합니다. 사용자가 Azure Portal을 통해 대상 스토리지 계정을 지정하고 사용자에게 적절한 권한이 있는 경우 Azure Data Share 서비스에서 자동으로 이 작업을 수행합니다. 예를 들어 사용자는 스토리지 계정의 소유자이거나 *Microsoft.Authorization/역할 할당/쓰기* 권한이 할당된 사용자 지정 역할의 구성원입니다. 

또는 수동으로 스토리지 계정에 데이터 공유 리소스의 관리 ID를 추가하도록 스토리지 계정의 소유자 권한이 있을 수도 있습니다. 이 작업은 데이터 공유 리소스마다 한 번만 수행해야 합니다. 데이터 공유 리소스의 관리 ID의 역할 할당을 수동으로 만들려면 아래 단계를 수행합니다. 

1. Azure 데이터 저장소로 이동합니다.
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가** 를 선택합니다.
1. *역할* 에 있는 위의 역할 할당 표에서 역할을 선택합니다. 예를 들어 스토리지 계정에서 *Storage Blob 데이터 읽기 권한자* 를 선택합니다.
1. *선택* 아래에서 Azure Data Share 리소스의 이름을 입력합니다.
1. *저장* 을 클릭합니다.

역할 할당에 관해 자세히 알아보려면 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요. REST API를 사용하여 데이터를 수신하는 경우 [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)을 참조하여 API로 역할 할당을 만들 수 있습니다. 

SQL 기반 대상의 경우 Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결하는 동안 Azure Data Share 리소스와 이름이 같은 SQL Database의 외부 공급자로부터 SQL 사용자를 만들어야 합니다. 이 사용자는 *db_datareader, db_datawriter, db_ddladmin* 권한이 부여되어야 합니다. SQL 기반 공유를 위한 다른 필수 구성 요소와 함께 샘플 스크립트는 [Azure SQL Database 또는 Azure Synapse Analytics에서 공유](how-to-share-from-sql.md) 자습서에서 찾을 수 있습니다. 

## <a name="resource-provider-registration"></a>리소스 공급자 등록 

다음 시나리오에서 Microsoft.DataShare 리소스 공급자를 Azure 구독에 수동으로 등록해야 할 수 있습니다. 

* Azure 테넌트에서 처음으로 Azure Data Share 초대 보기
* Azure Data Share 리소스와 다른 Azure 구독의 Azure 데이터 저장소에서 데이터 공유
* Azure Data Share 리소스와 다른 Azure 구독의 Azure 데이터 저장소로 데이터 수신

Azure 구독에 Microsoft.DataShare 리소스 공급자를 등록하려면 다음 단계를 따릅니다. 리소스 공급자를 등록하려면 Azure 구독에 대한 *기여자* 액세스 권한이 필요합니다.

1. Azure Portal에서 **구독** 으로 이동합니다.
1. Azure Data Share에 사용 중인 구독을 선택합니다.
1. **리소스 공급자** 를 클릭합니다.
1. Microsoft.DataShare를 검색합니다.
1. **등록** 을 클릭합니다.
 
리소스 공급자에 관한 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure - [Azure 역할 정의 이해](../role-based-access-control/role-definitions.md)에서 역할에 관해 자세히 알아봅니다.