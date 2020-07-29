---
title: Automation 계정에서 변경 내용 추적 및 인벤토리에 사용할 작업 영역의 연결 해제
description: 이 문서에서는 Automation 계정에서 변경 내용 추적 및 인벤토리에 사용할 Log Analytics 작업 영역의 연결을 해제하는 방법을 설명합니다.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828256"
---
# <a name="unlink-workspace-from-automation-account"></a>Automation 계정에서 작업 영역 연결 해제

[변경 내용 추적 및 인벤토리](change-tracking.md) 작업을 사용하도록 설정할 때 Automation 계정을 Log Analytics 작업 영역과 통합하지 않도록 선택할 수 있습니다. 이 문서에서는 계정에서 작업 영역을 연결 해제하는 방법을 설명합니다.

1. https://portal.azure.com 에서 Azure에 로그인합니다.

2. VM에 대한 업데이트 관리를 제거합니다. [변경 내용 추적 및 인벤토리에서 VM 제거](automation-remove-vms-from-change-tracking.md)를 참조하세요.

3. 변경 내용 추적 및 인벤토리에 이전 버전의 Azure SQL 모니터링이 포함된 경우 기능을 설치할 때 제거해야 하는 Automation 자산이 생성되었을 수도 있습니다. 이러한 자산을 찾아 제거합니다.

4. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역**을 선택합니다.

5. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Azure Automation이 Log Analytics 작업 영역에서 연결을 해제하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

또는 작업 영역 내 Automation 계정에서 Log Analytics 작업 영역의 연결을 해제할 수 있습니다.

1. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정**을 선택합니다. 
2. Automation 계정 페이지에서 **계정 연결 해제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적 및 인벤토리를 사용하려면 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.
