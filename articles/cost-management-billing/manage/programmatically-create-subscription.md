---
title: 프로그래밍 방식으로 Azure 구독 만들기
description: 이 문서는 프로그래밍 방식으로 Azure 구독을 만드는 데 사용할 수 있는 옵션을 이해하는 데 도움이 됩니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379012"
---
# <a name="create-azure-subscriptions-programmatically"></a>프로그래밍 방식으로 Azure 구독 만들기

이 문서는 프로그래밍 방식으로 Azure 구독을 만드는 데 사용할 수 있는 옵션을 이해하는 데 도움이 됩니다.

다양한 REST API를 사용하여 다음과 같은 Azure 계약 유형에 대한 구독을 만들 수 있습니다.

- EA(기업 계약)
- MCA(Microsoft 고객 계약)
- MPA(Microsoft 파트너 계약)

REST API를 사용하여 다른 계약 유형에 대한 추가 구독을 프로그래밍 방식으로 만들 수 없습니다.

구독을 만들기 위한 요구 사항 및 세부 정보는 계약과 API 버전에 따라 다릅니다. 상황에 적용되는 다음 문서를 참조하세요.

최신 API:

- [EA 구독 만들기](programmatically-create-subscription-enterprise-agreement.md)
- [MCA 구독 만들기](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA 구독 만들기](programmatically-create-subscription-microsoft-partner-agreement.md)

또한 이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 구독을 만드는 방법을 보여 줍니다. ARM 템플릿을 사용하면 구독 생성 프로세스를 자동화할 수 있습니다.

[미리 보기 API](programmatically-create-subscription-preview.md)를 사용하는 경우에도 이 API를 사용하여 계속 구독을 만들 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* 구독을 만든 후 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 여러 구독을 관리하는 방법에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
