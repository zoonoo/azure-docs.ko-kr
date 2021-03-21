---
title: Endpoint Manager를 사용 하 여 Azure Front 도어 표준/프리미엄 끝점 구성
description: 이 문서에서는 끝점 관리자를 사용 하 여 끝점을 구성 하는 방법을 보여 줍니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099668"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Endpoint Manager를 사용 하 여 Azure Front 도어 표준/프리미엄 (미리 보기) 끝점 구성

> [!NOTE]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? **[Azure 전방 도어 문서](../front-door-overview.md)** 를 봅니다.

이 문서에서는 끝점 관리자를 사용 하 여 기존 Azure Front 도어 Standard/Premium 프로필에 대 한 끝점을 만드는 방법을 보여 줍니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

끝점 관리자를 사용 하 여 Azure Front 도어 Standard/Premium 끝점을 만들려면 먼저 만든 Azure Front 도어 프로필을 하나 이상 만들어야 합니다. 프로필에 하나 이상의 Azure Front 도어 표준/프리미엄 끝점이 있어야 합니다. 인터넷 도메인, 웹 응용 프로그램 또는 기타 기준에 따라 Azure Front 도어 표준/프리미엄 끝점을 구성 하려면 여러 프로필을 사용할 수 있습니다. 

Azure Front 도어 프로필을 만들려면 [새 Azure Front 도어 표준/프리미엄 프로필 만들기](create-front-door-portal.md)를 참조 하세요.

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>새 Azure Front 도어 표준/프리미엄 끝점을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Front 도어 표준/프리미엄 프로필로 이동 합니다.

1. **끝점 관리자** 를 선택 합니다. 그런 다음 **끝점 추가** 를 선택 하 여 새 끝점을 만듭니다.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="끝점 관리자를 통해 끝점 추가의 스크린샷":::

1. **끝점 추가** 페이지에서를 입력 하 고 다음 설정을 선택 합니다.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="끝점 추가 페이지의 스크린샷":::

    | 설정 | 값 |
    | -------- | ----- |
    | Name | 새 Azure Front 도어 표준/프리미엄 끝점에 대 한 고유한 이름을 입력 합니다. 이 이름은 도메인의 캐시 된 리소스에 액세스 하는 데 사용 됩니다. `<endpointname>.az01.azurefd.net` |
    | 원본 응답 시간 제한 (초) | 원본에 대 한 연결을 고려 하기 전에 Azure Front 도어가 대기 하는 시간 제한 값 (초)을 입력 합니다. |
    | 상태 | 이 끝점을 사용 하도록 설정 하려면 확인란을 선택 합니다. |

## <a name="add-domains-origin-group-routes-and-security"></a>도메인, 원본 그룹, 경로 및 보안 추가

1. 끝점에서 **끝점 편집** 을 선택 하 여 경로를 구성 합니다.

1. **끝점 편집** 페이지의 도메인에서 **+ 추가** 를 선택 합니다.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="끝점 편집 페이지에서 도메인 선택의 스크린샷":::

### <a name="add-domain"></a>도메인 추가

1. **도메인 추가** 페이지에서 *Azure Front 도어 프로필의* 도메인을 연결 하거나 *새 도메인을 추가* 하도록 선택 합니다. 새 도메인을 만드는 방법에 대 한 자세한 내용은 [새 Azure Front 도어 표준/프리미엄 사용자 지정 도메인 만들기](how-to-add-custom-domain.md)를 참조 하세요.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="도메인 추가 페이지의 스크린샷":::

1. **추가** 를 선택 하 여 현재 끝점에 도메인을 추가 합니다. 선택한 도메인이 도메인 패널 내에 표시 됩니다.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="도메인 보기의 도메인 스크린샷":::

### <a name="add-origin-group"></a>원본 그룹 추가

1. 원본 그룹 보기에서 **추가** 를 선택 합니다. **원본 그룹 추가** 페이지가 나타납니다. 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="원본 그룹 추가 페이지의 스크린샷":::

1. **이름** 에 새 원본 그룹의 고유한 이름을 입력 합니다.

1. 현재 그룹에 새 원본을 추가 하려면 **원본 추가** 를 선택 합니다.
 
#### <a name="health-probes"></a>상태 프로브
프런트 도어는 각 원본에 주기적으로 HTTP/HTTPS 프로브 요청을 보냅니다. 프로브 요청은 최종 사용자 요청의 부하를 분산 하기 위해 각 원본의 근접성과 상태를 결정 합니다. 원본 그룹에 대 한 상태 프로브 설정은 앱 원본 상태를 폴링하는 방법을 정의 합니다. 다음 설정은 부하 분산 구성에 사용할 수 있습니다.

> [!WARNING]
> 전면 도어에는 전역적으로 많은에 지 환경이 있으므로 원본에 대 한 상태 프로브 볼륨은 구성 된 상태 프로브 주기에 따라 분당 25 개 요청부터 분당 1200 요청까지 매우 높을 수 있습니다. 기본 프로브 주파수를 30 초로 설정 하면 원본에 대 한 프로브 볼륨이 분당 약 200 요청 이어야 합니다.

