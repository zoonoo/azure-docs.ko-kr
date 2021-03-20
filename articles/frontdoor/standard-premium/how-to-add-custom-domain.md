---
title: Azure Front 도어 Standard/Premium SKU 구성에 사용자 지정 도메인을 추가 하는 방법
description: 이 자습서에서는 사용자 지정 도메인을 Azure Front 도어 Standard/Premium SKU에 등록 하는 방법에 대해 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 36cb5720e409c86fcb4bc1a97863e5d3523cd3bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588752"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Front 도어 Standard/Premium SKU (미리 보기)에서 사용자 지정 도메인 만들기

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

응용 프로그램 배달에 Azure Front 도어 Standard/Premium을 사용 하는 경우 사용자 지정 도메인은 최종 사용자 요청에서 고유한 도메인 이름을 표시 하려는 경우에 필요 합니다. 볼 수 있는 도메인 이름이 있다면 고객에게 편리하고 브랜딩 목적상 유용합니다.

Azure Front 도어 Standard/Premium 프로필을 만든 후 기본 프런트 엔드 호스트는 azurefd.net의 하위 도메인을 갖게 됩니다. 이 하위 도메인은 Azure Front 도어 Standard/Premium에서 기본적으로 백 엔드에서 콘텐츠를 배달 하는 경우 URL에 포함 됩니다. 예: `https://contoso-frontend.azurefd.net/activeusers.htm` 사용자 편의를 위해 Azure Front Door는 사용자 지정 도메인을 기본 호스트에 연결하는 옵션을 제공합니다. 이 옵션을 사용 하 여 Azure 전면 도어 표준/프리미엄 소유 도메인 이름 대신 URL의 사용자 지정 도메인을 사용 하 여 콘텐츠를 배달 합니다. 예: https://www.contoso.com/photo.png

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
* 이 자습서의 단계를 완료하려면 먼저 Front Door를 만들어야 합니다. 자세한 내용은 [빠른 시작: 전면 도어 표준/프리미엄 만들기](create-front-door-portal.md)를 참조 하세요.

* 사용자 지정 도메인이 아직 없는 경우 도메인 공급자를 사용 하 여 먼저 구매 해야 합니다. 예를 들어 [사용자 지정 도메인 이름 구매](../../app-service/manage-custom-dns-buy-domain.md)를 참조하세요.

* Azure를 사용 하 여 [dns 도메인](../../dns/dns-overview.md)을 호스트 하는 경우 도메인 공급자의 dns (domain name system)를 Azure DNS에 위임 해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](../../dns/dns-delegate-domain-azure-dns.md)을 참조하세요. 그렇지 않고 도메인 공급자를 사용 하 여 DNS 도메인을 처리 하는 경우 DNS TXT 레코드를 입력 하 여 도메인을 수동으로 확인 해야 합니다.

## <a name="add-a-new-custom-domain"></a>새 사용자 지정 도메인 추가

> [!NOTE]
> 공개 미리 보기 상태에서 Apex 도메인을 만드는 데 Azure DNS를 사용 하는 것은 Azure Front 도어 Standard/Premium에서 지원 되지 않습니다. APEX 도메인을 Azure Front 도어 Standard/Premium에 사용할 수 있도록 하는 CNAME 평면화 또는 DNS 추적을 지 원하는 다른 DNS 공급자가 있습니다.

포털의 도메인 섹션에서 사용자 지정 도메인을 관리 합니다. 끝점에 연결 하기 전에 사용자 지정 도메인을 만들고 유효성을 검사할 수 있습니다. 사용자 지정 도메인 및 하위 도메인은 한 번에 하나의 끝점에만 연결할 수 있습니다. 그러나 다른 프런트 문에는 동일한 사용자 지정 도메인의 다른 하위 도메인을 사용할 수 있습니다. 다른 하위 도메인의 사용자 지정 도메인을 동일한 Front 도어 끝점에 매핑할 수도 있습니다.

1. Azure Front 도어 프로필에 대 한 설정 *에서 도메인을 선택한 다음* **도메인 추가** 단추를 클릭 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="도메인 방문 페이지에서 도메인 추가 단추의 스크린샷":::

1. 사용자 지정 도메인에 대 한 정보를 입력할 수 있는 **도메인 추가** 페이지가 나타납니다. Azure에서 관리 되는 DNS를 선택할 수 있습니다. 권장 되는 경우 또는 사용자 고유의 DNS 공급자를 사용 하도록 선택할 수 있습니다. Azure 관리 DNS를 선택 하는 경우 기존 DNS 영역을 선택 하 고 사용자 지정 하위 도메인을 선택 하거나 새 하위 도메인을 만듭니다. 다른 DNS 공급자를 사용 하는 경우 사용자 지정 도메인 이름을 수동으로 입력 합니다. **추가** 를 선택 하 여 사용자 지정 도메인을 추가 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="도메인 추가 페이지의 스크린샷":::

    새 사용자 지정 도메인은 **제출** 의 유효성 검사 상태를 사용 하 여 만들어집니다.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="도메인 유효성 검사 상태 제출의 스크린샷":::

    유효성 검사 상태가 **보류 중** 으로 변경 될 때까지 기다립니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="보류 중인 도메인 유효성 검사 상태의 스크린샷.":::

