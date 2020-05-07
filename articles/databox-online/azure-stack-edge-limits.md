---
title: Azure Stack Edge 제한 | Microsoft Docs
description: Azure Stack Edge에 대 한 시스템 제한 및 권장 크기를 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 4f7800efb5d4382e8d73c819d950fdfafd10f296
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569837"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge 제한 사항

Microsoft Azure Stack Edge 솔루션을 배포 하 고 작동할 때 이러한 제한을 고려 합니다. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack 장치 제한

다음 표에서는 Azure Stack Edge 장치에 대 한 제한을 설명 합니다. 

| Description | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 |
|아니요. 디바이스당 공유 수 |24 |
|아니요. 컨테이너당 공유 수 |1 |
|공유에 기록되는 최대 파일 크기| 5TB |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포 준비](azure-stack-edge-deploy-prep.md)
