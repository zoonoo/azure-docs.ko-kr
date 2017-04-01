---
title: "Azure Blob Storage 간 데이터 이동 | Microsoft Docs"
description: "Azure Blob 저장소의 데이터 이동"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;sachouks
translationtype: Human Translation
ms.sourcegitcommit: 9b32c0d9c3bc19a187873eebd6ab21036ee06db8
ms.openlocfilehash: 1e773a1332d14109e9f50fb7db17528d061a98f2
ms.lasthandoff: 12/07/2016


---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage 간 데이터 이동
[!INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

가장 적합한 방법은 시나리오에 따라 달라집니다. [Azure 기계 학습의 고급 분석 시나리오](machine-learning-data-science-plan-sample-scenarios.md) 문서는 고급 분석 프로세스에서 사용되는 다양한 데이터 과학 워크플로에 필요한 리소스를 결정하도록 도와줍니다.

> [!NOTE]
> Azure Blob Storage에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../storage/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.
> 
> 

또는 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 를 사용하여 다음을 수행할 수 있습니다. 

* Azure blob 저장소의 데이터를 다운로드하는 파이프라인 만들기 및 예약 
* 게시된 Azure 기계 학습 웹 서비스에 전달 
* 예측 분석 결과 수신 
* 결과를 저장소에 업로드 

자세한 내용은 [Azure 데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기](../data-factory/data-factory-azure-ml-batch-execution-activity.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자에게 Azure 구독, 저장소 계정 및 계정에 해당하는 저장소 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

* Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 저장소 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.


