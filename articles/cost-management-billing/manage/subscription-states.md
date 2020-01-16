---
title: Azure 구독 상태
description: Azure 구독의 다른 상태를 설명합니다.
keywords: Azure 구독 상태
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 741e7cf0869e36b5788d0a883a4e982caf041512
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995937"
---
# <a name="azure-subscription-states"></a>Azure 구독 상태
이 문서에서는 Azure 구독에 포함될 수 있는 다양한 상태를 설명합니다. 이러한 상태는 구독 블레이드 아래에 “상태”로 표시됩니다.

| 구독 상태 | Description |
|-------------| ----------------|
| **활성** | Azure 구독이 활성 상태입니다. 이 구독을 사용하여 새 리소스를 배포하고 기존 리소스를 관리할 수 있습니다.|
| **기한 초과** | Azure 구독에 보류 중인 미해결 결제가 있습니다. 구독은 아직 활성 상태이지만 비용을 지불하지 못하면 구독이 비활성화될 수 있습니다. [Azure 구독의 미지불액을 해결합니다.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **사용 안 함** | Azure 구독이 비활성화되어 더 이상 Azure 리소스를 만들거나 관리하는 데 사용할 수 없습니다. 이 상태에서는 가상 머신의 할당이 취소되고 임시 IP 주소가 해제되며 스토리지는 읽기 전용이 되고 다른 서비스는 비활성 상태가 됩니다. 크레딧이 만료되었거나, 지출 한도에 도달했거나, 연체 청구서가 있거나, 신용 카드 한도가 초과되었거나, 명시적으로 비활성화/취소되었을 수 있기 때문에 구독이 비활성화될 수 있습니다. 구독 유형 및 비활성화 사유에 따라 구독은 영구적으로 삭제된 후 1-90일 간 비활성화된 상태로 유지될 수 있습니다. [비활성화된 Azure 구독을 다시 활성화합니다.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **삭제됨** | Azure 구독이 모든 기본 리소스/데이터와 함께 삭제되었습니다. |
