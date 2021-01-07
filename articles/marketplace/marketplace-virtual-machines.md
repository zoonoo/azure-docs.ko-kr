---
title: 가상 머신 제안 계획-Microsoft 상업적 marketplace
description: 이 문서에서는 Azure Marketplace에 가상 머신 제품을 게시 하기 위한 요구 사항을 설명 합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 433875e52e77a8992197b47fe0424cd0b7e9f50c
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628291"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>가상 컴퓨터 제품을 계획 하는 방법

이 문서에서는 가상 머신 (VM) 제품을 상용 marketplace에 게시 하기 위한 다양 한 옵션 및 요구 사항을 설명 합니다. VM 제공은 Azure Marketplace를 통해 배포 되 고 청구 되는 불가능 제품입니다.

시작 하기 전에 [파트너 센터에서 상용 marketplace 계정을 만들고](./partner-center-portal/create-account.md) 계정이 상업적 marketplace 프로그램에 등록 되었는지 확인 합니다.

### <a name="technical-fundamentals"></a>기술 기본 사항

제품을 디자인, 구축 및 테스트 하는 과정은 시간이 걸리고 제품을 빌드하는 데 사용 되는 Azure 플랫폼 및 기술에 대 한 전문 지식이 필요 합니다. 엔지니어링 팀은 azure [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)및 [azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)에 대 한 실무 지식을 갖추고 있으며 [azure 응용 프로그램의 설계 및 아키텍처](https://azure.microsoft.com/solutions/architecture/)를 숙련도 해야 합니다. 이러한 추가 기술 리소스를 참조 하세요. 

- 자습서
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- 샘플
  - [Linux VM용 Azure CLI 샘플](../virtual-machines/linux/cli-samples.md)
  - [Linux VM용 Azure PowerShell 샘플](../virtual-machines/linux/powershell-samples.md)
  - [Windows VM용 Azure CLI 샘플](../virtual-machines/windows/cli-samples.md)
  - [Windows VM용 Azure PowerShell 샘플](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

## <a name="technical-requirements"></a>기술적인 요구 사항

VM 제공의 기술 요구 사항은 다음과 같습니다.

- 운영 체제 VHD (가상 하드 디스크) 하나를 준비 해야 합니다. 데이터 디스크 Vhd는 선택 사항입니다. 이에 대해서는 아래에서 자세히 설명 합니다.
- 고객은 언제 든 지 제품을 취소할 수 있습니다.
- 제품에 대 한 계획을 하나 이상 만들어야 합니다. 요금제는 선택한 [라이선스 옵션](#licensing-options) 에 따라 가격이 책정 됩니다.
   > [!IMPORTANT]
   > 계획의 모든 VM 이미지에는 동일한 수의 데이터 디스크가 있어야 합니다.

VM에는 다음 두 가지 구성 요소가 포함 됩니다.

- **운영 VHD** – 제품과 함께 배포 하는 운영 체제 및 솔루션을 포함 합니다. VHD를 준비 하는 프로세스는 Linux, Windows 또는 사용자 지정 기반 VM 인지에 따라 달라 집니다.
- **데이터 디스크 vhd** (선택 사항) – VM의 전용 영구 저장소입니다. 운영 체제 VHD(예: C: 드라이브)를 사용하여 영구 정보를 저장하지 마세요. 
    - 최대 16 개의 데이터 디스크를 포함할 수 있습니다.
    - 디스크가 비어 있는 경우에도 데이터 디스크당 하나의 VHD를 사용합니다.

    > [!NOTE]
    > 사용 중인 운영 체제에 상관없이 솔루션에 필요한 최소 개수의 데이터 디스크만 추가합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만, 배포 중 또는 배포 후에 언제든지 디스크를 추가할 수 있습니다.

기술 자산을 준비 하는 방법에 대 한 자세한 지침은 [승인 된 기본을 사용 하 여 가상 머신 만들기](azure-vm-create-using-approved-base.md) 또는 [고유한 이미지를 사용 하 여 가상 머신 만들기](azure-vm-create-using-own-image.md)를 참조 하세요.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 대상 사용자는 라이브 게시 전에 종단 간 기능을 테스트 하기 위해 Azure Marketplace에 라이브 게시 되기 전에 VM 제안에 액세스할 수 있습니다. **대상 미리 보기** 페이지에서 제한 된 미리 보기 대상 그룹을 정의할 수 있습니다. 

> [!NOTE]
> 미리 보기 대상 그룹은 개인 계획과 다릅니다. 비공개 요금제는 사용자가 선택 하는 특정 대상에만 사용할 수 있도록 하는 계획입니다. 이렇게 하면 사용자 지정 요금제를 특정 고객과 협상할 수 있습니다. 자세한 내용은 다음 섹션인 계획을 참조 하세요.

Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 전자 메일 주소로 초대를 보낼 수 있습니다. 전자 메일 주소를 최대 10 개까지 수동으로 추가 하거나 .csv 파일을 사용 하 여 최대 20 개까지 가져오세요. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 테스트 하기 위한 미리 보기 대상을 계속 정의할 수 있습니다.

## <a name="plans-and-pricing"></a>플랜 및 가격 책정

VM 제공에는 하나 이상의 계획이 필요 합니다. 계획은 솔루션 범위와 제한 및 관련 된 가격을 정의 합니다. 제품에 대 한 여러 요금제를 만들어 고객에 게 다른 기술 및 라이선스 옵션과 무료 평가판을 제공할 수 있습니다. 가격 책정 모델, 무료 평가판 및 비공개 요금제를 비롯 한 요금제에 대 한 일반적인 지침은 [상업적 marketplace 제품에 대 한 계획 및 가격 책정](plans-pricing.md) 을 참조 하세요. 

Vm은 종 량 제 또는 BYOL (사용자 라이선스 보유) 라이선스 모델을 사용 하 여 완벽 하 게 상거래를 지원 합니다. Microsoft는 사용자 대신, 상거래를 주도하고 고객에게 요금을 청구합니다. 사용자는 기업계약을 비롯하여 고객과 Microsoft 간에 선호하는 결제 방식을 채택할 수 있습니다. 자세한 내용은 [상업적 marketplace의 기능](./marketplace-commercial-transaction-capabilities-and-considerations.md)을 참조 하세요.

> [!NOTE]
> 기업계약와 관련 된 금액 약정은 VM의 Azure 사용에 대해 사용할 수 있지만 소프트웨어 라이선스 요금에 대해서는 사용할 수 없습니다.

### <a name="licensing-options"></a>라이선스 옵션

새 VM 제품 게시를 준비할 때 선택할 라이선스 옵션을 결정 해야 합니다. 파트너 센터에서 제품을 만들 때 나중에 제공 해야 하는 추가 정보를 결정 합니다.

VM 제품에 사용할 수 있는 라이선스 옵션은 다음과 같습니다.

| 라이선싱 옵션 | 트랜잭션 프로세스 |
| --- | --- |
| 평가판 | 고객에 게 1, 3 또는 6 개월 무료 평가판을 제공 합니다. |
| 시험 사용 | 이 옵션을 사용 하면 고객이 추가 비용 없이 Vm을 평가할 수 있습니다. 평가판 환경에 참여 하기 위해 기존 Azure 고객이 될 필요는 없습니다. 자세한 내용은 [테스트 드라이브인 정의](./what-is-test-drive.md) 를 참조 하세요. |
| BYOL | 사용자의 라이선스 가져오기 옵션을 사용 하면 고객이 기존 소프트웨어 라이선스를 Azure에 가져올 수 있습니다.\* |
| 사용량 기반 | 종 량 제이 라고도 하는이 옵션을 사용 하면 고객이 시간당 요금을 지불할 수 있습니다. |
| 대화형 데모  | 대화형 데모를 사용 하 여 고객에 게 솔루션에 대 한 안내 환경을 제공 합니다. 이를 통해 복잡 한 솔루션을 복잡 하 게 설정 하지 않고도 평가판 환경을 제공할 수 있습니다. |
|

\* 게시자는 주문, 배송, 계량, 청구, 청구, 지불 및 수집을 포함 하 여 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원 합니다 (이에 국한 되지 않음).

다음 예제에서는 사용량 기반 가격 책정을 사용 하는 Azure Marketplace의 VM 제품을 보여 줍니다.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="샘플 VM 제품 화면.":::

### <a name="private-plans"></a>비공개 계획

이미지와 가격을 비공개 계획으로 게시 하 여 VM의 검색 및 배포를 특정 고객 집합으로 제한할 수 있습니다. 비공개 요금제는 가장 가까운 고객을 위한 독점적인 제품을 만들고 사용자 지정 된 소프트웨어 및 조건을 제공 하는 기능을 잠금 해제 합니다. 이러한 사용자 지정된 사용 약관을 사용하면 사용자가 전문화된 가격 책정 및 조건뿐 아니라 제한된 릴리스 소프트웨어에 대한 초기 액세스를 사용하여 선도적인 거래를 포함한 다양한 시나리오를 강조 표시할 수 있습니다. 비공개 요금제를 사용 하면 제한 된 고객 집합에 특정 가격 책정 또는 제품을 제공할 수 있습니다.

자세한 내용은 [Microsoft 상업적 marketplace에서](private-offers.md) [상업적 marketplace 제품에 대 한 계획 및 가격 책정](plans-pricing.md) 및 개인 제공을 참조 하세요.

## <a name="test-drives"></a>시험 사용

VM에 대 한 테스트 드라이브를 사용 하도록 선택할 수 있습니다. 시험 사용을 통해 고객은 고정 된 시간 동안 미리 구성 된 환경에 액세스할 수 있습니다. 모든 게시 옵션에 대해 테스트 드라이브를 사용 하도록 설정할 수 있지만이 기능에는 추가 요구 사항이 있습니다. 테스트 드라이브에 대 한 자세한 내용은 [test drive 란?](what-is-test-drive.md)을 참조 하세요. 다양 한 종류의 테스트 드라이브를 구성 하는 방법에 대 한 자세한 내용은 [테스트 드라이브 기술 구성](test-drive-technical-configuration.md)을 참조 하세요.

> [!TIP]
> 시험 [사용은 평가판](plans-pricing.md#free-trials)과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 사용자는 일정 기간 동안 고객에 게 솔루션을 제공 합니다. 그러나 테스트 드라이브에는 실제 구현 시나리오에서 설명 하는 제품의 주요 기능 및 이점에 대 한 실습 자체 둘러보기도 포함 되어 있습니다.

## <a name="customer-leads"></a>잠재 고객

제품을 CRM (고객 관계 관리) 시스템에 연결 하 여 고객 정보를 수집 해야 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 제품을 찾은 제품 이름, ID 및 온라인 스토어와 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. 상업적 marketplace는 Azure 테이블을 사용 하거나 전원 자동화를 사용 하 여 HTTPS 끝점을 구성 하는 옵션과 함께 다양 한 CRM 시스템을 지원 합니다.

제공을 만든 후 또는 후에 언제 든 지 CRM 연결을 추가 하거나 수정할 수 있습니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 상업적 marketplace에서 제품에 사용할 수 있는 표준 계약을 제공 합니다. 표준 계약에 따라 소프트웨어를 제공 하는 경우 고객은 한 번만 읽고 동의 하면 되며 사용자 지정 사용 약관을 만들 필요가 없습니다.

표준 계약을 사용 하도록 선택 하는 경우 표준 계약에 유니버설 수정 용어 및 최대 10 개의 사용자 지정 개정를 추가할 수 있습니다. 표준 계약 대신 사용자 고유의 사용 조건을 사용할 수도 있습니다. 이러한 세부 정보는 **속성** 페이지에서 관리 합니다. 자세한 내용은 [Microsoft 상업적 marketplace에 대 한 표준 계약](standard-contract.md)을 참조 하세요.

> [!NOTE]
> 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 한 후에는 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약이 나 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

## <a name="cloud-solution-providers"></a>클라우드 솔루션 공급자

파트너 센터에서 제품을 만들 때 **csp를 통해 재판매** 탭이 표시 됩니다. 이 옵션을 사용 하면 CSP (Microsoft 클라우드 솔루션 공급자) 프로그램의 일부인 파트너가 번들 제안의 일부로 VM을 재판매 수 있습니다. 모든 BYOL(사용자 라이선스 필요) 플랜은 자동으로 프로그램에 옵트인됩니다. 또한 비 BYOL 플랜을 옵트인하도록 선택할 수도 있습니다. 자세한 내용은 [클라우드 솔루션 공급자 프로그램](cloud-solution-providers.md) 을 참조 하세요. 

> [!NOTE]
> 이제 CSP (클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대 한 자세한 내용은 [**클라우드 솔루션 공급자**](./cloud-solution-providers.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Marketplace에서 가상 컴퓨터 제품 만들기](azure-vm-create.md)
- [승인 된 기반을 사용 하 여 가상 컴퓨터를 만들거나](azure-vm-create-using-approved-base.md) [사용자 고유의 이미지를 사용 하 여 가상 컴퓨터를 만듭니다](azure-vm-create-using-own-image.md).
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)