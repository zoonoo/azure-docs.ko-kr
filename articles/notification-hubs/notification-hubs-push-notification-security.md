---
title: Notification Hubs 보안
description: 이 항목에서는 Azure 알림 허브 보안에 대해 설명합니다.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431244"
---
# <a name="notification-hubs-security"></a>Notification Hubs 보안

## <a name="overview"></a>개요

이 항목에서는 Azure Notification Hubs의 보안 모델에 대해 설명합니다.

## <a name="shared-access-signature-security-sas"></a>SAS(공유 액세스 서명) 보안

Notification Hubs는 SAS(공유 액세스 서명)라는 엔터티 수준 보안 체계를 구현합니다. 이 체계를 통해 메시징 엔터티가 해당 엔터티에 대한 권한을 부여하는 설명에서 최대 12개의 권한 부여 규칙을 선언할 수 있습니다.

에 설명 된 대로 각 규칙을 이름, 키 값 (공유 암호) 및 권한 집합이 포함 [보안 클레임](#security-claims)합니다. 알림 허브를 만들 때 두 개의 규칙이 자동으로 만들어집니다: 개 **수신** 권한 (클라이언트 앱을 사용 하 여) 하 고 사용 하 여 하나 **모든** 권한 (앱 백 엔드에서 사용).

클라이언트 앱에서 등록 관리를 수행할 때 날씨 업데이트와 같이 알림을 통해 보낸 정보가 중요하지 않으면 알림 허브에 액세스하는 일반적인 방법은 규칙 수신 대기 전용 액세스의 키 값을 클라이언트 앱에 부여하는 것과 규칙 모든 권한의 키 값을 앱 백 엔드에 부여하는 것입니다.

앱은 Windows 스토어 클라이언트 앱에서 키 값을 포함 되지 않음, 대신가 클라이언트 앱을 시작할 때 앱 백 엔드에서 검색 해야 합니다.

사용 하 여 키 **수신** 액세스 태그에 등록 하려면 클라이언트 앱을 허용 합니다. 앱 등록 (예: 태그가 사용자 Id를 나타내는 경우) 특정 클라이언트에 특정 태그를 제한 해야 하는 경우 앱 백 엔드에서 등록을 수행 해야 합니다. 자세한 내용은 [등록 관리](notification-hubs-push-notification-registration-management.md)합니다. 이러한 방식으로 클라이언트 앱이 Notification Hubs에 직접 액세스할 수 없습니다.

## <a name="security-claims"></a>보안 클레임

다른 엔터티와 마찬가지로 알림 허브 작업에 허용된 3가지 보안 클레임은 **수신 대기**, **보낼**, 및 **관리**합니다.

| 클레임   | 설명                                          | 허용되는 연산 |
| ------- | ---------------------------------------------------- | ------------------ |
| 수신 대기  | 단일 등록 만들기/업데이트, 읽기 및 삭제 | 등록 만들기/업데이트<br><br>등록 읽기<br><br>핸들에 대한 모든 등록 읽기<br><br>등록 삭제 |
| 보내기    | 알림 허브에 메시지 보내기                | 메시지 보내기 |
| 관리  | Notification Hubs의 CRUD(PNS 자격 증명 및 보안 키 업데이트 포함) 및 태그 기준 등록 읽기 |알림 허브 만들기/업데이트/읽기/삭제<br><br>태그별 등록 읽기 |

Notification Hubs 서명 토큰 생성을 허용 알림 허브에서 직접 구성 하는 키를 공유 합니다.

둘 이상의 네임 스페이스에 알림을 보내도록 하는 것이 불가능 합니다. 네임 스페이스에 notification hubs에 대 한 논리적 컨테이너 이며 알림을 보내와 관련 되지 않은 합니다.
네임 스페이스 수준 작업에 대 한 예를 들어 네임 스페이스 수준 액세스 정책 (자격 증명)를 사용할 수 있습니다: 등 notification hubs를 나열, 만들기, notification hubs를 삭제 합니다. 허브 수준 액세스 정책에만 알림을 보낼 수 할 수 있습니다.
