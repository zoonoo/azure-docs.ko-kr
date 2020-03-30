---
title: 안드로이드에서 윈도우 가상 데스크톱에 연결 - Azure
description: 어떻게 안드로이드 클라이언트를 사용하여 윈도우 가상 데스크톱에 연결하는.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295371"
---
# <a name="connect-with-the-android-client"></a>Android 클라이언트와 연결

> 에 적용: 안 드 로이드 4.1 이상, 크롬 OS와 크롬 북 53 이상.

>[!NOTE]
> Android 클라이언트에서 Windows 가상 데스크톱 리소스에 액세스하는 기능은 현재 미리 보기에서 사용할 수 있습니다.

다운로드 가능한 클라이언트를 사용하여 Android 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 또한 구글 플레이 스토어를 지원하는 크롬 북 기기에서 안드로이드 클라이언트를 사용할 수 있습니다. 이 가이드는 안드로이드 클라이언트를 설정하는 방법을 알려줍니다.

## <a name="install-the-android-client"></a>안드로이드 클라이언트 설치

시작하려면 Android 기기에서 클라이언트를 [다운로드하여](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) 설치합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공하는 피드를 구독하여 Android 기기에서 액세스할 수 있는 관리되는 리소스 목록을 가져옵니다.

피드를 구독하려면 다음 을 수행하십시오.

1. 연결 센터에서 을 **+** 탭한 다음 **원격 리소스 피드를 누릅니다.**
2. 피드 URL을 **피드 URL** 필드에 입력합니다. 피드 URL은 URL 또는 이메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 일반적으로 지정한 <https://rdweb.wvd.microsoft.com>URL을 사용합니다.
   - 이메일을 사용하려면 이메일 주소를 입력합니다. 관리자가 서버를 그런 식으로 구성한 경우 클라이언트는 이메일 주소와 연결된 URL을 검색합니다.
3. **다음을**누릅니다.
4. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.
   - **사용자 이름의**경우 리소스에 액세스할 수 있는 권한이 있는 사용자 이름을 지정합니다.
   - **암호의**경우 사용자 이름과 연결된 암호를 지정합니다.
   - 관리자가 이러한 방식으로 인증을 구성한 경우 추가 요소를 제공하라는 메시지가 표시될 수도 있습니다.

구독 후 연결 센터에 원격 리소스가 표시되어야 합니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동으로 업데이트됩니다. 관리자는 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Android 클라이언트를 사용하는 방법에 대해 자세히 알아보려면 [Android 클라이언트로 시작하기를](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)확인하십시오.
