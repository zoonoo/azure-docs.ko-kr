---
title: Azure 응용 프로그램 제품에 대 한 솔루션 템플릿 계획
description: Microsoft 파트너 센터에서 상용 marketplace 포털을 사용 하 여 새 Azure 응용 프로그램 제품에 대 한 솔루션 템플릿 계획을 만드는 데 필요한 사항에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 38c7072472a13d7fe3d529933ca17a51e6a86733
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577807"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Azure 응용 프로그램 제품에 대 한 솔루션 템플릿 계획

이 문서에서는 Azure 애플리케이션 제품에 대 한 솔루션 템플릿 계획을 게시 하기 위한 요구 사항을 설명 합니다. 솔루션 템플릿 계획은 Azure 애플리케이션 제품에서 지 원하는 두 가지 유형의 계획 중 하나입니다. 이러한 두 계획 유형 간의 차이점에 대 한 자세한 내용은 [계획 유형](plan-azure-application-offer.md#plans)을 참조 하세요. 아직 수행 하지 않은 경우 [Azure 응용 프로그램 제안 계획](plan-azure-application-offer.md)을 참조 하세요.

솔루션 템플릿 계획 유형을 사용 하려면 솔루션 인프라를 자동으로 배포 하는 [Azure Resource Manager 템플릿 (ARM 템플릿)](/azure/azure-resource-manager/templates/overview) 이 필요 합니다.

## <a name="solution-template-requirements"></a>솔루션 템플릿 요구 사항

| 요구 사항 | 세부 정보 |
| ------------ | ------------- |
| 청구 및 계량 | 솔루션 템플릿 계획은 불가능 되지 않지만 Microsoft 상업적 marketplace를 통해 청구 되는 유료 VM 제품을 배포 하는 데 사용할 수 있습니다. 솔루션의 ARM 템플릿에서 배포 하는 리소스는 고객의 Azure 구독에 설정 됩니다. 종 량 제 가상 머신은 Microsoft를 통해 고객과 거래 되며 고객의 Azure 구독을 통해 청구 됩니다. <br><br> BYOL (사용자 라이선스 보유) 청구의 경우 Microsoft는 고객 구독에서 발생 하는 인프라 비용을 청구 하지만 고객과 직접 소프트웨어 라이선스 요금을 지불 합니다. |
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요.<ul><li>[AZURE VM 기술 자산 만들기](/azure/marketplace/partner-center-portal/vm-certification-issues-solutions#how-to-address-a-vulnerability-or-exploit-in-a-vm-offer.md) (Windows vhd의 경우)</li><li>[Azure에서 linux 배포판 보증](/azure/virtual-machines/linux/endorsed-distros) (linux vhd의 경우).</li></ul> |
| 고객 사용량 특성 | Azure Marketplace에 게시 된 모든 솔루션 템플릿에서 고객 사용 특성을 사용 하도록 설정 해야 합니다. 고객 사용 특성 및이를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure 파트너 고객 사용 특성](azure-partner-customer-usage-attribution.md)을 참조 하세요. |
| 관리 디스크 사용 | [Managed disks](/azure/virtual-machines/windows/managed-disks-overview) 는 Azure에서 IaaS (infrastructure as a Service) vm의 지속형 디스크에 대 한 기본 옵션입니다. 솔루션 템플릿에서 managed disks를 사용 해야 합니다.<ul><li>솔루션 템플릿을 업데이트 하려면 [Azure Resource Manager 템플릿에서 managed Disks 사용](/azure/virtual-machines/using-managed-disks-template-deployments)의 지침을 따르고 제공 된 [샘플](https://github.com/Azure/azure-quickstart-templates)을 사용 합니다.</li><li>VHD를 Azure Marketplace 이미지로 게시 하려면 [Azure PowerShell](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) 또는 [Azure CLI](/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) 를 사용 하 여 관리 디스크의 기본 VHD를 저장소 계정으로 가져옵니다.</ul> |
| 배포 패키지 | 고객이 요금제를 배포할 수 있도록 하는 배포 패키지가 필요 합니다. 동일한 기술 구성이 필요한 여러 계획을 만드는 경우 동일한 계획 패키지를 사용할 수 있습니다. 자세한 내용은 다음 섹션인 배포 패키지를 참조 하세요. |
|||

## <a name="deployment-package"></a>배포 패키지

배포 패키지에는이 계획에 필요한 모든 템플릿 파일 뿐만 아니라 .zip 파일로 패키지 된 추가 리소스도 포함 됩니다.

모든 Azure 응용 프로그램은 .zip 보관 파일의 루트 폴더에 다음 두 개의 파일을 포함 해야 합니다.

- [mainTemplate.json](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md)이라는 Resource Manager 템플릿 파일. 이 템플릿은 고객의 Azure 구독에 배포할 리소스를 정의합니다. Resource Manager 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조하세요.
- [createUiDefinition.json](/azure/azure-resource-manager/managed-application-createuidefinition-overview)이라는 Azure 애플리케이션 만들기 환경의 사용자 인터페이스 정의. 사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

지원되는 최대 파일 크기는 다음과 같습니다.

- 총 압축 된 .zip 보관 파일 크기 (최대 1gb)
- .Zip 보관 내에서 압축 되지 않은 개별 파일에 대해 최대 1gb

모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다.

## <a name="azure-regions"></a>Azure 지역

Azure 공용 지역, Azure Government 지역 또는 둘 다에 계획을 게시할 수 있습니다. [Azure Government](/azure/azure-government/documentation-government-manage-marketplace-partners)에 게시하기 전에 특정 엔드포인트가 다를 수 있으므로 환경에서 플랜을 테스트하고 유효성을 검사합니다. 플랜을 설정하고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청합니다.

게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

상용 marketplace에서 지원 되는 국가 및 지역 목록은 [지리적 가용성 및 통화 지원](marketplace-geo-availability-currencies.md)을 참조 하세요.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 관한 설명에 연결되는 링크일 수 있습니다. 이 링크는 Azure Government 고객에게만 표시됩니다.

## <a name="choose-who-can-see-your-plan"></a>요금제를 볼 수 있는 사용자 선택

모든 사용자 (공용) 또는 특정 대상 그룹 (개인)에 게 표시 되도록 각 계획을 구성할 수 있습니다. 최대 100 개의 요금제를 만들 수 있으며 최대 45 개의 요금제를 개인용으로 지정할 수 있습니다. 특정 고객에 게 다양 한 가격 책정 옵션이 나 기술 구성을 제공 하는 비공개 요금제를 만들 수 있습니다.

사용자가 할당 한 각 구독 ID에 대 한 설명을 포함 하는 옵션과 함께 Azure 구독 Id를 사용 하 여 개인 계획에 대 한 액세스 권한을 부여 합니다. 을 사용 하 여 최대 10 개의 구독 Id를 수동으로 또는 최대 1만 구독 Id를 추가할 수 있습니다. CSV 파일. Azure 구독 ID는 GUID로 표시되며 문자는 소문자여야 합니다.

> [!NOTE]
> 비공개 요금제를 게시 하는 경우 나중에 공용으로 표시를 변경할 수 있습니다. 그러나 공개 계획을 게시 한 후에는 해당 표시 유형을 개인으로 변경할 수 없습니다.

솔루션 템플릿 계획의 경우 Azure Marketplace에서 계획을 숨기도록 선택할 수도 있습니다. 계획이 다른 솔루션 템플릿 또는 관리 되는 응용 프로그램을 통해서만 간접적으로 배포 되는 경우이 작업을 수행 하는 것이 좋습니다.

> [!NOTE]
> 사설 요금제는 클라우드 솔루션 공급자 프로그램 (CSP)의 대리점을 통해 설정 된 Azure 구독에서 지원 되지 않습니다.

자세한 내용은 [Microsoft 상업적 marketplace의 개인 제품](private-offers.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [상용 marketplace에서 Azure 응용 프로그램 제품을 만드는 방법](create-new-azure-apps-offer.md)
