---
title: Azure Migrate 정보 | Microsoft Azure
description: Azure Migrate 서비스의 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 03/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7f0b3a0f63b87928938e5c0e9d39cc49c0fc791d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999960"
---
# <a name="about-azure-migrate"></a>Azure Migrate 정보

Azure Migrate 서비스는 Azure로 마이그레이션하는 온-프레미스 워크로드를 평가합니다. 이 서비스는 온-프레미스 머신의 마이그레이션 적합성을 평가하고, 성능 기반 크기 조정을 수행하며, Azure에서 온-프레미스 머신을 실행하는 비용을 예측합니다. 리프트 앤 시프트 마이그레이션을 고려 중이거나 마이그레이션의 초기 평가 단계에 있는 경우 이 서비스가 가장 이상적입니다. 평가 후에는 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 및 [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) 같은 서비스를 사용하여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.

## <a name="why-use-azure-migrate"></a>Azure Migrate를 사용하는 이유

Azure Migrate의 기능:

- **Azure 준비 상태 평가**: 온-프레미스 머신이 Azure에서 실행하기에 적합한지 여부를 평가합니다.
- **크기 권장 사항 가져오기**: 온-프레미스 VM의 성능 기록을 기반으로 Azure VM에 대한 권장 크기를 확인합니다.
- **월간 비용 예측**: Azure에서 온-프레미스 머신을 실행하는 데 드는 예상 비용을 확인합니다.  
- **매우 확실한 마이그레이션**: 온-프레미스 머신의 종속성을 시각화하여 함께 평가하고 마이그레이션할 머신 그룹을 만듭니다.

## <a name="current-limitations"></a>현재 제한 사항

