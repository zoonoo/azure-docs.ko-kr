---
title: Notification Hubs 보안 모델
description: Azure Notification Hubs의 보안 모델에 대해 알아봅니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76263764"
---
# <a name="notification-hubs-security"></a>Notification Hubs 보안

## <a name="overview"></a>개요

이 항목에서는 Azure Notification Hubs의 보안 모델에 대해 설명합니다.

## <a name="shared-access-signature-security"></a>공유 액세스 서명 보안

Notification Hubs는 SAS ( *공유 액세스 서명* ) 라는 엔터티 수준 보안 체계를 구현 합니다. 각 규칙에는 나중에 [보안 클레임](#security-claims)의 설명에 따라 이름, 키 값 (공유 암호) 및 권한 집합이 포함 되어 있습니다. 

허브를 만들 때 두 개의 규칙이 자동으로 생성 됩니다. 하나는 **수신 대기** **권한이 있는 하나** (클라이언트 앱에서 사용)이 고 다른 하나는 앱 백 엔드에서 사용 됩니다.

- **DefaultListenSharedAccessSignature**: **수신** 권한만 부여 합니다.
- **Defaultfullsharedaccesssignature**: **수신 대기**, **관리**및 **보내기** 권한을 부여 합니다. 이 정책은 앱 백 엔드에서만 사용 됩니다. 클라이언트 응용 프로그램에서 사용 하지 마십시오. **수신 대기** 액세스만 있는 정책을 사용 합니다. 새 SAS 토큰을 사용 하 여 새 사용자 지정 액세스 정책을 만들려면이 문서의 뒷부분에 있는 [액세스 정책에 대 한 SAS 토큰](#sas-tokens-for-access-policies) 을 참조 하세요.

클라이언트 앱에서 등록 관리를 수행할 때 날씨 업데이트와 같이 알림을 통해 보낸 정보가 중요하지 않으면 알림 허브에 액세스하는 일반적인 방법은 규칙 수신 대기 전용 액세스의 키 값을 클라이언트 앱에 부여하는 것과 규칙 모든 권한의 키 값을 앱 백 엔드에 부여하는 것입니다.

앱은 Windows 스토어 클라이언트 앱에 키 값을 포함 하면 안 됩니다. 대신 시작 시 클라이언트 앱이 앱 백 엔드에서이를 검색 하도록 합니다.

**수신 대기** 액세스를 사용 하는 키를 사용 하면 클라이언트 앱이 태그를 등록할 수 있습니다. 앱이 특정 클라이언트에 대 한 특정 태그로 등록을 제한 해야 하는 경우 (예: 태그가 사용자 Id를 나타내는 경우) 앱 백 엔드가 등록을 수행 해야 합니다. 자세한 내용은 [등록 관리](notification-hubs-push-notification-registration-management.md)를 참조 하세요. 이러한 방식으로 클라이언트 앱이 Notification Hubs에 직접 액세스할 수 없습니다.

## <a name="security-claims"></a>보안 클레임

다른 엔터티와 마찬가지로 알림 허브 작업은 **수신**, **보내기**및 **관리**의 세 가지 보안 클레임에 대해 허용 됩니다.

| 클레임   | Description                                          | 허용되는 연산 |
| ------- | ---------------------------------------------------- | ------------------ |
| 수신 대기  | 단일 등록 만들기/업데이트, 읽기 및 삭제 | 등록 만들기/업데이트<br><br>등록 읽기<br><br>핸들에 대한 모든 등록 읽기<br><br>등록 삭제 |
| 보내기    | 알림 허브로 메시지 보내기                | 메시지 보내기 |
| 관리  | Notification Hubs의 CRUD(PNS 자격 증명 및 보안 키 업데이트 포함) 및 태그 기준 등록 읽기 |허브 만들기/업데이트/읽기/삭제<br><br>태그별 등록 읽기 |

Notification Hubs는 허브에 직접 구성 된 공유 키로 생성 된 SAS 토큰을 허용 합니다.

둘 이상의 네임 스페이스에 알림을 보낼 수는 없습니다. 네임 스페이스는 Notification Hubs의 논리적 컨테이너 이며 알림을 보내는 작업과 관련이 없습니다.

네임 스페이스 수준 작업에 대 한 네임 스페이스 수준 액세스 정책 (자격 증명)을 사용 합니다. 예: 허브 나열, 허브 만들기 또는 삭제 등 허브 수준 액세스 정책만 알림을 보낼 수 있습니다.

### <a name="sas-tokens-for-access-policies"></a>액세스 정책에 대 한 SAS 토큰

새 보안 클레임을 만들거나 기존 SAS 키를 보려면 다음을 수행 합니다.

1. Azure Portal에 로그인합니다.
2. **모든 리소스**를 선택합니다.
3. 클레임을 만들거나 SAS 키를 보려는 알림 허브의 이름을 선택 합니다.
4. 왼쪽 메뉴에서 **액세스 정책**을 선택 합니다.
5. 새 **정책** 을 선택 하 여 새 보안 클레임을 만듭니다. 정책에 이름을 지정 하 고 부여할 사용 권한을 선택 합니다. 그런 다음, **확인**을 선택합니다.
6. 새 SAS 키를 포함 한 전체 연결 문자열은 액세스 정책 창에 표시 됩니다. 나중에 사용 하기 위해이 문자열을 클립보드로 복사할 수 있습니다.

특정 정책에서 SAS 키를 추출 하려면 원하는 SAS 키가 포함 된 정책 옆의 **복사** 단추를 선택 합니다. 이 값을 임시 위치에 붙여넣은 다음, 연결 문자열의 SAS 키 부분을 복사 합니다. 이 예제에서는 **mytestnamespace1**라는 Notification Hubs 네임 스페이스와 **policy2**이라는 정책을 사용 합니다. SAS 키는 **Sharedaccesskey**에서 지정한 문자열의 끝 부분에 있는 값입니다.

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS 키 가져오기](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>다음 단계

- [Notification Hubs 개요](notification-hubs-push-notification-overview.md)
