---
title: 대시보드를 사용 하 여 오류 조사
description: 이 문서에서는 대시보드를 사용 하 여 오류 조사에 대 한 정보를 포함 합니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540619"
---
# <a name="error-investigation-using-the-dashboard"></a>대시보드를 사용 하 여 오류 조사

이 페이지에는 ITSM 커넥터 대시보드에 대 한 정보가 포함 되어 있습니다. 이 대시보드는 ITSM 커넥터의 상태를 조사 하는 데 도움이 됩니다.

## <a name="how-to-view-the-dashboard"></a>대시보드를 보는 방법

대시보드에서 오류를 보려면 다음 단계를 수행 해야 합니다.

1. **모든 리소스** 에서 **servicedesk (*작업 영역 이름*)** 를 찾습니다.

   ![Azure Portal의 최근 리소스를 보여 주는 스크린샷](media/itsmc-definition/create-new-connection-from-resource.png)

2. 왼쪽 창의 **작업 영역 데이터 원본** 에서 **itsm 연결** 을 선택 합니다.

   ![ITSM 연결 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)

3. 왼쪽 상자 **IT 서비스 관리 커넥터** **요약** 아래에서 **요약 보기** 를 선택 합니다.

    ![보기 요약을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 왼쪽 상자의 **요약** 아래에서 그래프를 클릭 **IT 서비스 관리 커넥터** 합니다.

    ![그래프 클릭을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 이 대시보드를 사용 하 여 커넥터의 상태와 오류를 검토할 수 있습니다.
    ![커넥터 상태를 보여 주는 스크린샷](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>대시보드 요소

대시보드는이 커넥터를 사용 하 여 ITSM 도구로 전송 된 경고에 대 한 정보를 포함 합니다.
대시보드는 다음 네 부분으로 분할 됩니다.

1. 만든 작업 항목: 그래프와 아래 표에는 유형별 작업 항목 수가 포함 되어 있습니다. 그래프를 클릭 하거나 표를 클릭 하면 작업 항목에 대 한 자세한 정보를 볼 수 있습니다.
    ![만든 작업 항목을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. 영향을 받는 컴퓨터: 테이블에는 구성 항목을 만든 구성 항목에 대 한 세부 정보가 포함 됩니다.
    테이블에서 행을 클릭 하 여 구성 항목에 대 한 자세한 정보를 얻을 수 있습니다.
    "모두 보기"를 클릭 하 여 모든 목록을 보려면 테이블에 제한 된 수의 행이 포함 되어 있습니다.
    ![영향을 받는 컴퓨터를 표시 하는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. 커넥터 상태: 그래프 및 아래 표에는 커넥터의 상태에 대 한 메시지가 포함 되어 있습니다. 테이블의 행에 대 한 그래프를 클릭 하면 커넥터 상태의 메시지에 대 한 자세한 정보를 볼 수 있습니다.
    "모두 보기"를 클릭 하 여 모든 목록을 보려면 테이블에 제한 된 수의 행이 포함 되어 있습니다.
    ![커넥터 상태를 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. 경고 규칙: 테이블에는 검색 된 경고 규칙 수에 대 한 정보가 포함 되어 있습니다.
    테이블에서 행을 클릭 하면 검색 된 규칙에 대 한 자세한 정보를 볼 수 있습니다.
    "모두 보기"를 클릭 하 여 모든 목록을 보려면 테이블에 제한 된 수의 행이 포함 되어 있습니다.
    ![경고 규칙을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)