---
title: 윈도우 가상 데스크톱 윈도우에 연결 10 또는 7 - Azure
description: Windows 데스크톱 클라이언트를 사용하여 Windows 가상 데스크톱에 연결하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154341"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows Desktop 클라이언트와 연결

> 에 적용: 윈도우 7, 윈도우 10, 그리고 윈도우 10 IoT 엔터프라이즈

Windows 데스크톱 클라이언트를 사용하여 Windows 7, Windows 10 및 Windows 10 IoT 엔터프라이즈가 있는 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다.

> [!IMPORTANT]
> Windows 가상 데스크톱은 RADC(원격 앱 및 데스크톱 연결) 클라이언트 또는 MSTSC(원격 데스크톱 연결) 클라이언트를 지원하지 않습니다.

> [!IMPORTANT]
> Windows 가상 데스크톱은 현재 Windows 스토어에서 원격 데스크톱 클라이언트를 지원하지 않습니다. 이 클라이언트에 대한 지원은 향후 릴리스에서 추가될 예정입니다.

## <a name="install-the-windows-desktop-client"></a>Windows 데스크톱 클라이언트 설치

Windows 버전과 일치하는 클라이언트를 선택합니다.

- [윈도우 64 비트](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32비트](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

관리자 권한이 필요하지 않은 현재 사용자의 클라이언트를 설치하거나, 디바이스의 모든 사용자가 클라이언트에 액세스할 수 있도록 관리자가 클라이언트를 설치 및 구성할 수 있습니다.

설치가 완료되면 **원격 데스크톱**을 검색하여 시작 메뉴에서 클라이언트를 시작할 수 있습니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독하여 사용할 수 있는 관리 되는 리소스 목록을 가져옵니다. 구독하면 로컬 PC에서 리소스를 사용할 수 있습니다.

피드를 구독하려면 다음 을 수행하십시오.

1. Windows 데스크톱 클라이언트를 엽니다.
2. 기본 페이지에서 **구독을** 선택하여 서비스에 연결하고 리소스를 검색합니다.
3. 메시지가 표시되면 사용자 계정으로 로그인합니다.

성공적으로 로그인하면 액세스할 수 있는 리소스 목록이 표시됩니다.

두 가지 방법 중 하나로 리소스를 시작할 수 있습니다.

- 클라이언트의 기본 페이지에서 리소스를 두 번 클릭하여 시작합니다.
- 일반적으로 시작 메뉴에서 다른 앱과 마찬가지로 리소스를 시작합니다.
  - 검색 표시줄에서 앱을 검색할 수도 있습니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동으로 업데이트됩니다. 관리자는 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Windows 데스크톱 클라이언트를 사용하는 방법에 대해 자세히 알아보려면 [Windows 데스크톱 클라이언트로 시작하기](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)를 체크 아웃하십시오.
