---
title: Azure AD Domain Services에 대 한 복제본 집합 개념 Microsoft Docs
description: Azure Active Directory Domain Services 된 복제본 집합 및 id 서비스를 필요로 하는 응용 프로그램에 중복성을 제공 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 698009ee8a57ed5d30e01376b4f2c63b0a27ead8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505732"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services-preview"></a>복제본이 Azure Active Directory Domain Services (미리 보기)에 대 한 개념 및 기능을 설정 합니다.

Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인을 만들 때 고유한 네임 스페이스를 정의 합니다. 이 네임 스페이스는 *aaddscontoso.com*와 같은 도메인 이름이 고, 두 개의 dc (도메인 컨트롤러)는 선택한 Azure 지역에 배포 됩니다. 이 Dc 배포를 복제본 집합 이라고 합니다.

관리 되는 도메인을 확장 하 여 Azure AD 테 넌 트 당 복제본 집합을 두 개 이상 포함할 수 있습니다. Azure AD DS을 지 원하는 모든 Azure 지역의 피어 링 가상 네트워크에 복제본 집합을 추가할 수 있습니다. 다른 Azure 지역에 있는 추가 복제본 집합은 Azure 지역이 오프 라인 상태가 되는 경우 레거시 응용 프로그램에 대 한 지리적 재해 복구를 제공 합니다.

복제본 집합은 현재 미리 보기로 제공 됩니다.

> [!NOTE]
> 복제본 집합을 사용 하면 단일 Azure 테 넌 트에 여러 개의 고유 관리 도메인을 배포할 수 없습니다. 각 복제본 집합에는 동일한 데이터가 포함 되어 있습니다.

## <a name="how-replica-sets-work"></a>복제본 집합의 작동 방식

*Aaddscontoso.com*와 같은 관리 되는 도메인을 만들 때 초기 복제본 세트가 만들어집니다. 추가 복제본 세트는 동일한 네임 스페이스와 구성을 공유 합니다. 구성, 사용자 id 및 자격 증명, 그룹, 그룹 정책 개체, 컴퓨터 개체 및 기타 변경 내용을 포함 하 여 Azure AD DS에 대 한 변경 내용은 AD DS 복제를 사용 하 여 관리 되는 도메인의 모든 복제본 집합에 적용 됩니다.

가상 네트워크에서 각 복제본 집합을 만듭니다. 각 가상 네트워크는 관리 되는 도메인의 복제본 세트를 호스트 하는 다른 모든 가상 네트워크에 피어 링 해야 합니다. 이 구성은 디렉터리 복제를 지 원하는 메시 네트워크 토폴로지를 만듭니다. 각 복제본 집합이 다른 가상 서브넷에 있는 경우 가상 네트워크는 여러 개의 복제본 집합을 지원할 수 있습니다.

모든 복제본 세트는 동일한 Active Directory 사이트에 배치 됩니다. 따라서 모든 변경 내용은 빠른 수렴을 위해 사이트 간 복제를 사용 하 여 전파 됩니다.

> [!NOTE]
> 별도의 사이트를 정의 하 고 복제본 집합 간에 복제 설정을 정의할 수는 없습니다.

다음 다이어그램에서는 두 개의 복제본 집합을 가진 관리 되는 도메인을 보여 줍니다. 첫 번째 복제본 집합은 도메인 네임 스페이스를 사용 하 여 생성 됩니다. 두 번째 복제본 집합은 다음에 생성 됩니다.

