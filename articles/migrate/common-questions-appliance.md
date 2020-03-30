---
title: Azure 마이그레이션 어플라이언스 FAQ
description: Azure 마이그레이션 어플라이언스에 대한 일반적인 질문에 대한 답변을 가져옵니다.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2ef7de3b31cb11a71ec9379232fc5ff1022cf666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336980"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure 마이그레이션 어플라이언스: 일반적인 질문

이 문서에서는 Azure 마이그레이션 어플라이언스에 대한 일반적인 질문에 대해 답변합니다. 다른 질문이 있는 경우 다음 리소스를 확인하십시오.

- Azure 마이그레이션에 대한 [일반적인 질문](resources-faq.md)
- [검색, 평가 및 종속성 시각화에](common-questions-discovery-assessment.md) 대한 질문
- 서버 [마이그레이션에](common-questions-server-migration.md) 대한 질문
- [Azure 마이그레이션 포럼에서](https://aka.ms/AzureMigrateForum) 질문에 대한 답변 받기

## <a name="what-is-the-azure-migrate-appliance"></a>Azure 마이그레이션 어플라이언스는 무엇입니까?

Azure 마이그레이션 어플라이언스는 Azure 마이그레이션: 서버 평가 도구가 온-프레미스 서버를 검색하고 평가하는 데 사용하는 경량 어플라이언스입니다. Azure 마이그레이션: 서버 마이그레이션 도구는 온-프레미스 VM웨어 VM의 에이전트 없는 마이그레이션에도 어플라이언스를 사용합니다.

Azure 마이그레이션 어플라이언스에 대한 자세한 내용은 다음과 같습니다.

- 어플라이언스는 VM 또는 물리적 컴퓨터로 온-프레미스에 배포됩니다.
- 어플라이언스는 온-프레미스 컴퓨터를 검색하고 컴퓨터 메타데이터 및 성능 데이터를 Azure Migrate로 지속적으로 보냅니다.
- 어플라이언스 검색은 에이전트 없이 수행됩니다. 검색된 컴퓨터에는 아무 것도 설치되지 않습니다.

어플라이언스에 대해 [자세히 알아보세요.](migrate-appliance.md)

## <a name="how-does-the-appliance-connect-to-azure"></a>어플라이언스는 Azure에 어떻게 연결됩니까?

어플라이언스는 인터넷을 통해 또는 공용/Microsoft 피어링과 Azure ExpressRoute를 사용하여 연결할 수 있습니다.

## <a name="does-appliance-analysis-affect-performance"></a>어플라이언스 분석이 성능에 영향을 미칩니까?

Azure 마이그레이션 어플라이언스 프로필 온-프레미스 컴퓨터는 성능 데이터를 측정하기 위해 지속적으로 합니다. 이 프로파일링은 프로파일링된 컴퓨터에 거의 성능에 영향을 미치지 않습니다.

## <a name="can-i-harden-the-appliance-vm"></a>어플라이언스 VM을 강화할 수 있습니까?

다운로드한 템플릿을 사용하여 어플라이언스 VM을 만들 때 Azure Migrate 어플라이언스에 필요한 통신 및 방화벽 규칙을 그대로 두면 템플릿에 구성 요소(예: 바이러스 백신)를 추가할 수 있습니다.

## <a name="what-network-connectivity-is-required"></a>어떤 네트워크 연결이 필요합니까?

Azure 마이그레이션 어플라이언스에 대한 네트워크 연결 요구 사항에 대한 자세한 내용은 다음 문서를 참조하십시오.

- **VM웨어 평가**: [URL 액세스](migrate-appliance.md#url-access) 및 [포트 액세스](migrate-support-matrix-vmware.md#port-access)
- **VMware 에이전트 없는 마이그레이션**: [URL 액세스](migrate-appliance.md#url-access) 및 [포트 액세스](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **하이퍼-V 평가**: [URL 액세스](migrate-appliance.md#url-access) 및 [포트 액세스](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>어플라이언스는 어떤 데이터를 수집합니까?

Azure 마이그레이션 어플라이언스가 VM에서 수집하는 데이터에 대한 자세한 내용은 다음 문서를 참조하십시오.

- **VMware VM**: 수집된 데이터를 [검토합니다.](migrate-appliance.md#collected-data---vmware) [
- **하이퍼 VM**: 수집된 데이터를 [검토합니다.](migrate-appliance.md#collected-data---hyper-v)

## <a name="how-is-data-stored"></a>데이터가 어떻게 저장되나요?

Azure 마이그레이션 어플라이언스에서 수집한 데이터는 Azure 마이그레이션 프로젝트를 만든 Azure 위치에 저장됩니다.

데이터가 저장되는 방법에 대한 자세한 내용은 다음과 같습니다.

- 수집된 데이터는 Microsoft 구독의 CosmosDB에 안전하게 저장됩니다. Azure 마이그레이션 프로젝트를 삭제하면 데이터가 삭제됩니다. 저장소는 Azure 마이그레이션에서 처리됩니다. 수집된 데이터에 대한 저장소 계정을 구체적으로 선택할 수 없습니다.
- [종속성 시각화를](concepts-dependency-visualization.md)사용하는 경우 수집된 데이터는 Azure 구독에서 만든 Azure Log Analytics 작업 영역에 미국에 저장됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 데이터가 삭제됩니다.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>연속 프로파일링 중에 업로드되는 데이터의 양

Azure 마이그레이션으로 전송되는 데이터의 볼륨은 여러 매개 변수에 따라 다릅니다. 예를 들어, 10대의 컴퓨터(각각 디스크와 NIC가 있는 각)가 있는 Azure Migrate 프로젝트는 하루에 약 50MB의 데이터를 전송합니다. 이 값은 근사치입니다. 실제 값은 디스크 및 NIC의 데이터 소수점 수에 따라 달라집니다. 컴퓨터, 디스크 또는 NIC 수가 증가하면 전송되는 데이터의 증가가 비선형입니다.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>데이터가 미사용 및 전송 중에 암호화되어 있습니까?

예, 둘 다:

- 메타데이터는 HTTPS를 통해 인터넷을 통해 Azure 마이그레이션 서비스로 안전하게 전송됩니다.
- 메타데이터는 Azure [Cosmos](../cosmos-db/database-encryption-at-rest.md) 데이터베이스와 Microsoft 구독의 [Azure Blob 저장소에](../storage/common/storage-service-encryption.md) 저장됩니다. 메타데이터는 저장소를 위해 미사용 으로 암호화됩니다.
- 종속성 분석을 위한 데이터도 전송 중에 암호화됩니다(보안 HTTPS). 구독의 로그 분석 작업 영역에 저장됩니다. 데이터는 종속성 분석을 위해 미사용 으로 암호화됩니다.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>어플라이언스는 어떻게 vCenter 서버에 연결합니까?

다음 단계에서는 어플라이언스가 VMware vCenter 서버에 연결하는 방법을 설명합니다.

1. 어플라이언스는 어플라이언스를 설정할 때 제공한 자격 증명을 사용하여 vCenter Server(포트 443)에 연결합니다.
2. 어플라이언스는 VMware PowerCLI를 사용하여 vCenter Server를 쿼리하여 vCenter Server에서 관리하는 VM에 대한 메타데이터를 수집합니다.
3. 어플라이언스는 지난 한 달 동안 VM(코어, 메모리, 디스크, NIC)에 대한 구성 데이터와 각 VM의 성능 기록을 수집합니다.
4. 수집된 메타데이터는 평가를 위해 Azure 마이그레이션: 서버 평가 도구(HTTPS를 통해 인터넷을 통해)로 전송됩니다.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure 마이그레이션 어플라이언스가 여러 vCenter 서버에 연결할 수 있습니까?

아니요. [Azure 마이그레이션 어플라이언스와](migrate-appliance.md) vCenter 서버 간에 일대일 매핑이 있습니다. 여러 vCenter Server 인스턴스에서 VM을 검색하려면 여러 어플라이언스를 배포해야 합니다. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure 마이그레이션 프로젝트에 여러 어플라이언스가 있을 수 있습니까?
프로젝트에 여러 어플라이언스가 연결될 수 있습니다. 그러나 어플라이언스는 하나의 프로젝트와만 연결할 수 있습니다. 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>어플라이언스를 통해 얼마나 많은 VM 또는 서버를 검색할 수 있습니까?

최대 10,000개의 VM웨어 VM, 최대 5,000개의 Hyper-V VM, 단일 어플라이언스를 통해 최대 250개의 물리적 서버를 검색할 수 있습니다. 온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 [Hyper-V 평가 크기 조정,](scale-hyper-v-assessment.md) [VMware 평가 크기 조정](scale-vmware-assessment.md)및 [물리적 서버 평가 확장에](scale-physical-assessment.md)대해 읽어보십시오.

## <a name="can-i-delete-an-appliance"></a>어플라이언스를 삭제할 수 있나요?

현재 프로젝트에서 어플라이언스를 삭제하는 것은 지원되지 않습니다.

어플라이언스를 삭제하는 유일한 방법은 어플라이언스와 연결된 Azure Migrate 프로젝트가 포함된 리소스 그룹을 삭제하는 것입니다.

그러나 리소스 그룹을 삭제하면 프로젝트와 연결된 리소스 그룹의 다른 등록된 어플라이언스, 검색된 인벤토리, 평가 및 기타 모든 Azure 구성 요소도 삭제됩니다.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>다른 구독 또는 프로젝트에서 어플라이언스를 사용할 수 있습니까?

어플라이언스를 사용하여 검색을 시작한 후에는 다른 Azure 구독과 함께 사용할 어플라이언스를 다시 구성할 수 없으며 다른 Azure 마이그레이션 프로젝트에서 사용할 수 없습니다. 또한 vCenter 서버의 다른 인스턴스에서 VM을 검색할 수 없습니다. 이러한 작업에 대한 새 어플라이언스를 설정합니다.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Azure VM에서 어플라이언스를 설정할 수 있습니까?

아니요. 현재 이 옵션은 지원되지 않습니다. 

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi 호스트에서 찾을 수 있습니까?

아니요. VMware VM을 검색하려면 vCenter 서버가 있어야 합니다.

## <a name="how-do-i-update-the-appliance"></a>어플라이언스는 어떻게 업데이트하나요?

기본적으로 어플라이언스와 설치된 에이전트가 자동으로 업데이트됩니다. 어플라이언스는 24시간마다 업데이트를 확인합니다. 실패한 업데이트는 다시 시도됩니다. 

이러한 자동 업데이트에 의해 어플라이언스 및 어플라이언스 에이전트만 업데이트됩니다. Azure 마이그레이션 자동 업데이트에 의해 운영 체제가 업데이트되지 않습니다. Windows 업데이트를 사용하여 운영 체제를 최신 상태로 유지합니다.

## <a name="can-i-check-agent-health"></a>에이전트 상태를 확인할 수 있습니까?

예. 포털에서 Azure 마이그레이션: 서버 평가 또는 Azure 마이그레이션: 서버 마이그레이션 도구에 대한 **에이전트 상태** 페이지를 이동합니다. 거기에서 Azure와 어플라이언스의 검색 및 평가 에이전트 간의 연결 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure [마이그레이션 개요를](migrate-services-overview.md)읽습니다.
