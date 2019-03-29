---
title: Azure Site Recovery 서비스를 사용하여 재해 복구를 위해 보조 Azure 지역에 복제된 Azure IaaS VM을 장애 복구(failback)합니다.
description: Azure Site Recovery 서비스를 사용하여 Azure VM을 장애 복구(failback)하는 방법을 알아봅니다.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089708"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Azure 지역 간에 Azure VM 장애 복구(failback)

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 머신과 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 단일 Azure VM을 장애 복구(failback)하는 방법을 설명합니다. 장애 조치(Failover)한 후에 주 지역이 사용 가능해지면 장애 복구(failback)합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 보조 VM 장애 복구(failback)
> * 보조 지역으로 주 VM 다시 보호
> 
> [!NOTE]
> 
> 이 자습서는 대상 영역으로 장애 조치하여 최소 사용자 지정으로 복구하는 단계로 사용자를 안내하기 위한 것입니다. 네트워킹 고려 사항, 자동화 또는 문제 해결을 포함해 장애 조치와 관련된 다양한 측면에 대해 자세히 알아보고 싶다면 Azure VM에 대한 ‘방법’에 있는 문서를 참조하세요.

## <a name="prerequisites"></a>필수 조건

> * VM이 장애 조치(Failover) 커밋 상태이고 주 지역을 사용할 수 있는지 확인하면 새 리소스를 만들고 액세스할 수 있습니다.
> * 다시 보호가 사용 설정되어 있는지 확인합니다.

## <a name="fail-back-to-the-primary-region"></a>주 지역으로 장애 복구(failback)

VM이 다시 보호된 후 필요에 따라 원하는 시기에 주 지역으로 장애 복구(failback)할 수 있습니다.

1. Recovery Services 자격 증명 모음으로 이동합니다. 복제된 항목을 클릭하고 다시 보호된 VM을 선택합니다.

2. 다음이 표시되어야 합니다. 주 지역에서 테스트 장애 조치(failover) 및 장애 조치(failover)의 경우 블레이드와 유사합니다.
![주 지역으로 장애 복구](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. 주 지역으로 테스트 장애 조치(failover)를 수행하려면 테스트 장애 조치(failover)를 클릭합니다. 테스트 장애 조치(failover)에 복구 지점 및 Virtual Network를 선택하고 확인을 선택합니다. 주 지역에서 만들어진 사용자가 액세스하여 검사할 수 있는 테스트 VM이 보입니다.

4. 테스트 장애 조치(failover)가 만족스러우면 테스트 장애 조치(failover) 정리를 클릭하여 테스트 장애 조치(failover)에 대해 원본 지역에 만들어진 리소스를 정리할 수 있습니다.

5. 복제된 항목에서 장애 조치(failover)하려는 VM > 장애 조치(Failover)를 선택합니다.

6. 장애 조치(Failover)에서 장애 조치할 복구 지점을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
    1. 최신(기본값): 이 옵션은 Site Recovery 서비스의 모든 데이터를 처리하고 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
    2. 가장 최근에 처리됨: 이 옵션은 Site Recovery 서비스에서 처리한 최근 복구 지점으로 가상 머신을 되돌립니다.
    3. 사용자 지정: 특정 복구 지점으로 장애 조치(failover)하려면 이 옵션을 사용합니다. 이 옵션은 테스트 장애 조치(failover)를 수행하는 데 유용합니다.

7. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 가상 머신을 종료하려고 시도하는 경우 장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다를 선택합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. Site Recovery는 장애 조치 후 원본을 정리하지 않습니다.

8. 작업 페이지에서 장애 조치(failover) 진행 상황을 확인합니다.

9. 장애 조치 후에 가상 컴퓨터에 로그인하여 해당 가상 컴퓨터의 유효성을 검사합니다. 가상 머신에 대한 다른 복구 지점으로 이동하려면 복구 지점 변경 옵션을 사용할 수 있습니다.

10. 장애 조치된 가상 머신에 만족하면 장애 조치를 커밋할 수 있습니다. 커밋하면 서비스와 함께 사용할 수 있는 모든 복구 지점이 삭제됩니다. 복구 지점 변경 옵션은 더 이상 사용할 수 없습니다.

![주 지역 및 보조 지역의 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

위의 스크린샷을 보면 “ContosoWin2016” VM이 미국 중부에서 미국 동부로 장애 조치(failover)되었다가 미국 동부에서 미국 중부로 장애 복구(failback)되었습니다.

DR VM이 정지 할당 취소 상태로 유지됨을 참고하세요. 이 동작은 Azure Site Recovery가 가상 머신의 정보를 저장하므로 계획적인 것입니다. 이는 나중에 주 지역을 보조 지역으로 장애 조치(failover)하는 데 유용할 수 있습니다. 할당 취소된 가상 머신에 대해서는 요금이 부과되지 않으므로 그대로 유지해야 합니다.

> [!NOTE]
> 다시 보호 작업 흐름과 다시 보호 중에 발생하는 작업에 대한 자세한 내용은 [“방법” 섹션](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)을 참조하세요.
