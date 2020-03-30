---
title: Azure 알림 허브 및 Google Firebase 클라우드 메시징(FCM) 마이그레이션
description: Azure 알림 허브가 Google GCM에서 FCM 마이그레이션으로 해결하는 방법을 설명합니다.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127032"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure 알림 허브 및 Google 파이어베이스 클라우드 메시징 마이그레이션

## <a name="current-state"></a>현재 상태

구글이 구글 클라우드 메시징 (GCM)에서 파이어 베이스 클라우드 메시징 (FCM)으로의 마이그레이션을 발표했을 때, 우리와 같은 푸시 서비스는 변경에 맞게 Android 장치에 알림을 보내는 방법을 조정해야했습니다.

서비스 백엔드를 업데이트한 다음 필요에 따라 API 및 SDK에 대한 업데이트를 게시했습니다. 구현을 통해 기존 GCM 알림 스키마와의 호환성을 유지하여 고객의 영향을 최소화하기로 결정했습니다. 즉, 현재 FCM 레거시 모드에서 FCM을 사용하여 Android 기기에 알림을 보냅니다. 궁극적으로 새로운 기능과 페이로드 형식을 포함하여 FCM에 대한 진정한 지원을 추가하고 싶습니다. 이는 장기적인 변화이며 현재 마이그레이션은 기존 응용 프로그램 및 SDK와의 호환성을 유지하는 데 중점을 두는 것입니다. 앱에서 GCM 또는 FCM SDK를 사용할 수 있으며 SDK와 함께 알림이 올바르게 전송되었는지 확인합니다.

일부 고객은 최근 GCM 엔드포인트를 사용하여 알림을 사용하는 앱에 대한 Google 경고 이메일을 받았습니다. 이것은 단지 경고, 그리고 아무것도 깨진-응용 프로그램의 안 드 로이드 알림 처리를 위해 여전히 Google에 전송 되 고 구글은 여전히 그들을 처리. 서비스 구성에서 GCM 끝점을 명시적으로 지정한 일부 고객은 여전히 더 이상 사용되지 않는 끝점을 사용하고 있었습니다. 우리는 이미이 격차를 식별하고 구글이 이메일을 보낼 때 문제를 해결하기 위해 노력하고 있었다.

더 이상 사용되지 않는 끝점을 대체하고 수정 프로그램이 배포됩니다.

## <a name="going-forward"></a>앞으로

구글의 FCM FAQ는 아무것도 할 필요가 없다고 말합니다. [FCM FAQ에서](https://developers.google.com/cloud-messaging/faq)구글은 "클라이언트 SDK와 GCM 토큰은 무기한으로 계속 작동할 것입니다. 그러나 FCM으로 마이그레이션하지 않는 한 Android 앱에서 최신 버전의 Google Play 서비스를 타겟팅할 수 없습니다."

앱에서 GCM 라이브러리를 사용하는 경우 Google의 지침에 따라 앱의 FCM 라이브러리로 업그레이드합니다. SDK는 둘 중 하나와 호환되므로 SDK 버전으로 최신 상태인 한 앱에서 업데이트할 필요가 없습니다.

## <a name="questions-and-answers"></a>질문과 대답

다음은 고객으로부터 들었던 일반적인 질문에 대한 몇 가지 답변입니다.

**Q:** 컷오프 날짜(Google의 현재 차단 날짜는 5월 29일이며 변경될 수 있음)까지 호환하려면 어떻게 해야 하나요?

**A:** 아무것도. 기존 GCM 알림 스키마와의 호환성을 유지합니다. GCM 키는 응용 프로그램에서 사용하는 모든 GCM SDK 및 라이브러리와 마찬가지로 정상적으로 계속 작동합니다.

새로운 기능을 활용하기 위해 FCM SDK 및 라이브러리로 업그레이드하기로 결정한 경우 GCM 키는 계속 작동합니다. 원하는 경우 FCM 키를 사용하여 전환할 수 있지만 새 Firebase 프로젝트를 만들 때 기존 GCM 프로젝트에 Firebase를 추가해야 합니다. 이렇게 하면 GCM SDK 및 라이브러리를 여전히 사용하는 이전 버전의 앱을 실행하는 고객과의 이전 버전과의 호환성이 보장됩니다.

새 FCM 프로젝트를 만들고 기존 GCM 프로젝트에 연결하지 않는 경우 새 FCM 키에 이전 GCM에 대한 링크가 없으므로 알림 허브를 새 FCM 비밀로 업데이트하면 현재 앱 설치에 알림을 푸시할 수 없게 됩니다. 프로젝트.

**Q:** 이전 GCM 끝점에 대한 이 전자 메일이 사용되는 이유는 무엇입니까? 어떻게 해야 하나요?

**A:** 아무것도. 새 끝점으로 마이그레이션하였고 곧 완료될 것이므로 변경이 필요하지 않습니다. 아무것도 깨진, 우리의 하나의 놓친 엔드 포인트는 단순히 구글에서 경고 메시지를 발생.

**Q:** 기존 사용자를 중단하지 않고 새 FCM SDK 및 라이브러리로 전환하려면 어떻게 해야 합니까?

A: 언제든지 업그레이드할 수 있습니다. 구글은 아직 기존의 GCM SDK 및 라이브러리의 어떤 감가상각을 발표하지 않았습니다. 기존 사용자에게 푸시 알림을 중단하지 않도록 하려면 기존 GCM 프로젝트와 연관된 새 Firebase 프로젝트를 만들 때 확인해야 합니다. 이렇게 하면 GCM SDK 및 라이브러리를 통해 이전 버전의 앱을 실행하는 사용자와 FCM SDK 및 라이브러리를 통해 앱의 새 사용자에게 새 Firebase 암호가 작동합니다.

**Q:** 알림에 새 FCM 기능 및 스키마를 언제 사용할 수 있습니까?

**A:** API 및 SDK에 대한 업데이트를 게시하면 앞으로 몇 달 안에 뭔가가 있을 것으로 예상됩니다.
