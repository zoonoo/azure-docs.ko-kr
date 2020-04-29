---
title: Azure Monitor에서 경고 인스턴스 관리
description: Azure 전체에서 경고 인스턴스 관리
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667621"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>통합 경고를 사용 하 여 경고 인스턴스 관리

Azure Monitor의 [통합 경고 환경을](https://aka.ms/azure-alerts-overview) 사용 하 여 Azure에서 다양 한 유형의 경고를 모두 볼 수 있습니다. 단일 창에서 여러 구독으로 확장 됩니다. 이 문서에서는 경고 인스턴스를 보는 방법 및 문제 해결을 위해 특정 경고 인스턴스를 찾는 방법을 보여 줍니다.

> [!NOTE]
> 최근 30 일 동안 생성 된 경고에만 액세스할 수 있습니다.

## <a name="go-to-the-alerts-page"></a>경고 페이지로 이동

다음 방법 중 하나를 통해 경고 페이지로 이동할 수 있습니다.

- [Azure Portal](https://portal.azure.com/)에서 **모니터** > **경고**를 선택 합니다.  

     ![모니터 경고 스크린샷](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 특정 리소스의 컨텍스트를 사용 합니다. 리소스를 열고 **모니터링** 섹션으로 이동한 다음 **경고**를 선택 합니다. 방문 페이지는 특정 리소스에 대 한 경고를 미리 필터링 합니다.

     ![리소스 모니터링 경고 스크린샷](media/alerts-managing-alert-instances/alert-resource.JPG)

- 특정 리소스 그룹의 컨텍스트를 사용 합니다. 리소스 그룹을 열고 **모니터링** 섹션으로 이동한 다음 **경고**를 선택 합니다. 방문 페이지는 해당 특정 리소스 그룹에 대 한 경고에 대해 미리 필터링 됩니다.    

     ![리소스 그룹 모니터링 경고 스크린샷](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>경고 인스턴스 찾기

**경고 요약** 페이지에서는 Azure에서 모든 경고 인스턴스에 대 한 개요를 제공 합니다. **여러 구독** (최대 5 개)을 선택 하거나 **리소스 그룹**, 특정 **리소스**또는 **시간 범위**에서 필터링 하 여 요약 보기를 수정할 수 있습니다. 경고에 대 한 목록 보기로 이동 하려면 **전체 경고**또는 심각도 밴드를 선택 합니다.     

![경고 요약 페이지의 스크린샷](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
**모든 경고** 페이지에 Azure에서 모든 경고 인스턴스가 나열 됩니다. 경고 알림에서 포털로 이동하는 경우 필터를 사용하여 해당 특정 경고 인스턴스로 범위를 좁힐 수 있습니다.

> [!NOTE]
> 심각도 밴드 중 하나를 선택 하 여 페이지를 가져온 경우 해당 심각도에 대해 목록이 미리 필터링 됩니다.

이전 페이지에서 사용할 수 있는 필터 외에도 monitor 서비스 (예: 메트릭에 대 한 플랫폼), 모니터링 조건 (발생 또는 해결 됨), 심각도, 경고 상태 (신규/승인 됨/닫힘) 또는 스마트 그룹 ID를 기준으로 필터링 할 수 있습니다.

![모든 경고 페이지의 스크린샷](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 심각도 밴드 중 하나를 선택 하 여 페이지를 가져온 경우 해당 심각도에 대해 목록이 미리 필터링 됩니다.

경고 인스턴스를 선택 하면 해당 특정 경고 인스턴스에 대 한 자세한 정보를 볼 수 있는 **경고 정보** 페이지가 열립니다.   

![경고 정보 페이지의 스크린샷](media/alerts-managing-alert-instances/alert-details.jpg)  

