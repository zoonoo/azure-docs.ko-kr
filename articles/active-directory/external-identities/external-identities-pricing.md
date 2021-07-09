---
title: Azure AD External Identities에 대한 MAU 청구 모델
description: Azure AD의 게스트 사용자 공동 작업(B2B)에 대한 Azure AD External Identities MAU(월간 활성 사용자) 청구 모델에 대해 알아봅니다. Azure AD 테넌트를 Azure 구독에 연결하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86a055bade77f92912134a295f7576e545fe7124
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467152"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Azure AD External Identities에 대한 청구 모델

Azure AD(Azure Active Directory) External Identities 가격은 MAU(월간 활성 사용자)를 기반으로 하며, 이는 한 달 내에 인증 활동이 있는 고유한 사용자의 수입니다. 이 청구 모델은 Azure AD 게스트 사용자 공동 작업(B2B) 및 [Azure AD B2C 테넌트](../../active-directory-b2c/billing.md)에 모두 적용됩니다. MAU 청구는 무료 계층 및 유연하고 예측 가능한 가격을 제공하므로 비용을 절감할 수 있습니다. 이 문서에서는 MAU 청구 및 Azure AD 테넌트를 구독에 연결하는 방법을 알아봅니다.

> [!IMPORTANT]
> 이 문서에는 가격 정보가 포함되어 있지 않습니다. 사용량 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

## <a name="what-do-i-need-to-do"></a>무엇을 해야 하나요?

MAU 청구 기능을 사용하려면 Azure AD 테넌트가 Azure 구독에 연결되어 있어야 합니다.

|테넌트 ID는 다음과 같습니다.  |그러려면 다음 작업을 수행해야 합니다.  |
|---------|---------|
| Azure AD 테넌트가 구독에 이미 연결되어 있습니다.     | 아무 작업도 하지 않습니다. 외부 ID 기능을 사용하여 게스트 사용자와 공동 작업을 수행하는 경우 MAU 모델을 사용하여 자동으로 청구됩니다.        |
| Azure AD 테넌트가 아직 구독에 연결되지 않았습니다.     | [구독에 Azure AD 테넌트를 연결](#link-your-azure-ad-tenant-to-a-subscription)하여 MAU 청구를 활성화하세요.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>MAU(월간 활성 사용자) 청구 정보

Azure AD 테넌트에서 게스트 사용자 공동 작업 사용량은 한 달 내에 인증 활동이 있는 고유한 사용자 수를 기준으로 청구됩니다. 이 모델은 테넌트의 각 Azure AD Premium 라이선스에 대해 최대 5명의 게스트 사용자를 허용하는 1:5 비율 청구 모델을 대체합니다. 테넌트가 구독에 연결되어 있고 외부 ID 기능을 사용하여 게스트 사용자와 협업하는 경우 MAU 기반 청구 모델을 사용하여 자동으로 청구됩니다.

게스트 사용자에게 적용되는 가격 책정 계층은 Azure AD 테넌트에 할당된 가장 높은 가격 책정 계층을 기반으로 합니다. 자세한 내용은 [Azure Active Directory 외부 ID 가격 책정](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/)을 참조하세요.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>구독에 Azure AD 테넌트 연결

Azure AD 테넌트는 적절한 청구 및 기능 액세스를 위해 Azure 구독에 연결해야 합니다. 연결할 수 있는 구독이 디렉터리에 아직 없는 경우 이 프로세스 중에 구독을 추가할 수 있습니다.

1. 구독 내에서 [참가자](../../role-based-access-control/built-in-roles.md) 역할 이상이 할당된 Azure 계정 또는 구독 내 리소스 그룹으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 연결할 디렉터리를 선택합니다. Azure Portal 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, 구독을 선택합니다.

    ![디렉터리 + 구독 아이콘 선택](media/external-identities-pricing/portal-mau-pick-directory.png)

3. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.

4. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.

5. **구독** 아래에서 **연결된 구독** 을 선택합니다.

6. 테넌트 목록에서 테넌트 옆의 확인란을 선택한 후 **구독 연결** 을 선택합니다.

    ![테넌트 선택 및 구독 연결](media/external-identities-pricing/linked-subscriptions.png)

7. 구독 연결 창에서 **구독** 및 **리소스 그룹** 을 선택합니다. 그런 다음, **적용** 을 선택합니다.

   > [!NOTE]
   >
   > * 프리미엄 P1 및 프리미엄 P2 기능 모두에 대해 매월 처음 50,000개의 MAU가 무료입니다. MAU의 총 수를 확인하기 위해 동일한 구독에 연결된 모든 테넌트(Azure AD 및 Azure AD B2C)의 MAU를 결합합니다.
    >* 구독이 나열되지 않은 경우 [구독을 테넌트에 연결](../fundamentals/active-directory-how-subscriptions-associated-directory.md)하면 됩니다. 또는 링크를 선택하여 새 구독을 추가할 수 있습니다. **아직 구독이 없는 경우 여기에서 만들 수 있습니다**.

    ![구독 및 리소스 그룹 선택](media/external-identities-pricing/link-subscription-resource.png)

이러한 단계를 완료하면 Azure Direct 또는 기업계약 세부 정보(해당하는 경우)를 기준으로 Azure 구독에 대한 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

최신 가격 책정 정보는 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.