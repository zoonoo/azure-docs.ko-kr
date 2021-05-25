---
title: Azure Resource Mover에 대한 일반적인 질문
description: Azure Resource Mover에 대한 일반적인 질문과 답변 찾아보기
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: e900250aea84b4a9c9112fa54632a2be8b9cb49c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564274"
---
# <a name="common-questions"></a>일반적인 질문

이 문서에서는 [Azure Resource Mover](overview.md)에 대한 일반적인 질문의 답변을 제공합니다.


## <a name="moving-across-regions"></a>지역 간 이동

### <a name="can-i-move-resources-across-any-regions"></a>모든 지역에서 리소스를 이동할 수 있습니까?

현재는 [지역에서 사용 가능한 리소스 종류](https://azure.microsoft.com/global-infrastructure/services/)에 따라 모든 원본 공용 지역에서 대상 공용 지역으로 리소스를 이동할 수 있습니다. Azure Government 지역에서 리소스 이동은 현재 지원되지 않습니다.

### <a name="what-regions-are-currently-supported"></a>현재 지원되는 지역은 어떻게 됩니까?

Azure Resource Mover는 현재 다음과 같이 사용 가능합니다.

**지원** | **세부 정보**
--- | ---
이동 지원 | Resource Mover를 사용한 이동이 지원되는 Azure 리소스는 모든 공용 지역에서 다른 공용 지역으로 이동할 수 있습니다.
메타데이터 지원 |  이동할 컴퓨터에 대한 메타데이터 저장 지원 지역으로는 미국 동부2, 북유럽, 동남 아시아, 일본 동부, 영국 남부, 오스트레일리아 동부가 메타데이터 지역으로 포함됩니다. <br/><br/> Azure 중국 지역 내에서 리소스를 이동하는 것은 중국 북부2 메타데이터 지역을 통해 지원됩니다.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Resource Mover를 사용하여 어떤 리소스를 지역 간에 이동할 수 있습니까?

현재 Resource Mover를 사용하면 지역 간에 다음 리소스를 이동할 수 있습니다.

- Azure VM 및 연결된 디스크
- NIC
- 가용성 집합 
- Azure 가상 네트워크 
- 공용 IP 주소
- NSG(네트워크 보안 그룹)::
- 내부 및 공용 부하 분산 장치 
- Azure SQL 데이터베이스 및 탄력적 풀

### <a name="can-i-move-disks-across-regions"></a>지역 간에 디스크를 이동할 수 있습니까?

지역 간에 이동하는 리소스로 디스크를 선택할 수는 없습니다. 하지만 디스크는 VM 이동의 일부로 이동합니다.

### <a name="what-does-it-mean-to-move-a-resource-group"></a>리소스 그룹 이동은 무엇을 의미합니까?

리소스 이동이 선택되면 해당 리소스 그룹은 자동으로 이동을 위해 추가됩니다. 이를 통해 대상 리소스를 리소스 그룹에 배치할 수 있습니다. 이동을 위해 추가된 이후 기존 리소스 그룹을 사용자 지정하고 제공할 수 있습니다. 리소스 그룹을 이동할 때 원본 리소스 그룹의 모든 리소스가 이동하는 것을 의미하지는 않습니다.

### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>지역 간 이동할 때 구독 간 리소스를 이동할 수 있습니까?

리소스를 대상 지역으로 이동한 후 구독을 변경할 수 있습니다. 리소스를 다른 구독으로 이동하는 방법을 [자세히 알아보세요](../azure-resource-manager/management/move-resource-group-and-subscription.md). 

### <a name="does-azure-resource-mover-store-customer-data"></a>Azure Resource Mover는 고객 데이터를 저장합니까? 
아니요. Resource Mover 서비스는 고객 데이터를 저장하지 않습니다. 이동한 리소스의 원활한 추적 및 진행이 가능하도록 메타데이터 정보만을 저장합니다.

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>지역 간 이동을 위한 메타데이터는 어디에 저장됩니까?

[Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 데이터베이스 및 Microsoft 구독의 [Azure Blob Storage](../storage/common/storage-service-encryption.md)에 저장됩니다. 현재 메타데이터는 미국 동부 2 및 북유럽에 저장됩니다. 이 범위는 다른 지역으로 확장될 예정입니다. 이를 통해 모든 공용 지역에 걸쳐 리소스를 이동하는 데 제한이 없어집니다.

### <a name="is-the-collected-metadata-encrypted"></a>수집된 메타데이터는 암호화됩니까?

전송 중 및 미사용 메타데이터 모두 암호화됩니다.
- 전송 중에는 HTTPS를 사용하여 인터넷을 통해 메타데이터를 Resource Mover 서비스로 안전하게 전송합니다.
- 스토리지에서 메타데이터는 암호화됩니다.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Resource Mover에서 관리 ID는 어떻게 사용됩니까?

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)(이전 관리 서비스 ID(MSI))는 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다.
- Resource Mover는 Azure 구독에 액세스하여 지역 간에 리소스를 이동할 수 있도록 관리 ID를 사용합니다.
- 이동 컬렉션에는 이동하는 리소스가 포함된 구독에 대해 액세스 권한이 있는 시스템 할당 ID가 필요합니다.

- 포털의 여러 지역에서 리소스를 이동하는 경우 이 프로세스는 자동으로 수행됩니다.
- PowerShell을 사용하여 리소스를 이동하는 경우 cmdlet을 실행하여 시스템 할당 ID를 컬렉션에 할당한 다음, 올바른 구독 권한이 있는 역할을 ID 보안 주체에 할당합니다. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Resource Mover에 필요한 관리 ID 권한은 무엇입니까? 

Azure Resource Mover 관리되는 ID에는 적어도 다음 권한이 필요합니다. 

- *기여자* 역할과 함께 사용할 수 있는 사용자 구독에서 리소스 작성/만들기 권한. 
- 역할 할당을 만들 수 있는 권한. 일반적으로 *소유자* 또는 *사용자 액세스 관리자* 역할을 사용하거나 *Microsoft.Authorization/role assignments/write permission* 이 할당된 사용자 지정 역할을 사용하여 가능합니다. 데이터 공유 리소스의 관리 ID에 이미 Azure 데이터 저장소에 대한 액세스 권한이 부여된 경우에는 이 권한이 필요하지 않습니다. 
 
포털의 Resource Mover 허브에서 리소스를 추가하는 경우 사용자에게 위에서 설명한 사용 권한이 있으면 자동으로 사용 권한이 처리됩니다. PowerShell을 사용하여 리소스를 추가하는 경우 수동으로 권한을 할당해야 합니다.

> [!IMPORTANT]
> ID 역할 할당을 수정하거나 제거하지 않는 것이 좋습니다. 

### <a name="what-if-i-dont-have-permissions-to-assign-role-identity"></a>역할 ID를 할당할 수 있는 권한이 없는 경우 어떻게 합니까?

사용 권한이 없을 수 있는 몇 가지 이유가 있습니다.

**가능한 원인** | **권장**
--- | ---
처음으로 리소스를 추가할 때 *기여자* 및 *사용자 액세스 관리자*(또는 *소유자*)가 아닙니다. | 구독에 대해 *기여자* 및 *사용자 액세스 관리자*(또는 *소유자*) 권한이 있는 계정을 사용합니다.
Resource Mover 관리 ID에는 필요한 역할이 없습니다. | '기여자' 및 '사용자 액세스 관리자' 역할을 추가합니다.
Resource Mover 관리 ID가 *없음* 으로 재설정되었습니다. | 컬렉션 설정 > **ID** 로 이동하여 시스템 할당 ID를 다시 사용합니다. 또는 **리소스 추가** 에서 동일한 작업을 수행하는 리소스를 다시 추가합니다.  
구독이 다른 테넌트로 이동했습니다. | 이동 컬렉션에 대한 관리 ID를 사용하지 않도록 설정한 다음, 다시 사용 설정합니다.

### <a name="how-can-i-do-multiple-moves-together"></a>여러 이동을 함께 수행할 수 있습니까?

포털에서 변경 옵션을 사용하여 필요에 따라 원본/대상 조합을 변경합니다.

### <a name="what-happens-when-i-remove-a-resource-from-a-list-of-move-resources"></a>이동 리소스 목록에서 리소스를 제거하면 어떻게 됩니까?

이동 목록에 추가한 리소스를 제거할 수 있습니다. 정확한 제거 동작은 리소스 상태에 따라 다릅니다. [자세히 알아보기](remove-move-resources.md#vm-resource-state-after-removing).



## <a name="next-steps"></a>다음 단계

Resource Mover 구성 요소 및 이동 프로세스에 대해 [자세히 알아봅니다](about-move-process.md).
