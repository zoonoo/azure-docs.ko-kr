---
title: Notification Hubs 보안 모델
description: Azure 알림 허브의 보안 모델에 대해 알아봅니다.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263764"
---
# <a name="notification-hubs-security"></a>알림 허브 보안

## <a name="overview"></a>개요

이 항목에서는 Azure Notification Hubs의 보안 모델에 대해 설명합니다.

## <a name="shared-access-signature-security"></a>공유 액세스 서명 보안

알림 허브는 *SAS(공유 액세스 서명)라는* 엔터티 수준 보안 체계를 구현합니다. 각 규칙에는 [보안 클레임의](#security-claims)설명과 같이 이름, 키 값(공유 보안) 및 권한 집합이 포함됩니다. 

허브를 만들 때 두 개의 규칙이 자동으로 만들어집니다: **수신 권한(클라이언트** 앱에서 사용하는 권한)과 **모든** 권한(앱 백엔드에서 사용하는 권한)이 있는 규칙입니다.

- **DefaultListenShared액세스서명**: **Listen** 권한만 부여합니다.
- **DefaultFullSharedAccessSignature**: **수신자 확인,** **관리**및 **권한 보내기** 권한을 부여합니다. 이 정책은 앱 백엔드에서만 사용해야 합니다. 클라이언트 응용 프로그램에서 사용하지 마십시오. **수신자 권한** 만 있는 정책을 사용합니다. 새 SAS 토큰으로 새 사용자 지정 액세스 정책을 만들려면 이 문서의 후반부에서 [액세스 정책에 대한 SAS 토큰을](#sas-tokens-for-access-policies) 참조하십시오.

클라이언트 앱에서 등록 관리를 수행할 때 날씨 업데이트와 같이 알림을 통해 보낸 정보가 중요하지 않으면 알림 허브에 액세스하는 일반적인 방법은 규칙 수신 대기 전용 액세스의 키 값을 클라이언트 앱에 부여하는 것과 규칙 모든 권한의 키 값을 앱 백 엔드에 부여하는 것입니다.

앱은 Windows 스토어 클라이언트 앱에 키 값을 포함해서는 안 됩니다. 대신 시작 시 앱 백 엔드에서 클라이언트 앱을 검색하게 합니다.

**Listen** 액세스가 있는 키를 사용하면 클라이언트 앱이 모든 태그에 등록할 수 있습니다. 앱에서 특정 태그로 등록을 특정 클라이언트로 제한해야 하는 경우(예: 태그가 사용자 아이디를 나타내는 경우) 앱 백엔드는 등록을 수행해야 합니다. 자세한 내용은 [등록 관리를](notification-hubs-push-notification-registration-management.md)참조하십시오. 이러한 방식으로 클라이언트 앱이 Notification Hubs에 직접 액세스할 수 없습니다.

## <a name="security-claims"></a>보안 클레임

다른 엔터티와 마찬가지로 알림 허브 작업은 **수신자 표시,** **보내기**및 **관리의**세 가지 보안 클레임에 대해 허용됩니다.

| 클레임   | 설명                                          | 허용되는 연산 |
| ------- | ---------------------------------------------------- | ------------------ |
| 수신 대기  | 단일 등록 만들기/업데이트, 읽기 및 삭제 | 등록 만들기/업데이트<br><br>등록 읽기<br><br>핸들에 대한 모든 등록 읽기<br><br>등록 삭제 |
| 보내기    | 알림 허브로 메시지 보내기                | 메시지 보내기 |
| 관리  | Notification Hubs의 CRUD(PNS 자격 증명 및 보안 키 업데이트 포함) 및 태그 기준 등록 읽기 |허브 만들기/업데이트/읽기/삭제<br><br>태그별 등록 읽기 |

알림 허브는 허브에서 직접 구성된 공유 키로 생성된 SAS 토큰을 허용합니다.

두 개 이상의 네임스페이스에 알림을 보낼 수 없습니다. 네임스페이스는 알림 허브에 대한 논리적 컨테이너이며 알림 전송에는 관여하지 않습니다.

네임스페이스 수준 작업에 네임스페이스 수준 액세스 정책(자격 증명)을 사용합니다. 예: 허브 목록, 허브 만들기 또는 삭제 등 허브 수준 액세스 정책만 으로 알림을 보낼 수 있습니다.

### <a name="sas-tokens-for-access-policies"></a>액세스 정책에 대한 SAS 토큰

새 보안 클레임을 만들거나 기존 SAS 키를 보려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.
2. **모든 리소스**를 선택합니다.
3. 클레임을 만들려는 알림 허브의 이름을 선택하거나 SAS 키를 봅니다.
4. 왼쪽 메뉴에서 **액세스 정책을**선택합니다.
5. 새 보안 클레임을 만들려면 **새 정책을** 선택합니다. 정책에 이름을 지정하고 부여할 권한을 선택합니다. 그런 다음 **확인을**선택합니다.
6. 새 SAS 키를 포함한 전체 연결 문자열이 액세스 정책 창에 표시됩니다. 나중에 사용할 수 있도록 이 문자열을 클립보드에 복사할 수 있습니다.

특정 정책에서 SAS 키를 추출하려면 원하는 SAS 키가 포함된 정책 옆의 **복사** 단추를 선택합니다. 이 값을 임시 위치에 붙여넣은 다음 연결 문자열의 SAS 키 부분을 복사합니다. 이 예제에서는 **mytestnamespace1이라는**알림 허브 네임스페이스와 **policy2라는**정책을 사용합니다. SAS 키는 **SharedAccessKey에서**지정한 문자열 끝 근처의 값입니다.

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS 키 받기](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>다음 단계

- [알림 허브 개요](notification-hubs-push-notification-overview.md)
