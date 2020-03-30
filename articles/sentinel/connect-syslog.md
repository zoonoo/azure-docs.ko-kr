---
title: Syslog 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Syslog 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588079"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog를 사용하여 외부 솔루션 연결

Syslog를 지원하는 온-프레미스 어플라이언스를 Azure Sentinel에 연결할 수 있습니다. 이 작업은 어플라이언스와 Azure Sentinel 간의 Linux 컴퓨터를 기반으로 하는 에이전트를 사용하여 수행됩니다. Linux 컴퓨터가 Azure에 있는 경우 어플라이언스 또는 응용 프로그램에서 Azure에서 만든 전용 작업 영역으로 로그를 스트리밍하고 연결할 수 있습니다. Linux 컴퓨터가 Azure에 없는 경우 어플라이언스에서 Linux용 에이전트를 설치하는 전용 온-프레미스 VM 또는 컴퓨터로 로그를 스트리밍할 수 있습니다. 

> [!NOTE]
> 어플라이언스가 Syslog CEF를 지원하는 경우 연결이 더 완벽하며 이 옵션을 선택하고 [CEF의 데이터 연결](connect-common-event-format.md)에 대한 지침을 따라야 합니다.

## <a name="how-it-works"></a>작동 방법

Syslog는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. 애플리케이션은 로컬 컴퓨터에 저장되거나 Syslog 수집기에 배달될 수 있는 메시지를 전송합니다. Linux용 Log Analytics 에이전트를 설치하면 에이전트에 메시지를 전달하도록 로컬 Syslog 디먼이 구성됩니다. 그러면 에이전트는 레코드가 만들어진 Azure Monitor로 해당 메시지를 보냅니다.

자세한 내용은 [Azure 모니터의 Syslog 데이터 원본을](../azure-monitor/platform/data-sources-syslog.md)참조하십시오.

> [!NOTE]
> - 에이전트는 여러 소스에서 로그를 수집할 수 있지만 전용 프록시 컴퓨터에 설치해야 합니다.
> - 동일한 VM에서 CEF 및 Syslog 모두에 대한 커넥터를 지원하려면 다음 단계를 수행하여 데이터가 중복되지 않도록 하십시오.
>    1. 지침에 따라 [CEF를 연결합니다.](connect-common-event-format.md)
>    2. Syslog 데이터를 연결하려면 작업**영역** >  **설정 고급** > **설정** > **데이터** > **Syslog로** 이동하여 CEF 구성에서 사용한 시설과 속성과 동일한 시설이 아니도록 시설 및 우선 순위를 설정합니다. <br></br>**내 컴퓨터에 아래 구성 적용을**선택하면 이 작업 영역에 연결된 모든 VM에 이러한 설정이 적용됩니다.


## <a name="connect-your-syslog-appliance"></a>Syslog 어플라이언스 연결

1. Azure Sentinel에서 **데이터 커넥터를** 선택한 다음 **Syslog 커넥터를** 선택합니다.

2. **Syslog** 블레이드에서 **커넥터 열기 페이지를**선택합니다.

3. 리눅스 에이전트를 설치:
    
    - Linux 가상 시스템이 Azure에 있는 경우 **Azure Linux 가상 컴퓨터에서 에이전트 다운로드 및 설치를**선택합니다. 가상 **컴퓨터** 블레이드에서 가상 컴퓨터를 선택하여 에이전트를 설치한 다음 **연결을**클릭합니다.
    - Linux 컴퓨터가 Azure에 없는 경우 **Linux 비 Azure 컴퓨터에서 에이전트 다운로드 및 설치를**선택합니다. 직접 **에이전트** 블레이드에서 **리눅스용 다운로드 및 온보드 에이전트에 대한** 명령을 복사하여 컴퓨터에서 실행합니다. 
    
   > [!NOTE]
   > 조직의 보안 정책에 따라 이러한 컴퓨터에 대한 보안 설정을 구성해야 합니다. 예를 들어 조직의 네트워크 보안 정책에 맞게 네트워크 설정을 구성하고 데몬의 포트와 프로토콜을 변경하여 보안 요구 사항에 맞게 변경할 수 있습니다.

