---
title: Azure Web PubSub 인스턴스를 만드는 방법
description: Azure Web PubSub 인스턴스를 만드는 옵션 및 만드는 방법에 관한 개요
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/17/2021
ms.openlocfilehash: f3ed823d989abcb32ad7680e902fc5d52200e726
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951033"
---
# <a name="how-to-create-azure-web-pubsub-instance"></a>Azure Web PubSub 인스턴스를 만드는 방법

Azure Web PubSub 서비스를 사용하여 애플리케이션을 빌드하려면 Web PubSub 인스턴스를 만든 다음, 클라이언트와 서버를 연결해야 합니다. 이 방법 가이드에서는 Azure Web PubSub 인스턴스를 만드는 옵션을 보여 줍니다.

## <a name="create-azure-web-pubsub-instance-with-azure-portal"></a>Azure Portal을 사용하여 Azure Web PubSub 인스턴스 만들기 

[Azure Portal](../azure-portal/index.yml)은 명령줄 도구의 대안을 제공하는 웹 기반의 통합 콘솔입니다. Azure Portal을 사용하여 Azure 구독을 관리할 수 있습니다. 간단한 웹앱에서 복잡한 클라우드 배포까지 모든 것을 구축, 관리 및 모니터링합니다. Azure Portal을 사용하여 Azure Web PubSub 서비스 인스턴스를 만들 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 새로 만들기 단추를 선택합니다. 새 화면의 검색 상자에서 *Web PubSub* 를 입력하고 Enter를 누릅니다. (`Web` 범주에서 Azure Web PubSub를 검색할 수도 있습니다.)

:::image type="content" source="media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="포털에서 Azure Web PubSub를 검색하는 스크린샷":::

2. 검색 결과에서 **Web PubSub** 를 선택한 다음, **만들기** 를 선택합니다.

3. 다음 설정을 입력합니다.

    | 설정      | Description                                        |
    | ------------ | -------------------------------------------------- |
    | **리소스 이름** | 새 Web PubSub 서비스 인스턴스를 식별하는 전역적으로 고유한 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 이 새 Web PubSub 서비스 인스턴스가 생성되는 Azure 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  Web PubSub 서비스 인스턴스를 만들 새로운 또는 기존 리소스 그룹의 이름입니다. | 
    | **위치** | 근처 [지역](https://azure.microsoft.com/regions/)을 선택하세요. |
    | **가격 책정 계층** | [Azure Web PubSub 서비스 가격 책정 계층](https://azure.microsoft.com/pricing/details/web-pubsub/)에 관해 자세히 알아봅니다. |
    | **단위 수** |  단위 수는 Web PubSub 서비스 인스턴스가 허용할 수 있는 연결 수를 지정합니다. 각 단위는 최대 1,000개의 동시 연결을 지원합니다. 표준 계층에서만 구성할 수 있습니다. |

:::image type="content" source="media/howto-develop-create-instance/create-web-pubsub-instance-in-portal.png" alt-text="포털에서 Azure Web PubSub 인스턴스를 만드는 스크린샷":::

4. **만들기** 를 선택하여 Web PubSub 서비스 인스턴스 배포를 시작하세요.

## <a name="create-azure-web-pubsub-instance-with-azure-cli"></a>Azure CLI를 사용하여 Azure Web PubSub 인스턴스 만들기

[Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)는 Azure 리소스를 만들고 관리하는 데 사용되는 명령 세트입니다. Azure CLI는 모든 Azure 서비스에서 사용할 수 있으며, Azure를 빠르게 사용할 수 있도록 자동화에 초점을 두고 설계되었습니다. GA 이후 Azure CLI를 사용하여 Azure Web PubSub 서비스 인스턴스를 만들 수도 있습니다.