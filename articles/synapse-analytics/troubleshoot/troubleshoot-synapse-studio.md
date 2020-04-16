---
title: Azure 시냅스 스튜디오 문제 해결(미리 보기)
description: 문제 해결 Azure 시냅스 스튜디오
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431320"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure 시냅스 스튜디오(미리 보기) 문제 해결

이 문제 해결 가이드는 네트워크 연결 문제에 대한 지원 티켓을 열 때 제공해야 할 정보에 대한 지침을 제공합니다. 적절한 정보를 통해 문제를 더 신속하게 해결할 수 있습니다.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL 온디맨드(미리 보기) 서비스 연결 문제

### <a name="symptom-1"></a>증상 1

"주문형 SQL" 옵션은 "연결" 드롭다운에서 회색으로 표시됩니다.

![증상1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>증상 2

"주문형 SQL"으로 쿼리를 실행하면 "서버에 대한 연결을 설정하지 못했습니다" 오류 메시지가 나타납니다.

![증상2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>문제 해결 단계

> [!NOTE] 
>    크롬 에지 와 크롬에 대 한 다음 문제 해결 단계는. 동일한 문제 해결 단계가 있는 다른 브라우저(예: FireFox)를 사용할 수 있지만 "개발자 도구" 창은 이 TSG의 스크린샷과 다른 레이아웃을 가질 수 있습니다. 가능하면 특정 상황에서 부정확한 정보가 표시될 수 있으므로 문제 해결을 위해 클래식 Edge를 사용하지 마십시오.

"진단 정보" 패널을 열고 "진단 다운로드" 버튼을 선택합니다. 오류 보고를 위해 다운로드한 정보를 보관합니다. 대신 "세션 ID"를 복사하여 지원 티켓을 열 때 첨부할 수 있습니다.

![진단 정보](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

문제 해결을 시작하려면 Azure Synapse Studio에서 수행한 작업을 다시 시도합니다.

- 증상 1의 경우 "SQL 스크립트" 탭에서 "데이터베이스 사용" 드롭다운 오른쪽에 있는 "새로 고침" 단추를 선택하고 "주문형 SQL"을 볼 수 있는지 확인합니다.
- 증상 2의 경우 쿼리를 다시 실행하여 성공적으로 실행되는지 확인합니다.

그래도 문제가 있으면 브라우저에서 F12를 눌러 "개발자 도구"(DevTools)를 엽니다.

"개발자 도구" 창에서 "네트워크" 패널로 전환합니다. 필요한 경우 "네트워크" 패널의 도구 모음에서 "지우기" 버튼을 선택합니다.
"네트워크" 패널에서 "캐시 사용 안 함"이 선택되어 있는지 확인합니다.

Azure Synapse 스튜디오에서 수행한 작업을 다시 시도합니다. "개발자 도구"의 "네트워크" 목록에 새 항목이 표시될 수 있습니다. 지원 티켓에 제공할 현재 시스템 시간을 확인합니다.

![네트워크 패널](media/troubleshooting-synapse-studio/network-panel.png)

Url 열이 다음 패턴과 일치하는 항목을 찾습니다.

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

여기서 *[A]는*작업 영역 이름이고 "-ondemand"는 "-sqlod"이고 *[B]는*"마스터"와 같은 데이터베이스 이름이어야 합니다. URL 값은 같지만 메서드 값이 다른 항목은 두 개이상 이어야 합니다. 옵션 및 게시. 상태 열 아래에 "x"가 한 자리가 될 수 있는 상태 열 아래에 이 두 항목이 "200" 또는 "20x"가 있는지 확인합니다.

둘 중 하나가 "20x"와 다른 것을 가지고 있다면 :

- 상태는 "(실패)"로 시작하여 "상태" 열을 넓게 하거나 상태 텍스트 위로 포인터를 마우스로 가리키면 전체 텍스트를 볼 수 있습니다. 지원 티켓을 열 때 텍스트 및/또는 스크린샷을 포함합니다.

    ![상태 텍스트](media/troubleshooting-synapse-studio/status-text.png)

    - ERR_NAME_NOT_RESOLVED 표시되고 10분 이내에 작업 영역을 만든 경우 10분 동안 기다렸다가 다시 시도하여 문제가 여전히 존재하는지 확인합니다.
    - ERR_INTERNET_DISCONNECTED 또는 ERR_NETWORK_CHANGED 표시되면 PC 네트워크 연결에 문제가 있음을 나타낼 수 있습니다. 네트워크 연결을 확인하고 작업을 다시 시도합니다.
    - ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR 또는 "SSL"을 포함하는 기타 오류 코드가 표시되면 로컬 SSL 구성에 문제가 있거나 네트워크 관리자가 SQL 주문형 서버에 대한 액세스를 차단했음을 나타낼 수 있습니다. 지원 티켓을 열고 설명에 오류 코드를 첨부합니다.
    - ERR_NETWORK_ACCESS_DENIED 경우 로컬 방화벽 정책에서 *.database.windows.net 도메인 또는 원격 포트 1443에 대한 액세스를 차단했는지 여부를 관리자에게 확인해야 할 수 있습니다.
    - 선택적으로 다른 컴퓨터 및/또는 네트워크 환경에서 즉시 동일한 작업을 시도하여 PC에서 네트워크 구성 문제를 배제합니다.

- 상태는 "40x", "50x" 또는 기타 숫자로 항목에서 선택하여 세부 정보를 확인합니다. 항목 세부 정보가 오른쪽에 표시됩니다. "응답 헤더" 섹션을 찾습니다. 그런 다음 "액세스 제어 허용-원본"이라는 항목이 있는지 확인합니다. 그렇다면 다음 값 중 하나가 있는지 확인합니다.

    - `*`(단일 별표)
    - https://web.azuresynapse.net/(또는 브라우저 주소 표시줄의 텍스트가 시작되는 다른 값)

응답 헤더에 위의 값 중 하나가 포함되어 있으면 오류 정보를 이미 수집했어야 합니다. 필요한 경우 지원 티켓을 열고 선택적으로 항목 세부 정보의 스크린샷을 첨부할 수 있습니다.

헤더를 볼 수 없거나 헤더에 위에 나열된 값 중 하나가 없는 경우 티켓을 열 때 항목 세부 정보의 스크린샷을 첨부합니다.

![항목 세부 정보](media/troubleshooting-synapse-studio/item-details.png)

위의 단계를 통해 문제가 해결되지 않으면 지원 티켓을 열어야 할 수 있습니다. 지원 티켓을 제출할 때 이 가이드의 시작 부분에 다운로드한 "세션 ID" 또는 "진단 정보"를 포함합니다.

문제를 보고할 때 선택적으로 "개발자 도구"에서 "콘솔" 탭의 스크린샷을 찍어 지원 티켓에 첨부할 수 있습니다. 전체 메시지를 캡처하는 데 필요한 경우 콘텐츠를 스크롤하고 두 개 이상의 스크린샷을 찍습니다.

![개발자 도구 콘솔](media/troubleshooting-synapse-studio/developer-tool-console.png)

스크린샷을 첨부하는 경우 스크린샷을 찍은 시간(또는 예상 시간 범위)을 입력합니다. 그것은 문제를 들여다 볼 때 우리에게 도움이 될 것입니다.

일부 브라우저는 "콘솔" 탭에서 타임스탬프표시를 지원합니다. 크롬 에지/크롬의 경우 "개발자 도구"에서 "설정" 대화 상자를 열고 "기본 설정" 탭에서 "타임스탬프 표시"를 선택합니다.

![개발자-도구-콘솔 설정](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![쇼 타임 스탬프](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>다음 단계
이전 단계가 문제를 해결하는 데 도움이 되지 않는 경우 [지원 티켓 만들기](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
