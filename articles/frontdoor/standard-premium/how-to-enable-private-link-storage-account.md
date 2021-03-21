---
title: 개인 링크를 사용 하 여 Azure 전면 도어 프리미엄을 저장소 계정 원본에 연결
titleSuffix: Azure Private Link
description: Azure Front 도어 Premium을 개인적으로 저장소 계정에 연결 하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201671"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>개인 링크를 사용 하 여 Azure 전면 도어 프리미엄을 저장소 계정 원본에 연결

이 문서에서는 azure 개인 링크 서비스를 사용 하 여 개인 저장소 계정 원본에 연결 하도록 Azure Front 도어 Premium SKU를 구성 하는 방법을 안내 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-link-to-a-storage-account"></a>저장소 계정에 대 한 개인 링크 사용
 
이 섹션에서는 개인 링크 서비스를 Azure 전면 도어의 개인 네트워크에서 만든 개인 끝점에 매핑합니다. 

1. Azure Front 도어 Premium 프로필 내의 *설정* 에서 **원본 그룹** 을 선택 합니다.

1. 개인 링크를 사용 하도록 설정 하려는 저장소 계정 원본을 포함 하는 원본 그룹을 선택 합니다.

1. **+ 원본 추가** 를 선택 하 여 새 저장소 계정 원본을 추가 하거나 목록에서 이전에 만든 저장소 계정 원본을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="저장소 계정에 대 한 개인 링크를 사용 하도록 설정 하는 스크린샷":::

1. **Azure 리소스 선택** 의 경우 **내 디렉터리에서** 를 선택 합니다. Azure Front 도어 Premium을 개인적으로 연결 하려는 사이트를 구성 하려면 다음 설정을 선택 하거나 입력 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본에 동일 하거나 가장 가까운 지역을 선택 합니다. |
    | 리소스 종류 | **Microsoft. Storage/storageAccounts** 를 선택 합니다. |
    | 리소스 | 사용자의 스토리지 계정을 선택합니다. |
    | 대상 하위 리소스 | *Blob* 또는 *웹* 을 선택할 수 있습니다. |
    | 메시지 요청 | 메시지를 사용자 지정 하거나 기본값을 선택 합니다. |

1. 그런 다음 **추가** 를 선택 하 여 구성을 저장 합니다.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>저장소 계정에서 개인 끝점 연결 승인

1. 마지막 섹션에서 개인 링크를 구성 하는 저장소 계정으로 이동 합니다. **설정** 아래에서 **네트워킹** 을 선택 합니다.

1. **네트워킹** 에서 **개인 끝점 연결** 을 선택 합니다. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="웹 앱에서 네트워킹 설정의 스크린샷":::

1. Azure Front 도어 Premium에서 *보류 중인* 개인 끝점 요청을 선택 하 고 **승인** 을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="보류 중인 저장소 개인 끝점 요청 스크린샷":::

1. 승인 되 면 아래 스크린샷에서 같이 표시 됩니다. 연결이 완전히 설정 되는 데 몇 분 정도 걸릴 수 있습니다. 이제 Azure Front 도어 프리미엄에서 저장소 계정에 액세스할 수 있습니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="승인 된 저장소 끝점 요청의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[저장소 계정을 사용한 개인 링크 서비스](../../storage/common/storage-private-endpoints.md)에 대해 알아봅니다.
