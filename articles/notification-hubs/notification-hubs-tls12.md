---
title: TLS 업데이트 Notification Hubs
description: Azure Notification Hubs에서 TLS에 대한 지원에 대해 알아봅니다.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084248"
---
# <a name="transport-layer-security-tls"></a>TLS(전송 계층 보안)

더 높은 수준의 보안을 보장하기 위해 Notification Hubs는 **2020년 12월 31일** 에 TLS 버전 1.0 및 1.1에 대한 지원을 사용하지 않도록 설정합니다 (2020년 4월 30일부터 연장됨). 이러한 오래된 프로토콜은 약한 암호화를 제공하며, BEAST 및 POODLE 공격에 취약합니다. Android 버전 5 이상 또는 iOS 버전 5 이상을 실행하는 디바이스에 배포된 애플리케이션은 이러한 변경의 영향을 받지 않고, 이러한 운영 체제는 TLS 1.2를 지원하고 클라이언트와 서버는 연결 시 상호 지원되는 가장 높은 버전의 프로토콜을 협상합니다.

Azure Notification Hubs를 사용하는 모든 애플리케이션을 검토하여 TLS 1.2를 지원하는 가장 적절한 라이브러리 및 TLS 스택을 사용하는지 확인하는 것이 좋습니다.

## <a name="update-apps"></a>앱 업데이트

iOS 앱이 App Transport Security(ATS)라고 하는 Apple 네트워킹 보안 기능을 사용하여 TLS 1.2를 사용하는지 확인할 수 있습니다. ATS는 iOS 9.0 또는 macOS 10.11 이전 버전의 SDK에 사용할 수 없으며, [Apple 설명서](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)에서 자세히 읽을 수 있습니다.

SSLSocket 인스턴스를 사용하는 Android 애플리케이션의 경우 [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))에 명시된 대로 각 SSLSocket 인스턴스에서 사용하도록 설정된 프로토콜을 설정합니다.

[TLS 프로토콜 호환성](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) 지원 페이지의 표를 통해 운영 체제를 호환되는 TLS 버전에 매핑할 수 있습니다.

자세한 내용은 [Windows의 TLS 프로토콜에 대한 지원](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows) 개요를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Notification Hubs 개요](notification-hubs-push-notification-overview.md)
