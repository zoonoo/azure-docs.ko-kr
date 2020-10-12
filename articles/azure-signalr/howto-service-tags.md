---
title: 서비스 태그 사용
titleSuffix: Azure SignalR Service
description: 서비스 태그를 사용 하 여 Azure SignalR Service에 대 한 아웃 바운드 트래픽 허용
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84302105"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Azure SignalR Service에 서비스 태그 사용

[네트워크 보안 그룹](../virtual-network/security-overview.md#network-security-groups)을 구성 하는 경우 Azure SignalR service에 대 한 [서비스 태그](../virtual-network/security-overview.md#service-tags) 를 사용할 수 있습니다. IP 주소를 하드 코딩 하지 않고도 Azure SignalR Service 끝점에 대 한 아웃 바운드 네트워크 보안 규칙을 정의할 수 있습니다.

Azure SignalR Service는 이러한 서비스 태그를 관리 합니다. 사용자 고유의 서비스 태그를 만들거나 기존 서비스 태그를 수정할 수 없습니다. Microsoft는 서비스 태그와 일치 하는 이러한 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다.

## <a name="use-service-tag-on-portal"></a>포털에서 서비스 태그 사용

새 아웃 바운드 네트워크 보안 규칙을 추가 하 여 Azure SignalR Service에 대 한 아웃 바운드 트래픽을 허용할 수 있습니다.

1. 네트워크 보안 그룹으로 이동 합니다.

1. **아웃 바운드 보안 규칙**이라고 하는 설정 메뉴를 클릭 합니다.

1. 위쪽의 단추 **+ 추가** 를 클릭 합니다.

1. **대상**아래에서 **서비스 태그** 를 선택 합니다.

1. **대상 서비스 태그**아래에서 **AzureSignalR** 을 선택 합니다.

1. **대상 포트 범위**에 **443** 을 입력 합니다.

    ![아웃 바운드 보안 규칙 만들기](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 필요에 따라 다른 필드를 조정 합니다.

1. **추가**를 클릭합니다.


## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹: 서비스 태그](../virtual-network/security-overview.md#security-rules)
