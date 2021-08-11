---
title: Azure VNET(Virtual Network)의 리소스에 Stream Analytics 작업 연결
description: 이 문서에서는 VNET에 있는 리소스와 Azure Stream Analytics 작업을 연결하는 방법을 설명합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878242"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Azure VNet(Virtual Network)의 리소스에 Stream Analytics 작업 연결

Stream Analytics 작업은 입력에 대해 아웃바운드 연결을 만들고 Azure 리소스를 출력하여 실시간으로 데이터를 처리하고 결과를 생성합니다. 이러한 입력 및 출력 리소스(예: Azure Event Hubs 및 Azure SQL Database)는 Azure 방화벽 뒤에 있거나 Azure VNet(Virtual Network)에 있을 수 있습니다. Stream Analytics 서비스는 네트워크 규칙에 직접 포함될 수 없는 네트워크에서 작동합니다.

그러나 이러한 시나리오에서는 Stream Analytics 작업을 입력 및 출력 리소스에 안전하게 연결하는 두 가지 방법이 있습니다.
* Stream Analytics 클러스터에서 프라이빗 엔드포인트 사용
* '신뢰할 수 있는 서비스 허용' 네트워킹 설정과 함께 관리 ID 인증 모드 사용

Stream Analytics 작업은 인바운드 연결을 허용하지 않습니다.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Stream Analytics 클러스터의 프라이빗 엔드포인트
[Stream Analytics 클러스터](./cluster-overview.md)는 Stream Analytics 작업을 실행할 수 있는 단일 테넌트 전용 컴퓨팅 클러스터입니다. Stream Analytics 클러스터에서 관리형 프라이빗 엔드포인트를 만들 수 있습니다. 그러면 클러스터에서 실행되는 모든 작업이 입력 및 출력 리소스에 대한 안전한 아웃바운드 연결을 만들 수 있습니다.

Stream Analytics 클러스터에서 프라이빗 엔드포인트를 만드는 작업은 [2단계 작업](./private-endpoints.md)으로 진행됩니다. 이 옵션은 Stream Analytics 클러스터의 최소 크기가 36개의 SU이기 때문에 중대형 스트리밍 워크로드에 가장 적합합니다(개발, 테스트 및 프로덕션과 같은 다양한 구독 또는 환경의 여러 다른 작업에서 36개의 SU를 공유할 수 있음).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>'신뢰할 수 있는 서비스 허용' 구성을 사용하는 관리 ID 인증
일부 Azure 서비스는 **신뢰할 수 있는 Microsoft 서비스 허용** 네트워킹 설정을 제공합니다. 이 설정을 사용하면 강력한 인증을 사용하여 Stream Analytics 작업을 리소스에 안전하게 연결할 수 있습니다. 이 옵션을 사용하면 Stream Analytics 클러스터 및 프라이빗 엔드포인트를 요구하지 않고도 작업을 입력 및 출력 리소스에 연결할 수 있습니다. 이 기술을 사용하도록 작업을 구성하는 과정은 2단계 작업입니다.
* Stream Analytics 작업에서 입력 또는 출력을 구성할 때 관리 ID 인증 모드를 사용합니다.
* 작업의 시스템 할당 관리 ID에 Azure 역할을 할당하여 특정 Stream Analytics 작업에 대상 리소스에 대한 명시적 액세스 권한을 부여합니다. 

**신뢰할 수 있는 Microsoft 서비스 허용** 을 사용하도록 설정해도 작업에 대한 포괄적인 액세스 권한이 부여되지 않습니다. 이렇게 하면 리소스에 안전하게 액세스할 수 있는 특정 Stream Analytics 작업을 완전히 제어할 수 있습니다. 

사용자의 작업은 이 기술을 사용하여 다음 Azure 서비스에 연결할 수 있습니다.
1. [Blob Storage 또는 Azure Data Lake Storage Gen2](./blob-output-managed-identity.md) - 작업의 스토리지 계정, 스트리밍 입력 또는 출력일 수 있습니다.
2. [Azure Event Hubs](./event-hubs-managed-identity.md) - 작업의 스트리밍 입력 또는 출력일 수 있습니다.

작업이 다른 입력 또는 출력 형식에 연결해야 하는 경우 먼저 Stream Analytics에서 Event Hubs로 출력을 쓴 다음, Azure Functions를 사용하여 원하는 대상에 쓸 수 있습니다. Stream Analytics에서 VNet 또는 방화벽으로 보호되는 다른 출력 형식으로 직접 쓰려는 경우 유일한 옵션은 Stream Analytics 클러스터에서 프라이빗 엔드포인트를 사용하는 것입니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics 클러스터에서 프라이빗 엔드포인트 만들기 및 제거](./private-endpoints.md)
* [관리 ID 인증을 사용하여 VNet의 Event Hubs에 연결](./event-hubs-managed-identity.md)
* [관리 ID 인증을 사용하여 VNet에서 Blob Storage 및 ADLS Gen2에 연결](./blob-output-managed-identity.md)