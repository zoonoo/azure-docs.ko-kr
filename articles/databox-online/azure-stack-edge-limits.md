---
title: Azure Stack Edge Pro 제한 | Microsoft Docs
description: 서비스 한도, 장치 제한 및 저장소 제한을 포함 하 여 Edge Pro Azure Stack 배포 하 고 작동할 때 제한 사항 및 권장 크기에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992767"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro 제한 사항

Microsoft Azure Stack Edge Pro 솔루션을 배포 하 고 작동할 때 이러한 제한을 고려 합니다. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge 장치 제한

다음 표에서는 Azure Stack Edge Pro 장치에 대 한 제한을 설명 합니다. 

다음 표에서는 Azure Stack Edge 장치에 대 한 제한을 설명 합니다.

| 설명 | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 |
|아니요. 컨테이너당 공유 수 |1 |
|최대 개수입니다. 장치 당 공유 끝점 및 REST 끝점| 24 |
|최대 개수입니다. 장치당 계층화 된 저장소 계정| 24|
|공유에 기록되는 최대 파일 크기| 5TB |
|장치당 리소스 그룹의 최대 수| 800 |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Edge Pro Azure Stack 배포 준비](azure-stack-edge-deploy-prep.md)
