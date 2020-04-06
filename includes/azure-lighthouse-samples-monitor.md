---
title: 포함 파일
description: 포함 파일
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421156"
---
이러한 샘플은 Azure Monitor를 사용하여 Azure 위임 리소스 관리를 위해 온보드된 구독에 대한 경고를 만드는 방법을 보여 줍니다.

| **템플릿** | **설명** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | 관리 테넌트에서 지난 1일 동안의 활동을 쿼리하고 [추가되거나 제거된 위임에 대해 보고](../articles/lighthouse/how-to/monitor-delegation-changes.md)(또는 성공하지 못한 시도)합니다.|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | 이 템플릿은 Azure 경고를 만들고 기존 작업 그룹에 연결합니다.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Kusto 쿼리를 기반으로 여러 로그 경고를 만듭니다.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | 사용자가 관리 테넌트에 구독을 위임하면 테넌트에 경고를 배포합니다.|
