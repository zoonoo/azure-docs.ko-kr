---
title: Azure 센티널에 Syslog 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Syslog 데이터를 연결 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588079"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog를 사용 하 여 외부 솔루션 연결

Syslog를 지 원하는 온-프레미스 어플라이언스를 Azure 센티널에 연결할 수 있습니다. 어플라이언스와 Azure 센티널 사이에 Linux 컴퓨터를 기반으로 하는 에이전트를 사용 하 여이 작업을 수행 합니다. Linux 컴퓨터가 Azure에 있는 경우 어플라이언스 또는 응용 프로그램에서 Azure에 만든 전용 작업 영역으로 로그를 스트리밍하 고 연결할 수 있습니다. Linux 컴퓨터가 Azure에 없으면 어플라이언스의 로그를 Linux 용 에이전트를 설치 하는 전용 온-프레미스 VM 또는 컴퓨터로 스트리밍할 수 있습니다. 

> [!NOTE]
> 어플라이언스에서 Syslog CEF를 지 원하는 경우에는 연결이 더 완벽 하므로이 옵션을 선택 하 고 [CEF의 데이터 연결](connect-common-event-format.md)에 설명 된 지침을 따라야 합니다.

## <a name="how-it-works"></a>작동 방법

Syslog는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. 애플리케이션은 로컬 컴퓨터에 저장되거나 Syslog 수집기에 배달될 수 있는 메시지를 전송합니다. Linux용 Log Analytics 에이전트를 설치하면 에이전트에 메시지를 전달하도록 로컬 Syslog 디먼이 구성됩니다. 그러면 에이전트는 레코드가 만들어진 Azure Monitor로 해당 메시지를 보냅니다.

자세한 내용은 [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/platform/data-sources-syslog.md)을 참조 하세요.

> [!NOTE]
> - 에이전트는 여러 원본에서 로그를 수집할 수 있지만 전용 프록시 컴퓨터에 설치 해야 합니다.
> - 동일한 VM에서 CEF와 Syslog 모두에 대 한 커넥터를 지원 하려면 다음 단계를 수행 하 여 데이터 중복을 방지 합니다.
>    1. 지침에 따라 [CEF를 연결](connect-common-event-format.md)합니다.
>    2. Syslog 데이터를 연결 하려면 **설정** > **작업 영역 설정** > **고급 설정** > **데이터** > **Syslog** 로 이동 하 고, 해당 기능 및 해당 우선 순위를 설정 하 여 cef 구성에서 사용한 것과 동일한 기능 및 속성이 되도록 합니다. <br></br>**내 컴퓨터에 아래 구성 적용**을 선택 하는 경우이 작업 영역에 연결 된 모든 vm에 이러한 설정 적용 됩니다.


## <a name="connect-your-syslog-appliance"></a>Syslog 어플라이언스 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Syslog** 커넥터를 선택 합니다.

2. **Syslog** 블레이드에서 **커넥터 페이지 열기**를 선택 합니다.

3. Linux 에이전트를 설치 합니다.
    
    - Azure에 Linux 가상 머신이 있는 경우 **Azure linux 가상 머신에서 에이전트 다운로드 및 설치**를 선택 합니다. **Virtual machines** 블레이드에서 에이전트를 설치할 가상 컴퓨터를 선택 하 고 **연결**을 클릭 합니다.
    - Linux 컴퓨터가 Azure에 없는 경우 **linux가 아닌 linux에서 에이전트 다운로드 및 설치**를 선택 합니다. **직접 에이전트** 블레이드에서 **LINUX 용 에이전트 다운로드 및** 등록을 위한 명령을 복사 하 고 컴퓨터에서 실행 합니다. 
    
   > [!NOTE]
   > 조직의 보안 정책에 따라 이러한 컴퓨터에 대 한 보안 설정을 구성 해야 합니다. 예를 들어 조직의 네트워크 보안 정책에 맞게 네트워크 설정을 구성 하 고, 보안 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다.

