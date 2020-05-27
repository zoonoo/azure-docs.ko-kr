---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cac6c40397e272d6d65931029c500bdcc2b774cc
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875494"
---
Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델이나 정의를 따르지 않는 데이터입니다.

## <a name="about-blob-storage"></a>Blob Storage 정보

Blob Storage는 다음을 위해 설계되었습니다.

* 브라우저에 이미지 또는 문서 직접 제공
* 분산 액세스용 파일 저장
* 동영상 및 오디오 스트리밍
* 로그 파일에 쓰기
* 백업/복원, 재해 복구 및 보관용 데이터 저장
* 온-프레미스 또는 Azure 호스티드 서비스에 의한 분석용 데이터 저장

사용자 또는 클라이언트 애플리케이션은 전 세계 어디서든 HTTP/HTTPS를 통해 Blob Storage의 개체에 액세스할 수 있습니다. Blob Storage의 개체는 [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 액세스할 수 있습니다. 클라이언트 라이브러리는 다음을 포함하여 다양한 언어에서 사용할 수 있습니다.

* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.JS](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 정보

Blob Storage는 Microsoft의 클라우드용 엔터프라이즈 빅 데이터 분석 솔루션인 Azure Data Lake Storage Gen2를 지원합니다. Azure Data Lake Storage Gen2는 다음을 비롯한 Blob 스토리지의 장점뿐만 아니라 계층적 파일 시스템을 제공합니다.

* 저렴한 비용의 계층화된 스토리지
* 고가용성
* 강력한 일관성
* 재해 복구 기능

Azure Data Lake Storage Gen2에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../articles/storage/data-lake-storage/introduction.md)를 참조하세요.
