---
title: Azure Stack에 대 한 Azure Graph 통합 유효성 검사
description: Azure Stack 준비 검사를 사용 하 여 Azure Stack에 대 한 그래프 통합 유효성 검사.
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
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 43f30989fa09e711fc71941e7722dcd195212472
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416242"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Azure Stack에 대 한 그래프 통합 유효성 검사

환경의 Azure Stack을 사용 하 여 graph 통합에 대 한 준비가 되었는지 유효성을 검사 하려면 (AzsReadinessChecker) Azure Stack 준비 검사기 도구를 사용 합니다. 데이터 센터 통합을 시작 하기 전에 또는 Azure Stack 배포 하기 전에 graph 통합을 확인 합니다.

준비 상태 검사기의 유효성을 검사 합니다.

* Graph 통합에 대해 만든 서비스 계정 자격 증명에는 Active Directory 쿼리를 적절 한 권한이 있습니다.
* 합니다 *글로벌 카탈로그* 마스터가 됩니다 및 해결할 수 있습니다.
* KDC를 해결할 수 있습니다 및 마스터가 됩니다.
* 필요한 네트워크 연결이 설정 됩니다.

Azure Stack 데이터 센터 통합에 대 한 자세한 내용은 참조 하세요. [Azure Stack 데이터 센터 통합-Identity](azure-stack-integrate-identity.md)합니다.

## <a name="get-the-readiness-checker-tool"></a>준비 검사 도구

(AzsReadinessChecker) Azure Stack 준비 검사 도구의 최신 버전을 다운로드 합니다 [PowerShell 갤러리](https://aka.ms/AzsReadinessChecker)합니다.

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소가 준비 되어야 합니다.

**도구가 실행 되는 컴퓨터:**

* Windows 10 또는 Windows Server 2016 도메인 연결.
* PowerShell 5.1 이상 버전을 확인 하려면 다음 PowerShell 명령을 실행 하 고 검토 합니다 *주요* 버전 및 *부* 버전:  
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell 모듈입니다.
* 최신 버전의 [Microsoft Azure Stack 준비 검사](https://aka.ms/AzsReadinessChecker) 도구입니다.

**Active Directory 환경:**

* 사용자 이름 및 기존 Active Directory 인스턴스에서 그래프 서비스 계정의 암호를 식별 합니다.
* FQDN은 Active Directory 포리스트 루트를 식별 합니다.

## <a name="validate-the-graph-service"></a>Graph 서비스의 유효성을 검사합니다

1. 필수 구성 요소를 충족 하는 컴퓨터에서 관리자 PowerShell 프롬프트를 열고 AzsReadinessChecker를 설치 하려면 다음 명령을 실행:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. PowerShell 프롬프트에서 설정 하려면 다음 명령을 실행 합니다 *$graphCredential* 그래프 계정에 변수입니다. 바꿉니다 `contoso\graphservice` 를 사용 하 여 사용자 계정과 `domain\username` 형식입니다.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. PowerShell 프롬프트에서 그래프 서비스에 대 한 유효성 검사를 시작 하려면 다음 명령을 실행 합니다. 값을 지정 **-ForestFQDN** 포리스트 루트에 대 한 FQDN으로 합니다.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. 도구를 실행 한 후 출력을 검토 합니다. 그래프 통합 요구 사항에 대 한 상태 확인 인지 확인 합니다. 성공적인 유효성 검사는 다음 예제와 비슷합니다.

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

프로덕션 환경에서는 운영자의 워크스테이션에서 네트워크 연결을 테스트 하지 Azure Stack에 사용할 수 있는 연결을 완벽 하 게 나타냅니다. Azure Stack 스탬프의 공용 VIP 네트워크 id 통합 하는 데 LDAP 트래픽에 대 한 연결을 해야 합니다.

## <a name="report-and-log-file"></a>보고서 및 로그 파일

각 시간 유효성 검사 실행, 결과를 기록 **AzsReadinessChecker.log** 하 고 **AzsReadinessCheckerReport.json**합니다. PowerShell에서 유효성 검사 결과 사용 하 여 이러한 파일의 위치 표시 됩니다.

유효성 검사 파일 도움이 유효성 검사 문제를 조사 하거나 Azure Stack을 배포 하기 전에 상태를 공유 합니다. 두 파일의 각 후속 유효성 검사 결과 유지합니다. 보고서 id 구성의 배포 팀 확인을 제공합니다. 로그 파일에는 유효성 검사 문제를 조사 하는 배포 또는 지원의 팀원 데 도움이 됩니다.

기본적으로 두 파일에 기록 됩니다 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`합니다.

사용:

* **-OutputPath**: 합니다 *경로* 끝 다른 보고서 위치를 지정 하려면 실행된 명령에 매개 변수입니다.
* **-CleanReport**: 제거할 실행 명령의 끝에 있는 매개 변수 *AzsReadinessCheckerReport.json* 이전 보고서 정보입니다. 자세한 내용은 [Azure Stack 유효성 검사 보고서](azure-stack-validation-report.md)합니다.

## <a name="validation-failures"></a>유효성 검사 실패

유효성 검사에 실패 하는 경우 PowerShell 창에서 오류에 대 한 세부 정보가 나타납니다. 이 도구는 또한 정보를 기록 *AzsGraphIntegration.log*합니다.

## <a name="next-steps"></a>다음 단계

[준비 보고서를 보려면](azure-stack-validation-report.md)  
[일반 Azure Stack 통합 고려 사항](azure-stack-datacenter-integration.md)  
