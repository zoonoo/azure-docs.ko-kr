---
title: Endpoint Manager를 사용하여 Azure Front Door 표준/프리미엄 엔드포인트 구성
description: 이 문서에서는 Endpoint Manager를 사용하여 엔드포인트를 구성하는 방법을 보여 줍니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099668"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Endpoint Manager를 사용하여 Azure Front Door 표준/프리미엄(미리 보기) 엔드포인트 구성

> [!NOTE]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? **[Azure Front Door 문서](../front-door-overview.md)** 를 참조하세요.

이 문서에서는 Endpoint Manager를 사용하여 기존 Azure Front Door 표준/프리미엄 프로필의 엔드포인트를 만드는 방법을 보여 줍니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Endpoint Manager를 사용하여 Azure Front Door 표준/프리미엄 엔드포인트를 만들려면 먼저 생성된 Azure Front Door 프로필이 하나 이상 있어야 합니다. 이 프로필에는 하나 이상의 Azure Front Door 표준/프리미엄 엔드포인트가 있어야 합니다. 인터넷 도메인, 웹 애플리케이션 또는 기타 조건을 기준으로 Azure Front Door 표준/프리미엄 엔드포인트를 구성하려면 여러 프로필을 사용하면 됩니다. 

Azure Front Door 프로필을 만들려면 [새 Azure Front Door 표준/프리미엄 프로필 만들기](create-front-door-portal.md)를 참조하세요.

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>새 Azure Front Door 표준/프리미엄 엔드포인트 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Front Door 표준/프리미엄 프로필로 이동합니다.

1. **Endpoint Manager** 를 선택합니다. 그런 다음 **엔드포인트 추가** 를 선택하여 새 엔드포인트를 만듭니다.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Endpoint Manager를 통한 엔드포인트 추가 스크린샷.":::

1. **엔드포인트 추가** 페이지에서 다음 설정을 입력하고 선택합니다.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="엔드포인트 추가 페이지의 스크린샷.":::

    | 설정 | 값 |
    | -------- | ----- |
    | 이름 | 새 Azure Front Door 표준/프리미엄 엔드포인트의 고유한 이름을 입력합니다. 이 이름은 `<endpointname>.az01.azurefd.net` 도메인의 캐시된 리소스에 액세스하기 위해 사용됩니다. |
    | 원본 응답 시간 제한(초) | 원본과의 연결에 시간 제한이 있다는 점을 고려하여 Azure Front Door가 대기할 제한 시간 값을 초 단위로 입력합니다. |
    | 상태 | 이 엔드포인트를 사용하려면 확인란을 선택합니다. |

## <a name="add-domains-origin-group-routes-and-security"></a>도메인, 원본 그룹, 경로 및 보안 추가

1. 엔드포인트에서 **엔드포인트 편집** 을 선택하여 경로를 구성합니다.

1. **엔드포인트 편집** 페이지의 도메인에서 **+ 추가** 를 선택합니다.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="엔드포인트 편집 페이지에서 도메인 선택의 스크린샷.":::

### <a name="add-domain"></a>도메인 추가

1. **도메인 추가** 페이지에서 Azure Front Door 프로필의 도메인을 연결하거나 새 도메인을 추가하도록 선택합니다. 새 도메인을 만드는 방법에 대한 자세한 내용은 [새 Azure Front Door 표준/프리미엄 사용자 지정 도메인 만들기](how-to-add-custom-domain.md)를 참조하세요.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="도메인 추가 페이지의 스크린샷.":::

1. **추가** 를 선택하여 현재 엔드포인트에 도메인을 추가합니다. 선택한 도메인이 도메인 패널 내에 표시됩니다.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="도메인 보기의 도메인 스크린샷.":::

### <a name="add-origin-group"></a>원본 그룹 추가

1. 원본 그룹 보기에서 **추가** 를 선택합니다. **원본 그룹 추가** 페이지가 나타납니다. 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="원본 그룹 추가 페이지의 스크린샷":::

1. **이름** 에 새 원본 그룹의 고유한 이름을 입력합니다.

1. 현재 그룹에 새 원본을 추가하려면 **원본 추가** 를 선택합니다.
 
#### <a name="health-probes"></a>상태 프로브
Front Door는 각 원본에 주기적으로 HTTP/HTTPS 프로브 요청을 보냅니다. 프로브 요청은 최종 사용자 요청의 부하를 분산하기 위해 각 원본의 근접성과 상태를 확인합니다. 원본 그룹에 대한 상태 프로브 설정은 앱 원본의 상태를 폴링하는 방법을 정의합니다. 다음 설정은 부하 분산 구성에 사용할 수 있습니다.

> [!WARNING]
> Front Door는 전 세계적으로 많은 엣지 환경을 포함하므로, 원본에 대한 상태 프로브 볼륨은 구성된 상태 프로브 빈도에 따라 분당 25개 요청에서 분당 1,200개 요청까지 매우 높을 수 있습니다. 기본 프로브 빈도 30초를 사용할 경우 원본 프로브 볼륨은 분당 약 200개 요청이 되어야 합니다.

