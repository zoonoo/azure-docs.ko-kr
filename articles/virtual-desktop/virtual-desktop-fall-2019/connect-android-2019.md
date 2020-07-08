---
title: Android에서 Windows 가상 데스크톱에 연결-Azure
description: Android 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 802e957c9473276dfa73a3ffd93fdd7c526dbf26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213247"
---
# <a name="connect-with-the-android-client"></a>Android 클라이언트와 연결

> 적용 대상: Android 4.1 이상, Chromebooks ChromeOS 53 이상

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 2019년 가을 릴리스에 적용됩니다. 2020년 봄 업데이트에 도입된 Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../connect-android.md)를 참조하세요.

>[!NOTE]
> Android 클라이언트에서 Windows 가상 데스크톱 리소스에 액세스 하는 기능은 현재 미리 보기로 제공 됩니다.

다운로드 가능한 클라이언트를 사용 하 여 Android 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. Google Play 스토어를 지 원하는 Chromebook 장치에서 Android 클라이언트를 사용할 수도 있습니다. 이 가이드에서는 Android 클라이언트를 설정 하는 방법을 설명 합니다.

## <a name="install-the-android-client"></a>Android 클라이언트 설치

시작 하려면 Android 장치에 클라이언트를 [다운로드](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) 하 여 설치 합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독 하 여 Android 장치에서 액세스할 수 있는 관리 되는 리소스 목록을 가져옵니다.

피드를 구독하려면 다음을 수행합니다.

1. 연결 센터에서 **+** , **원격 리소스 피드**를 차례로 탭합니다.
2. **피드 url 필드에** 피드 url을 입력 합니다. 피드 URL은 URL 또는 전자 메일 주소일 수 있습니다.
   - URL을 사용 하는 경우 관리자가 제공 하는 URL을 사용 하는 것이 일반적 <https://rdweb.wvd.microsoft.com> 입니다.
   - 이메일을 사용하려면 이메일 주소를 입력하세요. 관리자가 서버를 구성한 경우 클라이언트는 전자 메일 주소와 연결 된 URL을 검색 합니다.
3. **다음**을 탭합니다.
4. 메시지가 표시되면 자격 증명을 입력합니다.
   - **사용자 이름**에 리소스에 액세스할 수 있는 권한을 사용자에 게 부여 합니다.
   - **암호**에는 사용자 이름과 연결 된 암호를 지정 합니다.
   - 관리자가 인증을 구성 하는 경우 추가 요인을 제공 하 라는 메시지가 표시 될 수도 있습니다.

구독 후에는 연결 센터에서 원격 리소스를 표시 해야 합니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동 업데이트됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Android 클라이언트를 사용 하는 방법에 대해 자세히 알아보려면 [android 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)을 참조 하세요.