---
title: Azure Active Directory B2C에 대한 청구 모델
description: Azure AD B2C의 월간 활성 사용자(MAU) 청구 모델과 특정 Azure 구독에 대한 청구를 활성화하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190010"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대한 청구 모델

Azure Active Directory B2C(Azure AD B2C) 사용량은 연결된 Azure 구독에 청구되며 월별 활성 사용자(MAU) 청구 모델을 사용합니다. Azure AD B2C 리소스를 구독에 연결하는 방법과 다음 섹션에서 MAU 청구 모델이 작동하는 방식에 대해 알아봅니다.

> [!IMPORTANT]
> 이 문서에는 가격 정보가 포함되어 있지 않습니다. 사용량 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory B2C 가격 책정을](https://azure.microsoft.com/pricing/details/active-directory-b2c/)참조하십시오.

## <a name="monthly-active-users-mau-billing"></a>월간 활성 사용자(MAU) 청구

Azure AD B2C 청구는 월별 활성 사용자(MAU) 청구라고 하는 월별 인증 활동이 있는 고유 사용자의 수를 기준으로 계산됩니다.

**2019년 11월 01일부터**새로 만든 모든 Azure AD B2C 테넌자는 월별 활성 사용자(MAU)에 대한 요금이 청구됩니다. 2019년 11월 01일 이후에 [구독에 연결된](#link-an-azure-ad-b2c-tenant-to-a-subscription) 기존 테넌자는 월별 활성 사용자(MAU)에 대한 요금이 청구됩니다.

2019년 11월 01일 이전에 구독에 연결된 기존 Azure AD B2C 테넌트가 있는 경우 다음 중 하나를 수행하도록 선택할 수 있습니다.

* 월간 활성 사용자(MAU) 청구 모델로 업그레이드하거나
* 인증별 청구 모델 유지

### <a name="upgrade-to-monthly-active-users-billing-model"></a>월간 활성 사용자 청구 모델로 업그레이드

Azure AD B2C 리소스에 대한 관리 액세스 권한이 있는 Azure 구독 소유자는 MAU 청구 모델로 전환할 수 있습니다. 청구 옵션은 Azure AD B2C 리소스에서 구성됩니다.

월간 활성 사용자(MAU) 청구로 전환하는 것은 **되돌릴 수 없습니다.** Azure AD B2C 리소스를 MAU 기반 청구 모델로 변환한 후에는 해당 리소스를 인증별 청구 모델로 되돌릴 수 없습니다.

기존 Azure AD B2C 리소스에 대해 MAU 청구로 전환하는 방법은 다음과 같습니다.

1. [Azure 포털에](https://portal.azure.com) 구독 소유자로 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 MAU 청구로 업그레이드할 Azure AD B2C 디렉터리를 선택합니다.<br/>
    ![Azure 포털의 디렉터리 및 구독 필터](./media/billing/portal-mau-01-select-b2c-directory.png)
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. Azure AD B2C 테넌트의 **개요** 페이지에서 리소스 **이름**아래의 링크를 선택합니다. Azure AD 테넌트의 Azure AD B2C 리소스로 이동합니다.<br/>
    ![Azure 포털에서 강조 표시된 Azure AD B2C 리소스 링크](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Azure AD B2C 리소스의 **개요** 페이지에서 **청구 가능한 단위에서** **인증당(MAU로 변경)** 링크를 선택합니다.<br/>
    ![Azure 포털에서 강조 표시된 MAU 링크로 변경](./media/billing/portal-mau-03-change-to-mau-link.png)
1. MAU 청구로 업그레이드를 완료하려면 **확인을** 선택합니다.<br/>
    ![Azure 포털에서 MAU 기반 청구 확인 대화 상자](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>인증별 청구에서 MAU 청구로 전환할 때 예상되는 사항

MAU 기반 계량은 구독/리소스 소유자가 변경 을 확인하는 즉시 활성화됩니다. 월별 청구서에는 변경 될 때까지 청구되는 인증 단위와 변경부터 시작하는 MAU의 새 단위가 반영됩니다.

전환 월 동안 사용자는 두 배로 계산되지 않습니다. 변경 전에 인증한 고유 활성 사용자에게는 한 달에 인증당 요금이 부과됩니다. 해당 사용자는 구독의 나머지 청구 주기동안 MAU 계산에 포함되지 않습니다. 예를 들어:

* Contoso B2C 테넌트에는 1,000명의 사용자가 있습니다. 250명의 사용자가 특정 월에 활성 상태입니다. 구독 관리자는 매월 10일에 인증당 사용자당 활성 사용자(MAU)로 변경됩니다.
* 1-10일에 대한 청구는 인증별 모델을 사용하여 청구됩니다.
  * 이 기간(1일~10일)동안 100명의 사용자가 로그인하면 해당 사용자는 *해당 월에 대한 유료로*태그가 지정됩니다.
* 10일부터(전환 유효 시간)의 청구는 MAU 요금으로 청구됩니다.
  * 이 기간(10일~30일) 동안 150명의 사용자가 추가로 로그인하는 경우 추가 150명만 청구됩니다.
  * 처음 100명의 사용자의 지속적인 활동은 달의 나머지 부분에 대한 청구에 영향을 미치지 않습니다.

전환의 청구 기간 동안 구독 소유자는 Azure 구독 청구 명세서에 두 메서드(인증별 및 MAU당)에 대한 항목이 표시될 수 있습니다.

* 인증당 반영하는 변경 날짜/시간까지의 사용량 에 대한 항목입니다.
* 월별 활성 사용자(MAU)를 반영하는 변경 후 사용에 대한 항목입니다.

Azure AD B2C에 대한 사용량 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory B2C 가격 책정을](https://azure.microsoft.com/pricing/details/active-directory-b2c/)참조하십시오.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Azure AD B2C 테넌트를 구독에 연결합니다.

Azure Active Directory B2C(Azure AD B2C)에 대한 사용량 요금은 Azure 구독에 청구됩니다. Azure AD B2C 테넌트가 만들어지면 테넌트 관리자는 Azure AD B2C 테넌트를 Azure 구독에 명시적으로 연결해야 합니다.

구독 링크는 대상 Azure 구독 내에서 Azure AD B2C *리소스를* 만들어 달성합니다. 가상 컴퓨터, 저장소 계정 및 논리 앱과 같은 다른 Azure 리소스와 함께 단일 Azure 구독에서 여러 Azure AD B2C 리소스를 만들 수 있습니다. 구독이 연결된 Azure Active Directory(Azure AD) 테넌트로 이동하여 구독 내의 모든 리소스를 볼 수 있습니다.

Azure AD B2C 테넌트에 연결된 구독은 Azure AD B2C 사용량 또는 추가 Azure AD B2C 리소스를 포함한 기타 Azure 리소스의 청구에 사용할 수 있습니다. Azure AD B2C 테넌트 내의 다른 Azure 라이선스 기반 서비스 또는 Office 365 라이선스를 추가하는 데는 구독을 사용할 수 없습니다.

### <a name="prerequisites"></a>사전 요구 사항

* [Azure 구독](https://azure.microsoft.com/free/)
* 구독에 연결하려는 [Azure AD B2C 테넌트](tutorial-create-tenant.md)
  * 테넌트 관리자여야 합니다.
  * 테넌트가 구독에 아직 연결되어 있지 않아야 합니다.

### <a name="create-the-link"></a>링크 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 사용할 Azure*구독이* 포함된 디렉터리(Azure AD B2C 테넌트가 포함된 디렉터리아님)를 선택합니다.
1. **리소스 만들기를**선택하고 `Active Directory B2C` **마켓플레이스 검색** 필드에 입력한 다음 **Azure Active Directory B2C를**선택합니다.
1. **만들기** 선택
1. **기존 Azure AD B2C 테넌트를 내 Azure 구독에 연결합니다.**
1. 드롭다운에서 **Azure AD B2C 테넌트를** 선택합니다. 글로벌 관리자이고 구독에 아직 연결되지 않은 테넌트만 표시됩니다. **Azure AD B2C 리소스 이름** 필드는 선택한 Azure AD B2C 테넌트의 도메인 이름으로 채워집니다.
1. 관리자인 활성 Azure **구독을** 선택합니다.
1. **리소스 그룹에서** **새**을 선택한 다음 **리소스 그룹 위치를**지정합니다. 여기에 있는 리소스 그룹 설정은 Azure AD B2C 테넌트 위치, 성능 또는 청구 상태에 영향을 미치지 않습니다.
1. **만들기**를 선택합니다.
    ![Azure 포털의 Azure AD B2C 리소스 만들기 페이지](./media/billing/portal-01-create-b2c-resource-page.png)

Azure AD B2C 테넌트에 대해 이러한 단계를 완료하면 Azure 구독(해당하는 경우 Azure Direct 또는 기업 계약 세부 정보에 따라 요금이 청구됩니다).

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C 테넌트 리소스 관리

Azure 구독에서 Azure AD B2C 리소스를 만든 후에는 "B2C 테넌트" 형식의 새 리소스가 다른 Azure 리소스와 함께 나타납니다.

이 리소스를 사용하여 다음을 수행할 수 있습니다.

* 구독으로 이동하여 청구 정보를 검토합니다.
* Azure AD B2C 테넌트의 테넌트 ID를 GUID 형식으로 가져옵니다.
* Azure AD B2C 테넌트로 이동합니다.
* 지원 요청을 제출합니다.
* Azure AD B2C 테넌트 리소스를 다른 Azure 구독 또는 리소스 그룹으로 이동

![Azure 포털의 B2C 리소스 설정 페이지](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>지역 제한

구독에서 Azure 리소스 만들기에 대한 지역 제한을 설정한 경우 이 제한으로 인해 Azure AD B2C 리소스를 만들지 못할 수 있습니다.

이 문제를 완화하려면 지역 제한을 완화하십시오.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>AZURE 클라우드 솔루션 공급자(CSP) 구독

Azure CSP(Cloud Solution Providers) 구독은 Azure AD B2C에서 지원됩니다. 이 기능은 Azure AD B2C 또는 모든 Azure 리소스에 대한 API 또는 Azure Portal을 통해 사용할 수 있습니다. CSP 구독 관리자는 다른 Azure 리소스와 마찬가지로 Azure AD B2C와의 관계를 연결, 이동 및 삭제할 수 있습니다.

역할 기반 액세스 제어를 사용한 Azure AD B2C 관리는 Azure AD B2C 테넌트와 Azure CSP 구독 간의 연결에 의해 영향을 받지 않습니다. 역할 기반 액세스 제어는 구독 기반 역할이 아닌 테넌트 기반 역할을 사용하여 수행됩니다.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C 테넌트 청구 구독 변경

원본 및 대상 구독이 동일한 Azure Active Directory 테넌트 내에 있는 경우 Azure AD B2C 테넌트를 다른 구독으로 이동할 수 있습니다.

Azure AD B2C 테넌트와 같은 Azure 리소스를 다른 구독으로 이동하는 방법을 알아보려면 [리소스를 새 리소스 그룹 또는 구독으로 이동을](../azure-resource-manager/management/move-resource-group-and-subscription.md)참조하세요.

이동을 시작하기 전에 전체 문서를 읽고 이러한 이동에 대한 제한 사항과 요구 사항을 완전히 이해해야 합니다. 리소스 이동에 대한 지침 외에도 이동 전 검사 목록 및 이동 작업의 유효성을 검사하는 방법과 같은 중요한 정보가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

최신 가격 정보는 [Azure Active Directory B2C 가격 책정을](https://azure.microsoft.com/pricing/details/active-directory-b2c/)참조하십시오.
