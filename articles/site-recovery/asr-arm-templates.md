---
title: Azure 리소스 관리자 템플릿
description: Azure Site Recovery 기능을 사용하기 위한 Azure Resource Manager 템플릿입니다.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720531"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Site Recovery용 Azure Resource Manager 템플릿

다음 표에는 Azure Site Recovery 기능 활용을 위한 Azure Resource Manager 템플릿 링크가 포함되어 있습니다.

| 템플릿 | Description |
|---|---|
|**Azure 간** | |
| [Recovery Services 자격 증명 모음 만들기](./quickstart-create-vault-template.md)| Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 Azure Backup 및 Azure Site Recovery에 사용할 수 있습니다. |
| [Azure VM에 복제 사용](https://aka.ms/asr-arm-enable-replication) | 기존 자격 증명 모음 및 사용자 지정 대상을 설정하여 Azure VM에 복제를 사용하도록 설정합니다.|
| [장애 조치(failover) 및 다시 보호 트리거](https://aka.ms/asr-arm-failover-reprotect) | Azure VM 집합에 장애 조치(failover) 및 다시 보호 작업을 트리거합니다. |
| [Azure VM 종단 간 DR 흐름 실행](https://aka.ms/asr-arm-e2e-flow) | Azure VM 대상의 전체 최종 재해 복구 흐름을 시작합니다(복제 + 장애 조치(failover) 및 재보호 + 장애 복구(failback) 사용). 다른 이름으로는 540° 흐름이라고도 합니다.|
|   |   |