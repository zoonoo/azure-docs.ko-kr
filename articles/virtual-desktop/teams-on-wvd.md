---
title: 윈도우 가상 데스크톱에 마이크로 소프트 팀 - Azure
description: 윈도우 가상 데스크톱에 마이크로 소프트 팀을 사용하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 702df1bfaf0701a40e514f8a47151f96917adf38
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811588"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>윈도우 가상 데스크톱에 마이크로 소프트 팀 사용

> 에 적용: 윈도우 10, 그리고 윈도우 10 IoT 엔터프라이즈

가상화된 환경은 지연 시간 증가, 높은 호스트 CPU 사용량, 전반적인 오디오 및 비디오 성능 저하 를 포함하여 Microsoft Teams와 같은 공동 작업 앱에 고유한 과제를 제시합니다. VDI 환경에서 Microsoft Teams를 사용하는 방법에 대해 자세히 알아보려면 [가상화된 데스크톱 인프라팀을](https://docs.microsoft.com/microsoftteams/teams-for-vdi)확인하십시오.

## <a name="prerequisites"></a>사전 요구 사항

Windows 가상 데스크톱에서 Microsoft 팀을 사용하려면 다음 을 수행해야 합니다.

- [Windows에 Windows 데스크톱 클라이언트를](connect-windows-7-and-10.md) 설치 10 마이크로 소프트 팀 [하드웨어 요구 사항을](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)충족 하는 장치 .
- Windows 10 멀티 세션 또는 Windows 10 엔터프라이즈 가상 시스템(VM)에 연결합니다.
- Microsoft 팀을 위해 [네트워크를 준비합니다.](https://docs.microsoft.com/microsoftteams/prepare-network)

## <a name="use-unoptimized-microsoft-teams"></a>최적화되지 않은 Microsoft 팀 사용

Windows 가상 데스크톱 환경에서 최적화되지 않은 Microsoft 팀을 사용하여 Microsoft Teams의 전체 채팅 및 공동 작업 기능과 오디오 통화를 활용할 수 있습니다. 최적화되지 않은 호출은 호스트 CPU를 더 많이 사용하기 때문에 통화의 오디오 품질은 호스트 구성에 따라 달라집니다.

### <a name="prepare-your-image-for-teams"></a>팀을 위한 이미지 준비

컴퓨터별 팀 설치를 활성화하려면 호스트에서 다음 레지스트리 키를 설정합니다.

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>마이크로소프트 팀 설치

컴퓨터별 설치를 사용하여 Teams 데스크톱 앱을 배포할 수 있습니다. Windows 가상 데스크톱 환경에 Microsoft 팀을 설치하려면 다음을 수행하십시오.

1. 사용자 환경에 맞는 [Teams MSI 패키지를](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) 다운로드하십시오. 64비트 운영 체제에서 64비트 설치 프로그램을 사용하는 것이 좋습니다.
2. 이 명령을 실행하여 호스트 VM에 MSI를 설치합니다.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      이렇게 하면 프로그램 파일 또는 프로그램 파일(x86)에 팀이 설치됩니다. 다음에 로그인하고 Teams를 시작할 때 앱에서 자격 증명을 요청합니다.

      > [!NOTE]
      > 사용자와 관리자는 현재 로그인하는 동안 Teams에 대한 자동 실행을 비활성화할 수 없습니다.

      호스트 VM에서 MSI를 제거하려면 다음 명령을 실행합니다.

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > MSI가 ALLUSERS=1을 설정한 팀을 설치하면 자동 업데이트가 비활성화됩니다. 한 달에 한 번 이상 팀을 업데이트하는 것이 좋습니다.
