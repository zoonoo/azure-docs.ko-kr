---
title: Azure Marketplace에서 가상 머신 제품을 만듭니다.
description: Microsoft 상업용 Marketplace에서 가상 머신 제품을 테스트하고 만드는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: 827e4d883fd9e80ae84845d620cc4ca00816f56e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551319"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace에서 가상 머신 제품을 만드는 방법

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com/)용 Azure 가상 머신 제품을 만드는 방법을 설명합니다. 운영 체제, VHD(가상 하드 디스크) 및 최대 16개의 데이터 디스크가 포함된 Windows 기반 및 Linux 기반 가상 머신을 모두 처리합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만듭니다](partner-center-portal/create-account.md). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

아직 수행하지 않은 경우 [가상 머신 제품 계획](marketplace-virtual-machines.md)을 검토합니다. 가신 머신에 대한 기술 요구 사항을 설명하고 제품을 만들 때 필요한 정보 및 자산을 나열합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 창에서 **상업용 마켓플레이스** > **개요** 를 차례로 선택합니다.
3. **개요** 페이지에서 **+ 새 제품** > **Azure Virtual Machine** 을 차례로 선택합니다.

    ![왼쪽 창 메뉴 옵션과 "새 제품" 단추를 보여 주는 스크린샷](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제품을 변경한 후에는 항상 다시 게시해 주세요.

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Azure Marketplace 제품의 웹 주소와 Azure PowerShell 및 Azure CLI(해당되는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1** 을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**별칭** 을 입력합니다. 제품 별칭은 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Azure Marketplace에서 사용되지 않습니다. 고객에게 표시되는 제품 이름 및 다른 값과는 다릅니다.

**만들기** 를 선택하여 제품을 생성하고 계속 진행합니다. 파트너 센터에서 **제품 설정** 페이지를 엽니다.

## <a name="enable-a-test-drive-optional"></a>시험 사용 지원(선택 사항)

시험 사용은 잠재 고객에게 정해진 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 제품을 소개하는 좋은 방법입니다. 시험 사용을 제공하면 전환율이 증가하고 충분한 자격을 갖춘 잠재 고객이 생성됩니다. 시험 사용에 대한 자세한 내용은 [시험 사용이란?](./what-is-test-drive.md)을 참조하세요.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 정해진 기간 동안 고객에게 솔루션을 제공합니다. 하지만 시험 사용에는 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 체험도 포함됩니다.

시험 사용을 사용하도록 설정하려면 **시험 사용 지원** 확인란을 선택합니다. 나중에 시험 사용을 구성합니다. 시험 사용을 통해 CRM을 구성해야 합니다(다음 섹션 참조).

## <a name="configure-customer-leads-management"></a>잠재 고객 관리 구성

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

왼쪽 탐색 메뉴에서 다음 탭을 계속하려면 **초안 저장** 을 선택하고 **속성** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [가상 머신 제품 속성을 구성하는 방법](azure-vm-create-properties.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)