---
title: Azure 방호 세션 모니터링 및 관리 | Microsoft Docs
description: 이 문서에서는 진행 중인 세션을 선택 하 고 강제로 연결을 끊거나 삭제 하는 방법에 대해 알아봅니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619187"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 방호의 세션 모니터링 및 관리

요새 서비스가 프로 비전 되 고 가상 네트워크에 배포 되 면이를 사용 하 여이 가상 네트워크의 VM에 원활 하 게 연결할 수 있습니다. 사용자가 작업에 연결 하면 Azure 방호를 사용 하 여 원격 세션을 모니터링 하 고 신속한 관리 작업을 수행할 수 있습니다. Azure 방호 세션 모니터링을 통해 어떤 사용자가 어떤 Vm에 연결 되어 있는 지 확인할 수 있습니다. 사용자가 연결 된 IP, 연결 된 시간 및 연결 된 시간을 표시 합니다. 세션 관리 환경을 통해 진행 중인 세션을 선택 하 고 세션을 강제로 연결 해제 하거나 삭제 하 여 진행 중인 세션에서 사용자의 연결을 끊을 수 있습니다.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>원격 세션 모니터링

1. [Azure Portal](https://portal.azure.com)에서 azure 방호 리소스로 이동 하 고 azure 방호 페이지에서 **세션** 을 선택 합니다.

   ![세션](./media/session-monitoring/sessions.png)
2. **세션** 페이지의 오른쪽에서 진행 중인 원격 세션을 볼 수 있습니다.

   ![세션 보기](./media/session-monitoring/view-session.png)
3. **새로 고침** 을 선택 하 여 원격 세션의 업데이트 된 목록을 표시 합니다. 새로 고침을 선택 하면 Azure 방호에서 최신 모니터링 정보를 가져오고 포털에서 새로 고칩니다.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 세션 모니터링이 작동 하려면 게이트웨이 관리자에서 인바운드 트래픽에 대해 포트 4443을 사용 하도록 설정 합니다.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>진행 중인 원격 세션 삭제 또는 강제 연결 끊기

세션 집합을 선택 하 고 강제로 연결을 끊을 수 있습니다. 다음 단계는 원격 세션을 삭제 하는 방법을 보여 줍니다.

1. Azure 방호 리소스로 이동 하 여 Azure 방호 페이지에서 **세션** 을 선택 합니다.

   ![탐색](./media/session-monitoring/navigate.png)
2. 세션을 선택 하면 원격 세션의 목록이 표시 됩니다.

   ![세션 나열](./media/session-monitoring/list.png)
3. 특정 원격 세션을 선택한 다음 세션 행의 오른쪽 끝에 있는 세 개의 줄임표를 선택 하 고 **삭제**를 선택 합니다.

   ![삭제](./media/session-monitoring/delete.png)
4. 삭제를 선택 하면 원격 세션의 연결을 끊고 사용자에 게 원격 세션의 "연결 끊김" 메시지가 표시 됩니다.

   ![연결 끊기](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>다음 단계

[요새 FAQ](bastion-faq.md)를 읽어 보세요.