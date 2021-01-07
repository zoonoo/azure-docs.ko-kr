---
title: Azure Virtual Network (VNET)에서 리소스에 Stream Analytics 작업 연결
description: 이 문서에서는 VNET에 있는 리소스와 Azure Stream Analytics 작업을 연결 하는 방법을 설명 합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: f140384ee2c78b38982c99dab5912bc451584a42
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898150"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Azure Virtual Network (VNet)에서 리소스에 Stream Analytics 작업 연결

Stream Analytics 작업은 입력에 대 한 아웃 바운드 연결을 설정 하 고 Azure 리소스를 출력 하 여 실시간으로 데이터를 처리 하 고 결과를 생성 합니다. 이러한 입력 및 출력 리소스 (예: Azure Event Hubs 및 Azure SQL Database)는 Azure 방화벽이 나 Azure Virtual Network (VNet) 뒤에 있을 수 있습니다. Stream Analytics 서비스는 네트워크 규칙에 직접 포함 될 수 없는 네트워크에서 작동 합니다.

그러나 이러한 시나리오에서 Stream Analytics 작업을 입력 및 출력 리소스에 안전 하 게 연결 하는 방법에는 두 가지가 있습니다.
* Stream Analytics 클러스터에서 전용 끝점 사용.
* ' 신뢰할 수 있는 서비스 허용 ' 네트워킹 설정과 함께 관리 되는 Id 인증 모드를 사용 합니다.

Stream Analytics 작업에서 인바운드 연결을 허용 하지 않습니다.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Stream Analytics 클러스터의 개인 끝점.
[Stream Analytics 클러스터](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) 는 Stream Analytics 작업을 실행할 수 있는 단일 테 넌 트 전용 계산 클러스터입니다. 클러스터에서 실행 중인 모든 작업에서 입력 및 출력 리소스에 대 한 보안 아웃 바운드 연결을 설정할 수 있도록 하는 Stream Analytics 클러스터에서 관리 되는 개인 끝점을 만들 수 있습니다.

Stream Analytics 클러스터에서 개인 끝점을 만드는 [작업은 두 단계로](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)진행 됩니다. 이 옵션은 Stream Analytics 클러스터의 최소 크기가 36 이기 때문에 중간 규모에서 큰 스트리밍 워크 로드에 가장 적합 합니다 (36 su는 다양 한 구독 또는 개발, 테스트, 프로덕션 등의 환경에서 다른 작업으로 공유할 수 있음).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>' 신뢰할 수 있는 서비스 허용 ' 구성을 사용 하는 관리 id 인증
일부 Azure 서비스는 **신뢰할 수 있는 Microsoft 서비스 네트워킹 허용** 설정을 제공 합니다 .이 설정을 사용 하도록 설정 하면 Stream Analytics 작업에서 강력한 인증을 사용 하 여 리소스에 안전 하 게 연결할 수 있습니다. 이 옵션을 사용 하면 Stream Analytics 클러스터와 개인 끝점을 요구 하지 않고 작업을 입력 및 출력 리소스에 연결할 수 있습니다. 이 기법을 사용 하도록 작업을 구성 하는 작업은 2 단계 작업입니다.
* Stream Analytics 작업에서 입력 또는 출력을 구성 하는 경우 관리 되는 Id 인증 모드를 사용 합니다.
* 작업의 시스템 할당 관리 id에 Azure 역할을 할당 하 여 특정 Stream Analytics 작업에 대상 리소스에 대 한 명시적 액세스를 부여 합니다. 

**신뢰할 수 있는 Microsoft 서비스 허용** 을 사용 하도록 설정 하면 모든 작업에 대 한 블 랭 킷 액세스 권한을 부여 하지 않습니다. 이를 통해 리소스에 안전 하 게 액세스할 수 있는 특정 Stream Analytics 작업을 완벽 하 게 제어할 수 있습니다. 

작업은이 방법을 사용 하 여 다음과 같은 Azure 서비스에 연결할 수 있습니다.
1. [Blob Storage 또는 Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) -작업의 저장소 계정, 스트리밍 입력 또는 출력 일 수 있습니다.
2. [Azure Event Hubs](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) -작업의 스트리밍 입력 또는 출력 일 수 있습니다.

작업에서 다른 입력 또는 출력 형식에 연결 해야 하는 경우에는 Stream Analytics에서 먼저 Event Hubs 출력으로 작성 한 다음 Azure Functions를 사용 하 여 원하는 대상에 수 있습니다. Stream Analytics에서 VNet 또는 방화벽으로 보호 되는 다른 출력 형식으로 직접 쓰려면 Stream Analytics 클러스터에서 전용 끝점을 사용 하는 것이 유일한 옵션입니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics 클러스터에서 개인 끝점 만들기 및 제거](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [관리 되는 Id 인증을 사용 하 여 VNet의 Event Hubs에 연결](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [관리 되는 Id 인증을 사용 하 여 VNet에서 Blob storage 및 ADLS Gen2에 연결](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
