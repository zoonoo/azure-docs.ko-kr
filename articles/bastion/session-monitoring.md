---
title: Azure 요새 세션 모니터링 및 관리 | 마이크로 소프트 문서
description: 이 문서에서는 진행 중인 세션을 선택하고 강제로 연결을 끊거나 삭제하는 방법을 알아봅니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619187"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 요새에 대한 세션 모니터링 및 관리

Bastion 서비스가 프로비전되고 가상 네트워크에 배포되면 이 가상 네트워크의 모든 VM에 원활하게 연결할 수 있습니다. 사용자가 워크로드에 연결할 때 Azure Bastion을 사용하여 원격 세션을 모니터링하고 빠른 관리 작업을 수행할 수 있습니다. Azure Bastion 세션 모니터링을 사용하면 어떤 사용자가 어떤 VM에 연결되어 있는지 볼 수 있습니다. 사용자가 연결한 IP, 연결된 시간 및 연결 시기를 보여 주는 것입니다. 세션 관리 환경을 사용하면 진행 중인 세션을 선택하고 세션의 연결을 강제로 끊거나 삭제하여 진행 중인 세션에서 사용자를 분리할 수 있습니다.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>원격 세션 모니터링

1. Azure [포털에서](https://portal.azure.com)Azure 요새 리소스로 이동하여 Azure 요새 페이지에서 **세션을** 선택합니다.

   ![세션](./media/session-monitoring/sessions.png)
2. **세션** 페이지에서 진행 중인 원격 세션을 오른쪽에 볼 수 있습니다.

   ![세션 보기](./media/session-monitoring/view-session.png)
3. **새로 고침을** 선택하여 업데이트된 원격 세션 목록을 확인합니다. 새로 고침을 선택하면 Azure Bastion이 최신 모니터링 정보를 가져오고 포털에서 새로 고칩니다.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 게이트웨이 관리자에서 인바운드 트래픽에 대해 포트 4443을 활성화하여 세션 모니터링이 작동하도록 합니다.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>진행 중인 원격 세션 삭제 또는 강제 연결 해제

세션 집합을 선택하고 강제로 연결을 끊을 수 있습니다. 다음 단계에서는 원격 세션을 삭제하는 방법을 보여 줍니다.

1. Azure 요새 리소스로 이동하여 Azure 요새 페이지에서 **세션을** 선택합니다.

   ![탐색](./media/session-monitoring/navigate.png)
2. 세션을 선택하면 원격 세션 목록이 표시됩니다.

   ![목록 세션](./media/session-monitoring/list.png)
3. 특정 원격 세션을 선택한 다음 세션 행의 오른쪽 끝에 있는 세 개의 타원을 선택한 다음 **삭제를**선택합니다.

   ![delete](./media/session-monitoring/delete.png)
4. 삭제를 선택하면 원격 세션의 연결이 끊어지고 원격 세션에서 "연결이 끊어졌습니다" 메시지가 사용자에게 표시됩니다.

   ![연결 끊기](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>다음 단계

[바스티온 자주 묻는 질문 읽기.](bastion-faq.md)