4. **작업 영역 열기 고급 설정 구성**을 선택 합니다.

5. **고급 설정** 블레이드에서 **데이터** > **Syslog**를 선택 합니다. 그런 다음 수집할 커넥터에 대 한 기능을 추가 합니다.
    
    Syslog 어플라이언스에 포함 된 기능을 로그 헤더에 추가 합니다. 이 구성은 `/etc/rsyslog.d/security-config-omsagent.conf` 폴더의 **syslog-d** 및 `/etc/syslog-ng/security-config-omsagent.conf`의 **r-syslog** 에서 볼 수 있습니다.
    
    수집한 데이터를 사용 하 여 비정상적인 SSH 로그인 검색을 사용 하려는 경우 **auth** 및 **authpriv**를 추가 합니다. 자세한 내용은 [다음 섹션](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) 을 참조 하세요.

6. 모니터링 하려는 모든 기능을 추가 하 고 각 기능에 대 한 심각도 옵션을 조정한 경우 **내 컴퓨터에 아래 구성 적용**확인란을 선택 합니다.

7. **저장**을 선택합니다. 

8. Syslog 어플라이언스에서 지정한 시설을 전송 하 고 있는지 확인 합니다.

9. Syslog 로그에 대해 Azure Monitor에서 관련 스키마를 사용 하려면 **syslog**를 검색 합니다.

10. [Azure Monitor 로그 쿼리의 함수를 사용 하 여](../azure-monitor/log-query/functions.md) Syslog 메시지를 구문 분석 하는 방법에 설명 된 Kusto 함수를 사용할 수 있습니다. 그런 다음 새 데이터 형식으로 사용할 새 Log Analytics 함수로 저장할 수 있습니다.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>비정상적인 SSH 로그인 검색을 위한 Syslog 커넥터 구성

> [!IMPORTANT]
> 비정상적인 SSH 로그인 검색은 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 센티널은 syslog 데이터에 기계 학습 (ML)을 적용 하 여 비정상적인 Secure Shell (SSH) 로그인 작업을 식별할 수 있습니다. 시나리오에는 다음이 포함됩니다.

- 불가능 한 이동-두 로그인 이벤트의 기간 내에 도달할 수 없는 두 위치에서 두 개의 성공한 로그인 이벤트가 발생 하는 경우
- 예기치 않은 위치 – 성공적인 로그인 이벤트가 발생 한 위치를 의심 합니다. 예를 들어 위치는 최근에 표시 되지 않았습니다.
 
이 검색에는 Syslog 데이터 커넥터의 특정 구성이 필요 합니다. 

1. 이전 절차의 5 단계에는 **auth** 및 **authpriv** 가 모두 모니터링할 기능으로 선택 되어 있는지 확인 합니다. 심각도 옵션의 기본 설정은 모두 선택 된 상태로 유지 합니다. 다음은 그 예입니다.
    
    > [!div class="mx-imgBorder"]
    > 비정상적인 SSH 로그인 검색에 필요한 ![기능](./media/connect-syslog/facilities-ssh-detection.png)

2. Syslog 정보를 수집할 수 있는 충분 한 시간을 허용 합니다. 그런 다음 **Azure 센티널-Logs**로 이동 하 여 다음 쿼리를 복사 하 여 붙여넣습니다.
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    필요한 경우 **시간 범위** 를 변경 하 고 **실행**을 선택 합니다.
    
    결과 수가 0 이면 커넥터의 구성을 확인 하 고 모니터링 되는 컴퓨터에 쿼리를 위해 지정한 기간 동안 성공한 로그인 작업이 있는지 확인 합니다.
    
    결과 개수가 0 보다 크면 syslog 데이터가 비정상적인 SSH 로그인 검색에 적합 합니다. **분석** >  **규칙 템플릿** >  **(미리 보기) 비정상적인 SSH 로그인 검색**에서이 검색을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Syslog 온-프레미스 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.

