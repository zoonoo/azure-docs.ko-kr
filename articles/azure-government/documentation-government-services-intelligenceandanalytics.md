---
title: "Azure Government 인텔리전스 + 분석 | Microsoft Docs"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0233aa66bc4f4f135456ec15bd09756e63192b14
ms.lasthandoff: 03/06/2017


---
# <a name="azure-government-intelligence--analytics"></a>Azure Government 인텔리전스 + 분석
이 문서에서는 Azure Government 환경의 인텔리전스 및 분석 서비스, 서비스 변형과 고려 사항을 개략적으로 설명합니다.

## <a name="hdinsight"></a>HDInsight
Linux Standard의 HDInsight는 일반적으로 Azure Government에서 사용할 수 있습니다. <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>여기</a>에서 HDInsight를 사용하여 Azure Government에서 데이터 중심 솔루션을 구축하는 방법에 대한 데모를 볼 수 있습니다.

Linux Premium의 HDInsight가 곧 제공됩니다.

### <a name="variations"></a>변형
다음 HDInsight 기능은 현재 Azure Government에서 사용할 수 없습니다.

* HDInsight는 Windows에서 사용할 수 없습니다.
* Azure Data Lake Store는 Azure Government에서 현재 사용할 수 없습니다. Azure Blob Storage가 현재 사용할 수 있는 유일한 저장소 옵션입니다.

Azure Government에서 Log Analytics의 URL은 서로 다릅니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| HDInsight 클러스터 | \*.azurehdinsight.net | \*.azurehdinsight.us |

자세한 내용은 [HDInsight 공개 문서](../hdinsight/hdinsight-hadoop-introduction.md)를 참조하세요.

## <a name="power-bi"></a>Power BI
Power BI US Government는 Office 365 US Government 커뮤니티 구독의 일부로 일반적으로 사용할 수 있습니다. <a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>여기</a>에서 Power BI US Government에 대해 알아볼 수 있습니다. <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>여기</a>에서 Power BI를 사용하여 Azure Government에서 데이터 중심 솔루션을 구축하는 방법에 대한 데모를 볼 수 있습니다.

Power BI Embedded가 출시될 예정입니다.

### <a name="variations"></a>변형

Power BI에 대한 URL은 US Government에서 다릅니다.

| 서비스 유형 | Power BI Commercial | Power BI US Government |
| --- | --- | --- |
| Power BI URL | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

