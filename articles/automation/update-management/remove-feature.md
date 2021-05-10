---
title: Azure Automation 업데이트 관리 기능 제거
description: 이 문서에서는 업데이트 관리 사용을 중지하고 Log Analytics 작업 영역에서 Automation 계정의 연결을 해제하는 방법을 설명합니다.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222586"
---
# <a name="remove-update-management-from-automation-account"></a>Automation 계정에서 업데이트 관리 제거

Azure Automation 업데이트 관리를 사용하여 가상 머신에서 업데이트 관리를 사용하도록 설정하면 업데이트 관리 사용을 중지하고 계정 및 연결된 Log Analytics 작업 영역에서 구성을 제거할 수 있습니다.  이 문서에서는 관리형 VM, Automation 계정 및 Log Analytics 작업 영역에서 업데이트 관리를 완전히 제거하는 방법을 설명합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="remove-management-of-vms"></a>VM 관리 제거

업데이트 관리를 제거하기 전에 먼저 VM 관리를 중지해야 합니다. 이 기능에서 VM 등록을 취소하려면 [업데이트 관리에서 VM 제거](remove-vms.md)를 참조하세요.

## <a name="remove-updatemanagement-solution"></a>업데이트 관리 솔루션 제거

작업 영역에서 Automation 계정의 연결을 해제하려면 다음 단계를 수행하여 업데이트 관리를 완전히 제거해야 합니다. 작업 영역에서 **업데이트** 솔루션을 제거합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모든 서비스** 를 선택합니다. 리소스 목록에서 **Log Analytics** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록에서 제안 항목이 필터링됩니다. **Log Analytics** 를 선택합니다.

3. 업데이트 관리를 사용하도록 설정했을 때 선택한 작업 영역을 Log Analytics 작업 영역 목록에서 선택합니다.

4. 왼쪽에서 **솔루션** 을 선택합니다.  

5. 솔루션 목록에서 **업데이트(작업 영역 이름)** 를 선택합니다. 솔루션의 **개요** 페이지에서 **삭제** 를 선택합니다. 확인하라는 프롬프트가 나타나면 **예** 를 선택합니다.

## <a name="unlink-workspace-from-automation-account"></a>Automation 계정에서 작업 영역 연결 해제

1. Azure Portal에서 **Automation 계정** 을 선택합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 선택하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/remove-feature/automation-unlink-workspace-blade.png)

Log Analytics 작업 영역에서 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

또는 작업 영역 내 Automation 계정에서 Log Analytics 작업 영역의 연결을 해제할 수 있습니다.

1. Azure Portal에서 **Log Analytics** 를 선택합니다.

2. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정** 을 선택합니다.

3. Automation 계정 페이지에서 **계정 연결 해제** 를 선택합니다.

Automation 계정의 연결을 해제하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

## <a name="cleanup-automation-account"></a>Automation 계정 정리

업데이트 관리에 이전 버전의 Azure SQL 모니터링을 지원하도록 구성된 경우 기능을 설정하면 제거해야 할 Automation 자산이 생성되었을 수도 있습니다. 업데이트 관리의 경우 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

   * 업데이트 일정 - 각 항목에는 사용자가 만든 업데이트 배포와 일치하는 이름이 있습니다.
   * 업데이트 관리용으로 만든 Hybrid Worker 그룹 - 각 그룹에는 *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)* 과 비슷하게 이름이 지정됩니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용하도록 설정하려면 [Automation 계정에서 업데이트 관리 사용](enable-from-automation-account.md), [Azure Portal을 탐색하여 업데이트 관리 사용](enable-from-portal.md), [Runbook에서 업데이트 관리 사용](enable-from-runbook.md) 또는 [Azure VM에서 업데이트 관리 사용](enable-from-vm.md)을 참조하세요.
