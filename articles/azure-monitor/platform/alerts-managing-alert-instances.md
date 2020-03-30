---
title: Azure 모니터에서 경고 인스턴스 관리
description: Azure 전체에서 경고 인스턴스 관리
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667621"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>통합 경고로 경고 인스턴스 관리

Azure Monitor의 [통합 경고 환경을](https://aka.ms/azure-alerts-overview) 사용하면 Azure 전체에서 모든 다른 유형의 경고를 볼 수 있습니다. 단일 창에서 여러 구독에 걸쳐 있습니다. 이 문서에서는 경고 인스턴스를 보는 방법과 문제 해결을 위한 특정 경고 인스턴스를 찾는 방법을 보여 줍니다.

> [!NOTE]
> 지난 30일 동안생성된 경고에만 액세스할 수 있습니다.

## <a name="go-to-the-alerts-page"></a>경고 페이지로 이동

다음 방법으로 경고 페이지로 이동할 수 있습니다.

- Azure [포털에서](https://portal.azure.com/)경고 **모니터링을** > **선택합니다.**  

     ![모니터 경고의 스크린샷](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 특정 리소스의 컨텍스트를 사용합니다. 리소스를 열고 **모니터링** 섹션으로 이동한 다음 **경고를 선택합니다.** 방문 페이지는 특정 리소스에 대한 경고를 위해 미리 필터링됩니다.

     ![리소스 모니터링 경고 의 스크린샷](media/alerts-managing-alert-instances/alert-resource.JPG)

- 특정 리소스 그룹의 컨텍스트를 사용합니다. 리소스 그룹을 열고 **모니터링** 섹션으로 이동한 다음 **경고를 선택합니다.** 방문 페이지는 특정 리소스 그룹에 대한 경고를 위해 미리 필터링됩니다.    

     ![리소스 그룹 모니터링 경고의 스크린샷](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>경고 인스턴스 찾기

**경고 요약** 페이지에서는 Azure 전체의 모든 경고 인스턴스에 대한 개요를 제공합니다. **여러 구독(최대** 5개까지)을 선택하거나 **리소스 그룹,** 특정 **리소스**또는 시간 범위를 필터링하여 요약 보기를 수정할 수 **있습니다.** **총 경고**또는 심각도 대역을 선택하여 경고의 목록 보기로 이동합니다.     

![경고 요약 페이지의 스크린샷](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
모든 **경고** 페이지에서 Azure의 모든 경고 인스턴스가 나열됩니다. 경고 알림에서 포털로 이동하는 경우 필터를 사용하여 해당 특정 경고 인스턴스로 범위를 좁힐 수 있습니다.

> [!NOTE]
> 심각도 밴드를 선택하여 페이지에 온 경우 해당 심각도에 대해 목록이 미리 필터링됩니다.

이전 페이지에서 사용할 수 있는 필터 외에도 모니터 서비스(예: 메트릭용 플랫폼), 모니터 상태(발생 또는 해결), 심각도, 경고 상태(새/승인/종료) 또는 스마트 그룹 ID를 기준으로 필터링할 수도 있습니다.

![모든 경고 페이지의 스크린샷](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 심각도 밴드를 선택하여 페이지에 온 경우 해당 심각도에 대해 목록이 미리 필터링됩니다.

경고 인스턴스를 선택하면 **경고 세부 정보** 페이지가 열리므로 특정 경고 인스턴스에 대한 자세한 내용을 볼 수 있습니다.   

![경고 세부 정보 페이지의 스크린샷](media/alerts-managing-alert-instances/alert-details.jpg)  

