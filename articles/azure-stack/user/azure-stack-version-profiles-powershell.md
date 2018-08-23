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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 8da02641cc54f9308f8e0bbb8d2b28da9a930aa2
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139457"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Azure Stack에서 PowerShell에 대 한 API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

API 버전 프로필에는 Azure 및 Azure Stack의 버전 차이 관리 하는 방법을 제공 합니다. API 버전 프로필을는 특정 API 버전을 사용 하 여 AzureRM PowerShell 모듈의 집합입니다. 각 클라우드 플랫폼에 지원 되는 API 버전 프로필 집합이 있습니다. 예를 들어 Azure 스택에서 특정 날짜가 지정 된 프로필 버전을 같은 **2017-03-09-프로필**, Azure 지원 합니다 **최신** API 버전 프로필입니다. 프로필을 설치할 때 지정된 된 프로필에 해당 하는 AzureRM PowerShell 모듈이 설치 됩니다.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>API 버전 프로필을 사용 하는 데 필요한 PowerShell 모듈 설치

합니다 **AzureRM.Bootstrapper** 모듈이 PowerShell 갤러리를 통해 사용할 수 있는 API 버전 프로필을 사용 하는 데 필요한 PowerShell cmdlet을 제공 합니다. AzureRM.Bootstrapper 모듈을 설치 하려면 다음 cmdlet을 사용 합니다.

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>프로필 설치

사용 합니다 **설치 AzureRmProfile** cmdlet을 사용 합니다 **2017-03-09-프로필** Azure Stack에 필요한 AzureRM 모듈을 설치 하려면 API 버전 프로필. 이 API 버전 프로필을 사용 하 여 Azure Stack 연산자 모듈이 설치 되지 않습니다. 설치의 3 단계에서에서 별도로 명시 합니다 [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md) 문서.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>설치 하 고 프로필에는 모듈 가져오기

사용 합니다 **사용 하 여 AzureRmProfile** cmdlet을 설치 하 고 API 버전 프로필을 사용 하 여 연결 된 모듈을 가져옵니다. PowerShell 세션에서 하나의 API 버전 프로필을 가져올 수 있습니다. 다른 API 버전 프로필을 가져오려면 새 PowerShell 세션을 열어야 합니다. 사용 하 여 AzureRMProfile cmdlet은 다음 작업을 실행 합니다.  
1. 지정된 된 API 버전 프로필을 사용 하 여 연결 된 PowerShell 모듈을 현재 범위에 설치 된 경우를 확인 합니다.  
2. 다운로드 하 고 아직 설치 되지 않은 경우 모듈을 설치 합니다.   
3. 현재 PowerShell 세션으로 모듈을 가져옵니다. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

를 설치 하 고 API 버전 프로필에서 선택한 AzureRM 모듈을 가져올 사용 하 여 사용 하 여 AzureRMProfile cmdlet을 실행 합니다 **모듈** 매개 변수:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
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

사용 된 **업데이트 AzureRmProfile** PSGallery를 사용할 수 있는 모듈의 최신 버전으로 API 버전 프로필에서 모듈을 업데이트 하는 cmdlet입니다. 항상 실행 하는 것이 좋습니다 합니다 **업데이트 AzureRmProfile** cmdlet 모듈을 가져올 때 충돌을 방지 하려면 새 PowerShell 세션에서. 업데이트 AzureRmProfile cmdlet은 다음 작업을 실행 합니다.

1. 모듈의 최신 버전을 현재 범위에 대 한 지정 된 API 버전 프로필에 설치 된 경우를 확인 합니다.  
2. 아직 설치 되지 않은 경우 설치 하 라는 메시지가 표시 됩니다.  
3. 설치 하 고 업데이트 된 모듈을 현재 PowerShell 세션으로 가져옵니다.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

사용 가능한 최신 버전으로 업데이트 하기 전에 이전에 설치 된 버전의 모듈을 제거 하려면 함께 업데이트 AzureRmProfile cmdlet을 사용 합니다 **-RemovePreviousVersions** 매개 변수:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

이 cmdlet은 다음 작업을 실행 합니다.  

1. 모듈의 최신 버전을 현재 범위에 대 한 지정 된 API 버전 프로필에 설치 된 경우를 확인 합니다.  
2. 현재 API 버전 프로필에서 현재 PowerShell 세션의 이전 버전의 모듈을 제거합니다.  
4. 최신 버전을 설치 하 라는 메시지가 표시 됩니다.  
5. 설치 하 고 업데이트 된 모듈을 현재 PowerShell 세션으로 가져옵니다.  
 
## <a name="uninstall-profiles"></a>프로필 제거

사용 된 **제거 AzureRmProfile** 지정된 된 API 버전 프로필을 제거 하려면 cmdlet.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>다음 단계
* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
