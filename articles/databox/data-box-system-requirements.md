---
title: Microsoft Azure Data Box 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box 및 Data Box에 연결 하는 클라이언트에 대 한 중요 한 시스템 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767858"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box 및 Data Box에 연결 하는 클라이언트에 대 한 중요 한 시스템 요구 사항을 설명 합니다. Data Box를 배포 하기 전에 정보를 신중 하 게 검토 하 고 배포 및 작업 중에 필요에 따라이 정보를 참조 하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **소프트웨어 요구 사항:** Data Box에 연결 하는 호스트의 경우는 지원 되는 운영 체제, 파일 전송 프로토콜, 저장소 계정, 저장소 형식 및 로컬 웹 UI에 대 한 브라우저를 설명 합니다.
* **네트워킹 요구 사항:** Data Box의 경우 Data Box의 최적 작업을 위한 네트워크 연결 및 포트 요구 사항을 설명 합니다.


## <a name="software-requirements"></a>소프트웨어 요구 사항

소프트웨어 요구 사항에는 지원 되는 운영 체제, 파일 전송 프로토콜, 저장소 계정, 저장소 유형 및 로컬 웹 UI 용 브라우저가 포함 됩니다.

### <a name="supported-operating-systems-for-clients"></a>클라이언트에 대해 지원되는 운영 체제

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>클라이언트에 대해 지원 되는 파일 전송 프로토콜

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> 내보내기 주문을 위해 REST를 통해 Data Box 공유에 대 한 연결이 지원 되지 않습니다. 

### <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>지원되는 스토리지 형식

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>지원되는 웹 브라우저

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>네트워킹 요구 사항

데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10gbe 연결을 사용할 수 없는 경우 데이터를 복사 하는 데 1-GbE 데이터 링크를 사용할 수 있지만 복사 속도가 영향을 받습니다.

### <a name="port-requirements"></a>포트 요구 사항

다음 표에는 SMB 또는 NFS 트래픽을 허용 하기 위해 방화벽에서 열어야 하는 포트가 나와 있습니다. 이 표에서 (*인바운드* *)는 들어오는* 클라이언트에서 장치에 대 한 액세스를 요청 하는 방향을 나타냅니다. *Out* (또는 *아웃 바운드*)은 배포 이외에 인터넷으로의 아웃 바운드와 같이 Data Box 장치가 외부에서 데이터를 전송 하는 방향을 나타냅니다.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
