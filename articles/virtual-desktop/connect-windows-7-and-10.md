---
title: Windows 가상 데스크톱 Windows 10 또는 7에 연결-Azure
description: Windows 데스크톱 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 745b33efe46c82e3b9358c9c5a2ed13292242db1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997346"
---
# <a name="connect-with-the-windows-desktop-client"></a>Kapcsolódás a Windows asztali ügyféllel

> 적용 대상: Windows 7, Windows 10 및 Windows 10 IoT Enterprise

Windows 데스크톱 클라이언트를 사용 하 여 windows 7, Windows 10 및 Windows 10 IoT Enterprise를 사용 하는 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다.

> [!IMPORTANT]
> Windows 가상 데스크톱은 RemoteApp 및 데스크톱 연결 (RADC) 클라이언트 또는 원격 데스크톱 연결 (MSTSC) 클라이언트를 지원 하지 않습니다.

## <a name="install-the-windows-desktop-client"></a>Windows 데스크톱 클라이언트 설치

Windows 버전과 일치 하는 클라이언트를 선택 합니다.

- [Windows 64 비트](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 비트](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

현재 사용자에 대해 관리자 권한이 필요 하지 않은 클라이언트를 설치 하거나 관리자가 클라이언트를 설치 하 고 구성 하 여 장치의 모든 사용자가 액세스할 수 있도록 할 수 있습니다.

설치가 완료 되 면 **원격 데스크톱**을 검색 하 여 시작 메뉴에서 클라이언트를 시작할 수 있습니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독 하 여 사용할 수 있는 관리 되는 리소스 목록을 가져옵니다. 구독을 통해 로컬 PC에서 리소스를 사용할 수 있습니다.

피드를 구독 하려면 다음을 수행 합니다.

1. Windows 데스크톱 클라이언트를 엽니다.
2. 기본 페이지에서 **구독** 을 선택 하 여 서비스에 연결 하 고 리소스를 검색 합니다.
3. 메시지가 표시 되 면 사용자 계정으로 로그인 합니다.

성공적으로 로그인 하면 액세스할 수 있는 리소스 목록이 표시 됩니다.

두 가지 방법 중 하나를 기준으로 리소스를 시작할 수 있습니다.

- 클라이언트의 기본 페이지에서 리소스를 두 번 클릭 하 여 시작 합니다.
- 시작 메뉴에서 일반적인 방법으로 다른 앱에서 리소스를 시작 합니다.
  - 검색 창에서 앱을 검색할 수도 있습니다.

피드를 구독 하 고 나면 피드의 콘텐츠가 정기적으로 자동 업데이트 됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>Következő lépések

Windows 데스크톱 클라이언트를 사용 하는 방법에 대 한 자세한 내용은 [Windows 데스크톱 클라이언트 시작](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop)을 참조 하세요.
