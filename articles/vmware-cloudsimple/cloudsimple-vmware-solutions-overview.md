---
title: CloudSimple의 Azure VMware 솔루션-개요
description: CloudSimple 서비스에서 제공 하는 Azure의 기능, 시나리오 및 기능에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 01fd132b3e43d15c5f2eee0114ef09dbac6df2a6
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812462"
---
# <a name="what-is-vmware-solution-on-azure-by-cloudsimple"></a>CloudSimple의 Azure에 대 한 VMware 솔루션 이란?

Azure에서 **CloudSimple에의 한 Vmware 솔루션** 은 Azure에서 vmware 플랫폼을 실행할 수 있는 완전히 관리 되는 서비스입니다. 이 솔루션은 vSphere, vCenter, Vsphere, NSX 및 해당 도구를 포함 합니다.
VMware 환경은 azure 클라우드 위치에서 Azure 운영 체제 미 설치 인프라에 대해 기본적으로 실행 됩니다. 서비스는 VMware 플랫폼을 효율적이 고 안전 하 게 사용 하는 데 필요한 모든 기능을 포함 합니다.

![CloudSimple의 Azure에 대 한 VMware 솔루션 개요](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>기능

* VMware 클라우드 환경의 주문형 셀프 서비스 프로 비전. 요청 시 용량을 추가 및 제거 하는 기능
* VMware platform 배포, 업그레이드, 관리 평면 백업, 상태/용량 모니터링, 경고, 문제 해결 및 수정
* 언더레이 네트워킹 서비스는 L2/L3 서비스 및 방화벽 규칙 관리를 포함 하 여 VMware를 사용 하도록 설정 하는 데 필요 합니다.
* VPN, 공용 IP 및 인터넷 게이트웨이를 포함 하는 네트워크 서비스에 대 한 경계를 입력 합니다. 이러한 edge 서비스는 Azure에서 실행 되며 Azure의 해당 보안 및 DDoS 보호를 수행 합니다.
* 비용을 절감 하기 위한 용량 예약.
* Azure 및 온-프레미스에 대 한 고속 및 대기 시간이 짧은 연결
* 고객이 Azure 서비스를 통합 방식으로 사용할 수 있는 솔루션 아키텍처는 이러한 고유한 "VMware 클라우드를 공용 클라우드" 아키텍처를 활용 합니다. 이러한 Azure 서비스에는 Azure AD, 저장소, 응용 프로그램 게이트웨이 및 기타가 포함 됩니다.
* 인프라는 완전히 전담 되며 다른 고객의 인프라와 물리적으로 격리 됩니다.
* 작업 관리, 사용량, 대금 청구/계량, 사용자 관리 등의 관리 기능을 제공 합니다.
* 연중 무휴 고객 지원.

## <a name="benefits"></a>이점

* **작업 연속성:** CloudSimple은 VMware 플랫폼에 대 한 기본 액세스를 제공 합니다. CloudSimple 아키텍처는 기존 사용자와 호환 됩니다.
  * 애플리케이션
  * 작업
  * 네트워킹
  * 보안
  * 백업
  * 재해 복구
  * 감사
  * 규정 준수 도구
  * 프로세스
* **재 학습 안 함:** VMware 플랫폼 호환성을 사용 하면 기존 기술과 지식을 사용할 수 있습니다.
* **인프라 민첩성:** 더 이상 모든 용량 요구 사항을 예측 하지 않아도 되며, 더 이상 용량이 나 인프라가 부족 하 게 됩니다. CloudSimple은 클라우드 서비스로 제공 되므로 언제 든 지 용량을 추가 하거나 줄일 수 있습니다.
* **보안:** Azure를 통해 CloudSimple 환경에 액세스 하면 기본 제공 되는 DDoS 보호 및 보안 모니터링이 제공 됩니다.
* **저렴 한 비용:** CloudSimple 플랫폼은 매우 엔지니어링 되며 높은 수준의 자동화, 운영 효율성 및 규모의 경제를 제공 합니다. 또한 CloudSimple은 공용 클라우드에서 VMware의 존재를 활용 하 여 비용을 절감 하는 솔루션 아키텍처를 게시 합니다. Azure AD, Azure storage에 백업, application gateway, 부하 분산 장치 등을 예로 들 수 있습니다.
* **새 하이브리드 플랫폼:** 이 서비스는 Azure의 나머지 부분에 대 한 고속 및 짧은 대기 시간 액세스를 가능 하 게 합니다. 또한 CloudSimple management를 사용 하면 동일한 UI 및 API를 사용 하 여 VMware Virtual Machines 및 나머지 Azure의 통합 된 관리를 수행할 수 있습니다. 개발 팀은 일관 된 통합 방식으로 공용 및 개인 플랫폼을 활용할 수 있습니다.
* **인프라 모니터링, 문제 해결 및 지원:** CloudSimple은 기본 인프라를 서비스로 작동 합니다. 장애 하드웨어는 자동으로 대체 됩니다. CloudSimple은 환경에서 원활 하 게 실행 되도록 하는 동안 사용에 집중할 수 있습니다.
* **정책 호환성:** VMware 기반 도구, 보안 절차, 감사 사례 및 규정 준수 인증을 유지 합니다.

## <a name="scenarios"></a>시나리오

* **데이터 센터 사용 중지 또는 마이그레이션:** 기존 데이터 센터의 제한에 도달 하거나 하드웨어를 새로 고칠 때 추가 용량을 확보 하세요. 클라우드에서 필요한 용량을 쉽게 추가 하 고 하드웨어 새로 고침을 관리 하는 문제를 제거할 수 있습니다. 시간이 많이 걸리는 변환 또는 재 아키텍처와 비교 하 여 클라우드 마이그레이션의 위험 및 비용을 줄입니다. 익숙한 VMware 도구와 기술을 사용 하 여 클라우드 마이그레이션을 가속화 하세요. 클라우드에서 Azure 서비스를 사용 하 여 응용 프로그램의 속도를 현대화.
* **요청 시 확장:** 새 개발 환경 또는 계절 용량 버스트와 같은 예기치 않은 요구를 충족 하기 위해 클라우드로 확장 합니다. 필요할 때 새 용량을 쉽게 만들고 필요할 때만 유지할 수 있습니다. 온-프레미스와 클라우드 둘 다에서 동일한 아키텍처 및 정책을 사용 하 여 선불 투자를 줄이고 프로 비전 속도를 가속화 하 고 복잡성을 줄입니다.
* **Azure 클라우드의 재해 복구 및 가상 데스크톱:** Azure 클라우드에서 데이터, 앱 및 데스크톱에 대 한 원격 액세스를 설정 합니다. 고대역폭 연결을 사용 하는 경우 데이터를 신속 하 게 업로드/다운로드 하 여 인시던트를 복구 합니다. 대기 시간이 짧은 네트워크는 사용자가 데스크톱 앱에서 요구 하는 빠른 응답 시간을 제공 합니다. CloudSimple을 사용 하면 CloudSimple 포털 및 익숙한 VMware 도구를 사용 하 여 클라우드의 모든 정책과 네트워킹을 쉽게 복제할 수 있습니다. 복구 및 복제의 용이성은 DR 및 VDI 구현을 만들고 관리 하는 작업 및 위험을 크게 줄여 줍니다.
* **고성능 응용 프로그램 및 데이터베이스:** CloudSimple은 가장 까다로운 VMware 워크 로드를 실행 하도록 설계 된 하이퍼 수렴 형 아키텍처를 제공 합니다. Oracle, Microsoft SQL server, 미들웨어 시스템 및 고성능의 SQL 데이터베이스를 실행 합니다. 성능을 저하 시 키 지 않고 Azure 및 Azure 개인 작업에서 온-프레미스, VMware에 걸친 하이브리드 앱을 실행할 수 있도록 하는 고속 25gbps 네트워크 연결을 사용 하 여 클라우드를 고유한 데이터 센터로 경험해 보세요.
* **진정한 하이브리드:** VMware와 Azure 간에 DevOps를 통합 합니다. 모든 워크 로드에서 적용할 수 있는 Azure 서비스 및 솔루션에 대 한 VMware 관리를 최적화 합니다. 데이터 센터를 확장 하거나 응용 프로그램을 다시 설계 하지 않고도 공용 클라우드 서비스에 액세스할 수 있습니다. Azure의 VMware 응용 프로그램에 대 한 id, 액세스 제어 정책, 로깅 및 모니터링을 중앙화 합니다.

![시나리오](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>다음 단계

* [CloudSimple 서비스 만들기](quickstart-create-cloudsimple-service.md)
* [사설 클라우드 만들기](quickstart-create-private-cloud.md)