---
title: Azure 자동화를 사용하여 Azure 서비스 버스 관리 | Microsoft Docs
description: Azure 자동화 서비스를 사용하여 Azure 서비스 버스를 관리하는 방법에 대해 알아봅니다.
services: service-bus, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand

---
# Azure 자동화를 사용하여 Azure 서비스 버스 관리
이 가이드에서는 Azure 자동화 서비스 및 이 서비스를 사용하여 Azure 서비스 버스 관리를 간소화하는 방법을 소개합니다.

## Azure 자동화 정의
[Azure 자동화](../automation/automation-intro.md)는 프로세스 자동화 및 원하는 상태 구성을 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure 자동화, 수동, 반복되는, 장기 실행 및 오류가 발생하기 쉬운 작업을 사용하면 조직의 안정성, 효율성 및 가치 창출 시간을 개선하도록 자동화할 수 있습니다.

Azure 자동화는 요구 사항에 맞게 크기가 조정되는 매우 안정적이며 가용성이 높은 워크플로 실행 엔진을 제공합니다. Azure 자동화에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure 자동화에서 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT 및 DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## Azure 자동화를 통해 Azure 서비스 버스 관리를 간소화하는 방법
[서비스 버스 REST API](https://msdn.microsoft.com/library/azure/mt639375.aspx)를 사용하여 Azure 자동화에서 서비스 버스를 관리할 수 있습니다. Azure 자동화 내에서 REST API를 사용하여 많은 서비스 버스 작업을 수행하는 PowerShell 스크립트를 실행할 수 있습니다. Azure 자동화에서 이러한 REST API 호출을 다른 Azure 서비스의cmdlet과 쌍을 이루어 Azure 서비스와 타사 시스템 간에 복잡한 작업을 자동화할 수도 있습니다.

PowerShell을 사용하여 Azure 서비스 버스를 관리하는 몇 가지 예는 다음과 같습니다.

* [Azure 서비스 버스 큐를 관리하기 위한 사용자 지정 PowerShell cmdlet](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [PowerShell 스크립트를 사용하여 서비스 버스 큐, 토픽 및 구독을 만드는 방법](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [PowerShell을 사용하여 Azure 서비스 버스 네임스페이스 만들기](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

자동화 Runbook에서 Azure 서비스 버스를 사용하기 위한 PowerShell 모듈은 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0)에서 다운로드할 수 있습니다.

## 다음 단계
Azure 자동화의 기본 사항과 Azure 자동화를 사용하여 Azure 서비스 버스를 관리하는 방법을 알아보았으므로 이제 다음 링크에 따라 Azure 자동화에 대해 자세히 알아보세요.

* Azure 자동화 [시작 자습서](../automation/automation-first-runbook-graphical.md) 참조
* [PowerShell을 사용하여 서비스 버스를 관리](../service-bus-messaging/service-bus-powershell-how-to-provision.md)하는 방법을 참조하세요.

<!---HONumber=AcomDC_0803_2016-->