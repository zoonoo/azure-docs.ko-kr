---
title: 대시보드를 사용 하 여 오류 조사
description: 이 문서에는 ITSMC 대시보드의 오류에 대 한 정보가 포함 되어 있습니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089278"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>ITSMC 대시보드를 사용 하 여 오류 조사

이 문서에는 ITSMC (IT 서비스 관리 커넥터) 대시보드에 대 한 정보가 포함 되어 있습니다. 대시보드는 ITSMC의 상태를 조사 하는 데 도움이 됩니다.

## <a name="view-the-dashboard"></a>대시보드 보기

대시보드를 열려면 다음 단계를 수행 합니다.

1. **모든 리소스** 를 선택 하 고 **servicedesk (*작업 영역 이름*)** 를 찾습니다.

   ![Azure 서비스의 리소스를 보여 주는 스크린샷](media/itsmc-definition/create-new-connection-from-resource.png)

1. 왼쪽 창에서 **작업 영역 데이터 원본** 을 선택 하 고 **itsm 연결** 을 선택 합니다.

   ![작업 공간 데이터 원본에서 ITSM 연결을 선택 하는 것을 보여 주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)

1. **요약 섹션에서** 요약 **보기** 를 선택 하 여 요약 그래프를 확인 합니다.

    ![요약 섹션에서 요약 보기 옵션을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. **요약** 섹션에서 그래프를 선택 하 여 대시보드를 엽니다.

    ![요약 그래프를 선택 하는 것을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. 대시보드에서 커넥터의 상태 및 오류를 검토 합니다.
    ![대시보드를 보여 주는 스크린샷](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>대시보드 요소 이해

대시보드는이 커넥터를 사용 하 여 ITSM 도구로 전송 된 경고에 대 한 정보를 포함 합니다.

대시보드는 다음 네 개의 섹션으로 분할 됩니다.

- **만든 작업 항목**: 그래프 및 표에서 유형별 작업 항목 수를 보여 줍니다. 그래프 또는 테이블을 선택 하 여 작업 항목에 대해 자세히 알아보세요.
      ![만든 작업 항목 섹션을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **영향을 받는 컴퓨터**: 테이블에는 작업 항목을 만든 구성 항목에 대 한 세부 정보가 포함 됩니다.
      구성 항목에 대 한 자세한 내용은 테이블에서 행을 선택 합니다.
      테이블에 제한 된 수의 행이 포함 되어 있습니다. 전체 목록을 보려면 **모두 표시** 를 선택 합니다.
      ![영향을 받는 컴퓨터 섹션을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **커넥터 상태**: 그래프 및 표는 커넥터의 상태에 대 한 정보를 표시 합니다. 자세한 내용은 테이블에서 그래프 또는 메시지를 선택 합니다. 테이블에는 제한 된 수의 행이 표시 됩니다. 전체 목록을 보려면 **모두 표시** 를 선택 합니다.
      ![커넥터 상태 섹션을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **경고 규칙**:이 섹션에는 검색 된 경고 규칙 수에 대 한 정보가 표시 됩니다. 검색 된 규칙에 대 한 자세한 내용은 테이블에서 행을 선택 합니다. 테이블에 제한 된 수의 행이 있습니다. 전체 목록을 보려면 **모두 표시** 를 선택 합니다.
      ![경고 규칙 섹션을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>다음 단계

[일반적인 커넥터 상태 오류](itsmc-dashboard-errors.md)를 확인 합니다.