* **상태**: 상태 검색을 켤 지 여부를 지정 합니다. 원본 그룹에 원본이 하나 있는 경우 상태 프로브를 사용 하지 않도록 설정 하 여 응용 프로그램 백 엔드에 대 한 부하를 줄일 수 있습니다. 그룹에 원본이 여러 개 있지만 둘 중 하나만 사용 상태에 있는 경우에도 상태 프로브를 사용 하지 않도록 설정할 수 있습니다.
   
* **경로**:이 원본 그룹의 모든 원본에 대 한 프로브 요청에 사용 되는 URL입니다. 예를 들어 원본 중 하나가 contoso-westus.azurewebsites.net이 고 경로가/probe/test.aspx로 설정 되어 있으면 프로토콜이 HTTP로 설정 된 것으로 가정 하는 프런트 도어 환경에서로 상태 프로브 요청을 보냅니다 `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **프로토콜**: HTTP 또는 HTTPS 프로토콜을 사용 하 여 Front 도어에서 원본으로 상태 프로브 요청을 보낼지 여부를 정의 합니다.
   
* **프로브 방법**: 상태 프로브를 보내는 데 사용할 HTTP 메서드입니다. 옵션에는 GET 또는 HEAD (기본값)이 포함 됩니다.

    > [!NOTE]
    > 원본에 대 한 부하와 비용을 낮추기 위해 전방 도어는 상태 프로브에 HEAD 요청을 사용 하도록 권장 합니다.

* **간격 (초)**: 원본에 대 한 상태 프로브의 빈도 또는 각 프런트 도어 환경에서 프로브를 전송 하는 간격을 정의 합니다.
   
    >[!NOTE]
    >더 빠른 장애 조치 (failover)를 위해 간격을 더 낮은 값으로 설정 합니다. 값이 낮을수록 원본에서 받은 상태 프로브 볼륨이 높아집니다. 예를 들어, 간격이 30 초로 설정 된 경우 100 앞면 도어를 전역적으로 설정 하면 각 백 엔드는 분당 200 프로브 요청을 수신 합니다.

#### <a name="load-balancing"></a>부하 분산
원본 그룹에 대 한 부하 분산 설정은 상태 프로브를 평가 하는 방법을 정의 합니다. 이러한 설정은 백 엔드가 정상 인지 비정상 인지를 결정 합니다. 또한 원본 그룹의 서로 다른 원본 간에 트래픽 부하를 분산 하는 방법을 확인 합니다. 다음 설정은 부하 분산 구성에 사용할 수 있습니다.

- **샘플 크기** 입니다. 원본 상태 평가에 대해 고려해 야 하는 상태 프로브 샘플 수를 식별 합니다.

- **성공적인 샘플 크기** 입니다. 앞에서 설명한 대로 샘플 크기를 정의 합니다. 원래 호출에 필요한 성공한 샘플 수는 정상입니다. 예를 들어, 전면 도어 상태 프로브 간격이 30 초이 고, 샘플 크기가 5이 고, 성공한 샘플 크기는 3 이라고 가정 합니다. 원본에 대 한 상태 프로브를 평가할 때마다 150 초 (5 x 30) 이상 지난 5 개 샘플을 살펴보겠습니다. 백 엔드를 정상으로 선언 하려면 적어도 3 개의 성공한 프로브가 필요 합니다.

- **대기 시간 민감도 (추가 대기 시간)**. 앞으로 이동 하 여 대기 시간 측정 민감도 범위 내에서 요청을 원본으로 보낼지 아니면 가장 가까운 백 엔드로 요청을 전송할지를 정의 합니다.

**추가** 를 선택 하 여 현재 끝점에 원본 그룹을 추가 합니다. 원본 그룹이 원본 그룹 패널에 표시 됩니다.

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="원본 그룹의 원본 스크린샷":::

### <a name="add-route"></a>경로 추가

경로 보기에서 **추가** 를 선택 하면 **경로 추가** 페이지가 나타납니다. 도메인 및 원본 그룹을 연결 하는 방법에 대 한 자세한 내용은 [새 Azure 프런트 도어 경로 만들기](how-to-configure-route.md) 를 참조 하세요.

### <a name="add-security"></a>보안 추가

1. 보안 보기에서 **추가** 를 선택 하면 **waf 정책 추가** 페이지가 나타납니다.
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="WAF 정책 추가 페이지의 스크린샷":::

1. **Waf 정책**:이 끝점 내에서 선택한 도메인에 적용 하려는 waf 정책을 선택 합니다. 
  
   새 WAF 정책을 만들려면 **새로 만들기** 를 선택 합니다.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="새 WAF 정책 만들기의 스크린샷":::
   
    **이름**: 새 waf 정책의 고유한 이름을 입력 합니다. 웹 응용 프로그램 방화벽 페이지에서 더 많은 구성을 사용 하 여이 정책을 편집할 수 있습니다.

    **도메인**: waf 정책을 적용할 도메인을 선택 합니다.

1. **추가** 단추를 선택 합니다. WAF 정책이 보안 패널 내에 표시 됩니다.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="보안 보기의 WAF 정책 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 끝점을 삭제 하려면 끝점 행의 끝에서 끝점 **삭제** 를 선택 합니다. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="끝점을 삭제 하는 방법의 스크린샷":::

## <a name="next-steps"></a>다음 단계

사용자 지정 도메인에 대 한 자세한 내용은 [사용자 지정 도메인 추가](how-to-add-custom-domain.md)를 계속 합니다.
