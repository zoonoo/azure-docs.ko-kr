---
title: Microsoft Azure Data Box Heavy 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box Heavy에 대 한 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707755"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy 시스템 요구 사항

이 문서에서는 Azure Data Box Heavy 장치와 장치에 연결 하는 클라이언트에 대 한 중요 한 시스템 요구 사항을 설명 합니다. Data Box Heavy를 배포 하기 전에 정보를 신중 하 게 검토 하 고 배포 및 후속 작업 중에 필요에 따라 다시 참조 하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **Data Box Heavy에 연결 하는 호스트에 대 한 소프트웨어 요구 사항** -지원 되는 플랫폼, 로컬 웹 UI에 대 한 브라우저, SMB 클라이언트 및 Data Box에 연결할 수 있는 호스트에 대 한 추가 요구 사항에 대해 설명 합니다.
* **Data Box Heavy에 대 한 네트워킹 요구 사항** -Data Box Heavy 장치의 최적 작업을 위한 네트워킹 요구 사항에 대 한 정보를 제공 합니다.

## <a name="software-requirements"></a>소프트웨어 요구 사항

소프트웨어 요구 사항에는 지원되는 운영 체제, 로컬 웹 UI에 지원되는 브라우저 및 SMB 클라이언트에 관한 정보가 포함됩니다.

### <a name="supported-operating-systems-for-clients"></a>클라이언트에 대해 지원되는 운영 체제

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux 클라이언트에 지원되는 파일 시스템

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>지원되는 스토리지 형식

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>지원되는 웹 브라우저

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>네트워킹 요구 사항

데이터 센터에는 고속 네트워크가 있어야 합니다. 고속 복사 속도의 경우 2 40-GbE 연결을 병렬로 사용할 수 있습니다 (노드당 하나). 40-GbE를 사용할 수 없는 경우 최소 2 10-GbE 연결 (노드당 하나)을 사용 하는 것이 좋습니다.

### <a name="port-requirements"></a>포트 요구 사항

다음 표에는 SMB 또는 NFS 트래픽을 허용 하기 위해 방화벽에서 열어야 하는 포트가 나와 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *Out* 또는 *아웃 바운드* 는 Data Box Heavy 장치가 배포를 벗어나 외부에서 데이터를 전송 하는 방향 (예: 인터넷으로 아웃 바운드)을 나타냅니다.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
