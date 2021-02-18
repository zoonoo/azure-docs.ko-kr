---
title: 개인 링크를 사용 하 여 원본에 Azure Front 도어 프리미엄 연결
description: Azure Portal를 사용 하 여 개인 링크 서비스로 Azure Front 도어 Premium을 원본에 연결 하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099461"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>개인 링크를 사용 하 여 원본에 Azure Front 도어 프리미엄 연결

이 문서에서는 azure 개인 링크 서비스를 사용 하 여 가상 네트워크에서 호스팅되는 응용 프로그램에 연결 하도록 Azure Front 도어 Premium SKU를 구성 하는 방법을 안내 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 원본 웹 서버에 대 한 [개인 링크](../../private-link/create-private-link-service-portal.md) 서비스를 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Azure Front 도어 서비스에서 개인 끝점 사용

이 섹션에서는 azure 개인 링크 서비스를 Azure Front 도어 Premium SKU의 개인 네트워크에서 만든 개인 끝점에 매핑합니다. 

1. Azure Front 도어 Premium 프로필 내의 *설정* 에서 **원본 그룹** 을 선택 합니다.

1. 개인 링크를 사용 하도록 설정 하려는 원본이 포함 된 원본 그룹을 선택 합니다.

1. **+ 원본 추가** 를 선택 하 여 새 원본을 추가 하거나 목록에서 이전에 만든 원본을 선택 합니다. 그런 다음 **개인 링크 서비스를 사용 하도록 설정** 하는 확인란을 선택 합니다.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="원본 추가 페이지에서 개인 링크를 사용 하도록 설정 하는 스크린샷":::

1. **Azure 리소스 선택** 의 경우 **내 디렉터리에서** 를 선택 합니다. Azure Front 도어 Premium을 개인적으로 연결 하려는 리소스를 구성 하려면 다음 설정을 선택 하거나 입력 합니다.
    
    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본에 동일 하거나 가장 가까운 지역을 선택 합니다. |
    | 리소스 유형 | **Microsoft.Network/privateLinkServices** 를 선택합니다. |
    | 리소스 | **myPrivateLinkService** 를 선택합니다. |
    | 대상 하위 리소스 | 이 필드는 비워 둡니다. |
    | 메시지 요청 | 메시지를 사용자 지정 하거나 기본 메시지를 선택 합니다. |

## <a name="next-steps"></a>다음 단계

[Azure 전면 도어 프리미엄 개인 링크](concept-private-link.md)에 대해 알아봅니다.
