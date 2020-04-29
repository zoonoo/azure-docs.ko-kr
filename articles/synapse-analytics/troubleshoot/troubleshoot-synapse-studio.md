---
title: Azure Synapse Studio (미리 보기) 문제 해결
description: Azure Synapse Studio 문제 해결
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431320"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure Synapse Studio (미리 보기) 문제 해결

이 문제 해결 가이드에서는 네트워크 연결 문제에 대 한 지원 티켓을 열 때 제공할 정보에 대 한 지침을 제공 합니다. 적절 한 정보를 사용 하 여 문제를 신속 하 게 해결할 수 있습니다.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL 주문형 (미리 보기) 서비스 연결 문제

### <a name="symptom-1"></a>증상 1

"SQL 주문형" 옵션은 "연결 대상" 드롭다운에서 회색으로 표시 됩니다.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>증상 2

"SQL 주문형"을 사용 하 여 쿼리를 실행 하면 "서버에 대 한 연결을 설정 하지 못했습니다." 라는 오류 메시지가 표시 됩니다.

![symptom2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>문제 해결 단계

> [!NOTE] 
>    다음 문제 해결 단계는 Chromium Edge 및 Chrome에 대 한 것입니다. 동일한 문제 해결 단계를 사용 하 여 다른 브라우저 (예: FireFox)를 사용할 수 있지만, "개발자 도구" 창의 레이아웃은이 TSG의 스크린샷에 다를 수 있습니다. 가능 하면 특정 상황에서 부정확 한 정보를 표시할 수 있으므로 문제를 해결 하기 위해 클래식 Edge를 사용 하지 마세요.

"진단 정보" 패널을 열고 "진단 다운로드" 단추를 선택 합니다. 오류 보고를 위해 다운로드 한 정보를 유지 합니다. 대신 "세션 ID"를 복사 하 여 지원 티켓을 열 때 연결할 수 있습니다.

![진단 정보](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

문제 해결을 시작 하려면 Azure Synapse Studio에서 수행한 작업을 다시 시도 하세요.

- 증상 1의 경우 "SQL 스크립트" 탭에서 "데이터베이스 사용" 드롭다운의 오른쪽에 있는 "새로 고침" 단추를 선택 하 고 "SQL 주문형"을 볼 수 있는지 확인 합니다.
- 증상 2의 경우 쿼리를 다시 실행 하 여 성공적으로 실행 되는지 확인 합니다.

문제가 계속 되 면 브라우저에서 F12 키를 눌러 "개발자 도구" (DevTools)를 엽니다.

"개발자 도구" 창에서 "네트워크" 패널로 전환 합니다. 필요한 경우 "네트워크" 패널의 도구 모음에서 "지우기" 단추를 선택 합니다.
"네트워크" 패널에서 "캐시 사용 안 함"이 선택 되어 있는지 확인 합니다.

Azure Synapse Studio에서 수행한 작업을 다시 시도 합니다. "개발자 도구"의 "네트워크" 목록에 표시 된 새 항목이 표시 될 수 있습니다. 지원 티켓에 제공할 현재 시스템 시간을 적어 둡니다.

![네트워크-패널](media/troubleshooting-synapse-studio/network-panel.png)

Url 열이 다음 패턴과 일치 하는 항목을 찾습니다.

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

여기서 [*A*]는 작업 영역 이름이 고 "-ondemand"는 "-sqlod"이 고, 여기서 [*B*]는 "master"와 같은 데이터베이스 이름 이어야 합니다. URL 값이 같지만 메서드 값이 다른 항목이 최대 두 개 있어야 합니다. 옵션 및 게시물. 이러한 두 항목의 상태 열에 "200" 또는 "20x"가 있는지 여부를 확인 합니다. 여기서 "x"는 단일 숫자일 수 있습니다.

둘 중 하나에 "20x" 및가 아닌 다른 항목이 있는 경우:

- 상태는 "(실패)"로 시작 하 고, "Status" 열을 확장 하거나 상태 텍스트에 대 한 포인터를 가리켜 전체 텍스트를 확인 합니다. 지원 티켓을 열 때 텍스트 및/또는 스크린샷을 포함 합니다.

    ![상태-텍스트](media/troubleshooting-synapse-studio/status-text.png)

    - ERR_NAME_NOT_RESOLVED 표시 되 고 10 분 이내에 작업 영역을 만든 경우 10 분 동안 기다린 후 다시 시도 하 여 문제가 계속 있는지 확인 합니다.
    - ERR_INTERNET_DISCONNECTED 또는 ERR_NETWORK_CHANGED 표시 되는 경우 PC 네트워크 연결에 문제가 있음을 나타낼 수 있습니다. 네트워크 연결을 확인 하 고 작업을 다시 시도 하세요.
    - ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR 또는 "SSL"을 포함 하는 기타 오류 코드가 표시 되는 경우 로컬 SSL 구성에 문제가 있거나 네트워크 관리자가 SQL 주문형 서버에 대 한 액세스를 차단 했음을 나타낼 수 있습니다. 지원 티켓을 열고 설명에 오류 코드를 연결 합니다.
    - ERR_NETWORK_ACCESS_DENIED 표시 되는 경우 관리자에 게 문의 하 여 로컬 방화벽 정책에서 *. database.windows.net 도메인 또는 원격 포트 1443에 대 한 액세스를 차단 했는지 확인 해야 할 수 있습니다.
    - 필요에 따라 다른 컴퓨터 및/또는 네트워크 환경에서 동일한 작업을 즉시 시도 하 여 PC에서 네트워크 구성 문제를 제외 합니다.

- status는 "40x", "50x" 또는 기타 숫자입니다. 세부 정보를 보려면 항목을 선택 합니다. 오른쪽에 항목 세부 정보가 표시 됩니다. "응답 헤더" 섹션을 찾습니다. 그런 다음 "액세스-컨트롤-원본" 이라는 항목이 있는지 확인 합니다. 그렇다면 다음 값 중 하나가 있는지 확인 합니다.

    - `*`(단일 별표)
    - https://web.azuresynapse.net/또는 브라우저 주소 표시줄의 텍스트에서 시작 하는 다른 값

응답 헤더에 위의 값 중 하나를 포함 하는 경우 오류 정보를 이미 수집 해야 한다는 의미입니다. 필요한 경우 지원 티켓을 열고 필요에 따라 항목 세부 정보의 스크린샷을 첨부할 수 있습니다.

헤더를 볼 수 없거나 헤더에 위에 나열 된 값 중 하나가 없는 경우 티켓을 열 때 항목 세부 정보의 스크린샷을 첨부 합니다.

![항목-세부 정보](media/troubleshooting-synapse-studio/item-details.png)

위의 단계를 수행 해도 문제가 해결 되지 않으면 지원 티켓을 열어야 할 수 있습니다. 지원 티켓을 제출할 때이 가이드의 시작 부분에서 다운로드 한 "세션 ID" 또는 "진단 정보"를 포함 합니다.

문제를 보고 하는 경우 선택적으로 "개발자 도구"에서 "콘솔" 탭의 스크린샷을 사용 하 여 지원 티켓에 연결할 수 있습니다. 콘텐츠를 스크롤하고 필요한 경우 전체 메시지를 캡처하는 스크린샷 하나를 사용 합니다.

![개발자-도구-콘솔](media/troubleshooting-synapse-studio/developer-tool-console.png)

스크린샷을 연결 하는 경우 스크린샷에 소요 된 시간 (또는 예상 시간 범위)을 제공 합니다. 문제를 확인 하는 데 도움이 됩니다.

특정 브라우저는 "콘솔" 탭에 타임 스탬프를 표시 하도록 지원 합니다. Chromium Edge/Chrome의 경우 "개발자 도구"에서 "설정" 대화 상자를 열고 "기본 설정" 탭에서 "타임 스탬프 표시"를 선택 합니다.

![개발자-도구-설정](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![타임 스탬프 표시](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>다음 단계
이전 단계에서 문제 해결에 도움이 되지 않는 경우 [지원 티켓 만들기](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
