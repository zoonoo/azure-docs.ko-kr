---
title: Azure Blob 스토리지 간 데이터 이동 - Team Data Science Process
description: Azure Storage Explorer, AzCopy, Python, SSIS를 사용하여 Azure Blob Storage의 데이터 이동
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 151375a37b80567aecf100ec3cd576882d06e3f3
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902252"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage 간 데이터 이동

팀 데이터 과학 프로세스는 프로세스의 각 단계에서 가장 적절한 방법으로 처리되거나 분석되도록 데이터가 다양한 스토리지 환경에 수집되거나 로드되어야 합니다.

## <a name="different-technologies-for-moving-data"></a>데이터 이동에 대한 다양한 기술

다음 문서에서는 다양한 기술을 사용하여 Azure Blob Storage 간에 데이터를 이동하는 방법을 설명합니다.

* [Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
  * Storage Explorer는 Windows, macOS, Linux에서 Azure Storage 데이터 작업 시에 사용할 수 있는 Microsoft의 무료 도구입니다.
  * Azure의 데이터 과학 가상 머신에서 제공하는 스크립트를 통해 설정된 VM을 사용하는 경우 Azure Storage Explorer가 VM에 이미 설치되어 있습니다.
* [AZCopy](../../storage/common/storage-use-azcopy-v10.md)
  * AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 
* [Python SDK](../../storage/blobs/storage-quickstart-blobs-python.md)
  * Python용 Azure Blob Storage 클라이언트 라이브러리를 사용하여 Blob을 이전합니다.
* [Azure용 SSIS(SQL Server Integration Services) 기능 팩](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)
  * SSIS는 Azure에 연결하고, Azure와 온-프레미스 데이터 원본 간에 데이터를 전송하며, Azure에 저장된 데이터를 처리하는 구성 요소를 제공합니다. SSIS를 사용하여 하이브리드 데이터 통합 시나리오에서 일반적인 비즈니스 요구 사항을 충족하는 정식 시나리오에 대한 자세한 내용은 [Azure용 SQL Server Integration Services 통합 팩으로 더 많은 작업 수행 블로그](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238)를 참조하세요.
  * SSIS에 대한 교육 자료는 [SSIS에 대한 실습 교육](https://www.microsoft.com/sql-server/training-certification)을 참조하세요.
  * SISS를 사용하여 간단한 ETL(추출, 변환 및 로드) 패키지를 빌드하는 방법에 대한 자세한 내용은 [SSIS 자습서: 간단한 ETL 패키지 만들기](/sql/integration-services/ssis-how-to-create-an-etl-package)를 참조하세요.

가장 적합한 방법은 시나리오에 따라 달라집니다. [Azure Machine Learning의 고급 분석 시나리오](plan-sample-scenarios.md) 문서는 고급 분석 프로세스에서 사용되는 다양한 데이터 과학 워크플로에 필요한 리소스를 결정하도록 도와줍니다.

> [!NOTE]
> Azure Blob Storage에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../../storage/blobs/storage-quickstart-blobs-dotnet.md) 및 [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts)를 참조하세요.
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory 사용

또는 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 를 사용하여 다음을 수행할 수 있습니다. 

* Azure Blob Storage의 데이터를 다운로드하는 파이프라인 만들기 및 예약
* 게시된 Azure Machine Learning 웹 서비스에 전달 
* 예측 분석 결과 수신 
* 결과를 스토리지에 업로드 

자세한 내용은 [Azure 데이터 팩터리 및 Azure Machine Learning을 사용하여 예측 파이프라인 만들기](../../data-factory/transform-data-using-machine-learning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 사용자에게 Azure 구독, 스토리지 계정 및 계정에 해당하는 스토리지 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure Storage 계정 이름과 계정 키를 알아야 합니다.

* Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 스토리지 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.