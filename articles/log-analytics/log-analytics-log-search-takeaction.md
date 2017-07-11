---
title: "Log Analytics에서 사용자가 시작한 Azure Automation Runbook 작업 | Microsoft Docs"
description: "이 문서는 요청 시 Log Analytics 검색 결과에서 Automation Runbook을 실행하는 방법을 설명합니다."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ff938697add98f3d21b4971175432335ee2e39ba
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017

---

<a id="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result" class="xliff"></a>

# Log Analytics 로그 검색 결과에서 Automation Runbook으로 작업 수행

Azure Log Analytics의 로그 검색 결과에서 이제 Automation Runbook을 실행하도록 **작업 수행**을 선택할 수 있습니다.  Runbook은 문제를 해결하거나 문제 해결 정보 수집, 전자 메일 전송 또는 서비스 요청 만들기와 같은 다른 작업을 수행하는 데 사용될 수 있습니다. 

<a id="components-and-features-used" class="xliff"></a>

## 사용된 구성 요소 및 기능
* [Azure Automation 계정](../automation/automation-offering-get-started.md)
* [Log Analytics 작업 영역](../log-analytics/log-analytics-overview.md)

<a id="to-initiate-runbook-from-log-search" class="xliff"></a>

## 로그 검색에서 Runbook을 시작하려면

이벤트에서 작업을 수행하고 로그 검색 결과에서 Runbook을 시작하려면 로그 검색을 생성하여 시작하고 결과에서 요청 시 Runbook을 호출할 수 있습니다.  Azure 또는 [OMS 포털](../log-analytics/log-analytics-log-searches.md)의 로그 검색 기능에서 수행할 수도 있습니다.  이 예제에서는 이 기능의 기본적인 데모를 사용하여 Azure Portal에서 로그 검색을 수행합니다.

1. Azure Portal의 허브 메뉴에서 **더 많은 서비스**를 클릭하고 **Log Analytics**를 선택합니다.  
2. Log Analytics 블레이드에서 Log Analytics 작업 영역을 선택하고 작업 영역 블레이드에서 **로그 검색**을 선택합니다.  
3. 로그 검색 블레이드에서 로그 검색을 수행합니다.  
4. 로그 검색 결과에서 필드 중 하나의 왼쪽에 있는 줄임표를 클릭하고 팝업에서 **작업 수행**을 선택합니다.<br><br> ![검색 결과에서 작업 수행 선택](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
5. 작업 수행 블레이드에서 **Runbook 실행**을 선택하고 **Runbook 실행** 블레이드에서 Runbook 실행을 선택할 수 있습니다.  Log Analytics 작업 영역에 연결된 Automation 계정에서 Runbook을 선택할 수 있습니다.  다음 사항에 유의하세요.

    * Runbook은 태그로 구성됩니다.
    * Runbook 입력 매개 변수 값은 검색 결과의 필드에서 직접 선택할 수 있습니다.  드롭다운 목록은 선택하는 결과에서 사용할 수 있는 모든 필드를 표시합니다.  
    * 멤버로 이 컴퓨터만을 포함하는 해당 Hybrid Runbook Worker 그룹이 있는 경우 문제가 있는 컴퓨터에 설치한 [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md)에서 Runbook을 실행하도록 선택할 수 있습니다.  Hybrid Worker 그룹의 이름이 로그 검색 결과에 있는 컴퓨터의 이름과 일치하는 경우 그룹이 자동으로 선택됩니다.    

6. **실행**을 클릭한 후 작업의 상태를 검토할 수 있도록 Runbook 작업 블레이드가 열립니다.   

[Log Analytics 경고에서 호출](../automation/automation-invoke-runbook-from-omsla-alert.md)되도록 구성된 Runbook을 선택하는 경우 **개체** 유형인 **WebhookData**라는 입력 매개 변수를 갖습니다.  입력 매개 변수가 필수인 경우 JSON 형식 문자열을 Runbook 작업에서 참조할 특정 항목을 필터링할 수 있도록 하는 개체 형식으로 변환할 수 있도록 검색 결과를 Runbook에 전달해야 합니다.  드롭다운 목록에서 **검색 결과(개체)**를 선택하여 수행합니다.<br><br> ![Runbook 매개 변수에 대한 웹후크 데이터 개체 선택](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
<a id="next-steps" class="xliff"></a>

## 다음 단계

* Log Analytics에 제공되는 모든 검색 필드 및 패싯을 보려면 [Log Analytics log search reference](log-analytics-search-reference.md) (Log Analytics 로그 검색 참조)를 검토합니다.
* Automation Runbook을 자동으로 호출하는 방법을 알아보려면 [OMS Log Analytics 경고에서 Azure Automation Runbook 호출](../automation/automation-invoke-runbook-from-omsla-alert.md)을 참조하세요.  
