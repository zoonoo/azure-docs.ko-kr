---
title: Azure 리소스에서 리소스 로그를 수집하고 Azure Monitor로 분석
description: Azure 리소스에서 Log Analytics 작업 영역(로그 쿼리를 사용하여 로그를 분석할 수 있는 작업 영역)으로 리소스 로그를 수집하도록 진단 설정을 구성하는 방법에 대한 자습서입니다.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: f2c780ffb7705575bf1bb5cabb6a09d9dabc0690
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505842"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>자습서: Azure 리소스에서 리소스 로그 수집 및 분석

리소스 로그는 Azure 리소스의 자세한 작업에 대한 인사이트를 제공하며 작업의 상태 및 가용성을 모니터링하는 데 유용합니다. Azure 리소스는 자동으로 리소스 로그를 생성하지만, 리소스 로그를 수집할 위치는 사용자가 구성해야 합니다. 이 자습서에서는 Azure 구독에서 리소스의 리소스 로그를 수집하고 로그 쿼리를 사용하여 분석하는 진단 설정을 만드는 과정을 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Monitor에서 Log Analytics 작업 영역 만들기
> * 리소스 로그를 수집하는 진단 설정 만들기 
> * 로그를 분석하는 간단한 로그 쿼리 만들기


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 모니터링할 Azure 리소스가 필요합니다. 진단 설정을 지원하는 Azure 구독의 모든 리소스를 사용할 수 있습니다. 리소스가 진단 설정을 지원하는지 확인하려면 Azure Portal에서 리소스 메뉴로 이동한 후 메뉴의 **모니터링** 섹션에 **진단 설정** 옵션이 있는지 확인합니다.


## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.


## <a name="create-a-workspace"></a>작업 영역 만들기
Azure Monitor의 Log Analytics 작업 영역은 다양한 원본에서 로그 데이터를 수집하여 인덱싱하며, 강력한 쿼리 언어를 사용하여 고급 분석을 수행할 수 있습니다. 데이터를 전송하기 위한 진단 설정을 만들려면 먼저 Log Analytics 작업 영역이 있어야 합니다. Azure 구독의 기존 작업 영역을 사용해도 되고 다음 절차에 따라 새로 만들어도 됩니다. 

> [!NOTE]
> **Azure Monitor** 메뉴의 Log Analytics 작업 영역에서 데이터를 작업할 수도 있지만, 여기서는 **Log Analytics 작업 영역** 메뉴에서 작업 영역을 만들고 관리합니다.

1. **모든 서비스**에서 **Log Analytics 작업 영역**을 선택합니다.
2. 화면 위쪽에서 **추가**를 클릭하고 작업 영역에 대한 다음 세부 정보를 입력합니다.
   - **Log Analytics 작업 영역**: 새 작업 영역의 이름입니다. 이 이름은 모든 Azure Monitor 구독에서 전역적으로 고유해야 합니다.
   - **구독**: 작업 영역을 저장할 구독을 선택합니다. 모니터링할 리소스와 동일한 구독이 아니어도 상관 없습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 선택하거나 **새로 만들기**를 클릭하여 새로 만듭니다. 모니터링할 리소스와 동일한 리소스 그룹이 아니어도 상관 없습니다.
   - **위치**: Azure 지역을 선택하거나 새 Azure 지역을 만듭니다. 모니터링할 리소스와 동일한 위치가 아니어도 상관 없습니다.
   - **가격 책정 계층**: 가격 책정 계층으로 *종량제*를 선택합니다. 나중에 가격 책정 계층을 변경할 수 있습니다. 가격 책정 계층에 대한 자세한 내용을 보려면 **Log Analytics 가격 책정** 링크를 클릭하세요.

    ![새 작업 영역](media/tutorial-resource-logs/new-workspace.png)

