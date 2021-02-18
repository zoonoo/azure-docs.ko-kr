---
title: ITSMC 대시보드를 사용 하 여 오류 조사
description: IT 서비스 관리 커넥터 대시보드를 사용 하 여 오류를 조사 하는 방법을 알아봅니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5cbd501b9ccb408ee23cb5c8ed9cde2689ef79fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617844"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>ITSMC 대시보드를 사용 하 여 오류 조사

이 문서에는 ITSMC (IT 서비스 관리 커넥터) 대시보드에 대 한 정보가 포함 되어 있습니다. 대시보드는 커넥터의 상태를 조사 하는 데 도움이 됩니다.

## <a name="view-errors"></a>오류 보기

대시보드에서 오류를 보려면 다음을 수행 합니다.

1. **모든 리소스** 를 선택 하 고 **servicedesk (*작업 영역 이름*)** 를 찾습니다.

   ![Azure 서비스의 리소스를 보여 주는 스크린샷](media/itsmc-definition/create-new-connection-from-resource.png)

2. 왼쪽 창의 **작업 영역 데이터 원본** 에서 **itsm 연결** 을 선택 합니다.

   ![작업 공간 데이터 원본에서 ITSM 연결을 선택 하는 것을 보여 주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)

3. **요약** 의 **IT 서비스 관리 커넥터** 영역에서 **요약 보기** 를 선택 합니다.

   ![보기 요약 단추를 표시 하는 스크린샷](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 그래프가 **IT 서비스 관리 커넥터** 영역에 표시 되 면 다음을 선택 합니다.

   ![그래프의 선택 항목을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 대시보드가 표시 됩니다. 커넥터에서 상태 및 오류를 검토 하는 데 사용 합니다.
   
   ![대시보드의 커넥터 상태를 보여 주는 스크린샷](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>대시보드 요소 이해

대시보드는이 커넥터를 통해 ITSM 도구에 전송 된 경고에 대 한 정보를 포함 합니다. 대시보드는 4 개의 부분으로 분할 됩니다.

### <a name="created-work-items"></a>만든 작업 항목 

**만든 작업 항목** 영역에서 그래프 및 아래 표에는 유형별 작업 항목 수가 포함 되어 있습니다. 그래프나 표를 선택 하면 작업 항목에 대 한 자세한 정보를 볼 수 있습니다.

![만든 작업 항목을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>영향을 받는 컴퓨터 

영향을 **받는 컴퓨터** 영역에서 표는 컴퓨터 및 연결 된 작업 항목을 나열 합니다. 테이블에서 행을 선택 하면 컴퓨터에 대 한 자세한 정보를 얻을 수 있습니다.

테이블에 제한 된 수의 행이 포함 되어 있습니다. 모든 행을 표시 하려면 **모두 보기** 를 선택 합니다.

![영향을 받는 컴퓨터를 표시 하는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>커넥터 상태 

**커넥터 상태** 영역에서 그래프 및 아래 표에는 커넥터의 상태에 대 한 메시지가 포함 되어 있습니다. 테이블에서 그래프 또는 행을 선택 하 여 메시지에 대 한 자세한 정보를 얻을 수 있습니다.

테이블에 제한 된 수의 행이 포함 되어 있습니다. 모든 행을 표시 하려면 **모두 보기** 를 선택 합니다.

테이블의 메시지에 대 한 자세한 내용은 [이 문서](itsmc-dashboard-errors.md)를 참조 하세요.

![커넥터 상태를 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>경고 규칙 

**경고 규칙** 영역의 테이블에는 검색 된 경고 규칙 수에 대 한 정보가 포함 되어 있습니다. 테이블에서 행을 선택 하 여 검색 된 규칙에 대 한 자세한 정보를 얻을 수 있습니다.
    
테이블에 제한 된 수의 행이 포함 되어 있습니다. 모든 행을 표시 하려면 **모두 보기** 를 선택 합니다.

![경고 규칙을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
