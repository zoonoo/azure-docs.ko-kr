---
title: App Service Mobile Apps와 통합
description: Azure Notification Hubs가 Azure App Service Mobile Apps와 어떻게 작동하는지 알아봅니다.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bb39949bc21ece819b7ac6e279390f8bdb0ee00a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157451"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps와 통합

> [!NOTE]
> Microsoft는 최신 OS 릴리스 지원, 버그 수정, 설명서 개선 및 커뮤니티 PR 검토를 포함하여 App Service Mobile Apps를 완벽하게 지원하기 위해 노력하고 있습니다. 제품 팀은 현재 Azure Mobile Apps의 새로운 기능 작업에 투자하지 않습니다. Azure Mobile Apps의 모든 영역에 커뮤니티가 기여한 것에 매우 감사드립니다.

Azure 서비스 전반에서 원활하고 일관적인 사용 환경을 조성하기 위하여 Notification Hubs를 사용한 푸시 알림이 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md)에 기본적으로 지원됩니다. [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md)는 엔터프라이즈 개발자 및 시스템 통합자를 위해 확장성이 크고 전 세계에서 사용 가능한 모바일 애플리케이션 개발 플랫폼을 제공합니다. 이 플랫폼은 모바일 개발자에게 풍부한 기능 집합을 제공합니다.

Mobile Apps 개발자는 다음 워크플로에서 Notification Hubs를 활용할 수 있습니다.

1. 디바이스 PNS 핸들을 검색합니다.
2. 편리한 Mobile Apps 클라이언트 SDK 등록 API를 통해 디바이스를 Notification Hubs에 등록합니다.

    > [!NOTE]
    > Mobile Apps는 등록 시 보안을 목적으로 모든 태그를 제거합니다. 백 엔드에서 Notification Hubs와 직접 작업하여 디바이스와 태그를 연결합니다.

3. Notification Hubs를 통해 앱 백 엔드에서 알림을 보냅니다.

이 통합으로 인해 개발자에게 다음과 같은 편리한 기능이 제공됩니다.

- **Mobile Apps 클라이언트 SDK**:  이러한 다중 플랫폼 SDK는 등록을 위한 간단한 API를 제공하고 모바일 앱과 자동으로 연결되는 알림 허브와 통신합니다. 개발자는 Notification Hubs 자격 증명을 심도있게 분석하고 추가 서비스로 작업할 필요가 없습니다.
  - *사용자에게 푸시*: SDK는 Mobile Apps 인증된 사용자 ID로 지정된 디바이스를 자동으로 태그하여 사용자 시나리오를 푸시할 수 있습니다.
  - *디바이스에 푸시*: SDK는 자동으로 Mobile Apps 설치 ID를 GUID로 사용하여 Notification Hubs로 등록하며 이는 개발자가 여러 서비스 GUID를 유지 관리하는 문제를 해결합니다.
- **설치 모델**: Mobile Apps는 Notification Hubs의 최신 푸시 모델과 함께 사용되어 푸시 알림 서비스에 맞게 조정되어 사용이 편리한 JSON 설치의 디바이스와 연결된 모든 푸시 속성을 나타냅니다.
- **유연성**: 개발자는 통합이 되어 있더라도 Notification Hubs와 직접 작업하도록 언제든지 선택할 수 있습니다.
- **[Azure Portal](https://portal.azure.com)의 통합된 환경**: 푸시는 하나의 기능으로 Mobile Apps에 시각적으로 표현되며 개발자는 Mobile Apps를 통해 연결된 알림 허브와 쉽게 작업할 수 있습니다.