3. **확인**을 클릭하여 작업 영역을 만듭니다.

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
[진단 설정](../platform/diagnostic-settings.md)은 특정 리소스에 대한 리소스 로그를 보낼 위치를 정의합니다. 진단 설정 하나에 여러 [대상](../platform/diagnostic-settings.md#destinations)이 있을 수 있지만, 이 자습서에서는 Log Analytics 작업 영역만 사용합니다.

1. 리소스 메뉴의 **모니터링** 섹션에서 **진단 설정**을 선택합니다.
2. "정의된 진단 설정이 없음" 메시지가 표시될 것입니다. **진단 설정 추가**를 클릭합니다.

    ![진단 설정](media/tutorial-resource-logs/diagnostic-settings.png)

3. 각 진단 설정은 다음과 같은 세 가지 기본 부분으로 구성되어 있습니다.
 
   - **Name**: 별 영향이 없으며 알기 쉬운 이름이면 됩니다.
   - **대상**: 로그를 보낼 하나 이상의 대상입니다. 모든 Azure 서비스는 동일한 3개 가능 대상 세트를 공유합니다. 각 진단 설정은 하나 이상의 대상을 정의할 수 있지만, 대상을 하나만 정의할 수 있는 유형도 있습니다. 
   - **범주**: 각 대상으로 보낼 로그 범주입니다. 범주 세트는 Azure 서비스마다 다릅니다.

4. **Log Analytics 작업 영역에 보내기**를 선택하고, 앞에서 만든 작업 영역을 선택합니다.
5. 수집하려는 범주를 선택합니다. 사용 가능한 범주 정의는 각 서비스의 설명서를 참조하세요.

    ![진단 설정](media/tutorial-resource-logs/diagnostic-setting.png)

6. **저장**을 클릭하여 진단 설정을 저장합니다.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>로그 쿼리를 사용하여 로그 검색
KQL(Kusto 쿼리 언어)로 작성된 로그 쿼리를 사용하여 Log Analytics 작업 영역에서 데이터가 검색됩니다. Azure Monitor의 인사이트 및 솔루션은 특정 서비스에 대한 데이터를 검색하는 로그 쿼리를 제공하지만, Azure Portal에서 Log Analytics를 사용하여 로그 쿼리 및 해당 결과를 직접 작업할 수 있습니다. 

1. 리소스 메뉴의 **모니터링** 섹션에서 **로그**를 선택합니다.
2. Log Analytics가 열리고, 범위가 사용자의 리소스로 설정된 빈 쿼리 창이 표시됩니다. 모든 쿼리에는 해당 리소스의 레코드만 포함됩니다.

    > [!NOTE]
    > Azure Monitor 메뉴에서 로그를 연 경우에는 범위가 Log Analytics 작업 영역으로 설정됩니다. 이 경우 모든 쿼리는 작업 영역에 있는 모든 레코드를 포함합니다.
   
    ![로그](media/tutorial-resource-logs/logs.png)

4. 예제에 표시된 서비스는 리소스 로그를 **AzureDiagnostics** 테이블에 쓰지만, 다른 서비스는 다른 테이블에 쓸 수 있습니다. 다른 Azure 서비스에서 사용하는 테이블에 대한 자세한 내용은 [Azure 리소스 로그를 지원하는 서비스, 스키마 및 범주](../platform/resource-logs-schema.md)를 참조하세요.

    > [!NOTE]
    > 많은 서비스가 AzureDiagnostics 테이블에 리소스 로그를 씁니다. Azure Monitor 메뉴에서 Log Analytics를 시작하는 경우 `ResourceProvider` 열이 있는 `where` 문을 추가하여 특정 서비스를 지정해야 합니다. 리소스의 메뉴에서 Log Analytics를 시작하는 경우 범위가 이 리소스의 레코드로만 설정되므로 이 열이 필요 없습니다. 샘플 쿼리는 서비스의 설명서를 참조하세요.


5. 쿼리를 입력하고 **실행**을 클릭하여 결과를 검사합니다. 
6. 로그 쿼리 작성에 대한 자습서는 [Azure Monitor에서 로그 쿼리 시작](../log-query/get-started-queries.md)을 참조하세요.

    ![로그 쿼리](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>다음 단계
Log Analytics 작업 영역으로 리소스 로그를 수집하는 방법을 배웠으므로, 이 데이터를 분석하는 로그 쿼리 작성에 대한 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [Azure Monitor에서 로그 쿼리 시작](../log-query/get-started-queries.md)
