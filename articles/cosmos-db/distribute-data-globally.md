---
title: Azure Cosmos DB로 데이터 글로벌 배포 | Microsoft Docs
description: 글로벌 분산형 다중 모델 데이터베이스 서비스인 Azure Cosmos DB에서 글로벌 데이터베이스를 사용한 전 세계적 지역 복제, 다중 마스터, 장애 조치(Failover) 및 데이터 복구에 대해 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238277"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용한 글로벌 데이터 분산

오늘날의 많은 응용 프로그램은 전 세계적인 규모로 실행되거나 전 세계 시장에 도달하려는 목표가 있습니다. 이러한 응용 프로그램은 항상 사용 가능하며 전 세계 사용자가 쉽게 접근할 수 있습니다. 고성능 및 고가용성을 제공하는 동시에 이러한 응용 프로그램에서 사용하는 데이터의 전역 분산을 관리하는 것은 힘든 작업입니다. Cosmos DB는 이러한 과제를 충족하기 위해 완벽하게 설계된 전역 분산형 데이터베이스 서비스입니다.

Cosmos DB는 기본 Azure 서비스이며, 기본적으로 모든 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 사용할 수 있습니다. Microsoft는 전 세계 50개 이상의 지역에서 Azure 데이터 센터를 운영하고 있으며, 고객의 증가하는 요구 사항을 충족하기 위해 계속해서 확장하고 있습니다. Microsoft에서 Cosmos DB 서비스를 연중무휴로 운영하므로, 사용자는 응용 프로그램에 집중할 수 있습니다. Cosmos DB 계정을 만들 때 계정을 배포할 지역을 결정합니다.

Cosmos DB 고객은 1개부터 50개 이상의 Azure 지역의 어디에서나 사용 가능한 전역 분산형 데이터베이스를 구성할 수 있습니다. 대기 시간을 줄이려면 사용자와 가까운 곳에 데이터를 배치해야 하므로, 얼마나 많은 지역에서 실행할지와 어떤 지역에서 실행할지는 응용 프로그램의 전 세계적 도달 범위 및 사용자 위치에 따라 결정됩니다. Cosmos DB는 Cosmos 계정 내의 모든 데이터를 구성된 모든 지역에 투명하게 복제합니다. Cosmos DB는 응용 프로그램이 로컬에서 읽고 쓸 수 있도록 Cosmos 데이터베이스 및 컨테이너의 단일 시스템 이미지를 제공합니다. Cosmos DB를 사용하면 언제든지 계정과 연결된 지역을 추가하거나 제거할 수 있습니다. 응용 프로그램을 일시 중지하거나 다시 배포할 필요가 없으며, 서비스에서 제공하는 멀티 호밍(multi-homing) 기능 덕분에 응용 프로그램이 항상 데이터를 제공하고 고가용성을 유지할 수 있습니다.

## <a name="key-benefits-of-global-distribution"></a>전역 분산의 주요 혜택

**전역 활성 - 활성 앱을 쉽게 빌드**: 다중 마스터 기능을 사용하면 모든 지역이 쓰기 가능(읽기 가능 포함)하므로 무제한 탄력적 쓰기 확장성, 전 세계에서 99.999% 읽기 및 쓰기 가용성, 99번째 백분위수로 10밀리초 미만에 제공되는 보장된 빠른 읽기 및 쓰기가 가능합니다.  

Azure Cosmos DB의 멀티 호밍(multi-homing) API를 사용하면 응용 프로그램이 항상 가장 가까운 지역을 인식하고 해당 지역으로 요청을 보냅니다. 가장 가까운 지역은 구성 변경 없이 식별됩니다. Cosmos DB 계정에서 지역을 추가하거나 제거할 때 응용 프로그램을 다시 배포할 필요가 없으며 고가용성을 계속 유지할 수 있습니다.

**응답성이 우수한 앱 빌드**: 데이터베이스에 대해 선택한 모든 지역에서 한 자리 밀리초 대기 시간으로 거의 실시간 읽기 및 쓰기를 수행하도록 응용 프로그램을 쉽게 설계할 수 있습니다.  Azure Cosmos DB는 Cosmos 계정에 대해 선택한 일관성 수준을 보장하는 방식으로 지역 간의 데이터 복제를 내부적으로 처리합니다.

많은 응용 프로그램은 다중 지역(로컬) 쓰기를 수행하는 기능에 수반되는 성능 향상 혜택을 얻게 됩니다. 강력한 일관성이 필요한 응용 프로그램과 같은 일부 응용 프로그램은 모든 쓰기를 단일 지역으로 유입하려고 합니다. Cosmos DB는 단일 지역 및 다중 지역의 두 구성을 모두 지원합니다.

**고가용성 앱 빌드**: 여러 지역에서 데이터베이스를 실행하면 데이터베이스 가용성이 향상됩니다. 한 지역을 사용할 수 없게 되면 다른 지역에서 응용 프로그램 요청을 자동으로 처리합니다. Azure Cosmos DB는 다중 지역 데이터베이스에 대해 99.999% 읽기 및 쓰기 가용성을 제공합니다.

**지역 가동 중단 시 비즈니스 연속성**: Azure Cosmos DB는 지역 가동 중단 시 [자동 장애 조치(failover)](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account)를 지원합니다. 또한 지역 가동 중단 시 Cosmos DB는 해당 대기 시간, 가용성, 일관성 및 처리량 SLA를 계속 유지합니다. 전체 앱의 고가용성을 유지할 수 있도록 Azure Cosmos DB는 지역 가동 중단을 시뮬레이션하는 수동 장애 조치(failover) API를 제공합니다. 이 API를 사용하여 정기적인 비즈니스 연속성 훈련을 수행하고 준비를 갖출 수 있습니다.

**글로벌 읽기 및 쓰기 확장성**: 다중 마스터 기능을 사용하면 전 세계의 읽기 및 쓰기 처리량을 탄력적으로 조정할 수 있습니다. 다중 마스터 기능은 응용 프로그램이 Azure Cosmos DB 데이터베이스 또는 컨테이너에 구성하는 처리량이 모든 지역에 제공되고 [재정적 지원을 받는 SLA](https://aka.ms/acdbsla)로 보호되도록 합니다.

**여러 개의 잘 정의된 일관성 모델**: Azure Cosmos DB의 복제 프로토콜은 각각 일관성과 성능이 절충된 5가지 잘 정의된, 실용적이고 직관적인 일관성 모델을 제공하도록 설계되었습니다. 이러한 일관성 모델을 사용하면 전역 분산형 응용 프로그램을 쉽게 빌드할 수 있습니다.

## <a id="Next Steps"></a>다음 단계

다음 문서에서 전역 분산에 대해 자세히 알아보세요.

* [전역 분산 - 내부 살펴보기](global-dist-under-the-hood.md)
* [클라이언트에서 멀티 호밍(multi-homing)을 구성하는 방법](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [데이터베이스에서 지역을 추가/제거하는 방법](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [SQL API 계정에 대한 사용자 지정 충돌 해결 정책을 만드는 방법](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)