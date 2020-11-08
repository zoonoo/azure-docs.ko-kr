---
title: Azure 응용 프로그램 제품에 대 한 Azure 관리 응용 프로그램 계획
description: Microsoft 파트너 센터에서 상용 marketplace 포털을 사용 하 여 새 Azure 응용 프로그램 제품에 대 한 관리 되는 응용 프로그램 계획을 만드는 데 필요한 사항에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: fbb78148418206a9a6e17b29ce17de0a23491839
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370214"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Azure 응용 프로그램 제품에 대 한 Azure 관리 응용 프로그램 계획

Azure _관리 되는 응용 프로그램_ 계획은 Azure Marketplace에 azure 응용 프로그램 제품을 게시 하는 한 가지 방법입니다. 아직 수행 하지 않은 경우 [상업용 marketplace에 대 한 Azure 애플리케이션 제품 계획](plan-azure-application-offer.md)을 읽어 보세요.

관리 되는 응용 프로그램은 Azure Marketplace를 통해 배포 되 고 청구 되는 transact-sql 제품입니다. 사용자에 게 표시 되는 목록 옵션은 지금 가져옵니다.

다음 조건이 필요한 경우 Azure 애플리케이션: 관리 되는 응용 프로그램 계획을 사용 합니다.

- VM (가상 머신) 또는 전체 IaaS (infrastructure as a service) 기반 솔루션을 사용 하 여 고객을 위한 구독 기반 솔루션을 배포 합니다.
- 사용자 또는 고객은 파트너에 의해 솔루션을 관리 해야 합니다. 예를 들어 파트너는 시스템 통합자 또는 MSP (관리 서비스 공급자) 일 수 있습니다.

## <a name="managed-application-offer-requirements"></a>관리 되는 응용 프로그램 제공 요구 사항

| 요구 사항 | 세부 정보 |
| ------------ | ------------- |
| Azure 구독 | 관리 되는 응용 프로그램은 고객의 구독에 배포 되어야 하지만 타사에서 관리할 수 있습니다. |
| 청구 및 계량 | 리소스는 고객의 Azure 구독에서 제공 됩니다. 종 량 제 지불 모델을 사용 하는 Vm은 Microsoft를 통해 고객과 거래 되며 고객의 Azure 구독을 통해 청구 됩니다. <br><br> 사용자 라이선스 Vm의 경우 Microsoft는 고객 구독에서 발생 하는 모든 인프라 비용을 청구 하지만 고객과 직접 소프트웨어 라이선스 요금을 지불 합니다. |
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요.<br> • [AZURE VM 기술 자산](/azure/marketplace/partner-center-portal/vm-certification-issues-solutions#how-to-address-a-vulnerability-or-exploit-in-a-vm-offer.md) (Windows vhd의 경우)을 만듭니다.<br> • Linux  [배포판 보증 On Azure](/azure/virtual-machines/linux/endorsed-distros.md) (linux vhd의 경우). |
| 고객 사용량 특성 | 모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다. 고객 사용 특성 및이를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure 파트너 고객 사용 특성](azure-partner-customer-usage-attribution.md)을 참조 하세요. |
| 배포 패키지 | 고객이 요금제를 배포할 수 있도록 하는 배포 패키지가 필요 합니다. 동일한 기술 구성이 필요한 여러 계획을 만드는 경우 동일한 패키지를 사용할 수 있습니다. 자세한 내용은 다음 섹션인 배포 패키지를 참조 하세요. |
|||

> [!NOTE]
> 관리 되는 응용 프로그램은 Azure Marketplace를 통해 배포 가능 해야 합니다. 고객 통신이 중요 한 경우 잠재 고객 공유를 사용 하도록 설정한 후 관심 있는 고객에 게 연락 하세요.

## <a name="deployment-package"></a>배포 패키지

배포 패키지에는이 계획에 필요한 모든 템플릿 파일 뿐만 아니라 .zip 파일로 패키지 된 추가 리소스도 포함 됩니다.

모든 Azure 응용 프로그램은 .zip 보관 파일의 루트 폴더에 다음 두 개의 파일을 포함 해야 합니다.

- [mainTemplate.json](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md)이라는 Resource Manager 템플릿 파일. 이 템플릿은 고객의 Azure 구독에 배포할 리소스를 정의합니다. 리소스 관리자 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조 하세요.
- [createUiDefinition.json](/azure/azure-resource-manager/managed-application-createuidefinition-overview.md)이라는 Azure 애플리케이션 만들기 환경의 사용자 인터페이스 정의. 사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

지원되는 최대 파일 크기는 다음과 같습니다.

- 총 압축 된 .zip 보관 파일 크기 (최대 1gb)
- .Zip 보관 내에서 압축 되지 않은 개별 파일에 대해 최대 1gb

모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다.

