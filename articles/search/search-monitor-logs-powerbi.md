---
title: Power BI를 사용하여 Azure 인지 검색 로그 및 메트릭 시각화
description: Power BI를 사용하여 Azure 인지 검색 로그 및 메트릭 시각화
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650162"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Power BI를 사용하여 Azure 인지 검색 로그 및 메트릭 시각화
[Azure Cognitive Search를](https://docs.microsoft.com/azure/search/search-what-is-azure-search) 사용하면 Azure Storage 계정에 검색 서비스에 대한 작업 로그 및 서비스 메트릭을 저장할 수 있습니다. 이 페이지에서는 Power BI 템플릿 앱을 통해 해당 정보를 시각화하는 방법에 대한 지침을 제공합니다. 앱은 검색, 인덱싱, 운영 및 서비스 메트릭에 대한 정보를 포함하여 검색 서비스에 대한 자세한 통찰력을 제공합니다.

전원 BI 템플릿 앱 Azure 인지 검색: [Power BI 앱 마켓플레이스에서](https://appsource.microsoft.com/marketplace/apps) **로그 및 메트릭 분석.**

## <a name="how-to-get-started-with-the-app"></a>앱을 시작하는 방법
1. 검색 서비스에 대한 진단 로깅 사용:
    1. 로그를 보관할 수 있는 기존 [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 생성 또는 식별
    1. Azure 포털에서 Azure 인지 검색 서비스로 이동
    1. 왼쪽 열의 모니터링 섹션에서 **진단 설정을** 선택합니다.

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. +진단 **설정 추가** 선택
    1. **저장소 계정에 보관을**확인하고, 저장소 계정 정보를 제공하고, **OperationLogs** 및 **AllMetrics를 확인합니다.**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. **저장** 선택

1. 로깅을 사용하도록 설정한 후 검색 서비스를 사용하여 로그 및 메트릭 생성을 시작합니다. 컨테이너가 이러한 로그를 사용하여 Blob 저장소에 나타나기까지 최대 1시간이 걸립니다. 검색 트래픽 **로그에 대한 인사이트 로그 작업 로그** 컨테이너와 메트릭에 대한 **인사이트 메트릭-pt1m** 컨테이너가 표시됩니다.

1. [Power BI 앱 마켓플레이스에서](https://appsource.microsoft.com/marketplace/apps) Azure 인지 검색 전원 BI 앱을 찾아 새 작업 영역 또는 기존 작업 영역에 설치합니다. 응용 프로그램은 **Azure 인지 검색이라고합니다 : 로그 및 메트릭을 분석합니다.**

1. 앱을 설치한 후 Power BI의 앱 목록에서 앱을 선택합니다.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. 데이터 연결하려면 **연결** 선택

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. 로그 및 메트릭이 포함된 저장소 계정의 이름을 입력합니다. 기본적으로 앱은 지난 10일간의 데이터를 볼 수 있지만 이 값은 **Days** 매개 변수로 변경할 수 있습니다.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. **키를** 인증 방법으로 선택하고 저장소 계정 키를 제공합니다. **개인 정보를** 개인 정보 수준으로 선택합니다. 로그인을 클릭하여 로드 프로세스를 시작합니다.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. 데이터가 새로 고쳐지때까지 기다립니다. 이 경우 데이터의 양에 따라 다소 시간이 걸릴 수 있습니다. 아래 표시기를 기반으로 데이터가 계속 새로 고쳐지고 있는지 확인할 수 있습니다.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. 데이터 새로 고침이 완료되면 **Azure 인지 검색 보고서를** 선택하여 보고서를 봅니다.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. 보고서를 연 후 데이터를 사용하여 페이지를 열도록 페이지를 새로 고쳐야 합니다.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>앱 매개 변수를 변경하는 방법
다른 저장소 계정에서 데이터를 시각화하거나 쿼리할 데이터 일 수를 변경하려면 아래 단계에 따라 **Days** 및 **StorageAccount** 매개 변수를 변경합니다.

1. Power BI 앱으로 이동하여 Azure 인지 검색 앱을 찾고 **앱 편집** 단추를 선택하여 작업 영역을 봅니다.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. 데이터 집합 옵션에서 **설정을** 선택합니다.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. 데이터 집합 탭에서 매개 변수 값을 변경하고 **적용을**선택합니다. 연결에 문제가 있는 경우 동일한 페이지에서 데이터 원본 자격 증명을 업데이트합니다.

1. 작업 영역으로 다시 이동하여 데이터 집합 옵션에서 **지금 새로 고침을** 선택합니다.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. 보고서를 열어 업데이트된 데이터를 봅니다. 최신 데이터를 보려면 보고서를 새로 고쳐야 할 수도 있습니다.

## <a name="troubleshooting"></a>문제 해결
데이터가 표시되지 않는 경우 다음 문제 해결 단계를 따릅니다.

1. 보고서를 열고 페이지를 새로 고쳐 최신 데이터를 보고 있는지 확인합니다. 보고서에 데이터를 새로 고치는 옵션이 있습니다. 최신 데이터를 얻으려면 이 옵션을 선택합니다.

1. 제공한 저장소 계정 이름과 액세스 키가 올바른지 확인합니다. 저장소 계정 이름은 검색 서비스 로그로 구성된 계정과 일치해야 합니다.

1. 저장소 계정에 컨테이너 **인사이트-로그-작업 로그** 및 **인사이트 메트릭-pt1m이** 포함되어 있고 각 컨테이너에 데이터가 포함되어 있는지 확인합니다. 로그 및 메트릭은 폴더의 몇 계층 내에 있습니다.

1. 데이터 집합이 여전히 새로 고쳐있는지 확인합니다. 새로 고침 상태 표시기는 위의 8단계에 표시됩니다. 여전히 새로 고침이 완료되면 새로 고침이 완료될 때까지 기다렸다가 보고서를 열고 새로 고칩니다.

## <a name="next-steps"></a>다음 단계
[Azure 인지 검색에 대해 자세히 알아보기](https://docs.microsoft.com/azure/search/)

[Power BI란 무엇입니까?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Power BI 서비스의 디자이너를 위한 기본 개념](https://docs.microsoft.com/power-bi/service-basic-concepts)