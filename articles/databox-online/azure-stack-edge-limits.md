---
title: Azure Stack Edge Pro FPGA 제한 | Microsoft Docs
description: 서비스 제한, 디바이스 제한 및 스토리지 제한을 포함하여 Azure Stack Edge Pro FPGA를 배포하고 운영할 때의 제한 및 권장 크기에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 8f10b6e1a621f9851ba5654f01a621551cf25f6e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110080959"
---
# <a name="azure-stack-edge-pro-fpga-limits"></a>Azure Stack Edge Pro FPGA 제한

Microsoft Azure Stack Edge Pro FPGA 솔루션을 배포 및 운영하면서 이러한 제한을 고려합니다. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge 디바이스 제한

다음 표에서는 Azure Stack Edge Pro FPGA 디바이스에 대한 제한을 설명합니다. 

다음 표에서는 Azure Stack Edge 디바이스에 대한 제한을 설명합니다.

| 설명 | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 |
|아니요. 컨테이너당 공유 수 |1 |
|디바이스당 공유 엔드포인트 및 REST 엔드포인트 최대 수| 24 |
|디바이스당 계층화된 스토리지 계정의 최대 수| 24|
|공유에 기록되는 최대 파일 크기| 5TB |
|디바이스당 리소스 그룹의 최대 수| 800 |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro FPGA 배포 준비](azure-stack-edge-deploy-prep.md)
