---
title: Azure HANA 대형 인스턴스가 Azure 포털을 통해 제어 | 마이크로 소프트 문서
description: 포털을 통해 Azure HANA 대형 인스턴스를 식별하고 상호 작용하는 방법을 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099809"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어
이 문서에서는 Azure [Portal에서](https://portal.azure.com) [HANA 대형 인스턴스를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 제공하는 방법과 배포된 HANA 대형 인스턴스 단위를 통해 Azure 포털을 통해 수행할 수 있는 활동을 설명합니다. Azure 포털에서 HANA 대형 인스턴스의 가시성은 현재 공개 미리 보기 중인 HANA 대형 인스턴스에 대한 Azure 리소스 공급자를 통해 제공됩니다.

## <a name="register-hana-large-instance-resource-provider"></a>HANA 대형 인스턴스 리소스 공급자 등록
일반적으로 HANA 대형 인스턴스 배포에 사용 하 던 Azure 구독 HANA 큰 인스턴스 리소스 공급자에 대 한 등록 됩니다. 그러나 HANA 대형 인스턴스 단위를 배포한 것을 볼 수 없는 경우 Azure 구독에서 리소스 공급자를 등록해야 합니다. HANA 대형 인스턴스 리소스 공급자를 등록하는 방법에는 두 가지가 있습니다.

### <a name="register-through-cli-interface"></a>CLI 인터페이스를 통해 등록
Azure CLI 인터페이스를 통해 HANA 대형 인스턴스 배포에 사용되는 Azure 구독에 로그인해야 합니다. 다음 명령을 통해 HANA 대형 인스턴스 공급자를 다시 등록할 수 있습니다.
    
    az provider register --namespace Microsoft.HanaOnAzure

자세한 내용은 [Azure 리소스 공급자 및 형식을](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli) 참조하세요.


### <a name="register-through-azure-portal"></a>Azure 포털을 통해 등록
Azure 포털을 통해 HANA 대형 인스턴스 리소스 공급자를 다시 등록할 수 있습니다. Azure 포털에 구독을 나열하고 HANA 대형 인스턴스 단위를 배포하는 데 사용된 구독을 두 번 클릭해야 합니다. 구독의 개요 페이지에 있는 하나, 아래와 같이 "리소스 공급자"를 선택하고 검색 창에 "HANA"를 입력합니다. 

![Azure 포털을 통해 HLI RP 등록](./media/hana-li-portal/portal-register-hli-rp.png)

표시된 스크린샷에서 리소스 공급자가 이미 등록되었습니다. 리소스 공급자가 아직 등록되지 않은 경우 "다시 등록" 또는 "등록"을 누릅니다.

자세한 내용은 [Azure 리소스 공급자 및 형식을](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell) 참조하세요.


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure 포털에서 HANA 대형 인스턴스 단위 표시
HANA 대형 인스턴스 배포 요청을 제출할 때 HANA 대형 인스턴스에 연결하는 Azure 구독도 지정하라는 메시지가 표시됩니다. HANA 대형 인스턴스 단위에 대해 작동하는 SAP 응용 프로그램 계층을 배포하는 데 사용하는 것과 동일한 구독을 사용하는 것이 좋습니다.
첫 번째 HANA 대형 인스턴스가 배포되면 HANA 대형 인스턴스에 대한 배포 요청에 제출한 Azure 구독에 새 [Azure 리소스 그룹이](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 만들어집니다.  새 리소스 그룹에는 특정 구독에 배포한 모든 HANA 대형 인스턴스 단위가 나열됩니다.

새 Azure 리소스 그룹을 찾으려면 Azure 포털의 왼쪽 탐색 창을 탐색하여 구독에 리소스 그룹을 나열합니다.

![Azure 포털의 탐색 창](./media/hana-li-portal/portal-resource-group.png)

리소스 그룹 목록에서 나열되는 경우 HANA 대형 인스턴스를 배포하는 데 사용한 구독을 필터링해야 할 수 있습니다.

![Azure 포털에서 리소스 그룹 필터링](./media/hana-li-portal/portal-filtering-subscription.png)

올바른 구독으로 필터링한 후에도 리소스 그룹의 긴 목록이 있을 수 있습니다. "xxx"가 **-T050과**같은 세 자리인 **-Txxx의** 사후 수정이 있는 것을 찾습니다. 

리소스 그룹을 찾았을 때 리소스 그룹의 세부 정보를 나열합니다. 받은 목록은 다음과 같습니다.

![Azure 포털의 HLI 목록](./media/hana-li-portal/portal-hli-units-list.png)

나열된 모든 단위는 구독에 배포된 단일 HANA 대형 인스턴스 단위를 나타냅니다. 이 경우 구독에 배포된 8개의 다른 HANA 대형 인스턴스 단위를 살펴봅니다.

동일한 Azure 구독에서 여러 HANA 대형 인스턴스 테넌을 배포한 경우 여러 Azure 리소스 그룹을 찾을 수 있습니다. 


## <a name="look-at-attributes-of-single-hli-unit"></a>단일 HLI 단위의 속성 보기
HANA 대형 인스턴스 단위 목록에서 단일 단위를 클릭하고 단일 HANA 대형 인스턴스 단위의 세부 정보를 확인할 수 있습니다. 

개요 화면에서 '더 보기'를 클릭한 후 다음과 같은 장치의 프레젠테이션을 얻게 됩니다.

![HLI 장치의 개요 표시](./media/hana-li-portal/portal-show-overview.png)

표시된 다른 특성을 살펴보면 이러한 특성은 Azure VM 특성과 거의 다르지 않습니다. 왼쪽 헤더에는 리소스 그룹, Azure 지역, 구독 이름 및 ID와 추가한 일부 태그가 표시됩니다. 기본적으로 HANA 큰 인스턴스 단위에는 태그가 할당되지 않습니다. 헤더의 오른쪽에는 배포가 완료될 때 장치 이름이 할당된 것으로 나열됩니다. 운영 체제는 IP 주소뿐만 아니라 표시됩니다. VM과 마찬가지로 CPU 스레드 및 메모리 수가 있는 HANA 대형 인스턴스 단위 유형도 표시됩니다. 다른 HANA 대형 인스턴스 단위에 대한 자세한 내용은 다음과 같습니다.

- [HLI에 사용 가능한 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA(대규모 인스턴스) 스토리지 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

오른쪽 하단의 추가 데이터는 HANA 대형 인스턴스 스탬프의 개정입니다. 가능한 값은 다음과 같습니다.

- 개정 3
- 개정 4

개정 4는 Azure VM과 개정 4 스탬프 또는 행에 배포된 HANA 대형 인스턴스 단위 간의 네트워크 대기 시간이 크게 개선된 HANA 대형 인스턴스의 최신 아키텍처입니다.
또 다른 매우 중요한 정보는 배포된 각 HANA 대형 인스턴스 단위에 대해 자동으로 생성되는 Azure 근접 배치 그룹의 이름으로 개요의 오른쪽 하단에 있습니다. SAP 응용 프로그램 계층을 호스트하는 Azure VM을 배포할 때 이 근접 배치 그룹을 참조해야 합니다. HANA 대형 인스턴스 장치와 연결된 [Azure 근접 배치 그룹을](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) 사용하면 Azure VM이 HANA 대형 인스턴스 단위에 근접하여 배포되는지 확인해야 합니다. 수정본 4 호스팅HANA 대형 인스턴스 단위와 동일한 Azure 데이터 센터에서 SAP 응용 프로그램 계층을 찾는 데 근접 배치 그룹을 사용하는 방법은 [SAP 응용 프로그램과 의 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹에 설명되어 있습니다.](sap-proximity-placement-scenarios.md)

헤더의 오른쪽 열에 있는 추가 필드는 HANA 대형 인스턴스 장치의 전원 상태에 대해 알립니다.

> [!NOTE]
> 전원 상태는 하드웨어 장치의 전원 켜기 또는 끄기 여부를 설명합니다. 운영 체제가 실행되고 있는 것에 대한 정보는 제공하지 않습니다. HANA 대형 인스턴스 단위를 다시 시작하면 단위 상태가 **시작**상태로 이동하기 **시작으로** 변경되는 작은 시간이 발생합니다. **시작** 상태가 된다는 것은 OS가 시작되었거나 OS가 완전히 시작되었다는 것을 의미합니다. 결과적으로 장치를 다시 시작한 후에는 상태가 **시작으로**전환되는 즉시 장치에 즉시 로그인할 수 없습니다.
> 

'더 보기'를 누르면 추가 정보가 표시됩니다. 한 가지 추가 정보는 HANA 대형 인스턴스 스탬프의 개정을 표시하는 것입니다. HANA 대형 인스턴스 스탬프의 다른 개정판에 대한 [Azure(대형 인스턴스)의 SAP HANA란](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 문서 참조

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>단일 HANA 대형 인스턴스 단위의 활동 확인 
HANA 대형 인스턴스 단위의 개요를 제공하는 것 외에도 특정 단위의 활동을 확인할 수 있습니다. 활동 로그는 다음과 같습니다.

![Azure 포털의 탐색 창](./media/hana-li-portal/portal-activity-list.png)

기록된 주요 활동 중 하나는 단위의 다시 시작입니다. 나열된 데이터에는 활동 상태, 활동이 트리거된 타임스탬프, 활동이 트리거된 구독 ID 및 활동을 트리거한 Azure 사용자가 포함됩니다. 

기록되는 또 다른 활동은 Azure 메타 데이터의 단위변경입니다. 다시 시작 하는 것 외에도 **HANAInstances 쓰기의**활동을 볼 수 있습니다. 이 유형의 활동은 HANA 대형 인스턴스 단위 자체에서 변경을 수행하지 않지만 Azure에서 단위의 메타 데이터에 대한 변경 내용을 문서화하고 있습니다. 나열된 경우 태그를 추가하고 삭제했습니다(다음 섹션 참조).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>HANA 대형 인스턴스 장치에 Azure 태그 추가 및 삭제
다른 가능성은 HANA 대형 인스턴스 단위에 [태그를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 추가하는 것입니다. 태그가 할당되는 방식은 VM에 태그를 할당하는 것과 다르지 않습니다. VM과 마찬가지로 태그는 Azure 메타 데이터에 존재하며 HANA 대형 인스턴스의 경우 VM에 대한 태그와 동일한 제한이 있습니다.

태그를 삭제하는 것은 VM과 동일한 방식으로 작동합니다. 태그를 적용하고 삭제하는 두 활동 모두 특정 HANA 대형 인스턴스 단위의 활동 로그에 나열됩니다.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA 대형 인스턴스 단위의 속성 확인
**속성** 섹션에는 인스턴스가 귀하에게 넘겨줄 때 얻을 수 있는 중요한 정보가 포함되어 있습니다. 지원 사례에서 필요할 수 있는 모든 정보를 얻거나 저장소 스냅숏 구성을 설정할 때 필요한 모든 정보를 얻을 수 있는 섹션입니다. 따라서 이 섹션은 인스턴스 주위의 데이터 컬렉션, Azure및 저장소 백 엔드에 대한 인스턴스의 연결입니다. 섹션의 맨 위쪽은 다음과 같습니다.


![Azure 포털에서 HLI 속성의 최상위 부분](./media/hana-li-portal/portal-properties-top.png)

처음 몇 가지 데이터 항목, 당신은 이미 개요 화면에서 본. 그러나 데이터의 중요한 부분은 첫 번째 배포 된 단위가 인계될 때 얻은 ExpressRoute 회로 ID입니다. 일부 지원 의 경우 해당 데이터에 대한 메시지가 표시됩니다. 중요한 데이터 항목은 스크린샷 하단에 표시됩니다. 표시되는 데이터는 HANA 대형 인스턴스 스택의 **테넌트에** 저장소를 격리하는 NFS 저장소 헤드의 IP 주소입니다. 이 IP 주소는 [저장소 스냅숏 백업을 위해 구성 파일을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)편집할 때도 필요합니다. 

속성 창에서 아래로 스크롤하면 HANA 대형 인스턴스 단위의 고유 리소스 ID 또는 배포에 할당된 구독 ID와 같은 추가 데이터가 표시됩니다.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Azure 포털을 통해 HANA 대형 인스턴스 단위 다시 시작
Linux 운영 체제의 다시 시작을 시작하면서 OS가 성공적으로 다시 시작을 완료할 수 없는 다양한 상황이 있었습니다. 다시 시작하려면 Microsoft 작업이 HANA 대형 인스턴스 단위의 전원 을 다시 시작하도록 서비스 요청을 열어야 했습니다. HANA 대형 인스턴스 단위의 전원 다시 시작 기능이 이제 Azure 포털에 통합됩니다. HANA 대형 인스턴스 단위의 개요 부분에 있는 동안 데이터 섹션 맨 위에 다시 시작하기 위한 버튼이 표시됩니다.

![Azure 포털에서 단계 #1 다시 시작](./media/hana-li-portal/portal-restart-first-step.png)

다시 시작 버튼을 누르면 장치를 실제로 다시 시작할지 묻는 메시지가 표시됩니다. "예"버튼을 눌러 확인하면 장치가 다시 시작됩니다.

> [!NOTE]
> 다시 시작 프로세스에서는 단위 상태가 **시작**상태로 이동하도록 **변경되는** 작은 시간을 경험하게 됩니다. **시작** 상태가 된다는 것은 OS가 시작되었거나 OS가 완전히 시작되었다는 것을 의미합니다. 결과적으로 장치를 다시 시작한 후에는 상태가 **시작으로**전환되는 즉시 장치에 즉시 로그인할 수 없습니다.

> [!IMPORTANT]
> HANA 대형 인스턴스 장치의 메모리 양에 따라 하드웨어 및 운영 체제의 재시작 및 재부팅에 최대 1시간이 걸릴 수 있습니다.


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA 대규모 인스턴스에 대한 지원 요청 열기
HANA 대형 인스턴스 단위의 Azure 포털 표시에서 HANA 대형 인스턴스 단위에 대해 특별히 지원 요청을 만들 수도 있습니다. 링크를 따라가면서 **새로운 지원 요청** 

![Azure 포털에서 서비스 요청 단계 #1 시작](./media/hana-li-portal/portal-initiate-support-request.png)

다음 화면에 나열된 SAP HANA 대형 인스턴스의 서비스를 받으려면 아래와 같이 '모든 서비스'를 선택해야 할 수 있습니다.

![Azure 포털의 모든 서비스 선택](./media/hana-li-portal/portal-create-service-request.png)

서비스 목록에서 **서비스 SAP HANA 대형 인스턴스를**찾을 수 있습니다. 해당 서비스를 선택하면 다음과 같이 특정 문제 유형을 선택할 수 있습니다.


![Azure 포털에서 문제 클래스 선택](./media/hana-li-portal/portal-select-problem-class.png)

각의 서로 다른 문제 유형에서 문제를 더 특성화하기 위해 선택해야 하는 문제 하위 유형의 선택이 제공됩니다. 하위 유형을 선택한 후 이제 제목의 이름을 지정할 수 있습니다. 선택 프로세스가 완료되면 생성의 다음 단계로 이동할 수 있습니다. **솔루션** 섹션에서는 HANA 대형 인스턴스에 대한 설명서를 가리키며, 이 문서는 문제 해결에 대한 포인터를 제공할 수 있습니다. 제안된 설명서에서 문제에 대한 해결책을 찾을 수 없는 경우 다음 단계로 이동합니다. 다음 단계에서는 문제가 VM에 있는지 또는 HANA 대형 인스턴스 단위인지 묻는 메시지가 표시됩니다. 이 정보는 지원 요청을 올바른 전문가에게 전달하는 데 도움이 됩니다. 

![Azure 포털의 지원 사례에 대한 세부 정보](./media/hana-li-portal/portal-support-request-details.png)

질문에 답변하고 추가 세부 정보를 제공하면 다음 단계로 이동하여 지원 요청을 검토하고 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 SAP HANA(대규모 인스턴스)를 모니터링하는 방법](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 쪽에서 모니터링 및 문제 해결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

