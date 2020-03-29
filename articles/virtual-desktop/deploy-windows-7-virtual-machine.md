---
title: 배포 윈도우 7 가상 머신 윈도우 가상 데스크톱 - Azure
description: Windows 가상 데스크톱에서 Windows 7 가상 컴퓨터를 구성하고 배포하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366686"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Windows Virtual Desktop에서 Windows 7 가상 머신 배포

Windows 가상 데스크톱에 Windows 7 가상 머신(VM)을 배포하는 프로세스는 이후 버전의 Windows를 실행하는 VM과 약간 다릅니다. 이 가이드에서는 Windows 7을 배포하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [PowerShell을 사용하여 호스트 풀 만들기의](create-host-pools-powershell.md) 지침을 따라 호스트 풀을 만듭니다. 그런 다음 [Azure Marketplace의 호스트 풀 만들기의](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) 지침에 따라 하나 이상의 사용자를 데스크톱 응용 프로그램 그룹에 할당합니다.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 가상 컴퓨터 구성

필수 구성 프로그램을 완료하면 Windows 가상 데스크톱에 배포할 Windows 7 VM을 구성할 준비가 된 것입니다.

윈도우 가상 데스크톱에 윈도우 7 VM을 설정하려면 :

1. Azure 포털에 로그인하여 Windows 7 엔터프라이즈 이미지를 검색하거나 사용자 지정된 Windows 7 Enterprise(x64) 이미지를 업로드합니다.  
2. Windows 7 Enterprise를 호스트 운영 체제로 하나 또는 여러 개의 가상 컴퓨터를 배포합니다. 가상 시스템이 RDP(원격 데스크톱 프로토콜)(TCP/3389 포트)를 허용하는지 확인합니다.
3. RDP를 사용하여 Windows 7 엔터프라이즈 호스트에 연결하고 배포를 구성하는 동안 정의한 자격 증명으로 인증합니다. 
4. RDP를 사용하여 호스트에 연결하는 동안 사용한 계정을 "원격 데스크톱 사용자" 그룹에 추가합니다. 이렇게 하지 않으면 Active Directory 도메인에 가입한 후 VM에 연결하지 못할 수 있습니다.
5. VM에서 Windows 업데이트로 이동합니다.
6. 중요 범주에 모든 Windows 업데이트를 설치합니다.
7. 선택적 범주에 모든 Windows 업데이트를 설치합니다(언어 팩 제외). 이렇게 하면 이 지침을 완료하는 데 필요한 원격 데스크톱 프로토콜 8.0[업데이트(KB2592687)가](https://www.microsoft.com/download/details.aspx?id=35387)설치됩니다.
8. 로컬 그룹 정책 편집기를 열고 **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **원격 세션 환경으로**이동합니다.
9. 원격 데스크톱 프로토콜 8.0 정책을 사용하도록 설정합니다.
10. 이 VM을 Active Directory 도메인에 가입합니다.
11. 다음 명령을 실행하여 가상 컴퓨터를 다시 시작합니다.
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. 등록 토큰을 얻으려면 [여기의](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) 지침에 따라.
13. [윈도우 7에 대한 윈도우 가상 데스크톱 에이전트를 다운로드합니다.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)
14. [윈도우 7에 대한 윈도우 가상 데스크톱 에이전트 관리자를 다운로드합니다.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)
15. Windows 가상 데스크톱 에이전트 설치 관리자를 열고 지침을 따릅니다. 메시지가 표시되면 12단계에서 만든 등록 키를 지정합니다.
16. Windows 가상 데스크톱 설치 관리자를 열고 지침을 따릅니다.
17. 선택적으로 TCP/3389 포트를 차단하여 VM에 대한 직접 원격 데스크톱 프로토콜 액세스를 제거합니다.

## <a name="next-steps"></a>다음 단계

이제 Windows 가상 데스크톱 배포를 사용할 준비가 되었습니다. [Windows 가상 데스크톱 클라이언트의 최신 버전을 다운로드하여](https://aka.ms/wvd/clients/windows) 시작하십시오.

알려진 문제 및 Windows 에 대 한 문제 해결 지침 의 목록 7 윈도우 가상 데스크톱에서, Windows 에서 문제 해결 에서 우리의 문제 해결 문서를 참조 [7 윈도우 가상 데스크톱에서 가상 머신](troubleshoot-windows-7-vm.md).
