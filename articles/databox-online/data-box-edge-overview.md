---
title: Microsoft Azure Data Box Edge 개요 | Microsoft Docs
description: Azure로의 네트워크 기반 전송에 물리적 디바이스를 사용하는 저장소 솔루션인 Azure Data Box Edge에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/27/2019
ms.author: alkohli
ms.openlocfilehash: 8e07678604797e7e2090f5fefcdb3f3adff40161
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521774"
---
# <a name="what-is-azure-data-box-edge"></a>Azure Data Box Edge란? 

Azure Data Box Edge는 데이터를 처리하여 네트워크를 통해 Azure로 전송할 수 있는 저장소 솔루션입니다. 이 문서에서는 Data Box Edge 솔루션의 개요, 이점, 주요 기능 및 이 디바이스를 배포할 수 있는 시나리오를 소개합니다. 

Data Box Edge는 Microsoft에서 제공하는 물리적 디바이스를 사용하여 보안 데이터 전송을 더욱 빠르게 수행합니다. 물리적 디바이스는 온-프레미스에 있으며, NFS 및 SMB 프로토콜을 사용하여 해당 디바이스에 데이터를 씁니다. 

Data Box Edge에는 Data Box Gateway의 모든 게이트웨이 기능이 포함되어 있습니다. Data Box에는 Azure 블록 Blob, 페이지 Blob 또는 Azure Files로 이동하는 데이터를 분석/처리/필터링하는 데 사용할 수 있는 AI 지원 에지 컴퓨팅 기능도 추가로 포함되어 있습니다.  

## <a name="use-cases"></a>사용 사례

Azure Data Box Edge는 네트워크 데이터 전송 기능이 포함된 AI 지원 에지 컴퓨팅 디바이스입니다. Data Box Edge를 데이터 전송에 사용할 수 있는 다양한 시나리오는 다음과 같습니다.

- **데이터 전처리** - 온-프레미스 또는 IoT 디바이스의 데이터가 생성되는 위치와 가까운 곳에서 해당 데이터를 분석하여 결과를 빠르게 생성할 수 있습니다. Data Box Edge는 전체 데이터 집합을 클라우드로 전송하여 고급 처리 또는 심층 분석을 수행합니다.  전처리를 사용하여 다음 작업을 수행할 수 있습니다. 

    - 데이터 집계
    - 데이터 수정(예: PII(개인 식별이 가능한 정보) 제거)
    - 클라우드에서 심층 분석에 필요한 데이터의 하위 집합을 만들어 전송
    - IoT 이벤트 분석 및 대응 

- **유추 Azure Machine Learning** - Data Box Edge를 사용하면 ML(Machine Learning) 모델을 실행하여 결과를 빠르게 생성한 다음 클라우드로 데이터를 전송하기 전에 해당 결과와 관련한 조치를 취할 수 있습니다. ML 모델을 계속 다시 학습시키고 개선할 수 있도록 전체 데이터 집합이 전송됩니다.

- **네트워크를 통해 Azure로 데이터 전송** - Data Box Edge를 사용하면 Azure로 데이터를 쉽고 빠르게 전송하여 추가 계산 및 분석을 수행하거나 데이터를 보관할 수 있습니다. 

## <a name="benefits"></a>이점

Data Box Edge의 이점은 다음과 같습니다.

- **손쉬운 데이터 전송** - 로컬 네트워크 공유를 사용할 때처럼 쉽게 Azure Storage 내부에서/외부로 데이터를 이동할 수 있습니다.  
- **고성능** - Azure에서/Azure로의 고성능 전송이 가능합니다. 
- **빠른 액세스** - 온-프레미스 파일에 빠르게 액세스할 수 있도록 최신 파일을 캐시합니다.  
- **대역폭 사용량 제한** - 사용량이 많은 업무 시간 동안 사용량을 제한하도록 네트워크가 제한될 때도 Azure에 데이터를 쓸 수 있습니다.  
- **데이터 변환** - Azure로 이동하는 데이터를 분석, 처리 또는 필터링할 수 있습니다.

## <a name="key-capabilities"></a>주요 기능

Data Box Edge의 기능은 다음과 같습니다.

