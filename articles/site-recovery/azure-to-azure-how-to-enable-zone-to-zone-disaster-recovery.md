---
title: Azure Virtual Machines에 대 한 영역에 영역 재해 복구를 사용 하도록 설정
description: 이 문서에서는 Azure virtual machines에 대해 영역에 영역 재해 복구를 사용 하는 시기 및 방법을 설명 합니다.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 543be2b408d9442c61ae2c052776e63c33206188
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488939"
---
# <a name="enable-zone-to-zone-disaster-recovery-for-azure-virtual-machines"></a>Azure virtual machines에 대해 영역에 영역 재해 복구를 사용 하도록 설정

이 문서에서는 동일한 Azure 지역 내의 한 가용성 영역에서 다른 가용성 영역으로 Azure 가상 머신을 복제, 장애 조치 (failover) 및 장애 복구 (failback) 하는 방법을 설명 합니다.

>[!NOTE]
>
>- 현재 Site Recovery는 포털을 통해 영역 간 재해 복구에 대 한 복구 계획을 지원 하지 않습니다. 영역 재해 복구 영역에 대 한 복구 계획을 활용 하려면 PowerShell 또는 REST API를 사용 하세요. 
>- 영역 간 재해 복구에 대 한 지원은 현재 동남 아시아 및 영국 남부의 두 지역으로 제한 됩니다.  

Site Recovery 서비스는 계획 되거나 계획 되지 않은 중단 중에 비즈니스 앱을 가동 및 실행 상태로 유지 하 여 비즈니스 연속성 및 재해 복구 전략에 기여 합니다. 지역 가동 중단이 발생 하는 경우 응용 프로그램을 계속 실행 하는 재해 복구 옵션을 권장 합니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역에는 하나 이상의 데이터 센터가 있습니다. 

## <a name="using-availability-zones-for-disaster-recovery"></a>재해 복구를 위해 가용성 영역 사용 

일반적으로 가용성 영역는 고가용성 구성으로 Vm을 배포 하는 데 사용 됩니다. 자연 재해 발생 시 재해 복구 솔루션을 제공 하기 위해 서로 너무 가까이 있을 수 있습니다.

그러나 일부 시나리오에서는 재해 복구를 위해 가용성 영역를 활용할 수 있습니다.

- 온-프레미스에서 응용 프로그램을 호스트 하는 동안 metro 재해 복구 전략을 받은 많은 고객은 응용 프로그램을 Azure로 마이그레이션할 때이 전략을 모방 하는 경우가 있습니다. 이러한 고객은 대규모 물리적 재해 발생 시 metro 재해 복구 전략이 작동 하지 않을 수 있다는 사실을 인정 하 고이 위험에 동의 합니다. 이러한 고객의 경우 영역 및 영역 간 재해 복구를 재해 복구 옵션으로 사용할 수 있습니다.

- 다른 많은 고객 들이 복잡 한 네트워킹 인프라를가지고 있으며, 관련 비용 및 복잡성 때문에 보조 지역에서 다시 만들지 않으려고 합니다. 영역 간 재해 복구는 구성을 훨씬 간소화 하는 가용성 영역에서 중복 네트워킹 개념을 활용 하므로 복잡성이 줄어듭니다. 이러한 고객은 간소화를 선호 하며 재해 복구를 위해 가용성 영역를 사용할 수도 있습니다.

- 동일한 법률 관할 지역 내에 쌍을 이루는 지역이 없는 일부 지역 (예: 동남 아시아)에서 영역 및 영역 재해 복구는 응용 프로그램 및 데이터가 국가 간 경계를 갖지 않으므로 법적 규정 준수를 보장 하는 데 도움이 되는 사실상 재해 복구 솔루션으로 사용할 수 있습니다. 

- 영역에 대 한 영역 재해 복구는 Azure에서 Azure로의 재해 복구와 비교 하 여 더 짧은 시간에 데이터를 복제 하는 것을 의미 하므로 더 짧은 대기 시간을 표시 하 고 RPO를 낮출 수 있습니다.

이러한 기능은 강력한 장점 이지만 지역 전체 자연 재해가 발생 하는 경우 영역 재해 복구에 대 한 영역 재해 복구 요구 사항이 부족할 수 있습니다.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>영역 및 영역 재해 복구를 위한 네트워킹

위에서 언급 한 대로 영역에 대 한 영역 재해 복구는 가용성 영역에서 중복 네트워킹 개념을 활용 하므로 복잡성이 감소 하므로 구성을 훨씬 더 간단 하 게 만들 수 있습니다. 영역 재해 복구 시나리오에 대 한 영역에서 네트워킹 구성 요소의 동작은 아래에 설명 되어 있습니다. 

- Virtual Network: 실제 장애 조치 (failover)를 위해 원본 네트워크와 동일한 가상 네트워크를 사용할 수 있습니다. 테스트 장애 조치 (failover)를 위해 원본 가상 네트워크에 다른 가상 네트워크를 사용 합니다.

- 서브넷: 동일한 서브넷으로 장애 조치 (Failover)가 지원 됩니다.

- 개인 IP 주소: 고정 IP 주소를 사용 하는 경우 이러한 방식으로 구성 하도록 선택 하는 경우 대상 영역에서 동일한 Ip를 사용할 수 있습니다.

- 가속화 된 네트워킹: Azure에서 Azure로 재해 복구와 유사 하 게, VM SKU에서 지원 되는 경우 가속화 된 네트워킹을 사용 하도록 설정할 수 있습니다.

