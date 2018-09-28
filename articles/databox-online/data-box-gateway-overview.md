---
title: Microsoft Azure Data Box Gateway 개요 | Microsoft Docs
description: Azure로 데이터를 전송하는 데 사용할 수 있는 가상 어플라이언스 저장소 솔루션인 Azure Data Box Gateway에 대해 설명합니다.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 9c12674a66582ede04b4cf9d311238d61816afec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969549"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Azure Data Box Gateway(미리 보기)란? 

Azure Data Box Gateway는 Azure로 데이터를 원활하게 전송하는 데 사용할 수 있는 저장소 솔루션입니다. 이 문서에서는 Azure Data Box Gateway 솔루션의 개요, 이점, 주요 기능 및 이 장치를 배포할 수 있는 시나리오를 소개합니다. 

Data Box Gateway는 가상화된 환경 또는 하이퍼바이저에 프로비전된 가상 머신을 기반으로 하는 가상 장치입니다. 가상 장치는 온-프레미스에 있으며, NFS 및 SMB 프로토콜을 사용하여 해당 장치에 데이터를 씁니다. 그러면 장치가 Azure 블록 Blob, 페이지 Blob 또는 Azure Files로 데이터를 전송합니다. 

> [!IMPORTANT]
> Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 배포하기 전에 [미리 보기에 대한 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요.

## <a name="use-cases"></a>사용 사례

클라우드 보관 등을 위해 클라우드로 데이터를 전송하려는 경우나 클라우드 범위에서 데이터를 처리해야 하는 경우에 Data Box Gateway를 활용할 수 있습니다. Data Box Gateway를 데이터 전송에 사용할 수 있는 다양한 시나리오는 다음과 같습니다.

- **클라우드 보관** - Data Box Gateway를 사용하여 수백 TB에 달하는 데이터를 안전하고 효율적인 방식으로 Azure Storage에 복사할 수 있습니다. 데이터는 보관 시나리오용으로 한 번 수집할 수도 있고 지속적으로 수집할 수도 있습니다.

- **데이터 집계** - 데이터 처리 및 분석을 위해 여러 원본의 데이터를 Azure Storage의 단일 위치로 집계할 수 있습니다.  

- **온-프레미스 워크로드와의 통합** - 클라우드 저장소를 사용하며 흔히 사용되는 파일에 로컬로 액세스해야 하는 백업/복원 등의 온-프레미스 워크로드와 통합이 가능합니다. 

## <a name="benefits"></a>이점

Data Box Gateway의 이점은 다음과 같습니다.

- **손쉬운 데이터 전송** - 로컬 네트워크 공유를 사용할 때처럼 쉽게 Azure Storage 내부에서/외부로 데이터를 이동할 수 있습니다.  
- **고성능** - Azure에서/Azure로의 고성능 전송 기능이 제공되므로 네트워크 데이터 전송 과정이 간편해집니다. 
- **빠른 액세스** - 온-프레미스 파일에 빠르게 액세스할 수 있도록 최신 파일을 캐시합니다.  
- **대역폭 사용량 제한** - 사용량이 많은 업무 시간 동안 사용량을 제한하도록 네트워크가 제한될 때도 Azure에 데이터를 쓸 수 있습니다.  

## <a name="key-capabilities"></a>주요 기능

Data Box Gateway의 기능은 다음과 같습니다.

|기능 |설명  |
|---------|---------|
|속도     | 고도로 최적화된 완전 자동 데이터 전송 기능과 대역폭이 제공됩니다.|
|지원되는 프로토콜     | 데이터 수집을 위한 표준 SMB 및 NFS 프로토콜이 지원됩니다. <br> 지원되는 버전에 대한 자세한 내용을 확인하려면 [Data Box Gateway 시스템 요구 사항](data-box-gateway-system-requirements.md)을 방문하세요.|
|데이터 액세스     | 클라우드 API를 사용하여 Azure Storage Blob 및 Azure Files에서 데이터에 직접 액세스해 클라우드에서 데이터를 추가로 처리할 수 있습니다.|
|빠른 액세스     | 가장 최근에 사용한 파일에 빠르게 액세스할 수 있도록 장치에 로컬 캐시가 생성됩니다.|
|오프라인 업로드     | 연결 끊김 모드에서도 오프라인 업로드 시나리오가 지원됩니다.|
|데이터 새로 고침     | 클라우드의 최신 데이터로 로컬 파일을 새로 고칠 수 있습니다.|
|암호화    | *https*를 통해 클라우드로 안전하게 데이터를 전송하고 로컬에서 데이터를 암호화할 수 있도록 BitLocker가 지원됩니다.       |
|복원력     | 네트워크 복원 기능이 기본적으로 제공됩니다.        |


## <a name="specifications"></a>사양

Data Box Gateway 가상 장치의 사양은 다음과 같습니다.

| 사양                                          | 설명              |
|---------------------------------------------------------|--------------------------|
| 가상 프로세서(코어)   | 최소 4개 |            
| 메모리  | 최소 8GB|
| 가용성|단일 노드|
| 디스크| OS 디스크: 250GB <br> 데이터 디스크: 최소 2TB(씬 프로비저닝), SSD를 통해 지원해야 함|
| 네트워크 인터페이스|가상 네트워크 인터페이스 하나 이상|
| 기본 파일 공유 프로토콜|SMB 및 NFS  |
| 보안| 인증을 통해 장치와 데이터 액세스 잠금 해제 <br> AES-256 비트 암호화를 사용하여 처리 중 데이터 암호화|
| 관리| 로컬 웹 UI - 장치 초기 설치, 진단 및 전원 관리 <br> Azure Portal - Data Box Gateway 장치의 일상적인 관리       |


## <a name="components"></a>구성 요소

Data Box Gateway 솔루션은 Data Box Gateway 리소스, Data Box Gateway 가상 장치 및 로컬 웹 UI로 구성되어 있습니다.

* **Data Box Gateway 가상 장치** - 가상화된 환경 또는 하이퍼바이저에 프로비전된 가상 머신을 기반으로 하며, Azure에 데이터를 전송하는 데 사용할 수 있는 장치입니다. 
    
* **Data Box Gateway 리소스** – 다양한 지리적 위치에서 액세스할 수 있는 웹 인터페이스에서 Data Box Gateway 장치를 관리할 수 있는 Azure Portal의 리소스입니다. Data Box Gateway 리소스를 사용하여 리소스를 작성/관리하고, 장치와 경고를 확인/관리하고, 공유를 관리합니다.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Data Box 로컬 웹 UI** - 로컬 웹 UI를 사용하여 진단을 실행하고, Data Box Gateway 장치를 종료 및 다시 시작하고, 복사 로그를 보고, 서비스 요청을 위해 Microsoft 지원에 연락을 합니다.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>지역 가용성

데이터를 전송하는 Data Box Edge 물리적 장치, Azure 리소스 및 대상 저장소 계정이 모두 같은 지역에 있지 않아도 됩니다.

- **리소스 가용성** - 이 릴리스의 경우 다음 지역에서 Data Box Edge 리소스를 사용할 수 있습니다.
    - **미국** - 미국 서부 2 및 미국 동부
    - **유럽 연합** - 유럽 서부
    - **아시아 태평양** - 동남 아시아

- **대상 저장소 계정** - 데이터를 저장하는 저장소 계정은 모든 Azure 지역에서 사용할 수 있습니다. 

    성능을 최적화하려면 저장소 계정이 Data Box 데이터를 저장하는 지역이 장치를 사용하는 지역 근처여야 합니다. 저장소 계정의 지역과 장치의 지역 간 거리가 멀면 대기 시간이 길어지고 성능이 저하됩니다. 


## <a name="next-steps"></a>다음 단계

- [Data Box Gateway 시스템 요구 사항](data-box-gateway-system-requirements.md)을 검토합니다.
- [Data Box Gateway 제한](data-box-gateway-limits.md)을 알아봅니다.
- Azure Portal에서 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)를 배포합니다.




