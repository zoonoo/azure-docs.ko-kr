---
title: Azure Front Door 표준/프리미엄 SKU 구성에 사용자 지정 도메인을 추가하는 방법
description: 이 자습서에서는 사용자 지정 도메인을 Azure Front Door 표준/프리미엄 SKU에 등록하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387924"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Azure Portal를 사용하여 Azure Front Door 표준/프리미엄 SKU(미리 보기)에서 사용자 지정 도메인 만들기

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

애플리케이션 전송에 Azure Front Door 표준/프리미엄을 사용하는 경우 고유한 도메인 이름을 최종 사용자 요청에 표시하려면 사용자 지정 도메인이 필요합니다. 볼 수 있는 도메인 이름이 있다면 고객에게 편리하고 브랜딩 목적상 유용합니다.

Azure Front Door 표준/프리미엄 프로필을 만든 후 기본 프런트 엔드 호스트는 azurefd.net의 하위 도메인을 갖게 됩니다. 이 하위 도메인은 Azure Front Door 표준/프리미엄이 기본적으로 백 엔드에서 콘텐츠를 제공하는 경우 URL에 포함됩니다. 예: `https://contoso-frontend.azurefd.net/activeusers.htm`. 사용자 편의를 위해 Azure Front Door는 사용자 지정 도메인을 기본 호스트에 연결하는 옵션을 제공합니다. 이 옵션을 사용하여 Azure Front Door 표준/프리미엄 소유 도메인 이름 대신 URL의 사용자 지정 도메인을 사용하여 콘텐츠를 전송합니다. 예제: ‘https://www.contoso.com/photo.png’.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
* 이 자습서의 단계를 완료하려면 먼저 Front Door를 만들어야 합니다. 자세한 내용은 [빠른 시작: Front Door 표준/프리미엄 만들기](create-front-door-portal.md)를 참조하세요.

* 사용자 지정 도메인이 없으면 먼저 도메인 공급자를 통해 구매해야 합니다. 예를 들어 [사용자 지정 도메인 이름 구매](../../app-service/manage-custom-dns-buy-domain.md)를 참조하세요.

* Azure를 사용하여 [DNS 도메인](../../dns/dns-overview.md)을 호스트하는 경우 도메인 공급자의 DNS(Domain Name System)를 Azure DNS에 위임해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](../../dns/dns-delegate-domain-azure-dns.md)을 참조하세요. 그렇지 않으면 도메인 공급자를 사용하여 DNS 도메인을 처리하는 경우 DNS TXT 레코드를 입력하여 도메인의 유효성을 수동으로 확인해야 합니다.

## <a name="add-a-new-custom-domain"></a>새 사용자 지정 도메인 추가

> [!NOTE]
> 공개 미리 보기 상태에서 Azure DNS를 사용하여 Apex 도메인을 만드는 것은 Azure Front Door 표준/프리미엄에서 지원되지 않습니다. APEX 도메인을 Azure Front Door 표준/프리미엄에 사용할 수 있도록 하는 CNAME 평면화 또는 DNS 추적을 지원하는 다른 DNS 공급자가 있습니다.

사용자 지정 도메인은 포털의 도메인 섹션에서 관리합니다. 엔드포인트에 연결하기 전에 사용자 지정 도메인을 만들고 유효성을 검사할 수 있습니다. 사용자 지정 도메인 및 해당 하위 도메인은 한 번에 하나의 단일 엔드포인트와 연결될 수 있습니다. 그러나 동일한 사용자 지정 도메인의 다른 하위 도메인은 다른 Front Door에 사용할 수 있습니다. 여러 하위 도메인이 있는 사용자 지정 도메인을 동일한 Front Door 엔드포인트에 매핑할 수도 있습니다.

1. Azure Front Door 프로필에 대한 설정에서 *도메인* 을 선택한 다음 **도메인 추가** 단추를 선택합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="도메인 방문 페이지에서 도메인 추가 단추의 스크린샷":::

1. 사용자 지정 도메인에 대한 정보를 입력할 수 있는 **도메인 추가** 페이지가 나타납니다. 권장되는 Azure 관리 DNS를 선택하거나 고유한 DNS 공급자를 사용하도록 선택할 수 있습니다. Azure 관리 DNS를 선택하는 경우 기존 DNS 영역을 선택한 다음, 사용자 지정 하위 도메인을 선택하거나 새 하위 도메인을 만듭니다. 다른 DNS 공급자를 사용하는 경우 사용자 지정 도메인 이름을 수동으로 입력합니다. **추가** 를 선택하여 사용자 지정 도메인을 추가합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="도메인 추가 페이지의 스크린샷":::

    유효성 검사 상태가 **제출 중** 인 새 사용자 지정 도메인이 만들어집니다.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="유효성 검사 상태가 제출 중인 도메인의 스크린샷":::

    유효성 검사 상태가 **보류 중** 으로 변경될 때까지 기다립니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="유효성 검사 상태가 보류 중인 도메인의 스크린샷":::

