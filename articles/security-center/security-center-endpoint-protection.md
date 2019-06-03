---
title: Endpoint protection 솔루션 검색 및 상태 평가 Azure Security Center에서 | Microsoft Docs
description: Endpoint protection 솔루션 검색 되 고 정상으로 식별 하는 방법입니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247966"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint protection 평가 및 Azure Security Center에서 권장 사항

Endpoint protection 평가 및 Azure Security Center에서 권장 사항을 검색 하 고의 상태 평가 제공 [지원](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) 끝점 보호 솔루션의 버전입니다. 이 항목에서는 Azure Security Center에서 Endpoint protection 솔루션에 대 한 다음 두 가지 권장 사항을 생성 하는 시나리오에 설명 합니다.

* **가상 컴퓨터에 endpoint protection 솔루션 설치**
* **컴퓨터에서 endpoint protection 상태 문제 해결**

## <a name="windows-defender"></a>Windows Defender

* **"가상 컴퓨터에서 끝점 보호 솔루션을 설치 합니다."** 권장 구성이 생성 된 경우 [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) 실행 되 고 결과 **AMServiceEnabled: False**

* 합니다 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 권장 구성이 생성 된 경우 [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) 실행 하 고 하나 또는 둘 중 발생:

  * 하나 이상의 다음 속성은 false.

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * 다음 속성 중 하나 이상이 7 같거나 큰 경우.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* 합니다 **"가상 컴퓨터에서 끝점 보호 솔루션을 설치 합니다."** 권장 사항을 가져올 때 생성 됩니다 **SCEPMpModule ("$env: ProgramFiles\Microsoft 보안 Client\MpProvider\MpProvider.psd1")** 들고 **Get MProtComputerStatus** 결과 **AMServiceEnabled = false**

* 합니다 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 권장 구성이 생성 된 경우 **Get MprotComputerStatus** 실행 하 고 하나 또는 둘 중 발생:

    * 하나 이상의 다음 속성은 false.

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * 하나 또는 둘 다 다음 서명 업데이트의 경우 7 보다 크거나 합니다. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* 합니다 **"가상 컴퓨터에서 끝점 보호 솔루션을 설치 합니다."** 충족 되지 않으면 다음 검사가 더 또는 경우에 권장 사항이 생성 됩니다.
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** 존재
    * **HKLM:\SOFTWARE\TrendMicro\Deep 보안 Agent\InstallationFolder** 존재
    * 합니다 **dsq_query.cmd** 설치 폴더에 파일을 찾을 수
    * 실행 중인 **dsa_query.cmd** 결과 **Component.AM.mode:에서 Trend Micro Deep Security Agent 검색**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
합니다 **"가상 컴퓨터에서 끝점 보호 솔루션을 설치 합니다."** 충족 되지 않으면 다음 검사가 권장 사항이 생성 됩니다.

* **HKLM:\Software\Symantec\Symantec 끝점 Protection\CurrentVersion\PRODUCTNAME "Symantec Endpoint Protection" =**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

또는

* **HKLM:\Software\Wow6432Node\Symantec\Symantec 끝점 Protection\CurrentVersion\PRODUCTNAME "Symantec Endpoint Protection" =**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

합니다 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 충족 되지 않으면 다음 검사가 권장 사항이 생성 됩니다.  

* Symantec 버전을 확인 > = 12:  레지스트리 위치: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* 실시간 보호 상태를 확인 합니다. **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* 서명 업데이트 상태를 확인 합니다. **HKLM\Software\Symantec\Symantec 끝점 Protection\CurrentVersion\public opstate\LatestVirusDefsDate < = 7 일**

* 전체 검사 상태를 확인 합니다. **HKLM:\Software\Symantec\Symantec 끝점 Protection\CurrentVersion\public opstate\LastSuccessfulScanDateTime < = 7 일**

* Symantec 12에 대 한 서명 버전에 서명 버전 번호를 경로 찾습니다. **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Symantec 14에 대 한 서명 버전 경로: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

레지스트리 경로:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Windows 용 McAfee endpoint protection

합니다 **"가상 컴퓨터에서 끝점 보호 솔루션을 설치 합니다."** 다음과 같은 검사가 충족 되지 않으면 권장 사항이 생성 됩니다.

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

합니다 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 다음과 같은 검사가 충족 되지 않으면 권장 사항이 생성 됩니다.

* McAfee 버전: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* 서명 버전을 찾습니다. **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* 서명 날짜를 찾습니다. **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* 검색 날짜를 찾습니다. **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

Microsoft 맬웨어 방지 확장 로그에서 사용할 수 있습니다.  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
