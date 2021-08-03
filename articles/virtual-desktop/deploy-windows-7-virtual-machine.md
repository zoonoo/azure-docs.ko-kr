---
title: Windows 7 가상 머신 Azure Virtual Desktop 배포 - Azure
description: Azure Virtual Desktop에서 Windows 7 가상 머신을 구성하고 배포하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 4e52594578202046d36e2cbd5a727d4973f26f39
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751975"
---
# <a name="deploy-a-windows-7-virtual-machine-on-azure-virtual-desktop"></a>Azure Virtual Desktop에서 Windows 7 가상 머신 배포

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)를 참조하세요.

Azure Virtual Desktop에 Windows 7 VM(가상 머신)을 배포하는 프로세스는 이후 버전의 Windows를 실행하는 VM과 약간 다릅니다. 이 가이드에서는 Windows 7을 배포하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell.md)의 지침에 따라 호스트 풀을 만듭니다. Portal을 사용하는 경우 [Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)의 1~9단계 지침을 따릅니다. 그런 다음, **검토 + 만들기** 를 선택하여 빈 호스트 풀을 만듭니다.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 가상 머신 구성

필수 조건을 완료하면 Azure Virtual Desktop에서 배포용 Windows 7 VM을 구성할 준비가 된 것입니다.

Azure Virtual Desktop에서 Windows 7 VM을 설정하려면

1. Azure Portal에 로그인하고 Windows 7 Enterprise 이미지를 검색하거나 사용자 지정된 Windows 7 Enterprise(x64) 이미지를 업로드합니다.
2. Windows 7 Enterprise를 호스트 운영 체제로 사용하는 하나 이상의 가상 머신을 배포합니다. 가상 머신에서 RDP(원격 데스크톱 프로토콜)를 허용하는지 확인합니다(TCP/3389 포트).
3. RDP를 사용하여 Windows 7 Enterprise 호스트에 연결하고 배포를 구성하는 동안 정의한 자격 증명을 사용하여 인증합니다.
4. "원격 데스크톱 사용자" 그룹에 RDP를 사용하여 호스트에 연결하는 동안 사용했던 계정을 추가합니다. 계정을 추가하지 않으면 Active Directory 도메인에 가입한 후 VM에 연결하지 못할 수 있습니다.
5. VM의 Windows 업데이트로 이동합니다.
6. 중요 범주에 있는 Windows 업데이트를 설치합니다.
7. 선택적 범주(언어 팩 제외)에 있는 모든 Windows 업데이트를 설치합니다. 이 프로세스를 진행하면 이러한 지침을 완료하는 데 필요한 원격 데스크톱 프로토콜 8.0 업데이트([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387))가 설치됩니다.
8. 로컬 그룹 정책 편집기를 열고 **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **원격 세션 환경** 으로 이동합니다.
9. 원격 데스크톱 프로토콜 8.0 정책을 사용하도록 설정합니다.
10. 이 VM을 Active Directory 도메인에 가입합니다.
11. 다음 명령을 실행하여 가상 머신을 다시 시작합니다.

     ```cmd
     shutdown /r /t 0
     ```

12. [여기](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo)에 나오는 지침에 따라 등록 토큰을 가져옵니다.

      - Azure Portal을 사용하려는 경우 VM을 추가하려는 호스트 풀의 개요 페이지로 이동한 후 토큰을 만들 수도 있습니다.

13. [Windows 7용 Azure Virtual Desktop 에이전트를 다운로드](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)합니다.
14. [Windows 7용 Azure Virtual Desktop 에이전트 관리자를 다운로드](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)합니다.
15. Azure Virtual Desktop 에이전트 설치 관리자를 열고 지침을 따릅니다. 요청 메시지가 표시되면 12단계에서 만든 등록 키를 제공합니다.
16. Azure Virtual Desktop 에이전트 관리자를 열고 지침을 따릅니다.
17. 필요에 따라 TCP/3389 포트를 차단하여 VM에 대한 직접 원격 데스크톱 프로토콜 액세스를 제거합니다.
18. 필요에 따라 .NET Framework 버전이 4.7.2 이상인지 확인합니다. 사용자 지정 이미지를 만드는 경우 프레임워크를 업데이트하는 것이 특히 중요합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Virtual Desktop 배포를 사용할 준비가 되었습니다. [Azure Virtual Desktop 클라이언트의 최신 버전을 다운로드](https://aka.ms/wvd/clients/windows)하여 시작하세요.

Azure Virtual Desktop의 Windows 7에 대한 알려진 이슈 및 문제 해결 지침은 [Azure Virtual Desktop의 Windows 7 가상 머신 문제 해결](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)에서 문제 해결 문서를 참조하세요.
