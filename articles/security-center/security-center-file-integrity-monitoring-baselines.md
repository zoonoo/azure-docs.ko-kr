---
title: Azure Security Center에서 파일 무결성 모니터링
description: Azure Security Center에서 파일 무결성 모니터링과 기준선을 비교 하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 5e58904b9462a3e23d95eda9babad4fb47cde4a7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323745"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>FIM(파일 무결성 모니터링)을 사용하여 기준 비교

FIM (파일 무결성 모니터링)은 리소스의 중요 한 영역이 변경 될 때 사용자에 게 알려 하므로 권한 없는 작업을 조사 하 고 해결할 수 있습니다. FIM은 Windows 파일, Windows 레지스트리 및 Linux 파일을 모니터링 합니다.

이 항목에서는 파일 및 레지스트리에서 FIM을 사용 하도록 설정 하는 방법을 설명 합니다. FIM에 대 한 자세한 내용은 [Azure Security Center 파일 무결성 모니터링](security-center-file-integrity-monitoring.md)을 참조 하세요.

## <a name="why-use-fim"></a>FIM을 사용 하는 이유

운영 체제, 응용 프로그램 및 연결 된 구성은 리소스의 동작 및 보안 상태를 제어 합니다. 따라서 공격자는 리소스의 운영 체제를 추월 하거나 검색 하지 않고 작업을 실행 하기 위해 리소스를 제어 하는 파일을 대상으로 합니다.

실제로 PCI DSS & ISO 17799 등의 많은 규정 준수 표준에는 FIM 컨트롤을 구현 해야 합니다.  

## <a name="enable-built-in-recursive-registry-checks"></a>기본 제공 재귀 레지스트리 검사 사용

FIM 레지스트리 hive 기본값은 일반적인 보안 영역 내에서 재귀적 변경 내용을 모니터링 하는 편리한 방법을 제공 합니다.  예를 들어 악의적 사용자는 시작 또는 종료 시 실행을 구성 하 여 LOCAL_SYSTEM 컨텍스트에서 실행 되도록 스크립트를 구성할 수 있습니다.  이 유형의 변경 내용을 모니터링 하려면 기본 제공 검사를 사용 하도록 설정 합니다.  

![레지스트리](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 재귀 확인은 권장 되는 보안 하이브에만 적용 되 고 사용자 지정 레지스트리 경로에는 적용 되지 않습니다.  

## <a name="add-a-custom-registry-check"></a>사용자 지정 레지스트리 검사 추가

FIM 기준은 운영 체제 및 지원 응용 프로그램에 대해 알려진 정상 상태의 특성을 식별 하 여 시작 합니다.  이 예에서는 Windows Server 2008 이상에 대 한 암호 정책 구성에 초점을 둡니다.


|정책 이름                 | 레지스트리 설정|
|---------------------------------------|-------------|
|도메인 컨트롤러: 컴퓨터 계정 암호 변경 거부| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|도메인 구성원: 보안 채널 데이터를 디지털 암호화 또는 서명(항상)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|도메인 구성원: 보안 채널 데이터를 디지털 암호화(가능한 경우)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|도메인 구성원: 보안 채널 데이터 디지털 서명(가능한 경우)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|도메인 구성원: 컴퓨터 계정 암호 변경 사항 사용 안 함|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|도메인 구성원: 컴퓨터 계정 암호의 최대 사용 기간|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|도메인 구성원: 고급 세션 키 요청(Windows 2000 이상)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|네트워크 보안: NTLM 제한:이 도메인에서 NTLM 인증|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|네트워크 보안: NTLM 제한: 이 도메인에서 서버 예외 추가|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|네트워크 보안: NTLM 제한: 이 도메인에서 NTLM 인증 감사|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 다양 한 운영 체제 버전에서 지원 되는 레지스트리 설정에 대 한 자세한 내용은 [그룹 정책 설정 참조 스프레드시트](https://www.microsoft.com/download/confirmation.aspx?id=25250)를 참조 하세요.

*레지스트리 기준을 모니터링 하도록 FIM을 구성 하려면:*

1. **변경 내용 추적에 대 한 Windows 레지스트리 추가** 창에서 **windows 레지스트리 키** 텍스트 상자에 레지스트리 키를 입력 합니다.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![레지스트리에서 FIM 사용](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="track-changes-to-windows-files"></a>Windows 파일 변경 내용 추적

1. **변경 내용 추적에 대 한 Windows 파일 추가** 창의 **경로 입력** 텍스트 상자에 추적 하려는 파일이 들어 있는 폴더를 입력 합니다. 다음 그림의 예제에서는 **Contoso 웹 앱** 이 D:\에 있습니다. **ContosWebApp** 폴더 구조 내에 있는 드라이브입니다.  
1. 설정 클래스의 이름을 제공 하 고, 재귀를 사용 하도록 설정 하 고, 와일드 카드 (*) 접미사를 사용 하 여 최상위 폴더를 지정 하 여 사용자 지정 Windows 파일 항목을 만듭니다.

    ![파일에서 FIM 사용](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieve-change-data"></a>변경 데이터 검색

파일 무결성 모니터링 데이터는 Azure Log Analytics/ConfigurationChange 테이블 집합 내에 상주 합니다.  

 1. 리소스의 변경 내용에 대 한 요약을 검색 하는 시간 범위를 설정 합니다.
다음 예제에서는 레지스트리와 파일의 범주에 있는 지난 14 일의 모든 변경 내용을 검색 합니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 레지스트리 변경 내용에 대 한 세부 정보를 보려면:

    1. **Where** 절에서 **파일** 을 제거 합니다. 
    1. 요약 줄을 제거 하 고 순서 절로 바꿉니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

보고서는 보관을 위해 CSV로 내보내거나 Power BI 보고서로 채널로 수 있습니다.  

![FIM 데이터](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)