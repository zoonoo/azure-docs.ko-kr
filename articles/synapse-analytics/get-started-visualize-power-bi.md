---
title: '자습서: Azure Synapse Analytics 시작 - Power BI를 사용하여 작업 영역 데이터 시각화'
description: 이 자습서에서는 Power BI 작업 영역을 만들고, Azure Synapse 작업 영역을 연결하고, Azure Synapse 작업 영역에서 데이터를 활용하는 Power BI 데이터 세트를 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: c5c41b98d52a4b8182fb162c3bfa9a08c0df5e15
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171856"
---
# <a name="visualize-data-with-power-bi"></a>Power BI를 사용하여 데이터 시각화

이 자습서에서는 Power BI 작업 영역을 만들고, Azure Synapse 작업 영역을 연결하고, Azure Synapse 작업 영역에서 데이터를 활용하는 Power BI 데이터 세트를 만드는 방법에 대해 알아봅니다. 

## <a name="overview"></a>개요

NYC 택시 데이터에서 집계된 데이터 세트를 다음 두 테이블에 만들었습니다.
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Power BI 작업 영역을 Azure Synapse 작업 영역에 연결할 수 있습니다. 이 기능을 사용하면 데이터를 Power BI 작업 영역에 쉽게 가져올 수 있습니다. Azure Synapse 작업 영역에서 Power BI 보고서를 직접 편집할 수 있습니다.

### <a name="create-a-power-bi-workspace"></a>Power BI 작업 영역 만들기

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/)에 로그인합니다.
1. **NYCTaxiWorkspace1**이라는 새 Power BI 작업 영역을 만듭니다.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>새 Power BI 작업 영역에 Azure Synapse 작업 영역 연결

1. Synapse Studio에서 **관리** > **연결된 서비스**로 차례로 이동합니다.
1. **새로 만들기** > **Power BI에 연결**을 차례로 선택하고, 다음 필드를 설정합니다.

    |설정 | 제안 값 | 
    |---|---|
    |**이름**|**NYCTaxiWorkspace1**|
    |**작업 영역 이름**|**NYCTaxiWorkspace1**|

1. **만들기**를 선택합니다.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Azure Synapse 작업 영역의 데이터를 사용하는 Power BI 데이터 세트 만들기

1. Synapse Studio에서 **개발** > **Power BI**로 차례로 이동합니다.
1. **NYCTaxiWorkspace1** > **Power BI 데이터 세트**로 차례로 이동하여 **새 Power BI 데이터 세트**를 선택합니다.
1. 마우스로 **SQLDB1** 데이터베이스 위를 가리키고, **.pbids 파일 다운로드**를 선택합니다.
1. 다운로드한 **.pbids** 파일을 엽니다. Power BI 데스크톱이 열리고, Azure Synapse 작업 영역의 **SQLDB1**에 자동으로 연결됩니다.
1. **SQL 서버 데이터베이스**라는 대화 상자가 표시되는 경우 다음을 수행합니다.
    1. **Microsoft 계정**을 선택합니다.
    1. **로그인**을 선택하고 계정에 로그인합니다.
    1. **연결**을 선택합니다.
1. **탐색기** 대화 상자가 열리면 **PassengerCountStats** 테이블을 확인하고 **로드**를 선택합니다.
1. **연결 설정** 대화 상자가 표시되면 **DirectQuery** > **확인**을 차례로 선택합니다.
1. 왼쪽에서 **보고서** 단추를 선택합니다.
1. **꺾은선형 차트**를 보고서에 추가합니다.
    1. **PassengerCount** 열을 **시각화** > **축**으로 끕니다.
    1. **SumTripDistance** 및 **AvgTripDistance** 열을 **시각화** > **값**으로 끕니다.
1. **홈** 탭에서 **게시**를 선택합니다.
1. **저장**을 선택하여 변경 내용을 저장합니다.
1. **PassengerAnalysis.pbix**라는 파일 이름을 선택한 다음, **저장**을 선택합니다.
1. **대상 선택**에서 **NYCTaxiWorkspace1**을 선택한 다음, **선택**을 클릭합니다.
1. 게시가 완료될 때까지 기다립니다.

### <a name="configure-authentication-for-your-dataset"></a>데이터 세트에 대한 인증 구성

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/)을 열고 **로그인**합니다.
1. 왼쪽의 **작업 영역** 아래에서 **NYCTaxiWorkspace1** 작업 영역을 선택합니다.
1. 해당 작업 영역 내에서 **승객 분석**이라는 데이터 세트 및 **승객 분석**이라는 보고서를 찾습니다.
1. 마우스로 **PassengerAnalysis** 데이터 세트 위를 가리키고, 줄임표(...) 단추를 선택한 다음, **설정**을 선택합니다.
1. **데이터 원본 자격 증명**에서 **인증 방법**을 **OAuth2**로 설정한 다음, **로그인**을 선택합니다.

### <a name="edit-a-report-in-synapse-studio"></a>Synapse Studio에서 보고서 편집

1. Synapse Studio로 돌아가서 **닫기 및 새로 고침**을 선택합니다.
1. **개발** 허브로 이동합니다.
1. 마우스로 **Power BI** 위를 가리키고, **Power BI 보고서** 노드 새로 고침을 선택합니다.
1. **Power BI** 아래에 다음과 같이 표시됩니다.
    * **NYCTaxiWorkspace1** > **Power BI 데이터 세트** 아래에 **PassengerAnalysis**라는 새 데이터 세트
    * **NYCTaxiWorkspace1** > **Power BI 보고서** 아래에 **PassengerAnalysis**라는 새 보고서
1. **PassengerAnalysis** 보고서를 선택합니다. 보고서가 열리고, Synapse Studio 내에서 직접 편집할 수 있습니다.

## <a name="monitor-activities"></a>모니터링 활동

1. Synapse Studio에서 **모니터** 허브로 이동합니다.
1. 이 위치에서는 작업 영역에서 수행되는 모든 활동의 기록과 현재 활성 상태인 활동을 확인할 수 있습니다.
1. **파이프라인 실행**, **Apache Spark 애플리케이션** 및 **SQL 요청**을 검색하여 작업 영역에서 이미 수행한 작업을 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [모니터](get-started-monitor.md)
                                 

