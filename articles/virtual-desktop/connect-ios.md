---
title: iOS에서 윈도우 가상 데스크톱에 연결 - Azure
description: iOS 클라이언트를 사용하여 Windows 가상 데스크톱에 연결하는 방법.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128261"
---
# <a name="connect-with-the-ios-client"></a>iOS 클라이언트와 연결

> 에 적용: iOS 13.0 이상. 아이폰, 아이 패드, 아이팟 터치와 호환됩니다.

다운로드 가능한 클라이언트를 사용하여 iOS 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 이 가이드에서는 iOS 클라이언트를 설정하는 방법을 설명합니다.

## <a name="install-the-ios-client"></a>iOS 클라이언트 설치

시작하려면 iOS 기기에서 클라이언트를 [다운로드하여](https://aka.ms/rdios) 설치합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독하여 iOS 기기에서 액세스할 수 있는 관리되는 리소스 목록을 가져옵니다.

피드를 구독하려면 다음 을 수행하십시오.

1. 연결 센터에서 을 **+** 탭한 다음 **작업 영역 추가를**탭합니다.
2. 피드 URL을 **피드 URL** 필드에 입력합니다. 피드 URL은 URL 또는 이메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 제공한 URL을 사용합니다. 일반적으로 URL은 <https://rdweb.wvd.microsoft.com>.
   - 이메일을 사용하려면 이메일 주소를 입력합니다. 이렇게 하면 관리자가 서버를 그런 식으로 구성한 경우 클라이언트가 이메일 주소와 연결된 URL을 검색하도록 지시합니다.
3. **다음을**누릅니다.
4. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.
   - **사용자 이름의**경우 리소스에 액세스할 수 있는 권한이 있는 사용자 이름을 지정합니다.
   - **암호의**경우 사용자 이름과 연결된 암호를 지정합니다.
   - 관리자가 이러한 방식으로 인증을 구성한 경우 추가 요소를 제공하라는 메시지가 표시될 수도 있습니다.
5. **저장**을 누릅니다.

이 후에는 연결 센터에 원격 리소스가 표시되어야 합니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동으로 업데이트됩니다. 관리자는 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

iOS 클라이언트를 사용하는 방법에 대해 자세히 알아보려면 [iOS 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) 설명서를 사용하여 시작하기를 확인하십시오.
