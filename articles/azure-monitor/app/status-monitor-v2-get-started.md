---
title: Azure 상태 모니터 v2 시작 | Microsoft Docs
description: 상태 모니터 v2에 대 한 빠른 시작 가이드입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: c20cbf50129247fa01f10f12a99c558bc86492b8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145169"
---
# <a name="getting-started-with-status-monitor-v2"></a>상태 모니터 v2를 사용 하 여 시작

이 문서에는 대부분의 환경에 작동 하도록 빠른 시작 명령을 포함 합니다. 업데이트를 배포할 PowerShell 갤러리에서 이러한 지침에 따라 달라 집니다. 이러한 명령은 PowerShell 지원 `-Proxy` 매개 변수입니다.

검토 우리의 [상세 지침](status-monitor-v2-detailed-instructions.md) 페이지에 대 한 설명은이 명령, 문제를 해결 하는 방법과 사용자 지정 하는 방법에 대 한 지침입니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>다운로드 및 PowerShell 갤러리를 통해 설치

### <a name="install-prerequisites"></a>필수 구성 요소 설치
관리자 권한으로 PowerShell을 실행 합니다.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
PowerShell을 종료 합니다.

### <a name="install-status-monitor-v2"></a>설치 상태 v2를 모니터링 합니다.
관리자 권한으로 PowerShell을 실행 합니다.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>모니터링 사용
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>다운로드 및 설치를 수동으로 (오프 라인 옵션)
### <a name="manual-download"></a>수동 다운로드
모듈의 최신 버전 다운로드: https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>압축을 풀고 상태 모니터 v2를 설치 합니다.
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
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

- [메트릭을 탐색하여](../../azure-monitor/app/metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제 진단
- [분석](../../azure-monitor/app/analytics.md)을 통해 고급 쿼리를 수행합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/app-insights-dashboards.md).

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 삽입할 수 있도록 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록

상태 모니터 v2 사용 하 여 더 수행 합니다.

- 검토 합니다 [상세 지침](status-monitor-v2-detailed-instructions.md) 에 대 한 설명은이 가이드에서 찾을 명령입니다.
- 가이드를 사용 하 여 [문제 해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
