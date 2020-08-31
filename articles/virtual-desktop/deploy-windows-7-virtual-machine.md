---
title: Windows 7 가상 머신 Windows 가상 데스크톱 배포-Azure
description: Windows 가상 데스크톱에서 Windows 7 가상 컴퓨터를 구성 하 고 배포 하는 방법을 설명 합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0dcf21190b52f966dafb9caa9ae28fdf9b99ba86
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007576"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Windows Virtual Desktop에서 Windows 7 가상 머신 배포

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)를 참조하세요.

Windows 가상 데스크톱에 Windows 7 VM (가상 컴퓨터)을 배포 하는 프로세스는 이후 버전의 Windows를 실행 하는 Vm과 약간 다릅니다. 이 가이드에서는 Windows 7을 배포 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 PowerShell을 사용 하 여 [호스트 풀 만들기](create-host-pools-powershell.md) 의 지침에 따라 호스트 풀을 만듭니다. 포털을 사용 하는 경우 [Azure Portal를 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)의 1 ~ 9 단계에 설명 된 지침을 따르세요. **그런 다음 검토 + 만들기** 를 선택 하 여 빈 호스트 풀을 만듭니다.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 가상 머신 구성

필수 구성 요소를 완료 하면 windows 가상 데스크톱에서 배포용 Windows 7 VM을 구성할 준비가 된 것입니다.

Windows 가상 데스크톱에서 Windows 7 VM을 설정 하려면 다음을 수행 합니다.

1. Azure Portal에 로그인 하 고 Windows 7 Enterprise 이미지를 검색 하거나 사용자 지정 된 Windows 7 Enterprise (x64) 이미지를 업로드 합니다.
2. Windows 7 Enterprise를 호스트 운영 체제로 사용 하는 하나 이상의 가상 컴퓨터를 배포 합니다. 가상 컴퓨터에서 RDP (원격 데스크톱 프로토콜)를 허용 하는지 확인 합니다 (TCP/3389 포트).
3. RDP를 사용 하 여 Windows 7 Enterprise 호스트에 연결 하 고 배포를 구성 하는 동안 정의한 자격 증명을 사용 하 여 인증 합니다.
4. "원격 데스크톱 사용자" 그룹에 RDP를 사용 하 여 호스트에 연결 하는 동안 사용한 계정을 추가 합니다. 계정을 추가 하지 않으면 Active Directory 도메인에 가입한 후 VM에 연결 하지 못할 수 있습니다.
5. VM의 Windows 업데이트로 이동 합니다.
6. 중요 범주에 모든 Windows 업데이트를 설치 합니다.
7. 선택적 범주 (언어 팩 제외)에 모든 Windows 업데이트를 설치 합니다. 이 프로세스는 이러한 지침을 완료 하는 데 필요한 원격 데스크톱 프로토콜 8.0 업데이트 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387))를 설치 합니다.
8. 로컬 그룹 정책 편집기를 열고 **컴퓨터 구성**  >  **관리 템플릿**  >  **Windows 구성 요소**  >  **원격 데스크톱 서비스**  >  **원격 데스크톱 세션 호스트**  >  **원격 세션 환경**으로 이동 합니다.
9. 원격 데스크톱 프로토콜 8.0 정책을 사용 하도록 설정 합니다.
10. 이 VM을 Active Directory 도메인에 가입 시킵니다.
11. 다음 명령을 실행 하 여 가상 컴퓨터를 다시 시작 합니다.

     ```cmd
     shutdown /r /t 0
     ```

12. [여기](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo?view=azps-4.3.0) 의 지침에 따라 등록 토큰을 가져옵니다.

      - Azure Portal를 사용 하는 경우 VM을 추가 하려는 호스트 풀의 개요 페이지로 이동 하 여 해당 위치에서 토큰을 만들 수도 있습니다.

13. Windows [7 용 Windows 가상 데스크톱 에이전트를 다운로드](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)합니다.
14. Windows [7 용 Windows 가상 데스크톱 에이전트 관리자을 다운로드](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)합니다.
15. Windows 가상 데스크톱 에이전트 설치 관리자를 열고 지침을 따릅니다. 메시지가 표시 되 면 12 단계에서 만든 등록 키를 제공 합니다.
16. Windows 가상 데스크톱 에이전트 관리자 열고 지침을 따릅니다.
17. 필요에 따라 TCP/3389 포트를 차단 하 여 VM에 대 한 직접 원격 데스크톱 프로토콜 액세스를 제거 합니다.
18. 필요에 따라 .NET framework가 버전 4.7.2 이상 인지 확인 합니다. 사용자 지정 이미지를 만드는 경우 프레임 워크를 업데이트 하는 것이 특히 중요 합니다.

## <a name="next-steps"></a>다음 단계

이제 Windows 가상 데스크톱 배포를 사용할 준비가 되었습니다. [최신 버전의 Windows 가상 데스크톱 클라이언트를 다운로드](https://aka.ms/wvd/clients/windows) 하 여 시작 하세요.

Windows 가상 데스크톱의 windows 7에 대 한 알려진 문제 및 문제 해결 지침은 windows [가상 데스크톱의 windows 7 가상 컴퓨터 문제](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)해결에서 문제 해결 문서를 참조 하세요.
