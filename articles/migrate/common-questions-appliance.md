---
title: Azure Migrate 어플라이언스에 대 한 일반적인 질문
description: Azure Migrate 어플라이언스에 대 한 일반적인 질문에 대 한 답변 가져오기
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 29cb390f509a2a4ebf44c1f9aacef99b507aebb8
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720330"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 어플라이언스: 일반적인 질문

이 문서에서는 Azure Migrate 어플라이언스에 대 한 일반적인 질문에 답변 합니다. 이 문서를 읽은 후 추가 쿼리가 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시 하세요. 다른 질문이 있는 경우 다음 문서를 검토 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md) 입니다.
- 검색, 평가 및 종속성 시각화에 대 한 [질문](common-questions-discovery-assessment.md) 입니다.


## <a name="what-is-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 란 무엇 인가요?

Azure Migrate 어플라이언스는 Azure Migrate: 서버 평가 도구를 사용 하 여 온-프레미스 서버를 검색 및 평가 하 고, 온-프레미스 VMware Vm의 에이전트 없는 마이그레이션을 위해 Azure Migrate: 서버 마이그레이션 도구에서 사용 하는 경량 어플라이언스입니다. 

어플라이언스는 VM 또는 물리적 컴퓨터로 온-프레미스에 배포 됩니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 Azure Migrate에 컴퓨터 메타 데이터 및 성능 데이터를 지속적으로 보냅니다. 어플라이언스 검색은 에이전트 없이 수행됩니다. 검색 된 컴퓨터에는 아무 것도 설치 되지 않습니다. 어플라이언스에 [대해 자세히 알아보세요](migrate-appliance.md) .

## <a name="how-does-the-appliance-connect-to-azure"></a>어플라이언스는 Azure에 어떻게 연결 되나요?

인터넷을 통해 연결 하거나 공용/Microsoft 피어 링에서 Azure Express 경로를 사용할 수 있습니다.

## <a name="does-appliance-analysis-impact-performance"></a>어플라이언스 분석이 성능에 영향을 미칩니까?

Azure Migrate 어플라이언스는 VM 성능 데이터를 계속 측정 하기 위해 온-프레미스 컴퓨터를 계속 프로 파일링 합니다. 이 프로 파일링은 Hyper-v/ESXi 호스트 또는 VMware vCenter Server의 성능에 거의 영향을 주지 않습니다.

### <a name="can-i-harden-the-appliance-vm"></a>어플라이언스 VM을 강화할 수 있나요?

다운로드 한 템플릿을 사용 하 여 어플라이언스 VM을 만들 때 Azure Migrate 어플라이언스에 필요한 통신 및 방화벽 규칙을 그대로 유지 하는 한 구성 요소 (예: 바이러스 백신)를 템플릿에 추가할 수 있습니다.


## <a name="what-network-connectivity-is-needed"></a>필요한 네트워크 연결은 무엇 인가요?

