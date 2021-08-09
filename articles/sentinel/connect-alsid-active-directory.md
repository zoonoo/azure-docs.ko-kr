---
title: Active Directory용 Alsid를 Azure Sentinel에 연결 | Microsoft Docs
description: Active Directory용 Alsid 커넥터를 사용하여 Alsid 로그를 Azure Sentinel로 끌어오는 방법에 대해 알아봅니다. 통합 문서에서 Alsid 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566795"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>AD(Active Directory)용 Alsid를 Azure Sentinel에 연결

> [!IMPORTANT]
> Active Directory용 Alsid 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 AD용 Alsid 솔루션을 Azure Sentinel에 연결하는 방법을 설명합니다. Active Directory용 Alsid 데이터 커넥터를 사용하면 AD용 Alsid 로그를 Azure Sentinel에 쉽게 연결할 수 있으므로, 통합 문서에서 데이터를 보고, 해당 데이터를 쿼리하여 사용자 지정 경고를 만들고, 데이터를 통합하여 조사를 개선할 수 있습니다. AD용 Alsid와 Azure Sentinel 간의 통합에는 Log Analytics 에이전트가 설치된 Syslog 서버를 사용합니다. 또한 Kusto 함수를 기반으로 하는 사용자 지정 로그 파서를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure Sentinel 작업 영역에 대한 쓰기 권한이 있어야 합니다.

- AD용 Alsid 솔루션은 Syslog를 통해 로그를 내보내도록 구성해야 합니다.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Syslog 에이전트를 통해 AD용 Alsid 로그를 Azure Sentinel에 보내기

Syslog 에이전트를 통해 Azure Sentinel 작업 영역에 Syslog 메시지를 전달하도록 AD용 Alsid를 구성합니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Active Directory용 Alsid(미리 보기)** 커넥터를 선택한 다음, **커넥터 페이지를 엽니다**.

1. **Active Directory용 Alsid** 커넥터 페이지의 지침을 따릅니다.

    1. Syslog 서버 구성

        1. Syslog 서버가 아직 없는 경우, 로그를 보낼 AD용 Alsid에 대한 Linux Syslog 서버를 만듭니다. Azure Sentinel은 **rsyslog** 및 **syslog-ng** 디먼을 지원합니다. 

        1. AD용 Alsid 로그를 별도의 파일로 출력하도록 Syslog 서버를 구성합니다.

    1. Syslog 서버에 로그를 보내도록 AD용 Alsid를 구성

        1. **AD용 Alsid** 포털에서 ‘시스템’, ‘구성’, *Syslog* 로 차례로 이동합니다.  여기에서 Syslog 서버에 대한 Syslog 경고를 새로 만들 수 있습니다. 원격 서버의 경우 Linux 에이전트를 설치한 Linux 머신의 IP 주소를 사용합니다.

        1. 로그가 서버에서 별도의 파일로 올바르게 수집되었는지 확인합니다(이렇게 하기 위해 AD용 Alsid의 *Syslog* 경고 구성에서 **구성 테스트** 단추를 사용할 수 있습니다).

    1. Linux용 Log Analytics 에이전트 설치 및 온보딩

        - Azure Linux VM 또는 비 Azure Linux 머신(실제 또는 가상)을 선택합니다. 화면의 링크와 지침을 따릅니다. 자세한 내용은 [Linux 머신 또는 어플라이언스 구성](connect-syslog.md#configure-your-linux-machine-or-appliance)을 참조하세요.

    1. Log Analytics 에이전트에서 수집할 로그 구성

        - 작업 영역 고급 설정 구성에서 시설 및 심각도를 선택합니다.

            1. 커넥터 페이지에서 **작업 영역 고급 설정 구성 열기 >** 링크를 클릭합니다.

            1. **고급 설정** 화면에서 **데이터** 를 선택한 다음, **사용자 지정 로그** 를 선택합니다.

            1. **내 Linux 머신에 아래 구성 적용** 확인란을 표시하고 **추가** 를 클릭합니다.

            1. **파일 선택** 을 클릭하여 Syslog 서버를 실행하는 Linux 머신에서 AD용 Alsid 샘플 Syslog 파일을 업로드하고 **다음** 을 클릭합니다.

            1. **레코드 구분 기호 설정** 이 **새 줄** 로 설정되었는지 확인하고 **다음** 을 클릭합니다.

            1. **Linux** 를 선택하고 Syslog 파일의 파일 경로를 입력한 후 **+** 를 클릭하고 **다음** 을 클릭합니다.

            1. 이름 필드에서 _CL 접미사 앞에 *AlsidForADLog* 를 입력한 다음, **완료** 를 클릭합니다.
    
로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 *AlsidForADLog_CL* 테이블에 있는 **사용자 지정 로그** 아래 **로그** 에 표시됩니다.

이 데이터 커넥터는 정상적으로 작동하기 위해 Kusto 함수를 기반으로 하는 파서를 사용합니다. 다음 단계에 따라 쿼리 및 통합 문서에서 사용할 **afad_parser** Kusto 함수를 설정합니다.

1. Azure Sentinel 탐색 메뉴에서 **로그** 를 선택합니다.

1. 다음 쿼리를 복사하여 쿼리 창에 붙여넣습니다.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. **저장** 드롭다운을 클릭한 다음, **저장** 을 클릭합니다. **저장** 패널에서

    1. **이름** 으로 **afad_parser** 를 입력합니다.

    1. **다른 이름으로 저장** 에서 **함수** 를 선택합니다.

    1. **함수 별칭** 으로 **afad_parser** 를 입력합니다.

    1. **범주** 로 **함수** 를 입력합니다.

    1. **저장** 을 클릭합니다.

    일반적으로 함수 앱이 활성화되려면 10~15분이 걸립니다.

이제 AD용 Alsid 데이터를 쿼리할 준비가 되었습니다. 쿼리 창 상단에 `afad_parser`를 입력하면 됩니다.

더 많은 쿼리 샘플은 커넥터 페이지에서 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AD용 Alsid를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