## <a name="azure-regions"></a>Azure 지역

Azure 공용 지역, Azure Government 지역 또는 둘 다에 계획을 게시할 수 있습니다. [Azure Government](/azure/azure-government/documentation-government-manage-marketplace-partners.md)에 게시하기 전에 특정 엔드포인트가 다를 수 있으므로 환경에서 플랜을 테스트하고 유효성을 검사합니다. 플랜을 설정하고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청합니다.

게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

상용 marketplace에서 지원 되는 국가 및 지역 목록은 [지리적 가용성 및 통화 지원](marketplace-geo-availability-currencies.md)을 참조 하세요.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 관한 설명에 연결되는 링크일 수 있습니다. 이 링크는 Azure Government 고객에게만 표시됩니다.

## <a name="choose-who-can-see-your-plan"></a>요금제를 볼 수 있는 사용자 선택

모든 사용자 (공용) 또는 특정 대상 그룹 (개인)에 게 표시 되도록 각 계획을 구성할 수 있습니다. 최대 100 개의 요금제를 만들 수 있으며 최대 45 개의 요금제를 개인용으로 지정할 수 있습니다. 특정 고객에 게 다양 한 가격 책정 옵션이 나 기술 구성을 제공 하는 비공개 요금제를 만들 수 있습니다.

사용자가 할당 한 각 구독 ID에 대 한 설명을 포함 하는 옵션과 함께 Azure 구독 Id를 사용 하 여 개인 계획에 대 한 액세스 권한을 부여 합니다. 을 사용 하 여 최대 10 개의 구독 Id를 수동으로 또는 최대 1만 구독 Id를 추가할 수 있습니다. CSV 파일. Azure 구독 ID는 GUID로 표시되며 문자는 소문자여야 합니다.

사설 요금제는 클라우드 솔루션 공급자 프로그램 (CSP)의 대리점을 통해 설정 된 Azure 구독에서 지원 되지 않습니다. 자세한 내용은 [Microsoft 상업적 marketplace의 개인 제품](private-offers.md)을 참조 하세요.

> [!NOTE]
> 비공개 요금제를 게시 하는 경우 나중에 공용으로 표시를 변경할 수 있습니다. 그러나 공개 계획을 게시 한 후에는 해당 표시 유형을 개인으로 변경할 수 없습니다.

## <a name="define-pricing"></a>가격 책정 정의

각 요금제에 대 한 월별 가격을 제공 해야 합니다. 이 가격은 이 솔루션을 통해 배포된 리소스에 의해 발생하는 Azure 인프라 또는 종량제 소프트웨어 비용에 추가됩니다.

또한 월별 가격 외에 [요금 청구](partner-center-portal/azure-app-metered-billing.md)를 사용하여 비표준 단위의 사용량에 해당하는 가격을 설정할 수 있습니다. 월별 가격을 0으로 설정하고 원하는 경우 요금 청구를 사용하여 전용 요금을 청구할 수 있습니다.

가격이 USD (USD = 미국 달러)로 설정 된 경우에는 저장할 때 현재 환율을 사용 하 여 선택한 모든 시장의 현지 통화로 변환 됩니다. 하지만 각 시장에 대해 고객 가격을 설정 하도록 선택할 수 있습니다.

## <a name="just-in-time-jit-access"></a>JIT (just-in-time) 액세스

JIT 액세스를 사용하면 문제 해결 또는 유지 관리를 위해 관리형 애플리케이션의 리소스에 대한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대한 읽기 전용 액세스 권한을 가지지만 특정 기간 동안 더 큰 액세스 권한을 가질 수 있습니다. 자세한 내용은 [Azure Managed Applications의 JIT(Just-In-Time) 액세스 사용 및 요청](/azure/managed-applications/request-just-in-time-access.md)을 참조하세요.

> [!NOTE]
> 이 기능을 지원 하기 위해 skimmingBe에서 파일을 업데이트 해야 하는 경우에도 사용자에 게 알리는 정보 `createUiDefinition.json` 입니다.

## <a name="deployment-mode"></a>배포 모드

**전체** 또는 **증분** 배포 모드를 사용 하도록 관리 되는 응용 프로그램 계획을 구성할 수 있습니다. 완료 모드에서는 고객이의 [mainTemplate.js](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md)에 리소스가 정의 되어 있지 않은 경우 고객이 응용 프로그램을 다시 배포 하 여 관리 되는 리소스 그룹의 리소스를 제거 합니다. 증분 모드에서 애플리케이션을 다시 배포하면 기존 리소스가 변경되지 않습니다. 자세히 알아보려면 [Azure Resource Manager 배포 모드](/azure/azure-resource-manager/templates/deployment-modes.md?WT.mc_id=pc_52)를 참조 하세요.

## <a name="notification-endpoint-url"></a>알림 엔드포인트 URL

