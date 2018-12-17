---
title: 포함 파일
description: 포함 파일
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: c95f05127d62d366e1ad78b45df23d04ced0164a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262781"
---
## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service 인스턴스 만들기

응용 프로그램이 Azure의 SignalR Service 인스턴스에 연결됩니다.

1. Azure Portal의 왼쪽 위에 있는 새로 만들기 단추를 선택합니다. 새 화면의 검색 상자에서 *SignalR Service*를 입력한 후 Enter 키를 누릅니다.

    ![SignalR Service 검색](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. 검색 결과에서 **SignalR Service**를 선택한 다음, **만들기**를 선택합니다.

1. 다음 설정을 입력합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **리소스 이름** | 전역적으로 고유한 이름 | 새 SignalR Service 인스턴스를 식별하는 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 사용자의 구독 | 이 새 SignalR Service 인스턴스가 생성되는 구독입니다. | 
    | **[리소스 그룹](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | SignalR Service 인스턴스를 만들 새 리소스 그룹의 이름입니다. | 
    | **위치**: | 미국 서부 | 근처 [지역](https://azure.microsoft.com/regions/)을 선택하세요. |
    | **가격 책정 계층** | 무료 | Azure SignalR Service를 체험해 보세요. |
    | **단위 수** |  해당 없음 | 단위 수는 SignalR Service 인스턴스가 허용할 수 있는 연결 수를 지정합니다. 표준 계층에서만 구성할 수 있습니다. |

    ![SignalR Service 만들기](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. **만들기**를 선택하여 SignalR Service 인스턴스 배포를 시작하세요.