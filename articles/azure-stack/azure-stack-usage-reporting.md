---
title: Azure에 Azure Stack 사용 현황 데이터를 보고 | Microsoft Docs
description: Azure Stack의 보고 기능 사용 현황 데이터를 설정 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 1cf0d0f6676da78d9b8b159abce2c83c8a850c15
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252169"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Azure에 Azure Stack 사용 현황 데이터를 보고 합니다.

소비량 데이터 라고도 사용 현황 데이터를 사용 하는 리소스의 양을 나타냅니다.

Azure Stack 다중 노드 시스템 사용량 기반 청구 모델을 사용 하는 청구를 위해 Azure에 사용 현황 데이터를 보고 해야 합니다. Azure Stack 운영자가 Azure Stack 인스턴스 azure 사용 데이터 보고를 구성 해야 합니다.

> [!IMPORTANT]
> 모든 워크 로드 [테 넌 트 구독에 배포 해야](#are-users-charged-for-the-infrastructure-vms) Azure Stack의 사용 조건을 준수 하도록 합니다.

사용 데이터 보고-수-종 모델에서 사용 허가 받은 Azure Stack 다중 노드 사용자에 대 한 필요 합니다. 용량 모델에서 사용 허가 받은 고객에 대 한 선택 사항입니다 (참조를 [구입 방법 페이지](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)합니다. Azure Stack Development Kit 사용자에 대 한 Azure Stack 운영자는 사용 현황 데이터를 보고 하 고 기능을 테스트 수 있습니다. 그러나 그로 인해 발생 하는 사용량에 대해 사용자 요금이 청구 되지 않습니다.

![결제 흐름](media/azure-stack-usage-reporting/billing-flow.png)

사용 현황 데이터는 Azure Stack에서 Azure 연결을 통해 Azure에 전송 됩니다. Azure에서 상거래 시스템 사용 현황 데이터를 처리 하 고 청구서를 생성 합니다. Azure 구독 소유자 보기를에서 다운로드할 청구서를 생성 후 합니다 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)합니다. Azure Stack은 사용이 허가 하는 방법에 대 한 자세한 참조를 [패키징 및 가격 문서는 Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847)합니다.

## <a name="set-up-usage-data-reporting"></a>사용 데이터 보고 설정

사용 데이터 보고를 설정 하려면 [Azure를 사용 하 여 Azure Stack 인스턴스 등록](azure-stack-register.md)합니다. 등록 프로세스의 일부로 Azure에 연결 하는 Azure Stack을 사용 현황 데이터를 전송 하는 Azure Stack의 Azure 브리지 구성 요소 구성 됩니다. 다음 사용 현황 데이터는 Azure Stack에서 Azure로 전송 됩니다.

- **측정기 ID** – 사용 된 리소스에 대 한 고유 ID입니다.
- **수량** – 리소스 사용량입니다.
- **위치** -현재 Azure Stack 리소스를 배포할 위치입니다.
- **리소스 URI** – 사용량이 보고 되는 리소스의 URI를 정규화 합니다.
- **구독 ID** -로컬 (Azure Stack) 구독이 있는 Azure Stack 사용자의 구독 ID입니다.
- **시간** – 사용 현황 데이터의 시작 및 종료 시간입니다. 지연은 일부 시간 사이 및 사용 현황 데이터는 상거래에 보고 되 면 Azure Stack에서 사용 된 이러한 리소스는 합니다. 24 시간 마다 azure Stack 집계 사용 현황 데이터 및 Azure에서 commerce 파이프라인으로 보고 사용 현황 데이터에는 다른 몇 시간이 걸립니다. 따라서 자정 직전 발생 하는 사용량에에서 표시 될 Azure 다음 날입니다.

## <a name="generate-usage-data-reporting"></a>사용 데이터 보고를 생성 합니다.

- 사용량 데이터 보고를 테스트 하려면 Azure Stack에서 몇 가지 리소스를 만듭니다. 예를 들어, 만들 수는 [저장소 계정](azure-stack-provision-storage-account.md)를 [Windows Server VM](azure-stack-provision-vm.md) 및 코어 사용량 보고 되는 방법을 참조 하는 기본 및 표준 Sku를 사용 하 여 Linux VM입니다. 다양 한 유형의 리소스에 대 한 사용 현황 데이터는 서로 다른 미터에서 보고 됩니다.

- 몇 시간 동안 실행 하 여 리소스를 그대로 둡니다. 사용 정보는 약 1 시간 마다 수집 됩니다. 를 수집한 후이 데이터는 Azure에 전송 되 고 Azure 상거래 시스템에서 처리 합니다. 이 프로세스는 몇 시간 걸릴 수 있습니다.

## <a name="view-usage---csp-subscriptions"></a>사용 현황 보기-CSP 구독

CSP 구독을 사용 하 여 Azure Stack에 등록 한 경우 동일한 방식으로 Azure 사용량을 볼 수 있는 사용 현황 및 요금을 볼 수 있습니다. Azure Stack 사용 및 조정 파일을 통해 사용 가능한 청구서에 포함 됩니다는 [파트너 센터](https://partnercenter.microsoft.com/partner/home)합니다. 조정 파일은 매월 업데이트 됩니다. 최신 Azure Stack 사용량 정보에 액세스 해야 할 경우 파트너 센터 Api를 사용할 수 있습니다.

![파트너 센터](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage--enterprise-agreement-subscriptions"></a>사용 현황 보기-엔터프라이즈 계약 구독

사용량 및 요금에서 기업 계약 구독을 사용 하 여 Azure Stack에 등록 하는 경우 볼 수 있습니다 합니다 [EA 포털](https://ea.azure.com/)합니다. Azure Stack 사용이이 포털에서 보고서 섹션에서 Azure 사용과 함께 고급 다운로드에 포함 됩니다. 

## <a name="view-usage--other-subscriptions"></a>사용 현황 보기-다른 구독

Azure Stack에 등록 한 경우 사용 하 여 다른 구독 형식 예를 들어, 종 량 제 구독을 Azure 계정 센터에서 사용량 및 요금을 볼 수 있습니다. 에 로그인 합니다 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions) Azure 관리자 계정 및 Azure Stack 등록 하는 데는 Azure 구독을 선택 합니다. Azure Stack 사용 현황 데이터는 다음 이미지에 나와 있는 것 처럼 각 사용 되는 리소스에 대 한 요금이 청구 금액을 볼 수 있습니다.

![결제 흐름](media/azure-stack-usage-reporting/pricing-details.png)

Azure Stack 개발 키트에 대 한 Azure Stack 리소스 요금이 부과 되지 않습니다, 있도록 가격이 $0.00으로 표시 됩니다.

## <a name="which-azure-stack-deployments-are-charged"></a>Azure Stack 배포 비용은 청구 되나요?

리소스 사용량은 Azure Stack 개발 키트에 대 한 무료입니다. Azure Stack 다중 노드 시스템, 워크 로드 Vm, 저장소 서비스 및 앱 서비스 요금이 청구 됩니다.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>사용자는 인프라 Vm에 대 한 비용은 청구 되나요?

 아니요. 일부 Vm의 Azure Stack 리소스 공급자에 대 한 사용 현황 데이터를 Azure로 보고 됩니다 있지만 이러한 vm 또는 Azure Stack 인프라를 사용 하도록 설정 하려면 배포 중에 만든 Vm에 대 한 청구 되지 않습니다.  

사용자만 테 넌 트 구독에서 실행 되는 Vm에 대 한 요금이 청구 됩니다. Azure Stack의 사용 조건을 준수 하기 위해 테 넌 트 구독 아래에 있는 모든 워크 로드를 배포 되어야 합니다.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Azure Stack에서 사용 하려면 Windows Server 라이선스가 있음을, 어떻게 하나요?

기존 라이선스를 사용 하 여 방지 사용량 미터를 생성 합니다. 기존 Windows Server 라이선스의 "Azure Stack을 사용 하 여 기존 소프트웨어를 사용" 섹션에 설명 된 대로 Azure Stack에서 사용할 수 있습니다 합니다 [Azure Stack 라이선스 가이드](https://go.microsoft.com/fwlink/?LinkId=851536)합니다. 에 설명 된 대로 고객에 게 Windows Server 가상 컴퓨터를 배포 해야 합니다 [Windows Server 라이선스에 대 한 Hybrid benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) 기존 라이선스를 사용 하려면 문서.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>구독을 사용 하는 리소스 요금이 부과 됩니다?

될 때 제공 되는 구독이 [Azure를 사용 하 여 Azure Stack 등록](azure-stack-register.md) 요금이 청구 됩니다.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>어떤 유형의 구독 사용량 데이터 보고가 지원 되나요?

Azure Stack 다중 노드, EA (기업 계약) 및 CSP 구독 지원 됩니다. Azure Stack 개발 키트에 대 한 MSDN, CSP, 종 량 제 및 EA (기업 계약) 구독 사용량 데이터 보고를 지원합니다.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>사용량 데이터 보고 sovereign cloud에서 작업 합니까?

Azure Stack 개발 키트에서 사용 데이터 보고에 전역 Azure 시스템에서 만들어진 구독에는 필요 합니다. 소 버린 클라우드 (Azure Government, Azure 독일 및 Azure 중국 클라우드) 중 하나에서 만들어진 구독 사용량 데이터 보고를 지원 하지 되므로 Azure를 사용 하 여 등록할 수 없습니다.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Azure Stack에서 보고 된 사용량 하지 Azure 계정 센터에서 생성 된 보고서를 일치 하는 이유

Azure Stack Api 사용 하 여 보고 사용 현황 데이터 및 Azure 계정 센터에서 보고 된 사용 현황 데이터 사이 지연이 항상입니다. 이 지연 시간은 Azure 상거래 Azure Stack에서 사용 현황 데이터를 업로드 하는 데 필요한 시간입니다. 이 지연으로 인해 자정 직전 발생 하는 사용량에에서 표시 될 Azure 다음 날입니다. 사용 하는 경우는 [Azure Stack 사용량 Api](azure-stack-provider-resource-api.md), Azure 청구 포털에서 보고 된 사용량에 결과 비교 하 고, 차이 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [공급자 사용량 API](azure-stack-provider-resource-api.md)  
* [테넌트 사용량 API](azure-stack-tenant-resource-usage-api.md)
* [사용 FAQ](azure-stack-usage-related-faq.md)
* [사용량 관리 및 클라우드 서비스 공급자로 대금 청구](azure-stack-add-manage-billing-as-a-csp.md)
