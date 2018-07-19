---
title: Azure 시설, 프레미스 및 물리적 보안 | Microsoft Docs
description: 이 문서에서는 물리적 인프라, 보안 및 규정 준수 제안을 포함하여 Azure 데이터 센터에 대해 설명합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101821"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure 시설, 구역 및 물리적 보안
Microsoft의 클라우드는 전 세계적으로 수천 개의 온라인 서비스를 지원하고 100개가 넘는 고도의 보안 시설에 걸쳐 있는 [글로벌로 분산된 데이터 센터 인프라](https://azure.microsoft.com/global-infrastructure/)로 구성되어 있습니다.

이 인프라는 전 세계 사용자에게 응용 프로그램을 더 가까이 제공하는 데 필요한 확장성을 제공하고, 데이터 상주를 유지하고, 고객에게 포괄적인 준수 및 복원력 옵션을 제공하도록 디자인되어 있습니다. Azure는 전 세계 52개 지역을 포함하며 140개 국가에서 사용할 수 있습니다.

지역은 대규모의 복원력 있는 네트워크를 통해 상호 연결된 데이터 센터 집합입니다. 네트워크에는 기본적으로 콘텐츠 배포, 부하 분산, 중복성 및 암호화가 포함되어 있습니다. 다른 클라우드 공급자보다 많은 글로벌 지역을 제공하는 Azure는 고객에게 필요한 곳에 응용 프로그램을 배포하는 유연성을 제공합니다.

Azure 지역은 지리적 위치로 구성됩니다. Azure 지리적 위치는 지리적 경계 내에서 데이터 상주, 주권, 준수 및 복원력 요구 사항이 적용되도록 합니다.

지리적 위치를 통해 특정 데이터 상주 및 준수 요구 사항이 있는 고객은 데이터와 응용 프로그램을 가깝게 유지할 수 있습니다. 지리는 전용 대용량 네트워킹 인프라와 연결을 통해 전체 지역 오류를 견디는 내결함성이 있습니다.

가용성 영역은 Azure 지역 내에서 물리적으로 떨어진 위치입니다. 각 가용성 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 가용성 영역을 통해 고객은 고가용성과 짧은 대기 시간 복제로 중요 업무용 응용 프로그램을 실행할 수 있습니다.

다음 그림은 Azure 글로벌 인프라가 고가용성, 데이터 복구 및 백업을 위해 동일한 데이터 상주 경계 내에서 지역과 가용성 영역을 연결하는 방법을 보여 줍니다.

![데이터 상주 경계][1]

데이터 센터의 대규모 공간은 지리적으로 분산되어 있으므로 Microsoft가 네트워크 대기 시간을 줄이고 지역 중복 백업 및 장애 조치(Failover)를 허용하기 위해 고객과 가깝게 있을 수 있습니다.

## <a name="physical-security"></a>물리적 보안
Microsoft는 고객 데이터가 저장되어 있는 영역에 대한 물리적 액세스를 엄격하게 제어하는 방식으로 데이터 센터를 디자인, 구축 및 운영합니다. Microsoft는 고객 데이터 보호의 중요성을 이해하며 데이터를 포함하는 데이터 센터의 보안을 유지하도록 노력합니다. Microsoft에는 Azure를 지원하는 물리적 시설을 디자인, 구축 및 운영하는 일을 전담하는 부서가 있습니다. 이 팀은 최첨단의 물리적 보안을 유지 관리하는 데 투입되었습니다.

Microsoft는 허가되지 않은 사용자가 데이터 및 데이터 센터 리소스에 대한 실제 액세스 권한을 얻을 위험을 줄이기 위해 물리적 보안에 대해 계층화된 접근 방식을 사용합니다. Microsoft에서 관리하는 데이터 센터는 시설 경계, 건물 경계, 건물 내부 및 데이터 센터 층에서 액세스 승인을 허용하는 광범위한 보호 계층을 포함합니다. 물리적 보안 계층은 다음과 같습니다.

- 액세스 요청 및 승인 – 데이터 센터에 도착하기 전에 액세스를 요청해야 합니다. 규정 준수 또는 감사 목적과 같이, 방문에 대해 정당한 비즈니스 근거를 제공하도록 요구됩니다. 모든 요청은 Microsoft 직원의 액세스 필요성에 따라 승인됩니다. 액세스 필요성 기준은 데이터 센터에서 작업을 완료해야 하는 개인의 수를 최소한으로 유지하는 데 도움이 됩니다. 권한이 부여되면 개인만이 승인된 비즈니스 근거에 따라 데이터 센터의 개별 영역에 액세스할 수 있습니다. 권한은 특정 기간으로 제한되며, 허용된 기간 후에는 만료됩니다.

- 시설의 경계 - 데이터 센터에 도착하면 잘 정의된 액세스 지점을 통과해야 합니다. 일반적으로 강철과 콘크리트로 만들어진 높은 울타리가 경계를 1인치 간격으로 둘러싸고 있습니다. 데이터 센터 주변에는 카메라가 있어서 연중무휴로 촬영되며, 보안 팀에서 모니터링합니다.

- 건물 입구 - 데이터 센터 입구에는 엄격한 훈련 및 신원 조사를 거친 전문 보안 책임자가 배치됩니다. 이러한 보안 책임자는 또한 연중무휴로 데이터 센터 내부를 감시 카메라로 모니터링하면서 데이터 센터를 정기적으로 순찰합니다.

- 건물 내부 - 건물에 들어간 후에 데이터 센터로 계속 이동하려면 생체 인식 방법을 통한 2단계 인증을 통과해야 합니다. ID가 유효한지 확인되면 액세스가 승인된 데이터 센터 부분에 들어갈 수 있습니다. 승인된 기간 동안만 데이터 센터에 머무를 수 있습니다.

- 데이터 센터 층 - 입장하도록 승인된 층만 입장할 수 있습니다. 전신 금속 탐지기를 통과해야 합니다. Microsoft에서 알지 못하게 데이터 센터에서 무단으로 데이터를 입출력할 위험을 줄이기 위해 승인된 장치만 데이터 센터 층에 유입되도록 할 수 있습니다. 또한 감시 카메라가 모든 서버 랙의 앞뒷면을 모니터링합니다. 데이터 센터 층을 나갈 때도 전신 금속 탐지기가 작동합니다. 데이터 센터를 벗어나기 위해 추가 보안 검사를 통과해야 합니다.

방문자는 모든 Microsoft 시설에서 떠나는 시점에 배지를 반납해야 합니다.

## <a name="physical-security-reviews"></a>물리적 보안 검토
데이터 센터에서 Microsoft Azure 보안 요구 사항을 적절히 처리할 수 있도록 시설에 대한 물리적 보안 검토가 정기적으로 수행됩니다. 데이터 센터 호스팅 공급자 담당자는 Microsoft Azure 서비스 관리를 제공하지 않습니다. 직원은 Azure 시스템에 대한 로그온 액세스가 없으며 Azure 배치실 및 운전실에 실제로 액세스할 수 없습니다.

## <a name="data-bearing-devices"></a>데이터 포함 장치
Microsoft는 [NIST 800-88 규격](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01)의 모범 사례 절차 및 초기화 솔루션을 사용합니다. 초기화할 수 없는 하드 드라이브의 경우, 해당 드라이브를 삭제하고 정보를 복구할 수 없게 렌더링하는 삭제 프로세스가 사용됩니다. 삭제 프로세스로는 분해, 파쇄, 분쇄 또는 소각 등이 있습니다. 적절한 폐기 방법은 자산 유형에 따라 결정됩니다. 파기 기록은 유지됩니다.  

## <a name="equipment-disposal"></a>장비 폐기
Microsoft Azure는 고객을 대신하여 이 원칙을 구현합니다. 시스템의 수명이 다하면 Microsoft 운영 담당자가 엄격한 데이터 처리 및 하드웨어 폐기 절차에 따라 고객 데이터를 포함하는 하드웨어를 신뢰할 수 없는 사용자가 사용할 수 없도록 합니다. 이를 지원하는 드라이브에 대한 보안 지우기 방식(하드 드라이브 펌웨어를 통한)이 진행됩니다. 초기화할 수 없는 하드 드라이브의 경우, 해당 드라이브를 삭제하고 정보를 복구할 수 없게 렌더링하는 삭제 프로세스가 사용됩니다. 삭제 프로세스로는 분해, 파쇄, 분쇄 또는 소각 등이 있습니다. 적절한 폐기 방법은 자산 유형에 따라 결정됩니다. 파기 기록은 유지됩니다. 모든 Microsoft Azure 서비스는 승인된 미디어 저장소 및 폐기 관리 서비스를 사용합니다.

## <a name="compliance"></a>규정 준수
Azure 인프라는 광범위한 국제 및 업계 고유의 준수 표준(예: ISO 27001, HIPAA, FedRAMP, SOC 1 및 SOC 2)을 충족하도록 디자인되고 관리됩니다. 호주 IRAP, UK G-Cloud 및 싱가포르 MTCS를 비롯한 국가별 표준도 충족됩니다. 영국 표준 협회 등의 엄격한 타사 감사에 따르면 Azure는 이러한 표준이 규정하는 엄격한 보안 제어 정책을 준수합니다.

Azure에서 따르는 전체 규정 준수 표준 목록에 대해서는 [규정 준수 제안](https://www.microsoft.com/trustcenter/compliance/complianceofferings)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure 인프라 보호를 위해 Microsoft가 수행하는 작업에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 인프라의 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Microsoft Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [Azure 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라의 무결성](azure-infrastructure-integrity.md)
- [Azure에서 고객 데이터 보호](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