1. **보류 중** 유효성 검사 상태를 선택합니다. 사용자 지정 도메인의 유효성을 검사하는 데 필요한 DNS TXT 레코드 정보가 포함된 새 페이지가 표시됩니다. TXT 레코드는 `_dnsauth.<your_subdomain>` 형식입니다. Azure DNS 기반 영역을 사용하는 경우 **추가** 단추를 선택하면 표시된 레코드 값을 포함하는 새 TXT 레코드가 Azure DNS 영역에 만들어집니다. 다른 DNS 공급자를 사용하는 경우 페이지에 표시된 대로 레코드 값을 사용하여 이름 `dnsauth.<your_subdomain>`에 대한 새 TXT 레코드를 수동으로 만듭니다.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="사용자 지정 도메인 유효성 검사 페이지의 스크린샷":::

1. 새로 고침 상태를 선택합니다. DNS TXT 레코드를 사용하여 도메인의 유효성을 검사하면 유효성 검사 상태가 **확인됨** 으로 변경됩니다. 이 작업의 유효성을 검사하는 데 몇 분 정도 걸릴 수 있습니다.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="유효성 검사된 사용자 지정 도메인의 스크린샷":::

1. 사용자 지정 도메인 목록 방문 페이지로 돌아가려면 페이지를 닫습니다. 사용자 지정 도메인의 프로비전 상태가 **프로비전됨** 으로 변경되고 유효성 검사 상태가 **승인됨** 으로 변경되어야 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="프로비전되고 승인된 상태의 스크린샷":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>사용자 지정 도메인을 Front Door 엔드포인트와 연결

사용자 지정 도메인의 유효성을 검사하면 Azure Front Door 표준/프리미엄 엔드포인트에 추가할 수 있습니다.

1. 사용자 지정 도메인의 유효성이 검사되면 기존 Azure Front Door 표준/프리미엄 엔드포인트 및 경로에 연결할 수 있습니다. **엔드포인트 연결** 링크를 선택하여 **엔드포인트 및 경로 연결** 페이지를 엽니다. 연결하려는 엔드포인트와 경로를 선택합니다. 그런 다음 **연결** 을 선택합니다. 연결 작업이 완료되면 페이지를 닫습니다.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="엔드포인트 및 경로 연결 페이지의 스크린샷":::

    사용자 지정 도메인이 현재 연결되어 있는 엔드포인트를 반영하도록 엔드포인트 연결 상태가 변경되어야 합니다. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="엔드포인트 연결 링크의 스크린샷":::

1. DNS 상태 링크를 선택합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="DNS 상태 링크의 스크린샷":::

1. **CNAME 레코드 추가 또는 업데이트** 페이지가 표시되고 트래픽 흐름을 시작하기 전에 제공해야 하는 CNAME 레코드 정보가 표시됩니다. Azure DNS 호스팅 영역을 사용하는 경우 페이지에서 **추가** 단추를 선택하여 CNAME 레코드를 만들 수 있습니다. 다른 DNS 공급자를 사용하는 경우 페이지에 표시된 대로 CNAME 레코드 이름과 값을 수동으로 입력해야 합니다.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="CNAME 레코드 추가 또는 업데이트의 스크린샷":::

1. CNAME 레코드가 만들어지고 사용자 지정 도메인이 Azure Front Door 엔드포인트에 연결되면 트래픽 흐름이 시작됩니다.

    > [!NOTE]
    > HTTPS를 사용하는 경우 모든 가장자리 위치로 전파가 수행되기 때문에 인증서 프로비전 및 전파에 몇 분 정도 걸릴 수 있습니다. 

## <a name="verify-the-custom-domain"></a>사용자 지정 도메인 확인

사용자 지정 도메인의 유효성을 검사하고 연결한 후 사용자 지정 도메인이 사용자의 엔드포인트에 올바르게 참조되었는지 확인합니다.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="유효성이 검사되고 연결된 사용자 지정 도메인의 스크린샷":::

그런 다음, 마지막으로 브라우저를 사용하여 애플리케이션 콘텐츠가 제공되고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 도메인에 HTTPS를 사용하도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에 HTTPS 사용]()
