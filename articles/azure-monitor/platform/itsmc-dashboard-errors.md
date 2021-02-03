---
title: ITSMC 대시보드의 커넥터 상태 오류
description: IT 서비스 관리 커넥터 대시보드에 있는 일반적인 오류에 대해 알아봅니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: d1ba698cd95a074c021aa351a98eb12fc8ae0fc3
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492521"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>ITSMC 대시보드의 커넥터 상태 오류

ITSMC (IT 서비스 관리 커넥터) 대시보드는 커넥터에서 문제를 해결 하는 데 도움이 될 수 있는 오류를 제공 합니다.

다음 섹션에서는 대시보드의 커넥터 상태 섹션에 표시 되는 일반적인 오류 및 해결 방법에 대해 설명 합니다.

## <a name="unexpected-response"></a>예기치 않은 응답

**오류**: "성공 상태 코드와 함께 ServiceNow에서 예기치 않은 응답이 발생 했습니다. 응답: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [{"transform_map": "OMS 인시던트", "table": "인시던트", "상태": "오류", "error_message": "{대상 레코드를 찾을 수 없습니다. | 잘못 된 테이블 | 잘못 된 준비 테이블 "}"

**원인**: ServiceNow는 다음과 같은 경우이 오류를 반환 합니다.

* ServiceNow 인스턴스에 배포 된 사용자 지정 스크립트는 인시던트를 무시 합니다.
* "OMS 통합자 앱" 코드는 ServiceNow 쪽에서 수정 되었습니다 (예: 스크립트를 통해 `onBefore` ).

**해결** 방법: 모든 사용자 지정 스크립트 또는 코드 수정을 사용 하지 않도록 설정 합니다.

## <a name="exception-update-failure"></a>예외 업데이트 실패

**오류**: "{" error ": {" message ":" 작업이 실패 했습니다. "," 세부 정보 ":" 보안 제약 조건으로 인해 ACL 예외를 업데이트 하지 못했습니다. "}"

**원인**: ServiceNow 권한이 잘못 구성 되었습니다.

**해결** 방법: 모든 역할이 [지정 된](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)대로 제대로 할당 되었는지 확인 합니다.

## <a name="problem-sending-a-request"></a>요청을 보내는 데 문제가 있습니다.

**오류**: "요청을 보내는 동안 오류가 발생 했습니다."

**원인**: ServiceNow 인스턴스를 사용할 수 없습니다.

**해결** 방법: ServiceNow에서 인스턴스를 확인 합니다. 삭제 되었거나 사용할 수 없는 상태일 수 있습니다.

## <a name="servicenow-rate-problem"></a>ServiceNow 요금 문제

**오류**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

**원인**: ServiceNow 속도가 너무 크거나 너무 낮습니다.

**해결** 방법: [servicenow 설명서](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)에 설명 된 대로 servicenow 인스턴스의 요율 제한을 늘리거나 취소 합니다.

## <a name="invalid-refresh-token"></a>잘못 된 새로 고침 토큰

**오류**: "AccessToken 및 RefreshToken가 잘못 되었습니다. 사용자가 다시 인증 해야 합니다. "

**원인**: 새로 고침 토큰이 만료 되었습니다.

**해결** 방법: 동기화 [문제를 수동으로 수정 하는 방법](./itsmc-resync-servicenow.md)에 설명 된 대로 itsmc를 동기화 하 여 새로운 새로 고침 토큰을 생성 합니다.

## <a name="missing-connector"></a>누락 된 커넥터

**오류**: "경고 {alertname}에 대 한 작업 항목을 만들거나 업데이트할 수 없습니다. ITSM 커넥터 {connectionIdentifier}이 (가) 없거나 삭제 되었습니다. "

**원인**: itsmc가 삭제 되었습니다.

**해결** 방법: itsmc는 삭제 되었지만 정의 된 Itsmc (Service Management) 작업 그룹은 아직 연결 되어 있습니다. 이 문제를 해결 하는 세 가지 옵션이 있습니다.

* 이러한 작업 그룹을 찾아서 사용 하지 않도록 설정 하거나 삭제 합니다.
* 기존 ITSMC 인스턴스를 사용 하도록 [작업 그룹을 다시 구성](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 합니다.
* [새 ITSMC 인스턴스를 만들고이 인스턴스](./itsmc-definition.md#create-an-itsm-connection) [를 사용 하도록 작업 그룹을 다시 구성](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)합니다.

## <a name="lack-of-connection-details"></a>연결 정보가 부족 합니다.

**오류**: "오류가 발생 했습니다. 연결 정보를 가져올 수 없습니다. " 이 오류는 ITSM 작업 그룹을 정의할 때 나타납니다.

**원인**: 이러한 오류는 다음과 같은 경우에 나타납니다.

* 새로 만든 ITSM 커넥터 인스턴스는 아직 초기 동기화를 완료 해야 합니다.
* 커넥터가 올바르게 정의 되지 않았습니다.

**해결 방법**: 

* 새 ITSMC 인스턴스를 만들면 작업 항목 템플릿 및 작업 항목과 같은 ITSMC 시스템에서 정보를 동기화 하기 시작 합니다. [ITSMC를 동기화 하 여 새 새로 고침 토큰을 생성](./itsmc-resync-servicenow.md)합니다.
* [Itsmc에서 연결 정보를 검토](./itsmc-connections-servicenow.md#create-a-connection) 하 고 itsmc가 성공적으로 [동기화](./itsmc-resync-servicenow.md)할 수 있는지 확인 합니다.