* **상태**: 상태 검색을 켤지 여부를 지정합니다. 원본 그룹에 원본이 하나 있는 경우 상태 프로브를 사용하지 않도록 설정하여 애플리케이션 백 엔드에 대한 부하를 줄일 수 있습니다. 그룹에 원본이 여러 개 있지만 그중 하나만 사용 상태인 경우에도 상태 프로브를 사용하지 않도록 설정할 수 있습니다.
   
* **경로**: 이 원본 그룹의 모든 원본에 대한 프로브 요청에 사용되는 URL입니다. 예를 들어 원본 중 하나가 contoso-westus.azurewebsites.net이고 경로가 /probe/test.aspx로 설정된 경우 Front Door 환경은 프로토콜이 HTTP로 설정되었다고 가정하고 `http://contoso-westus.azurewebsites.net/probe/test.aspx`로 상태 프로브 요청을 보냅니다. 
   
* **프로토콜**: HTTP 또는 HTTPS 프로토콜을 사용하여 Front Door에서 원본으로 상태 프로브 요청을 보낼지 여부를 정의합니다.
   
* **프로브 메서드**: 상태 프로브를 보내는 데 사용할 HTTP 메서드입니다. 옵션에는 GET 또는 HEAD(기본값)가 포함됩니다.

    > [!NOTE]
    > 원본에 대한 부하 및 비용을 낮추기 위해 Front Door는 상태 프로브에 HEAD 요청을 사용할 것을 권장합니다.

* **간격(초)** : 원본에 대한 상태 프로브의 빈도 또는 각 Front Door 환경에서 프로브를 전송하는 간격을 정의합니다.
   
    >[!NOTE]
    >더 빠른 장애 조치(failover)를 위해서는 간격을 더 낮은 값으로 설정합니다. 이 값이 낮을수록 원본이 수신하는 상태 프로브 용량이 더 많아집니다. 예를 들어 전역으로 100 Front Door POP에 간격이 30초로 설정된 경우 각 백 엔드는 분당 200개의 프로브 요청을 수신합니다.

#### <a name="load-balancing"></a>부하 분산
원본 그룹에 대한 부하 분산 설정은 상태 프로브를 평가하는 방법을 정의합니다. 이러한 설정은 백 엔드가 정상인지 비정상인지를 결정합니다. 또한 원본 그룹의 여러 원본 간에 트래픽 부하를 분산하는 방법도 확인합니다. 다음 설정은 부하 분산 구성에 사용할 수 있습니다.

- **샘플 크기** 원본 상태 평가에서 고려해야 하는 상태 프로브의 샘플 수를 식별합니다.

- **성공적인 샘플 크기**. 앞에서 설명한 대로 샘플 크기, 즉 원본을 정상이라고 부르기 위해 필요한 성공적인 샘플 수를 정의합니다. 예를 들어 Front Door 상태 프로브 간격이 30초, 샘플 크기가 5, 성공적인 샘플 크기가 3이라고 가정해 보겠습니다. 원본에 대한 상태 프로브를 평가할 때마다 지난 5개 샘플을 150초(5 x 30) 이상 살펴봅니다. 백 엔드를 정상으로 선언하려면 적어도 성공적인 프로브가 3개 필요합니다.

- **대기 시간 민감도(추가 대기 시간)** . Front Door가 대기 시간 측정 민감도 범위 내에서 요청을 원본으로 보낼지 가장 가까운 백 엔드로 요청을 전달할지 여부를 정의합니다.

**추가** 를 선택하여 현재 엔드포인트에 원본 그룹을 추가합니다. 원본 그룹이 원본 그룹 패널 내에 표시됩니다.

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="원본 그룹의 원본 스크린샷.":::

### <a name="add-route"></a>경로 추가

경로 보기에서 **추가** 를 선택하면 **경로 추가** 페이지가 나타납니다. 도메인 및 원본 그룹을 연결하는 방법에 대한 자세한 내용은 [새 Azure Front Door 경로 만들기](how-to-configure-route.md)를 참조하세요.

### <a name="add-security"></a>보안 추가

1. 보안 보기에서 **추가** 를 선택하면 **WAF 정책 추가** 페이지가 나타납니다.
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="WAF 정책 추가 페이지의 스크린샷.":::

1. **WAF 정책**: 이 엔드포인트 내에서 선택한 도메인에 적용하려는 WAF 정책을 선택합니다. 
  
   새 WAF 정책을 만들려면 **새로 만들기** 를 선택합니다.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="새 WAF 정책 만들기의 스크린샷.":::
   
    **이름**: 새 WAF 정책의 고유한 이름을 입력합니다. 웹 애플리케이션 방화벽 페이지에서 더 많은 구성을 사용하여 이 정책을 편집할 수 있습니다.

    **도메인**: WAF 정책을 적용할 도메인을 선택합니다.

1. **추가** 단추를 선택합니다. WAF 정책이 보안 패널 내에 표시됩니다.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="보안 보기의 WAF 정책 스크린샷.":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 엔드포인트를 삭제하려면 엔드포인트 행의 끝에서 **엔드포인트 삭제** 를 선택합니다. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="엔드포인트를 삭제하는 방법의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

사용자 지정 도메인에 대해 알아보려면 [사용자 지정 도메인](how-to-add-custom-domain.md)으로 이동하세요.