![두 개의 복제본 집합을 가진 관리 되는 도메인 예의 다이어그램](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> 복제본 집합은 복제본 집합이 구성 된 지역에서 인증 서비스의 가용성을 보장 합니다. 지역 가동 중단이 발생 하는 경우 응용 프로그램에 지리적 중복성을 적용 하려면 관리 되는 도메인에 의존 하는 응용 프로그램 플랫폼도 다른 지역에 상주해 야 합니다.
>
> 응용 프로그램을 작동 하는 데 필요한 다른 서비스 (예: Azure Vm 또는 Azure 앱 서비스)의 복원 력을 복제본 집합에서 제공 하지 않습니다. 다른 응용 프로그램 구성 요소의 가용성 디자인은 응용 프로그램을 구성 하는 서비스에 대 한 복원 력 기능을 고려해 야 합니다.

다음 예에서는 복원 력을 추가로 제공 하 고 인증 서비스의 가용성을 보장 하기 위해 세 개의 복제본 집합을 가진 관리 되는 도메인을 보여 줍니다. 두 예제에서 응용 프로그램 작업은 관리 되는 도메인 복제본 집합과 동일한 지역에 있습니다.

![세 개의 복제본 집합을 가진 관리 되는 도메인의 예 다이어그램](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>배포 고려 사항

관리 되는 도메인에 대 한 기본 SKU는 여러 복제본 집합을 지 원하는 *Enterprise* SKU입니다. *표준* SKU로 변경한 경우 추가 복제본 집합을 만들려면 [관리 되는 도메인](change-sku.md) 을 *Enterprise* 또는 *Premium*으로 업그레이드 합니다.

미리 보기 중에 지원 되는 최대 복제본 집합 수는 관리 되는 도메인을 만들 때 생성 되는 첫 번째 복제본을 포함 하 여 4입니다.

각 복제본 세트에 대 한 청구는 도메인 구성 SKU를 기반으로 합니다. 예를 들어 *Enterprise* SKU를 사용 하는 관리 되는 도메인이 있고 3 개의 복제본 집합을 사용 하는 경우 세 개의 복제본 집합 각각에 대해 구독이 시간당 청구 됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-use-my-production-managed-domain-with-this-preview"></a>이 미리 보기에서 내 프로덕션 관리 되는 도메인을 사용할 수 있나요?

복제 세트는 Azure AD Domain Services의 공개 미리 보기 기능입니다. 프로덕션 관리 되는 도메인을 사용할 수 있지만 미리 보기 상태에 있는 기능에 대해 존재 하는 지원 차이점에 대해 알고 있어야 합니다. 미리 보기에 대 한 자세한 내용은 [미리 보기 SLA를 Azure Active Directory](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>구독에서 관리 되는 도메인과 다른 복제본 집합을 만들 수 있나요?

아니요. 복제본 집합은 관리 되는 도메인과 동일한 구독에 있어야 합니다.

### <a name="how-many-replica-sets-can-i-create"></a>만들 수 있는 복제본 집합은 몇 개입니까?

미리 보기는 관리 되는 도메인에 대 한 초기 복제본 세트와 세 개의 추가 복제본 세트를 포함 하는 최대 4 개의 복제본 집합으로 제한 됩니다.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>사용자 및 그룹 정보는 내 복제본 집합에 동기화 되는 방법

모든 복제본 집합은 메시 가상 네트워크 피어 링을 사용 하 여 서로 연결 됩니다. 하나의 복제본 세트가 Azure AD에서 사용자 및 그룹 업데이트를 받습니다. 이러한 변경 내용은 피어 링 네트워크를 통해 사이트 간 AD DS 복제를 사용 하 여 다른 복제본 집합에 복제 됩니다.

온-프레미스 AD DS와 마찬가지로 확장 된 disconnected 상태를 사용 하면 복제가 중단 될 수 있습니다. 피어 링 가상 네트워크가 전이 되지 않으므로 복제 세트에 대 한 디자인 요구 사항에는 완전 하 게 메쉬 된 네트워크 토폴로지가 필요 합니다.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>복제본 세트가 있는 경우 관리 되는 도메인에서 변경 어떻게 할까요?

관리 되는 도메인 내의 변경 내용은 이전에 수행한 것과 동일한 방식으로 작동 합니다. 관리 [되는 도메인에 가입 된 RSAT 도구를 사용 하 여 관리 VM을 만들고 사용](tutorial-create-management-vm.md)합니다. 관리 되는 도메인에 원하는 수 만큼 관리 Vm을 조인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

복제본 세트를 시작 하려면 [Azure AD DS 관리 되는 도메인을 만들고 구성][tutorial-create-advanced]합니다. 배포 된 경우 [추가 복제본 세트를 만들고 사용][create-replica-set]합니다.

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
