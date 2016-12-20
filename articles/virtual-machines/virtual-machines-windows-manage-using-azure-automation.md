---
title: "Azure Automation을 사용하여 VM 관리 | Microsoft Docs"
description: "Azure 자동화 서비스를 사용하여 대규모 Azure 가상 컴퓨터를 관리하는 방법을 알아봅니다."
services: virtual-machines-windows, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: ce49f83a-f409-42ee-af74-a8ea2caa9ae8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d9c161b58419bb85c7ec473d853db4cbc40dd1


---
# <a name="managing-azure-virtual-machines-using-azure-automation"></a>Azure 자동화를 사용하여 Azure 가상 컴퓨터 관리
이 가이드에서는 Azure 자동화 서비스 및 이 서비스를 사용하여 Azure 가상 컴퓨터 관리를 간소화하는 방법을 소개합니다.

## <a name="what-is-azure-automation"></a>Azure 자동화 정의
[Azure 자동화](https://azure.microsoft.com/services/automation/) 는 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure 자동화를 통해 장기 실행 작업, 수동 작업, 오류가 발생하기 쉬운 작업 및 빈번하게 반복되는 작업을 자동화하여 조직의 안정성, 효율성 및 가치 창출 시간을 향상시킬 수 있습니다.

Azure 자동화는 조직이 성장함에 따라 요구를 충족하기 위해 크기가 조정되는 안정성과 가용성 높은 워크플로 실행 엔진을 제공합니다. Azure 자동화에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure 자동화에서 자동으로 실행되도록 클라우드 관리 작업을 실행하여 작업 오버헤드를 줄이고 IT 및 DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-virtual-machines"></a>Azure 자동화를 통해 Azure 가상 컴퓨터 관리 향상
[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)을 사용하여 Azure 자동화에서 가상 컴퓨터를 관리 할 수 있습니다. Azure 자동화에서는 이러한 PowerShell cmdlet을 포함하고 있으므로 서비스 내에서 모든 가 컴퓨터 관리 작업을 수행할 수 있습니다. Azure 자동화에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
Azure 자동화의 기본 사항과 Azure 자동화를 사용하여 Azure 가상 컴퓨터를 관리하는 방법을 알아보았습니다. 추가 정보는

* [Azure 자동화 개요](../automation/automation-intro.md)
* [내 첫 번째 runbook](../automation/automation-first-runbook-graphical.md)
* [Azure 자동화 학습 맵](https://azure.microsoft.com/documentation/learning-paths/automation/)




<!--HONumber=Nov16_HO3-->


