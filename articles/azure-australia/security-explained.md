---
title: Azure Australia 보안 설명
description: 오스트레일리아 지역에 대해 가장 많이 묻는 정보 이며 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족 합니다.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571433"
---
# <a name="azure-australia-security-explained"></a>Azure Australia 보안 설명

이 문서에서는 오스트레일리아 정부 기관에서 Microsoft Azure 오스트레일리아를 조사, 디자인 및 배포 하는 데 대 한 일반적인 질문과 관심 영역을 다룹니다.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP 및 CCSL (인증 된 Cloud Services 목록) 문서

ACSC (오스트레일리아 사이버 Security Center)는 CCSL에 추가 될 때 서비스에 대 한 인증, 인증 보고서 및 소비자 가이드의 문자를 제공 합니다.

Microsoft는 현재 Azure, Office 365 및 Dynamics 365 CRM 용 CCSL에 나열 되어 있습니다.

Microsoft는 [Microsoft 서비스 신뢰 포털](https://aka.ms/au-irap)의 오스트레일리아 관련 페이지에서 고객 및 파트너에 게 감사, 평가 및 acsc 인증 문서를 사용할 수 있도록 합니다.

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>DLM (보급 제한 마커) 및 보호 된 인증

정부 기관에서 사용 하도록 승인 된 클라우드 서비스를 포함 하 여 시스템을 보유 하는 프로세스는 ACSC (오스트레일리아 사이버 Security Center)에서 생성 하 고 게시 한 [ISM (Information Security Manual)](https://acsc.gov.au/infosec/ism/) 에 정의 되어 있습니다. ACSC (오스트레일리아 사이버 Security Center)는 사이버 보안 및 클라우드 인증을 담당 하는 ASD 내의 엔터티입니다.

승인 프로세스에는 두 가지 단계가 있습니다.

1. IRAP (보안 평가)-등록 된 전문가가 ISM의 기술 제어에 대 한 시스템, 서비스 및 솔루션을 평가 하 고 컨트롤이 효과적으로 구현 되었는지 여부를 평가 하는 프로세스입니다. 평가는 또한 승인 (ATO)을 실행 하기 전에 승인 기관에서 고려할 특정 위험을 식별 합니다.

1. 운영 승인 – 정부 기관의 선임 책임자가 공식적으로은 하 고 시스템에서 처리, 저장 및 통신 하는 정보에 대 한 시스템의 잔여 위험을 허용 하는 프로세스입니다.  이 프로세스에 대 한 입력은 보안 평가입니다.

보호 된 수준에서 Azure 서비스를 평가 하는 경우 보호 된 데이터와 그 아래 데이터의 처리에 필요한 보안 제어의 구현이 준비 되어 있으며 효율적으로 작동 하는지 확인 합니다.

## <a name="australian-data-classification-changes"></a>오스트레일리아 데이터 분류 변경

2018 년 10 월 1 일, 변호사 일반 부서는 특별히 새로운 [중요 하 고 분류 된 정보 시스템](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx)인 Pspf (보호 보안 정책 프레임 워크)에 대 한 변경 사항을 공개 발표 했습니다.

![수정 된 PSPF 분류](media/pspf-classifications.png)

PSPF에서 작동 하는 모든 오스트레일리아 기관 및 조직은 이러한 변경의 영향을 받습니다. 현재 IRAP/CCSL 인증에 영향을 주는 주요 변경 내용은 현재 인 DLM (현재 보급 제한 표시)이 사용 중지 되었습니다. 표시 **공식: 중요 정보를 보호 하는 데 사용 되는 다양 한 dlms를대체합니다.** 변경 내용에는 모든 민감도 및 분류 수준에서 모든 공식 정보에 적용할 수 있는 세 가지 정보 관리 표식도 도입 되었습니다. **보호** 된 분류는 변경 되지 않은 상태로 유지 됩니다.

미분류 라는 용어는 새 시스템에서 제거 되 고 비공식는 공식 표시를 요구 하지 않더라도 비 정부 정보에 적용 됩니다.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>공식에 대해 Azure 지역 선택: 중요 한 작업 및 보호 된 작업

Azure **공식: 중요** 한 **보호 된** 인증 서비스는 4 개의 오스트레일리아 데이터 센터 지역 (오스트레일리아 동부, 오스트레일리아 남부 동부, 오스트레일리아 중부 및 오스트레일리아 중부 2)에 배포 되지만 ACSC는 서비스를 사용할 수 있는 경우 캔버라의 Azure Government 지역에 **보호** 된 데이터를 배포 하는 것을 권장 합니다. **보호** 된 인증 된 Azure 서비스에 대 한 자세한 내용은 [오스트레일리아 페이지](https://aka.ms/au-irap)에서 다운로드할 수 있습니다.

>[!NOTE]
>Microsoft는 정부 요구 사항에 맞게 설계 되 고 운영 되기 때문에 모든 구분 및 분류의 정부 데이터를 오스트레일리아 중부 및 오스트레일리아 중부 2 지역에 배포 하는 것이 좋습니다.

Azure 오스트레일리아 지역의 특수 한 특성에 대 한 자세한 내용은 [Azure 오스트레일리아 중부 지역](https://azure.microsoft.com/global-infrastructure/australia/)에서 제공 됩니다.

## <a name="how-microsoft-separates-classified-and-official-data"></a>Microsoft에서 분류 된 데이터와 공식 데이터를 구분 하는 방법

Microsoft는 모든 데이터가 중요 하 고/또는 분류 되는 것 처럼 오스트레일리아에서 Azure 및 Office 365을 운영 하 여 보안 제어를 해당 상위 수준으로 증가 시킵니다.

Azure를 지 원하는 인프라는 여러 분류의 데이터를 처리할 수 있습니다.  그러나 고객 데이터가 분류 되는 것으로 가정 하면 적절 한 컨트롤이 적절 하 게 적용 됩니다. Microsoft는 **보호** 된 분류 정보를 저장 하 고 처리 하기 위해 pspf 요구 사항을 준수 하는 서비스에 대 한 기본 보안 상태를 채택 했습니다.

고객에 게 Azure의 한 테 넌 트가 다른 테 넌 트의 위험에 영향을 주지 않도록 하기 위해 Microsoft는 포괄적인 심층 방어 방식을의 심층적인 제어를 구현 합니다.

Microsoft Azure 플랫폼 내에서 구현 된 기능 외에도 고객이 관리 하는 키, 중첩 된 가상화 및 Just-in-time 관리 액세스를 통한 암호화와 같은 추가 고객 구성 가능 컨트롤은 위험을 줄일 수 있습니다. 캔버라의 Azure Government 오스트레일리아 지역 내에서 공식 허용 목록에 대 한 프로세스는 뉴질랜드 정부 및 국가 중요 인프라 조직에 &만 적용 됩니다. 이 커뮤니티 클라우드는 공동 테 넌 트 위험에 민감한 조직에 추가 보증을 제공 합니다.

Microsoft Azure **보호** 된 인증 보고서는 이러한 컨트롤이 **보호** 된 분류 된 데이터 및 격리를 저장 하 고 처리 하는 데 적용 되는지 확인 합니다.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>상태 정부 및 중요 인프라 공급자에 대 한 IRAP/CCSL의 관련성

많은 주 정부 및 중요 인프라 공급자는 연방 정부 요구 사항을 보안 정책 및 보증 프레임 워크에 통합 합니다. 이러한 조직에서는 **공식적인**공식도 **처리 합니다. 연방** 정부와의 상호 작용 또는 고유한 권한으로 **보호** 된 분류 된 데이터의 중요 한 일부입니다.

오스트레일리아 정부는 오스트레일리아의 보안 및 경제 prosperity 근본적으로 영향을 주는 비 정부 데이터 보호에 대 한 정책 및 법률에 따라 점점 점차 집중 하 고 있습니다. 이와 같이 Azure 오스트레일리아 지역과 CCSL 인증은 이러한 모든 산업과 관련이 있습니다.

![중요 인프라 섹터](media/nci-sectors.png)

Microsoft 인증에는 Azure 서비스에 대 한 보안 보호 기능에 대 한 철저 하 고 엄격한 공식 평가가 적용 되었으며 이러한 매우 중요 한 데이터를 처리 하기 위해 승인 되었습니다.

## <a name="location-and-control-of-microsoft-data-centres"></a>Microsoft 데이터 centres의 위치 및 제어

데이터 센터 위치와 데이터를 처리 하는 클라우드 서비스에 대 한 소유권을 명시적으로 알려면 정부 및 중요 인프라의 필수 요구 사항입니다.  Microsoft는 이러한 위치 및 소유권에 대 한 광범위 한 정보를 제공 하는 하이퍼 규모의 클라우드 공급자로 서 고유 합니다.

Microsoft의 Azure 오스트레일리아 지역 (오스트레일리아 중부 및 오스트레일리아 중부 2)은 CDC Datacentres의 기능 내에서 운영 됩니다.  CDC Datacentres의 소유권은 Superannuation Corporation의 48% 소유권, Infratil에서 48%의 소유권 (NZ 기반, 이중 오스트레일리아, 뉴질랜드 재고 교환에 나열 된 장기 인프라 자산 기금)을 통해 오스트레일리아로 제어 됩니다. 및 4% 오스트레일리아 관리.  

CDC Datacentres의 관리에는 오스트레일리아 정부에서 향후 소유권 및 제어 양도를 제한 하는 계약 보증이 적용 됩니다. CDC Datacentres와 Microsoft의 파트너 관계를 통해 제공 되는 공급망 및 소유권의 투명성은 [정부 호스팅 전략 전체의](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) 원칙과 **인증 된 소 버린 datacentres**의 정의를 포함 합니다.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>현재 CCSL 인증에 포함 된 Azure 서비스

6 월 2017에는, **미분류에서 데이터를 저장 하 고 처리 하기 위한 acsc 인증 된 41 Azure 서비스가 있습니다. Dlm** 수준. 2018 년 4 월, 이러한 서비스 중 24 개는 **보호 된** 분류 된 데이터에 대해 인증 되었습니다.

오스트레일리아의 Azure 지역에서 ACSC 인증 된 Azure 서비스를 사용 하는 것은 다음과 같습니다 (굵은 글꼴로 인증 됨 **).**

|Azure 호주 중부 지역|비 지역 서비스 및 기타 지역|
|---|---|
|API Management, App Gateway, 애플리케이션 서비스, **Automation, Azure Portal, Backup, Batch, Cloud Services**, Cosmos DB, Event Hubs, **express**경로, HDInsight, **Key Vault**, Load Balancer, Log Analytics, **다단계 인증**, Redis Cache, **리소스 관리자, Service Bus, Service Fabric, Site Recovery**, SQL Database, 저장소, Traffic Manager, Virtual Machines, Virtual Network **, VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **가져오기 내보내기, Information Protection, IOT Hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **보안 센터, Scheduler**, 검색, Stream Analytics|
|

Microsoft는 IRAP/CCSL을 포함 하 여 Azure가 거치는 모든 전역, 정부, 산업 및 지역 규정 준수 및 평가 프로세스에 대 한 모든 범위 서비스를 나열 하는 [Microsoft Azure 준수 개요](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) 를 게시 합니다.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Azure 서비스가 필요한 것 보다 낮은 수준에서 나열 되거나 평가 되지 않음

인증 되지 않았거나 **공식으로 인증 된 서비스: 중요** 하지만 **보호**되지 않음은 서비스를 제공 하는 경우 **보호** 된 데이터를 호스팅하는 솔루션의 일부로 또는 함께 사용할 수 있습니다.
1. 암호화 되지 않은 **보호 된** 데이터를 저장 하거나 처리 하지 않습니다. 또는
1. 위험 평가를 완료 하 고 **보호 된** 데이터를 직접 저장 하도록 서비스를 승인 했습니다.

CCSL에 포함 되지 않은 서비스를 사용 하 여 **공식** 데이터를 저장 하 고 처리 하려면 클라우드 서비스 공급자를 사용 하 여 계약을 입력 하거나 갱신 하기 전에이 작업을 수행 하는 작업을 수행 하는 것을 ISM에서 수행 해야 합니다.

**보호** 된 워크 로드에 대해 에이전시에서 사용 하는 모든 서비스는 계속 해 서 ISM에 설명 된 프로세스와 [DTA 보안 클라우드 전략](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)의 에이전시 관리 irap 평가 프로세스에 따라 보안을 평가 하 고 승인 해야 합니다.

![DTA 보안 클라우드 전략 인증 프로세스](media/certification.png)

Microsoft는 서비스를 지속적으로 평가 하 여, 오스트레일리아 정부 사용을 위해 플랫폼이 안전 하 고 적합 한지 확인 하 고, 현재 **보호**된 CCSL에 없는 서비스에 대 한 지원이 필요한 경우 microsoft에 연락 합니다.

Microsoft에서는 Microsoft가 **미분류 DLM** 과 **보호** 된 분류 모두에서 ccsl에 인증 된 다양 한 서비스를 제공 하기 때문에 ISM은 최소 2 년 마다 서비스의 irap 평가를 수행 해야 합니다. Microsoft는 연간 평가를 배포한 추가 서비스를 포함 하 여 고려할 수도 있습니다.

## <a name="certified-protected-gateway-in-azure"></a>Azure의 인증 된 보호 된 게이트웨이

Microsoft는 게이트웨이 통합 프로그램에서 허용 되는 SIGs 수에 대 한 제한으로 인해 정부 인증 보안 인터넷 게이트웨이 (SIG)를 작동 하지 않습니다.  그러나 Microsoft Azure 내에서 SIG의 예상 및 필요한 기능을 구성할 수 있습니다.

Azure 서비스의 **보호** 된 인증을 통해 acsc에는 azure에 연결 하기 위한 기관에 대 한 특정 권장 사항이 있으며, **보호** 된 도메인과 인터넷과. 이러한 권장 사항에는 네트워크 보안 그룹, 방화벽 및 Vpn (가상 사설망)을 사용 하는 것이 포함 됩니다.  ACSC는 가상 게이트웨이 어플라이언스를 사용 하는 것을 권장 합니다. Azure에서 사용할 수 있는 여러 가상 어플라이언스는 ASD 평가 제품 목록에 물리적으로 해당 하는 것으로 되어 있거나 Common Criteria 보호 프로필에 대해 평가 되 고 Common Criteria 포털에 나열 되어 있습니다. 이러한 제품은 CCRA (Common Criteria 인식)의 서명자 인 failedodatapropertyname으로 함께 사용할 수 있습니다.

Microsoft는 서로 다른 보안 도메인 간의 경계를 보호 하는 데 필요한 보안 기능을 제공 하는 Azure 기반 기능을 구현 하는 데 대 한 지침을 제공 하 여 결합 된 경우 인증 된 SIG와 동등한 것을 형성 합니다. 이러한 기능의 디자인 및 구현 뿐만 아니라 동일한 작업을 수행 하는 여러 파트너 솔루션을 지원할 수 있는 여러 파트너가 있습니다.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Microsoft 고객 지원 담당자의 보안 clearances 및 참여

Microsoft는 귀하의 서비스를 담당 하 고 학습 된 보안 담당자와 전역적으로 운영 합니다.  시드니 및 멜버른의 시설에 대 한 물리적 액세스를 unescorted 하는 직원에 게는 오스트레일리아 정부 기준 보안 clearances이 있습니다. 오스트레일리아 중부 및 오스트레일리아 중부 2 지역 내의 직원은 최소 부정 심사 1 (NV1) clearances ( **비밀** 데이터에 적합)를 가집니다. 이는 데이터 centres 운영 Azure 내의 인력이 매우 신뢰할 수 있는 고객에 게 추가 보증을 제공 합니다.

Microsoft에는 적시에 시스템을 통해 액세스 권한을 부여 하 고, 역할 기반 액세스 제어에 따라 간단 하 게 관리 하는 액세스 정책이 있습니다. 대부분의 경우 관리자는 서비스 문제를 해결 하 고 서비스를 유지 관리 하기 위해 고객 데이터에 대 한 액세스 또는 권한이 필요 하지 않습니다.  원격 실행을 위한 작업의 자동화 및 스크립팅이 높으면 고객 데이터에 직접 액세스할 필요가 없습니다.

변호사-일반 부서는 Azure 내의 Microsoft 직원 보안 정책 및 절차가 INFOSEC-9의 정보 프로 비전에 대 한 PSPF 액세스의 의도와 일치 하는지 확인 했습니다.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>ITAR (Arm 규정)의 국제 트래픽 저장 또는 귀 (관리 규정) 데이터 내보내기

사용자가 내보내기 제어 데이터에 대 한 의무를 충족 하는 데 도움이 되는 Azure 기술 컨트롤은 Azure에서 전역적으로 동일 합니다. 중요 한 것은 내보내기 제어 데이터에 대 한 공식적인 평가 및 인증 프레임 워크가 없기 때문에 "ITAR/귀 상자의 틱"은 없는 것입니다.

Azure Government 및 Office 365 미국 정부 방어를 위해 추가 계약 및 프로세스 측정값을 준비 하 여 고객에 게 컨트롤 내보내기가 적용 되도록 지원 합니다. 이러한 추가 계약 절과 보장 된 미국 국가별 지원 및 Azure 지역에 대 한 관리는 오스트레일리아에 적합 하지 않습니다.

즉, 오스트레일리아의 Azure는 ITAR/귀에 사용할 수 없지만, 내보내기 라이선스를 통해 사용자에 게 적용 되는 제한을 명확 하 게 이해 해야 하며, Azure를 사용 하 여 저장 하기 전에 이러한 의무를 충족 하기 위해 추가 보호를 구현 해야 합니다. 해당 데이터입니다. 예를 들어 Azure Active Directory를 특성으로 작성 하 고, Azure Information Protection를 사용 하 여 데이터에 대 한 암호화 규칙을 적용 하 고,이를 미국 및 내보내기 라이선스에 포함 된 다른 모든 nationalities으로 제한 해야 할 수 있습니다. Azure에 저장 하기 전에 온-프레미스의 모든 데이터를 암호화 하 고, 고객 키를 사용 하거나 ITAR 데이터에 대 한 고유 키를 보유 하 고 있습니다.

ITAR는 공식 인증이 아니므로 내보내기 라이선스와 관련 된 제한 및 제한 사항을 이해 하 고 Azure에 이러한 요구 사항을 충족 하기에 충분 한 컨트롤이 있는지 여부를 확인 해야 합니다. 이 경우에 고려해 야 할 문제 중 하나는 수출 라이선스에 대 한 nationality 승인 되지 않은 엔지니어의 액세스입니다.

## <a name="next-steps"></a>다음 단계

Azure [VPN Gateway](vpn-gateway.md) 에서 azure 오스트레일리아에 대 한 VPN 연결의 ISM 호환 구성 및 구현에 대 한 문서를 검토 합니다.
