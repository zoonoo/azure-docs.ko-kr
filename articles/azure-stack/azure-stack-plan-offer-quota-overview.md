---
title: Azure Stack 계획, 제안, 할당량 및 구독 개요 | Microsoft Docs
description: 클라우드 운영자로 서 Azure Stack 계획, 제안, 할당량 및 구독 이해 하려고 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/12/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 61afc8a736025763f950307fdbb78bb21577b22f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244208"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>계획, 제안, 할당량 및 구독 개요

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

[Azure Stack](azure-stack-poc.md) 배달 하는 광범위 한 있습니다 서비스, virtual machines 처럼 SQL Server 데이터베이스, SharePoint, Exchange, 및에 [Azure Marketplace 항목](azure-stack-marketplace-azure-items.md)합니다. Azure Stack 운영자를 구성 하 고 Azure Stack에서 계획, 제품 및 할당량을 사용 하 여 이러한 서비스를 제공 합니다.

제품에는 하나 이상의 계획을 포함 하 고 각 계획 하나 이상의 서비스를 포함 합니다. 계획을 만들고 다른 제품으로 결합 하 여 다음을 관리할 수 있습니다.

- 사용자가 액세스할 수 있는 서비스 및 리소스.
- 사용자가 사용할 수 있는 리소스의 양입니다.
- 지역을은 리소스에 액세스할 수 있어야 합니다.

서비스를 제공 하는 경우 이러한 수준 높은 단계를 수행 합니다.

1. 사용자에 게 제공 하려는 서비스를 추가 합니다.
2. 에 하나 이상의 서비스 계획을 만듭니다. 계획을 만들 때에 선택 하거나 계획의 각 서비스의 리소스 한도 정의 하는 할당량을 만듭니다.
3. 하나 이상의 계획을 포함 하는 제품을 만듭니다. 기본 요금제 및 선택적 추가 기능 계획 제품 포함할 수 있습니다.

제품을 만든 후에 제공 된 서비스 및 리소스에 액세스 하려면 사용자가 구독할 수 있습니다. 사용자가 원하는 만큼 많은 제품을 구독할 수 있습니다. 다음 그림에는 두 가지 제품을 구독 하는 사용자의 간단한 예를 보여 줍니다. 각 제품 계획 또는 두 있고 각 계획 한 액세스 권한을 제공 서비스입니다.

![제품 및 계획을 사용 하 여 테 넌 트 구독](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>요금제

계획은 하나 이상의 서비스가 모인 그룹입니다. Azure Stack 운영자 있습니다 [계획을 만들고](azure-stack-create-plan.md) 사용자에 게 제공 합니다. 차례로 사용자 계획 및 포함 하는 서비스를 사용 하 여 제품을 구독 합니다. 계획을 만들면, 할당량 설정, 기본 계획을 정의 및 선택적 추가 기능 계획을 포함 하는 것이 좋습니다에 있는지 확인 합니다.

### <a name="quotas"></a>할당량

을 클라우드 용량을 관리할 수 있도록 하려면 사용할 수 있습니다 미리 구성 된 *할당량*, 계획의 각 서비스에 대 한 새 할당량을 만들거나 합니다. 할당량은 사용자 구독 프로 비전 하거나 사용할 수 있는 상위 리소스 한도 정의 합니다. 예를 들어 할당량 사용자 최대 5 개의 Vm (가상 머신)을 만들 수 있습니다.

지역에서 할당량을 구성할 수 있습니다. 예를 들어 지역 A에 대 한 계산 서비스를 제공 하는 계획을 두 Vm의 할당량이 없습니다.

>[!NOTE]
>Azure Stack Development Kit를 하나의 지역에서 (이라는 *로컬*)를 사용할 수 있습니다.

에 대해 자세히 알아보세요 [Azure Stack에서 할당량 유형은](azure-stack-quota-types.md)합니다.

### <a name="base-plan"></a>기본 계획

제품을 만들면, 서비스 관리자는 기본 계획을 포함할 수 있습니다. 이러한 기본 요금제는 사용자가 해당 제품을 구독 하는 경우 기본적으로 포함 됩니다. 사용자가 구독 하는 경우 (해당 할당량)와 해당 기본 계획에 지정 된 모든 리소스 공급자에 액세스할을 수 있습니다.

### <a name="add-on-plans"></a>추가 요금제

추가 기능 계획은 제품에 추가 하는 선택적 계획입니다. 추가 기능 계획은 구독에서 기본적으로 포함되지 않습니다. 추가 기능 계획은 추가 계획 (할당량) 구독자가 구독에 추가할 수 있는 제품에서 사용할 수 있습니다. 예를 들어, 고객에 게 서비스를 채택 하려는 평가판에 대 한 제한 된 리소스를 사용 하 여 기본 계획 하 고 더 많은 리소스를 사용 하 여 추가 기능 계획을 제공할 수 있습니다.

## <a name="offers"></a>제품

혜택은 그룹에 사용자가 등록할 수 있도록 만들어야 하는 하나 이상의 계획입니다. 예를 들어 제품 a 계산 서비스의 집합을 제공 하는 계획 A와 저장소 및 네트워크 서비스의 집합을 제공 하는 계획 B를 포함할 수 있습니다.

경우 있습니다 [제품을 만드는](azure-stack-create-offer.md), 하나 이상의 기본 계획을 포함 해야 하지만 사용자가 해당 구독에 추가할 수 있는 추가 기능 계획을 만들 수도 있습니다.

## <a name="subscriptions"></a>구독

구독은은 사용자가 제품을 액세스 하는 방법입니다. 서비스 공급자에 대 한 Azure Stack 운영자 인 경우 사용자 (테 넌 트) 제품에 가입 하 여 서비스를 구입 합니다. 조직에서 Azure Stack 운영자 인 경우 (직원) 사용자가 지불 하지 않고 제공 서비스를 구독할 수 있습니다.

제품을 사용 하 여 사용자의 각 조합에 고유 구독입니다. 사용자는 여러 제품에 대 한 구독 수 있지만 각 구독은 하나의 제품에만 적용 됩니다. 계획, 제품 및 할당량 고유 구독에만 적용 – 구독 간에 공유할 수는 없습니다. 사용자가 만든 각 리소스는 하나의 구독과 연결 합니다.

### <a name="default-provider-subscription"></a>기본 공급자 구독

Azure Stack 개발 키트를 배포할 때 기본 공급자 구독을 자동으로 만들어집니다. 이 구독, Azure Stack을 관리 하 고, 추가 리소스 공급자를 배포 하 고, 계획을 만들고 사용자에 대 한 제품을 사용할 수 있습니다. 보안 및 라이선스에 대 한 해당 해서는 안 고객 워크 로드 및 응용 프로그램을 실행 합니다.

## <a name="next-steps"></a>다음 단계

계획 및 제품에 대 한 자세한 내용은 참조 하세요. [계획을 만들려면](azure-stack-create-plan.md)합니다.
