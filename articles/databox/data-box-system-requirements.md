---
title: Microsoft Azure Data Box 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box의 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839771"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box 및 Data Box에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box를 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **호스트가 Data Box에 연결하기 위한 소프트웨어 요구 사항** - 지원되는 플랫폼, 로컬 웹 UI용 브라우저, SMB 클라이언트 및 Data Box에 연결할 수 있는 호스트에 대한 추가 요구 사항을 설명합니다.
* **Data Box의 네트워킹 요구 사항** - Data Box가 최적으로 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.


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

데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용하여 데이터를 복사할 수 있지만 복사 속도는 느려집니다.

### <a name="port-requirements"></a>포트 요구 사항

다음 표에서 SMB 또는 NFS 트래픽을 허용 하려면 방화벽에서 열려야 하는 포트를 나열 합니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *Out* 또는 *아웃 바운드* Data Box 장치 외부적으로 데이터 배포를 벗어나 전송 방향을 가리킵니다: 예를 들어, 인터넷에 아웃 바운드입니다.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
