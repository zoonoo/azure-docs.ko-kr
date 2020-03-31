---
title: Visual Studio 앱 센터 및 Azure 알림 허브를 통해 모바일 앱에서 푸시 알림의 중요성
description: 모바일 응용 프로그램 사용자와 소통하는 데 사용할 수 있는 Visual Studio 앱 센터와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235340"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>푸시 알림을 전송하여 응용 프로그램 사용자와 소통

소비자 응용 프로그램을 빌드하든 엔터프라이즈 응용 프로그램을 빌드하든 관계없이 사용자가 응용 프로그램을 적극적으로 사용할 수 있도록 해야 합니다. 속보, 게임 업데이트, 흥미진진한 제안, 제품 업데이트 또는 기타 관련 정보를 사용자와 공유하려는 경우가 종종 있습니다. 사용자 참여를 늘리고 사용자가 응용 프로그램으로 돌아오도록 하려면 사용자와 실시간으로 통신할 수 있는 방법이 필요합니다.

푸시 알림은 응용 프로그램 사용자와 빠르고 효율적으로 통신할 수 있는 방법을 제공합니다. 당신은 적시에 사용자에게 도달하고 원하는 정보를 그들에게 알릴 수 있습니다, 일반적으로 팝업 항목이나 모바일 장치의 대화 상자에, 에 관계없이 그들이 무슨 일을하는지.

## <a name="value-of-push-notifications"></a>푸시 알림의 가치
푸시 알림은 사용자와 기업에 가치를 제공합니다.

기업은 다음을 수행할 수 있습니다.
- 지원되는 플랫폼에서 적시에 메시지를 전송하여 사용자와 직접 통신할 수 있습니다.
- 사용자에게 실시간 업데이트 및 미리 알림을 전송하여 사용자 참여를 높이고 정기적으로 응용 프로그램에 참여하도록 장려합니다.
- 사용자를 유지하고 응용 프로그램을 다운로드했지만 다시 활성화하는 데 사용하지 않는 비활성 사용자와 다시 참여합니다.
- 모바일 푸시 알림을 기반으로 사용자 행동을 분석하고, 사용자를 세분화하고, 캠페인을 활용하여 전환율을 높입니다.
- 푸시 메시지와 적시에 업데이트를 사용자에게 전송하여 제품 및 서비스를 홍보합니다.
- 개인 설정된 푸시 메시지를 전송하여 사용자를 타겟팅합니다.

응용 프로그램 사용자의 경우 푸시 알림:
- 가치와 정보를 실시간으로 제공합니다.
- 사용자에게 응용 프로그램을 사용하도록 상기시킵니다.

다음 서비스를 사용하여 모바일 앱에서 푸시 알림을 활성화합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[앱 센터 푸시를](/appcenter/push/)사용하면 PNS(푸시 알림 서비스)를 사용하여 장치에 알림을 보내는 프로세스를 관리할 필요 없이 iOS, Android 및 Windows 사용자에게 대상 메시지를 보낼 수 있습니다. Azure 알림 허브 위에 구축된 이 서비스는 강력한 대시보드를 제공하여 수동으로 알림을 푸시하는 것과 관련된 복잡성을 제거합니다.

**주요 기능**
- 다양한 플랫폼에서 모바일 장치에 푸시 알림을 보냅니다.
- 알림을 사용하여 응용 프로그램에 데이터를 보내거나, 사용자에게 메시지를 표시하거나, 응용 프로그램에서 작업을 트리거합니다.
- 알림 대상을 사용하여 다음을 수행합니다. 
    - 등록된 모든 장치에 메시지를 브로드캐스트합니다.
    - 기기 정보 및 사용자 지정 속성을 기반으로 잠재고객에게 알림을 보냅니다.
    - 특정 사용자에게 알림을 보냅니다.
    - 특정 장치에 알림을 보냅니다.
- App Center 포털에서 사용할 수 있는 푸시, 장치 및 오류에 대한 풍부한 원격 분석을 사용합니다.
- iOS, 안드로이드, 맥OS, 자마린, 리폰 네이티브, 유니티, 코르도바에 대한 플랫폼 지원을 확보.

**참조**
- [비주얼 스튜디오 앱 센터에 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [앱 센터 푸시 로 시작하기](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[알림 허브는](/azure/notification-hubs/notification-hubs-push-notification-overview) 사용하기 쉽고 확장된 푸시 엔진을 제공합니다. 클라우드 또는 온-프레미스의 모든 플랫폼과 백 엔드에서 알림을 보내는 데 사용할 수 있습니다.

**주요 기능**
- 백 엔드, 클라우드 또는 온-프레미스에서 모든 플랫폼에 푸시 알림을 보냅니다.
- 단일 API 호출로 수백만 개의 모바일 장치에 빠른 브로드캐스트 푸시를 할 수 있습니다.
- 고객, 언어 및 위치별로 푸시 알림을 맞춤설정합니다.
- 고객 세그먼트를 동적으로 정의하고 알립니다.
- 수백만 개의 모바일 장치로 즉시 확장할 수 있습니다.
- iOS, 안드로이드, 윈도우, 킨들, 바이두에 대한 플랫폼 지원을 확보하십시오.
        
**참조**
- [Azure 포털](https://portal.azure.com) 
- [Azure Notification Hubs 시작](/azure/notification-hubs/)
- [빠른 시작](/azure/notification-hubs/create-notification-hub-portal)
- [샘플](/azure/notification-hubs/samples)