필요에 따라 계획의 관리 되는 응용 프로그램 인스턴스에 대 한 모든 CRUD 작업에 대 한 알림을 수신 하는 HTTPS Webhook 끝점을 제공할 수 있습니다.

## <a name="customize-allowed-customer-actions-optional"></a>허용 된 고객 작업 사용자 지정 (선택 사항)

필요에 따라 `*/read` 기본적으로 사용 가능한 작업 외에도 고객이 관리 되는 리소스에 대해 수행할 수 있는 작업을 지정할 수 있습니다.

이 옵션을 선택 하는 경우 제어 작업 또는 허용 되는 데이터 작업 중 하나 또는 둘 모두를 제공 해야 합니다. 자세한 내용은 [Azure 리소스에 대한 거부 할당 이해](/azure/role-based-access-control/deny-assignments.md)를 참조하세요. 사용 가능한 작업에 대해서는 [Azure Resource Manager 리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations.md)을 참조하세요. 예를 들어 소비자가 가상 머신을 다시 시작하도록 허용하려면 허용되는 작업에 `Microsoft.Compute/virtualMachines/restart/action`을 추가합니다.

## <a name="choose-who-can-manage-the-application"></a>응용 프로그램을 관리할 수 있는 사용자 선택

선택한 각 클라우드에서 관리 되는 응용 프로그램을 관리할 수 있는 사용자 ( _공용 Azure_ 및 _Azure Government 클라우드_ )를 지정 해야 합니다. 다음과 같은 정보를 수집합니다.

- **Azure Active Directory 테 넌 트 id** – 사용 권한을 부여 하려는 사용자, 그룹 또는 응용 프로그램의 id를 포함 하는 Azure AD 테 넌 트 id (디렉터리 ID 라고도 함)입니다. Azure Portal에서 [Azure Active Directory 속성](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)의 Azure AD 테 넌 트 ID를 찾을 수 있습니다.
- **권한 부여 – 관리** 되는 리소스 그룹에 권한을 부여 하려는 각 사용자, 그룹 또는 응용 프로그램의 AZURE ACTIVE DIRECTORY 개체 ID를 추가 합니다. 사용자를 해당 보안 주체 ID로 식별하며, 이 ID는 [Azure Portal의 Azure Active Directory 사용자 블레이드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)에서 찾을 수 있습니다.

각 보안 주체 ID에 대해 Azure AD 기본 제공 역할 (소유자 또는 참가자) 중 하나를 연결 합니다. 선택한 역할은 사용자가 고객 구독의 리소스에 대해 가질 수 있는 권한을 설명 합니다. 자세한 정보는 [Azure 기본 제공 역할](/azure/role-based-access-control/built-in-roles.md)을 참조하세요. RBAC(역할 기반 액세스 제어)에 관한 자세한 내용은 [Azure Portal에서 RBAC 시작](/azure/role-based-access-control/overview.md)을 참조하세요.

> [!NOTE]
> Azure 지역 당 최대 100 권한 부여를 추가할 수 있지만, 일반적으로 Active Directory 사용자 그룹을 만들고 "보안 주체 ID"에 해당 ID를 지정 하는 것이 더 쉽습니다. 이렇게 하면 계획이 배포 된 후 관리 그룹에 더 많은 사용자를 추가할 수 있으며, 더 많은 권한 부여를 추가 하기 위해 계획을 업데이트할 필요성이 줄어듭니다.

## <a name="policy-settings"></a>정책 설정

[Azure 정책을](/azure/governance/policy.md) 관리 되는 응용 프로그램에 적용 하 여 배포 된 솔루션에 대 한 규정 준수 요구 사항을 지정할 수 있습니다. 매개 변수 값의 형식 및 정책 정의는 [Azure Policy 샘플](/azure/governance/policy/samples.md)을 참조하세요.

최대 5 개의 정책 및 각 정책 형식의 인스턴스를 하나만 구성할 수 있습니다. 일부 정책 형식에는 추가 매개 변수가 필요 합니다.

| 정책 유형 | 정책 매개 변수가 필요 합니다. |
| ------------ | ------------- |
| 암호화 Azure SQL Database | 예 |
| Azure SQL Server 감사 설정 | yes |
| 암호화 Azure Data Lake Store | 예 |
| 진단 설정 감사 | yes |
| 감사 리소스 위치 준수 | 예 |
|||

추가 하는 각 정책 유형에 대해 Standard 또는 Free 정책 SKU를 연결 해야 합니다. 표준 SKU는 감사 정책에 필요합니다. 정책 이름은 50 자로 제한 됩니다.

## <a name="next-steps"></a>다음 단계

- [상용 marketplace에서 Azure 응용 프로그램 제품을 만드는 방법](create-new-azure-apps-offer.md)
