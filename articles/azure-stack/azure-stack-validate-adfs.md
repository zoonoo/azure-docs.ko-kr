---
title: Azure Stack에 대 한 AD FS 통합 유효성 검사
description: Azure Stack 준비 검사를 사용 하 여 Azure Stack에 대 한 AD FS 통합 유효성 검사.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 2200b9a48d7f83d6785c8dbb4a7b02be52fca75a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241069"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Azure Stack에 대 한 AD FS 통합 유효성 검사

환경의 Azure Stack을 사용 하 여 Active Directory Federation Services (AD FS) 통합에 대 한 준비가 되었는지 유효성을 검사 하려면 (AzsReadinessChecker) Azure Stack 준비 검사기 도구를 사용 합니다. 데이터 센터 통합을 시작 하기 전에 또는 Azure Stack 배포 하기 전에 AD FS 통합을 확인 합니다.

준비 상태 검사기의 유효성을 검사 합니다.

* 합니다 *페더레이션 메타 데이터* 페더레이션에 대 한 유효한 XML 요소를 포함 합니다.
* 합니다 *AD FS SSL 인증서* 검색할 수 있습니다 및 빌드해야 체인 신뢰 수 있습니다. 스탬프에서 AD FS SSL 인증서 체인을 신뢰 해야 합니다. 동일한 인증서를 서명 해야 합니다 *기관* 신뢰할 수 있는 루트 인증 기관 파트너 또는 Azure Stack 배포 인증서를 사용 합니다. 신뢰할 수 있는 루트 인증 기관 파트너의 전체 목록을 참조 하세요 [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)합니다.
* 합니다 *AD FS 서명 인증서* 신뢰할 수 있고 하지 임박한 만료 됩니다.

Azure Stack 데이터 센터 통합에 대 한 자세한 내용은 참조 하세요. [Azure Stack 데이터 센터 통합-Identity](azure-stack-integrate-identity.md)합니다.

## <a name="get-the-readiness-checker-tool"></a>준비 검사 도구

(AzsReadinessChecker) Azure Stack 준비 검사 도구의 최신 버전을 다운로드 합니다 [PowerShell 갤러리](https://aka.ms/AzsReadinessChecker)합니다.  

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소가 준비 되어야 합니다.

**도구가 실행 되는 컴퓨터:**

* Windows 10 또는 Windows Server 2016 도메인 연결.
* PowerShell 5.1 이상 버전을 확인 하려면 다음 PowerShell 명령을 실행 하 고 검토 합니다 *주요* 버전 및 *부* 버전:  
   > `$PSVersionTable.PSVersion`
* 최신 버전의 [Microsoft Azure Stack 준비 검사](https://aka.ms/AzsReadinessChecker) 도구입니다.

**Active Directory Federation Services 환경:**

메타 데이터의 다음 형식 중 하나 이상이 필요합니다.

* AD FS 페더레이션 메타 데이터에 대 한 URL입니다. 예는 `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`입니다.
* 페더레이션 메타 데이터 XML 파일입니다. 예로 FederationMetadata.xml입니다.

## <a name="validate-ad-fs-integration"></a>AD FS 통합 유효성 검사

1. 필수 구성 요소를 충족 하는 컴퓨터에서 관리자 PowerShell 프롬프트를 열고 AzsReadinessChecker를 설치 하려면 다음 명령을 실행:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. PowerShell 프롬프트에서 유효성 검사를 시작 하려면 다음 명령을 실행 합니다. 값을 지정 **-CustomADFSFederationMetadataEndpointUri** 페더레이션 메타 데이터 URI로 합니다.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. 도구를 실행 한 후 출력을 검토 합니다. AD FS 통합 요구 사항에 대 한 상태 확인 인지 확인 합니다. 성공적인 유효성 검사는 다음 예제와 비슷합니다.

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

프로덕션 환경에서는 운영자의 워크스테이션에서 신뢰의 인증서 체인을 테스트 하지 않습니다 Azure Stack 인프라의 PKI 트러스트 상태에 대 한 완벽 하 게 나타냅니다. Azure Stack 스탬프의 공용 VIP 네트워크 PKI 인프라가 CRL에 대 한 연결이 필요합니다.

## <a name="report-and-log-file"></a>보고서 및 로그 파일

각 시간 유효성 검사 실행, 결과를 기록 **AzsReadinessChecker.log** 하 고 **AzsReadinessCheckerReport.json**합니다. PowerShell에서 유효성 검사 결과 사용 하 여 이러한 파일의 위치 표시 됩니다.

유효성 검사 파일 도움이 유효성 검사 문제를 조사 하거나 Azure Stack을 배포 하기 전에 상태를 공유 합니다. 두 파일의 각 후속 유효성 검사 결과 유지합니다. 보고서 id 구성의 배포 팀 확인을 제공합니다. 로그 파일에는 유효성 검사 문제를 조사 하는 배포 또는 지원의 팀원 데 도움이 됩니다.

기본적으로 두 파일에 기록 됩니다 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`합니다.

사용:

* **-OutputPath**: 합니다 *경로* 끝 다른 보고서 위치를 지정 하려면 실행된 명령에 매개 변수입니다.
* **-CleanReport**: 이전 보고서 정보의 AzsReadinessCheckerReport.json 지우려면 실행 명령의 끝에 대 한 매개 변수입니다. 자세한 내용은 [Azure Stack 유효성 검사 보고서](azure-stack-validation-report.md)합니다.

## <a name="validation-failures"></a>유효성 검사 실패

유효성 검사에 실패 하는 경우 PowerShell 창에서 오류에 대 한 세부 정보가 나타납니다. 이 도구는 또한 정보를 기록 *AzsReadinessChecker.log*합니다.

다음 예제에서는 일반적인 유효성 검사 오류에 지침을 제공 합니다.

### <a name="command-not-found"></a>명령을 찾을 수 없음

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**원인**: PowerShell Autoload 올바르게 준비 검사기 모듈을 로드 하지 못했습니다.

**해결 방법**: 준비 검사 모듈을 명시적으로 가져옵니다. PowerShell 및 업데이트에 다음 코드를 붙여 복사 \<버전\> 현재 설치 된 버전 번호를 사용 하 여 합니다.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>다음 단계

[준비 보고서를 보려면](azure-stack-validation-report.md)  
[일반 Azure Stack 통합 고려 사항](azure-stack-datacenter-integration.md)  
