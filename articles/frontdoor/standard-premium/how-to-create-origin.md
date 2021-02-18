---
title: Azure Front 도어 표준/프리미엄 (미리 보기) 원본 설정
description: 이 문서에서는 끝점 관리자를 사용 하 여 원본을 구성 하는 방법을 보여 줍니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 47134712bb953d9194bd943e228bae681877dcaa
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099449"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Azure Front 도어 표준/프리미엄 (미리 보기) 원본 설정

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 기존 원본 그룹에서 Azure Front 도어 표준/프리미엄 원본을 만드는 방법을 보여 줍니다. 

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Front 도어 Standard/Premium 원본을 만들려면 먼저 하나 이상의 원본 그룹을 만들어야 합니다.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>새 Azure Front 도어 표준/프리미엄 원본 만들기

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Front 도어 표준/프리미엄 프로필로 이동 합니다.

1. **원본 그룹** 을 선택 합니다. 그런 다음 **+ 추가** 를 선택 하 여 새 원본 그룹을 만듭니다.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="원본 그룹 방문 페이지의 스크린샷":::

1. **원본 그룹 추가** 페이지에서 새 원본 그룹의 고유 **이름을** 입력 합니다.

1. 그런 다음 **+ 원본 추가** 를 선택 하 여이 원본 그룹에 새 원본을 추가 합니다. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="원본 추가 페이지의 스크린샷":::
  
    | 설정 | 값 |
    | --- | --- |
    | 이름 | 새 Azure 전면 도어 원본에 대 한 고유한 이름을 입력 합니다. |   
    | 원본 형식 | 추가 하려는 리소스의 형식입니다. Azure Front 도어 Standard/Premium은 app service, cloud service 또는 storage에서 앱 원본의 자동 검색을 지원 합니다. Azure 또는 비 Azure 백엔드에서 다른 리소스를 원하는 경우 **사용자 지정 호스트** 를 선택 합니다. |
    | 호스트 이름  | 원본 호스트 유형으로 **사용자 지정 호스트** 를 선택 하지 않은 경우 드롭다운에서 원본 호스트 이름을 선택 하 여 백 엔드를 선택 합니다. |
    | 원본 호스트 헤더 | 각 요청에 대 한 백 엔드로 전송 되는 호스트 헤더 값을 입력 합니다. 자세한 내용은 [원본 호스트 헤더](concept-origin.md#hostheader)를 참조 하세요. |
    | HTTP 포트 | 원본에서 HTTP 프로토콜에 대해 지 원하는 포트 값을 입력 합니다. |
    | HTTPS 포트 | 원본에서 HTTPS 프로토콜에 대해 지 원하는 포트 값을 입력 합니다. |
    | 우선 순위 | 모든 트래픽에 대해 기본 서비스 원본을 사용 하려는 경우 다른 원본에 우선 순위를 할당 합니다. 또한 기본 또는 백업 원본을 사용할 수 없는 경우 백업을 제공 합니다. 자세한 내용은 [우선 순위](concept-origin.md#priority)를 참조 하세요. |
    | 무게 | 다른 원본에 가중치를 할당 하 여 원본 집합 전체에 걸쳐 균등 하 게 또는 가중치 계수에 따라 트래픽을 분산 합니다. 자세한 내용은 [가중치](concept-origin.md#weighted)를 참조 하세요. |
    | 상태 | 원본을 사용 하도록 설정 하려면이 옵션을 선택 합니다. |
    | 규칙 | 이 경로에 적용 될 규칙 집합을 선택 합니다. 규칙을 구성 하는 방법에 대 한 자세한 내용은 [Azure 전면 도어에 대 한 규칙 집합 구성](how-to-configure-rule-set.md) 을 참조 하세요. | 

    > [!IMPORTANT]
    > 구성 하는 동안 Api는 프런트 도어 환경에서 원본에 액세스할 수 없는지 확인 하지 않습니다. 전면 도어가 원본에 도달할 수 있는지 확인 합니다.

1. **추가** 를 선택 하 여 새 원본을 만듭니다. 만든 원점이 원본 목록에서 그룹으로 표시 됩니다.
  
    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="원본 추가 페이지의 스크린샷":::

1. **추가** 를 선택 하 여 현재 끝점에 원본 그룹을 추가 합니다. 원본 그룹은 원본 그룹 패널에 표시 됩니다.

## <a name="clean-up-resources"></a>리소스 정리
원본 그룹을 더 이상 필요 하지 않은 경우 삭제 **하려면 ...을** 클릭 한 다음 드롭다운에서 **삭제** 를 선택 합니다.

:::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="원본 그룹을 삭제 하는 방법의 스크린샷":::

더 이상 필요 하지 않은 원본을 삭제 **하려면 ...을** 클릭 한 다음 드롭다운에서 **삭제** 를 선택 합니다. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="원본을 삭제 하는 방법의 스크린샷":::

## <a name="next-steps"></a>다음 단계

사용자 지정 도메인에 대 한 자세한 내용은 Azure Front 도어 표준/프리미엄 끝점에 [사용자 지정 도메인 추가](how-to-add-custom-domain.md) 를 참조 하세요.