- 온-프레미스 VMware VM(가상 머신)에서 Azure VM으로의 마이그레이션만 평가할 수 있습니다. VMware VM은 vCenter Server(버전 5.5, 6.0, 6.5 또는 6.7)에서 관리되어야 합니다.
- Hyper-V 지원은 프로덕션 지원을 통해 현재 미리 보기로 제공되고 있습니다. 사용해 보고 싶다면 [여기](https://aka.ms/migratefuture)에서 가입하세요.
- 실제 서버의 평가를 위해 [파트너 도구](https://azure.microsoft.com/migration/partners/)를 활용할 수 있습니다.
- 단일 검색에서 최대 1500대의 VM을 검색하고, 단일 프로젝트에서 최대 1500대의 VM을 검색할 수 있습니다. 또한 단일 평가에서 최대 1500대의 VM을 평가할 수 있습니다.
- 대규모 환경을 검색하려면 검색을 분할하고 여러 프로젝트를 만들면 됩니다. [자세히 알아보기](how-to-scale-assessment.md). Azure Migrate는 구독당 최대 20개의 프로젝트를 지원합니다.
- 마이그레이션을 평가하는 경우 Azure Migrate는 관리 디스크만 지원합니다.
-  다음 지역에서만 Azure Migrate 프로젝트를 만들 수 있지만, 여전히 다른 대상 Azure 위치를 평가할 수 있습니다.

    **지리** | **스토리지 위치**
    --- | ---
    Azure Government | 미국 정부 버지니아
    아시아 | 동남 아시아 또는 동아시아
    유럽 | 북유럽 또는 유럽 서부
    미국 | 미국 동부 또는 미국 중서부

    마이그레이션 프로젝트와 연결된 지역은 온-프레미스 환경에서 발견된 메타데이터를 저장하는 데 사용됩니다. 메타데이터는 마이그레이션 프로젝트에 지정된 지역에 따라 지역 중 하나에 저장됩니다. 새 Log Analytics 작업 영역을 만들어서 종속성 시각화를 사용할 경우 작업 영역은 프로젝트와 같은 지역에 생성됩니다.
- Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.


## <a name="what-do-i-need-to-pay-for"></a>어떤 요금이 발생하나요?

Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>평가의 내용

평가 설정은 필요에 따라 사용자 지정할 수 있습니다. 평가 속성은 다음 표에 요약되어 있습니다.

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 위치입니다.<br/><br/>현재 Azure Migrate는 마이그레이션 대상 위치로 33개 지역을 지원합니다. [지역을 확인합니다](https://azure.microsoft.com/global-infrastructure/services/). 기본적으로 대상 지역은 미국 동부로 설정되어 있습니다.
**저장소 유형** | 평가의 일부인 모든 VM에 대해 할당하려는 관리형 디스크의 형식입니다. 크기 조정 기준이 *온-프레미스 크기 조정으로*인 경우 프리미엄 디스크(기본값), 표준 SSD 디스크 또는 표준 HDD 디스크 중에서 대상 디스크 종류를 지정할 수 있습니다. *성능 기반 크기 조정*의 경우, 위의 옵션과 함께 자동화를 선택할 수 있는 옵션이 있습니다. 이는 VM의 성능 데이터에 따라 디스크 크기 권장 사항이 자동으로 이루어집니다. 예를 들어 [99.9%의 단일 인스턴스 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)를 달성하려는 경우, 저장소 유형을 프리미엄 관리 디스크로 지정하고자 할 수도 있습니다. 즉, 평가 시 모든 디스크가 프리미엄 관리 디스크로 권장됩니다. 마이그레이션 평가의 경우 Azure Migrate는 관리 디스크만을 지원합니다.
**예약 인스턴스** |  Azure에 [예약 인스턴스](https://azure.microsoft.com/pricing/reserved-vm-instances/)가 있는지 여부입니다. Azure Migrate는 그에 따라 비용을 예측합니다.
**크기 조정 기준** | 온-프레미스 VM의 **성능 기록**에 따라 크기를 조정할 수도 있고(기본값), 성능 기록을 고려하지 않고 **온-프레미스로** VM의 크기를 조정할 수도 있습니다.
**성능 기록** | 기본적으로 Azure Migrate는 백분위 값이 95%인 마지막 날의 성능 기록을 사용하여 온-프레미스 머신의 성능을 평가합니다.
**쾌적 인자** | Azure Migrate는 평가 중에 버퍼(쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다. 기본 쾌적 설정은 1.3x입니다.
**VM 시리즈** | 크기 예측에 사용되는 VM 시리즈입니다. 예를 들어 Azure에서 A 시리즈 VM으로 마이그레이션하지 않으려는 프로덕션 환경이 있는 경우 목록 또는 시리즈에서 A 시리즈를 제외할 수 있습니다. 크기 조정은 선택한 시리즈만을 기반으로 합니다.   
**통화** | 청구 통화입니다. 기본값은 미국 달러입니다.
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. 기본 설정은 0%입니다.
**VM 작동 시간** | VM이 Azure에서 24x7 실행되지 않는 경우 VM이 실행될 기간(월당 일 수 및 일당 시간 수)을 지정하면 그에 따라 비용이 계산됩니다. 기본값은 월당 31일이며 일당 24시간입니다.
**Azure 제품** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. Azure Migrate는 그에 따라 비용을 예측합니다.
**Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 할인된 가격으로 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 받을 수 있는지 여부입니다.

## <a name="how-does-azure-migrate-work"></a>Azure Migrate의 작동 방식

1. 사용자가 Azure Migrate 프로젝트를 만듭니다.
2. Azure Migrate는 수집기 어플라이언스라고 하는 온-프레미스 VM을 사용하여 온-프레미스 컴퓨터에 대한 정보를 검색합니다. 어플라이언스를 만들려면 Open Virtualization Appliance (.ova) 형식의 설치 파일을 다운로드하여 온-프레미스 vCenter Server에서 VM으로 가져옵니다.
3. vCenter Server의 VM에 연결하고 연결하는 동안 여기에 새 암호를 지정합니다.
4. 검색을 시작하려면 VM에서 수집기를 실행합니다.
5. 수집기는 VMware PowerCLI cmdlet을 사용하여 VM 메타데이터를 수집합니다. 검색은 에이전트 없이 수행되며, 및 VMware 호스트 또는 VM에 어떠한 것도 설치하지 않습니다. 수집된 메타데이터에는 VM 정보(코어, 메모리, 디스크, 디스크 크기 및 네트워크 어댑터)가 포함됩니다. 수집기는 CPU 및 메모리 사용량, 디스크 IOPS, 디스크 처리량(MBps) 및 네트워크 출력(MBps)을 포함하여 VM에 대한 성능 데이터도 수집합니다.
5. 메타데이터는 Azure Migrate 프로젝트에 푸시됩니다. Azure Portal에서 메타데이터를 볼 수 있습니다.
6. 평가를 위해 검색한 VM을 그룹으로 모읍니다. 예를 들면 동일한 응용 프로그램을 실행하는 VM을 그룹화할 수 있습니다. 더 상세한 그룹화를 위해 종속성 시각화를 사용하여 특정 컴퓨터나 그룹의 모든 시스템에 대한 종속성을 보고 그룹을 구체화할 수 있습니다.
7. 그룹을 정의한 후에 이에 대해 평가합니다.
8. 평가가 완료되면 포털에서 보거나 Excel 형식으로 다운로드할 수 있습니다.

   ![Azure Migrate 아키텍처](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>포트 요구 사항이 어떻게 됩니까?

다음 표에 Azure Migrate 통신에 필요한 포트가 요약되어 있습니다.

| 구성 요소 | 통신 대상 |  세부 정보 |
| --- | --- |--- |
|데이터 수집기  | Azure Migrate 서비스 | 수집기는 SSL 포트 443을 통해 서비스에 연결합니다.|
|데이터 수집기 | vCenter Server | 기본적으로 수집기는 443 포트로 vCenter 서버에 연결합니다. 서버가 다른 포트에서 수신 대기하는 경우 수집기 VM에서 송신 포트로 구성합니다.|
|온-프레미스 VM | Log Analytics 작업 영역 | [MMA(Microsoft Monitoring Agent)](../log-analytics/log-analytics-windows-agent.md)는 TCP 포트 443을 사용하여 Log Analytics 로그에 연결합니다. MMA 에이전트가 필요한 종속성 시각화를 사용하는 경우 이 포트가 필요합니다.|


## <a name="what-happens-after-assessment"></a>평가 후 어떻게 되나요?

온-프레미스 머신을 평가한 후에 몇 가지 도구를 사용하여 마이그레이션을 수행할 수 있습니다.

- **Azure Site Recovery**: Azure Site Recovery를 사용하여 Azure에 마이그레이션할 수 있습니다. 이렇게 하려면 저장소 계정 및 가상 네트워크를 비롯하여 필요한 [Azure 구성 요소를 준비합니다](../site-recovery/tutorial-prepare-azure.md). 온-프레미스에서 [VMware 환경을 준비합니다](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). 모든 준비가 완료되면 Azure로 복제를 설정하고 사용하여 VM을 마이그레이션합니다. [자세히 알아보기](../site-recovery/vmware-azure-tutorial.md).
- **Azure Database Migration**: 온-프레미스 머신이 SQL Server, MySQL 또는 Oracle과 같은 데이터베이스를 실행 중인 경우 [Azure Database Migration Service](../dms/dms-overview.md)를 사용하여 Azure로 마이그레이션할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [자습서에 따라](tutorial-assessment-vmware.md) 온-프레미스 VMware VM에 대해 평가합니다.
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
