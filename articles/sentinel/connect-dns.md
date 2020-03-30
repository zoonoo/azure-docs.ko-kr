---
title: Azure 센티넬에서 DNS 데이터 연결 | 마이크로 소프트 문서
description: Azure Sentinel에서 DNS 데이터를 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588317"
---
# <a name="connect-your-domain-name-server"></a>도메인 이름 서버 연결

> [!IMPORTANT]
> Azure Sentinel의 DNS 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows에서 실행 중인 모든 도메인 이름 서버(DNS)를 Azure Sentinel에 연결할 수 있습니다. 이 작업은 DNS 컴퓨터에 에이전트를 설치하여 수행됩니다. DNS 로그를 사용하면 DNS 서버에서 분석 및 감사 로그 및 기타 관련 데이터를 수집, 분석 및 상호 연관시켜 조직의 DNS 인프라에 대한 보안, 성능 및 운영 관련 통찰력을 얻을 수 있습니다.

DNS 로그 연결을 사용하도록 설정하면 다음을 수행할 수 있습니다.
- 악의적인 도메인 이름을 확인하려는 클라이언트 식별
- 부실 리소스 레코드 식별
- 자주 쿼리되는 도메인 이름 및 대화량이 많은 DNS 클라이언트 식별
- DNS 서버에서 요청 부하 보기
- 동적 DNS 등록 오류 보기

## <a name="connected-sources"></a>연결된 소스

다음 표는 이 솔루션이 지원하는 연결된 원본을 설명합니다.

| **연결된 소스** | **지원** | **설명** |
| --- | --- | --- |
| [Windows 에이전트](../azure-monitor/platform/agent-windows.md) | yes | 솔루션이 Windows 에이전트에서 DNS 정보를 수집합니다. |
| [Linux 에이전트](../azure-monitor/learn/quick-collect-linux-computer.md) | 예 | 솔루션이 직접 Linux 에이전트에서 DNS 정보를 수집하지 않습니다. |
| [System Center Operations Manager 관리 그룹](../azure-monitor/platform/om-agents.md) | yes | 솔루션이 연결된 Operations Manager 관리 그룹의 에이전트에서 DNS 정보를 수집합니다. Operations Manager 에이전트에서 Azure Monitor로 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |
| [Azure 저장소 계정](../azure-monitor/platform/collect-azure-metrics-logs.md) | 예 | Azure Storage가 솔루션에서 사용되지 않습니다. |

### <a name="data-collection-details"></a>데이터 수집 세부 정보

솔루션이 Log Analytics가 설치된 DNS 서버에서 DNS 인벤토리 및 DNS 이벤트 관련 데이터를 수집합니다. DNS Powershell cmdlet을 실행하여 DNS 서버, 영역 및 리소스 레코드 수와 같은 인벤토리 관련 데이터가 수집됩니다. 데이터가 2일마다 한 번씩 업데이트됩니다. 이벤트 관련 데이터가 Windows Server 2012 R2의 향상된 DNS 로깅 및 진단이 제공하는 [분석 및 감사 로그](https://technet.microsoft.com/library/dn800669.aspx#enhanc)를 통해 거의 실시간으로 수집됩니다.


## <a name="connect-your-dns-appliance"></a>DNS 어플라이언스 연결

1. Azure Sentinel 포털에서 데이터 커넥터를 선택하고 **DNS(미리 보기)** 타일을 **선택합니다.**
1. DNS 컴퓨터가 Azure에 있는 경우:
    1. **Azure Windows 가상 컴퓨터에서 에이전트 설치를 클릭합니다.**
    1. 가상 **컴퓨터** 목록에서 Azure Sentinel로 스트리밍할 DNS 컴퓨터를 선택합니다. 이것이 Windows VM인지 확인합니다.
    1. 해당 VM에 대해 열리는 창에서 **연결을**클릭합니다.  
    1. **DNS 커넥터** 창에서 **활성화를** 클릭합니다. 

2. DNS 컴퓨터가 Azure VM이 아닌 경우:
    1. **Azure 가 아닌 컴퓨터에서 에이전트 설치를 클릭합니다.**
    1. 직접 **에이전트** 창에서 **Windows 에이전트 다운로드(64비트)** 또는 **Windows 에이전트 다운로드(32비트)를**선택합니다.
    1. DNS 컴퓨터에 에이전트를 설치합니다. 작업 **영역 ID,** **기본 키**및 **보조 키를** 복사하고 설치 하는 동안 메시지가 표시 될 때 사용 합니다.

3. DNS 로그에 대한 로그 분석에서 관련 스키마를 사용하려면 **DnsEvents를**검색합니다.

## <a name="validate"></a>유효성 검사 

로그 분석에서 스키마 **DnsEvents를** 검색하고 이벤트가 있는지 확인합니다.

## <a name="troubleshooting"></a>문제 해결

조회 쿼리가 Azure Sentinel에 표시되지 않으면 다음 단계를 수행하여 쿼리가 제대로 표시됩니다.
1. [서버에서 DNS 분석 로그를](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))켭니다.
2. DNSEvents가 로그 분석 컬렉션 목록에 나타나는지 확인합니다.
3. Azure [DNS 분석을](../azure-monitor/insights/dns-analytics.md)켭니다.
4. Azure DNS 분석에서 **구성에서**설정 중 일부를 변경하고 저장한 다음 필요한 경우 다시 변경한 다음 다시 저장합니다.
5. Azure DNS 분석을 확인하여 쿼리가 현재 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 DNS 온-프레미스 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
