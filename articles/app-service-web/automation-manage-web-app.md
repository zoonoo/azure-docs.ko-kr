---
title: "Azure Automation을 사용하여 Azure 웹앱 관리 | Microsoft Docs"
description: "Azure 자동화 서비스를 사용하여 Azure 웹앱을 관리하는 방법에 대해 알아봅니다."
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 4edd2696c9a5709ded6e2a3e352090775335f0d2
ms.openlocfilehash: 4fcfa2e7ec2e8257407026ed4cca0e15fd0b5bb6
ms.contentlocale: ko-kr
ms.lasthandoff: 12/07/2016

---
# <a name="managing-azure-web-app-using-azure-automation"></a>Azure 자동화를 사용하여 Azure 웹앱 관리
이 가이드에서는 Azure 자동화 서비스 및 이 서비스를 사용하여 Azure SQL 웹앱 관리를 간소화하는 방법을 소개합니다.

## <a name="what-is-azure-automation"></a>Azure 자동화 정의
[Azure 자동화](../automation/automation-intro.md) 는 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure 자동화, 수동, 반복되는, 장기 실행 및 오류가 발생하기 쉬운 작업을 사용하면 조직의 안정성, 효율성 및 가치 창출 시간을 개선하도록 자동화할 수 있습니다.

Azure 자동화는 요구 사항에 맞게 크기가 조정되는 매우 안정적이며 가용성이 높은 워크플로 실행 엔진을 제공합니다. Azure 자동화에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure 자동화에서 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT 및 DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>어떻게 Azure 자동화가 Azure 웹앱을 관리할 수 있나요?
[Azure PowerShell 모듈](/powershell/azureps-cmdlets-docs)에서 사용할 수 있는 PowerShell cmdlet을 사용하여 Azure 자동화에서 웹앱을 관리할 수 있습니다. [Azure 자동화에서는 이러한 웹앱 PowerShell cmdlet을 설치](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/)할 수 있으므로 서비스 내에서 웹앱 관리 작업을 모두 수행할 수 있습니다. Azure 자동화에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

자동화를 사용하여 앱 서비스를 관리하는 몇 가지 예는 다음과 같습니다.

* [웹앱을 관리하기 위한 스크립트](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>다음 단계
Azure 자동화의 기본 사항과 Azure 자동화를 사용하여 Azure 웹앱을 관리하는 방법을 알아보았으므로 이제 다음 링크에 따라 Azure 자동화에 대해 자세히 알아보세요.

* Azure 자동화 [시작 자습서](../automation/automation-first-runbook-graphical.md)


