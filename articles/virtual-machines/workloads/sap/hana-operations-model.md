---
title: Azure(대규모 인스턴스)에서 SAP HANA의 작업 모델 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA의 작업 모델입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a648e2d46cce96a8ff663f45ccf45326898a84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477892"
---
# <a name="operations-model-and-responsibilities"></a>작업 모델 및 책임

Azure(큰 인스턴스)에서 SAP HANA가 제공하는 서비스는 Azure IaaS 서비스에 맞춰 조정됩니다. SAP HANA에 최적화된 운영 체제가 설치된 HANA 대규모 인스턴스를 가져옵니다. Azure IaaS VM과 마찬가지로 OS를 강화하고, 필요한 추가 소프트웨어와 HANA를 설치하며, OS와 HANA를 운영하고 업데이트하는 대부분의 작업은 사용자의 책임입니다. Microsoft는 사용자에게 OS 업데이트 또는 HANA 업데이트를 강제로 요구하지 않습니다.

![Azure(큰 인스턴스)에서 SAP HANA의 책임](./media/hana-overview-architecture/image2-responsibilities.png)

다이어그램에서 보여 주듯이 SAP HANA on Azure(대규모 인스턴스)는 다중 테넌트 IaaS 제품입니다. 대부분의 경우 책임 분담은 OS 인프라 경계에 있습니다. Microsoft는 운영 체제 선 아래에서 서비스의 모든 측면을 담당합니다. 사용자는 선 위에 있는 서비스의 모든 측면을 담당합니다. OS는 사용자의 책임입니다. 규정 준수, 보안, 애플리케이션 관리, 기본 요소 및 OS 관리를 위해 채택할 수 있는 최신 온-프레미스 방법을 계속 사용할 수 있습니다. 시스템은 모든 측면의 네트워크에 있는 것처럼 표시됩니다.

이 서비스는 SAP HANA에 최적화되어 있으므로 기본 인프라 기능을 사용하여 최상의 결과를 얻기 위해 Microsoft와 협력해야 하는 영역이 있습니다.

다음 목록에는 각 레이어와 책임에 대한 자세한 정보를 제공합니다.

**네트워킹**: SAP HANA를 실행하는 대규모 인스턴스 스탬프에 대한 모든 내부 네트워크입니다. 저장소에 대한 액세스, 인스턴스 간 연결(규모 확장 및 기타 기능), 환경에 대한 연결, VM에서 SAP 애플리케이션 계층이 호스팅되는 Azure에 대한 연결은 사용자의 책임입니다. 또한 재해 복구용 복제를 위한 Azure 데이터 센서 간 WAN 연결도 포함됩니다. 모든 네트워크는 테넌트에서 분할되며 QoS(서비스 품질)를 적용합니다.

**저장소**: SAP HANA 서버에 필요한 모든 볼륨 및 스냅샷을 위한 가상화되고 분할된 스토리지입니다. 

**서버**: 테넌트에 할당된 SAP HANA DB를 실행할 전용 물리적 서버입니다. SKU의 Type I 클래스 서버는 추상화된 하드웨어입니다. 이런 유형의 서버에서는 서버 구성이 프로필에 수집되고 유지 관리되며, 이런 프로필은 하나의 실제 하드웨어에서 다른 실제 하드웨어에 이동할 수 있습니다. 작업에 의한 이러한 프로필 이동(수동)은 Azure 서비스 복구와 약간 비교될 수 있습니다. 유형 II 클래스 SKU 서버는 이러한 기능을 제공하지 않습니다.

**SDDC**: 데이터 센터를 소프트웨어 정의 엔터티로 관리하는 데 사용되는 관리 소프트웨어입니다. Microsoft를 확장성, 가용성 및 성능상의 이유로 리소스를 풀(pool)할 수 있습니다.

**O/S**: 서버에서 실행되는 OS로 사용자가 선택(SUSE Linux 또는 Red Hat Linux)합니다. 함께 제공되는 OS 이미지는 SAP HANA를 실행하기 위해 개별 Linux 공급업체에서 Microsoft에 제공한 것입니다. SAP HANA에 최적화된 특정 이미지에 대해서는 Linux 공급업체에서 제공한 구독이 있어야 합니다. OS 공급업체에 이미지를 등록하는 것은 사용자의 책임입니다. 

