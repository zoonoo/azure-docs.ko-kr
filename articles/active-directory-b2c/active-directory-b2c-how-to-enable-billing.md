---
title: "Azure AD B2C에 Azure 구독을 연결하는 방법 | Microsoft Docs"
description: "Azure AD B2C 테넌트에 대한 요금을 Azure 구독에 청구하는 단계별 가이드입니다."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Azure 구독을 Azure B2C 테넌트와 연결하여 사용 요금 지불

Azure AD B2C(Azure Active Directory B2C)에 대한 사용 요금은 Azure 구독에 지속적으로 청구됩니다. 테넌트 관리자는 B2C 테넌트 자체를 만든 후에 Azure AD B2C 테넌트를 Azure 구독에 명시적으로 연결할 필요가 있습니다.  이 연결은 대상 Azure 구독에 Azure AD "B2C 테넌트" 리소스를 만들어서 이루어집니다. 많은 B2C 테넌트는 다른 Azure 리소스(예: VM, 데이터 저장소, LogicApps)와 함께 단일 Azure 구독에 연결할 수 있습니다.


> [!IMPORTANT]
> B2C의 사용 요금 청구 및 가격 책정에 대한 최신 정보는 [Azure AD B2C 가격](
https://azure.microsoft.com/pricing/details/active-directory-b2c/) 페이지에 있습니다.

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>1단계 - Azure AD B2C 테넌트 만들기
먼저 B2C 테넌트 만들기를 완료해야 합니다. 이미 대상 B2C 테넌트를 만든 경우에는 이 단계를 건너뜁니다. [Azure AD B2C 시작](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>2단계 - Azure 구독을 보여 주는 Azure AD 테넌트에서 Azure Portal 열기
[Azure Portal](https://portal.azure.com)로 이동합니다. 사용할 Azure 구독을 보여 주는 Azure AD 테넌트로 전환합니다. 이 Azure AD 테넌트는 B2C 테넌트와 다릅니다. Azure Portal에서 대시보드의 오른쪽 위에 있는 계정 이름을 클릭하여 Azure AD 테넌트를 선택합니다. 계속하려면 Azure 구독이 필요합니다. [Azure 구독 가져오기](https://account.windowsazure.com/signup?showCatalog=True)

![Azure AD 테넌트로 전환](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>3단계 - Azure Marketplace에서 B2C 테넌트 리소스 만들기
[마켓플레이스] 아이콘을 클릭하거나 대시보드의 왼쪽 위 모서리에 있는 녹색 "+" 기호를 선택하여 마켓플레이스를 엽니다.  Azure Active Directory B2C를 검색하고 선택합니다. 만들기를 선택합니다.

![Marketplace 선택](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![AD B2C 검색](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

[Azure AD B2C 리소스 만들기] 대화 상자는 다음 매개 변수를 포함하고 있습니다.

1. Azure AD B2C 테넌트 - 드롭다운에서 [Azure AD B2C 테넌트]를 선택합니다.  적격 Azure AD B2C 테넌트만 표시됩니다.  적격 B2C 테넌트는 다음 두 조건을 충족합니다. 사용자가 B2C 테넌트의 전역 관리자이며, B2C 테넌트가 현재 Azure 구독과 연결되어 있지 않습니다.

2. Azure AD B2C 리소스 이름 - B2C 테넌트의 도메인 이름과 일치하도록 미리 선택됩니다.

3. 구독 - 관리자이거나 공동 관리자인 활성 Azure 구독입니다.  하나의 Azure 구독에 여러 Azure AD B2C 테넌트를 추가할 수 있습니다.

4. 리소스 그룹 및 리소스 그룹 위치 - 이 아티팩트를 사용하면 여러 Azure 리소스를 구성할 수 있습니다.  이 선택은 B2C 테넌트 위치, 성능 또는 청구 상태에 영향을 주지 않습니다.

5. 대시보드에 고정 - B2C 테넌트 청구 정보 및 B2C 테넌트 설정에 가장 쉽게 액세스할 수 있게 합니다. ![B2C 리소스 만들기](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>4단계 - B2C 테넌트 리소스 관리(선택 사항)
배포가 완료되면 대상 리소스 그룹 및 관련 Azure 구독에 새로운 "B2C 테넌트" 리소스가 만들어집니다.  다른 "Azure" 리소스와 함께 추가된 "B2C 테넌트" 유형의 새로운 리소스가 표시됩니다.

![B2C 리소스 만들기](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

B2C 테넌트 리소스를 클릭하면 다음을 수행할 수 있습니다.
- 구독 이름을 클릭하여 청구 정보를 검토합니다. 청구 및 사용을 참조하세요.
- Azure AD B2C 설정을 클릭하여 B2C 테넌트 설정 블레이드에서 직접 새 브라우저 탭을 엽니다.
- 지원 요청을 제출합니다.
- B2C 테넌트 리소스를 다른 Azure 구독 또는 다른 리소스 그룹으로 이동합니다.  사용 요금이 이 선택에 따라 Azure 구독에 부과됩니다.

![B2C 리소스 설정](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>알려진 문제
- B2C 테넌트 삭제. B2C 테넌트를 만들었다가 삭제한 후 동일한 도메인 이름으로 다시 만들 경우 동일한 도메인 이름을 사용하여 "연결" 리소스를 삭제한 후 다시 만드세요.  Azure Portal을 통해 구독 테넌트의 "모든 리소스"에서 이 "연결" 리소스를 찾을 수 있습니다.
- 지역별 리소스 위치에 자체적인 제한 적용.  드물지만 사용자가 Azure 리소스 만들기에 대해 지역별 제한을 구축했을 수 있습니다.  이 제한 때문에 Azure 구독과 테넌트 B2C 간에 연결을 만들지 못할 수 있습니다. 유연하게 작업하려면 이러한 제한을 완화하세요.

## <a name="next-steps"></a>다음 단계
각 B2C 테넌트에 대해 이러한 단계가 완료되면 Azure 직접 또는 기업 계약 세부 정보에 따라 비용이 Azure 구독에 청구됩니다.
- Azure 구독을 선택한 상태에서 사용 현황 및 청구 검토
- [사용 현황 보고 API](active-directory-b2c-reference-usage-reporting-api.md)를 사용하여 자세한 일일 사용 현황 보고서 검토
