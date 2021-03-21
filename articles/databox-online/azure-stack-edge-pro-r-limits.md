---
title: Azure Stack Edge Pro R 제한 | Microsoft Docs
description: Azure Stack Edge Pro R에 대 한 시스템 제한 및 권장 크기를 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467634"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R 제한

Azure Stack Edge Pro R 솔루션을 배포 하 고 작동할 때 이러한 제한을 고려 합니다.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Azure Stack Edge Pro R 서비스 제한

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack Edge Pro R 장치 제한

다음 표에서는 Azure Stack Edge Pro R 장치에 대 한 제한을 설명 합니다.

| 설명 | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 |
|아니요. 컨테이너당 공유 수 |1 |
|최대 개수입니다. 장치 당 공유 끝점 및 REST 끝점| 24 |
|최대 개수입니다. 장치당 계층화 된 저장소 계정| 24|
|공유에 기록되는 최대 파일 크기| 5TB |
|장치당 리소스 그룹의 최대 수| 800 |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Edge Pro R Azure Stack 배포 준비](azure-stack-edge-pro-r-deploy-prep.md)
