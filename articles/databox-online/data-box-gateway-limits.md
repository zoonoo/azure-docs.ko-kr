---
title: Azure Data Box Gateway 제한 | Microsoft Docs
description: Microsoft Azure Data Box Gateway에 대한 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/15/2019
ms.author: alkohli
ms.openlocfilehash: f01fb88bf0ea726b421111a262bdfdd68cd3d38c
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388418"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Azure Data Box Gateway 제한(미리 보기)


Microsoft Azure Data Box Gateway 솔루션을 배포 및 운영할 때 이러한 제한을 고려합니다. 

> [!IMPORTANT] 
> Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 배포하기 전에 [미리 보기에 대한 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway 서비스 제한

- 이 릴리스에서는 미국, EU 및 아시아 태평양의 특정 지역에서만 서비스를 사용할 수 있습니다. 자세한 내용은 [지역 가용성](#data-box-gateway-overview#region-availability)을 참조하세요. 저장소 계정은 디바이스가 배포된 지역(서비스 지역과 다를 수 있음)에 물리적으로 가장 가까운 곳에 있어야 합니다.
- Data Box Gateway 리소스를 다른 구독 또는 리소스 그룹으로 이동할 수 없습니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)을 참조하세요.

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway 디바이스 제한

다음 표에서는 Data Box Gateway 디바이스에 대한 제한을 설명합니다.

| 설명 | 값 |
|---|---|
| 아니요. 디바이스당 파일 수 |1억 <br> 제한은 2TB 디스크 공간마다 25백만 개 파일로, 최대 한도는 1억 개입니다. |
| 아니요. 디바이스당 공유 수 |24 |
|공유에 기록되는 최대 파일 크기|2TB 가상 디바이스의 경우 최대 파일 크기는 500GB입니다. <br> 최대 파일 크기는 최대 5TB에 도달할 때까지 이전 비율의 데이터 디스크 크기에 따라 증가합니다. |

## <a name="azure-storage-limits"></a>Azure Storage 제한

이 섹션에서는 Data Box Gateway/Data Box Edge 서비스에 적용되는, Azure Storage 서비스에 대한 제한 및 Azure Files, Azure 블록 Blob 및 Azure 페이지 Blob에 대한 필수 명명 규칙을 설명합니다. 저장소 제한을 신중히 검토하고 모든 권장 사항을 수행합니다.

Azure 저장소 서비스 제한에 대한 최신 정보 및 공유, 컨테이너 및 파일 이름 지정에 대한 모범 사례는 다음으로 이동합니다.

- [컨테이너 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [공유 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 Blob 및 페이지 Blob 규칙](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure Storage 서비스 제한을 초과하거나 Azure Files/Blob 명명 규칙을 준수하지 않는 파일 또는 디렉터리가 있는 경우 이러한 파일 또는 디렉터리는 Data Box Gateway/Data Box Edge 서비스를 통해 Azure Storage로 수집되지 않습니다.

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

다음 주의 사항은 Azure로 이동되는 데이터에 적용됩니다.

- 둘 이상의 디바이스가 같은 컨테이너에 기록하지 않도록 하는 것이 좋습니다.
- 클라우드에 복사되는 개체와 동일한 이름을 사용하는 기존 Azure 개체(예: Blob 또는 파일)가 있는 경우 디바이스는 클라우드의 파일을 덮어씁니다.
- 공유 폴더 아래에 만들어진 빈 디렉터리 계층 구조(파일 없음)는 Blob 컨테이너로 업로드되지 않습니다.
- 디바이스 크기보다 큰 파일을 복사하는 경우 *Robocopy* 또는 *rsync*를 사용하여 오류가 없도록 하는 것이 좋습니다.

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

저장소 계정에 복사되는 데이터 크기에 대한 제한은 다음과 같습니다. 업로드한 데이터가 이러한 제한을 준수하는지 확인합니다. 이러한 제한에 대한 최신 정보는 [Azure Blob Storage 크기 조정 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) 및 [Azure Files 크기 조정 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)로 이동합니다.

| Azure 저장소 계정에 복사되는 데이터의 크기                      | 기본 제한          |
|---------------------------------------------------------------------|------------------------|
| 블록 Blob 및 페이지 Blob                                            | 저장소 계정당 500TB|


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

쓸 수 있는 Azure 개체의 크기는 다음과 같습니다. 업로드되는 모든 파일이 이러한 제한을 준수하는지 확인합니다.

| Azure 개체 형식 | 기본 제한                                             |
|-------------------|-----------------------------------------------------------|
| 블록 Blob        | ~ 8TB                                                 |
| 페이지 Blob         | 1TB <br> 페이지 Blob 형식으로 업로드되는 모든 파일은 정렬된 512바이트(정수 배수)여야 합니다. 그렇지 않은 경우 업로드에 실패합니다. <br> VHD 및 VHDX는 정렬된 512바이트입니다. |


## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
