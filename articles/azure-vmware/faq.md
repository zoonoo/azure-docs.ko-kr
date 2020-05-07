---
title: 자주 묻는 질문
description: Azure VMware 솔루션 (AVS)에 대 한 일반적인 질문에 대 한 답변을 제공 합니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 6d8e87dd52871b82109ccc794af04244efe95b06
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854607"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Azure VMware 솔루션 (AVS) 미리 보기에 대 한 질문과 대답

Azure VMware 솔루션 (AVS)에 대 한 자주 묻는 질문에 대 한 대답입니다.

## <a name="general"></a>일반

**AVS(Azure VMware 솔루션)란?**

기업은 IT 현대화 전략을 활용 하 여 비즈니스 민첩성을 개선 하 고 비용을 절감 하며 혁신을 가속화 하는 하이브리드 클라우드 플랫폼은 고객의 디지털 변환의 핵심 핵심으로 등장 했습니다. AVS는 VMware의 SDDC (소프트웨어 정의 데이터 센터) 소프트웨어를 Microsoft Azure 글로벌 클라우드 서비스 에코 시스템과 결합 합니다. AVS 솔루션은 성능, 가용성, 보안 및 규정 준수 요구 사항을 충족 하도록 관리 됩니다.

## <a name="avs-service"></a>AVS 서비스

**지금 AVS를 사용할 수 있는 곳은 어디 인가요?**

미리 보기 중에는 미국 동부에서 북아메리카 및 서 부의 암스테르담에서 사용할 수 있습니다.

**Azure VMware 솔루션 (AVS) 인스턴스에서 실행 되는 워크 로드는 Azure 서비스를 사용 하거나 통합할 수 있나요?**

모든 Azure 서비스는 AVS 솔루션 고객에 게 제공 됩니다. 특정 서비스에 대 한 성능 및 가용성 제한 사항은 사례별로 해결 해야 합니다.

**이제 사설 클라우드 리소스를 관리 하는 데 사용 하는 것과 동일한 도구를 사용 하나요?**

예. Azure Portal은 배포 및 여러 관리 작업에 사용 됩니다. vCenter 및 NSX Manager는 vSphere NSX 리소스를 관리 하는 데 사용 됩니다.

**온-프레미스 vCenter를 사용 하 여 사설 클라우드를 관리할 수 있나요?**

시작 시, AVS는 온-프레미스 및 사설 클라우드 환경에서 단일 관리 환경을 지원 하지 않습니다. 사설 클라우드 클러스터는 사설 클라우드로의 vCenter 및 NSX Manager 로컬에서 관리 됩니다.

**온-프레미스에서 실행 되는 vRealize Suite를 사용할 수 있나요?** 

특정 통합 및 사용 사례는 사례별로 평가할 수 있습니다.

**온-프레미스 환경에서 AVS 사설 클라우드로 vSphere Vm을 마이그레이션할 수 있나요?**

