---
title: Azure Sentinel에서 DNS 데이터 연결 | Microsoft Docs
description: DNS 머신에 에이전트를 설치하여 Windows에서 실행되는 모든 DNS(도메인 이름 서버)를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 4b6ad04afce5fb4ead5575d47333525c0f5f8a13
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110075201"
---
# <a name="connect-your-domain-name-server"></a>도메인 이름 서버 연결

> [!IMPORTANT]
> Azure Sentinel의 DNS 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!CAUTION]
> DNS 솔루션을 사용하도록 설정하면 대상 컴퓨터에서 높은 CPU 사용량이 발생할 수 있습니다. 위에서 언급했듯이 이 커넥터는 프로덕션 환경에서 사용하지 않는 것이 좋습니다. 일반 공급을 위해 이 커넥터의 안정적인 버전을 사용하고 있습니다.

Windows에서 실행되는 모든 DNS(도메인 이름 서버)를 Azure Sentinel에 연결할 수 있습니다. 이 작업은 DNS 머신에 에이전트를 설치하여 수행합니다. DNS 로그를 사용하면 DNS 서버에서 분석 및 감사 로그와 기타 관련 데이터를 수집, 분석 및 상호 연결하여 조직의 DNS 인프라에 대한 보안, 성능 및 작업 관련 인사이트를 얻을 수 있습니다.

DNS 로그 연결을 사용하도록 설정하면 다음을 수행할 수 있습니다.
- 악의적인 도메인 이름을 확인하려는 클라이언트 식별
- 부실 리소스 레코드 식별
- 자주 쿼리되는 도메인 이름 및 대화량이 많은 DNS 클라이언트 식별
- DNS 서버에서 요청 부하 보기
- 동적 DNS 등록 오류 보기

## <a name="connected-sources"></a>연결된 소스

다음 표는 이 솔루션이 지원하는 연결된 원본을 설명합니다.

| **연결된 원본** | **지원** | **설명** |
| --- | --- | --- |
| [Windows 에이전트](../azure-monitor/agents/agent-windows.md) | 예 | 솔루션이 Windows 에이전트에서 DNS 정보를 수집합니다. |
| [Linux 에이전트](../azure-monitor/vm/quick-collect-linux-computer.md) | 예 | 솔루션이 직접 Linux 에이전트에서 DNS 정보를 수집하지 않습니다. |
| [System Center Operations Manager 관리 그룹](../azure-monitor/agents/om-agents.md) | 예 | 솔루션이 연결된 Operations Manager 관리 그룹의 에이전트에서 DNS 정보를 수집합니다. Operations Manager 에이전트에서 Azure Monitor로 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |
| [Azure Storage 계정](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | 예 | Azure Storage가 솔루션에서 사용되지 않습니다. |

### <a name="data-collection-details"></a>데이터 수집 세부 정보

솔루션이 Log Analytics가 설치된 DNS 서버에서 DNS 인벤토리 및 DNS 이벤트 관련 데이터를 수집합니다. DNS Powershell cmdlet을 실행하여 DNS 서버, 영역 및 리소스 레코드 수와 같은 인벤토리 관련 데이터가 수집됩니다. 데이터가 2일마다 한 번씩 업데이트됩니다. 이벤트 관련 데이터가 Windows Server 2012 R2의 향상된 DNS 로깅 및 진단이 제공하는 [분석 및 감사 로그](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc)를 통해 거의 실시간으로 수집됩니다.


## <a name="connect-your-dns-appliance"></a>DNS 어플라이언스 연결

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 선택하고 **DNS(미리 보기)** 타일을 선택합니다.
1. DNS 머신이 Azure에 있는 경우:
    1. **Azure Windows 가상 머신에 에이전트 설치** 를 클릭합니다.
    1. **가상 머신** 목록에서 Azure Sentinel로 스트리밍할 DNS 머신을 선택합니다. Windows VM인지 확인합니다.
    1. 해당 VM에 대해 열리는 창에서 **연결** 을 클릭합니다.  
    1. **DNS 커넥터** 창에서 **사용** 을 클릭합니다. 

2. DNS 머신이 Azure VM이 아닌 경우:
    1. **비 Azure 머신에 에이전트 설치** 를 클릭합니다.
    1. **직접 에이전트** 창에서 **Windows 에이전트 다운로드(64비트)** 또는 **Windows 에이전트 다운로드(32 비트)** 를 선택합니다.
    1. DNS 머신에 에이전트를 설치합니다. **작업 영역 ID**, **기본 키** 및 **보조 키** 를 복사하고 설치 중에 메시지가 표시되면 사용합니다.

3. DNS 로그의 Log Analytics에서 관련 스키마를 사용하려면 **DnsEvents** 를 검색합니다.

## <a name="validate"></a>유효성 검사 

Log Analytics에서 스키마 **DnsEvents** 를 검색하고 이벤트가 있는지 확인합니다.

## <a name="troubleshooting"></a>문제 해결

조회 쿼리가 Azure Sentinel에 표시되지 않는 경우 쿼리가 제대로 표시되도록 다음 단계를 수행합니다.
1. [서버에서 DNS Analytics 로그](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))를 설정합니다.
2. Log Analytics 컬렉션 목록에 DNSEvents가 표시되는지 확인합니다.
3. [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md)를 설정합니다.
4. Azure DNS Analytics의 **구성** 에서 설정을 변경하고 저장한 다음, 필요한 경우 다시 변경한 다음, 다시 저장합니다.
5. Azure DNS 분석을 선택하여 쿼리가 현재 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 DNS 온-프레미스 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
