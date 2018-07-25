---
title: Windows 사용자용 Azure Cloud Shell | Microsoft Docs
description: Linux 시스템에 익숙하지 않은 사용자를 위한 가이드
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861220"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Windows 사용자용 Azure Cloud Shell의 PowerShell

2018년 5월 Azure Cloud Shell의 PowerShell에 대한 변경이 [발표](https://azure.microsoft.com/blog/pscloudshellrefresh/)되었습니다.  Azure Cloud Shell의 PowerShell 환경은 이제 Linux에서 PowerShell Core 6입니다.
이러한 변경으로 인해 Cloud Shell에 포함된 PowerShell의 몇 가지 측면이 Windows PowerShell 5.1에서 예상되는 것과 다를 수 있습니다.

## <a name="case-sensitivity"></a>대/소문자 구분

Windows의 파일 시스템은 대/소문자를 구분하지 않습니다.  Linux의 파일 시스템은 대/소문자를 구분합니다.
즉, 이전의 `file.txt` 및 `FILE.txt`는 같은 파일로 간주되었으나 이제는 다른 파일로 간주됩니다.
파일 시스템에서 `tab` 완성 동안 적절한 대/소문자를 사용해야 합니다.  `tab` cmdlet과 같은 PowerShell 특정 환경은 대/소문자를 구분하지 않습니다. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Windows PowerShell 별칭 및 Linux 유틸리티

Linux의 기존 명령(예: `ls`, `sort` 및 `sleep`)은 PowerShell 별칭보다 우선합니다.  다음은 제거된 일반적인 별칭과 해당 명령입니다.  

|제거된 별칭   |해당 명령   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>$home 및 $home\clouddrive 지속

스크립트 및 기타 파일을 클라우드 드라이브에 보존하는 경우, 이제 $HOME 디렉터리가 세션이 바뀌어도 지속됩니다.

## <a name="powershell-profile"></a>PowerShell 프로필

기본적으로 PowerShell 프로필은 만들어지지 않습니다.  프로필을 만들려면 `$HOME/.config` 아래에 `PowerShell` 디렉터리를 만듭니다.  `$HOME/.config/PowerShell`에서 `Microsoft.PowerShell_profile.ps1` 이름 아래에 프로필을 만들 수 있습니다.

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6의 새로운 기능

PowerShell Core 6의 새로운 기능에 대한 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) 및 [PowerShell Core 시작](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) 블로그 게시물을 참조하세요.
