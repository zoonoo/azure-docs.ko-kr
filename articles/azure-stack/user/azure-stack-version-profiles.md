---
title: "Azure 스택에서 API 버전 프로필을 관리 | Microsoft Docs"
description: "Azure 스택에서 API 버전 프로필에 대 한 자세한 내용은"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6B749785-DCF5-4AD8-B808-982E7C6BBA0E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: mabrigg
ms.openlocfilehash: d86a54ea9e165269131eb961df7f74703f0ec6ff
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure 스택에서 API 버전 프로필을 관리

Azure 앱 서비스 버전 프로필의 API 기능에는 Azure 및 Azure 스택 간에 버전 간 차이점을 관리 하는 방법을 제공 합니다. API 버전 프로필은는 특정 API 버전 AzureRM PowerShell 모듈의 집합입니다. 각 클라우드 플랫폼에 지원 되는 API 버전 프로필 집합이 있습니다. 예를 들어 Azure 스택 등의 지원 특정 날짜가 지정 된 프로필 버전 **2017-03-09-프로필**, Azure를 지원 하 고는 *최신* API 버전 프로필입니다. 프로필을 설치 하는 경우 지정된 된 프로필에 해당 하는 AzureRM PowerShell 모듈이 설치 됩니다.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>API 버전 프로필을 사용 하는 데 필요한 PowerShell 모듈 설치

**AzureRM.Bootstrapper** PowerShell 갤러리를 통해 사용할 수 있는 모듈 API 버전 프로필로 작업 하는 데 필요한 PowerShell cmdlet을 제공 합니다. 다음 cmdlet을 사용 하 여 설치 된 **AzureRM.Bootstrapper** 모듈:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
**AzureRM.Bootstrapper** 모듈은 미리 보기, 세부 정보 및 기능은 변경 될 수 있도록 합니다. 를 다운로드 하 여 PowerShell 갤러리에서이 모듈의 최신 버전을 설치 하려면 다음 cmdlet을 실행 합니다.

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>프로필 설치

사용 하 여는 **설치 AzureRmProfile** 사용 하 여 cmdlet는 **2017-03-09-프로필** API 버전 프로필을 Azure 스택에서 필요한 AzureRM 모듈을 설치 합니다. 

>[!NOTE]
>이 API 버전 프로필 Azure 스택 클라우드 관리자 모듈 설치 되지 않습니다. 관리자 모듈의 3 단계에는 지정 된 대로 별도로 설치 해야는 [Azure 스택 위한 PowerShell 설치](azure-stack-powershell-install.md) 문서.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>설치 하 고 프로필에는 모듈 가져오기

사용 하 여는 **사용 AzureRmProfile** cmdlet 설치 하 고 API 버전 프로필과 연결 되어 있는 모듈을 가져와야 합니다. PowerShell 세션에서 하나의 API 버전 프로필을 가져올 수 있습니다. 다른 API 버전 프로필을 가져오려면 새 PowerShell 세션을 열어야 합니다. **사용 AzureRMProfile** 다음 작업을 실행 하는 cmdlet:  
1. 현재 범위에 지정 된 API 버전 프로필에 연결 된 PowerShell 모듈 설치 된 경우를 확인 합니다.  
2. 다운로드 아직 설치 되지 않은 경우 모듈을 설치 합니다.   
3. 현재 PowerShell 세션으로 모듈을 가져옵니다. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

설치 하는 API 버전 프로필에서 선택한 AzureRM 모듈을 가져올 실행는 **사용 AzureRMProfile** 사용 하 여 cmdlet는 *모듈* 매개 변수:

```PowerShell
# Installs and imports the Compute, Storage, and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>설치 되어 있는 프로필 가져오기

사용 하 여는 **Get AzureRmProfile** cmdlet을 사용할 수 있는 API 버전 프로필 목록을 가져옵니다. 

```PowerShell
# Lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# Lists the API version profiles that are installed on your machine.
Get-AzureRmProfile
```
## <a name="update-profiles"></a>프로필 업데이트

사용 하 여는 **업데이트 AzureRmProfile** API 버전 프로필의 모듈에서는 PowerShell 갤러리에서 사용할 수 있는 모듈의 최신 버전으로 업데이트 하는 cmdlet입니다. 실행 하는 것이 좋습니다는 **업데이트 AzureRmProfile** 충돌을 피하기 위해 모듈을 가져올 때 새 PowerShell 세션에서 cmdlet. **업데이트 AzureRmProfile** 다음 작업을 실행 하는 cmdlet:

1. 모듈의 최신 버전은 현재 범위에 대 한 지정 된 API 버전 프로필에 설치 되어 있는지 확인 합니다.  
2. 아직 설치 되지 않은 경우 모듈을 설치 하 라는 메시지를 표시 합니다.  
3. 설치 하 고 업데이트 된 모듈은 현재 PowerShell 세션으로 가져옵니다.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

사용 가능한 최신 버전을 업데이트 하기 전에 모듈의 이전에 설치 된 버전으로는 **업데이트 AzureRmProfile** cmdlet과 함께 *-RemovePreviousVersions* 매개 변수:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

이 cmdlet는 다음 작업을 실행 합니다.  

1. 모듈의 최신 버전은 현재 범위에 대 한 지정 된 API 버전 프로필에 설치 되어 있는지 확인 합니다.  
2. 현재 API 버전 프로필 및 현재 PowerShell 세션에서 모듈의 이전 버전을 제거 합니다.  
3. 모듈의 최신 버전을 설치 하 라는 메시지를 표시 합니다.  
4. 설치 하 고 업데이트 된 모듈은 현재 PowerShell 세션으로 가져옵니다.  
 
## <a name="uninstall-profiles"></a>프로필 제거

사용 하 여는 **제거 AzureRmProfile** cmdlet는 지정 된 API 버전 프로필을 제거 하려면:

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>다음 단계
* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
