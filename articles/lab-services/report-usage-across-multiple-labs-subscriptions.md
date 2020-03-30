---
title: 여러 랩 및 구독에서 Azure DevTest 랩 사용
description: 여러 랩 및 구독에서 Azure DevTest 랩 사용량을 보고하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169175"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>여러 랩 및 구독에서 Azure DevTest 랩 사용 보고

대부분의 대규모 조직에서는 사용 의 추세와 이상값을 시각화하여 리소스 사용량을 보다 효과적으로 추적하려고 합니다. 리소스 사용량에 따라 랩 소유자 또는 관리자는 랩을 사용자 지정하여 [리소스 사용 및 비용을 개선할](https://docs.microsoft.com/azure/billing/billing-getting-started)수 있습니다. Azure DevTest Labs에서는 랩당 리소스 사용량을 다운로드하여 사용 패턴을 자세히 살펴볼 수 있습니다. 이러한 사용 패턴은 효율성을 개선하기 위해 변경 사항을 정확히 파악하는 데 도움이 될 수 있습니다. 대부분의 기업은 여러 랩 및 [구독에서](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)개별 랩 사용과 전체 사용을 모두 원합니다. 

이 문서에서는 여러 랩 및 구독에서 리소스 사용 정보를 처리하는 방법에 대해 설명합니다.

![보고서 사용량](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>개별 랩 사용

이 섹션에서는 단일 랩에 대한 리소스 사용량을 내보내는 방법에 대해 설명합니다.

DevTest Labs의 리소스 사용량을 내보내기 전에 Azure Storage 계정을 설정하여 사용 데이터가 포함된 다른 파일을 저장할 수 있도록 해야 합니다. 데이터 내보내기를 실행하는 방법에는 두 가지가 있습니다.

* [데브테스트 랩 레스트 API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az.Resource 모듈은 , 랩 리소스 ID `exportResourceUsage`및 필요한 매개 변수의 작업을 통해 [호출-AzResourceAction을](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) 호출합니다. 

    [내보내기 또는 삭제 개인 데이터](personal-data-delete-export.md) 문서에는 내보낸 데이터에 대한 자세한 정보가 포함된 샘플 PowerShell 스크립트가 포함되어 있습니다. 

    > [!NOTE]
    > 날짜 매개 변수에는 타임스탬프가 포함되지 않으므로 랩이 있는 표준 시간대를 기준으로 자정부터 모든 데이터가 데이터에 포함됩니다.

내보내기가 완료되면 다른 리소스 정보가 포함된 Blob 저장소에 여러 CSV 파일이 있습니다.
  
현재 두 개의 CSV 파일이 있습니다.

* *virtualmachine.csv* - 랩의 가상 컴퓨터에 대한 정보가 포함되어 있습니다.
* *disks.csv* - 랩의 여러 디스크에 대한 정보가 포함되어 있습니다. 

이러한 파일은 *랩 리소스사용* Blob 컨테이너에 랩 이름, Lab 고유 ID, 실행된 날짜 및 내보내기 요청을 기반으로 한 전체 또는 시작 날짜 아래에 저장됩니다. 예를 들어 Blob 구조는 다음과 같은 것입니다.

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>모든 랩에 대한 사용 내보내기

여러 랩의 사용 정보를 내보내려면 

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)- PowerShell 또는 
* [Azure 자동화 Runbook은](https://docs.microsoft.com/azure/automation/)PowerShell, 파이썬 또는 사용자 지정 그래픽 디자이너를 사용하여 내보내기 코드를 작성합니다.

이러한 기술을 사용하여 특정 날짜 및 시간에 모든 랩에서 개별 랩 내보내기를 실행할 수 있습니다. 

Azure 함수는 데이터를 장기 저장소로 푸시해야 합니다. 여러 랩에 대한 데이터를 내보낼 때 내보내기에 다소 시간이 걸릴 수 있습니다. 성능을 돕고 정보 중복 가능성을 줄이려면 각 랩을 병렬로 실행하는 것이 좋습니다. 병렬 처리를 수행하려면 Azure 함수를 비동기적으로 실행합니다. 또한 Azure Functions가 제공하는 타이머 트리거를 활용합니다.

## <a name="using-a-long-term-storage"></a>장기 저장소 사용

장기 저장소는 다른 랩의 내보내기 정보를 단일 데이터 원본으로 통합합니다. 장기 저장소를 사용하는 또 다른 이점은 중복 및 비용을 줄이기 위해 저장소 계정에서 파일을 제거할 수 있다는 것입니다. 

장기 저장소는 다음과 같은 텍스트 조작을 수행하는 데 사용할 수 있습니다. 

* 친숙한 이름 추가
* 복잡한 그룹화 만들기
* 데이터를 집계합니다.

몇 가지 일반적인 저장소 솔루션은 [SQL Server,](https://azure.microsoft.com/services/sql-database/) [Azure 데이터 레이크](https://azure.microsoft.com/services/storage/data-lake-storage/)및 [Cosmos DB입니다.](https://azure.microsoft.com/services/cosmos-db/) 어떤 장기 스토리지 솔루션을 선택하는지 기본 설정에 따라 다릅니다. 데이터를 시각화할 때 상호 작용 가용성 측면에서 제공하는 도구에 따라 도구를 선택하는 것이 좋습니다.

## <a name="visualizing-data-and-gathering-insights"></a>데이터 시각화 및 인사이트 수집

선택한 데이터 시각화 도구를 사용하여 장기 저장소에 연결하여 사용 데이터를 표시하고 통찰력을 수집하여 사용 효율성을 확인합니다. 예를 들어 [Power BI를](https://docs.microsoft.com/power-bi/power-bi-overview) 사용하여 사용 데이터를 구성하고 표시할 수 있습니다. 

[Azure Data Factory를](https://azure.microsoft.com/services/data-factory/) 사용하여 단일 위치 인터페이스 내에서 리소스를 생성, 연결 및 관리할 수 있습니다. 더 큰 제어가 필요한 경우 개별 리소스를 단일 리소스 그룹 내에서 만들고 Data Factory 서비스와 독립적으로 관리할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

시스템이 설정되고 데이터가 장기 저장소로 이동되면 다음 단계는 데이터가 대답해야 하는 질문을 마련하는 것입니다. 예를 들어: 

-   VM 크기 사용량은 무엇입니까?

    사용자가 고성능(더 비싼) VM 크기를 선택하고 있습니까?
-   어떤 마켓플레이스 이미지가 사용되나요?

    공유 이미지 [갤러리](../virtual-machines/windows/shared-image-galleries.md) 또는 [이미지 팩터리와](image-factory-create.md)같이 공통 이미지 저장소를 빌드해야 하는 사용자 지정 이미지가 가장 일반적인 VM 베이스입니까?
-   어떤 사용자 지정 이미지가 사용중이거나 사용되지 않습니까?
