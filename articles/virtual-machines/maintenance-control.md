---
title: Azure Portal를 사용 하 여 Azure 가상 머신에 대 한 유지 관리 제어 개요
description: 유지 관리 제어를 사용 하 여 Azure Vm에 유지 관리를 적용 하는 시기를 제어 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4d126d1e6bb92d2079ac3c0d95f754cb9aab3c21
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978614"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>유지 관리 제어를 사용 하 여 플랫폼 업데이트 관리 

유지 관리 제어를 사용 하 여 다시 부팅이 필요 하지 않은 플랫폼 업데이트를 관리 합니다. Azure는 안정성, 성능, 보안을 개선 하거나 새로운 기능을 시작 하기 위해 인프라를 자주 업데이트 합니다. 대부분의 업데이트는 사용자에 게 투명 합니다. 게임, 미디어 스트리밍 및 금융 트랜잭션과 같은 일부 중요 한 워크 로드는 유지 관리를 위해 VM을 고정 하거나 연결을 끊는 경우에도 몇 초 정도 허용할 수 없습니다. 유지 관리 제어는 플랫폼 업데이트를 대기 하는 옵션을 제공 하 고 35 일의 롤링 창 내에서 적용 합니다. 

유지 관리 제어를 통해 격리 된 Vm 및 Azure 전용 호스트에 업데이트를 적용 하는 시기를 결정할 수 있습니다.

유지 관리 제어를 사용 하 여 다음을 수행할 수 있습니다.
- 업데이트를 하나의 업데이트 패키지로 일괄 처리 합니다.
- 업데이트를 적용 하려면 최대 35 일 동안 기다립니다. 
- [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)를 사용 하 여 유지 관리 기간에 대 한 플랫폼 업데이트를 자동화 합니다.
- 유지 관리 구성은 구독 및 리소스 그룹에 걸쳐 작동 합니다. 

## <a name="limitations"></a>제한 사항

- Vm은 [전용 호스트](./dedicated-hosts.md)에 있거나 [격리 된 VM 크기](isolation.md)를 사용 하 여 만들어야 합니다.
- 35 일 후 업데이트가 자동으로 적용 됩니다.
- 사용자에 게 **리소스 참여자** 액세스 권한이 있어야 합니다.

## <a name="management-options"></a>관리 옵션

다음 옵션 중 하나를 사용 하 여 유지 관리 구성을 만들고 관리할 수 있습니다.

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

Azure Functions 샘플은 유지 관리 [제어 및 Azure Functions를 사용 하 여 유지 관리 업데이트 예약](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조 하세요.