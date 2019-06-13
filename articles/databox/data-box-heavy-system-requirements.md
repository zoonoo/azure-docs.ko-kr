---
title: Microsoft Azure 데이터 상자 높은 시스템 요구 사항 | Microsoft Docs
description: 소프트웨어 및 네트워킹 요구 사항에 Azure 데이터 상자 많은 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427733"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Azure 데이터 상자 과도 한 시스템 요구 사항 (미리 보기)

이 문서에서는 Azure 데이터 상자 많은 장치 및 장치에 연결 하는 클라이언트에 대 한 중요 한 시스템 요구 사항을 설명 합니다. 정보를 신중 하 게 전에 검토에 데이터 상자 Heavy, 배포 하 고 다음 다시 참조 되도록 필요에 따라 배포 및 후속 작업을 하는 동안 하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **상자에 과도 한 데이터에 연결 하는 호스트에 대 한 소프트웨어 요구 사항** -지원 되는 플랫폼, 로컬 웹 UI에 대 한 브라우저, SMB 클라이언트 및 Data Box에 연결할 수 있는 호스트에 대 한 추가 요구 사항을 설명 합니다.
* **데이터 상자 많은 대 한 네트워킹 요구 사항** -데이터 상자 많은 장치의 최적의 작업에 대 한 네트워킹 요구 사항에 대 한 정보를 제공 합니다.

## <a name="software-requirements"></a>소프트웨어 요구 사항

소프트웨어 요구 사항에는 지원되는 운영 체제, 로컬 웹 UI에 지원되는 브라우저 및 SMB 클라이언트에 관한 정보가 포함됩니다.

### <a name="supported-operating-systems-for-clients"></a>클라이언트에 대해 지원되는 운영 체제

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux 클라이언트에 지원되는 파일 시스템

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>지원되는 저장소 계정

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>지원되는 저장소 형식

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>지원되는 웹 브라우저

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>네트워킹 요구 사항

데이터 센터에는 고속 네트워크가 있어야 합니다. 가장 빠른 복사 속도 대 한 두 개의 GbE 40 연결 (노드당 하나) 병렬로 활용할 수 있습니다. 40 개 사용할 수 없는 경우에 (노드당 하나) 두 개 이상의 10gbe 연결 해야 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
