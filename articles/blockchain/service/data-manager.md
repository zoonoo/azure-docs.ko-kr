---
title: Azure Blockchain 서비스의 Blockchain Data Manager 이란?
description: Blockchain은 블록 체인 데이터를 캡처, 변환 및 제공 하는 Data Manager Event Grid 항목으로 보냅니다.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: e5fc38767d6127e341e257c23411d23b739d0362
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518243"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Azure Blockchain 서비스에 대 한 Blockchain Data Manager 이란?

Blockchain은 azure Blockchain 서비스 트랜잭션 데이터를 캡처, 변환 및 제공 하 여 Azure 서비스와 안정적이 고 확장 가능한 블록 체인 원장 통합을 제공 하는 Azure Event Grid 항목으로 제공 Data Manager 합니다.

대부분의 엔터프라이즈 블록 체인 시나리오에서 blockchain 원장은 솔루션의 한 부분입니다. 예를 들어 한 엔터티에서 다른 엔터티로 자산을 전송 하려면 트랜잭션을 전송 하기 위한 메커니즘이 필요 합니다. 그런 다음, 트랜잭션이 발생 하 고 수락 되었는지 확인 하기 위해 원장 데이터를 읽는 메커니즘이 필요 하 고, 결과 상태 변경이 종단 간 솔루션에 통합 됩니다. 이 예제에서는 자산을 전송 하기 위해 스마트 계약을 작성 하는 경우 블록 체인 Data Manager를 사용 하 여 외부 연결 응용 프로그램 및 데이터 저장소를 통합할 수 있습니다. 자산 전송 예제의 경우 blockchain에서 자산이 전송 될 때 이벤트 및 속성 상태 변경은 Event Grid를 통해 Blockchain Data Manager에 의해 전달 됩니다. 그런 다음 Event Grid에 대해 여러 이벤트 처리기를 사용 하 여 블록 체인 데이터를 오프 체인으로 저장 하거나 실시간으로 상태 변경에 대응할 수 있습니다.

Blockchain Data Manager는 캡처, 변환 및 배달의 세 가지 주요 기능을 수행 합니다.

![Blockchain Data Manager 함수](./media/data-manager/functions.png)

## <a name="capture"></a>캡처

각 Blockchain Data Manager 인스턴스는 하나의 Azure Blockchain 서비스 구성원 트랜잭션 노드에 연결 합니다. 트랜잭션 노드에 대 한 액세스 권한이 있는 사용자만 고객 데이터에 대 한 적절 한 액세스 제어를 보장 하는 연결을 만들 수 있습니다. Blockchain Data Manager 인스턴스는 트랜잭션 노드에서 모든 원시 블록 및 원시 트랜잭션 데이터를 안정적으로 캡처하고 엔터프라이즈 워크 로드를 지원 하도록 확장할 수 있습니다.

## <a name="transform"></a>변환

Blockchain Data Manager를 사용 하 여 Blockchain Data Manager 내에서 스마트 계약 응용 프로그램을 구성 하 여 이벤트 및 속성 상태를 디코딩할 수 있습니다. 스마트 계약을 추가 하려면 ABI 및 바이트 코드를 제공 합니다. Blockchain Data Manager는 스마트 계약 아티팩트를 사용 하 여 계약 주소를 디코딩하고 검색 합니다. 블록 체인 응용 프로그램을 인스턴스에 추가 하 고 나면 스마트 계약이 컨소시엄에 배포 되 고 디코딩된 이벤트와 속성 상태를 구성 된 대상으로 보낼 때 Blockchain Data Manager 스마트 계약 주소를 동적으로 검색 합니다.

## <a name="deliver"></a>제공

Blockchain Data Manager는 지정 된 Blockchain Data Manager 인스턴스에 대해 여러 Event Grid 토픽 아웃 바운드 연결을 지원 합니다. 블록 체인 데이터를 단일 대상으로 전송 하거나 블록 체인 데이터를 여러 대상으로 보낼 수 있습니다. Blockchain Data Manager를 사용 하 여 블록 체인 배포를 위한 확장 가능한 이벤트 기반 데이터 게시 솔루션을 빌드할 수 있습니다.

## <a name="configuration-options"></a>구성 옵션

솔루션 요구 사항에 맞게 Blockchain Data Manager를 구성할 수 있습니다. 예를 들어 다음을 프로 비전 할 수 있습니다.

* Azure Blockchain 서비스 구성원에 대 한 단일 Blockchain Data Manager 인스턴스입니다.
* 블록 체인은 Azure Blockchain 서비스 트랜잭션 노드당 인스턴스를 Data Manager 합니다. 예를 들어 개인 트랜잭션 노드는 기밀성을 유지 하기 위해 자체 Blockchain Data Manager 인스턴스를 사용할 수 있습니다.
* Blockchain Data Manager 인스턴스는 여러 출력 연결을 지원할 수 있습니다. 단일 Blockchain Data Manager 인스턴스를 사용 하 여 Azure Blockchain 서비스 구성원에 대 한 모든 데이터 게시 통합 요소를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Blockchain 서비스 구성원에 대 한 [blockchain Data Manager 인스턴스를 만들어](data-manager-portal.md) 보세요.
