---
title: Azure Data Box Heavy 제한 | Microsoft Docs
description: Microsoft Azure Data Box Heavy 구성 요소 및 연결에 대한 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 06/28/2021
ms.author: alkohli
ms.openlocfilehash: fedd7417f6f199289bfd5c6e11a1cf1bd8c98a82
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113038357"
---
# <a name="azure-data-box-heavy-limits"></a>Azure Data Box Heavy 제한

Azure Data Box Heavy 디바이스를 배포하고 운영할 때 이러한 제한을 고려합니다. 다음 표에서는 Data Box에 대한 이러한 제한을 설명합니다.


## <a name="data-box-heavy-service-limits"></a>Data Box Heavy 서비스 제한

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-heavy-limits"></a>Data Box Heavy 제한

- Data Box Heavy는 노드당 최대 10억 개의 파일을 저장할 수 있습니다.
- Data Box Heavy는 클라우드의 노드당 최대 512개의 컨테이너 또는 공유를 지원합니다. 사용자 공유 내의 최상위 수준 디렉터리는 클라우드의 컨테이너 또는 Azure 파일 공유가 됩니다. 

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="azure-storage-account-size-limits"></a>Azure Storage 계정 크기 제한

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 블록 Blob과 페이지 Blob 및 파일 명명 규칙

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
