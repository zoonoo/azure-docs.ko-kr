---
title: Azure Blob Storage 간 데이터 이동 | Microsoft Docs
description: Azure Blob Storage 간 데이터 이동
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 7d0111b22df45577fccc3f4491f375ddd2e8b40f
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344470"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage 간 데이터 이동

팀 데이터 과학 프로세스는 프로세스의 각 단계에서 가장 적절한 방법으로 처리되거나 분석되도록 데이터가 다양한 저장소 환경에 수집되거나 로드되어야 합니다.
다음 문서에서는 다양한 기술을 사용하여 Azure Blob Storage 간에 데이터를 이동하는 방법을 설명합니다.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AZCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

가장 적합한 방법은 시나리오에 따라 달라집니다. [Azure Machine Learning의 고급 분석 시나리오](plan-sample-scenarios.md) 문서는 고급 분석 프로세스에서 사용되는 다양한 데이터 과학 워크플로에 필요한 리소스를 결정하도록 도와줍니다.

> [!NOTE]
> Azure Blob Storage에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.
> 
> 

또는 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 를 사용하여 다음을 수행할 수 있습니다. 

* Azure blob 저장소의 데이터를 다운로드하는 파이프라인 만들기 및 예약 
* 게시된 Azure Machine Learning 웹 서비스에 전달 
* 예측 분석 결과 수신 
* 결과를 저장소에 업로드 

자세한 내용은 [Azure 데이터 팩터리 및 Azure Machine Learning을 사용하여 예측 파이프라인 만들기](../../data-factory/transform-data-using-machine-learning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자에게 Azure 구독, 저장소 계정 및 계정에 해당하는 저장소 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

* Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 저장소 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../../storage/common/storage-create-storage-account.md)를 참조하세요.

