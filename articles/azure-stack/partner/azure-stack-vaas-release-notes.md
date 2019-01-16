---
title: Azure Stack 유효성 검사 서비스로 릴리스 정보 | Microsoft Docs
description: 서비스로 azure Stack 유효성 검사 하 고 릴리스 키를 누릅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 735f1d7cce8621a29e5a582917d3eea8ee399b8f
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319651"
---
# <a name="release-notes-for-validation-as-a-service"></a>서비스 유효성 검사에 대 한 릴리스 정보

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

이 문서는 서비스로 Azure Stack 유효성 검사에 대 한 릴리스 정보에서는 합니다.

## <a name="version-402"></a>버전 4.0.2

2019 년 1 월 7

Azure Stack 월별 업데이트 확인 워크플로 실행 중인 경우 OEM 업데이트 패키지의 버전은 1810 이상의 OEM 업데이트 단계를 가져오면는 오류가 발생 합니다. 이 버그입니다. 수정 프로그램은 개발 중입니다. 완화 단계를 아래와 같습니다.

1.  OEM 업데이트가 정상적으로 실행 합니다.
2.  패키지의 응용 프로그램 성공 후 AzureStack 테스트를 실행 하 고 출력을 저장 합니다.
3.  테스트를 취소 합니다.
4.  저장 된 출력을 보낼 VaaSHelp@microsoft.com 실행에 대 한 전달 결과 받을 수 있습니다.

## <a name="version-402"></a>버전 4.0.2

2018 년 11 월 30

- 내부 버그 수정

## <a name="version-401"></a>버전 4.0.1

2018 년 10 월 8

- VaaS 필수 구성 요소

    `Install-VaaSPrerequisites` 클라우드 관리자 자격 증명을 더 이상 필요합니다. 이 cmdlet의 최신 버전을 실행 하는 경우 참조 [에이전트를 다운로드 및 설치](azure-stack-vaas-local-agent.md#download-and-install-the-agent) 필수 구성 요소 설치에 대 한 수정 된 명령에 대 한 합니다. 명령은 다음과 같습니다.

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>버전 4.0.0

2018 년 8 월 29

- 업데이트 VaaS 필수 구성 요소 및 VHD

    `Install-VaaSPrerequisites` 이제 솔루션 유효성 검사 중 문제를 해결 하려면 클라우드 관리자 자격 증명이 필요 합니다. 설명서 [에이전트를 다운로드 및 설치](azure-stack-vaas-local-agent.md#download-and-install-the-agent) 다음을 사용 하 여 업데이트 되었습니다.

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > `$CloudAdminCreds` 스크립트에서 Azure Stack에 대 한 인스턴스의 유효성을 검사할 필요 합니다. Azure Active Directory 자격 증명 VaaS 테 넌 트에서 사용 되지 않습니다.

- 로컬 에이전트 업데이트

    이전 버전의 로컬 에이전트가 현재 4.0.0와 호환 되지 않습니다. 서비스의 릴리스 합니다. 모든 사용자는 해당 로컬 에이전트를 업데이트 해야 합니다. 참조 [다운로드 하 여 에이전트를 설치](azure-stack-vaas-local-agent.md#download-and-install-the-agent) 의 최신 에이전트를 설치 합니다.

- PowerShell 자동화 업데이트

    에 변경 내용이 `LaunchVaaSTests` 스크립팅 패키지의 최신 버전을 필요로 하는 PowerShell 스크립트입니다. 참조 [테스트 통과 워크플로 시작](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) 스크립팅 패키지의 최신 버전 설치에 대 한 지침은 합니다.

- 유효성 검사 서비스 포털

  - 패키지 서명 알림

    OEM 사용자 지정 패키지를 솔루션의 유효성 검사 워크플로의 일부로 전송 되 면 게시 된 사양을 따르는지 확인 하는 패키지 형식 유효성 검사가 수행 됩니다. 패키지 맞지 않는 경우 실행이 실패 합니다. 전자 메일 알림 대화 상대의 등록 된 Azure Active Directory 테 넌 트에 대 한 전자 메일 주소로 보내집니다.

  - 대화형 테스트 범주

    합니다 **대화형** 테스트 범주가 추가 되었습니다. 이러한 테스트에는 대화형으로 자동화 되지 않은 Azure Stack 시나리오를 실행 하는 파트너 수 있습니다.

  - 대화형 기능 확인

    피드백을 보내도록 포커스가 있는 특정 기능 기능은 이제 테스트 통과 워크플로에서 사용할 수 있습니다. `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` 테스트 특정 업데이트가 올바르게 적용 되었는지 확인 하 고 다음 피드백을 수집 합니다.

## <a name="next-steps"></a>다음 단계

- [서비스 유효성 검사 문제 해결](azure-stack-vaas-troubleshoot.md)