|기능 |설명  |
|---------|---------|
|고성능     | 고도로 최적화된 완전 자동 데이터 전송 기능과 대역폭이 제공됩니다.|
|지원되는 프로토콜     | 데이터 수집을 위한 표준 SMB 및 NFS 프로토콜이 지원됩니다. <br> 지원되는 버전에 대한 자세한 내용을 확인하려면 [Data Box Edge 시스템 요구 사항](https://aka.ms/dbe-docs)을 방문하세요.|
|컴퓨팅       |데이터를 분석, 처리, 필터링할 수 있습니다.|
|데이터 액세스     | 클라우드 API를 사용하여 Azure Storage Blob 및 Azure Files에서 데이터에 직접 액세스해 클라우드에서 데이터를 추가로 처리할 수 있습니다.|
|빠른 액세스     | 가장 최근에 사용한 파일에 빠르게 액세스할 수 있도록 디바이스에 로컬 캐시가 생성됩니다.|
|오프라인 업로드     | 연결 끊김 모드에서도 오프라인 업로드 시나리오가 지원됩니다.|
|데이터 새로 고침     | 클라우드의 최신 데이터로 로컬 파일을 새로 고칠 수 있습니다.|
|암호화    | *https*를 통해 클라우드로 안전하게 데이터를 전송하고 로컬에서 데이터를 암호화할 수 있도록 BitLocker가 지원됩니다.       |
|복원력     | 네트워크 복원 기능이 기본적으로 제공됩니다.        |


## <a name="components"></a>구성 요소

Data Box Edge 솔루션은 Data Box Edge 리소스, Data Box Edge 물리적 디바이스 및 로컬 웹 UI로 구성되어 있습니다.

* **Data Box Edge 물리적 디바이스** - Azure로 데이터를 전송하도록 구성할 수 있는 Microsoft 제공 1U 랙 탑재 서버입니다. 
    
* **Data Box Edge 리소스** – 다양한 지리적 위치에서 액세스할 수 있는 웹 인터페이스에서 Data Box Edge 디바이스를 관리할 수 있는 Azure Portal의 리소스입니다. Data Box Edge 리소스를 사용하여 리소스를 작성/관리하고, 디바이스와 경고를 확인/관리하고, 공유를 관리합니다.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    자세한 내용은 [Data Box Edge 서비스를 사용하여 Data Box Edge 디바이스 관리](https://aka.ms/dbe-docs)로 이동하세요.

* **Data Box 로컬 웹 UI** - 로컬 웹 UI를 사용하여 진단을 실행하고, Data Box Edge 디바이스를 종료 및 다시 시작하고, 복사 로그를 보고, 서비스 요청을 위해 Microsoft 지원에 연락을 합니다.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    웹 기반 UI 사용에 대한 자세한 내용은 [웹 기반 UI를 사용하여 Data Box 관리](https://aka.ms/dbe-docs)로 이동하세요.


## <a name="region-availability"></a>지역 가용성

데이터를 전송하는 Data Box Edge 물리적 디바이스, Azure 리소스 및 대상 저장소 계정이 모두 같은 지역에 있지 않아도 됩니다.

- **리소스 가용성** - 이 릴리스의 경우 다음 지역에서 Data Box Edge 리소스를 사용할 수 있습니다.
    - **미국** - 미국 동부
    - **유럽 연합** - 유럽 서부
    - **아시아 태평양** - 동남 아시아
    
    Data Box Gateway는 Azure Government 클라우드에도 배포할 수 있습니다. 자세한 내용은 [Azure Government란?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)을 참조하세요.
    
- **대상 저장소 계정** - 데이터를 저장하는 저장소 계정은 모든 Azure 지역에서 사용할 수 있습니다. 

    성능을 최적화하려면 저장소 계정이 Data Box 데이터를 저장하는 지역이 디바이스를 사용하는 지역 근처여야 합니다. 저장소 계정의 지역과 디바이스의 지역 간 거리가 멀면 대기 시간이 길어지고 성능이 저하됩니다. 


## <a name="next-steps"></a>다음 단계

- [Data Box Edge 시스템 요구 사항](data-box-edge-system-requirements.md)을 검토합니다.
- [Data Box Edge 제한](data-box-edge-limits.md)을 알아봅니다.
- Azure Portal에서 [Azure Data Box Edge](data-box-edge-deploy-prep.md)를 배포합니다.




