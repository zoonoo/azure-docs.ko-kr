---
title: Azure Security Center에서 파일 무결성 모니터링 사용 하 여 기준 비교 | Microsoft Docs
description: Azure Security Center에서 파일 무결성 모니터링 사용 하 여 기준 비교 하는 방법에 알아봅니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358440"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>파일 무결성 모니터링 (FIM)를 사용 하 여 기준 비교

파일 무결성 모니터링 (FIM)은 변경이 발생 리소스에서 중요 한 영역을 조사 하 고 권한 없는 활동을 해결할 수 있도록 알려 줍니다. FIM에는 Windows, Windows 레지스트리 파일과 Linux 모니터링합니다.

이 항목에서는 파일 및 레지스트리에 FIM을 사용 하도록 설정 하는 방법에 설명 합니다. FIM에 대 한 자세한 내용은 참조 하세요. [Azure Security Center에서 파일 무결성 모니터링](security-center-file-integrity-monitoring.md)합니다.

## <a name="why-use-fim"></a>FIM을 사용 하는 이유

운영 체제, 응용 프로그램 및 관련된 구성 리소스의 동작 및 보안 상태를 제어 합니다. 따라서 공격자가 대상 리소스의 운영 체제 추월 및/또는 검색 되지 않고 작업을 실행 하기 위해 리소스를 제어 하는 파일입니다.

사실, ISO 17799 및 PCI-DSS와 같은 많은 규정 준수 표준을 FIM 컨트롤을 구현 해야 합니다.  

## <a name="enable-built-in-recursive-registry-checks"></a>기본 제공 재귀 레지스트리 검사를 사용 하도록 설정

FIM 레지스트리 hive 기본값에는 일반적인 보안 영역에서 재귀 변경 내용을 모니터링 하는 편리한 방법을 제공 합니다.  예를 들어, 악의적 사용자가 시작 또는 종료 시 실행을 구성 하 여 LOCAL_SYSTEM 컨텍스트에서 실행할 스크립트를 구성할 수 있습니다.  이 유형의 변경 내용을 모니터링 하려면 기본 제공 확인을 사용 하도록 설정 합니다.  

![레지스트리](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 재귀 검사 사용자 지정 레지스트리 경로 아니라 권장 되는 보안 하이브에만 적용 됩니다.  

## <a name="adding-a-custom-registry-check"></a>사용자 지정 레지스트리 검사를 추가합니다.

FIM 기준 운영 체제에 대 한 정상 작동이 확인 된 상태의 특성을 식별 하 고 응용 프로그램을 지원 하 여 시작 합니다.  예를 들어 Windows Server 2008 이상 암호 정책 구성에 대해 살펴볼 것입니다.


|정책 이름                 | 레지스트리 설정|
|---------------------------------------|-------------|
|도메인 컨트롤러: 컴퓨터 계정 암호 변경 거부| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|도메인 구성원: 디지털 방식으로 데이터 암호화 또는 서명 보안 채널 (항상)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|도메인 구성원: (가능한 경우) 보안 채널 데이터를 디지털 암호화|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|도메인 구성원: 보안 (가능한 경우) 채널 데이터 디지털 서명|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|도메인 구성원: 컴퓨터 계정 암호 변경을 사용 하지 않도록 설정|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|도메인 구성원: 최대 컴퓨터 계정 암호 사용 기간|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|도메인 구성원: 고급 (Windows 2000 또는 그 이상) 세션 키 요청|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|네트워크 보안: NTLM 제한:  이 도메인에서 NTLM 인증|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|네트워크 보안: NTLM 제한: 이 도메인에서 서버 예외 추가|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|네트워크 보안: NTLM 제한: 이 도메인에서 NTLM 인증 감사|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 다양 한 운영 체제 버전에서 지원 되는 레지스트리 설정에 대 한 자세한 내용은 참조는 [그룹 정책 설정 참조 스프레드시트](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250)합니다.

*레지스트리 기준을 모니터링 하는 FIM을 구성 합니다.*

1. 에 **변경 내용 추적에 대해 Windows 레지스트리 추가** 창에서 합니다 **Windows 레지스트리 키** 텍스트 상자에 레지스트리 키를 입력 합니다.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![FIM에서 레지스트리를 사용 하도록 설정](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows 파일 변경 내용 추적

1. 에 **변경 내용 추적에 대해 Windows 파일 추가** 창에서 합니다 **경로 입력** 텍스트 상자에 추적 하려는 파일을 포함 하는 폴더를 입력 합니다. 다음 그림에서는 예제의 **Contoso 웹 앱** 는 D:\에 있는 내에서 드라이브를 **ContosWebApp** 폴더 구조입니다.  
1. Setting 클래스의 이름을 제공 하 고 재귀를 사용 하도록 설정 하면 와일드 카드 (*) 접미사를 사용 하 여 최상위 폴더를 지정 하 여 사용자 지정 Windows 파일 항목을 만듭니다.

    ![파일에서 FIM을 사용 하도록 설정](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>변경 데이터 검색

파일 무결성 모니터링 Azure Log Analytics 내에서 데이터가 있는 ConfigurationChange 테이블 설정 합니다.  

 1. 리소스에서 변경 내용이 요약을 검색 하는 시간 범위를 설정 합니다.
다음 예에서 레지스트리 및 파일의 범주에서 지난 14 일 이내에 모든 변경 내용을 검색:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 레지스트리 변경 내용의 세부 정보를 보려면:

    1. 제거할 **파일** 에서 합니다 **여기서** 절 
    1. 요약 줄을 제거 하 고 정렬 절을 바꿉니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

보고서 보관 하거나 Power BI 보고서 스레드로 전달에 대 한 CSV로 내보낼 수 있습니다.  

![FIM 데이터](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)