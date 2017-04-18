---
title: "Azure Automation을 사용하여 Azure RemoteApp 관리 | Microsoft Docs"
description: "Azure 자동화 서비스를 사용하여 Azure RemoteApp을 관리하는 방법에 대해 알아봅니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 589f01ef-f9c1-4e7b-a040-1b46862d3544
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 59ac11f153c0bd74a1106400dbbf5790968b657c
ms.lasthandoff: 03/31/2017


---
# <a name="managing-azure-remoteapp-using-azure-automation"></a>Azure 자동화를 사용하여 Azure RemoteApp 관리
> [!IMPORTANT]
> Azure RemoteApp은 2017년 8월 31일에 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

이 가이드에서는 Azure 자동화 서비스 및 이 서비스를 사용하여 Azure RemoteApp 관리를 간소화하는 방법을 소개합니다.

## <a name="what-is-azure-automation"></a>Azure 자동화 정의
[Azure 자동화](../automation/automation-intro.md) 는 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure 자동화, 수동, 빈번하게 반복되는, 장기 실행 및 오류가 발생하기 쉬운 작업을 사용하면 조직의 안정성, 효율성 및 가치 창출 시간을 개선하도록 자동화할 수 있습니다.

Azure 자동화는 요구 사항에 맞게 크기가 조정되는 매우 안정적이며 가용성이 높은 워크플로 실행 엔진을 제공합니다. Azure 자동화에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure 자동화에서 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT 및 DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-remoteapp"></a>Azure 자동화를 통해 Azure RemoteApp 관리 향상
[Azure PowerShell 도구](https://msdn.microsoft.com/library/azure/jj156055.aspx)에서 사용할 수 있는 PowerShell cmdlet을 통해 Azure 자동화에서 RemoteApp을 관리할 수 있습니다. Azure 자동화에서는 이러한 RemoteApp PowerShell cmdlet을 기본적으로 사용할 수 있으므로 서비스 내에서 RemoteApp 관리 작업을 모두 수행할 수 있습니다. Azure 자동화에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
Azure 자동화의 기본 사항과 Azure 자동화를 사용하여 Azure RemoteApp을 관리하는 방법을 알아보았으므로 이제 다음 링크에 따라 Azure 자동화에 대해 자세히 알아보세요.

* Azure 자동화 [시작 자습서](../automation/automation-first-runbook-graphical.md)