4. **작업 영역 고급 설정 구성 열기를 선택합니다.**

5. 고급 **설정** 블레이드에서 **데이터** > **Syslog를 선택합니다.** 그런 다음 수집할 커넥터에 대한 기능을 추가합니다.
    
    syslog 어플라이언스가 로그 헤더에 포함하는 기능을 추가합니다. 폴더의 **Syslog-d** `/etc/rsyslog.d/security-config-omsagent.conf` 및 **r-Syslog에서** `/etc/syslog-ng/security-config-omsagent.conf`Syslog 어플라이언스에서 이 구성을 볼 수 있습니다.
    
    수집한 데이터와 함께 비정상적인 SSH 로그인 검색을 사용하려면 **auth** 및 **authpriv**를 추가합니다. 자세한 내용은 [다음 섹션을](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) 참조하십시오.

6. 모니터링하려는 모든 시설을 추가하고 각 시설에 대한 심각도 옵션을 조정한 경우 **내 컴퓨터에 구성 아래에 적용됨 확인란을**선택합니다.

7. **저장**을 선택합니다. 

8. syslog 어플라이언스에서 지정한 시설을 보내고 있는지 확인합니다.

9. Syslog 로그에 Azure 모니터에서 관련 스키마를 사용하려면 **Syslog**를 검색합니다.

10. [Azure Monitor 로그 쿼리의 함수 사용에서](../azure-monitor/log-query/functions.md) 설명한 Kusto 함수를 사용하여 Syslog 메시지를 구문 분석할 수 있습니다. 그런 다음 새 Log Analytics 기능으로 저장하여 새 데이터 유형으로 사용할 수 있습니다.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>비정상적인 SSH 로그인 검색을 위해 Syslog 커넥터 구성

> [!IMPORTANT]
> 비정상적인 SSH 로그인 검색은 현재 공개 미리 보기상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel은 syslog 데이터에 기계 학습(ML)을 적용하여 비정상적인 SSH(보안 셸) 로그인 활동을 식별할 수 있습니다. 시나리오는 다음과 같습니다.

- 불가능한 여행 – 두 개의 로그인 이벤트의 기간 내에 도달할 수 없는 두 위치에서 두 개의 성공적인 로그인 이벤트가 발생하는 경우.
- 예기치 않은 위치 – 성공적인 로그인 이벤트가 발생한 위치가 의심스럽습니다. 예를 들어 최근에 위치를 볼 수 없습니다.
 
이 검색에는 Syslog 데이터 커넥터의 특정 구성이 필요합니다. 

1. 이전 절차의 5단계의 경우 **인증** 및 **authpriv가** 모니터링할 시설로 선택되어 있는지 확인합니다. 심각도 옵션의 기본 설정을 모두 선택하여 유지합니다. 예를 들어:
    
    > [!div class="mx-imgBorder"]
    > ![비정상적인 SSH 로그인 감지에 필요한 시설](./media/connect-syslog/facilities-ssh-detection.png)

2. syslog 정보가 수집될 때까지 충분한 시간을 허용합니다. 그런 다음 **Azure Sentinel - 로그로**이동하여 다음 쿼리를 복사하여 붙여넣습니다.
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    필요한 경우 **시간 범위를** 변경하고 **실행을**선택합니다.
    
    결과 수가 0이면 커넥터의 구성을 확인하고 모니터링된 컴퓨터가 쿼리에 대해 지정한 기간 동안 성공적인 로그인 작업이 있는지 확인합니다.
    
    결과 수가 0보다 크면 syslog 데이터가 비정상적인 SSH 로그인 검색에 적합합니다. **분석** >  **규칙 템플릿(미리** > **보기)에서**이 검색을 사용하도록 설정하면 비정상적인 SSH 로그인 검색 .

## <a name="next-steps"></a>다음 단계
이 문서에서는 Syslog 온-프레미스 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

