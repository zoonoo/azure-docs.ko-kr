---
title: macOS에서 윈도우 가상 데스크톱에 연결 - Azure
description: macOS 클라이언트를 사용하여 Windows 가상 데스크톱에 연결하는 방법.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 673c20355e9ad4abf2c8318b3eb2ebbcc36ed8fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128238"
---
# <a name="connect-with-the-macos-client"></a>macOS 클라이언트와 연결

> 적용: macOS 10.12 이상

다운로드 가능한 클라이언트를 사용하여 macOS 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 이 가이드에서는 클라이언트를 설정하는 방법을 설명합니다.

## <a name="install-the-client"></a>클라이언트 설치

시작하려면 macOS 장치에서 클라이언트를 [다운로드하여](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) 설치합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

macOS 기기에서 관리되는 리소스 목록을 가져오기 위해 관리자가 제공한 피드를 구독합니다.

피드를 구독하려면 다음 을 수행하십시오.

1. 기본 페이지에서 **피드 추가를** 선택하여 서비스에 연결하고 리소스를 검색합니다.
2. 피드 URL을 입력합니다. 이는 URL 또는 이메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 제공한 URL을 사용합니다. 일반적으로 URL은 <https://rdweb.wvd.microsoft.com>.
   - 이메일을 사용하려면 이메일 주소를 입력합니다. 이렇게 하면 관리자가 서버를 그런 식으로 구성한 경우 클라이언트가 이메일 주소와 연결된 URL을 검색하도록 지시합니다.
3. **구독을**선택합니다.
4. 메시지가 표시되면 사용자 계정으로 로그인합니다.

로그인한 후에는 사용 가능한 리소스 목록이 표시됩니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동으로 업데이트됩니다. 관리자는 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

macOS 클라이언트에 대해 자세히 알아보려면 [macOS 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) 설명서를 사용하여 시작하기를 확인하십시오.
