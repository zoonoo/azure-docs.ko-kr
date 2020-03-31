---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296900"
---
로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. 함께 `isDefault: true` 구독 정보는 Azure CLI 명령에 대해 현재 활성화된 구독입니다. 이 구독은 Azure 기계 학습 작업 영역을 포함하는 것과 같아야 합니다. 작업 영역의 개요 페이지를 방문하여 [Azure 포털에서](https://portal.azure.com) 구독 ID를 찾을 수 있습니다. SDK를 사용하여 작업 영역 개체에서 구독 ID를 얻을 수도 있습니다. `Workspace.from_config().subscription_id`)을 입력합니다.
    
다른 구독을 선택하려면 전환할 구독 ID와 함께 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 명령을 사용합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용을](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)참조하십시오.