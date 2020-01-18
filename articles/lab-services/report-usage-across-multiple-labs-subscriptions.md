---
title: 여러 랩 및 구독에서 Azure DevTest Labs 사용
description: 여러 랩 및 구독에서 Azure DevTest Labs 사용량을 보고 하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169175"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>여러 랩 및 구독에서 Azure DevTest Labs 사용 보고

대부분의 큰 조직은 사용량에서 추세 및 이상 값을 시각화 하 여 리소스 사용을 보다 효과적으로 추적 하려고 합니다. 리소스 사용량에 따라 랩 소유자 또는 관리자는 [리소스 사용량 및 비용을 개선](https://docs.microsoft.com/azure/billing/billing-getting-started)하도록 랩을 사용자 지정할 수 있습니다. Azure DevTest Labs에서 랩 당 리소스 사용량을 다운로드 하 여 사용 패턴에 대 한 세부적인 기록을 볼 수 있습니다. 이러한 사용 패턴은 효율성 향상을 위해 변경 사항을 정확히 파악 하는 데 도움이 됩니다. 대부분의 기업은 [여러 랩 및 구독](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)에서 개별 랩 사용량과 전체 사용량을 모두 원합니다. 

이 문서에서는 여러 랩 및 구독에서 리소스 사용량 정보를 처리 하는 방법을 설명 합니다.

![보고서 사용량](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>개별 랩 사용

이 섹션에서는 단일 랩에 대 한 리소스 사용량을 내보내는 방법을 설명 합니다.

DevTest Labs의 리소스 사용을 내보내려면 먼저 사용 데이터를 포함 하는 다른 파일을 저장할 수 있도록 Azure Storage 계정을 설정 해야 합니다. 데이터 내보내기를 실행 하는 두 가지 일반적인 방법은 다음과 같습니다.

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az. Resource 모듈은 `exportResourceUsage`의작업으로 [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) 작업, 랩 리소스 ID 및 필요한 매개 변수를 사용 하 여 AzResourceAction를 호출 합니다. 

    [개인 데이터 내보내기 또는 삭제](personal-data-delete-export.md) 문서에는 내보낸 데이터에 대 한 자세한 정보가 포함 된 샘플 PowerShell 스크립트가 포함 되어 있습니다. 

    > [!NOTE]
    > Date 매개 변수는 타임 스탬프를 포함 하지 않으므로 랩이 위치한 표준 시간대를 기준으로 하는 자정의 모든 항목을 데이터에 포함 합니다.

내보내기가 완료 되 면 다양 한 리소스 정보를 사용 하 여 blob 저장소에 여러 CSV 파일이 만들어집니다.
  
현재 다음과 같은 두 개의 CSV 파일이 있습니다.

* *virtualmachines* -랩의 가상 컴퓨터에 대 한 정보를 포함 합니다.
* *디스크 .csv* -랩에서 다른 디스크에 대 한 정보를 포함 합니다. 

이러한 파일은 랩 이름, 랩 고유 ID, 실행 날짜, 전체 또는 시작 날짜 (내보내기 요청에 기반) 아래에 있는 랩 *resourceusage* blob 컨테이너에 저장 됩니다. 예제 blob 구조는 다음과 같습니다.

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>모든 랩에 대 한 사용 내보내기

여러 랩에 대 한 사용 정보를 내보내려면 다음을 사용 하십시오. 

* PowerShell을 비롯 한 여러 언어에서 사용할 수 있는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)또는 
* [Runbook을 Azure Automation](https://docs.microsoft.com/azure/automation/)PowerShell, Python 또는 사용자 지정 그래픽 디자이너를 사용 하 여 내보내기 코드를 작성 합니다.

이러한 기술을 사용 하 여 특정 날짜 및 시간에 모든 랩에서 개별 랩 내보내기를 실행할 수 있습니다. 

Azure 함수는 데이터를 장기적인 저장소로 푸시 해야 합니다. 여러 랩에 대 한 데이터를 내보내는 경우 내보내기에 시간이 걸릴 수 있습니다. 성능을 지원 하 고 정보의 중복 가능성을 줄이려면 각 랩을 병렬로 실행 하는 것이 좋습니다. 병렬 처리를 수행 하려면 Azure Functions를 비동기적으로 실행 합니다. 또한 Azure Functions 제공 하는 타이머 트리거를 활용 합니다.

## <a name="using-a-long-term-storage"></a>장기 저장소 사용

장기 저장소는 여러 랩에서 내보내기 정보를 단일 데이터 원본으로 통합 합니다. 장기 저장소를 사용 하는 또 다른 혜택은 저장소 계정에서 파일을 제거 하 여 중복 및 비용을 줄일 수 있다는 것입니다. 

장기 저장소를 사용 하 여 텍스트 조작을 수행할 수 있습니다. 예를 들면 다음과 같습니다. 

* 이름 추가
* 복합 그룹 만들기
* 데이터 집계.

몇 가지 일반적인 저장소 솔루션은 [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)및 [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)입니다. 선택한 장기 저장소 솔루션 선택은 기본 설정에 따라 달라 집니다. 데이터를 시각화할 때 상호 작용 가용성 측면에서 제공 되는 기능에 따라 도구를 선택 하는 것을 고려할 수 있습니다.

## <a name="visualizing-data-and-gathering-insights"></a>데이터 시각화 및 정보 수집

원하는 데이터 시각화 도구를 사용 하 여 사용 현황 데이터를 표시 하 고 정보를 수집 하 여 사용 효율성을 확인 합니다. 예를 들어 [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) 사용 하 여 사용 현황 데이터를 구성 하 고 표시할 수 있습니다. 

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 를 사용 하 여 단일 위치 인터페이스 내에서 리소스를 만들고, 연결 하 고, 관리할 수 있습니다. 더 많은 제어가 필요한 경우에는 개별 리소스를 단일 리소스 그룹 내에 만들고 Data Factory 서비스와 독립적으로 관리할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

시스템이 설정 되 고 데이터가 장기 저장소로 이동 하는 경우 다음 단계는 데이터에 응답 해야 하는 질문에 대 한 것입니다. 예: 

-   VM 크기 사용은 무엇 인가요?

    사용자가 고성능 (더 저렴 한) VM 크기를 선택 하나요?
-   어떤 마켓플레이스 이미지를 사용 하 고 있나요?

    사용자 지정 이미지는 가장 일반적인 VM 기반으로, 공용 이미지 저장소를 [공유 이미지 갤러리](../virtual-machines/windows/shared-image-galleries.md) 또는 [이미지 팩터리](image-factory-create.md)와 같은 방식으로 빌드해야 합니다.
-   사용 중인 사용자 지정 이미지는 무엇 인가요?
