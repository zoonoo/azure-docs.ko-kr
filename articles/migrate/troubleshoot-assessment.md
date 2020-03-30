---
title: Azure 마이그레이션에서 평가 및 종속성 시각화 문제 해결
description: Azure 마이그레이션에서 문제 해결 평가 및 종속성 시각화에 대한 도움말을 얻을 수 있습니다.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127671"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>평가/종속성 시각화 문제 해결

이 문서에서는 [Azure 마이그레이션: 서버 평가를](migrate-services-overview.md#azure-migrate-server-assessment-tool)통해 평가 및 종속성 시각화 문제를 해결하는 데 도움이 됩니다.


## <a name="assessment-readiness-issues"></a>평가 준비 문제

다음과 같이 평가 준비 문제를 해결합니다.

**문제** | **수정**
--- | ---
지원되지 않는 부팅 유형 | Azure는 EFI 부팅 유형의 VM을 지원하지 않습니다. 마이그레이션을 실행하기 전에 부팅 유형을 BIOS로 변환하는 것이 좋습니다. <br/><br/>Azure 마이그레이션 서버 마이그레이션을 사용하여 이러한 VM의 마이그레이션을 처리할 수 있습니다. 마이그레이션 하는 동안 VM의 부팅 형식을 BIOS로 변환 합니다.
조건부 지원 되는 Windows 운영 체제 | 운영 체제가 지원 종료 날짜를 지났으며 [Azure에서 지원을](https://aka.ms/WSosstatement)위해 CSA(사용자 지정 지원 계약)가 필요합니다. Azure로 마이그레이션하기 전에 업그레이드하는 것이 좋습니다.
지원되지 않는 Windows 운영 체제 | Azure는 [선택한 Windows OS 버전만](https://aka.ms/WSosstatement)지원합니다. Azure로 마이그레이션하기 전에 컴퓨터를 업그레이드하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure는 [선택한 Linux OS 버전만 보증합니다.](../virtual-machines/linux/endorsed-distros.md) Azure로 마이그레이션하기 전에 컴퓨터를 업그레이드하는 것이 좋습니다.
보증되지 않는 Linux OS | 컴퓨터가 Azure에서 시작될 수 있지만 Azure는 운영 체제 지원을 제공하지 않습니다. Azure로 마이그레이션하기 전에 [승인된 Linux 버전으로](../virtual-machines/linux/endorsed-distros.md) 업그레이드하는 것이 좋습니다.
알 수 없는 운영 체제 | VM의 운영 체제는 vCenter 서버에서 "기타"로 지정되었습니다. 이 동작은 Azure 마이그레이션이 VM의 Azure 준비 상태를 확인하지 못하도록 차단합니다. 컴퓨터를 마이그레이션하기 전에 Azure에서 운영 체제를 [지원해야](https://aka.ms/azureoslist) 합니다.
지원되지 않는 비트 버전 | 32비트 운영 체제가 있는 VM은 Azure에서 부팅할 수 있지만 Azure로 마이그레이션하기 전에 64비트로 업그레이드하는 것이 좋습니다.
Microsoft 비주얼 스튜디오 구독 필요 | 컴퓨터가 Visual Studio 구독을 통해서만 지원되는 Windows 클라이언트 운영 체제를 실행중입니다.
필요한 스토리지 성능에 대해 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 저장소 성능(초당 입력/출력 작업[IOPS] 및 처리량)이 Azure VM 지원을 초과합니다. 마이그레이션을 시작하기 전에 컴퓨터의 스토리지 요구 사항을 낮춰봅니다.
필요한 네트워크 성능에 대해 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정된 위치에 VM을 찾을 수 없습니다. | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
하나 이상의 부적절한 디스크 | VM에 연결된 디스크가 하나 이상 Azure 요구 사항을 충족하지 않습니다. A.<br/><br/> Azure 마이그레이션: 서버 평가는 현재 Ultra SSD 디스크를 지원하지 않으며 프리미엄 관리 디스크(32TB)의 디스크 제한을 기준으로 디스크를 평가합니다.<br/><br/> VM에 연결된 각 디스크에 대해 디스크 크기가 64TB(Ultra SSD 디스크에서 지원)< 있는지 확인합니다.<br/><br/> 그렇지 않은 경우 Azure로 마이그레이션하기 전에 디스크 크기를 줄이거나 Azure에서 여러 디스크를 사용하고 [함께 스트라이프하여](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) 저장소 제한을 높입니다. Azure 관리 가상 시스템 디스크에서 각 디스크에 필요한 성능(IOPS 및 처리량)이 지원되는지 [확인합니다.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure 마이그레이션: 서버 평가는 현재 Ultra SSD 디스크를 지원하지 않으며 프리미엄 디스크 제한(32TB)을 기준으로 디스크를 평가합니다.<br/><br/> 그러나 Azure는 최대 64TB 크기의 디스크를 지원합니다(Ultra SSD 디스크에서 지원). 마이그레이션 하기 전에 64 TB 미만으로 디스크를 축소 하거나 Azure에서 여러 디스크를 사용 하 고 더 높은 저장소 제한을 얻기 위해 [함께 스트라이프.](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 스토리지 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure에서 적합한 VM 유형을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크에 대한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터에 대한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.

## <a name="linux-vms-are-conditionally-ready"></a>리눅스 VM은 "조건부 준비"

서버 평가는 서버 평가에서 알려진 간격으로 인해 Linux VM을 "조건부 준비"로 표시합니다.

- 이 간격으로 인해 온-프레미스 VM에 설치된 Linux OS의 부 버전이 검색되지 않습니다.
- 예를 들어 RHEL 6.10의 경우 현재 서버 평가는 RHEL 6만 OS 버전으로 검색합니다.
-  Azure는 특정 버전의 Linux만 보증하므로 Linux VM은 현재 서버 평가에서 조건부로 준비된 것으로 표시됩니다.
- Azure [Linux 지원을](https://aka.ms/migrate/selfhost/azureendorseddistros)검토하여 온-프레미스 VM에서 실행되는 Linux OS가 Azure에서 승인되는지 여부를 확인할 수 있습니다.
-  승인된 배포를 확인한 후 이 경고를 무시할 수 있습니다.

## <a name="azure-skus-bigger-than-on-premises"></a>온-프레미스보다 더 큰 Azure SUS

Azure 마이그레이션 서버 평가는 평가 유형에 따라 현재 온-프레미스 할당보다 더 많은 코어와 메모리를 갖춘 Azure VM SUS를 추천할 수 있습니다.


- VM SKU 권장 사항은 평가 속성에 따라 다릅니다.
- 이는 서버 평가( *성능 기반*또는 *온-프레미스)에서*수행하는 평가 유형에 따라 영향을 받습니다.
- 성능 기반 평가의 경우 서버 평가는 온-프레미스 VM(CPU, 메모리, 디스크 및 네트워크 사용률)의 사용률 데이터를 고려하여 온-프레미스 VM에 적합한 대상 VM SKU를 결정합니다. 또한 효과적인 활용도를 결정할 때 편안함 요소를 추가합니다.
- 온-프레미스 크기 조정의 경우 성능 데이터는 고려되지 않으며 대상 SKU는 온-프레미스 할당을 기반으로 하는 것이 좋습니다.

이것이 권장 사항에 미치는 영향을 보여 드리기 위해 다음을 예로 들어 보겠습니다.

4개의 코어와 8GB의 메모리를 갖춘 온-프레미스 VM을 통해 CPU 사용률 50% 및 메모리 사용률 50%, 지정된 컴포트 팩터 1.3을 제공합니다.

-  평가가 **온-프레미스인**경우 4개의 코어와 8GB의 메모리가 있는 Azure VM SKU를 권장합니다.
- 평가가 효과적인 CPU 및 메모리 사용률(4코어 50% * 1.3 = 2.6코어 50%, 8GB 메모리 50%) * 1.3 = 5.3GB 메모리) 4개 코어중 가장 저렴한 VM SKU(가장 가까운 지원 코어 수) 및 8GB 메모리(가장 가까운 지원됨) 메모리 크기)를 권장합니다.
- 평가 크기 조정에 대해 [자세히 알아보세요.](concepts-assessment-calculation.md#types-of-assessments)

## <a name="azure-disk-skus-bigger-than-on-premises"></a>온-프레미스보다 큰 Azure 디스크 SUS

Azure 마이그레이션 서버 평가는 평가 유형에 따라 더 큰 디스크를 권장할 수 있습니다.
- 서버 평가의 디스크 크기 조정은 크기 조정 기준 및 저장소 유형이라는 두 가지 평가 속성에 따라 달라집니다.
- 크기 조정 기준이 **성능 기반이고**저장소 유형이 **자동**, IOPS로 설정된 경우 대상 디스크 유형(표준 HDD, 표준 SSD 또는 프리미엄)을 식별할 때 디스크의 처리량 값이 고려됩니다. 그런 다음 디스크 형식의 디스크 SKU를 권장하며 온-프레미스 디스크의 크기 요구 사항을 고려하는 것이 좋습니다.
- 크기 조정 기준이 **성능 기반이고**저장소 유형이 **프리미엄인**경우 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항을 기반으로 Azure의 프리미엄 디스크 SKU를 권장합니다. 동일한 논리는 크기 조정 기준이 **온-프레미스이고** 저장소 유형이 표준 **HDD,** **표준 SSD**또는 **Premium인**경우 디스크 크기 조정을 수행하는 데 사용됩니다.

예를 들어 32GB의 메모리가 있는 온-프레미스 디스크가 있지만 디스크에 대한 집계된 읽기 및 쓰기 IOPS가 800 IOPS인 경우 서버 평가는 상위 IOPS 요구 사항으로 인해 프리미엄 디스크를 권장한 다음 필요한 IOPS 및 크기. 이 예제에서 가장 가까운 일치 항목은 P15(256GB, 1100IOPS)입니다. 온-프레미스 디스크에 필요한 크기가 32GB였음에도 불구하고 서버 평가는 온-프레미스 디스크의 IOPS 요구 사항이 높기 때문에 더 큰 디스크를 권장합니다.

## <a name="utilized-corememory-percentage-missing"></a>활용 코어/메모리 백분율 누락

Azure 마이그레이션 어플라이언스가 관련 온-프레미스 VM에 대한 성능 데이터를 수집할 수 없는 경우 서버 평가보고서 "백분율사용률누락" 또는 "백분율메모리활용 누락"

- 평가 기간 동안 VM이 꺼져 있는 경우 이러한 일이 발생할 수 있습니다. 어플라이언스는 꺼져 있을 때 VM에 대한 성능 데이터를 수집할 수 없습니다.
- 메모리 카운터만 누락되어 Hyper-V VM을 평가하려는 경우 이러한 VM에서 동적 메모리를 사용하도록 설정했는지 확인합니다. Azure 마이그레이션 어플라이언스가 동적 메모리를 사용하도록 설정하지 않은 VM에 대한 메모리 사용률 데이터를 수집할 수 없는 Hyper-V VM에대해서만 알려진 문제가 있습니다.
- 성능 카운터가 없는 경우 Azure 마이그레이션 서버 평가는 할당된 코어 및 메모리로 돌아가므로 해당 VM 크기를 권장합니다.
- 모든 성능 카운터가 없는 경우 평가를 위한 포트 액세스 요구 사항이 충족되었는지 확인합니다. [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) 및 [물리적](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) 서버 평가에 대한 포트 액세스 요구 사항에 대해 자세히 알아봅니다.

## <a name="is-the-operating-system-license-included"></a>운영 체제 라이센스가 포함되어 있습니까?

Azure 마이그레이션 서버 평가는 현재 Windows 컴퓨터에 대해서만 운영 체제 라이센스 비용을 고려합니다. Linux 컴퓨터에 대한 라이선스 비용은 현재 고려되지 않습니다.

## <a name="how-does-performance-based-sizing-work"></a>성능 기반 크기 조정은 어떻게 작동합니까?

Server Assessment는 온-프레미스 머신의 성능 데이터를 지속적으로 수집하여 Azure에서 VM SKU 및 디스크 SKU를 추천하는 데 사용합니다. 성능 기반 데이터가 수집되는 [방법에 대해 알아봅니다.](concepts-assessment-calculation.md#calculate-sizing-performance-based)


## <a name="dependency-visualization-in-azure-government"></a>Azure 정부의 종속성 시각화

Azure 마이그레이션은 종속성 시각화 기능에 대한 서비스 맵에 따라 달라집니다. 현재 Azure 정부에서는 서비스 맵을 사용할 수 없으므로 Azure 정부에서는 이 기능을 사용할 수 없습니다.

## <a name="dependencies-dont-show-after-agent-install"></a>에이전트 설치 후 종속성이 표시되지 않습니다.

온-프레미스 VM에 종속성 시각화 에이전트를 설치한 후 Azure Migrate는 일반적으로 포털에 종속성을 표시하는 데 15~30분이 걸립니다. 30분 이상 기다린 경우 MMA(Microsoft 모니터링 에이전트)가 Log Analytics 작업 영역에 연결할 수 있는지 확인합니다.

Windows VM의 경우
1. 제어판에서 MMA를 시작합니다.
2. Microsoft **모니터링 에이전트 속성** > **Azure Log 분석(OMS)에서**작업 영역의 **상태가** 녹색인지 확인합니다.
3. 상태가 녹색이 아닌 경우 작업 영역을 제거하고 MMA에 다시 추가해 보십시오.

    ![MMA 상태](./media/troubleshoot-assessment/mma-properties.png)

Linux VM의 경우 MMA 및 종속성 에이전트에 대한 설치 명령이 성공했는지 확인합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

- **MMS 에이전트**: 지원되는 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)및 [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) 운영 체제를 검토합니다.
- **종속성 에이전트**: 지원되는 [Windows 및 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 운영 체제입니다.

## <a name="visualize-dependencies-for--hour"></a>> 시간 동안 종속성 시각화

Azure Migrate를 사용하면 지난 달의 특정 날짜로 돌아갈 수 있지만 종속성을 시각화할 수 있는 최대 기간은 1시간입니다.

예를 들어 종속성 맵의 시간 지속 시간 기능을 사용하여 어제의 종속성을 볼 수 있지만 한 시간 동안만 볼 수 있습니다.

그러나 Azure Monitor 로그를 사용하여 더 긴 기간 동안 [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 수 있습니다.

## <a name="visualized-dependencies-for--10-machines"></a>> 10대의 컴퓨터에 대한 시각화된 종속성

Azure 마이그레이션 서버 평가에서 최대 10개의 VM이 있는 [그룹에 대한 종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 대규모 그룹의 경우 VM을 더 작은 그룹으로 분할하여 종속성을 시각화하는 것이 좋습니다.

## <a name="machines-show-install-agent"></a>컴퓨터에 "에이전트 설치"가 표시됩니다.

Azure로 종속성 시각화를 사용하도록 설정한 컴퓨터를 마이그레이션한 후 다음 동작으로 인해 "종속성 보기" 대신 "에이전트 설치" 작업이 표시될 수 있습니다.


- Azure로 마이그레이션한 후 온-프레미스 컴퓨터가 꺼지고 해당 VM이 Azure에서 회전합니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다.
- 온-프레미스 IP 주소를 유지했는지 여부에 따라 컴퓨터에 다른 IP 주소가 있을 수도 있습니다.
- MAC 및 IP 주소가 온-프레미스와 다른 경우 Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결하지 않습니다. 이 경우 종속성을 보는 대신 에이전트를 설치하는 옵션이 표시됩니다.
- Azure로 테스트 마이그레이션한 후 온-프레미스 컴퓨터는 예상대로 켜져 있습니다. Azure에서 스펀즈된 등가 컴퓨터는 서로 다른 MAC 주소를 획득하고 다른 IP 주소를 획득할 수 있습니다. 이러한 컴퓨터에서 나가는 Azure Monitor 로그 트래픽을 차단하지 않는 한 Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결하지 않으므로 종속성을 보는 대신 에이전트를 설치하는 옵션이 표시됩니다.


## <a name="capture-network-traffic"></a>네트워크 트래픽 캡처

다음과 같이 네트워크 트래픽 로그를 수집합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 개발자 도구를 시작하려면 F12를 누릅니다. 필요한 경우 **탐색 설정에서 지우기 항목을** 지웁울 수 있습니다.
3. **네트워크** 탭을 선택하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽이 캡처되고 있음을 나타냅니다. 빨간색 원이 나타나지 않으면 시작할 검은색 원을 선택합니다.
   - Microsoft Edge 및 인터넷 익스플로러에서 기록이 자동으로 시작됩니다. 그렇지 않으면 녹색 재생 버튼을 선택합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서 마우스 오른쪽 단추를 클릭하고 **콘텐츠가 있는 HAR로 저장을**선택합니다. 이 작업은 로그를 압축하고 .har 파일로 내보올 수 있습니다.
   - Microsoft Edge 또는 인터넷 익스플로러에서 **캡처한 트래픽 내보내기** 옵션을 선택합니다. 이 작업은 로그를 압축하고 내보전합니다.
6. **콘솔** 탭을 선택하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. 내보낼 **로 저장을**선택하고 로그를 압축합니다.
   - Microsoft Edge 또는 인터넷 익스플로러에서 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사를**선택합니다.
7. 개발자 도구를 닫습니다.

## <a name="next-steps"></a>다음 단계

평가를 [만들거나](how-to-create-assessment.md) [사용자 지정합니다.](how-to-modify-assessment.md)
