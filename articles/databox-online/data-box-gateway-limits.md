---
title: Azure Data Box Gateway 제한 | Microsoft Docs
description: Microsoft Azure Data Box Gateway에 대한 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: 641d7410e414be2adae2a83840c90a680aedd2fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683348"
---
# <a name="azure-data-box-gateway-limits"></a>Azure 데이터 박스 게이트웨이 제한

Microsoft Azure Data Box Gateway 솔루션을 배포 및 운영할 때 이러한 제한을 고려합니다. 

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway 디바이스 제한

다음 표에서는 Data Box Gateway 디바이스에 대한 제한을 설명합니다.

| Description | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 <br> 추가되는 2,500만 개의 파일에 대해 최대 한도가 1억 개에 달하는 경우 2TB의 디스크 공간, 8GB의 RAM 및 4개의 CPU 코어를 추가해야 합니다. |
|아니요. 디바이스당 공유 수 |24 |
|아니요. Azure Storage 컨테이너당 공유 수 |1 |
|공유에 기록되는 최대 파일 크기|2TB 가상 디바이스의 경우 최대 파일 크기는 500GB입니다. <br> 최대 파일 크기는 최대 5TB에 도달할 때까지 이전 비율의 데이터 디스크 크기에 따라 증가합니다. |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 박스 게이트웨이 배포 준비](data-box-gateway-deploy-prep.md)
