---
title: Azure Security Center에서 파일 무결성 모니터링
description: Azure 보안 센터에서 기준을 파일 무결성 모니터링과 비교하는 방법을 알아봅니다.
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
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664403"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>FIM(파일 무결성 모니터링)을 사용하여 기준 비교

FIM(파일 무결성 모니터링)은 리소스의 중요한 영역에 변경 사항이 발생할 때 이를 알려주므로 승인되지 않은 활동을 조사하고 해결할 수 있습니다. FIM은 Windows 파일, Windows 레지스트리 및 Linux 파일을 모니터링합니다.

이 항목에서는 파일 및 레지스트리에서 FIM을 사용하도록 설정하는 방법을 설명합니다. FIM에 대한 자세한 내용은 [Azure 보안 센터의 파일 무결성 모니터링을](security-center-file-integrity-monitoring.md)참조하십시오.

## <a name="why-use-fim"></a>FIM을 사용해야 하는 이유

운영 체제, 응용 프로그램 및 관련 구성은 리소스의 동작 및 보안 상태를 제어합니다. 따라서 공격자는 리소스의 운영 체제를 추월하거나 감지되지 않고 활동을 실행하기 위해 리소스를 제어하는 파일을 대상으로 합니다.

실제로 PCI-DSS & ISO 17799와 같은 많은 규정 준수 표준에는 FIM 제어 구현이 필요합니다.  

## <a name="enable-built-in-recursive-registry-checks"></a>기본 제공 재귀 레지스트리 검사 사용

FIM 레지스트리 하이브 기본값은 공통 보안 영역 내에서 재귀 변경 사항을 모니터링하는 편리한 방법을 제공합니다.  예를 들어, adversa는 시작 또는 종료 시 실행을 구성하여 LOCAL_SYSTEM 컨텍스트에서 실행하도록 스크립트를 구성할 수 있습니다.  이 유형의 변경 내용을 모니터링하려면 기본 제공 확인을 사용하도록 설정합니다.  

![레지스트리](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 재귀 검사는 권장되는 보안 하이브에만 적용되며 사용자 지정 레지스트리 경로에만 적용되지 않습니다.  

## <a name="adding-a-custom-registry-check"></a>사용자 지정 레지스트리 검사 추가

FIM 기준은 운영 체제및 지원 응용 프로그램에 대해 알려진 상태의 특성을 식별하는 것으로 시작합니다.  이 예제에서는 Windows Server 2008 이상에 대한 암호 정책 구성에 중점을 둡니다.


|정책 이름                 | 레지스트리 설정|
|---------------------------------------|-------------|
|도메인 컨트롤러: 컴퓨터 계정 암호 변경 거부| 기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\거부암호변경|
|도메인 구성원: 보안 채널 데이터를 디지털 암호화 또는 서명(항상)|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\요구서명또는봉인|
|도메인 구성원: 보안 채널 데이터를 디지털 암호화(가능한 경우)|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\SealSecureChannel|
|도메인 구성원: 보안 채널 데이터 디지털 서명(가능한 경우)|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\SignSecureChannel|
|도메인 구성원: 컴퓨터 계정 암호 변경 사항 사용 안 함|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\사용 안 함 암호변경|
|도메인 구성원: 컴퓨터 계정 암호의 최대 사용 기간|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\최대암호|
|도메인 구성원: 고급 세션 키 요청(Windows 2000 이상)|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\요구StrongKey|
|네트워크 보안: NTLM 제한: 이 도메인의 NTLM 인증|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\제한NTLMInDomain|
|네트워크 보안: NTLM 제한: 이 도메인에서 서버 예외 추가|기계\시스템\현재 제어셋\서비스 \넷로그온\매개 변수\DCAllowedNTLMServers|
|네트워크 보안: NTLM 제한: 이 도메인의 NTLM 인증 감사|기계\시스템\현재 제어세트\서비스 \넷로그온\매개 변수\AuditNTLMInDomain|

> [!NOTE]
> 다양한 운영 체제 버전에서 지원하는 레지스트리 설정에 대한 자세한 내용은 [그룹 정책 설정 참조 스프레드시트를](https://www.microsoft.com/download/confirmation.aspx?id=25250)참조하십시오.

*레지스트리 기준을 모니터링하도록 FIM을 구성하려면 다음을 수행하십시오.*

1. 변경 **추적을 위한 Windows 레지스트리 추가** 창에서 Windows 레지스트리 **키** 텍스트 상자에 레지스트리 키를 입력합니다.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![레지스트리에서 FIM 사용](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows 파일에 대한 변경 내용 추적

1. 변경 **내용 추적을 위한 Windows 파일 추가** 창에서 경로 텍스트 **입력** 상자에 추적할 파일이 포함된 폴더를 입력합니다. 다음 그림의 예에서 **Contoso 웹 앱은** D:\ **ContosWebApp** 폴더 구조 내에서 드라이브를 구동할 수 있습니다.  
1. 설정 클래스의 이름을 제공하고, 재지정을 활성화하고, 와일드카드(*) 접미사가 있는 최상위 폴더를 지정하여 사용자 지정 Windows 파일 항목을 만듭니다.

    ![파일에 FIM 사용 설정](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>변경 데이터 검색

파일 무결성 모니터링 데이터는 Azure 로그 분석/ ConfigurationChange 테이블 집합 내에 있습니다.  

 1. 시간 범위를 설정하여 리소스별 변경 요약을 검색합니다.
다음 예제에서는 레지스트리 및 파일 범주에서 지난 14일 동안의 모든 변경 내용을 검색합니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 레지스트리 변경 사항에 대한 세부 정보를 보려면 다음을 수행하십시오.

    1. **where** 절에서 **파일** 제거, 
    1. 요약 줄을 제거하고 순서 절로 바꿉을 바꿉으로 바꿉습니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

보고서를 CSV로 내보내 보관 및/또는 Power BI 보고서로 채널화할 수 있습니다.  

![FIM 데이터](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)