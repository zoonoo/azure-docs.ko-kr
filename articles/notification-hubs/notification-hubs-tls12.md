---
title: 알림 허브 TLS 업데이트
description: Azure 알림 허브에서 TLS에 대한 지원에 대해 자세히 알아보세요.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885755"
---
# <a name="transport-layer-security-tls"></a>TLS(전송 계층 보안)

더 높은 수준의 보안을 보장하기 위해 알림 허브는 2020년 5월 31일(2020년 4월 30일부터 연장)에 TLS 버전 1.0 및 1.1에 대한 지원을 비활성화합니다. 이러한 이전 프로토콜은 약한 암호화를 제공하며 BEAST 및 POODLE 공격에 취약합니다. Android 버전 5 이상 또는 iOS 버전 5 이상인 장치에 배포된 응용 프로그램은 이러한 운영 체제가 TLS 1.2를 지원하고 클라이언트와 서버가 연결 시 가장 높은 상호 지원되는 프로토콜 버전을 협상하므로 이러한 변경의 영향을 받지 않습니다.

Azure 알림 허브를 사용하는 모든 응용 프로그램을 검토하여 TLS 1.2를 지원하는 가장 적용 가능한 라이브러리 및 TLS 스택을 사용하는지 확인하는 것이 좋습니다.

## <a name="update-apps"></a>앱 업데이트

atS(앱 전송 보안)라는 Apple의 네트워킹 보안 기능을 사용하여 iOS 앱이 TLS 1.2를 사용하고 있는지 확인할 수 있습니다. ATS는 iOS 9.0 또는 macOS 10.11보다 오래된 SDK에 사용할 수 없으며 [Apple 의 설명서에서](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)자세한 내용을 읽을 수 있습니다.

SSLSocket 인스턴스를 사용하는 Android 응용 프로그램의 경우 [setEnabledProtocols에](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))명시된 대로 각 SSLSocket 인스턴스에서 사용 가능한 프로토콜을 설정합니다.

[TLS 프로토콜 호환성](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) 지원 페이지의 표는 호환되는 TLS 버전으로 운영 체제를 매핑하는 데 도움이 됩니다.

자세한 내용은 [Windows에서 TLS 프로토콜에 대한 지원 개요를](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [알림 허브 개요](notification-hubs-push-notification-overview.md)