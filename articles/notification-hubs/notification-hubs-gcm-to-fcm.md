---
title: Azure Notification Hubs 및 Google FCM(Firebase Cloud Messaging) 마이그레이션
description: Azure Notification Hubs에서 Google GCM에서 FCM으로의 마이그레이션을 다루는 방법을 설명합니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80127032"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure Notification Hubs 및 Google Firebase Cloud Messaging 마이그레이션

## <a name="current-state"></a>현재 상태

Google에서 GCM(Google Cloud Messaging)에서 FCM(Firebase Cloud Messaging)으로의 마이그레이션을 발표했을 때 이와 같은 푸시 서비스는 변경 내용을 수용하기 위해 Android 디바이스에 알림을 보내는 방식을 조정해야 했습니다.

서비스 백 엔드를 업데이트한 다음 필요에 따라 API 및 SDK에 대한 업데이트를 게시했습니다. 이 구현에서는 고객 영향을 최소화하기 위해 기존 GCM 알림 스키마와의 호환성을 유지하기로 했습니다. 이는 현재 FCM 레거시 모드에서 FCM를 사용하여 Android 디바이스로 알림을 보내는 것을 의미합니다. 궁극적으로 새로운 기능 및 페이로드 형식을 포함하여 FCM에 대한 진정한 지원을 추가하려고 합니다. 이는 장기적인 변화이며 현재 마이그레이션은 기존 애플리케이션 및 SDK와의 호환성을 유지하는 데 중점을 두었습니다. 앱에서 GCM 또는 FCM SDK(Microsoft의 SDK 포함)를 사용할 수 있으며 알림이 올바르게 전송되는지 확인합니다.

일부 고객은 최근에 알림에 대한 GCM 엔드포인트를 사용한 앱에 대한 Google 경고에서 메일을 받았습니다. 이것은 경고일 뿐이며, 아무것도 중단되지 않습니다. 앱의 Android 알림은 처리를 위해 Google에 전송되고 Google은 이를 계속 처리합니다. 서비스 구성에서 GCM 엔드포인트를 명시적으로 지정한 일부 고객은 여전히 사용되지 않는 엔드포인트를 사용하고 있습니다. 해당 격차를 이미 식별했고 Google에서 메일을 보낼 때 문제를 해결하기 위해 노력했습니다.

사용되지 않는 엔드포인트를 바꾸고 픽스를 배포했습니다.

## <a name="going-forward"></a>앞으로 이동

Google의 FCM FAQ에서는 사용자가 아무것도 하지 않아도 된다고 말합니다. [FCM FAQ](https://developers.google.com/cloud-messaging/faq)에서 Google은 다음과 같이 말했습니다. “클라이언트 SDK 및 GCM 토큰은 계속 무기한으로 작동합니다. 그러나 FCM으로 마이그레이션해야 하는 경우를 제외하고 Android 앱에서 최신 버전의 Google Play 서비스를 대상으로 지정할 수 없습니다.”

앱에서 GCM 라이브러리를 사용하는 경우 계속해서 Google 지침에 따라 앱의 FCM 라이브러리로 업그레이드합니다. 해당 SDK는 둘 다와 호환되므로 앱에서 모든 항목을 업데이트할 필요가 없습니다(SDK 버전을 최신 상태로 유지하는 경우).

## <a name="questions-and-answers"></a>질문 및 답변

다음은 고객의 몇 가지 일반적인 질문에 대한 답변입니다.

**Q:** 컷오프 날짜까지 호환되도록 하려면 무엇을 해야 하나요? (Google의 현재 컷오프 날짜는 5월 29일이며 변경될 수 있음)

**A:** 아무것도 하지 않아도 됩니다. 기존 GCM 알림 스키마와의 호환성이 유지됩니다. GCM 키는 애플리케이션에서 사용하는 GCM SDK 및 라이브러리와 마찬가지로 정상적으로 계속 작동합니다.

새 기능을 활용하기 위해 FCM SDK 및 라이브러리로 업그레이드하기로 한 경우 GCM 키는 계속 작동합니다. 원하는 경우 FCM 키를 사용하도록 전환할 수 있지만 새 Firebase 프로젝트를 만들 때 기존 GCM 프로젝트에 Firebase를 추가해야 합니다. 이렇게 하면 아직 GCM SDK 및 라이브러리를 사용하는 이전 버전의 앱을 실행 중인 고객과의 호환성이 보장됩니다.

새 FCM 프로젝트를 만드는 중이고 기존 GCM 프로젝트에 연결되지 않은 경우 새 FCM 비밀을 사용하여 Notification Hubs를 업데이트하면 새 FCM 키에 이전 GCM 프로젝트에 대한 연결이 없으므로 현재 앱 설치에 알림을 푸시할 수 있는 기능이 사라집니다.

**Q:** 이전 GCM 엔드포인트가 사용되고 있다는 메일을 받는 이유는 무엇인가요? 무엇을 해야 하나요?

**A:** 아무것도 하지 않아도 됩니다. 새 엔드포인트로 마이그레이션했으며 곧 완료될 예정이므로 변경할 필요가 없습니다. 아무것도 중단되지 않으며, 한 번의 누락된 엔드포인트로 인해 단지 Google에서 경고 메시지를 보냈습니다.

**Q:** 기존 사용자를 중단하지 않고 새로운 FCM SDK 및 라이브러리로 전환하려면 어떻게 해야 하나요?

A: 언제든지 업그레이드합니다. Google은 아직 기존 GCM SDK 및 라이브러리의 사용 중단을 발표하지 않았습니다. 기존 사용자에게 푸시 알림이 중단되지 않도록 하려면 기존 GCM 프로젝트와 연결하는 새 Firebase 프로젝트를 만들어야 합니다. 이렇게 하면 새 Firebase 비밀이 GCM SDK 및 라이브러리를 사용하여 앱의 이전 버전을 실행하는 사용자와 FCM SDK 및 라이브러리를 사용하는 앱의 새로운 사용자에게 적용됩니다.

**Q:** 언제 내 알림에 대한 새로운 FCM 기능 및 스키마를 사용할 수 있나요?

**A:** API 및 SDK에 대한 업데이트를 게시하면 향후 몇 개월 내에 제공할 수 있을 것으로 예상합니다.
