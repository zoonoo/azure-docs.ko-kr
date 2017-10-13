---
title: "Azure에서 Linux VM에 대한 유지 관리 알림 처리 | Microsoft Docs"
description: "Azure에서 실행 중인 Linux 가상 컴퓨터에 대한 유지 관리 알림을 확인하고 셀프 서비스 유지 관리를 시작합니다."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 2fc96898c9b7ebefc7648b5819975d9015e76c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Linux 가상 컴퓨터에 대한 계획된 유지 관리 처리

Azure에서는 가상 컴퓨터에 대한 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 주기적으로 업데이트를 수행합니다. 업데이트란 호스팅 환경의 패치 적용 또는 하드웨어의 업그레이드 및 서비스 해제와 같은 변경 내용을 말합니다. 이러한 업데이트 중 대다수는 호스트된 가상 컴퓨터에 영향을 미치지 않고 수행됩니다. 그러나 업데이트가 다음 항목에 영향을 미치는 경우가 있습니다.

- 유지 관리를 다시 부팅하지 않아도 되는 경우 Azure에서는 호스트를 업데이트하는 동안 바로 마이그레이션을 사용하여 VM을 일시 중지합니다.

- 유지 관리를 다시 부팅해야 하는 경우 유지 관리가 계획된 시기에 대해 알림을 받을 수 있습니다. 이러한 경우에 사용자가 원하는 시점에 스스로 유지 관리를 시작할 수 있는 기간도 지정됩니다.


다시 부팅해야 하는 계획된 유지 관리는 웨이브에서 예약됩니다. 각 웨이브는 범위(지역)이 다릅니다.

- 웨이브는 고객에게 알림을 보내면서 시작합니다. 기본적으로 알림은 구독 소유자 및 공동 소유자에게 전송됩니다. 추가 수신자나 전자 메일, SMS 및 웹후크와 같은 메시징 옵션을 추가할 수 있습니다. 
- 알림 후 바로 셀프 서비스 기간이 설정됩니다. 이 기간 동안 이 웨이브에 포함되어 있는 가상 컴퓨터를 찾고, 사전 재배포를 사용하여 유지 관리를 시작할 수 있습니다. 
- 셀프 서비스 기간에 이어 예약된 유지 관리 기간이 시작됩니다. 이때 Azure는 가상 컴퓨터에 필요한 유지 관리를 예약하고 적용합니다.  

두 기간이 존재하는 이유는 Azure에서 유지 관리를 자동으로 시작하는 시기를 파악하면서 유지 관리를 시작하고 가상 컴퓨터를 다시 부팅하는 데 충분한 시간을 제공하기 위한 것입니다.

Azure CLI, PowerShell, REST API 및 Azure Portal을 사용하여 VM에 대한 유지 관리 기간을 쿼리하고 셀프 서비스 유지 관리를 시작합니다.

 > [!NOTE]
 > 유지 관리를 시작하려고 시도하다가 실패한 경우 Azure는 사용자 VM을 **건너뜀**으로 표시하고 예약된 유지 관리 기간 동안 재부팅하지 않습니다. 대신 새 일정으로 나중에 연결됩니다. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>CLI를 사용하여 유지 관리가 예약된 VM 찾기

계획된 유지 관리 정보는 [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view)를 사용하여 볼 수 있습니다.
 
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. VM에 영향을 미치는 예약된 유지 관리가 없는 경우 명령은 유지 관리 정보를 반환하지 않습니다. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

MaintenanceRedeployStatus의 다음과 같은 값이 반환됩니다. 

| 값 | 설명   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 이번에 VM에서 유지 관리를 시작할 수 있는지 여부를 나타냅니다. ||
| PreMaintenanceWindowStartTime         | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 시작 시간입니다. ||
| PreMaintenanceWindowEndTime           | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 종료 시간입니다. ||
| MaintenanceWindowStartTime            | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 예약 기간의 시작 시간입니다. ||
| MaintenanceWindowEndTime              | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 예약 기간의 종료 시간입니다. ||
| LastOperationResultCode               | VM에서 유지 관리를 시작하는 마지막 시도의 결과입니다. ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>CLI를 사용하여 VM에서 유지 관리 시작

`IsCustomerInitiatedMaintenanceAllowed`가 true로 설정되는 경우 다음 호출이 VM에서 유지 관리를 시작합니다.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="next-steps"></a>다음 단계

[예약된 이벤트](scheduled-events.md)를 사용하여 VM 내에서 유지 관리 이벤트에 등록하는 방법을 알아보세요.