---
title: 마이크로소프트 Azure 데이터 박스 무거운 시스템 요구 사항 | 마이크로 소프트 문서
description: Azure 데이터 박스 헤비의 소프트웨어 및 네트워킹 요구 사항에 대해 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260073"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure 데이터 상자 무거운 시스템 요구 사항

이 문서에서는 Azure 데이터 상자 무거운 장치 및 장치에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항에 대해 설명합니다. 데이터 박스 헤비를 배포하기 전에 정보를 주의 깊게 검토한 다음 배포 및 후속 작업 중에 필요에 따라 다시 참조하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **Data Box Heavy에 연결하는 호스트에 대한 소프트웨어 요구 사항** - 지원되는 플랫폼, 로컬 웹 UI에 대한 브라우저, SMB 클라이언트 및 데이터 상자에 연결할 수 있는 호스트에 대한 추가 요구 사항을 설명합니다.
* **데이터 박스 헤비에 대한 네트워킹 요구 사항** - 데이터 박스 무거운 장치의 최적 작동을위한 네트워킹 요구 사항에 대한 정보를 제공합니다.

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

데이터 센터에는 고속 네트워크가 있어야 합니다. 가장 빠른 복사 속도를 위해 두 개의 40GbE 연결을 병렬로 사용할 수 있습니다(노드당 하나). 40GbE를 사용할 수 없는 경우 노드당 하나 이상의 10GbE 연결을 두 개 이상 포함하는 것이 좋습니다.

### <a name="port-requirements"></a>포트 요구 사항

다음 표에는 SMB 또는 NFS 트래픽을 허용하기 위해 방화벽에서 열어야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *아웃 바운드* 또는 *아웃바운드는* 데이터 박스 헤비 장치가 배포 를 넘어 외부로 데이터를 보내는 방향(예: 인터넷아웃바운드)을 나타냅니다.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
