---
title: Azure Marketplace에 관리형 서비스 솔루션 게시
description: 고객이 Azure 위임 리소스 관리에 고객을 온보딩하는 관리형 서비스 제품을 게시하는 방법을 알아봅니다.
ms.date: 11/15/2019
ms.topic: overview
ms.openlocfilehash: 29f17e6227d3c50a4d9fe13f7525ac71f7550632
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131299"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Azure Marketplace에 관리형 서비스 솔루션 게시

이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com/)을 사용하여 [Azure Marketplace](https://azuremarketplace.microsoft.com)에 공용 또는 프라이빗 관리형 서비스 제품을 게시하여 제품을 구매하는 고객이 Azure 위임 리소스 관리를 위해 리소스를 온보딩할 수 있도록 하는 방법을 알아봅니다.

> [!NOTE]
> 이러한 제품을 만들고 게시하려면 [파트너 센터에 유효한 계정](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)이 있어야 합니다. 계정이 아직 없는 경우 [등록 프로세스](https://aka.ms/joinmarketplace)를 통해 파트너 센터에서 계정을 만들고 상업적 Marketplace 프로그램에 등록하는 단계를 진행합니다. MPN(Microsoft 파트너 네트워크) ID는 게시하는 제품에 [자동으로 연결되어](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) 고객 계약에 미치는 영향을 추적합니다.
>
> Azure Marketplace에 제품을 게시하지 않으려는 경우 Azure Resource Manager 템플릿을 사용하여 수동으로 고객을 온보딩할 수 있습니다. 자세한 내용은 [Azure 위임 리소스 관리에 고객 온보딩](onboard-customer.md)을 참조하세요.

관리 서비스 제품 게시는 Azure Marketplace에 다른 유형의 제품을 게시하는 것과 비슷합니다. 해당 프로세스에 대해 알아보려면 [Azure Marketplace 및 AppSource 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) 및 [Azure 및 AppSource Marketplace 제품 관리](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers)를 참조하세요. [상업 마켓플레이스 인증 정책](https://docs.microsoft.com/legal/marketplace/certification-policies), 특히 [관리 서비스](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) 섹션도 검토해야 합니다.

고객이 제품을 추가하면 하나 이상의 특정 구독 또는 리소스 그룹을 위임할 수 있습니다. 그러면 [Azure 위임 리소스 관리에 온보딩](#the-customer-onboarding-process)됩니다. 구독(또는 구독 내의 리소스 그룹)을 온보딩하려면 먼저 **Microsoft.ManagedServices** 리소스 공급자를 수동으로 등록하여 온보딩에 대한 권한을 구독에 부여해야 합니다.

> [!IMPORTANT]
> 관리형 서비스 제품의 각 플랜에는 해당 플랜을 구입하는 고객의 위임된 리소스 그룹 및/또는 구독에 액세스할 수 있는 테넌트의 Azure AD(Azure Active Directory) 엔터티를 정의하는 **매니페스트 세부 정보** 섹션이 포함되어 있습니다. 여기에 포함하는 모든 그룹(또는 사용자 또는 서비스 주체)이 플랜을 구입하는 모든 고객에 대해 동일한 권한을 갖게 됩니다. 각 고객에 작동할 다른 그룹을 할당하려면 각 고객에게 단독으로 사용되는 별도의 프라이빗 플랜을 게시해야 합니다.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Cloud 파트너 포털에서 제품 만들기

1. [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **새 제품**을 선택하고 **관리형 서비스**를 선택합니다.
3. 제품에 대한 **편집기** 섹션이 표시되며 이 섹션에서 **제품 설정**, **플랜**, **Marketplace** 및 **지원**의 네 부분을 채워야 합니다. 이러한 섹션을 완료하는 방법에 대한 지침은 참조하세요.

## <a name="enter-offer-settings"></a>제품 설정 입력

**제품 설정** 섹션에서 다음을 제공합니다.

|필드  |설명  |
|---------|---------|
|**제안 ID**     | 게시자 프로필에 있는 제품의 고유 식별자입니다. 이 ID에는 소문자 영숫자, 대시 및 밑줄만 사용할 수 있으며 최대 50자로 제한됩니다. 제품 ID는 제품 URL 및 청구 보고서 등에 표시될 수 있습니다. 제품을 게시한 후에는 이 값을 변경할 수 없습니다.        |
|**게시자 ID**     | 제품에 연결될 게시자 ID입니다. 게시자 ID가 둘 이상인 경우 이 제품에 사용할 ID를 선택할 수 있습니다.       |
|**Name**     | 고객이 Azure Marketplace 및 Azure Portal에서 제품에 대해 보게 되는 이름(최대 50자)입니다. 고객이 이해할 수 있는 인식 가능한 브랜드 이름 사용합니다. 고유한 웹 사이트를 통해 이 제품을 홍보하는 경우 여기에 정확히 동일한 이름을 사용해야 합니다.        |

완료되면 **저장**을 선택합니다. 이제 **플랜** 섹션으로 이동할 준비가 되었습니다.

## <a name="create-plans"></a>플랜 만들기

각 제품에는 하나 이상의 플랜(SKU라고도 함)이 있어야 합니다. 여러 플랜을 추가하여 다양한 가격으로 다양한 기능 세트를 지원하거나, 제한된 특정 고객 대상 그룹을 위한 특정 플랜을 사용자 지정할 수 있습니다. 고객은 상위 제품 아래에서 사용할 수 있는 플랜을 볼 수 있습니다.

플랜 섹션에서 **새 플랜**을 선택합니다. 그런 다음, **플랜 ID**를 입력합니다. 이 ID에는 소문자 영숫자, 대시 및 밑줄만 사용할 수 있으며 최대 50자로 제한됩니다. 플랜 ID는 제품 URL 및 청구 보고서 등에 표시될 수 있습니다. 제품을 게시한 후에는 이 값을 변경할 수 없습니다.

### <a name="plan-details"></a>플랜 세부 정보

**플랜 세부 정보** 섹션에서 다음 섹션을 완료합니다.

|필드  |설명  |
|---------|---------|
|**제목**     | 표시할 플랜의 이름입니다. 최대 길이는 50자입니다.        |
|**요약**     | 제목 아래에 표시할 플랜에 대한 간결한 설명입니다. 최대 길이는 100자입니다.        |
|**설명**     | 플랜에 대한 자세한 설명을 제공하는 설명 텍스트입니다.         |
|**청구 모델**     | 여기에는 2개의 청구 모델이 있지만 관리형 서비스 제품에 대한 **사용자 라이선스 필요**를 선택해야 합니다. 즉, 고객에게 이 제품과 관련된 비용을 직접 청구할 것이며 Microsoft는 사용자에게 요금을 부과하지 않음을 의미합니다.   |
|**프라이빗 플랜인가요?**     | SKU가 프라이빗 또는 공개인지 여부를 나타냅니다. 기본값은 **아니요**(공개)입니다. 이 선택을 그대로 두면 플랜이 특정 고객 또는 특정 수의 고객으로 제한되지 않습니다. 일단 퍼블릭 플랜을 게시하면 나중에 프라이빗으로 변경할 수 없습니다. 이 플랜을 특정 고객만 사용할 수 있도록 설정하려면 **예**를 선택합니다. 이렇게 하면 구독 ID를 제공하여 고객을 식별해야 합니다. 구독 ID를 하나씩(구독이 최대 10개인 경우) 입력하거나 .csv 파일을 업로드하여(구독이 최대 2만개인 경우)을 업로드하여 입력할 수 있습니다. 제품을 테스트하고 유효성을 검사할 수 있도록 여기에 고유한 구독을 포함해야 합니다. 자세한 내용은 [프라이빗 SKU 및 플랜](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus)을 참조하세요.  |

### <a name="manifest-details"></a>매니페스트 세부 정보

플랜의 **매니페스트 세부 정보** 섹션을 완료합니다. 이렇게 하면 고객 리소스를 관리하기 위한 권한 부여 정보가 포함된 매니페스트가 생성됩니다. 이 정보는 Azure 위임 리소스 관리를 사용하도록 설정하는 데 필요합니다.

> [!NOTE]
> 위에서 설명한 것처럼 **권한 부여** 항목의 사용자 및 역할은 플랜을 구매한 모든 고객에게 적용됩니다. 특정 고객에 대한 액세스를 제한하려는 경우 독점 사용을 위해 프라이빗 플랜을 게시해야 합니다.

먼저 매니페스트의 **버전** 을 제공합니다. *n.n.n* 형식(예: 1.2.5)을 사용합니다.

그런 다음, **테넌트 ID**를 입력합니다. 이 GUID는 조직의 Azure Active Directory 테넌트 ID(예: 고객의 리소스를 관리하기 위해 작업할 테넌트)와 연결된 GUID입니다. 이 작업이 어려운 경우 Azure Portal의 오른쪽 위에 있는 계정 이름으로 마우스를 가져가거나 **디렉터리 전환**을 선택하여 찾을 수 있습니다.

마지막으로 플랜에 하나 이상의 **권한 부여** 항목을 추가합니다. 권한 부여는 플랜을 구매한 고객의 리소스 및 구독에 액세스할 수 있는 엔터티를 정의하고, 특정 수준의 액세스 권한을 부여하는 역할을 할당합니다. 지원되는 역할에 대한 자세한 내용은 [Azure Lighthouse 시나리오의 테넌트, 역할 및 사용자](../concepts/tenants-users-roles.md)를 참조하세요.

각 **권한 부여**에 대해 다음을 제공해야 합니다. 그런 다음, 사용자 및 역할 정의를 더 추가하는 데 필요한 횟수만큼 **새 권한 부여**를 선택할 수 있습니다.

- **Azure AD 개체 ID**: 고객의 리소스에 대한 특정 권한(역할 정의에 설명됨)을 부여할 사용자, 사용자 그룹 또는 애플리케이션의 Azure AD 식별자입니다.
- **Azure AD 개체 표시 이름**: 고객이 이 권한 부여의 용도를 이해하는 데 도움이 되는 친숙한 이름입니다. 리소스를 위임할 때 고객에게 이 이름이 표시됩니다.
- **역할 정의**: 목록에서 사용 가능한 Azure AD 기본 제공 역할 중 하나를 선택합니다. 이 역할은 **Azure AD 개체 ID** 필드의 사용자가 고객의 리소스에 대해 갖게 되는 권한을 결정합니다. 이러한 역할에 대한 설명은 [기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 및 [Azure 위임 리소스 관리에 대한 역할 지원](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)을 참조하세요.
- **할당 가능한 역할**: 이 권한 부여에 대해 **역할 정의**에서 사용자 액세스 관리자를 선택한 경우에만 필요합니다. 그렇다면 여기에 할당 가능한 역할을 하나 이상 추가해야 합니다. **Azure AD 개체 ID** 필드의 사용자는 이러한 **할당 가능한 역할**을 [수정할 수 있는 정책을 배포](deploy-policy-remediation.md)하는 데 필요한 [관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에 할당할 수 있습니다. 사용자 액세스 관리자 역할에 연결된 다른 사용 권한이 이 사용자에게 적용되지 않습니다. 여기에서 하나 이상의 역할을 선택하지 않으면 제출이 인증을 통과하지 못합니다. 이 사용자의 역할 정의에 대해 사용자 액세스 관리자를 선택하지 않은 경우 이 필드는 영향을 주지 않습니다.

> [!TIP]
> 대부분의 경우 일련의 개별 사용자 계정이 아닌 Azure AD 사용자 그룹 또는 서비스 주체에 권한을 할당하는 것이 좋습니다. 이렇게 하면 액세스 요구 사항이 변경될 때 플랜을 업데이트한 후 다시 게시하지 않고도 개별 사용자에 대한 액세스 권한을 추가하거나 제거할 수 있습니다. 추가 권장 사항은 [Azure Lighthouse 시나리오의 테넌트, 역할 및 사용자](../concepts/tenants-users-roles.md)를 참조하세요.

정보를 완료한 후에는 추가 플랜을 만드는 데 필요한 만큼 **새 플랜**을 선택할 수 있습니다. 작업이 끝나면 **저장**을 선택한 후 **Marketplace** 섹션을 계속 진행합니다.

## <a name="provide-marketplace-text-and-images"></a>Marketplace 텍스트 및 이미지 제공

**Marketplace** 섹션에서는 고객이 Azure Marketplace 및 Azure Portal에 표시되는 텍스트와 이미지를 제공합니다.

**개요** 섹션에서 다음 필드를 완료합니다.

|필드  |설명  |
|---------|---------|
|**제목**     |  제안의 제목이며. 종종 긴 정식 이름입니다. 이 제목은 마켓플레이스에서 눈에 띄게 표시됩니다. 최대 길이는 50자입니다. 대부분의 경우 이 이름은 **제품 설정** 섹션에 입력한 **이름**과 동일해야 합니다.       |
|**요약**     | 제품의 간략한 용도 또는 기능입니다. 일반적으로 제목 아래에 표시됩니다. 최대 길이는 100자입니다.        |
|**긴 요약**     | 제품의 용도 또는 기능을 좀 더 자세히 요약한 것입니다. 최대 길이는 256자입니다.        |
|**설명**     | 제품에 대한 자세한 정보. 이 필드의 최대 길이는 3000자이며 단순 HTML 형식을 지원합니다. 설명의 어딘가에 "관리 서비스" 또는 "관리 서비스"라는 단어를 포함해야 합니다.       |
|**마케팅 식별자**     | 고유한 URL 식별자입니다. 이 제품의 Marketplace URL에서 사용됩니다. 예를 들어, 게시자 ID가 *contoso*이고 마케팅 식별자가 *sampleApp*이면 Azure Marketplace에서 제품의 URL은 *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* 이 됩니다.        |
|**미리 보기 구독 ID**     | 1-100개의 구독 식별자를 추가합니다. 이러한 구독과 연결된 고객은 활성화되기 전에 Azure Marketplace에서 제품을 볼 수 있습니다. 고객이 제품을 사용하도록 설정하기 전에 Azure Marketplace에서 제품이 표시되는 방식을 미리 볼 수 있도록 여기에 사용자 고유의 구독을 포함하는 것이 좋습니다.  Microsoft 지원 및 엔지니어링 팀은 이 미리 보기 기간 중에 제품을 볼 수도 있습니다.   |
|**유용한 링크**     | 설명서, 릴리스 정보, FAQ 등의 제품과 관련된 URL입니다.        |
|**권장 범주(최대 5개)**     | 제품에 적용되는 하나 이상의 범주(최대 5개)입니다. 이러한 범주는 고객이 Azure Marketplace 및 Azure Portal에서 제품을 검색하는 데 도움이 됩니다.        |

**마케팅 아티팩트** 섹션에서 제품과 함께 표시할 로고 및 기타 자산을 업로드할 수 있습니다. 필요에 따라 고객이 제품을 이해하는 데 도움이 될 수 있는 스크린샷 또는 비디오 링크를 업로드할 수 있습니다.

다음과 같은 4개의 로고 크기가 필요합니다. **작게(40x40)** , **보통(90x90)** , **크게(115x115)** 및 **아주 크게(255x155)** . 로고에 대해서는 다음 지침을 따르세요.

- Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수를 제한합니다.
- 포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로 이러한 색을 사용하지 마십시오. 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다.
- 투명한 배경을 사용하는 경우 로고 및 텍스트는 흰색, 검은색 또는 파란색이 아니어야 합니다.
- 로고의 모양과 느낌은 평면적이어야 하며 그라데이션은 사용하지 마십시오. 로고의 배경에 그라데이션 효과를 사용하지 마십시오.
- 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 마십시오.
- 로고가 늘어나지 않았는지 확인합니다.

**Hero (815x290)** 로고는 선택 사항이지만 권장됩니다. 대표 로고를 포함하는 경우 다음 지침을 따르세요.

- 대표 로고에는 텍스트를 포함하지 않도록 하고 로고 오른쪽에 415픽셀의 빈 공간을 남겨 두어야 합니다. 프로그래밍 방식으로 포함될 텍스트 요소, 즉 게시자 표시 이름, 플랜 제목, 제품의 자세한 요약에 대한 공간을 확보하는 데 필요합니다.
- 대표 로고의 배경은 검은색, 흰색 또는 투명이 아닐 수 있습니다. 포함된 텍스트가 흰색으로 표시되기 때문에 배경색이 너무 밝지 않은지 확인합니다.
- 대표 아이콘이 있는 제품을 게시한 후에는 제거할 수 없습니다(원할 경우 다른 버전으로 업데이트할 수 있음).

**잠재 고객 관리** 섹션에서 잠재 고객이 저장될 CRM 시스템을 선택할 수 있습니다. [관리 서비스 인증 정책](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)에 따라 **잠재 대상 고객**이 필요합니다.

마지막으로 **법적 정보** 섹션에서 **개인정보취급방침 URL** 및 **사용 약관**을 제공합니다. 이 제품에 대해 [표준 계약](https://docs.microsoft.com/azure/marketplace/standard-contract)을 사용할지 여부를 여기에서 지정할 수도 있습니다.

**지원** 섹션으로 이동하기 전에 변경 내용을 저장해야 합니다.

## <a name="add-support-info"></a>지원 정보 추가

**지원** 섹션에서 엔지니어링 담당자와 고객 지원 담당자의 이름, 메일 및 전화 번호를 제공합니다. 지원 URL도 제공해야 합니다. Microsoft는 비즈니스 및 지원 문제에 대해 연락해야 하는 경우 이 정보를 사용할 수 있습니다.

이 정보를 추가한 후에는 **저장**을 선택합니다.

## <a name="publish-your-offer"></a>제품 게시

모든 섹션을 완료한 후, 다음 단계는 Azure Marketplace에 제품을 게시하는 것입니다. **게시** 단추를 선택하여 제품을 라이브로 전환하는 프로세스를 시작합니다. 이 프로세스에 대한 자세한 내용은 [Azure Marketplace 및 AppSource 제안 게시](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer)를 참조하세요.

언제든지 [제품의 업데이트된 버전을 게시](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-update-offer)할 수 있습니다. 예를 들어 이전에 게시된 제품에 새 역할 정의를 추가하려고 할 수 있습니다. 이렇게 하면 제품을 이미 추가한 고객은 Azure Portal의 [**서비스 공급자**](view-manage-service-providers.md) 페이지에 업데이트를 사용할 수 있음을 알려 주는 아이콘이 표시됩니다. 각 고객은 [변경 내용을 검토](view-manage-service-providers.md#update-service-provider-offers)하고 새 버전으로 업데이트할지 여부를 결정할 수 있습니다. 

## <a name="the-customer-onboarding-process"></a>고객 온보딩 프로세스

고객이 제품을 추가하면 하나 이상의 특정 구독 또는 리소스 그룹을 위임할 수 있습니다. 그러면 [Azure 위임 리소스 관리에 온보딩](view-manage-service-providers.md#delegate-resources)됩니다. 고객이 제품을 수락했지만 아직 리소스를 위임하지 않은 경우 Azure Portal에서 [**서비스 공급자**](view-manage-service-providers.md) 페이지의 **공급자 제품** 섹션 맨 위에 메모가 표시됩니다.

> [!IMPORTANT]
> 위임은 온보딩하려는 구독에 대해 [소유자 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)이 있는(또는 온보딩하려는 리소스 그룹을 포함하는) 고객 테넌트의 비게스트 계정이 수행해야 합니다. 구독을 위임할 수 있는 모든 사용자를 보기 위해 고객 테넌트의 사용자는 Azure Portal에서 구독을 선택하고, **IAM(액세스 제어)** 을 열고, [소유자 역할이 있는 모든 소유자를 볼 수 있습니다](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

고객이 구독 또는 구독 내의 하나 이상의 리소스 그룹을 위임한 후에는 해당 구독에 대해 **Microsoft ManagedServices** 리소스 공급자가 등록되고, 테넌트의 사용자는 제품의 권한 부여에 따라 위임된 리소스에 액세스할 수 있습니다.

> [!NOTE]
> 현재 구독에서 Azure Databricks를 사용하는 경우 구독 또는 구독 내의 리소스 그룹을 위임할 수 없습니다. 마찬가지로 구독 또는 구독 내의 리소스 그룹이 이미 위임된 경우에는 현재 해당 구독에서 Databricks 작업 영역을 만들 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객**으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
