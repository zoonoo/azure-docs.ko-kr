---
title: Azure Stream Analytics 작업 정리
description: 이 아티클은 Azure Stream Analytics 작업을 삭제하는 방법에 대한 가이드입니다.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660409"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Azure Stream Analytics 작업 정리

Azure Stream Analytics 작업은 Azure Portal, Azure PowerShell, .Net 또는 REST API용 Azure SDK를 통해 쉽게 삭제될 수 있습니다.

>[!NOTE] 
>Stream Analytics 작업을 중지한 경우 Event Hubs 또는 Azure SQL Database와 같은 입력 및 출력 저장소에서만 데이터가 유지됩니다. Azure에서 데이터를 제거해야 하는 경우 Stream Analytics 작업의 입력 및 출력 리소스에 대해 제거 프로세스를 수행해야 합니다.

## <a name="stop-a-job-in-azure-portal"></a>Azure Portal에서 작업 중지

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. 실행 중인 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지에서 **중지**를 선택하여 작업을 중지합니다. 

   ![작업 중지](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Azure Portal에서 작업 삭제

1. Azure 포털에 로그인합니다. 

2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지에서 **삭제**를 선택하여 작업을 삭제합니다. 

   ![작업 삭제](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>PowerShell을 사용하여 작업 중지 또는 삭제

PowerShell을 사용하여 작업을 중지하려면 [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) cmdlet을 사용합니다. PowerShell을 사용하여 작업을 삭제하려면 [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) cmdlet을 사용합니다.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>.NET용 Azure SDK를 사용하여 작업 중지 또는 삭제

.NET용 Azure SDK를 사용하여 작업을 중지하려면 [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) 메서드를 사용합니다. .NET용 Azure SDK를 사용하여 작업을 삭제하려면 [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) 메서드를 사용합니다.

## <a name="stop-or-delete-a-job-using-rest-api"></a>REST API를 사용하여 작업 중지 또는 삭제

REST API를 사용하여 작업을 중지하려면 [중지](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop) 메서드를 참조합니다. REST API를 사용하여 작업을 삭제하려면 [삭제](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete) 메서드를 참조합니다.