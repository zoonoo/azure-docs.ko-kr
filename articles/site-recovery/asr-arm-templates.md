---
title: Azure 리소스 관리자 템플릿
description: Azure Site Recovery 기능을 사용 하기 위한 템플릿을 Azure Resource Manager 합니다.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720531"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Site Recovery에 대 한 Azure Resource Manager 템플릿

다음 표에는 Azure Site Recovery 기능을 사용 하기 위한 Azure Resource Manager 템플릿에 대 한 링크가 포함 되어 있습니다.

| 템플릿 | 설명 |
|---|---|
|**Azure 간** | |
| [Recovery Services 자격 증명 모음 만들기](./quickstart-create-vault-template.md)| Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 Azure Backup 및 Azure Site Recovery에 사용할 수 있습니다. |
| [Azure Vm에 대 한 복제 사용](https://aka.ms/asr-arm-enable-replication) | 기존 자격 증명 모음 및 사용자 지정 대상 설정을 사용 하 여 Azure Vm에 대해 복제를 사용 하도록 설정 합니다.|
| [장애 조치 (Failover) 및 다시 보호 트리거](https://aka.ms/asr-arm-failover-reprotect) | Azure Vm 집합에 대해 장애 조치 (Failover) 및 다시 보호 작업을 트리거합니다. |
| [Azure Vm에 대 한 종단 간 DR 흐름 실행](https://aka.ms/asr-arm-e2e-flow) | Azure Vm에 대 한 전체 최종 재해 복구 흐름을 시작 합니다 (복제 540를 사용 하도록 설정 하 고 장애 조치 (Failover) 및 다시 보호 + 장애 복구 및 다시 보호 사용).|
|   |   |