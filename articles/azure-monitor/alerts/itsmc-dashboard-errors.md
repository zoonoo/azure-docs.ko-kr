---
title: ITSMC 대시보드의 커넥터 상태 오류
description: IT 서비스 관리 커넥터 대시보드에 있는 일반적인 오류에 대해 알아봅니다.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471535"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>ITSMC 대시보드의 커넥터 상태 오류

ITSMC(IT 서비스 관리 커넥터) 대시보드는 커넥터에서 문제를 해결하는 데 도움이 될 수 있는 오류를 표시합니다.

다음 섹션에서는 대시보드의 커넥터 상태 섹션에 표시되는 일반적인 오류 및 해결 방법에 대해 설명합니다.

## <a name="unexpected-response"></a>예기치 않은 응답

**오류**: “성공 상태 코드와 함께 ServiceNow에서 예기치 않은 응답이 발생했습니다. 응답: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [ { "transform_map": "OMS Incident", "table": "incident", "status": "error", "error_message": "{Target record not found|Invalid table|Invalid staging table" }"

**원인**: ServiceNow는 다음과 같은 경우 이 오류를 반환합니다.

* ServiceNow 인스턴스에 배포된 사용자 지정 스크립트로 인해 인시던트가 무시됩니다.
* “OMS 통합자 앱” 코드가 ServiceNow 쪽에서 수정되었습니다(예: `onBefore` 스크립트를 통해).

**해결 방법**: 모든 사용자 지정 스크립트 또는 코드 수정을 사용하지 않습니다.

## <a name="exception-update-failure"></a>예외 업데이트 실패

**오류**: "{"error":{"message":"Operation Failed","detail":"ACL Exception Update Failed due to security constraints"}"

**원인**: ServiceNow 권한이 잘못 구성되었습니다.

**해결 방법**: 모든 역할이 [지정](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)된 대로 올바르게 할당되었는지 확인합니다.

## <a name="problem-sending-a-request"></a>요청 전송 관련 문제

**오류**: “요청을 보내던 중 오류가 발생했습니다.”

**원인**: ServiceNow 인스턴스를 사용할 수 없습니다.

**해결 방법**: ServiceNow에서 인스턴스를 확인합니다. 삭제되었거나 사용할 수 없는 상태일 수 있습니다.

## <a name="servicenow-rate-problem"></a>ServiceNow 속도 문제

**오류**: “ServiceDeskHttpBadRequestException: StatusCode=429”

**원인**: ServiceNow 속도 제한이 너무 높거나 낮습니다.

**해결 방법**: [ServiceNow 설명서](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)에 설명된 대로 ServiceNow 인스턴스의 속도 제한을 높이거나 취소합니다.

## <a name="invalid-refresh-token"></a>잘못된 새로 고침 토큰

**오류**: 
  * “AccessToken 및 RefreshToken가 잘못되었습니다. 사용자가 다시 인증해야 합니다.”
  * “이벤트, 경고, 인시던트에 대한 템플릿 구성을 동기화할 수 없습니다. 자세한 내용은 예외 메시지를 참조하세요.”

**원인**: 새로 고침 토큰이 만료되었습니다.

**해결 방법**: [동기화 문제를 수동으로 해결하는 방법](./itsmc-resync-servicenow.md)에 설명된 대로 ITSMC를 동기화하여 새로운 새로 고침 토큰을 생성합니다.

## <a name="missing-connector"></a>누락된 커넥터

**오류**: “경고 {alertName}에 대한 작업 항목을 생성/업데이트할 수 없습니다. ITSM 커넥터 {connectionIdentifier}가 없거나 삭제되었습니다.”

**원인**: ITSMC가 삭제되었습니다.

**해결 방법**: ITSMC는 삭제되었지만 정의된 ITSM(IT Service Management) 작업 그룹은 아직 연결되어 있습니다. 다음 세 가지 옵션으로 이 문제를 해결할 수 있습니다.

* 해당 작업 그룹을 찾아서 사용하지 않거나 삭제합니다.
* 기존 ITSMC 인스턴스를 사용하도록 [작업 그룹을 다시 구성](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)합니다.
* [새 ITSMC 인스턴스를 만들고](./itsmc-definition.md#create-an-itsm-connection) [이 인스턴스를 사용하도록 작업 그룹을 다시 구성](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)합니다.

## <a name="lack-of-connection-details"></a>연결 세부 정보 부족

**오류**: “문제가 발생했습니다. 연결 세부 정보를 가져올 수 없습니다.” 이 오류는 ITSM 작업 그룹을 정의할 때 나타납니다.

**원인**: 오류는 다음과 같은 경우에 나타납니다.

* 새로 만든 ITSM 커넥터 인스턴스가 아직 초기 동기화를 완료하지 않았습니다.
* 커넥터가 올바르게 정의되지 않았습니다.

**해결 방법**: 

* 새 ITSMC 인스턴스를 만들면 작업 항목 템플릿, 작업 항목과 같은 ITSM 시스템의 정보가 동기화됩니다. [ITSMC를 동기화하여 새 새로 고침 토큰을 생성](./itsmc-resync-servicenow.md)합니다.
* [ITSMC의 연결 세부 정보를 검토](./itsmc-connections-servicenow.md#create-a-connection)하고 ITSMC가 성공적으로 [동기화](./itsmc-resync-servicenow.md)할 수 있는지 확인합니다.


## <a name="ip-restrictions"></a>IP 제한
**오류**: “잘못된 요청으로 인해 이름이 “XXX”인 ITSM 연결을 추가하지 못했습니다. 오류: 잘못된 요청입니다. 연결에 제공된 매개 변수가 잘못되었습니다. Http 예외: 상태 코드를 사용할 수 없습니다.”

**원인**: ITSM 애플리케이션의 IP 주소는 파트너 ITSM 도구의 ITSM 연결을 허용하지 않습니다.

**해결 방법**: 파트너 ITSM 도구에서 ITSM 연결을 허용하기 위해 ITSM IP 주소를 나열하려면 LogAnalytics 작업 영역이 속한 Azure 지역의 전체 공용 IP 범위를 나열하는 것이 좋습니다. [여기에서 세부 정보를 참조하세요](https://www.microsoft.com/download/details.aspx?id=56519). 미국 동부/서부 유럽/미국 동부2/서부 유럽2/미국 중남부 지역의 경우 고객이 ActionGroup 네트워크 태그만 나열할 수 있습니다.
