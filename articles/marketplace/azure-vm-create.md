---
title: Azure Marketplace에서 가상 컴퓨터 제품을 만듭니다.
description: Microsoft 상업적 marketplace에서 virtual machine 제품을 만드는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 5964292d7037465456efe22283a5552a2bfc7ad8
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331263"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace에서 가상 컴퓨터 제품을 만드는 방법

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에 대 한 Azure 가상 머신 제품을 만드는 방법을 설명 합니다. 운영 체제, VHD(가상 하드 디스크) 및 최대 16개의 데이터 디스크가 포함된 Windows 기반 및 Linux 기반 가상 머신을 모두 처리합니다.

시작 하기 전에 [파트너 센터에서 상용 marketplace 계정을 만듭니다](partner-center-portal/create-account.md). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

아직 수행 하지 않은 경우 [가상 머신 제안 계획](marketplace-virtual-machines.md)을 검토 합니다. 여기에서는 가상 머신에 대 한 기술 요구 사항을 설명 하 고 제품을 만들 때 필요한 정보 및 자산을 나열 합니다. 

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 창에서 **상업용 마켓플레이스** > **개요**를 차례로 선택합니다.
3. **개요** 페이지에서 **새 제품** > **Azure Virtual Machine**을 차례로 선택합니다.

    ![왼쪽 창 메뉴 옵션과 "새 제품" 단추를 보여 주는 스크린샷](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제품을 변경한 후에는 항상 다시 게시해 주세요.

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Azure Marketplace 제품의 웹 주소와 Azure PowerShell 및 Azure CLI(해당되는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**별칭**을 입력합니다. 제품 별칭은 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Azure Marketplace에서 사용되지 않습니다. 고객에게 표시되는 제품 이름 및 다른 값과는 다릅니다.

## <a name="enable-a-test-drive-optional"></a>테스트 드라이브 사용 (선택 사항)

시험 사용은 고정 된 시간 동안 미리 구성 된 환경에 대 한 액세스 권한을 부여 하 여 잠재 고객에 게 제품을 소개 하는 좋은 방법입니다. 테스트 드라이브를 제공 하면 변환 비율이 증가 하 고 우량으로 선별 된 잠재 고객을 생성 합니다. 테스트 드라이브에 대 한 자세한 내용은 [test drive 란?](partner-center-portal/test-drive.md)을 참조 하세요.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 고객에 게 고정 기간에 대 한 솔루션을 제공 합니다. 그러나 테스트 드라이브에는 실제 구현 시나리오에서 설명 하는 제품의 주요 기능 및 이점에 대 한 실습 자체 둘러보기도 포함 되어 있습니다.

**테스트 드라이브를 사용 하도록 설정 하려면**
1.  **시험** **사용에서 테스트 드라이브 사용** 확인란을 선택 합니다.
1.  표시 되는 목록에서 테스트 드라이브 유형을 선택 합니다.

## <a name="configure-lead-management"></a>잠재 고객 관리 구성

파트너 센터를 사용하여 제품을 상업용 마켓플레이스에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 테스트 드라이브를 사용 하도록 설정 하려면 CRM에 연결 해야 합니다 (이전 섹션 참조). 그렇지 않으면 CRM에 연결하는 것은 선택 사항입니다.

1. **고객 리드**에서 **연결** 링크를 선택 합니다.
1. **연결 정보** 대화 상자의 목록에서 리드 대상을 선택 합니다.
1. 표시 되는 필드를 완료 합니다. 자세한 단계는 다음 문서를 참조 하세요.

   - [Azure 테이블로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 고객 참여를 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (이전의 Dynamics CRM Online)
   - [제품을 구성 하 여 HTTPS 끝점으로 잠재 고객 보내기](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Marketo로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Salesforce로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 제공 된 구성의 유효성을 검사 하려면 **유효성 검사** 링크를 선택 합니다.
1. 대화 상자를 닫으려면 **확인**을 선택 합니다.

## <a name="resell-through-csps"></a>CSP를 통한 재판매

[CSP(클라우드 솔루션 공급자)](https://azure.microsoft.com/offers/ms-azr-0145p/) 프로그램에서 파트너가 사용할 수 있도록 설정하여 제품의 연결 범위를 확장합니다. 모든 BYOL(사용자 라이선스 필요) 플랜은 자동으로 프로그램에 옵트인됩니다. 또한 비 BYOL 플랜을 옵트인하도록 선택할 수도 있습니다.

**만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="next-steps"></a>다음 단계

- [가상 컴퓨터 제품 속성 구성](azure-vm-create-properties.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)