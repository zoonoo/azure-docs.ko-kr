---
title: 개인 링크를 사용 하 여 내부 부하 분산 장치 원본에 Azure Front 도어 Premium 연결
titleSuffix: Azure Private Link
description: Azure Front 도어 프리미엄을 내부 부하 분산 장치에 연결 하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803826"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>개인 링크를 사용 하 여 내부 부하 분산 장치 원본에 Azure Front 도어 Premium 연결

이 문서에서는 azure 개인 링크 서비스를 사용 하 여 내부 부하 분산 장치 원본에 연결 하도록 Azure Front 도어 Premium SKU를 구성 하는 방법을 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[개인 링크 서비스](../../private-link/create-private-link-service-portal.md)를 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-link-to-an-internal-load-balancer"></a>내부 부하 분산 장치에 대 한 개인 링크를 사용 하도록 설정
 
이 섹션에서는 개인 링크 서비스를 Azure 전면 도어의 개인 네트워크에서 만든 개인 끝점에 매핑합니다. 

1. Azure Front 도어 Premium 프로필 내의 *설정* 에서 **원본 그룹** 을 선택 합니다.

1. 내부 부하 분산 장치에 대 한 개인 링크를 사용 하도록 설정 하려는 원본 그룹을 선택 합니다.

1. **+ 원본 추가** 를 선택 하 여 내부 부하 분산 장치 원본을 추가 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="내부 부하 분산 장치에 대 한 개인 링크를 사용 하도록 설정 하는 스크린샷":::

1. **Azure 리소스 선택** 의 경우 **내 디렉터리에서** 를 선택 합니다. Azure Front 도어 Premium을 개인적으로 연결 하려는 사이트를 구성 하려면 다음 설정을 선택 하거나 입력 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본에 동일 하거나 가장 가까운 지역을 선택 합니다. |
    | 리소스 유형 | **Microsoft.Network/privateLinkServices** 를 선택합니다. |
    | 리소스 | 내부 부하 분산 장치에 연결 된 개인 링크를 선택 합니다. |
    | 대상 하위 리소스 | 비워 둡니다. |
    | 메시지 요청 | 메시지를 사용자 지정 하거나 기본값을 선택 합니다. |

1. 그런 다음 **추가** 를 선택 하 고 **업데이트** 를 선택 하 여 구성을 저장 합니다.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>저장소 계정에서 개인 끝점 연결 승인

1. 개인 링크 센터로 이동 하 고 **개인 링크 서비스** 를 선택 합니다. 그런 다음 개인 링크 이름을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="개인 링크 목록의 스크린샷.":::

1. *설정* 아래에서 **개인 끝점 연결** 을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="개인 링크 개요 페이지의 스크린샷":::

1. Azure Front 도어 Premium에서 *보류 중인* 개인 끝점 요청을 선택 하 고 **승인** 을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="개인 링크에 대 한 보류 중인 승인의 스크린샷":::

1. 승인 되 면 아래 스크린샷에서 같이 표시 됩니다. 연결이 완전히 설정 되는 데 몇 분 정도 걸릴 수 있습니다. 이제 Azure Front 도어 Premium에서 내부 부하 분산 장치에 액세스할 수 있습니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="승인 된 개인 링크 요청의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[개인 링크 서비스](../../private-link/private-link-service-overview.md)에 대해 알아봅니다.
