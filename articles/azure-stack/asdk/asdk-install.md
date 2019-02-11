---
title: Azure Stack 개발 키트 (ASDK) 설치 | Microsoft Docs
description: Azure Stack 개발 키트 (ASDK)를 설치 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a58f5a3794d352fa8671321f5a30d74d2598df75
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977731"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Azure Stack 개발 키트 ASDK ()를 설치 합니다.
한 후 [ASDK 호스트 컴퓨터를 준비 하 고](asdk-prepare-host.md)는 ASDK이 문서의 다음 단계를 사용 하 여 CloudBuilder.vhdx 이미지로 배포할 수 있습니다.

## <a name="install-the-asdk"></a>ASDK 설치
이 문서의 단계에서는 다운로드 및 실행 하 여 제공 하는 그래픽 사용자 인터페이스 (GUI)를 사용 하 여 ASDK를 배포 하는 방법을 보여 줍니다.는 **asdk installer.ps1** PowerShell 스크립트입니다.

> [!NOTE]
> Azure Stack 개발 키트에 대 한 설치 관리자 사용자 인터페이스는 WCF 및 PowerShell 기반 오픈 소스 스크립트.


1. 관리자 자격 증명을 사용 하 여 로그인 때 지정 된 호스트 컴퓨터를 성공적으로 CloudBuilder.vhdx 이미지로 부팅 한 후 있습니다 [개발 키트 호스트 컴퓨터를 준비](asdk-prepare-host.md) ASDK 설치 합니다. 이 개발 키트 호스트의 로컬 관리자 자격 증명과 동일 해야 합니다.
2. 승격된 된 PowerShell 콘솔을 열고 실행 합니다  **&lt;드라이브 문자 > \AzureStack_Installer\asdk-installer.ps1** PowerShell 스크립트입니다. 스크립트를 다른 CloudBuilder.vhdx 이미지에는 C:\ 드라이브에 있을 수 이제는 참고 합니다. **Install**을 클릭합니다.

    ![](media/asdk-install/1.PNG) 

3. Id 공급자의 **형식** 드롭다운 목록 상자에서 **Azure 중국 클라우드에서**를 **Azure 미국 정부 클라우드**를 **AD FS**, 또는 **Azure 클라우드**합니다. 아래 **로컬 관리자 암호** (현재 구성 된 로컬 관리자 암호와 일치 해야 합니다)는 로컬 관리자 암호를 입력 합니다 **암호** 상자를 선택한 다음 클릭  **다음**합니다.

    ![](media/asdk-install/2.PNG) 
  
   인터넷에 연결 된 Azure AD의 전체 이름을 사용 해야 Azure 구독 id 공급자를 선택 하면 디렉터리 테 넌 트의 형태로 *domainname*. onmicrosoft.com 또는 Azure AD에 사용자 지정 도메인 이름 및 글로벌 확인 지정된 된 디렉터리에 대 한 관리자 자격 증명입니다.<br><br>배포 후 Azure Active Directory 전역 관리자 권한이 필요 하지 않습니다. 그러나 일부 작업에는 전역 관리자 자격 증명을 필요할 수 있습니다. 예를 들어, 리소스 공급자 설치 관리자 스크립트 또는 권한 부여 요청을 새로운 기능. 일시적으로 계정의 전역 관리자 권한이 다시 설정 하거나의 소유자가 별도 전역 관리자 계정을 사용 합니다 *공급자 구독을 기본*입니다.<br><br>AD FS를 id 공급자로 사용 하는 경우에 기본 스탬프 디렉터리 서비스 사용 됩니다. 로그인 하는 데 기본 계정은 azurestackadmin@azurestack.local, 암호를 사용 하 여 설치의 일부로 제공 된 것 이며 합니다.

  > [!NOTE]
  > 최상의 결과 id 공급자로 Adfs를 사용 하 여 연결이 끊긴된 Azure Stack 환경을 사용 하려는 경우에 것이 좋습니다 ASDK 인터넷에 연결 하는 동안 설치 합니다. 이런 방식으로 배포 시에 개발 키트 설치에 포함 된 Windows Server 2016 평가판을 활성화할 수 있습니다.

4. 개발 키트에 대 한를 클릭 한 다음 네트워크 어댑터를 선택 **다음**합니다.

    ![](media/asdk-install/3.PNG)

5. 에 **네트워크 구성** 페이지에서 유효한 제공 **서버 IP를 시간** 주소입니다. 이 필드는 개발 키트에서 사용할 시간 서버를 설정 해야 합니다. 이 매개 변수는 유효한 시간 서버 IP 주소로 제공 되어야 합니다. 서버 이름은 지원 되지 않습니다.

      > [!TIP]
      > 시간 서버 IP 주소를 찾으려면 방문 [ntppool.org](https://www.ntppool.org/) 하거나 time.windows.com를 ping 합니다. 

    **필요에 따라**를 제공할 수는 **DNS 전달자** IP 주소입니다. DNS 서버는 Azure Stack 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에서 컴퓨터를 허용 하려면 기존 인프라 DNS 서버를 제공 합니다. 타임 스탬프에서 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.

    ![](media/asdk-install/4.PNG)

6. 에 **네트워크 인터페이스 카드 속성 확인** 페이지에서 진행률 표시줄이 표시 됩니다. 확인 완료 되 면 클릭 **다음**합니다.

    ![](media/asdk-install/5.PNG)

7. **요약** 페이지에서 **배포** 개발 키트 호스트 컴퓨터에서 ASDK 설치를 시작 합니다.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > 다음 개발 키트를 설치 하는 데 사용할 수 있는 PowerShell 설치 명령을 복사할 수 있습니다. 해야 할 경우에 유용 [PowerShell을 사용 하 여 호스트 컴퓨터에서 ASDK 재배포](asdk-deploy-powershell.md)합니다.

8. Azure AD 배포를 수행 하는 경우 설치 프로그램 시작 후 몇 분이 Azure AD 전역 관리자 계정 자격 증명을 입력 하 라는 메시지가 됩니다.

9. 배포 프로세스를이 기간 동안 호스트 컴퓨터를 자동으로 다시 부팅 되 면 몇 시간이 걸립니다. 배포 진행률을 모니터링 하려는 경우 로그인 azurestack\AzureStackAdmin로 개발 키트 호스트를 다시 시작한 후 합니다. 배포에 성공 하면 PowerShell 콘솔에 표시 됩니다. **완료 합니다. 작업 '배포'** 합니다. 
    > [!IMPORTANT]
    > 로컬 관리자로 컴퓨터 azurestack의 경우 도메인에 가입 된 후에 로그인 하는 경우에 배포 진행률을 볼 수 없습니다. 대신 실행 되는지 유효성을 검사 하려면 azurestack\AzureStackAdmin로 로그인, 배포를 다시 실행 되지 않습니다.

    ![](media/asdk-install/7.PNG)

축는 ASDK 성공적으로 설치한!

배포가 실패 하는 몇 가지 이유로 할 수 있습니다 [재배포](asdk-redeploy.md) 처음부터 새로 만들거나 사용 하 여 다음 PowerShell 명령을 동일한 관리자 권한 PowerShell 창에서 마지막 성공한 단계에서 배포를 다시 시작 합니다.

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>다음 단계
[배포 후 구성](asdk-post-deploy.md)
