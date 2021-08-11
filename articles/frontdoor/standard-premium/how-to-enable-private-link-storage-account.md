---
title: Private Link를 사용하여 스토리지 계정 원본에 Azure Front Door 프리미엄 연결
titleSuffix: Azure Private Link
description: Azure Front Door 프리미엄을 스토리지 계정에 비공개로 연결하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: duau
ms.openlocfilehash: 991727c369b70fdd84507f6d32b6f8a3405fa4ac
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633558"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Private Link를 사용하여 스토리지 계정 원본에 Azure Front Door 프리미엄 연결

이 문서에서는 Azure Private Link 서비스를 사용하여 스토리지 계정 원본에 비공개로 연결하도록 Azure Front Door 프리미엄 SKU를 구성하는 방법을 안내합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-link-to-a-storage-account"></a>스토리지 계정에 Private Link 사용
 
이 섹션에서는 Private Link 서비스를 Azure Front Door의 개인 네트워크에서 만든 프라이빗 엔드포인트에 매핑합니다. 

1. Azure Front Door 프리미엄 프로필의 *설정* 아래에서 **원본 그룹** 을 선택합니다.

1. Private Link를 사용하도록 설정할 스토리지 계정 원본이 포함된 원본 그룹을 선택합니다.

1. **+ 원본 추가** 를 선택하여 새 스토리지 계정 원본을 추가하거나 목록에서 이전에 만든 스토리지 계정 원본을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="스토리지 계정에 대한 프라이빗 링크를 사용하도록 설정하는 스크린샷":::

1. **Azure 리소스 선택** 에서 **내 디렉터리에서** 를 선택합니다. Azure Front Door 프리미엄에서 비공개로 연결할 사이트를 구성하려면 다음 설정을 선택하거나 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본과 동일하거나 가장 가까운 지역을 선택합니다. |
    | 리소스 유형 | **Microsoft.Storage/storageAccounts** 를 선택합니다. |
    | 리소스 | 사용자의 스토리지 계정을 선택합니다. |
    | 대상 하위 리소스 | *Blob* 또는 *웹* 을 선택할 수 있습니다. |
    | 메시지 요청 | 메시지를 사용자 지정하거나 기본값을 선택합니다. |

1. 그런 다음, **추가** 를 선택하여 구성을 저장합니다.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>스토리지 계정에서 프라이빗 엔드포인트 연결 승인

1. 마지막 섹션에서 Private Link를 구성한 스토리지 계정으로 이동합니다. **설정** 에서 **네트워킹** 을 선택합니다.

1. **네트워킹** 에서 **프라이빗 엔드포인트 연결** 을 선택합니다. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="웹앱의 네트워킹 설정 스크린샷":::

1. Azure Front Door 프리미엄에서 *보류 중* 인 프라이빗 엔드포인트 요청을 선택한 다음, **승인** 을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="보류 중인 스토리지 프라이빗 엔드포인트 요청의 스크린샷":::

1. 승인되면 아래 스크린샷과 같이 표시됩니다. 연결이 완전히 설정되는 데 몇 분 정도 걸릴 수 있습니다. 이제 Azure Front Door 프리미엄에서 스토리지 계정에 액세스할 수 있습니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="승인된 스토리지 엔드포인트 요청의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[스토리지 계정을 사용한 Private Link 서비스](../../storage/common/storage-private-endpoints.md)에 대해 알아봅니다.
