---
title: Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정
description: 사용할 기술을 결정할 수 있도록 Azure에서 데이터를 저장 및 액세스하는 여러 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671037"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정

Microsoft Azure에서는 Azure Storage에서 클라우드에 데이터를 저장 및 액세스하기 위한 여러 가지 기능을 제공합니다. 이 문서에서는 Azure 파일, Blob 및 디스크에 대해 설명하고 이러한 기능 중에서 선택하는 데 도움이 되도록 작성되었습니다.

## <a name="scenarios"></a>시나리오

다음 표에서 파일, Blob 및 디스크를 비교하고 각각에 대해 적절한 예제 시나리오를 보여 줍니다.

| 기능 | 설명 | 사용 시기 |
|--------------|-------------|-------------|
| **Azure 파일** | 어디에서나 저장된 파일에 액세스할 수 있는 SMB 인터페이스, 클라이언트 라이브러리 및 [REST 인터페이스](/rest/api/storageservices/file-service-rest-api)를 제공합니다. | 이미 기본 파일 시스템 API를 사용하는 클라우드로 애플리케이션을 “전환”하여 Azure에서 실행 중인 다른 애플리케이션과 데이터를 공유하려고 합니다.<br/><br/>여러 가상 머신에서 액세스해야 하는 개발 및 디버깅 도구를 저장하려고 합니다. |
| **Azure Blob** | 클라이언트 라이브러리와 구조화되지 않은 데이터를 블록 Blob의 대규모로 저장하고 액세스할 수 있는 [REST 인터페이스를](/rest/api/storageservices/blob-service-rest-api) 제공합니다.<br/><br/>엔터프라이즈 빅 데이터 분석 솔루션을 위한 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)도 지원합니다. | 애플리케이션에서 스트리밍 및 임의 액세스 시나리오를 지원하도록 하려고 합니다.<br/><br/>어디에서든 애플리케이션 데이터에 액세스할 수 있게 되기를 원합니다.<br/><br/>Azure에서 엔터프라이즈 Data Lake를 빌드하고 빅 데이터 분석을 수행하려고 합니다. |
| **Azure 디스크** | 데이터를 연결된 가상 하드 디스크에서 영구적으로 저장 및 액세스할 수 있도록 클라이언트 라이브러리 및 [REST 인터페이스](/rest/api/compute/manageddisks/disks/disks-rest-api)를 제공합니다. | 기본 파일 시스템 API를 사용하는 애플리케이션을 데이터를 영구 디스크에 읽고 쓰도록 전환하려고 합니다.<br/><br/>가상 머신 외부에서 액세스할 필요가 없는 데이터를 디스크가 연결된 컴퓨터에 저장하려고 합니다. |


## <a name="next-steps"></a>다음 단계

데이터가 저장 및 액세스되는 방식을 결정할 때 관련 비용도 고려해야 합니다. 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.
  
일부 SMB 기능은 클라우드에 적용되지 않습니다. 자세한 내용은 [Azure File 서비스에서 지원되지 않는 기능](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)을 참조하세요.
 
Azure Blob에 대한 자세한 내용은 Azure [Blob 저장소란 무엇입니까?](../blobs/storage-blobs-overview.md)

디스크 저장소에 대한 자세한 내용은 [관리 디스크 소개를](../../virtual-machines/windows/managed-disks-overview.md)참조하십시오.

Azure 파일에 대한 자세한 내용은 Azure [파일 배포 계획](../files/storage-files-planning.md)문서입니다.