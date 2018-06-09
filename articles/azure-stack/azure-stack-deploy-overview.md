---
title: Azure 스택 개발 키트 평가 | Microsoft Docs
description: Azure 스택 개발 키트 평가 목적을 배포 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235188"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Azure 스택 개발 키트를 평가 하는 빠른 시작:

[Azure 스택 개발 키트 (ASDK)](.\asdk\asdk-what-is.md) 를 평가 하 여 Azure 스택 기능 및 서비스를 보여 줍니다. 배포할 수 있는 테스트 및 개발 환경입니다. ASDK을 시작 하려면 호스트 컴퓨터 하드웨어를 준비한 후 (설치는 몇 시간) 일부 스크립트를 실행 해야 합니다. 그 후 수에 로그인 할 관리자 또는 사용자 포털에 Azure 스택을 사용 하 여 시작 합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="asdk-host-computer-requirements"></a>ASDK 호스트 컴퓨터 요구 사항

ASDK를 설치 하기 전에 개발 키트를 호스팅하는 컴퓨터를 준비 해야 합니다. 개발 키트 호스트 컴퓨터의 하드웨어, 소프트웨어를 충족 해야 하며 네트워크 요구 사항에 설명 된  **[ASDK 배포 계획 고려 사항 검토](.\asdk\asdk-deploy-considerations.md)** 합니다.

> [!TIP]
> 사용할 수는 [Azure 스택 배포 요구 사항 확인 도구](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) 하드웨어 요구 사항을 모두 충족 하는지 확인 하려면 개발 키트 호스트 컴퓨터에 운영 체제를 설치한 후입니다.

### <a name="account-requirements"></a>계정 요구 사항

또한 해야 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)을 사용 하 여 중에서 선택할 수 id 솔루션으로 배포 합니다. 계정 요구 사항을 검토  **[배포 계획 고려 사항](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>다운로드 하 고 배포 패키지를 압축 합니다.

개발 키트 호스트 컴퓨터를 준비한 후 다운로드 하 고 ASDK 배포 패키지를 추출 합니다. 배포 패키지는 부팅 가능한 운영 체제와 가상 하드 드라이브 (VHD), Cloudbuilder.vhdx 파일과 Azure 스택 설치 파일을 포함 합니다.

개발 키트 호스트 또는 다른 컴퓨터에 배포 패키지를 다운로드할 수 있습니다. 추출 된 배포 파일 다른 컴퓨터를 사용 하 여 개발 키트 호스트에서 저장소 요구 사항을 줄일 수 있으므로 60GB의 사용 가능한 디스크 공간을 차지 합니다.

**[다운로드 하 고 Azure 스택 개발 키트 (ASDK) 추출](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>호스트 컴퓨터를 준비 합니다.

ASDK를 설치 하기 전에 호스트 환경이 준비 되어야 합니다 및 시스템이 VHD 개발 키트에서 부팅 하도록 구성 합니다. 호스트를 다시 시작 하면 CloudBuilder.vhdx에서 부팅 하 고는 ASDK 배포를 시작할 수 있습니다.

**[ASDK 호스트 컴퓨터를 준비 합니다.](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>호스트 컴퓨터의 ASDK 설치

VHD에서 호스트 컴퓨터를 부팅 한 후 개발 키트 Cloudbuilder 가상 환경에 배포할 수 있습니다. Asdk installer.ps1 PowerShell 스크립트를 실행 하거나에서 제공 하는 그래픽 사용자 인터페이스 (GUI)를 사용 하 여 ASDK 배포할 수 있습니다 [PowerShell 명령줄](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> 구성할 수 있는 호스트 Cloudbuilder.vhdx 이미지에서 부팅 한 후 [Azure 스택 원격 분석 설정이](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *전에* 는 ASDK를 설치 합니다.

**[Azure 스택 개발 키트 (ASDK) 설치](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>배포 후 구성을 수행합니다

ASDK를 설치한 후은 몇 가지 권장 되는 설치 후 검사 및 구성 변경 내용을입니다.

**도구**

Azure 스택 PowerShell 및 GitHub와 도구를 설치 하 고 테스트 AzureStack cmdlet을 사용 하 여 설치의 성공 여부를 확인 합니다.

**Id 솔루션**

Azure AD를 사용 하는 배포에 대 한 Azure 스택 관리자 및 테 넌 트 포털 모두 활성화 해야 합니다. 이 활성화에 Azure 스택 포털 및 Azure 리소스 관리자 정확한 권한을 제공 (동의 페이지에 나열 된) 모든 사용자에 대 한 디렉터리의에 동의 합니다.

**암호 만료**

암호 만료 정책을 개발 키트 호스트에 대 한 암호 평가 기간이 종료 되기 전에 만료 되지 되도록 다시 설정 해야 합니다.

> [!NOTE]
> 구성할 수 있습니다 [Azure 스택 원격 분석 설정이](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *후* 는 ASDK를 설치 합니다.

**[Post ASDK 배포 작업](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure에 등록

수 있도록 Azure와 함께 Azure 스택을 등록 해야 [Azure 마켓플레이스 항목을 다운로드](.\asdk\asdk-marketplace-item.md) Azure 스택에 있습니다.

**[Azure 스택 Azure 등록](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>다음 단계

축하합니다! 이 빠른 시작의 단계를 완료 하 여 ASDK 환경에 있는 한 [관리자](https://adminportal.local.azurestack.external) 포털 및 [사용자](https://portal.local.azurestack.external) 포털입니다.
