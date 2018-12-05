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
ms.openlocfilehash: 181a8ad7291a8e8a0aa2a8373985c8747bd4569b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446826"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용한 글로벌 데이터 분산

현대의 애플리케이션은 응답성이 뛰어나고 항상 온라인 상태여야 합니다. 짧은 대기 시간 및 고가용성을 이루려면 이러한 애플리케이션의 인스턴스를 해당 사용자에서 가까운 데이터 센터에 배포해야 합니다. 이러한 애플리케이션은 일반적으로 여러 데이터 센터에 배포되며 글로벌 분산형이라고 합니다. 글로벌 분산형 애플리케이션에는 해당 사용자와 가까이 있는 데이터의 복사본에서 해당 애플리케이션이 작동하도록 설정하려면 전 세계 어디서나 데이터를 투명하게 복제할 수 있는 글로벌 분산형 데이터베이스가 있어야 합니다. Azure Cosmos DB는 전역적으로 분산되는 데이터베이스 서비스로 짧은 대기 시간, 탄력적인 처리량 확장성, 데이터 일관성에 대해 잘 정의된 의미 체계 및 고가용성을 제공하도록 설계되었습니다. 즉, 전 세계 어디서나 애플리케이션의 빠른 응답 시간을 보장하고, 애플리케이션을 항상 온라인으로 유지하고 처리량 및 스토리지를 제한 없이 탄력적으로 확장할 수 있는 확장성을 제공하려면 Azure Cosmos DB를 사용하여 애플리케이션을 빌드하는 방안을 고려해 야 합니다.

Azure Cosmos DB는 필수 Azure 서비스이며, 기본적으로 모든 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 사용할 수 있습니다. Microsoft는 전 세계 54개 이상의 지역에서 Azure 데이터 센터를 운영하고 있으며, 고객의 증가하는 요구 사항을 충족하기 위해 지역을 계속 확장하고 있습니다. Azure Cosmos 계정을 만들 때 배포할 지역을 결정합니다. Microsoft에서 Azure Cosmos DB 서비스를 연중무휴 운영하므로 고객은 애플리케이션에 집중할 수 있습니다.

고객은 데이터베이스가 전역적으로 분산되고 모든 Azure 지역에서 사용 가능하도록 구성할 수 있습니다. 대기 시간을 줄이려면 데이터를 사용자와 가깝게 배치하면 됩니다. 필수 지역을 선택하는 작업은 애플리케이션의 글로벌 도달률 및 사용자가 있는 위치에 따라 달라집니다. Azure Cosmos DB는 계정 내의 데이터를 계정과 연결된 모든 지역에 투명하게 복제합니다. 또한 사용자 애플리케이션이 로컬로 읽고 쓸 수 있는 글로벌 분산형 Azure Cosmos 데이터베이스 및 컨테이너의 단일 시스템 이미지를 제공합니다. Azure Cosmos DB를 사용하면 언제든지 계정과 연결된 지역을 추가하거나 제거할 수 있습니다. 애플리케이션이 지역을 추가하거나 제거하기 위해 일시 중지되거나 재배포될 필요가 없습니다. 서비스를 제공하는 멀티 호밍 기능으로 인해 애플리케이션은 계속해서 항상 사용 가능합니다.

## <a name="key-benefits-of-global-distribution"></a>전역 분산의 주요 혜택

**글로벌 활성-활성 앱 빌드**: 다중 마스터 기능을 사용하면 모든 지역은 쓰기 지역입니다(읽기도 가능). 또한 다중 마스터 기능은 전 세계에서 99.999%의 읽기 및 쓰기 가용성을 제공하는 무제한 탄력적 읽기 확장성을 보장하고, 백분위 99번째에서 10밀리초 미만으로 제공된 읽기/쓰기를 보장합니다.  

Azure Cosmos DB의 멀티 호밍(multi-homing) API를 사용하여 애플리케이션이 가장 가까운 지역을 인식하고 해당 지역으로 요청을 보낼 수 있습니다. 가장 가까운 지역은 구성 변경 없이 식별됩니다. Azure Cosmos DB 계정에서 지역을 추가하거나 제거할 때 애플리케이션을 다시 배포할 필요가 없으며 고가용성을 계속 유지할 수 있습니다.

**응답성이 우수한 앱 빌드**: 데이터베이스에 대해 선택한 모든 지역에서 한 자리 밀리초 대기 시간으로 근실시간 읽기 및 쓰기를 수행하도록 쉽게 애플리케이션을 설계할 수 있습니다.  Azure Cosmos DB는 Azure Cosmos 계정에 대해 선택한 일관성 수준을 보장하는 등 지역 간의 데이터 복제를 내부적으로 처리합니다.

많은 응용 프로그램은 다중 지역(로컬) 쓰기를 수행하는 기능에 수반되는 성능 향상 혜택을 얻게 됩니다. 강력한 일관성이 필요한 일부 애플리케이션은 모든 쓰기를 단일 지역으로 유입하려고 합니다. 이러한 애플리케이션을 지원하기 위해 Azure Cosmos DB는 단일 지역 및 다중 지역 구성을 모두 지원합니다.

**고가용성 앱 빌드**: 여러 지역에서 데이터베이스를 실행하면 데이터베이스 가용성이 증가됩니다. 한 지역을 사용할 수 없게 되면 다른 지역에서 응용 프로그램 요청을 자동으로 처리합니다. Azure Cosmos DB는 다중 지역 데이터베이스에 대해 99.999% 읽기 및 쓰기 가용성을 제공합니다.

**지역 가동 중단 시 비즈니스 연속성**: Azure Cosmos DB는 지역 가동 중단 시 [자동 장애 조치(failover)](how-to-manage-database-account.md#automatic-failover)를 지원합니다. 또한 지역 가동 중단 시 Azure Cosmos DB는 해당 대기 시간, 가용성, 일관성 및 처리량 SLA를 계속 유지합니다. 전체 애플리케이션의 고가용성을 유지할 수 있도록 Azure Cosmos DB는 지역 가동 중단을 시뮬레이션하는 수동 장애 조치(failover) API를 제공합니다. 이 API를 사용하여 정기적인 비즈니스 연속성 훈련을 수행할 수 있습니다.

**글로벌 읽기 및 쓰기 확장성**: 다중 마스터 기능을 사용하면 전 세계의 읽기 및 쓰기 처리량을 탄력적으로 조정할 수 있습니다. 다중 마스터 기능은 응용 프로그램이 Azure Cosmos DB 데이터베이스 또는 컨테이너에 구성하는 처리량이 모든 지역에 제공되고 [재정적 지원을 받는 SLA](https://aka.ms/acdbsla)로 보호되도록 합니다.

**여러 개의 잘 정의된 일관성 모델**: Azure Cosmos DB의 복제 프로토콜은 5가지 잘 정의된 실용적이고 직관적인 일관성 모델을 제공하도록 설계되었습니다. 각 일관성 모델에서는 일관성과 성능이 절충되었습니다. 이러한 일관성 모델을 사용하면 전역적으로 분산된 애플리케이션을 쉽게 빌드할 수 있습니다.

## <a id="Next Steps"></a>다음 단계

다음 문서에서 전역 분산에 대해 자세히 알아보세요.

* [전역 분산 - 내부 살펴보기](global-dist-under-the-hood.md)
* [클라이언트에서 멀티 호밍(multi-homing)을 구성하는 방법](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Azure Cosmos 계정에서 지역을 추가/제거하는 방법](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [SQL API 계정에 대한 사용자 지정 충돌 해결 정책을 만드는 방법](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)