검토할 사항은 다음과 같습니다.
- 어플라이언스 VMware 평가: [URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements) 및 [포트](migrate-support-matrix-vmware.md#assessment-port-requirements) 액세스 요구 사항입니다.
- 어플라이언스 에이전트 없는 VMware 마이그레이션: [URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) 및 [포트](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) 액세스 요구 사항입니다.
- 어플라이언스 Hyper-v 평가: [URL](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) 및 [포트](migrate-support-matrix-hyper-v.md#assessment-port-requirements) 액세스 요구 사항입니다.


## <a name="what-data-does-the-appliance-collect"></a>기기가 수집 하는 데이터는 무엇 인가요?

수집 된 데이터를 검토 합니다.

- VMware VM [성능 데이터](migrate-appliance.md#collected-performance-data-vmware) 및 [메타 데이터](migrate-appliance.md#collected-metadata-vmware).
- Hyper-v VM [성능 데이터](migrate-appliance.md#collected-performance-data-hyper-v) 및 [메타 데이터](migrate-appliance.md#collected-metadata-hyper-v)


## <a name="how-is-data-stored"></a>데이터가 어떻게 저장되나요?

Azure Migrate 어플라이언스에 의해 수집 된 데이터는 마이그레이션 프로젝트를 만들 때 선택 하는 Azure 위치에 저장 됩니다. 

- 데이터는 Microsoft 구독에 안전 하 게 저장 되며 Azure Migrate 프로젝트를 삭제 하면 삭제 됩니다.
- [종속성 시각화](concepts-dependency-visualization.md)를 사용 하는 경우 수집 된 데이터는 Azure 구독에서 생성 된 Log Analytics 작업 영역에 미국에 저장 됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>연속 프로 파일링에서 업로드 되는 데이터의 양

Azure Migrate로 전송 되는 데이터의 볼륨은 여러 매개 변수에 따라 달라 집니다. 볼륨을 파악 하기 위해 10 대의 컴퓨터가 있는 Azure Migrate 프로젝트 (각각 하나의 디스크와 하나의 NIC)는 하루에 50 MB를 보냅니다. 이 값은 근사값 이며, Nic 및 디스크에 대 한 데이터 요소 수에 따라 변경 됩니다. 컴퓨터, Nic 또는 디스크 수가 증가 하는 경우 전송 되는 데이터의 증가는 비선형입니다.

## <a name="is-data-encrypted-at-restin-transit"></a>데이터는 미사용/전송 중에 암호화 되나요?

예, 둘 다

- 메타 데이터는 HTTPS를 통해 인터넷을 통해 Azure Migrate 서비스로 안전 하 게 전송 됩니다.
- 메타 데이터는 azure [Cosmos](../cosmos-db/database-encryption-at-rest.md) 데이터베이스 및 [azure Blob storage](../storage/common/storage-service-encryption.md)의 Microsoft 구독에 저장 됩니다. 메타 데이터는 미사용 암호화 됩니다.
- 종속성 분석에 대 한 데이터는 전송 중에도 암호화 됩니다 (보안 HTTPS). 구독의 Log Analytics 작업 영역에 저장 됩니다. 또한 미사용 암호화 됩니다.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>어플라이언스는 vCenter Server에 어떻게 연결 되나요?

1. 어플라이언스는 어플라이언스를 설정할 때 제공한 자격 증명을 사용 하 여 vCenter Server (포트 443)에 연결 됩니다.
2. 어플라이언스는 VMware PowerCLI를 사용 하 여 vCenter Server를 쿼리하고 vCenter Server에서 관리 하는 Vm에 대 한 메타 데이터를 수집 합니다.
3. 어플라이언스는 지난 달 동안 vm (코어, 메모리, 디스크, Nic) 및 각 VM의 성능 기록에 대 한 구성 데이터를 수집 합니다.
4. 수집 된 메타 데이터는 평가를 위해 서버 평가 (HTTPS를 통한 인터넷을 통해) Azure Migrate 됩니다.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>어플라이언스를 여러 vCenter 서버에 연결할 수 있나요?

아닙니다. 어플라이언스와 vCenter Server 사이에 일대일 매핑이 있습니다. 여러 vCenter Server 인스턴스에서 Vm을 검색 하려면 여러 어플라이언스를 배포 해야 합니다.

### <a name="how-many-vms-can-i-discover-with-an-appliance"></a>어플라이언스로 검색할 수 있는 Vm 수는 몇 개입니까?

단일 어플라이언스를 사용 하 여 최대 1만 VMware Vm 및 최대 5000 Hyper-v Vm을 검색할 수 있습니다. 온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 [hyper-v](scale-hyper-v-assessment.md) 및 [VMware](scale-vmware-assessment.md) 평가 크기 조정을 참조 하세요.

## <a name="can-i-delete-an-appliance"></a>어플라이언스를 삭제할 수 있나요?

프로젝트에서 현재 어플라이언스 삭제가 지원 되지 않습니다.

- 어플라이언스를 삭제 하는 유일한 방법은 기기와 연결 된 Azure Migrate 프로젝트를 포함 하는 리소스 그룹을 삭제 하는 것입니다.
- 그러나 리소스 그룹을 삭제 하면 리소스 그룹의 프로젝트와 연결 된 다른 등록 된 어플라이언스, 검색 된 인벤토리, 평가 및 기타 모든 Azure 구성 요소도 삭제 됩니다.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>다른 구독/프로젝트에서 어플라이언스를 사용할 수 있나요?

어플라이언스를 사용 하 여 검색을 시작한 후에는 다른 Azure 구독 또는 다른 Azure Migrate 프로젝트를 사용 하 여 다시 구성할 수 없습니다. 다른 vCenter Server에서 Vm을 검색할 수도 없습니다. 이러한 작업에 대해 새 어플라이언스를 설정 합니다.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Azure VM에서 어플라이언스를 설정할 수 있나요?
현재 지원되지 않습니다. 

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi 호스트에서 검색할 수 있나요?
VMware Vm을 검색 하는 vCenter Server 필요 하지 않습니다.

## <a name="how-do-i-update-the-appliance"></a>어플라이언스를 업데이트 어떻게 할까요??

기본적으로 어플라이언스와 설치 된 에이전트는 자동으로 업데이트 됩니다. 어플라이언스는 24 시간 마다 한 번씩 업데이트를 확인 합니다. 업데이트 프로세스 중에 오류가 발생 하면 다시 시도 프로세스가 수행 됩니다. 자동 업데이트는 어플라이언스 및 어플라이언스 에이전트만 업데이트 합니다. 운영 체제가 업데이트 되지 않았습니다. Microsoft 업데이트를 사용 하 여 운영 체제를 최신 상태로 유지 합니다.

## <a name="can-i-check-agent-health"></a>에이전트 상태를 확인할 수 있나요?

포털에서 서버 평가 또는 서버 마이그레이션 도구의 **에이전트 상태** 페이지로 이동 합니다. 여기에서 어플라이언스와 Azure의 검색 및 평가 에이전트 간에 연결 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
