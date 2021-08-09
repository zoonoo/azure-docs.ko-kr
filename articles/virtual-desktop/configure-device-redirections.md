---
title: 디바이스 리디렉션 구성 - Azure
description: Azure Virtual Desktop을 위해 디바이스 리디렉션을 구성하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 570983229c16fa9c01d14bd7575d0720a7197191
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756548"
---
# <a name="configure-device-redirections"></a>디바이스 리디렉션 구성

Azure Virtual Desktop 환경을 위해 디바이스 리디렉션을 구성하면 원격 세션에서 프린터, USB 디바이스, 마이크 및 기자 주변 기기를 사용할 수 있습니다. 일부 디바이스 리디렉션의 경우 RDP(원격 데스크톱 프로토콜) 속성과 그룹 정책 설정을 모두 변경해야 합니다.

## <a name="supported-device-redirections"></a>지원되는 디바이스 리디렉션

각 클라이언트는 서로 다른 디바이스 리디렉션을 지원합니다. [클라이언트 비교](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare)에서 각 클라이언트에 대해 지원되는 디바이스 리디렉션의 전체 목록을 확인하세요.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>호스트 풀에 대해 RDP 속성 사용자 지정

PowerShell 또는 Azure Portal을 사용하여 호스트 풀에 대해 RDP 속성을 사용자 지정하는 방법을 자세히 알아보려면 [RDP 속성](customize-rdp-properties.md)을 확인하세요. 지원되는 RDP 속성의 전체 목록은 [지원되는 RDP 파일 설정](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)에서 확인하세요.

## <a name="setup-device-redirections"></a>디바이스 리디렉션 설정

다음 RDP 속성 및 그룹 정책 설정을 사용하여 디바이스 리디렉션을 구성할 수 있습니다.

### <a name="audio-input-microphone-redirection"></a>오디오 입력(마이크) 리디렉션

다음 RDP 속성을 설정하여 오디오 입력 리디렉션을 구성합니다.

- `audiocapturemode:i:1`은 오디오 입력 리디렉션을 사용하도록 설정합니다.
- `audiocapturemode:i:0`은 오디오 입력 리디렉션을 사용하지 않도록 설정합니다.

### <a name="audio-output-speaker-redirection"></a>오디오 출력(스피커) 리디렉션

다음 RDP 속성을 설정하여 오디오 출력 리디렉션을 구성합니다.

- `audiomode:i:0`은 오디오 출력 리디렉션을 사용하도록 설정합니다.
- `audiomode:i:1` 또는 `audiomode:i:2`는 오디오 출력 리디렉션을 사용하지 않도록 설정합니다.

### <a name="camera-redirection"></a>카메라 리디렉션

다음 RDP 속성을 설정하여 카메라 리디렉션을 구성합니다.

- `camerastoredirect:s:*`은 모든 카메라를 리디렉션합니다.
- `camerastoredirect:s:`는 카메라 리디렉션을 사용하지 않도록 설정합니다.

>[!NOTE]
>`camerastoredirect:s:` 속성이 사용하지 않도록 설정된 경우에도 `devicestoredirect:s:` 속성을 통해 로컬 카메라가 리디렉션될 수 있습니다. 카메라 리디렉션을 완전히 사용하지 않도록 설정하려면 `camerastoredirect:s:`를 설정하고 `devicestoredirect:s:`를 설정하거나 카메라를 포함하지 않는 플러그 앤 플레이 디바이스의 하위 집합을 정의합니다.

KSCATEGORY_VIDEO_CAMERA 인터페이스의 세미콜론으로 구분된 목록을 사용하여 특정 카메라를 리디렉션할 수도 있습니다(예: `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi`).

### <a name="clipboard-redirection"></a>클립보드 리디렉션

다음 RDP 속성을 설정하여 클립보드 리디렉션을 구성합니다.

