---
title: 개인 링크를 사용 하 여 Azure Front 문을 웹 앱 원본에 연결
titleSuffix: Azure Private Link
description: Azure Front 도어 Premium을 개인적으로 webapp에 연결 하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: f3250101449971ffd985fcfcce400870e2a3d50f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745366"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>개인 링크를 사용 하 여 Azure Front 문을 웹 앱 원본에 연결

이 문서에서는 azure 개인 링크 서비스를 사용 하 여 개인적으로 웹 앱에 연결 하도록 Azure Front 도어 Premium SKU를 구성 하는 방법을 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 원본 웹 서버에 대 한 [개인 링크](../../private-link/create-private-link-service-portal.md) 서비스를 만듭니다.

> [!Note]
> Private Endpoint는 PremiumV2 계층/PremiumV3 계층 Windows 웹앱, Linux 웹앱 및 Azure Functions 프리미엄 플랜(탄력적 프리미엄 플랜이라고도 함)의 퍼블릭 지역에서 사용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Azure 전면 도어 프리미엄에서 웹 앱에 대 한 개인 링크를 사용 하도록 설정
 
이 섹션에서는 개인 링크 서비스를 Azure 전면 도어의 개인 네트워크에서 만든 개인 끝점에 매핑합니다. 

1. Azure Front 도어 Premium 프로필 내의 *설정* 에서 **원본 그룹** 을 선택 합니다.

1. 개인 링크를 사용 하도록 설정 하려는 웹 앱 원본이 포함 된 원본 그룹을 선택 합니다.

1. **+ 원본 추가** 를 선택 하 여 새 웹 앱 원본을 추가 하거나 목록에서 이전에 만든 웹 앱 원본을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="웹 앱에 대 한 개인 링크를 사용 하도록 설정 하는 스크린샷":::

1. **Azure 리소스 선택** 의 경우 **내 디렉터리에서** 를 선택 합니다. Azure Front 도어 Premium을 개인적으로 연결 하려는 사이트를 구성 하려면 다음 설정을 선택 하거나 입력 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본에 동일 하거나 가장 가까운 지역을 선택 합니다. |
    | 리소스 유형 | **Microsoft.Web/sites** 를 선택합니다. |
    | 리소스 | **myPrivateLinkService** 를 선택합니다. |
    | 대상 하위 리소스 | sites |
    | 메시지 요청 | 메시지를 사용자 지정 하거나 기본값을 선택 합니다. |

1. 그런 다음 **추가** 를 선택 하 여 구성을 저장 합니다.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>웹 앱에서 Azure Front 도어 Premium 개인 끝점 연결 승인

1. 마지막 섹션에서 개인 링크로 구성 된 웹 앱으로 이동 합니다. **설정** 아래에서 **네트워킹** 을 선택 합니다.

1. **네트워킹** 에서 **프라이빗 엔드포인트 연결 구성** 을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="웹 앱에서 네트워킹 설정의 스크린샷":::

1. Azure Front 도어 Premium에서 *보류 중인* 개인 끝점 요청을 선택 하 고 **승인** 을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="보류 중인 개인 끝점 요청의 스크린샷.":::

1. 승인 되 면 아래 스크린샷에서 같이 표시 됩니다. 연결이 완전히 설정 되는 데 몇 분 정도 걸릴 수 있습니다. 이제 Azure Front 도어 Premium에서 웹 앱에 액세스할 수 있습니다. 개인 끝점을 사용 하도록 설정한 후에는 공용 인터넷에서 웹 앱에 직접 액세스할 수 없습니다.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="승인 된 끝점 요청의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[Azure 웹 앱을 사용한 개인 링크 서비스](../../app-service/networking/private-endpoint)에 대해 알아봅니다.
