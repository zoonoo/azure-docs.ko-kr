---
title: 포함 파일
description: 포함 파일
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66128233"
---
1. Azure SignalR Service 리소스를 만들려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다. 페이지의 왼쪽 상단에서 **+ 리소스 만들기**를 선택합니다. **Marketplace 검색** 텍스트 상자에 **SignalR Service**를 입력합니다.

2. 결과에서 **SignalR Service**를 선택하고 **만들기**를 선택합니다.

3. 새 **SignalR** 설정 페이지에서 새 SignalR 리소스에 대한 다음 설정을 추가합니다.

    | Name | 권장되는 값 | 설명 |
    | ---- | ----------------- | ----------- |
    | 리소스 이름 | *testsignalr* | SignalR 리소스에 사용할 고유한 리소스 이름을 입력합니다. 이름은 1~63자의 문자열로, 숫자, 영문자 및 하이픈(`-`) 문자만 포함할 수 있습니다. 이름은 하이픈 문자로 시작하거나 끝날 수 없고 연속되는 하이픈 문자는 유효하지 않습니다.|
    | 구독 | 구독 선택 |  SignalR을 테스트하는 데 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나만 있으면 해당 구독이 자동으로 선택되며 **구독** 드롭다운은 표시되지 않습니다.|
    | 리소스 그룹 | *SignalRTestResources*라는 리소스 그룹 만들기| SignalR 리소스에 대한 리소스 그룹을 선택하거나 만듭니다. 이 그룹은 리소스 그룹을 삭제하여 여러 리소스를 동시에 삭제할 수 있도록 구성하는 데 유용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../articles/azure-resource-manager/resource-group-overview.md)를 참조하세요. |
    | 위치 | *미국 동부* | **위치**를 사용하여 SignalR 리소스가 호스트되는 지리적 위치를 지정합니다. 최상의 성능을 위해 애플리케이션의 다른 구성 요소와 동일한 지역에 리소스를 만드는 것이 좋습니다. |
    | 가격 책정 계층  | *Free* | 현재 **체험** 및 **표준** 옵션을 사용할 수 있습니다. |
    | 대시보드에 고정 | ✔ | 쉽게 찾을 수 있도록 리소스를 대시보드에 고정하려면 이 상자를 선택합니다. |

4. **만들기**를 선택합니다. 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

5. 배포가 완료되면 **설정**에서 **키**를 선택합니다. 기본 키의 연결 문자열을 복사합니다. 나중에 이 문자열을 사용하여 Azure SignalR 서비스 리소스를 사용하도록 앱을 구성합니다.

    연결 문자열은 다음과 같은 양식입니다.
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