- 공용 IP 주소: 이전에 만든 표준 공용 IP 주소를 동일한 지역에서 대상 VM에 연결할 수 있습니다. 기본 공용 IP 주소는 가용성 영역 관련 시나리오를 지원 하지 않습니다.

- 부하 분산 장치: 표준 부하 분산 장치는 지역별 리소스 이므로 대상 VM을 동일한 부하 분산 장치의 백 엔드 풀에 연결할 수 있습니다. 새 부하 분산 장치는 필요 하지 않습니다.

- 네트워크 보안 그룹: 원본 VM에 적용 된 것과 동일한 네트워크 보안 그룹을 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

Vm에 대 한 영역 재해 복구 영역에 영역을 배포 하기 전에 VM에서 사용 하도록 설정 된 다른 기능이 영역 재해 복구 영역에서 작동 하는지 확인 하는 것이 중요 합니다.

|기능  | 지원 정책  |
|---------|---------|
|클래식 VM   |     지원되지 않음    |
|ARM Vm    |    지원됨    |
|Azure Disk Encryption v1 (AAD를 사용 하는 이중 패스)     |     지원됨 |
|Azure Disk Encryption v2 (AAD 없이 단일 패스)    |    지원됨    |
|관리되지 않는 디스크    |    지원되지 않음    |
|관리 디스크    |    지원됨    |
|고객 관리 키    |    지원됨    |
|근접 배치 그룹    |    지원됨    |
|백업 상호 운용성    |    파일 수준 백업 및 복원이 지원 됩니다. 디스크 및 VM 수준 백업 및 복원이 지원 되지 않습니다.    |
|핫 추가/제거    |    영역을 영역 복제를 사용 하도록 설정한 후 디스크를 추가할 수 있습니다. 영역에 영역 복제를 사용 하도록 설정한 후에는 디스크를 제거할 수 없습니다.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>영역 재해 복구 Site Recovery 영역 설정

### <a name="log-in"></a>로그인

Azure Portal 로그인 합니다.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>영역 Azure 가상 컴퓨터에 대 한 복제를 사용 하도록 설정

1. Azure Portal 메뉴에서 가상 머신을 선택하거나 모든 페이지에서가상 머신을 검색하여 선택합니다. 복제할 VM을 선택합니다. 영역 간 재해 복구의 경우이 VM은 이미 가용성 영역에 있어야 합니다.

2. 작업에서 재해 복구를 선택합니다.

3. 아래와 같이 기본 사항 탭에서 ' 예 '를 선택 하 고 ' 가용성 영역 간 재해 복구 '를 선택 합니다.

    ![기본 설정 페이지](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. 모든 기본값을 적용 하는 경우 ' 검토 + 시작 복제 '를 클릭 한 다음 ' 복제 시작 '을 클릭 합니다.

5. 복제 설정을 변경 하려면 ' 다음: 고급 설정 '을 클릭 합니다.

6. 적절 한 경우 언제 든 지 설정을 기본 설정으로 변경 합니다. Azure에서 Azure로의 재해 복구에 대 한 사용자의 경우이 페이지가 친숙 하 게 보일 수 있습니다. 이 블레이드에 제공 되는 옵션에 대 한 자세한 내용은 여기를 참조 [하세요](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) .

    ![고급 설정 페이지](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. ' 다음: 검토 + 복제 시작 '을 클릭 한 다음 ' 복제 시작 '을 클릭 합니다.

## <a name="faqs"></a>FAQ

**1. 지역에서 영역 재해 복구에 대 한 가격은 어떻게 작동 하나요?**
영역 및 영역 재해 복구에 대 한 가격은 Azure에서 Azure로 재해 복구에 대 한 가격 책정과 동일 합니다. 가격 책정 페이지에 대 한 자세한 [내용은 여기에서](https://azure.microsoft.com/pricing/details/site-recovery/) 찾을 수 [있습니다.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) 영역 재해 복구 영역에 표시 되는 송신 요금은 지역에서 지역 재해 복구 보다 낮습니다.

**2. RTO 및 RPO에 대 한 SLA는 무엇 인가요?**
RTO SLA는 전체 Site Recovery와 동일 합니다. 최대 2 시간의 RTO를 보장 합니다. RPO에 대해 정의 된 SLA는 없습니다.

**3. 보조 영역에서 용량을 보장 하나요?**
Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치 (failover)를 시작할 때 팀은 Site Recovery로 보호 되는 VM 인스턴스가 대상 영역에 배포 되도록 하는 데도 도움이 됩니다.

**4. 지원 되는 운영 체제는 무엇 인가요?**
영역 간 재해 복구는 Azure에서 Azure로 재해 복구와 동일한 운영 체제를 지원 합니다. [여기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)에서 지원 매트릭스를 참조 하세요.

**5. 소스 및 대상 리소스 그룹이 동일할 수 있나요?**
아니요, 다른 리소스 그룹으로 장애 조치 (failover) 해야 합니다.

## <a name="next-steps"></a>다음 단계

재해 복구 훈련, 장애 조치 (failover), 다시 보호 및 장애 복구를 실행 하기 위해 따라야 하는 단계는 Azure에서 Azure로 재해 복구 시나리오의 단계와 동일 합니다.

재해 복구 훈련을 수행 하려면 [여기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-dr-drill)에 설명 된 단계를 따르세요.

장애 조치 (failover)를 수행 하 고 보조 영역에서 Vm을 다시 보호 하려면 [여기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)에 설명 된 단계를 따르세요.

주 영역으로 장애 복구 (failback) 하려면 [여기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failback)에 설명 된 단계를 따르세요.
