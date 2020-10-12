---
title: Azure 센티널의 여러 테 넌 트를 관리 되는 보안 서비스 공급자로 관리 | Microsoft Docs
description: Azure Lighthouse를 사용 하 여 Azure 센티널에서 여러 테 넌 트를 관리 되는 MSSP (보안 서비스 공급자)로 등록 하 고 관리 하는 방법입니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578152"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Azure 센티널에서 여러 테 넌 트를 MSSP로 관리

사용자가 관리 되는 MSSP (security service provider) 인 경우 [Azure Lighthouse](../lighthouse/overview.md) 를 사용 하 여 고객에 게 SOC (security operations center) 서비스를 제공 하는 경우 고객의 테 넌 트에 연결 하지 않고도 자신의 azure 테 넌 트에서 직접 고객의 azure 센티널 리소스를 관리할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Lighthouse 등록](../lighthouse/how-to/onboard-customer.md)

- 이 작업이 제대로 작동 하려면 테 넌 트 (MSSP 테 넌 트)에 하나 이상의 구독에 등록 된 Azure 센티널 리소스 공급자가 있어야 합니다. 또한 각 고객의 테 넌 트에는 리소스 공급자를 등록 해야 합니다. 테 넌 트에 Azure 센티널을 등록 하 고 고객에 게 사용자를 등록 한 경우 시작할 준비가 된 것입니다. 등록을 확인 하려면 다음 단계를 수행 합니다.

    1. Azure Portal에서 **구독** 을 선택한 다음 메뉴에서 관련 구독을 선택 합니다.

    1. 구독 화면의 탐색 메뉴에 있는 **설정**에서 **리소스 공급자**를 선택 합니다.

    1. ** *구독 이름* 에서 | 리소스 공급자** 화면에서 *OperationalInsights* 및 *microsoft securityinsights*를 검색 하 고 선택 하 고 **상태** 열을 확인 합니다. 공급자의 상태가 *Notregistered*인 경우 **등록**을 선택 합니다.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="리소스 공급자 확인":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>관리 되는 테 넌 트에서 Azure 센티널에 액세스 하는 방법

1. **디렉터리 + 구독**에서 위임 된 디렉터리 (디렉터리 = 테 넌 트)를 선택 하 고 고객의 Azure 센티널 작업 영역이 있는 구독을 선택 합니다.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="리소스 공급자 확인":::

1. Azure 센티널를 엽니다. 선택한 구독의 모든 작업 영역을 볼 수 있으며, 사용자는 테 넌 트의 모든 작업 영역 처럼 원활 하 게 작업할 수 있습니다.

> [!NOTE]
> 관리 되는 작업 영역 내에서 Azure 센티널의 커넥터를 배포할 수 없습니다. 커넥터를 배포 하려면 커넥터를 배포 하려는 테 넌 트에 직접 로그인 하 고 필요한 권한으로 해당 테 넌 트에 인증 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 Azure 센티널 테 넌 트를 원활 하 게 관리 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

