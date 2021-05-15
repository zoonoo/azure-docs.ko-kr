---
title: Azure VMware Solution by CloudSimple - 개요
description: CloudSimple 서비스에서 제공하는 Azure VMware Solution의 기능, 시나리오, 기능에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7b5017cdeade14e53778b58154456412507c5ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024877"
---
# <a name="what-is-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple이란?

**Azure VMware Solution by CloudSimple** 은 Azure에서 VMware 플랫폼을 실행할 권한을 부여하는 완전 관리형 서비스입니다. 솔루션에는 vSphere, vCenter, vSAN, NSX-T와 해당 도구가 포함됩니다. VMware 환경은 Azure 클라우드 위치의 Azure 운영 체제 미설치 인프라에서 기본적으로 실행됩니다. 서비스에는 VMware 플랫폼을 효율적이고 안전하게 사용하는 데 필요한 모든 기능이 포함됩니다.

![Azure VMware Solution by CloudSimple 개요](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>기능

* VMware 클라우드 환경의 주문형 셀프 서비스 프로비저닝. 요청에 따라 용량을 추가하고 제거하는 기능.
* VMware 플랫폼 배포, 업그레이드, 관리 평면 백업, 상태/용량 모니터링, 경고, 문제 해결 및 수정.
* 기본 네트워킹 서비스는 L2/L3 서비스 및 방화벽 규칙 관리를 포함하여 VMware를 사용하도록 설정하는 데 필요합니다.
* VPN, 공용 IP, 인터넷 게이트웨이를 포함한 가장자리 유형 네트워킹 서비스. 이러한 서비스는 Azure에서 실행되며 Azure의 보안 및 DDoS 보호를 수행합니다.
* 비용을 절감하기 위한 용량 예약.
* Azure 및 온-프레미스에 대한 고속 및 낮은 대기 시간 연결.
* 고객이 통합된 방식으로 Azure 서비스를 사용하고 고유한 “퍼블릭 클라우드의 VMware 클라우드” 아키텍처를 활용하는 솔루션 아키텍처. Azure 서비스에는 Azure AD, 스토리지, 애플리케이션 게이트웨이 등이 포함됩니다.
* 사용자 전용으로 다른 고객의 인프라와 물리적으로 격리된 인프라입니다.
* 작업 관리, 사용량, 청구/계량, 사용자 관리 등의 관리 기능.
* 연중 무휴 고객 지원.

## <a name="benefits"></a>이점

* **운영 연속성**. CloudSimple은 VMware 플랫폼에 대한 기본 액세스를 제공합니다. CloudSimple 아키텍처는 다음 기존 항목과 호환됩니다.
    * 애플리케이션
    * 작업
    * 보안
    * Backup
    * 재해 복구
    * 감사
    * 규정 준수 도구
    * 프로세스
* **재학습하지 않음**. VMware 플랫폼 호환성을 사용하면 기존 기술과 지식을 사용할 수 있습니다.
* **인프라 민첩성**. 더 이상 모든 용량 요구를 예측하지 않아도 되며 용량이 낭비되거나 인프라가 부족해지지 않습니다. CloudSimple은 클라우드 서비스로 제공되므로 언제든지 용량을 추가하거나 줄일 수 있습니다.
* **보안**. Azure를 통해 CloudSimple 환경에 액세스하면 기본 제공 DDoS 보호 및 보안 모니터링이 제공됩니다.
* **비용 절감**. CloudSimple 플랫폼은 고도로 엔지니어링되어 높은 수준의 자동화, 운영 효율성과 규모의 경제를 제공합니다. 또한 CloudSimple은 퍼블릭 클라우드에서 VMware의 존재를 활용하여 비용을 절감하는 솔루션 아키텍처를 게시합니다. Azure AD, Azure 스토리지에 백업, 애플리케이션 게이트웨이, 부하 분산 디바이스 등을 예로 들 수 있습니다.
* **새 하이브리드 플랫폼**. 해당 서비스는 Azure의 나머지 기능에 대한 고속 및 낮은 대기 시간 액세스를 사용할 수 있도록 합니다. 또한 CloudSimple 관리를 사용하면 동일한 UI 및 API를 사용하여 VMware 가상 머신 및 Azure의 나머지 기능을 통합적으로 관리할 수 있습니다. 개발 팀은 통합되고 일관된 방식으로 퍼블릭 및 프라이빗 플랫폼을 활용할 수 있습니다.
* **인프라 모니터링, 문제 해결, 지원**. CloudSimple은 기본 IaaS(Infrastructure as a Service)를 운영합니다. 오류가 발생한 하드웨어는 자동으로 대체됩니다. CloudSimple이 원활한 환경을 실행하는 동안 사용에 집중할 수 있습니다.
* **정책 호환성**. VMware 기반 도구, 보안 절차, 감사 사례, 규정 준수 인증을 유지하세요.

## <a name="scenarios"></a>시나리오

* **데이터 센터 사용 중지 또는 마이그레이션**. 기존 데이터 센터의 제한에 도달하거나 하드웨어를 새로 고칠 때 추가 용량을 확보하세요. 클라우드에서 필요한 용량을 간편하게 추가하고 하드웨어 새로 고침 관리 문제에서 자유로워질 수 있습니다. 시간이 많이 걸리는 변환 또는 재설계와 비교하여 클라우드 마이그레이션의 위험 및 비용을 줄입니다. 친숙한 VMware 도구와 기술을 사용하여 클라우드 마이그레이션을 가속화하세요. 클라우드에서 Azure 서비스를 사용하여 원하는 속도로 애플리케이션을 현대화합니다.
* **주문형 확장**. 클라우드 확장을 통해 새로운 개발 환경 또는 계절별 용량 급증과 같은 예기치 않은 요구 사항을 충족합니다. 원하는 대로 새 용량을 간편하게 만들고 필요한 만큼만 유지할 수 있습니다. 온-프레미스와 클라우드 모두에서 동일한 아키텍처 및 정책을 사용하여 선불 투자를 줄이고 프로비저닝 속도를 가속화하고 복잡성을 줄입니다.
* **Azure 클라우드의 재해 복구 및 가상 데스크톱**. Azure 클라우드에서 데이터, 앱, 데스크톱에 대한 원격 액세스를 설정합니다. 고대역폭 연결을 통해 데이터를 신속하게 업로드하거나 다운로드하여 인시던트를 복구합니다. 대기 시간이 낮은 네트워크는 사용자가 데스크톱 앱에 요구하는 빠른 응답 시간을 제공합니다. CloudSimple을 사용하면 CloudSimple 포털 및 친숙한 VMware 도구를 사용하여 클라우드의 모든 정책과 네트워킹을 간편하게 복제할 수 있습니다. 복구 및 복제의 간편함은 DR 및 VDI 구현을 만들고 관리하는 작업 및 위험을 크게 줄입니다.
* **고성능 애플리케이션 및 데이터베이스**. CloudSimple은 가장 까다로운 VMware 워크로드를 실행하도록 설계된 하이퍼컨버지드 아키텍처를 제공합니다. Oracle, Microsoft SQL Server, 미들웨어 시스템, 고성능의 비 SQL 데이터베이스를 실행합니다. 성능 저하 없이 온-프레미스, Azure의 VMware, Azure 프라이빗 워크로드에 걸친 하이브리드 앱을 실행할 수 있도록 하는 고속의 25Gbps 네트워크 연결을 사용하여 클라우드를 자체 데이터 센터로 경험해 보세요.
* **진정한 하이브리드**. VMware 및 Azure에서 DevOps를 통합합니다. 모든 워크로드에 적용할 수 있는 Azure 서비스 및 솔루션에 대한 VMware 관리를 최적화합니다. 데이터 센터를 확장하거나 애플리케이션을 다시 설계하지 않으면서도 퍼블릭 클라우드 서비스에 액세스합니다. Azure의 VMware 애플리케이션에 대한 ID 액세스 제어 정책, 로깅 및 모니터링을 중앙 집중화합니다.

![시나리오](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>다음 단계

* [CloudSimple 서비스 만들기](quickstart-create-cloudsimple-service.md)
* [프라이빗 클라우드 만들기](quickstart-create-private-cloud.md)
