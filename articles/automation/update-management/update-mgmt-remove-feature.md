---
title: Azure Automation 업데이트 관리 기능 제거
description: 이 문서에서는 업데이트 관리 사용을 중지 하 고 Log Analytics 작업 영역에서 Automation 계정의 연결을 해제 하는 방법을 설명 합니다.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4b4946da9f63299c7ba2b383d6c153673595a1ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450393"
---
# <a name="remove-update-management-from-automation-account"></a>Automation 계정에서 업데이트 관리 제거

Azure Automation 업데이트 관리를 사용 하 여 가상 머신에서 업데이트 관리를 사용 하도록 설정한 후에는 사용을 중지 하 고 계정 및 연결 된 Log Analytics 작업 영역에서 구성을 제거할 수 있습니다.  이 문서에서는 관리 되는 Vm, Automation 계정 및 Log Analytics 작업 영역에서 업데이트 관리를 완전히 제거 하는 방법을 설명 합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="remove-management-of-vms"></a>Vm 관리 제거

업데이트 관리을 제거 하기 전에 먼저 Vm 관리를 중지 해야 합니다. [업데이트 관리에서 Vm 제거](update-mgmt-remove-vms.md) 를 참조 하 여 기능에서 등록을 취소 합니다.

## <a name="remove-updatemanagement-solution"></a>UpdateManagement 솔루션 제거

작업 영역에서 Automation 계정의 연결을 해제 하기 전에 다음 단계를 수행 하 여 업데이트 관리를 완전히 제거 해야 합니다. 작업 영역에서 **업데이트** 솔루션을 제거 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작 하면 목록에서 입력을 기준으로 제안을 필터링 합니다. **Log Analytics**를 선택합니다.

3. Log Analytics 작업 영역 목록에서 업데이트 관리을 사용 하도록 설정할 때 선택한 작업 영역을 선택 합니다.

4. 왼쪽에서 **솔루션**을 선택 합니다.  

5. 솔루션 목록에서 **업데이트 (작업 영역 이름)** 를 선택 합니다. 솔루션에 대 한 **개요** 페이지에서 **삭제**를 선택 합니다. 확인 하 라는 메시지가 표시 되 면 **예**를 선택 합니다.

## <a name="unlink-workspace-from-automation-account"></a>Automation 계정에서 작업 영역 연결 해제

1. Azure Portal에서 **Automation 계정**을 선택합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역**을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 선택 하 고 프롬프트에 응답 합니다.

   ![작업 영역 연결 해제 페이지](media/update-mgmt-remove-feature/automation-unlink-workspace-blade.png)

Log Analytics 작업 영역의 연결을 해제 하려고 시도 하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

또는 작업 영역 내 Automation 계정에서 Log Analytics 작업 영역의 연결을 해제할 수 있습니다.

1. Azure Portal에서 **Log Analytics**를 선택합니다.

2. 작업 영역에서 **관련 리소스**아래에 있는 **Automation 계정** 을 선택 합니다.

3. Automation 계정 페이지에서 **계정 연결 해제**를 선택합니다.

Automation 계정의 연결을 해제 하려고 시도 하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

## <a name="cleanup-automation-account"></a>Automation 계정 정리

이전 버전의 Azure SQL 모니터링을 지원 하도록 업데이트 관리 구성 된 경우 기능을 설치 하면 제거 해야 하는 Automation 자산이 생성 될 수 있습니다. 업데이트 관리의 경우 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

   * 업데이트 일정 - 각 항목에는 사용자가 만든 업데이트 배포와 일치하는 이름이 있습니다.
   * 업데이트 관리에 대해 생성 된 하이브리드 작업자 그룹-각각은 *machine1.contoso.com_9ceb8108-26c9-b6b3-227600d715c8)* 와 비슷하게 이름이 지정 됩니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용 하도록 설정 하려면 [Automation 계정에서 업데이트 관리 사용](update-mgmt-enable-automation-account.md)을 참조 하 고, [Azure Portal 탐색 하 여 업데이트 관리를 사용 하도록 설정](update-mgmt-enable-portal.md)하거나, [runbook에서 업데이트 관리를 사용 하도록](update-mgmt-enable-runbook.md)설정 하거나, [Azure VM에서 업데이트 관리을 사용](update-mgmt-enable-vm.md)하도록 설정 합니다.
