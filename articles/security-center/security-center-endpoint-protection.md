---
title: Azure 보안 센터의 엔드포인트 보호 권장 사항
description: 엔드포인트 보호 솔루션을 검색하고 정상으로 식별하는 방법.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208545"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 보안 센터의 엔드포인트 보호 평가 및 권장 사항

Azure 보안 센터는 [지원되는](security-center-services.md#endpoint-supported) Endpoint 보호 솔루션 버전의 상태 평가를 제공합니다. 이 문서에서는 보안 센터가 다음 두 가지 권장 사항을 생성하는 시나리오를 설명합니다.

* **가상 시스템에 엔드포인트 보호 솔루션 설치**
* **머신의 엔드포인트 보호 상태 문제 해결**

## <a name="windows-defender"></a>Windows Defender

* 보안 센터는 [Get-MpComputerStatus가](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) 실행되고 결과가 **AMServiceEnabled: False인** 경우 **"가상 시스템에 엔드포인트 보호 솔루션 설치"를** 권장합니다.

* 보안 센터는 [Get-MpComputerStatus가](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) 실행되고 다음 중 어느 이라도 발생할 때 **"컴퓨터에서 엔드포인트 보호 상태 해결"을** 권장합니다.

  * 다음 속성은 false입니다.

    **AMService 사용 가능**

    **안티스파이웨어 사용 가능**

    **실시간 보호 사용 가능**

    **동작모니터사용**

    **이오아브프로텍션지원**

    **온액세스프로텍션사용**

  * 다음 속성 중 하나 또는 둘 다 7 개 이상인 경우.

    **안티스파이웨어 시그니처**

    **바이러스 백신 서명**

## <a name="microsoft-system-center-endpoint-protection"></a>마이크로소프트 시스템 센터 엔드포인트 보호

* 보안 센터는 **SCEPMpModule ("$env:ProgramFiles\Microsoft 보안 클라이언트\MpProvider\MpProvider.psd1")를** 가져올 때 **"가상 머신에 엔드포인트 보호 솔루션을 설치"하고** **AMServiceEnabled = false를** 사용하여 **Get-MProtComputerStatus** 결과를 실행하는 것이 좋습니다.

* 보안 센터는 **Get-MprotComputerStatus가** 실행되고 다음 중 어느 한 가지라도 발생할 때 **"컴퓨터에서 엔드포인트 보호 상태 해결"을** 권장합니다.

    * 다음 속성 중 하나 이상이 false입니다.

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * 다음 서명 업데이트 중 하나 또는 둘 다가 7과 같거나 큰 경우. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>트렌드 마이크로

* 보안 센터는 다음 검사 중 어느 한 가지도 충족되지 않을 때 **"가상 시스템에 엔드포인트 보호 솔루션 설치"를** 권장합니다.
    * **HKLM:\SOFTWARE\TrendMicro\깊은 보안 에이전트가** 존재합니다.
    * **HKLM:\SOFTWARE\TrendMicro\깊은 보안 에이전트\설치폴더** 존재
    * **dsa_query.cmd** 파일은 설치 폴더에 있습니다.
    * **Component.am.mode로** **dsa_query.cmd** 결과 실행: 켜기 - 트렌드 마이크로 딥 시큐리티 에이전트 감지

## <a name="symantec-endpoint-protection"></a>시만텍 엔드포인트 보호
보안 센터는 다음 검사 중 어느 한 가지도 충족되지 않을 때 **"가상 시스템에 엔드포인트 보호 솔루션 설치"를** 권장합니다.

* **HKLM:\소프트웨어\시만텍\시만텍 엔드포인트 보호\현재버전\PRODUCTNAME = "시만텍 엔드포인트 보호"**

* **HKLM:\소프트웨어\시만텍\시만텍 엔드포인트 보호\현재버전\공용-opstate\ASRunningStatus = 1**

또는

* **HKLM:\소프트웨어\Wow6432Node\시만텍\시만텍 엔드포인트 보호\현재버전\PRODUCTNAME = "시만텍 엔드포인트 보호"**

* **HKLM:\소프트웨어\Wow6432Node\시만텍\시만텍 엔드포인트 보호\현재버전\공용 opstate\ASRunningStatus = 1**

보안 센터는 다음 검사 중 어느 이중도 충족되지 않을 때 **"컴퓨터의 엔드포인트 보호 상태 문제 해결"을** 권장합니다.

* 확인 시만텍 버전 >= 12: 레지스트리 위치: **HKLM:\소프트웨어\시만텍\시만텍 엔드포인트 보호\현재버전" -값 "PRODUCTVERSION"**

* 실시간 보호 상태 확인: **HKLM:\소프트웨어\Wow6432Node\시만텍=시만텍 엔드포인트 보호\AV\스토리지\파일 시스템\실시간 스캔\OnOff =1**

* 서명 업데이트 상태 확인: **HKLM\소프트웨어\시만텍\시만텍 엔드포인트 보호\현재버전\공개 opstate\LatestVirusDefsDate <= 7일**

* 전체 검사 상태 확인: **HKLM:\소프트웨어\시만텍\시만텍 엔드포인트 보호\현재버전\공개-opstate\LastSuccessfulScanDateTime <= 7일**

* 시만텍 12: **레지스트리 경로+ "현재버전\SharedDefs" -값 "SRTSP"에** 대한 서명 버전 번호 찾기 

* 시만텍 14의 서명 버전 경로: **레지스트리 경로+ "현재버전\SharedDefs\SDSDefs" -값 "SRTSP"**

레지스트리 경로:

* **"HKLM:\소프트웨어\시만텍\시만텍 엔드포인트 보호" + $Path;**
* **"HKLM:\소프트웨어\Wow6432Node\시만텍\시만텍 엔드포인트 보호" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Windows용 McAfee 엔드포인트 보호

보안 센터는 다음 검사 중 어느 한 가지도 충족되지 않을 때 **"가상 시스템에 엔드포인트 보호 솔루션 설치"를** 권장합니다.

* **HKLM:\SOFTWARE\McAfee\엔드포인트\AV\제품버전** 이 존재

* **HKLM:\소프트웨어\맥아피\AV솔루션\맥쉴드글로벌\글로벌\인에이블로아스 = 1**

보안 센터는 다음 검사 중 어느 이중도 충족되지 않을 때 **"컴퓨터의 엔드포인트 보호 상태 문제 해결"을** 권장합니다.

* McAfee 버전: **HKLM:\SOFTWARE\McAfee\엔드포인트\AV\제품버전 >= 10**

* 서명 버전 찾기: **HKLM:\소프트웨어\McAfee\AV솔루션\DS\DS -값 "dwContentMajorVersion"**

* 서명 날짜 찾기: **HKLM:\소프트웨어\McAfee\AVSolution\DS\DS -값 "szContentCreationDate" >= 7 일**

* 스캔 날짜 찾기: **HKLM:\소프트웨어\McAfee\ENDpoint\AV\ODS -값 "LastFullScanOdsRunTime" >= 7일**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Linux 위협 방지를 위한 McAfee 엔드포인트 보안 

보안 센터는 다음 검사 중 어느 한 가지도 충족되지 않을 때 **"가상 시스템에 엔드포인트 보호 솔루션 설치"를** 권장합니다.

- 파일 **/옵트/isec/ens/위협 방지/빈/이세카프** 출구 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** 출력은 다음과 됩니다: **McAfee 이름 = Linux 위협 방지 및 McAfee 버전 >= 10**

보안 센터는 다음 검사 중 어느 이중도 충족되지 않을 때 **"컴퓨터의 엔드포인트 보호 상태 문제 해결"을** 권장합니다.

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"는** **빠른 스캔, 전체 스캔** 및 둘 다 <= 7일을 반환합니다.

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"는** **DAT 및 엔진 업데이트 시간을 반환하며** 둘 다 <= 7일

- **"/opt/isec/ens/threatprevention/bin/isecav--getoasconfig --요약"** 반환 **에 액세스 스캔** 상태

## <a name="sophos-antivirus-for-linux"></a>리눅스에 대한 소포스 안티 바이러스 

보안 센터는 다음 검사 중 어느 한 가지도 충족되지 않을 때 **"가상 시스템에 엔드포인트 보호 솔루션 설치"를** 권장합니다.

- 파일 **/옵트 /소포스 - AV / 빈 / savdstatus** 종료 또는 사용자 정의 위치 **"읽기 링크 $(어떤 savscan)"에** 대한 검색

- **"/opt/sophos-av/bin/savdstatus --version"은** 소포스 이름 = **소포스 안티 바이러스 및 소포스 버전 >= 9를** 반환합니다.

보안 센터는 다음 검사 중 어느 이중도 충족되지 않을 때 **"컴퓨터의 엔드포인트 보호 상태 문제 해결"을** 권장합니다.

- **"/옵트/소포스-AV/빈/자블로그 --maxage=7 | grep -i "예약 된 스캔 . \* 완료" | tail -1",** 값을 반환합니다.

- **"/옵트/소포스-AV/빈/자블로그 --maxage=7 | grep "스캔 완료"** | 꼬리 -1", 값을 반환합니다.

- **"/opt/sophos-av/bin/savdstatus --lastupdate" 반환 lastUpdate,** <= 7 일 

- **"/opt/sophos-av/bin/savdstatus -v"는** **"온액세스 스캔이 실행 중"과** 같습니다. 

- **"/옵트/소포스-av/빈/savconfig Get LiveProtection"** 반환 사용

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

마이크로소프트 맬웨어 방지 확장 로그에서 사용할 수 있습니다.: **%Systemdrive%\WindowsAzure\Logs\플러그인\마이크로소프트.Azure.Security.IaaSAntimalware(또는 PaaSAntimalware)\1.5.5.x (버전 번호)\명령 실행.log**

### <a name="support"></a>고객 지원팀

자세한 내용은 [MSDN Azure 및 스택 오버플로 포럼의 Azure 전문가에게 문의하십시오.](https://azure.microsoft.com/support/forums/) 또는 Azure 지원 인시던트를 제출합니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.