예. 표준 cross vCenter [vMotion 요구 사항] [https://kb.vmware.com/s/article/210695]이 충족 되는 경우 vm 마이그레이션 및 vMotion를 사용 하 여 사설 클라우드로 vm을 이동할 수 있습니다.

**온-프레미스 환경에 필요한 특정 버전의 vSphere 있나요?**

모든 클라우드 환경은 vMotion에 대 한 온-프레미스 환경에서 HCX, vSphere 5.5 이상으로 제공 됩니다.

**변경 제어 프로세스의 모양은 어떻게 되나요?**

서비스 자체에 대 한 업데이트는 Microsoft Azure의 표준 변경 관리 프로세스를 따릅니다. 고객은 작업 관리 작업 및 관련 된 변경 관리 프로세스를 담당 합니다.

**CloudSimple에의 한 Azure VMware 솔루션과 어떻게 다른가?**

새 Azure VMware 솔루션을 사용 하 여 Microsoft와 VMware에는 직접 클라우드 공급자의 파트너 관계가 있습니다. 새 솔루션은 Microsoft에서 완전히 설계, 구축 및 지원 되며 VMware에 의해 보증 됩니다. 아키텍처는 Azure 전용 인프라에서 실행 되는 VMware 기술 스택을 사용 하 여 일관 된 솔루션입니다.

**기존 Azure VMware 솔루션 고객 인 경우이 미리 보기는 무엇을 의미 하나요?**

CloudSimple에의 한 기존 Azure VMware 솔루션은 변경 되지 않습니다. Azure에서 솔루션을 계속 지원 합니다. Azure VMware 솔루션은 [SLA (](https://aka.ms/CSVMwareSLA)서비스 수준 계약)에 의해 지원 됩니다. 고객은 프로덕션 워크 로드에 서비스를 계속 사용 해야 합니다. 이 솔루션은 [Microsoft 서비스 약관](https://azure.microsoft.com/support/legal/)에서 적용 하는 사용 가능한 솔루션입니다.

**CloudSimple을 통해이 새 솔루션으로 Azure VMware 솔루션에서 마이그레이션할 수 있나요?**

예, Azure VMware 솔루션은 HCX와 같은 익숙한 VMware 도구를 사용 하 여 마이그레이션을 지원 합니다. 새 솔루션으로 마이그레이션하는 데 관심이 있는 고객의 경우 Microsoft 계정 팀과 협력 하 여 옵션 및 사용 가능한 지원을 살펴보세요.

## <a name="compute-network-and-storage"></a>계산, 네트워크 및 저장소

**사용할 수 있는 호스트 유형이 둘 이상 있나요?**

사용할 수 있는 호스트 유형은 하나 뿐입니다.

**각 호스트 유형의 CPU 사양은 무엇 인가요?**

서버에는 이중 18 코어 2.3 g h z Intel Cpu가 있습니다.

**각 호스트에 얼마나 많은 메모리가 있나요?**

서버에는 576 GB의 RAM이 있습니다.

**각 호스트의 저장소 용량은 무엇 인가요?**

각 ESXi 호스트에는 15.2 TB의 용량 계층을 포함 하는 두 개의 VSAN diskgroups와 3.2 TB NVMe 캐시 계층 (각 diskgroups의 1.6 TB)이 있습니다.

**각 ESXi 호스트에서 사용할 수 있는 네트워크 대역폭은 얼마나 되나요?**

ESXi 호스트는 최대 25Gbps의 연결 대역폭을 지원 합니다.

**미사용 암호화 된 VSAN 데이터 저장소에 저장 된 데이터 입니까?**

예, 모든 VSAN 데이터는 Azure Key Vault에 저장 된 키를 사용 하 여 기본적으로 암호화 됩니다.

## <a name="hosts-clusters-and-private-clouds"></a>호스트, 클러스터 및 사설 클라우드

**기본 인프라가 공유 되나요?**

아니요, 사설 클라우드 호스트 및 클러스터는 사용 전후에 안전 하 게 삭제 됩니다.

**클러스터 당 호스트의 최소 및 최대 수는 얼마 인가요?**

클러스터는 ESXi 호스트 수를 3 개에서 16 개 사이로 확장할 수 있습니다. 평가판 클러스터는 호스트 3 개로 제한 됩니다.

**사설 클라우드 클러스터를 확장할 수 있나요?**

예, 클러스터는 ESXi 호스트의 최소 및 최대 수 사이에서 크기를 조정 합니다. 평가판 클러스터는 호스트 3 개로 제한 됩니다.

**평가판 클러스터 란?**

평가판 클러스터는 AVS 사설 클라우드의 1 개월 평가에 사용 되는 세 개의 호스트 클러스터입니다.

**평가판 클러스터에 대해 고성능 호스트를 사용할 수 있나요?**

아니요. 고성능 ESXi 호스트는 프로덕션 클러스터에서 사용 하도록 예약 되어 있습니다.

## <a name="avs-and-vmware-software"></a>AVS 및 VMware 소프트웨어

**사설 클라우드에서 사용 되는 VMware 소프트웨어 버전은 무엇 인가요?**

사설 클라우드는 NSX의 vSphere 6.7, Vsphere 6.7, HCX 및 버전 2.5를 사용 합니다.  

**사설 클라우드가 VMware NSX을 사용 하나요?**

예, NSX-T 2.5은 AVS 사설 클라우드의 소프트웨어 정의 네트워킹에 사용 됩니다.

**사설 클라우드에서 VMware NSX-V를 사용할 수 있나요?**

아니요. NSX은 유일 하 게 지원 되는 NSX 버전입니다.

**은 (는) 온-프레미스 환경 또는 사설 클라우드에 연결 하는 네트워크에서 NSX 필요 합니다.**

아니요, NSX 온-프레미스를 사용할 필요가 없습니다.

**사설 클라우드의 VMware 소프트웨어에 대 한 업그레이드 및 업데이트 일정은 무엇 인가요?**

사설 클라우드 소프트웨어 번들 업그레이드는 소프트웨어 번들 형식의 최신 릴리스 버전 내에서 소프트웨어를 유지 하기 위해 수행 됩니다. 사설 클라우드 소프트웨어 버전은 개별 소프트웨어 구성 요소의 최신 버전 (ESXi, NSX, vCenter, VSAN)과 다를 수 있습니다.

**사설 클라우드 소프트웨어 stack은 얼마나 자주 업데이트 되나요?**

사설 클라우드 소프트웨어는 VMware에서 소프트웨어 번들의 릴리스와 함께 추적 되는 일정에 따라 업그레이드 됩니다. 사설 클라우드에는 업그레이드에 대 한 가동 중지 시간이 필요 하지 않습니다.

## <a name="connectivity"></a>연결

**사설 클라우드를 온-프레미스 환경과 통합 하는 데 필요한 네트워크 IP 주소 계획은 무엇 인가요?**

개인 네트워크/22 주소 공간은 AVS 사설 클라우드를 배포 하는 데 필요 합니다. 이 개인 주소 공간은 구독의 다른 가상 네트워크 또는 온-프레미스 네트워크와 겹치지 않아야 합니다.
 
**온-프레미스 환경에서 AVS 사설 클라우드로 연결 어떻게 할까요??**

다음 두 가지 방법 중 하나를 통해 서비스에 연결할 수 있습니다. 

- 사설 클라우드로 Express 경로를 통해 피어 링 하는 Azure 가상 네트워크에 배포 된 VM 또는 응용 프로그램 게이트웨이 사용.
- Express 경로를 통해 온-프레미스 데이터 센터에서 Azure Express 경로 회로로 Global Reach 합니다.

**작업 VM을 인터넷 또는 Azure 서비스 끝점에 연결 어떻게 할까요??**

Azure Portal에서 사설 클라우드에 대해 인터넷 연결을 사용 하도록 설정 합니다. NSX-T 관리자를 사용 하 여 NSX T1 라우터 및 논리 스위치를 만듭니다. 그런 다음 vCenter를 사용 하 여 논리 스위치에 의해 정의 된 네트워크 세그먼트에서 VM을 배포 합니다. 해당 VM은 인터넷 및 Azure 서비스에 대 한 네트워크 액세스 권한을 가집니다.

**사설 클라우드의 논리 네트워크에 있는 Vm에 대 한 인터넷 액세스를 제한 해야 하나요?**

아니요. 인터넷에서 사설 클라우드로의 네트워크 트래픽 인바운드는 허용 되지 않습니다.

**논리 네트워크의 Vm에서 인터넷에 대 한 인터넷 액세스를 제한 해야 하나요?**

예. NSX를 사용 하 여 인터넷에 대 한 VM 액세스를 제한 하는 방화벽을 만들어야 합니다.

## <a name="accounts-and-privileges"></a>계정 및 권한

**새 AVS 사설 클라우드를 사용 하 여 얻을 수 있는 계정 및 권한은 무엇 인가요?**

NSX Manager의 vCenter 및 admin 액세스에서 cloudadmin 사용자에 대 한 자격 증명을 제공 합니다. 또한 Azure Active Directory를 통합 하는 데 사용할 수 있는 CloudAdmin 그룹이 있습니다. 자세한 내용은 [액세스 및 Id 개념](concepts-identity.md)을 참조 하세요.

**ESXi 호스트에 대 한 관리자 액세스 권한을 가질 수 있나요?**

아니요, ESXi에 대 한 관리자 액세스는 솔루션의 보안 요구 사항을 충족 하도록 제한 됩니다.

**VCenter에는 어떤 권한 및 사용 권한이 있나요?**

CloudAdmin 그룹 권한이 있습니다. 자세한 내용은 [액세스 및 Id 개념](concepts-identity.md)을 참조 하세요.

**NSX manager에 대 한 권한 및 사용 권한은 무엇 인가요?**

NSX에 대 한 모든 관리자 권한을 보유 하 고 NSX 데이터 센터 온-프레미스와 마찬가지로 역할 기반 액세스 제어를 관리할 수 있습니다. 자세한 내용은 [액세스 및 Id 개념](concepts-identity.md)을 참조 하세요.

> [!NOTE]
> T0 라우터는 사설 클라우드 배포의 일부로 생성 및 구성 됩니다. 해당 논리 라우터 또는 NSX-T에 지 노드 Vm을 수정 하면 사설 클라우드에 대 한 연결에 영향을 줄 수 있습니다.

## <a name="billing-and-support"></a>청구 및 지원

**AVS 미리 보기 중에 요금이 청구 되는 방식**

미리 보기 중에는에 대 한 청구는 종 량 제 기준으로 매달 청구 됩니다. 추가 옵션은 일반 공급에서 사용할 수 있습니다.

**AVS 미리 보기 중에는 가격 책정을 어떻게 구성 하나요?**

가격 책정에 대 한 일반적인 질문은 Azure VMware 솔루션 [가격 책정](https://azure.microsoft.com/pricing/details/azure-vmware) 페이지를 참조 하세요. 요청에 따라 미리 보기 가격 책정을 사용할 수 있습니다. 계정 팀에 문의 하거나 가격 책정 페이지의 링크를 따라 판매 담당자에 게 문의 하세요.

**AVS를 지 원하는 사람은 누구 인가요?**

Microsoft에서 AVS에 대 한 지원을 제공 합니다. 미리 보기 지침에 따라 9 ~ 5 pm PST 업무 시간 월요일부터 금요일까지 지원 서비스를 제공할 예정입니다. [이 링크](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 에서 지원 티켓을 발생 시킬 수 있습니다.

**AVS 사설 클라우드를 만들려면 어떤 계정이 필요 한가요?**

Azure 구독에는 Azure 계정이 필요 합니다.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
