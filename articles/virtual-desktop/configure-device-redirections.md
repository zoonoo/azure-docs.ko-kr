---
title: 장치 리디렉션 구성-Azure
description: Windows 가상 데스크톱에 대 한 장치 리디렉션을 구성 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 00a3c1d0a2a905e6435b811d5f2611c16a5de502
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328882"
---
# <a name="configure-device-redirections"></a>디바이스 리디렉션 구성

Windows 가상 데스크톱 환경에 대 한 장치 리디렉션을 구성 하면 원격 세션에서 프린터, USB 장치, 마이크 및 기타 주변 장치를 사용할 수 있습니다. 일부 장치 리디렉션은 원격 데스크톱 프로토콜 (RDP) 속성과 그룹 정책 설정을 변경 해야 합니다.

## <a name="supported-device-redirections"></a>지원 되는 장치 리디렉션

각 클라이언트는 서로 다른 장치 리디렉션을 지원 합니다. 클라이언트 [에서](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) 각 클라이언트에 대해 지원 되는 장치 리디렉션의 전체 목록을 확인 하세요.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>호스트 풀의 RDP 속성 사용자 지정

PowerShell 또는 Azure Portal를 사용 하 여 호스트 풀의 RDP 속성을 사용자 지정 하는 방법에 대 한 자세한 내용은 [rdp 속성](customize-rdp-properties.md)을 참조 하세요. 지원 되는 RDP 속성의 전체 목록은 [지원 되는 rdp 파일 설정](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)을 참조 하세요.

## <a name="setup-device-redirections"></a>장치 리디렉션 설정

다음 RDP 속성 및 그룹 정책 설정을 사용 하 여 장치 리디렉션을 구성할 수 있습니다.

### <a name="audio-input-microphone-redirection"></a>마이크 (오디오 입력) 리디렉션

다음 RDP 속성을 설정 하 여 오디오 입력 리디렉션을 구성 합니다.

- `audiocapturemode:i:1` 오디오 입력 리디렉션을 사용 합니다.
- `audiocapturemode:i:0` 오디오 입력 리디렉션을 사용 하지 않도록 설정 합니다.

### <a name="audio-output-speaker-redirection"></a>오디오 출력 (스피커) 리디렉션

다음 RDP 속성을 설정 하 여 오디오 출력 리디렉션을 구성 합니다.

- `audiomode:i:0` 오디오 출력 리디렉션을 사용 합니다.
- `audiomode:i:1` 또는 `audiomode:i:2` 오디오 출력 리디렉션을 사용 하지 않도록 설정 합니다.

### <a name="camera-redirection"></a>카메라 리디렉션

다음 RDP 속성을 설정 하 여 카메라 리디렉션을 구성 합니다.

- `camerastoredirect:s:*` 모든 카메라를 리디렉션합니다.
- `camerastoredirect:s:` 카메라 리디렉션을 사용 하지 않도록 설정 합니다.

>[!NOTE]
>`camerastoredirect:s:`속성이 사용 하지 않도록 설정 된 경우에도 로컬 카메라는 속성을 통해 리디렉션될 수 있습니다 `devicestoredirect:s:` . 카메라 리디렉션을 완전히 사용 하지 않도록 설정 하 `camerastoredirect:s:` 고 `devicestoredirect:s:` 카메라를 포함 하지 않는 플러그 앤 플레이 장치의 일부 하위 집합을 설정 하거나 정의 합니다.

와 같은 KSCATEGORY_VIDEO_CAMERA 인터페이스의 세미콜론으로 구분 된 목록을 사용 하 여 특정 카메라를 리디렉션할 수도 있습니다 `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>클립보드 리디렉션

다음 RDP 속성을 설정 하 여 클립보드 리디렉션을 구성 합니다.

- `redirectclipboard:i:1` 클립보드 리디렉션을 사용 하도록 설정 합니다.
- `redirectclipboard:i:0` 클립보드 리디렉션을 사용 하지 않도록 설정 합니다.

### <a name="com-port-redirections"></a>COM 포트 리디렉션

다음 RDP 속성을 설정 하 여 COM 포트 리디렉션을 구성 합니다.

- `redirectcomports:i:1` COM 포트 리디렉션을 사용 하도록 설정 합니다.
- `redirectcomports:i:0` COM 포트 리디렉션을 사용 하지 않도록 설정 합니다.

### <a name="usb-redirection"></a>USB 리디렉션

먼저, 다음 RDP 속성을 설정 하 여 USB 장치 리디렉션을 사용 하도록 설정 합니다.

- `usbdevicestoredirect:s:*` USB 장치 리디렉션을 사용 하도록 설정 합니다.
- `usbdevicestoredirect:s:` USB 장치 리디렉션을 사용 하지 않도록 설정 합니다.

두 번째로, 사용자의 로컬 장치에서 다음 그룹 정책를 설정 합니다.

- **컴퓨터 구성**  >  **정책** >  **관리 템플릿**  >  **Windows 구성 요소**  >  **원격 데스크톱 서비스**  >  **원격 데스크톱 연결 클라이언트**  >  **RemoteFX USB 장치 리디렉션**으로 이동 합니다.
- **이 컴퓨터에서 지원 되는 다른 REMOTEFX USB 장치의 RDP 리디렉션을 허용**합니다 .를 선택 합니다.
- **사용** 옵션을 선택한 다음, **RemoteFX USB 리디렉션 액세스 권한 상자에서 관리자 및 사용자** 를 선택 합니다.
- **확인**을 선택합니다.

### <a name="plug-and-play-device-redirection"></a>장치 리디렉션 플러그 앤 플레이

다음 RDP 속성을 설정 하 여 플러그 앤 플레이 장치 리디렉션을 구성 합니다.

- `devicestoredirect:s:*` 모든 플러그 앤 플레이 장치를 리디렉션할 수 있습니다.
- `devicestoredirect:s:` 플러그 앤 플레이 장치의 리디렉션을 사용 하지 않도록 설정 합니다.

세미콜론으로 구분 된 목록 (예:)을 사용 하 여 특정 플러그 앤 플레이 장치를 선택할 수도 있습니다 `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>로컬 드라이브 리디렉션

다음 RDP 속성을 설정 하 여 로컬 드라이브 리디렉션을 구성 합니다.

- `drivestoredirect:s:*` 모든 디스크 드라이브를 리디렉션할 수 있습니다.
- `drivestoredirect:s:` 로컬 드라이브 리디렉션을 사용 하지 않도록 설정 합니다.

세미콜론으로 구분 된 목록 (예:)을 사용 하 여 특정 드라이브를 선택할 수도 있습니다 `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>프린터 리디렉션

다음 RDP 속성을 설정 하 여 프린터 리디렉션을 구성 합니다.

- `redirectprinters:i:1` 프린터 리디렉션을 사용 하도록 설정 합니다.
- `redirectprinters:i:0` 프린터 리디렉션을 사용 하지 않도록 설정 합니다.

### <a name="smart-card-redirection"></a>스마트 카드 리디렉션

다음 RDP 속성을 설정 하 여 스마트 카드 리디렉션을 구성 합니다.

- `redirectsmartcards:i:1` 스마트 카드 리디렉션을 사용 하도록 설정 합니다.
- `redirectsmartcards:i:0` 스마트 카드 리디렉션을 사용 하지 않도록 설정 합니다.
