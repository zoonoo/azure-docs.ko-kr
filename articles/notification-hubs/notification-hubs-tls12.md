---
title: TLS 업데이트 Notification Hubs
description: Azure Notification Hubs에서 TLS에 대 한 지원에 대해 알아봅니다.
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
ms.openlocfilehash: a87f3563b995081de8e7cbb4b4499718f77b02ff
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583257"
---
# <a name="transport-layer-security-tls"></a>TLS(전송 계층 보안)

더 높은 수준의 보안을 보장 하기 위해 Notification Hubs은 **2020 년 12 월 31** 일에 TLS 버전 1.0 및 1.1에 대 한 지원을 사용 하지 않도록 설정 합니다 (4 월 30 2020 일에 연장 됨). 이러한 이전 프로토콜은 약한 암호화를 제공 하며, 비스 트 및 POODLE 공격에 취약 합니다. Android 버전 5 이상 또는 iOS 버전 5 이상을 실행 하는 장치에 배포 된 응용 프로그램은 이러한 변경의 영향을 받지 않습니다. 이러한 운영 체제는 TLS 1.2을 지원 하 고 클라이언트와 서버는 연결 시 상호 지원 되는 가장 높은 버전의 프로토콜을 협상 합니다.

Azure Notification Hubs를 사용 하는 모든 응용 프로그램을 검토 하 여 TLS 1.2을 지 원하는 가장 적절 한 라이브러리 및 TLS 스택을 사용 하는지 확인 하는 것이 좋습니다.

## <a name="update-apps"></a>앱 업데이트

IOS 앱이 ATS (App Transport Security) 라고 하는 Apple 네트워킹 보안 기능을 사용 하 여 TLS 1.2을 사용 하는지 확인할 수 있습니다. ATS는 iOS 9.0 또는 macOS 10.11 이전 버전의 Sdk에 사용할 수 없으며 [Apple 설명서](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)에서 자세히 읽을 수 있습니다.

SSLSocket 인스턴스를 사용 하는 Android 응용 프로그램의 경우 [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))에 명시 된 대로 각 sslsocket 인스턴스에서 사용 하도록 설정 된 프로토콜을 설정 합니다.

[Tls 프로토콜 호환성](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) 지원 페이지의 표를 통해 운영 체제를 호환 되는 tls 버전에 매핑할 수 있습니다.

자세한 내용은 [Windows의 TLS 프로토콜에 대 한 지원](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)개요를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Notification Hubs 개요](notification-hubs-push-notification-overview.md)