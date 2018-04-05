---
title: Azure 모바일 계약 내에서 이모지 이모티콘 사용
description: 푸시 알림 내에서 이모지 이모티콘을 사용하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b5b0e7bfe07054d093dc164cb5f72bde4ba28170
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>푸시 알림 내에서 이모지 이모티콘 사용
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

다음과 같은 쉬운 몇 가지 단계를 통해 푸시 알림에 이모지 이모티콘을 포함할 수 있습니다. 

1. 먼저 메시지에서 보낼 이모지를 찾아야 합니다. 장치 제조업체에서 새로 승인한 이모지를 장치 플랫폼에 추가하려면 다소 시간이 걸리므로 선택하는 이모지를 대상 장치에서 지원할지 확인하십시오. 
2. **Windows** 에서 이 [링크](http://apps.timwhitlock.info/emoji/tables/unicode) 를 탐색하고 ‘네이티브' 아이콘을 복사할 수 있습니다.
   
    ![][7] 
3. **Mac**에서 편집 -> 이모지 및 기호 아래의 사전 응용 프로그램에서 이모지를 찾을 수 있습니다.
   
    ![][6] 
4. 이제 Azure Mobile Engagement 포털에서 **Reach** 탭으로 이동합니다. 푸시 알림 유형(공지, 설문 조사 등)을 선택합니다. 이 예제에서는 공지 푸시를 선택합니다.
5. 알림 텍스트에 도달할 때까지 여러 알림 필드를 지정합니다. 이 위치에서 이모지 이모티콘을 추가합니다. 제목, 메시지 또는 두 항목 모두에 이모티콘을 넣을 수 있습니다. 위의 위치에서 찾은 이모지를 끌어서 놓거나 복사해야 합니다. 
   
    ![][1]
6. 알림에 대한 다른 필드를 완료하고 저장합니다. 
7. 테스트를 실행하거나 공지를 활성화할 때 지정된 대로 이모티콘이 있는 알림이 표시됩니다.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

