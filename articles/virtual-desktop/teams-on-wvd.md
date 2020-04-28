---
title: Windows 가상 데스크톱의 Microsoft 팀-Azure
description: Windows 가상 데스크톱에서 Microsoft 팀을 사용 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187531"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Windows 가상 데스크톱에서 Microsoft 팀 사용

> 적용 대상: Windows 10 및 Windows 10 IoT Enterprise

가상화 된 환경에는 대기 시간 증가, 높은 호스트 CPU 사용량, 전체 오디오 및 비디오 성능 저하를 비롯 하 여 Microsoft 팀과 같은 공동 작업 앱에 대 한 고유한 문제 집합이 제공 됩니다. VDI 환경에서 Microsoft 팀을 사용 하는 방법에 대 한 자세한 내용은 [가상화 된 데스크톱 인프라를 위한 팀](https://docs.microsoft.com/microsoftteams/teams-for-vdi)을 확인 하세요.

## <a name="prerequisites"></a>사전 요구 사항

Windows 가상 데스크톱에서 Microsoft 팀을 사용 하려면 먼저 다음 작업을 수행 해야 합니다.

- Microsoft 팀의 [하드웨어 요구 사항을](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)충족 하는 windows 10 장치에 [windows 데스크톱 클라이언트](connect-windows-7-and-10.md) 를 설치 합니다.
- Windows 10 다중 세션 또는 Windows 10 Enterprise VM (가상 머신)에 연결 합니다.
- Microsoft 팀을 위해 [네트워크를 준비](https://docs.microsoft.com/microsoftteams/prepare-network) 합니다.

## <a name="use-unoptimized-microsoft-teams"></a>최적화 되지 않은 Microsoft 팀 사용

Windows 가상 데스크톱 환경에서 Microsoft 팀을 사용 하 여 Microsoft 팀의 채팅 및 공동 작업 기능을 활용할 수 있습니다. Windows 가상 데스크톱은 VDI audio/video (AV) 최적화의 팀을 지원 하지 않습니다. 호출 및 모임은 지원 되지 않습니다. 조직의 정책에서 허용 하는 경우에도 오디오 통화를 수행 하 고 오디오를 사용 하 여 모임에 참여할 수 있지만 최적화 되지 않은 오디오 품질은 호스트 구성에 따라 달라 지 며 신뢰할 수 없습니다. 자세한 내용은 [VDI 성능 고려 사항 팀](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations)을 확인 하세요.

### <a name="prepare-your-image-for-teams"></a>팀을 위한 이미지 준비

팀 당 컴퓨터 설치를 사용 하도록 설정 하려면 호스트에서 다음 레지스트리 키를 설정 합니다.

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Microsoft 팀 설치

컴퓨터 단위 설치를 사용 하 여 팀 데스크톱 앱을 배포할 수 있습니다. Windows 가상 데스크톱 환경에 Microsoft 팀을 설치 하려면 다음을 수행 합니다.

1. 환경에 맞는 [팀 MSI 패키지](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) 를 다운로드 합니다. 64 비트 운영 체제에서 64 비트 설치 관리자를 사용 하는 것이 좋습니다.
2. 이 명령을 실행 하 여 호스트 VM에 MSI를 설치 합니다.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      그러면 프로그램 파일이 나 프로그램 파일 (x86)에 팀이 설치 됩니다. 다음 번에 로그인 하 여 팀을 시작 하면 앱에서 자격 증명을 요청 합니다.

      > [!NOTE]
      > 사용자 및 관리자는 현재 로그인 하는 동안 팀에 대해 자동 실행을 사용 하지 않도록 설정할 수 없습니다.

      호스트 VM에서 MSI를 제거 하려면 다음 명령을 실행 합니다.

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > MSI 설정 ALLUSER = 1을 사용 하 여 팀을 설치 하는 경우 자동 업데이트를 사용할 수 없습니다. 한 달에 한 번 이상 팀을 업데이트 하는 것이 좋습니다. 팀 데스크톱 앱을 배포 하는 방법에 대해 자세히 알아보려면 [팀 데스크톱 앱을 VM에 배포](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)를 확인 하세요.

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정
다중 모니터 환경과 같은 RDP (호스트 풀의 원격 데스크톱 프로토콜) 속성을 사용자 지정 하 여 마이크와 오디오 리디렉션을 사용 하면 요구 사항에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다. **Set RdsHostPool** cmdlet의 **-customrdpproperty** 매개 변수를 사용 하 여 WINDOWS 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다.
지원 되는 속성 및 해당 기본값의 전체 목록은 [지원 되는 RDP 파일 설정](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) 을 참조 하세요.
