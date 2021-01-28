---
title: 일반 오류
description: 이 문서에는 대시보드에 있는 일반적인 오류에 대 한 정보가 포함 되어 있습니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: be6d47d8f40746bfb2154ddb62cf2e9ce93e74aa
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955686"
---
# <a name="errors-in-the-connector-status-section"></a>커넥터 상태 섹션의 오류

대시보드의 커넥터 상태 목록 섹션에서 ITSM 커넥터의 문제를 해결 하는 데 도움이 될 수 있는 오류를 찾을 수 있습니다.

## <a name="status-common-errors"></a>상태 일반 오류

이 섹션에서는 커넥터 상태 섹션에 표시 되는 일반적인 오류와 이러한 오류를 해결 하는 방법을 확인할 수 있습니다.

* **오류**: "성공 상태 코드와 함께 ServiceNow에서 예기치 않은 응답이 발생 했습니다. 응답: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [{"transform_map": "OMS 인시던트", "table": "인시던트", "상태": "오류", "error_message": "{대상 레코드를 찾을 수 없습니다. | 잘못 된 테이블 | 잘못 된 준비 테이블 "}"

    **원인**: 이러한 오류는 다음과 같은 경우 ServiceNow에서 반환 됩니다.
  * ServiceNow 인스턴스에 배포 된 사용자 지정 스크립트는 인시던트를 무시 합니다.
  * "OMS 통합자 앱" 코드 자체가 ServiceNow 쪽에서 수정 되었습니다 (예: onBefore 스크립트).

  **해결** 방법: 모든 사용자 지정 스크립트 또는 코드 수정을 사용 하지 않도록 설정 합니다.

* **오류**: "{" error ": {" message ":" 작업이 실패 했습니다. "," 세부 정보 ":" 보안 제약 조건으로 인해 ACL 예외를 업데이트 하지 못했습니다. "}"

    **원인**: ServiceNow 권한 잘못 구성

    **해결** 방법: 모든 역할이 [지정](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)된 대로 올바르게 할당 되었는지 확인 합니다.

* **오류**: "요청을 보내는 동안 오류가 발생 했습니다."

    **원인**: "ServiceNow 인스턴스를 사용할 수 없음"

    **해결** 방법: ServiceNow에서 인스턴스를 확인 합니다. 삭제 되었거나 사용할 수 없게 될 수 있습니다.

* **오류**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

    **원인**: ServiceNow 속도가 너무 높음/낮음입니다.

    **해결** 방법: [여기](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)에 설명 된 대로 ServiceNow 인스턴스의 요율 제한을 늘리거나 취소 합니다.

* **오류**: "AccessToken 및 RefreshToken가 잘못 되었습니다. 사용자가 다시 인증 해야 합니다. "

    **원인**: 새로 고침 토큰이 만료 되었습니다.

    **해결** 방법: [여기](./itsmc-resync-servicenow.md)에 설명 된 대로 ITSM 커넥터를 동기화 하 여 새로운 새로 고침 토큰을 생성 합니다.

* **오류**: "경고 {alertname}에 대 한 작업 항목을 만들거나 업데이트할 수 없습니다. ITSM 커넥터 {connectionIdentifier}이 (가) 없거나 삭제 되었습니다. "

    **원인**: ITSM 커넥터 삭제 되었습니다.

    **해결** 방법: ITSM 커넥터 삭제 되었지만 아직 itsm 작업 그룹이 연결 되어 있습니다. 이 문제를 해결 하는 두 가지 옵션이 있습니다.
  * 이러한 작업 그룹을 찾아서 사용 하지 않도록 설정 하거나 삭제 합니다.
  * 기존 ITSM 커넥터를 사용 하도록 [작업 그룹을 다시 구성](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 합니다.
  * [새 ITSM 커넥터를 만들고](./itsmc-definition.md#create-an-itsm-connection) [작업 그룹을 다시 구성 하 여 사용](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)합니다.

## <a name="ui-common-errors"></a>UI 일반 오류

* **오류**: "오류가 발생 했습니다. 연결 정보를 가져올 수 없습니다. " 고객이 ITSM 작업 그룹을 정의 하는 경우이 오류가 표시 됩니다.

    **원인**: 이러한 오류는 다음과 같은 경우에 표시 됩니다.
    * 새로 만든 ITSM 커넥터는 아직 초기 동기화를 완료 해야 합니다.
    * 커넥터가 올바르게 정의 되지 않았습니다.

    **해결 방법**: 
    * 새 ITSM 커넥터가 만들어지면 ITSM 커넥터는 작업 항목 템플릿 및 작업 항목과 같은 ITSM 시스템의 정보를 동기화 하기 시작 합니다. [여기](./itsmc-resync-servicenow.md)에 설명 된 대로 ITSM 커넥터를 동기화 하 여 새로운 새로 고침 토큰을 생성 합니다.
    * [여기](./itsmc-connections-servicenow.md#create-a-connection) 에 설명 된 대로 itsm 커넥터에서 연결 정보를 검토 하 고 itsm 커넥터가 성공적으로 [동기화](./itsmc-resync-servicenow.md)될 수 있는지 확인 합니다.
