---
title: Stream Analytics 작업에 대한 출력 데이터를 구성하는 방법 | Microsoft Docs
description: Stream Analytics 작업에 대한 출력 구성 | 학습 경로 세그먼트.
keywords: 데이터 출력, 데이터 이동
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/26/2017
ms.author: jeanb
ms.openlocfilehash: 9e1a8640421c4232cf4abc09794ca584995906f9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Stream Analytics 작업에 대한 출력 데이터를 구성하는 방법

Azure Stream Analytics 작업은 기존 데이터 링크에 대한 연결을 정의하는 하나 이상의 데이터 출력에 연결할 수 있습니다. Stream Analytics 작업이 들어오는 데이터를 처리 및 변환하면 데이터 출력 이벤트 스트림이 작업의 출력에 기록됩니다.

Stream Analytics 데이터 출력을 사용하여 실시간 대시보드 또는 경고를 제공하거나, 데이터 이동 워크플로를 트리거하거나, 나중에 일괄 처리하기 위해 데이터를 보관할 수 있습니다. Stream Analytics은 여기에 자세히 문서화되어 있는 여러 Azure 서비스와 높은 수준으로 통합됩니다.

Stream Analytics 작업에 출력을 추가하려면

1. [Azure Portal](https://portal.azure.com)에서 작업을 열고 **출력**을 클릭한 다음, 표시되는 출력 블레이드에서 **추가**를 클릭합니다.
   
    ![출력 추가](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. **출력 별칭** 상자에 이 출력의 이름을 입력합니다. 이 이름은 나중에 작업 쿼리에서 출력을 참조하는 데 사용할 수 있습니다.  
   
    출력에 연결하는 데 필요한 나머지 연결 속성을 입력합니다.  이러한 필드는 출력 형식마다 다르며 여기서 자세히 정의됩니다.  
   
    ![데이터 이동 서비스 선택](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. 출력 형식에 따라 데이터를 직렬화하거나 형식을 지정하는 방법을 지정해야 할 수도 있습니다. 각 출력 형식에 대한 특정 직렬화 설정은 여기에 문서화되어 있습니다.
   
    데이터 원본에 연결하는 데 필요한 나머지 연결 속성을 입력합니다. 이러한 필드는 입력 형식 및 원본 형식마다 다르며 [작업 만들기 문서](stream-analytics-create-a-job.md)에 자세히 정의되어 있습니다.  

> [!Note]
>
> 작업에 추가 된 모든 출력 요소는 작업이 시작 및 이벤트 전송이 시작되기 전에 있어야 합니다. 예를 들어, 출력으로 Blob 저장소를 사용한 경우, 작업은 저장소 계정을 자동으로 만들지 않습니다. ASA 작업이 시작되기 전에 사용자가 만들어야 합니다.
> 
 

## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

