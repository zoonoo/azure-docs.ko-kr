---
title: 포털을 사용하여 Azure Web PubSub 인스턴스를 만드는 방법
description: 포털을 사용하여 Azure Web PubSub 인스턴스 만들기에 대한 파일 포함
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: e147d6f954009f8ed4267f8b947438e2e60da0b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110487071"
---
## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [https://portal.azure.com/](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

## <a name="create-an-azure-web-pubsub-service-instance"></a>Azure Web PubSub 서비스 인스턴스 만들기

애플리케이션이 Azure의 Web PubSub 서비스 인스턴스에 연결됩니다.

1. Azure Portal의 왼쪽 위에 있는 새로 만들기 단추를 선택합니다. 새 화면의 검색 상자에서 *Web PubSub* 를 입력하고 Enter를 누릅니다. (`Web` 범주에서 Azure Web PubSub를 검색할 수도 있습니다.)

:::image type="content" source="../media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="포털에서 Azure Web PubSub를 검색하는 스크린샷.":::

2. 검색 결과에서 **Web PubSub** 를 선택한 다음, **만들기** 를 선택합니다.

3. 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **리소스 이름** | 전역적으로 고유한 이름 | 새 Web PubSub 서비스 인스턴스를 식별하는 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 사용자의 구독 | 이 새 Web PubSub 서비스 인스턴스가 생성되는 구독입니다. | 
    | **[리소스 그룹](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Web PubSub 서비스 인스턴스를 만들 새 리소스 그룹의 이름입니다. | 
    | **위치** | 미국 서부 | 근처 [지역](https://azure.microsoft.com/regions/)을 선택하세요. |
    | **가격 책정 계층** | 무료 | Azure Web PubSub 서비스를 무료로 사용해 보세요. |
    | **단위 수** |  해당 없음 | 단위 수는 Web PubSub 서비스 인스턴스가 허용할 수 있는 연결 수를 지정합니다. 표준 계층에서만 구성할 수 있습니다. |

4. **만들기** 를 선택하여 Web PubSub 서비스 인스턴스 배포를 시작하세요.
