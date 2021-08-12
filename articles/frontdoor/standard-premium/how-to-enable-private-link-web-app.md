---
title: Private Link를 사용하여 웹앱 원본에 Azure Front Door 프리미엄 연결
titleSuffix: Azure Private Link
description: Azure Front Door 프리미엄을 웹앱에 비공개로 연결하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: d30b73d1e80273d766f4f94224798ddb04763195
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633253"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Private Link를 사용하여 웹앱 원본에 Azure Front Door 프리미엄 연결

이 문서에서는 Azure Private Link 서비스를 사용하여 웹앱에 비공개로 연결하도록 Azure Front Door 프리미엄 SKU를 구성하는 방법을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 원본 웹 서버에 대한 [Private Link](../../private-link/create-private-link-service-portal.md) 서비스를 만듭니다.

> [!Note]
> Private Endpoint는 PremiumV2 계층/PremiumV3 계층 Windows 웹앱, Linux 웹앱 및 Azure Functions 프리미엄 플랜(탄력적 프리미엄 플랜이라고도 함)의 퍼블릭 지역에서 사용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Azure Front Door 프리미엄에서 웹앱에 Private Link 사용
 
이 섹션에서는 Private Link 서비스를 Azure Front Door 프라이빗 네트워크에서 만든 프라이빗 엔드포인트에 매핑합니다. 

1. Azure Front Door 프리미엄 프로필의 *설정* 아래에서 **원본 그룹** 을 선택합니다.

1. Private Link를 사용하도록 설정할 웹앱 원본이 포함된 원본 그룹을 선택합니다.

1. **+ 원본 추가** 를 선택하여 새 웹앱 원본을 추가하거나 목록에서 이전에 만든 웹앱 원본을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="웹앱에 대한 프라이빗 링크를 사용하도록 설정하는 스크린샷.":::

1. **Azure 리소스 선택** 에서 **In my directory(내 디렉터리에서)** 를 선택합니다. Azure Front Door 프리미엄에서 비공개로 연결할 사이트를 구성하려면 다음 설정을 선택하거나 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본과 동일하거나 가장 가까운 지역을 선택합니다. |
    | 리소스 유형 | **Microsoft.Web/sites** 를 선택합니다. |
    | 리소스 | **myPrivateLinkService** 를 선택합니다. |
    | 대상 하위 리소스 | sites |
    | 메시지 요청 | 메시지를 사용자 지정하거나 기본값을 선택합니다. |

1. 그런 다음, **추가** 를 선택하여 구성을 저장합니다.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>웹앱에서 Azure Front Door 프리미엄 프라이빗 엔드포인트 연결 승인

1. 마지막 섹션에서 Private Link를 구성한 웹앱으로 이동합니다. **설정** 에서 **네트워킹** 을 선택합니다.

1. **네트워킹** 에서 **프라이빗 엔드포인트 연결 구성** 을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="웹앱의 네트워킹 설정 스크린샷.":::

1. Azure Front Door 프리미엄에서 *보류 중* 인 프라이빗 엔드포인트 요청을 선택하고 **승인** 을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="보류 중인 개인 엔드포인트 요청의 스크린샷.":::

1. 승인되면 아래 스크린샷에서 같이 표시됩니다. 연결이 완전히 설정 되는 데 몇 분 정도 걸릴 수 있습니다. 이제 Azure Front Door 프리미엄에서 웹앱에 액세스할 수 있습니다. 프라이빗 엔드포인트를 사용하도록 설정한 후에는 공용 인터넷에서 웹앱에 직접 액세스할 수 없습니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="승인된 엔드포인트 요청의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[Azure 웹앱을 사용한 프라이빗 링크 서비스](../../app-service/networking/private-endpoint.md)에 대해 알아봅니다.
