---
title: 지역 간에 Azure Automanage 가상 머신 이동
description: 지역 간에 자동 관리 가상 머신을 이동 하는 방법을 알아봅니다.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101650468"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Azure Automanage 가상 머신을 다른 지역으로 이동
이 문서에서는 VM (가상 머신)을 다른 지역으로 이동할 때 자동 관리를 사용 하도록 설정 하는 방법을 설명 합니다. 여러 가지 이유로 가상 컴퓨터를 다른 지역으로 이동 하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용 하 여 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나 용량 계획 요구 사항에 대 한 응답으로 사용할 수 있습니다. 이동 하는 Vm은 현재 자동으로 관리 될 수 있으며 이동 후 자동으로 관리 되도록 할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
* [Automanage에서](./automanage-virtual-machines.md#prerequisites)대상 지역이 지원 되는지 확인 합니다.
* Log Analytics 작업 영역, Automation 계정 지역 및 대상 지역이 [여기](../automation/how-to/region-mappings.md)에서 지역 매핑에서 지 원하는 모든 지역 인지 확인 합니다.

## <a name="prepare-your-automanaged-vms-for-moving"></a>이동 하기 위해 Automanaged Vm 준비
Automanage Vm에서 Automanage를 사용 하지 않도록 설정 합니다. Automanage 블레이드에서 Vm을 선택 하 고 Automanage 블레이드에서 **automanage 사용 안 함** 을 클릭 하 여이 작업을 수행할 수 있습니다.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Automanaged Vm 이동 및 Automanaged 다시 사용
Vm을 이동 하는 방법에 대 한 자세한 내용은이 [문서](../resource-mover/tutorial-move-region-virtual-machines.md)를 참조 하세요.

여러 지역에서 Vm을 이동한 후에는 다시 자동 관리 기능을 다시 사용 하도록 설정할 수 있습니다. 세부 정보는 [여기](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Automanage에 대해 자세히 알아보기](./automanage-virtual-machines.md)
* [Azure Automanage에 대해 자주 묻는 질문과 대답 보기](./faq.md)