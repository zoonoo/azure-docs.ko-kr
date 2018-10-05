---
title: Azure Stack 개발 키트 (ASDK) 호스트 컴퓨터를 준비 | Microsoft Docs
description: ASDK 설치에 대 한 Azure Stack 개발 키트 (ASDK) 호스트 컴퓨터를 준비 하는 방법에 설명 합니다.
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fc9681ee286c30825ac908f9f97ae092808c783a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802140"
---
# <a name="prepare-the-asdk-host-computer"></a>ASDK 호스트 컴퓨터를 준비 합니다.
호스트 컴퓨터에는 ASDK를 설치할 수 있습니다, 전에 ASDK 환경 설치에 대 한 준비 되어야 합니다. 개발 키트 호스트 컴퓨터 준비 되었으므로 ASDK 배포를 시작 하려면 CloudBuilder.vhdx 가상 컴퓨터 하드 드라이브에서 부팅 됩니다.

## <a name="prepare-the-development-kit-host-computer"></a>개발 키트 호스트 컴퓨터를 준비 합니다.
호스트 컴퓨터에는 ASDK를 설치할 수 있습니다, 전에 ASDK 호스트 컴퓨터 환경이 준비 되어야 합니다.
1. 개발 키트 호스트 컴퓨터에 로컬 관리자로 로그인 합니다.
2. CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트에 옮겨 졌음을 있는지 확인 합니다.
3. (asdk installer.ps1) 개발 키트 설치 관리자 파일을 다운로드 하려면 다음 스크립트를 실행 합니다 [도구에 Azure Stack GitHub 리포지토리](https://github.com/Azure/AzureStack-Tools) 에 **C:\AzureStack_Installer** 폴더에 사용자 개발 키트 호스트 컴퓨터:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 승격된 된 PowerShell 콘솔에서 시작 합니다 **C:\AzureStack_Installer\asdk-installer.ps1** 스크립팅하고 클릭 **준비 환경**합니다.

    ![](media/asdk-prepare-host/1.PNG) 

5. 에 **Cloudbuilder 선택 vhdx** 설치 관리자, 이동 및 선택 페이지를 **cloudbuilder.vhdx** 파일을 다운로드 하 고에서 추출 [이전 단계](asdk-download.md)합니다. 이 페이지에서 설정할 수 있습니다. 또한 필요에 따라 합니다 **드라이버 추가** 확인란 추가 드라이버 개발 키트 호스트 컴퓨터를 추가 해야 할 경우. **다음**을 클릭합니다.  

    ![](media/asdk-prepare-host/2.PNG)

6. 에 **선택적 설정을** 페이지에서 로컬 관리자에 게 제공 개발 키트 호스트 컴퓨터에 대 한 정보를 고려 하 고 클릭 **다음**합니다. 또한 다음 선택적 설정에 대 한 값을 제공할 수 있습니다.
  - **Computername**:이 옵션에는 개발 키트 호스트에 대 한 이름을 설정 합니다. 이름은은 FQDN 요구 사항을 준수 해야 하며 길이가 또는 15 자 여야 합니다. 기본값은 Windows에서 생성 된 임의의 컴퓨터 이름입니다.
  - **고정 IP 구성을**: 고정 IP 주소를 사용 하도록 배포를 설정 합니다. 그렇지 않으면 설치 관리자는 cloudbuilder.vhx를 다시 부팅, 네트워크 인터페이스 DHCP를 사용 하 여 구성 됩니다.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > 이 단계에서는 로컬 관리자 자격 증명을 제공 하지 않으면, 해야 direct 또는 KVM 호스트에 대 한 액세스 설정 개발 키트의 일부분으로 컴퓨터를 다시 시작 합니다.

7. 이전 단계에서 고정 IP 구성을 했다면 이제 해야 합니다.
    - 네트워크 어댑터를 선택 합니다. 클릭 하기 전에 어댑터에 연결할 수 있는지 **다음**합니다.
    - 있는지 확인 합니다 **IP 주소**, **게이트웨이**, 및 **DNS** 값을 정확 하 고 클릭 **다음**합니다.
13. 클릭 **다음** 준비 프로세스를 시작 합니다.
14. 시점을 나타내는 준비 **완료 됨**, 클릭 **다음**합니다.
15. 클릭 **지금 다시 부팅** 는 cloudbuilder.vhdx로 개발 키트 호스트 컴퓨터를 부팅 하 고 [배포 프로세스를 계속](asdk-install.md)합니다.

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>다음 단계
[ASDK 설치](asdk-install.md)