- `redirectclipboard:i:1`은 클립보드 리디렉션을 사용하도록 설정합니다.
- `redirectclipboard:i:0`은 클립보드 리디렉션을 사용하지 않도록 설정합니다.

### <a name="com-port-redirections"></a>COM 포트 리디렉션

다음 RDP 속성을 설정하여 COM 포트 리디렉션을 구성합니다.

- `redirectcomports:i:1`은 COM 포트 리디렉션을 사용하도록 설정합니다.
- `redirectcomports:i:0`은 COM 포트 리디렉션을 사용하지 않도록 설정합니다.

### <a name="usb-redirection"></a>USB 리디렉션

첫째, 다음 RDP 속성을 설정하여 USB 디바이스 리디렉션을 사용하도록 설정합니다.

- `usbdevicestoredirect:s:*`은 USB 디바이스 리디렉션을 사용하도록 설정합니다.
- `usbdevicestoredirect:s:`는 USB 디바이스 리디렉션을 사용하지 않도록 설정합니다.

둘째, 사용자의 로컬 디바이스에서 다음 그룹 정책을 설정합니다.

- **컴퓨터 구성** > **정책**> **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 연결 클라이언트** > **RemoteFX USB 디바이스 리디렉션** 으로 이동합니다.
- **Allows RDP redirection of other supported RemoteFX USB devices from this computer**(이 컴퓨터에서 지원되는 다른 RemoteFX USB 디바이스의 RDP 리디렉션 허용)를 선택합니다.
- **사용** 옵션을 선택하고 **RemoteFX USB 리디렉션 액세스 권한** 의 관리자 및 사용자 확인란을 선택합니다.
- **확인** 을 선택합니다.

### <a name="plug-and-play-device-redirection"></a>플러그 앤 플레이 디바이스 리디렉션

다음 RDP 속성을 설정하여 플러그 앤 플레이 디바이스 리디렉션을 구성합니다.

- `devicestoredirect:s:*`은 모든 플러그 앤 플레이 디바이스의 리디렉션을 사용하도록 설정합니다.
- `devicestoredirect:s:`는 플러그 앤 플레이 디바이스의 리디렉션을 사용하지 않도록 설정합니다.

세미콜론으로 구분된 목록을 사용하여 특정 플러그 앤 플레이 디바이스를 선택할 수도 있습니다(예: `devicestoredirect:s:root\*PNP0F08`).

### <a name="local-drive-redirection"></a>로컬 드라이브 리디렉션

다음 RDP 속성을 설정하여 로컬 드라이브 리디렉션을 구성합니다.

- `drivestoredirect:s:*`은 모든 디스크 드라이브의 리디렉션을 사용하도록 설정합니다.
- `drivestoredirect:s:`는 로컬 드라이브 리디렉션을 사용하지 않도록 설정합니다.

세미콜론으로 구분된 목록을 사용하여 특정 드라이브를 선택할 수도 있습니다(예: `drivestoredirect:s:C:;E:;`).

웹 클라이언트 파일 전송을 사용하도록 설정하려면 `drivestoredirect:s:*`를 설정합니다. 이 RDP 속성에 다른 값을 설정하면 웹 클라이언트 파일 전송이 사용하지 않도록 설정됩니다.

### <a name="printer-redirection"></a>프린터 리디렉션

다음 RDP 속성을 설정하여 프린터 리디렉션을 구성합니다.

- `redirectprinters:i:1`은 프린터 리디렉션을 사용하도록 설정합니다.
- `redirectprinters:i:0`은 프린터 리디렉션을 사용하지 않도록 설정합니다.

### <a name="smart-card-redirection"></a>스마트 카드 리디렉션

다음 RDP 속성을 설정하여 스마트 카드 리디렉션을 구성합니다.

- `redirectsmartcards:i:1`은 스마트 카드 리디렉션을 사용하도록 설정합니다.
- `redirectsmartcards:i:0`은 스마트 카드 리디렉션을 사용하지 않도록 설정합니다.