1. **보류 중인** 유효성 검사 상태를 선택 합니다. 사용자 지정 도메인의 유효성을 검사 하는 데 필요한 DNS TXT 레코드 정보가 포함 된 새 페이지가 표시 됩니다. TXT 레코드는 형식입니다 `_dnsauth.<your_subdomain>` . Azure DNS 기반 영역을 사용 하는 경우 **추가** 단추를 선택 하면 표시 된 레코드 값을 포함 하는 새 TXT 레코드가 Azure DNS 영역에 생성 됩니다. 다른 DNS 공급자를 사용 하는 경우 `dnsauth.<your_subdomain>` 페이지에 표시 된 것 처럼 레코드 값을 사용 하 여 이름에 대 한 새 TXT 레코드를 수동으로 만듭니다.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="사용자 지정 도메인 유효성 검사 페이지의 스크린샷":::

1. 새로 고침 상태를 선택 합니다. DNS TXT 레코드를 사용 하 여 도메인의 유효성이 검사 되 면 유효성 검사 상태가 **확인** 됨으로 변경 됩니다. 이 작업의 유효성을 검사 하는 데 몇 분 정도 걸릴 수 있습니다.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="확인 된 사용자 지정 도메인의 스크린샷":::

1. 사용자 지정 도메인 목록 방문 페이지로 돌아가려면 페이지를 닫습니다. 사용자 지정 도메인의 프로 비전 상태가 **프로 비전** 됨으로 변경 되 고 유효성 검사 상태가 **승인 됨** 으로 변경 되어야 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="프로 비전 되 고 승인 된 상태의 스크린샷.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>사용자 지정 도메인을 프런트 도어 끝점에 연결

사용자 지정 도메인의 유효성을 검사 한 후 Azure Front 도어 표준/프리미엄 끝점에 추가할 수 있습니다.

1. 사용자 지정 도메인의 유효성이 검사 되 면 기존 Azure Front 도어 표준/프리미엄 끝점 및 경로에 연결할 수 있습니다. 끝점 **연결** 링크를 선택 하 여 **끝점 및 경로 연결** 페이지를 엽니다. 연결 하려는 끝점과 경로를 선택 합니다. 그런 다음 **연결** 을 선택 합니다. 연결 작업이 완료 되 면 페이지를 닫습니다.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="끝점 및 경로 연결 페이지의 스크린샷":::

    사용자 지정 도메인이 현재 연결 되어 있는 끝점을 반영 하도록 끝점 연결 상태가 변경 되어야 합니다. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="끝점 연결 링크의 스크린샷":::

1. DNS 상태 링크를 선택 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="DNS 상태 링크의 스크린샷":::

1. **Cname 레코드 추가 또는 업데이트** 페이지가 표시 되 고 트래픽 흐름을 시작 하기 전에 제공 해야 하는 cname 레코드 정보가 표시 됩니다. 호스트 된 영역 Azure DNS 사용 하는 경우 페이지에서 **추가** 단추를 선택 하 여 CNAME 레코드를 만들 수 있습니다. 다른 DNS 공급자를 사용 하는 경우 페이지에 표시 된 대로 CNAME 레코드 이름과 값을 수동으로 입력 해야 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="CNAME 레코드 추가 또는 업데이트의 스크린샷":::

1. CNAME 레코드가 만들어지고 사용자 지정 도메인이 Azure 프런트 도어 끝점에 연결 되 면 트래픽 흐름이 시작 됩니다.

    > [!NOTE]
    > HTTPS를 사용 하는 경우 모든에 지 위치로 전파가 수행 되기 때문에 인증서 프로 비전 및 전파에 몇 분 정도 걸릴 수 있습니다. 

## <a name="verify-the-custom-domain"></a>사용자 지정 도메인 확인

사용자 지정 도메인의 유효성을 검사 하 고 연결한 후 사용자 지정 도메인이 사용자의 끝점에 올바르게 참조 되는지 확인 합니다.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="유효성이 검사 되 고 연결 된 사용자 지정 도메인의 스크린샷":::

마지막으로 브라우저를 사용 하 여 응용 프로그램 콘텐츠를 제공 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 도메인에 HTTPS를 사용하도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에 HTTPS 사용]()
