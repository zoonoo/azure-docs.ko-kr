---
title: Azure 상태 모니터 v2-시작 | Microsoft Docs
description: 상태 모니터 v2에 대 한 빠른 시작 가이드입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스 또는 Azure Vm에서 호스트 하는 ASP.NET 웹 앱에서 작동 합니다.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 4da9d8e8efd5f70718f18b2e8e35ea6b5adf6757
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734969"
---
# <a name="get-started-with-status-monitor-v2"></a>상태 모니터 v2를 사용 하 여 시작

이 문서에서는 대부분의 환경에 작동 하도록 빠른 시작 명령을 포함 합니다.
업데이트를 배포할 PowerShell 갤러리의 지침에 따라 달라 집니다.
이러한 명령은 PowerShell 지원 `-Proxy` 매개 변수입니다.

이러한 명령을, 사용자 지정 지침 및 문제 해결에 대 한 설명은 참조 하세요. 합니다 [상세 지침](status-monitor-v2-detailed-instructions.md)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="download-and-install-via-powershell-gallery"></a>다운로드 하 여 PowerShell 갤러리를 통해 설치

### <a name="install-prerequisites"></a>필수 구성 요소 설치
관리자로 PowerShell을 실행 합니다.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
PowerShell을 닫습니다.

### <a name="install-status-monitor-v2"></a>설치 상태 v2를 모니터링 합니다.
관리자로 PowerShell을 실행 합니다.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>모니터링 사용
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>수동으로 다운로드 및 설치 (오프 라인 옵션)
### <a name="download-the-module"></a>모듈을 다운로드
수동으로에서 모듈의 최신 버전을 다운로드 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)합니다.

### <a name="unzip-and-install-status-monitor-v2"></a>압축을 풀고 상태 모니터 v2를 설치 합니다.
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>모니터링 사용
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md) 성능 모니터링을 사용 합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제를 진단 합니다.
- [Analytics를 사용 하 여](../../azure-monitor/app/analytics.md) 자세한 고급 쿼리 합니다.
- [대시보드를 만들](../../azure-monitor/app/overview-dashboard.md)합니다.

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록 합니다.

상태 모니터 v2 사용 하 여 더 수행 합니다.

- 검토 합니다 [상세 지침](status-monitor-v2-detailed-instructions.md) 에 대 한 설명은 여기 명령입니다.
- 가이드를 사용 하 여 [해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
