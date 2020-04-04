---
title: Azure AD 도메인 서비스의 SKU 변경 | 마이크로 소프트 문서
description: 비즈니스 요구 사항이 변경되는 경우 Azure AD 도메인 서비스 관리 도메인의 SKU 계층에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655594"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>기존 Azure AD 도메인 서비스 관리 도메인에 대한 SKU 변경

Azure Active Directory 도메인 서비스(Azure AD DS)에서 사용 가능한 성능 및 기능은 SKU 유형을 기반으로 합니다. 이러한 기능 차이에는 백업 빈도 또는 단방향 아웃바운드 포리스트 트러스트의 최대 수(현재 미리 보기)가 포함됩니다. 관리되는 도메인을 만들 때 SKU를 선택하고 관리되는 도메인을 배포한 후 비즈니스 요구 사항이 변경되면 SKU를 위 또는 아래로 전환할 수 있습니다. 비즈니스 요구 사항의 변경에는 더 자주 백업하거나 추가 포리스트 트러스트를 만들 필요가 포함될 수 있습니다. 다른 SKU의 제한 및 가격 책정에 대한 자세한 내용은 [Azure AD DS SKU 개념][concepts-sku] 및 [Azure AD DS 가격 책정][pricing] 페이지를 참조하십시오.

이 문서에서는 Azure 포털을 사용하여 기존 Azure AD DS 관리 도메인에 대한 SKU를 변경하는 방법을 보여 주며 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 다음과 같은 리소스와 권한이 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory 도메인 서비스 인스턴스를 만들고 구성합니다.][create-azure-ad-ds-instance]

## <a name="sku-change-limitations"></a>SKU 변경 제한 사항

Azure AD DS 관리 도메인이 배포된 후 SCO를 위 또는 아래로 변경할 수 있습니다. 그러나 리소스 포리스트(현재 미리 보기)를 사용하고 Azure AD DS에서 온-프레미스 AD DS 환경으로 의 한방향 아웃바운드 포리스트 트러스트를 만든 경우 SKU 변경 작업에 몇 가지 제한 사항이 있습니다. *프리미엄* 및 *엔터프라이즈* SCO는 만들 수 있는 트러스트 수에 대한 제한을 정의합니다. 현재 구성한 것보다 낮은 최대 제한으로 SKU로 변경할 수 없습니다.

다음은 그 예입니다.

* *프리미엄* SKU에서 두 개의 포리스트 트러스트를 만든 경우 *표준* SKU로 변경할 수 없습니다. *표준* SKU는 포리스트 트러스트를 지원하지 않습니다.
* 또는 *프리미엄* SKU에서 7개의 트러스트를 만든 경우 *엔터프라이즈* SKU로 변경할 수 없습니다. *엔터프라이즈* SKU는 최대 5개의 트러스트를 지원합니다.

이러한 제한에 대한 자세한 내용은 [Azure AD DS SKU 기능 및 제한을][concepts-sku]참조하십시오.

## <a name="select-a-new-sku"></a>새 SKU 선택

Azure 포털을 사용하여 Azure AD DS 관리 도메인의 SKU를 변경하려면 다음 단계를 완료합니다.

1. Azure 포털 상단에서 **Azure AD 도메인 서비스를**검색하고 선택합니다. aaddscontoso.com *와*같은 목록에서 관리되는 도메인을 선택합니다.
1. Azure AD DS 페이지의 왼쪽에 있는 메뉴에서 **SKU**> 설정을 선택합니다.

    ![Azure 포털에서 Azure AD DS 관리 도메인에 대한 SKU 메뉴 옵션을 선택합니다.](media/change-sku/overview-change-sku.png)

1. 드롭다운 메뉴에서 Azure AD DS 관리 도메인에 대해 원하는 SKU를 선택합니다. 리소스 포리스트가 있는 경우 *엔터프라이즈* SKU 이상에서만 포리스트 트러스트를 사용할 수 있으므로 *표준* SKU를 선택할 수 없습니다.

    드롭다운 메뉴에서 원하는 SKU를 선택한 다음 **저장을**선택합니다.

    ![Azure 포털의 드롭다운 메뉴에서 필요한 SKU를 선택합니다.](media/change-sku/change-sku-selection.png)

SKU 유형을 변경하려면 1~2분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

리소스 포리스트가 있고 SKU 변경 후 추가 트러스트를 만들려는 경우 [Azure AD DS(미리 보기)의 온-프레미스 도메인에 대한 아웃바운드 포리스트 트러스트 만들기를][create-trust]참조하십시오.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
