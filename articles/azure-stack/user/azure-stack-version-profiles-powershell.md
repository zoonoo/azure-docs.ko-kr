---
title: API 버전 프로필을 사용 하 여 Azure Stack에서 PowerShell을 사용 하 여 | Microsoft Docs
description: API 버전 프로필을 사용 하 여 Azure Stack에서 PowerShell을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: f6fd560d6ecb9feaa3df14b288cee5d98ce55aee
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391089"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Azure Stack에서 PowerShell에 대 한 API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

API 버전 프로필에는 Azure 및 Azure Stack의 버전 차이 관리 하는 방법을 제공 합니다. API 버전 프로필을는 특정 API 버전을 사용 하 여 AzureRM PowerShell 모듈의 집합입니다. 각 클라우드 플랫폼에 지원 되는 API 버전 프로필 집합이 있습니다. 예를 들어 Azure 스택에서 특정 날짜가 지정 된 프로필 버전을 같은 **2018-03-01-하이브리드**, Azure 지원 합니다 **최신** API 버전 프로필입니다. 프로필을 설치할 때 지정된 된 프로필에 해당 하는 AzureRM PowerShell 모듈이 설치 됩니다.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>API 버전 프로필을 사용 하는 데 필요한 PowerShell 모듈 설치

합니다 **AzureRM.Bootstrapper** 모듈이 PowerShell 갤러리를 통해 사용할 수 있는 API 버전 프로필을 사용 하는 데 필요한 PowerShell cmdlet을 제공 합니다. 다음 cmdlet을 사용 하 여 설치 합니다 **AzureRM.Bootstrapper** 모듈:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Azure Stack 버전 및 프로필 버전

다음 표에서 필요한 API 프로필 버전 및 Azure Stack의 최신 릴리스에 사용 되는 PowerShell 관리자 모듈 모니커를 나열 합니다. 사용할 경우이 문서에서는 버전을 사용 하 여 1808 하기 전에, 올바른 값으로 모니커 고 버전 프로필을 업데이트 합니다.

| 버전 번호 | API 버전 프로필 | PS 관리자 모듈 모니커 |
| --- | --- | --- |
| 1811 이상 | 2018-03-01-hybrid | 1.6.0 |
| 1808 이상 | 2018-03-01-hybrid | 1.5.0 |
| 1804 이상 | 2017-03-09-profile | 1.4.0 |
| 1804 이전 버전 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> 1.2.11 업그레이드할 버전 참조는 [마이그레이션 가이드](https://aka.ms/azpsh130migration)합니다.

## <a name="install-a-profile"></a>프로필 설치

사용 합니다 **설치 AzureRmProfile** cmdlet을 사용 합니다 **2018-03-01-하이브리드** Azure Stack에 필요한 AzureRM 모듈을 설치 하려면 API 버전 프로필. 이 API 버전 프로필을 사용 하 여 Azure Stack 연산자 모듈이 설치 되지 않습니다. 설치의 3 단계에서에서 별도로 명시 합니다 [Azure Stack 용 PowerShell 설치](../azure-stack-powershell-install.md) 문서.

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>설치 하 고 프로필에는 모듈 가져오기

사용 합니다 **사용 하 여 AzureRmProfile** cmdlet을 설치 하 고 API 버전 프로필을 사용 하 여 연결 된 모듈을 가져옵니다. PowerShell 세션에서 하나의 API 버전 프로필을 가져올 수 있습니다. 다른 API 버전 프로필을 가져오려면 새 PowerShell 세션을 열어야 합니다. 합니다 **사용 하 여 AzureRMProfile** cmdlet은 다음 작업을 수행 합니다.

1. 지정된 된 API 버전 프로필을 사용 하 여 연결 된 PowerShell 모듈을 현재 범위에 설치 된 경우를 확인 합니다.  
2. 다운로드 하 고 아직 설치 되지 않은 경우 모듈을 설치 합니다.
3. 현재 PowerShell 세션으로 모듈을 가져옵니다.

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

API 버전 프로필에서 선택한 AzureRM 모듈을 가져올 설치를 실행 합니다 **사용 하 여 AzureRMProfile** cmdlet을 사용 합니다 **모듈** 매개 변수:

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>설치 된 프로필 가져오기

사용 합니다 **Get AzureRmProfile** cmdlet을 사용할 수 있는 API 버전 프로필의 목록을 가져옵니다.

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>프로필 업데이트

사용 된 **업데이트 AzureRmProfile** PSGallery를 사용할 수 있는 모듈의 최신 버전으로 API 버전 프로필에서 모듈을 업데이트 하는 cmdlet입니다. 항상 실행 하는 것이 좋습니다. 합니다 **업데이트 AzureRmProfile** cmdlet 모듈을 가져올 때 충돌을 방지 하려면 새 PowerShell 세션에서. 합니다 **업데이트 AzureRmProfile** cmdlet은 다음 작업을 실행 합니다.

1. 모듈의 최신 버전을 현재 범위에 대 한 지정 된 API 버전 프로필에 설치 된 경우를 확인 합니다.  
2. 아직 설치 되지 않은 경우 설치 하 라는 메시지가 표시 됩니다.  
3. 설치 하 고 업데이트 된 모듈을 현재 PowerShell 세션으로 가져옵니다.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

이 cmdlet은 다음 작업을 실행 합니다.  

1. 모듈의 최신 버전을 현재 범위에 대 한 지정 된 API 버전 프로필에 설치 된 경우를 확인 합니다.  
2. 현재 API 버전 프로필에서 현재 PowerShell 세션의 이전 버전의 모듈을 제거합니다.  
3. 최신 버전을 설치 하 라는 메시지가 표시 됩니다.  
4. 설치 하 고 업데이트 된 모듈을 현재 PowerShell 세션으로 가져옵니다.  

## <a name="uninstall-profiles"></a>프로필 제거

사용 된 **제거 AzureRmProfile** 지정된 된 API 버전 프로필을 제거 하려면 cmdlet.

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
