---
title: Azure Marketplace의 상용 마켓플레이스 청구
description: Marketplace 게시 옵션 및 상업적 marketplace의 거래 청구 모델에 대해 알아봅니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: deb4f3f1bb17bff0b09b2e4f79ceb967d4d7ff59
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744961"
---
# <a name="commercial-marketplace-billing-with-transact-billing-models"></a>청구 모델을 사용 하 여 상업적 marketplace 청구

이 문서에서는 상업적 marketplace에 대 한 상거래 관련 항목을 다룹니다.

- [Marketplace 게시 옵션](#marketplace-publishing-options)
- [거래 일반 개요](#transact-general-overview)
- [거래 청구 모델](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Marketplace 게시 옵션

상용 marketplace는 게시자에 대 한 여러 게시 옵션을 제공 합니다.

### <a name="list-and-trial-publishing-options"></a>목록 및 평가판 게시 옵션

게시자는 판촉 및 사용자 획득을 위해 목록, 평가판 및 BYOL (사용자 라이선스) 게시 옵션을 활용할 수 있습니다. 이러한 옵션을 사용 하는 경우 Microsoft는 게시자의 소프트웨어 라이선스 트랜잭션에 직접 참여 하지 않으며 관련 된 트랜잭션 요금은 없습니다. 게시자는 주문, 이행, 계량, 청구, 송장, 지불 및 수금을 포함하되 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원할 책임이 있습니다. 목록 및 평가판 게시 옵션을 사용할 때 게시자는 고객으로부터 수금된 게시자 소프트웨어 라이선스 요금을 100% 보유합니다.

### <a name="transact-publishing-option"></a>거래 게시 옵션

목록 및 평가판 게시 옵션 외에도, 게시자에 게는 트랜잭션 게시 옵션을 사용할 수 있습니다. 이 옵션은 Microsoft의 전 세계적으로 사용 가능한 상거래 기능을 활용 하며 Microsoft에서 게시자를 대신해 클라우드 marketplace 트랜잭션을 호스트할 수 있도록 합니다.

## <a name="transact-general-overview"></a>거래 일반 개요

Microsoft는 transact-sql 게시 옵션을 사용할 때 타사 소프트웨어를 판매 하 고 일부 제품 유형을 고객의 Azure 구독에 배포할 수 있도록 합니다. 청구 모델 및 제안 유형을 선택 하는 경우 게시자는 인프라 요금 및 게시자의 소프트웨어 라이선스 요금에 대 한 청구를 고려해 야 합니다.

현재는 virtual machines, Azure 응용 프로그램 및 SaaS 응용 프로그램 등의 제안 유형에 대해 Transact-sql 게시 옵션이 지원 됩니다.

![Azure Marketplace의 transact-sql](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>인프라 비용 청구

#### <a name="for-virtual-machines-and-azure-applications"></a>가상 컴퓨터 및 Azure 응용 프로그램의 경우

Virtual machines 및 Azure 응용 프로그램의 경우 Azure 인프라 사용 요금은 고객의 Azure 구독으로 청구 됩니다. 인프라 사용 요금은 고객 청구서에 대 한 소프트웨어 공급자의 라이선스 수수료와 별도로 가격이 책정 되 고 표시 됩니다.

#### <a name="for-saas-apps"></a>SaaS 앱의 경우

SaaS 앱의 경우 게시자는 Azure 인프라 사용 요금 및 소프트웨어 라이선스 요금을 단일 비용 항목으로 처리해야 합니다. 이는 고객에 게 고정 요금으로 표시 됩니다. Azure 인프라 사용량은 관리되며 파트너에게 직접 청구됩니다. 실제 인프라 사용 요금은 고객에게 표시되지 않습니다. 일반적으로 게시자는 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격 책정에 추가하는 것을 선택합니다. 소프트웨어 라이선스 요금은 측정되거나 사용되지 않습니다.

## <a name="transact-billing-models"></a>거래 청구 모델

사용 된 트랜잭션 옵션에 따라 게시자의 소프트웨어 라이선스 요금은 다음과 같이 표시 될 수 있습니다.

- *무료*: 소프트웨어 라이선스에 대 한 요금이 부과 되지 않습니다.
- *BYOL (사용자 라이선스 보유)*: 소프트웨어 라이선스에 대 한 모든 적용 요금은 게시자와 고객 간에 직접 관리 됩니다. Microsoft는 Azure 인프라 사용 요금을 통해서만 전달합니다. (Virtual Machines 및 Azure 애플리케이션의 경우만 해당)
- *종 량*제: 소프트웨어 라이선스 요금은 사용한 Azure 인프라를 기준으로 시간당, 코어 당 (vcpu) 가격 책정 요금으로 제공 됩니다. 이 모델은 가상 머신 및 Azure 응용 프로그램에만 적용 됩니다.
- *구독 가격*: 소프트웨어 라이선스 요금은 월별 또는 연간 요금으로 청구 되는 월별 또는 연간 요금으로 제공 됩니다. 이 모델은 SaaS 앱 및 Azure 응용 프로그램 관리 앱에만 적용 됩니다.
- *무료 소프트웨어 평가판*: 30 일 또는 90 일의 소프트웨어 라이선스에는 요금이 부과 되지 않습니다.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>무료 및 BYOL(사용자 라이선스 필요) 가격 책정

무료 또는 사용자 라이선스 필요 트랜잭션 제품을 게시하는 경우 Microsoft는 해당 소프트웨어 라이선스 요금에 대한 판매 트랜잭션을 촉진하는 역할을 담당하지 않습니다. 목록 및 평가판 게시 옵션과 같이 게시자는 소프트웨어 라이선스 요금을 100% 보유합니다.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>종량제 및 구독(사이트 기준) 가격 책정

종 량 제 또는 구독 트랜잭션 제안을 게시할 때 Microsoft는 소프트웨어 라이선스 구매, 반환 및 요금 청구를 처리 하는 기술 및 서비스를 제공 합니다. 이 시나리오에서 게시자는 Microsoft가 이러한 목적을 위한 에이전트 역할을 하도록 권한을 부여합니다. 게시자는 Microsoft가 판매자, 공급 기업, 배포자 및 라이선스 허가자로서의 역할을 유지하는 동시에 소프트웨어 라이선스 트랜잭션을 촉진하도록 허용합니다.

Microsoft에서는 고객 들이 Microsoft의 상용 Marketplace 및 게시자의 최종 사용자 사용권 계약에 대 한 사용 약관에 테스트할 게시자 소프트웨어를 주문 하 고, 라이선스를 부여 하 고, 사용할 수 있습니다. 제품을 만들 때 게시자는 최종 사용자 사용권 계약을 제공 하거나 [표준 계약](https://docs.microsoft.com/azure/marketplace/standard-contract) 을 선택 해야 합니다.

### <a name="free-software-trials"></a>소프트웨어 평가판

거래 게시 시나리오의 경우 게시자는 30일 또는 90일 동안 소프트웨어 라이선스를 무료로 사용할 수 있습니다. 이 할인 기능은 파트너 솔루션 사용으로 인한 Azure 인프라 사용 비용은 포함하지 않습니다.

### <a name="private-offers"></a>프라이빗 제품

제공 유형 및 청구 모델을 사용 하 여 제품을 수익 창출 하는 것 외에도, 게시자는 개인 제품을 제공 하 고, 협의 된 특정 가격 책정 또는 사용자 지정 구성을 완료할 수 있습니다. 프라이빗 제품은 3가지 거래 게시 옵션 모두에서 지원됩니다.

이 옵션을 사용 하면 공개적으로 제공 되는 제품 보다 더 높거나 낮은 가격 책정을 사용할 수 있습니다. 비공개 제품을 사용 하 여 제안의 프리미엄을 추가 하거나 추가할 수 있습니다. 프라이빗 제품은 제품 수준에서 Azure 구독을 허용 목록에 추가하면 둘 이상의 고객이 사용할 수 있습니다.

### <a name="examples"></a>예

#### <a name="pay-as-you-go"></a>종량제

* 종량제 옵션을 사용하면 다음과 같은 비용 구조가 나타납니다.

|라이선스 비용  | 시간당 $1.00  |
|---------|---------|
|Azure 사용량 비용(D1/1개 코어)    |   시간당 $0.14     |
|*Microsoft에서 고객에게 청구하는 요금*    |  *시간당 $1.14*       |

* 이 시나리오에서 Microsoft는 게시된 VM 이미지 사용에 대해 시간당 $1.14를 청구합니다.

|Microsoft 청구  | 시간당 $1.14  |
|---------|---------|
|Microsoft는 라이선스 비용의 80%를 지불합니다.|   시간당 $0.80     |
|Microsoft는 라이선스 비용의 20%를 유지합니다.  |  시간당 $0.20       |
|Microsoft는 Azure 사용 비용의 100%를 유지합니다. | 시간당 $0.14 |

### <a name="bring-your-own-license-byol"></a>BYOL (사용자 라이선스 사용)

* BYOL 옵션을 사용하면 다음과 같은 비용 구조가 나타납니다.

|라이선스 비용  | 사용자가 라이선스 요금을 협상하고 청구합니다.  |
|---------|---------|
|Azure 사용량 비용(D1/1개 코어)    |   시간당 $0.14     |
|*Microsoft에서 고객에게 청구하는 요금*    |  *시간당 $0.14*       |

* 이 시나리오에서 Microsoft는 게시된 VM 이미지 사용에 대해 시간당 $0.14를 청구합니다.

|Microsoft 청구  | 시간당 $0.14  |
|---------|---------|
|Microsoft는 Azure 사용량 비용을 유지합니다.    |   시간당 $0.14     |
|Microsoft은 라이선스 비용의 0%를 유지합니다.   |  시간당 $0.00       |

### <a name="saas-app-subscription"></a>SaaS 앱 구독

이 옵션은 Microsoft를 통해 판매 하도록 구성 해야 하며, 월별 또는 연간 기준으로 가격이 나 사용자 단위로 요금이 책정 될 수 있습니다.

• SaaS 제품에 대해 Microsoft에서 판매 옵션을 사용 하도록 설정 하는 경우 다음과 같은 비용 구조가 있습니다.

|라이선스 비용       | 매월 $100.00  |
|--------------|---------|
|Azure 사용량 비용(D1/1개 코어)    | 고객이 아닌 게시자에게 직접 청구됩니다. |
|*Microsoft에서 고객에게 청구하는 요금*    |  *월간 $100.00(참고: 게시자는 발생하거나 통과된 인프라 비용을 라이선스 요금으로 계산해야 함)*  |

* 이 시나리오에서 Microsoft는 소프트웨어 라이선스에 대해 $100.00를 청구하고, 게시자에게 $80.00를 지급합니다.
* 줄어든 Marketplace 서비스 요금으로 한정 된 파트너는 2019 년 5 월 2020 일까 야 년 5 월부터 SaaS 제품에 대 한 트랜잭션 요금이 감소 하는 것을 볼 수 있습니다. 이 시나리오에서 Microsoft는 귀하의 소프트웨어 라이선스에 대해 $100.00를 청구 하 고 게시자에 게 $90.00을 지불 합니다.

|Microsoft 청구  | 매월 $100.00  |
|---------|---------|
|Microsoft는 라이선스 비용의 80%를 지불합니다. <br> \*Microsoft는 정규화 된 SaaS 앱에 대 한 라이선스 비용의 90%를 지불 합니다.   |   매월 $80.00 <br> \*$90.00/월    |
|Microsoft는 라이선스 비용의 20%를 유지합니다. <br> \*Microsoft는 정규화 된 SaaS 앱에 대 한 라이선스 비용의 10%를 유지 합니다.  |  매월 $20.00 <br> \*$10.00     |

**Marketplace 서비스 요금 감소:** 상업적 Marketplace에 게시 하는 특정 SaaS 제품의 경우 Microsoft는 Microsoft 게시자 계약에 설명 된 대로 20%에서 10%까지 Marketplace 서비스 요금을 절감 합니다.  제품을 한정 하려면 Microsoft에서 하나 이상의 제품을 IP 공동 판매 준비 또는 IP 공동 판매 우선 순위로 지정 해야 합니다. 이 월에 대해이 감소 하는 Marketplace 서비스 요금을 받으려면 이전 달력 월이 끝날 때까지 최소 5 영업일 영업일 이전에 자격을 충족 해야 합니다. Marketplace 서비스 요금은 Vm, 관리 되는 앱 또는 상용 Marketplace를 통해 제공 되는 기타 제품에는 적용 되지 않습니다.  이 절감 된 Marketplace 서비스 요금은 Microsoft에서 5 월 1 일부 터 2019 년 6 월 30 2020 일 사이에 수집 된 라이선스 요금으로 정규화 된 제안에 제공 될 예정입니다.  이 시간 후에 Marketplace 서비스 요금은 일반적인 금액으로 반환 됩니다.