Microsoft에서 인계하는 시점부터 Linux 운영 체제의 추가 패치를 수행해야 합니다. 이 패치에는 성공적인 SAP HANA 설치에 필요할 수 있고 특정 Linux 공급업체에서 해당 SAP HANA에 최적화된 OS 이미지에 포함하지 않은 추가 패키지가 포함되어 있습니다. 자세한 내용은 SAP의 HANA 설치 설명서 및 SAP Note를 참조하세요. 

특정 서버 하드웨어와 관련하여 OS 및 해당 드라이버의 오작동 또는 최적화로 인해 OS 패치를 수행해야 합니다. OS의 보안 또는 기능 패치도 수행해야 합니다. 

또한 다음과 같은 모니터링 및 용량 계획에도 책임이 있습니다.

- CPU 리소스 사용량
- 메모리 사용량
- 사용 가능한 공간, IOPS 및 대기 시간과 관련된 디스크 볼륨
- HANA 대규모 인스턴스 및 SAP 애플리케이션 계층 간의 네트워크 볼륨 트래픽

HANA 대규모 인스턴스의 기본 인프라는 OS 볼륨의 백업 및 복원에 대한 기능을 제공합니다. 이 기능을 사용하는 것도 사용자의 책임입니다.

**미들웨어**: 주로 SAP HANA 인스턴스입니다. 관리, 작업 및 모니터링은 사용자의 책임입니다. 제공된 기능을 사용하여 백업/복원 및 재해 복구 용도로 저장소 스냅샷을 사용할 수 있습니다. 이러한 기능은 인프라에 의해 제공됩니다. 이러한 기능을 사용하여 고가용성 또는 재해 복구를 설계하고, 이를 활용하며, 모니터링하여 저장소 스냅샷이 성공적으로 실행되었는지 여부를 확인해야 합니다.

**Data**: SAP HANA에서 관리하는 데이터와 볼륨 또는 파일 공유에 있는 백업 파일과 같은 기타 데이터입니다. 사용 가능한 디스크 공간 모니터링 및 볼륨의 콘텐츠 관리는 사용자의 책임입니다. 또한 디스크 볼륨 및 저장소 스냅샷의 성공적인 백업 실행도 모니터링해야 합니다. 재해 복구 사이트에 대한 데이터 복제를 성공적으로 실행하는 것은 Microsoft의 책임입니다.

**애플리케이션:** SAP 애플리케이션 인스턴스이거나, 비 SAP 애플리케이션의 경우 해당 애플리케이션의 애플리케이션 레이어입니다. 해당 애플리케이션의 배포, 관리, 운영 및 모니터링은 사용자의 책임입니다. 가상 네트워크 내에서 CPU 리소스 사용, 메모리 사용, Azure Storage 사용 및 네트워크 대역폭 사용에 대한 용량 계획을 담당합니다. 또한 가상 네트워크에서 SAP HANA on Azure(대규모 인스턴스)에 이르는 리소스 사용에 대한 용량 계획도 담당합니다.

**WAN**: 온-프레미스에서 워크로드용 Azure 배포로 설정하는 연결입니다. HANA 대규모 인스턴스를 사용하는 모든 고객은 Azure ExpressRoute를 사용하여 연결합니다. 이 연결은 SAP HANA on Azure(대규모 인스턴스) 솔루션에 포함되지 않습니다. 이 연결 설정은 사용자의 책임입니다.

**보관**: 스토리지 계정에서 사용자 고유의 방법을 사용하여 데이터 복사본을 보관할 수 있습니다. 보관하려면 관리, 규정 준수, 비용 및 작업이 필요합니다. Azure에서 보관 복사본과 백업을 생성하고 호환되는 방식으로 저장해야 합니다.

[Azure(큰 인스턴스)의 SAP HANA에 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)를 참조하세요.

**다음 단계**
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](hana-architecture.md) 참조
