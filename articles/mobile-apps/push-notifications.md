---
title: Visual Studio App Center 및 Azure Notification Hubs를 사용 하 여 모바일 앱에서 푸시 알림의 중요도
description: 모바일 응용 프로그램 사용자와 연계 하는 데 사용할 수 있는 Visual Studio App Center와 같은 서비스에 대해 알아봅니다.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 1717bf6da849218f60bae7dafca20cb304286976
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453140"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>푸시 알림을 보내 응용 프로그램 사용자 참여

소비자 또는 엔터프라이즈 응용 프로그램을 작성 하 고 있는지 여부에 관계 없이 사용자가 응용 프로그램을 적극적으로 사용할 수 있습니다. 최신 뉴스, 게임 업데이트, 흥미로운 제안, 제품 업데이트 또는 기타 관련 정보를 사용자와 공유 하려는 경우가 종종 있습니다. 사용자의 참여를 높이고 응용 프로그램으로 돌아가려면 사용자와 실시간으로 통신 하는 방법이 필요 합니다.

푸시 알림은 응용 프로그램 사용자와 빠르고 효율적으로 통신할 수 있는 방법을 제공 합니다. 사용자는 적절 한 시간에 사용자에 게 연락 하 여 수행 하는 작업에 관계 없이 일반적으로 모바일 장치의 팝업 항목 또는 대화 상자에서 원하는 정보를 알릴 수 있습니다.

## <a name="value-of-push-notifications"></a>푸시 알림 값
푸시 알림은 사용자 및 비즈니스에 가치를 제공 합니다.

기업은 다음과 같은 작업을 수행할 수 있습니다.
- 적절 한 시간에 지원 되는 플랫폼에서 메시지를 전송 하 여 사용자와 직접 통신 합니다.
- 사용자에 게 실시간 업데이트 및 미리 알림을 전송 하 여 사용자 참여를 향상 시켜 정기적으로 응용 프로그램에 참여 하도록 장려 합니다.
- 사용자를 유지 하 고 응용 프로그램을 다운로드 하 고 다시 활성화 하는 데 사용 하지 않는 비활성 사용자를 다시 참여 시킵니다.
- 사용자 동작을 분석 하 고, 사용자를 분할 하 고, 모바일 푸시 알림에 따라 캠페인을 활용 하 여 변환 비율을 늘립니다.
- 사용자에 게 푸시 메시지와 시기 적절 한 업데이트를 전송 하 여 제품 및 서비스를 승격 합니다.
- 개인 설정 된 푸시 메시지를 전송 하 여 사용자를 대상으로 합니다.

응용 프로그램 사용자의 경우 푸시 알림:
- 실시간으로 값과 정보를 제공 합니다.
- 응용 프로그램을 사용 하도록 사용자에 게 알립니다.

다음 서비스를 사용 하 여 모바일 앱에서 푸시 알림을 사용 하도록 설정 합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
푸시 알림 서비스 (PNS)를 사용 하 여 장치에 알림을 보내는 프로세스를 관리 하지 않고도 [App Center 푸시](/appcenter/push/)를 사용 하 여 대상 메시지를 IOS, Android 및 Windows 사용자에 게 보낼 수 있습니다. Azure Notification Hubs을 기반으로 구축 된이 서비스는 강력한 대시보드를 제공 하 여 알림을 수동으로 푸시하는 것과 관련 된 복잡성을 제거 합니다.

**주요 기능**
- 다양 한 플랫폼에서 모바일 장치에 푸시 알림을 보냅니다.
- 알림을 사용 하 여 응용 프로그램에 데이터를 전송 하거나, 사용자에 게 메시지를 표시 하거나, 응용 프로그램에서 작업을 트리거합니다.
- 알림 대상을 사용 하 여 다음을 수행 합니다. 
    - 등록 된 모든 장치에 메시지를 브로드캐스트합니다.
    - 장치 정보 및 사용자 지정 속성에 따라 대상 그룹에 알림을 보냅니다.
    - 특정 사용자에 게 알림을 보냅니다.
    - 특정 장치에 알림을 보냅니다.
- App Center 포털에서 제공 되는 푸시, 장치 및 오류에 대 한 다양 한 원격 분석을 활용 하세요.
- IOS, Android, macOS, Xamarin, 기본, Unity 및 Cordova에 대 한 플랫폼 지원을 얻습니다.

**참조**
- [Visual Studio App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 푸시 시작](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) 는 사용 하기 쉽고 확장 된 푸시 엔진을 제공 합니다. 이를 사용 하 여 모든 플랫폼 및 클라우드 또는 온-프레미스의 모든 백 엔드에서 알림을 보낼 수 있습니다.

**주요 기능**
- 클라우드 또는 온-프레미스의 모든 백 엔드에서 모든 플랫폼에 푸시 알림을 보냅니다.
- 단일 API 호출을 사용 하 여 수백만 개의 모바일 장치로 빠른 브로드캐스트 푸시
- 고객, 언어 및 위치에 따라 푸시 알림을 조정 합니다.
- 고객 세그먼트를 동적으로 정의 하 고 알립니다.
- 수백만 개의 모바일 장치로 즉시 확장할 수 있습니다.
- IOS, Android, Windows, Kindle 및 Baidu에 대 한 플랫폼 지원을 얻습니다.
        
**참조**
- [Azure Portal](https://portal.azure.com) 
- [Azure Notification Hubs 시작](/azure/notification-hubs/)
- [빠른 시작](/azure/notification-hubs/create-notification-hub-portal)
- [샘플](/azure/notification-hubs/samples)
