---
title: Azure 전면 도어 표준/프리미엄 (미리 보기)에서 개인 링크를 사용 하 여 원본 보호
description: 이 페이지에서는 개인 링크를 사용 하 여 원본에 연결을 보호 하는 방법에 대 한 정보를 제공 합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: 6a1ec6e0b8862c6ad2b884b019e908e7d2a59a1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715516"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)에서 개인 링크를 사용 하 여 원본 보호

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [Azure 전방 도어 문서](../front-door-overview.md)를 봅니다.

## <a name="overview"></a>개요

[Azure 개인 링크](../../private-link/private-link-overview.md) 를 사용 하면 가상 네트워크의 개인 끝점을 통해 Azure PaaS 서비스 및 azure 호스 티 드 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Front 도어 Premium SKU는 개인 링크 서비스를 사용 하 여 웹 앱 및 저장소 계정 뒤의 원본에 연결 하 여 원본에 액세스할 수 있는 공개적으로 필요를 제거할 수 있습니다.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="전면 도어 전용 끝점 아키텍처":::

Azure Front 도어 Premium 구성에서 원본에 대 한 개인 링크를 사용 하도록 설정 하는 경우 Front 도어가 프런트 도어의 지역 개인 네트워크에서 사용자를 대신 하 여 개인 끝점을 만듭니다. 이 끝점은 Azure Front 도어를 통해 관리 됩니다. 원본에서 승인 메시지에 대 한 Azure Front 도어 개인 끝점 요청을 받게 됩니다. 요청을 승인한 후에는 개인 IP 주소가 Front 도어의 가상 네트워크에서 할당 되 고, Azure Front 문과 원본 간의 트래픽이 Azure 네트워크 백본으로 설정 된 개인 링크를 통과 합니다. 이제 Azure Front 도어에서 들어오는 트래픽에 대 한 들어오는 트래픽이 보호 됩니다.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="개인 끝점 사용":::

> [!NOTE]
> 개인 링크 원본을 사용 하도록 설정 하 고 개인 끝점을 승인한 후에는 연결을 설정 하는 데 몇 분 정도 걸립니다. 이 시간 동안 원본에 대 한 요청은 전방 도어 오류 메시지를 받게 됩니다. 연결이 설정 되 면 오류 메시지가 사라집니다.

## <a name="limitations"></a>제한 사항

Azure 전면 도어 개인 끝점은 공개 미리 보기 중 미국 동부, 미국 서 부, 미국 서 부 및 미국 동부 지역에서 사용할 수 있습니다.

가장 적절 한 대기 시간을 위해 프런트 도어 개인 링크 끝점을 사용 하도록 선택할 때 항상 원본에 가장 가까운 Azure 지역을 선택 해야 합니다.

Azure 전면 도어 개인 끝점은 플랫폼 및 Azure Front 도어 구독에서 관리 됩니다. Azure 전면 도어는 프런트 도어 프로필을 만드는 데 사용 되는 것과 동일한 고객 구독에 대 한 개인 링크 연결을 허용 합니다.

## <a name="next-steps"></a>다음 단계

* 개인 링크 서비스를 통해 Azure Front 도어 Premium을 웹 앱에 연결 하려면 [개인 끝점을 사용 하 여 웹 앱에 연결](../../private-link/tutorial-private-endpoint-webapp-portal.md)을 참조 하세요.
* 개인 링크 서비스를 통해 Azure Front 도어 Premium을 저장소 계정에 연결 하려면 [개인 끝점을 사용 하 여 저장소 계정에 연결](../../private-link/tutorial-private-endpoint-storage-portal.md)을 참조 하세요.
