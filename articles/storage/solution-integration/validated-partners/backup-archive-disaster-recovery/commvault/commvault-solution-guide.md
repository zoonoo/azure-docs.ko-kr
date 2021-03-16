---
title: Commvault를 사용 하 여 Azure에 데이터 백업
titleSuffix: Azure Storage
description: Commvault 전체 백업 및 복구를 위한 저장소 대상 및 복구 위치로 Azure를 사용 하기 위해 고려해 야 하는 요소에 대 한 개요를 제공 합니다.
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 29484bee685fcc15073ef255e65a780e05f1200d
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561671"
---
# <a name="backup-to-azure-with-commvault"></a>Commvault를 사용 하 여 Azure에 백업

이 문서는 Azure Blob storage와 Commvault 인프라를 통합 하는 데 도움이 됩니다. 여기에는 필수 구성 요소, 고려 사항, 구현 및 작업 지침이 포함 됩니다. 이 문서에서는 Azure를 오프 사이트 백업 대상으로 사용 하 고 재해 발생 시 복구 사이트를 사용 하 여 기본 사이트 내에서 정상적인 작업을 방지 하는 방법을 설명 합니다.

> [!NOTE]
> Commvault는 더 낮은 RTO (복구 시간 목표) 솔루션인 Commvault Live Sync를 제공 합니다. 이 솔루션을 사용 하면 Azure 프로덕션 환경에서 재해가 발생 한 경우 보다 신속 하 게 복구 하는 데 도움이 되는 대기 VM을 사용할 수 있습니다. 이러한 기능은이 문서의 범위를 벗어납니다.

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 온-프레미스와 Azure 간 배포에 대 한 참조 아키텍처를 제공 합니다.

![Commvault에서 Azure 참조 아키텍처](../media/commvault-diagram.png)

기존 Commvault 배포는 Azure storage 계정 또는 여러 계정을 클라우드 저장소 대상으로 추가 하 여 Azure와 쉽게 통합할 수 있습니다. Commvault를 사용 하면 azure 내의 온-프레미스에서 백업을 복구 하 여 Azure에서 주문형 복구 사이트를 제공할 수도 있습니다.

## <a name="commvault-interoperability-matrix"></a>Commvault 상호 운용성 매트릭스

| 작업 | GPv2 및 Blob 저장소 | 쿨 계층 지원 | 보관 계층 지원 | Data Box 제품군 지원 |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| 온-프레미스 Vm/데이터 | v 11.5 | v 11.5 | v 11.10 | v 11.10 |
| Azure VM | v 11.5 | v 11.5 | v 11.5 | 해당 없음 |
| Azure Blob | v 11.6 | v 11.6 | v 11.6 | 해당 없음 |
| Azure 파일 | v 11.6 | v 11.6 | v 11.6 | 해당 없음 |

## <a name="before-you-begin"></a>시작하기 전에

약간의 사전 계획을 통해 Azure를 오프 사이트 백업 대상 및 복구 사이트로 사용할 수 있습니다.

### <a name="get-started-with-azure"></a>Azure 시작

Microsoft는 Azure를 시작 하기 위해 수행할 수 있는 프레임 워크를 제공 합니다. Caf ( [클라우드 채택 프레임 워크](https://docs.microsoft.com/azure/architecture/cloud-adoption/) )는 프로덕션 등급 클라우드 도입을 계획 하기 위한 엔터프라이즈 디지털 변환 및 포괄적인 가이드에 대 한 자세한 접근 방식입니다. CAF에는 빠르고 안전 하 게 시작 및 실행 하는 데 도움이 되는 단계별 [Azure 설치 가이드가](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) 포함 되어 있습니다. [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)에서 대화형 버전을 찾을 수 있습니다. 샘플 아키텍처, 응용 프로그램 배포에 대 한 특정 모범 사례 및 Azure 전문 지식에 대 한 경로를 제공 하는 무료 교육 리소스를 찾을 수 있습니다.

### <a name="consider-the-network-between-your-location-and-azure"></a>위치와 Azure 간의 네트워크 고려

클라우드 리소스를 사용 하 여 프로덕션, 테스트 및 개발을 실행 하거나 백업 대상 및 복구 사이트로 사용 하는지 여부에 관계 없이 초기 백업 시드에 대 한 대역폭 요구 사항을 이해 하 고 진행 중인 일상적인 전송에 대 한 대역폭 요구 사항을 이해 하는 것이 중요 합니다.

Azure Data Box은 더 많은 대역폭을 요구 하지 않고 초기 백업 기준을 Azure에 전송 하는 방법을 제공 합니다. 이는 기본 전송이 허용 하는 것 보다 더 오래 걸릴 것으로 예상 되는 경우에 유용 합니다. 저장소 계정을 만들 때 데이터 전송 평가기를 사용 하 여 초기 백업을 전송 하는 데 필요한 시간을 예상할 수 있습니다.

![포털에서 Azure Storage 데이터 전송 평가기를 표시 합니다.](../media/az-storage-transfer.png)

프로덕션 응용 프로그램에 영향을 주지 않고 필요한 전송 기간 (백업 창) 내에서 일일 데이터 전송을 지원 하기에 충분 한 네트워크 용량이 필요 합니다. 이 섹션에서는 네트워크 요구 사항을 평가 하는 데 사용할 수 있는 도구와 기술에 대해 간략하게 설명 합니다.

#### <a name="determine-how-much-bandwidth-youll-need"></a>필요한 대역폭 크기 결정

필요한 대역폭 크기를 확인 하려면 다음 리소스를 사용 합니다.

- 백업 소프트웨어의 보고서
- Commvault는 Azure로 초기 기준 전송에 대 한 [변경 률](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) 및 [총 백업 세트 크기](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) 를 결정 하는 표준 보고서를 제공 합니다.
- 다음과 같은 소프트웨어 독립적 평가 및 보고 도구를 백업 합니다.
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>과도 한 인터넷 대역폭 확인

일상적으로 사용할 수 있는 대역폭 (또는 *여유*)의 양을 파악 하는 것이 중요 합니다. 이를 통해 목표를 충족할 수 있는지 여부를 평가할 수 있습니다.

- 오프 라인 시드에 Azure Data Box를 사용 하지 않는 경우 초기 업로드 시간
- 이전에 식별 된 변경 비율과 백업 기간에 따라 매일 백업 완료

Azure에 대 한 백업이 무료로 사용할 수 있는 대역폭을 확인 하려면 다음 방법을 사용 합니다.

- 기존 Azure Express 경로 고객 인 경우 Azure Portal에서 [회로 사용량](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) 을 확인 하세요.
- ISP에 문의 하십시오. 기존 매일 및 매월 사용률을 표시 하는 보고서를 공유할 수 있어야 합니다.
- 라우터/스위치 수준에서 네트워크 트래픽을 모니터링 하 여 사용률을 측정할 수 있는 몇 가지 도구가 있습니다. 여기에는 다음이 포함됩니다.
  - [Solarwinds 대역폭 분석기 팩](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco 네트워크 길잡이](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp 황금색](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>올바른 저장소 옵션 선택

Azure를 백업 대상으로 사용 하는 경우 [Azure Blob storage](../../../../blobs/storage-blobs-introduction.md)를 사용 하 게 됩니다. Blob storage는 Microsoft의 개체 저장소 솔루션입니다. Blob 저장소는 데이터 모델 또는 정의를 준수 하지 않는 데이터 인 대량의 구조화 되지 않은 데이터를 저장 하기 위해 최적화 되어 있습니다. 또한 Azure Storage는 내구성, 고가용성, 보안 및 확장성을 제공 합니다. 워크 로드에 적합 한 저장소를 선택 하 여 내부 Sla를 충족 하는 [복원 력 수준을](../../../../common/storage-redundancy.md) 제공할 수 있습니다. Blob 저장소는 유료 서비스입니다. 저장 된 데이터의 양에 대해 [매달 요금이 청구](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) 되 고, 해당 데이터에 액세스 하 고, 쿨 및 보관 계층의 경우 필요한 최소 보존 기간이 청구 됩니다. 백업 데이터에 적용할 수 있는 복원 력 및 계층화 옵션은 다음 표에 요약 되어 있습니다.

**Blob storage 복원 력 옵션:**

|  |로컬 중복  |Zone-redundant  |지역 중복  |지역 영역 중복  |
|---------|---------|---------|---------|---------|
|**유효 복사본 개수**     | 3         | 3         | 6         | 6 |
|**가용성 영역 개수**     | 1         | 3         | 2         | 4 |
|**지역 번호**     | 1         | 1         | 2         | 2 |
|**보조 지역으로 수동 장애 조치 (failover)**     | 해당 없음         | 해당 없음         | 예         | 예 |

**Blob 저장소 계층:**

|  | 핫 계층   |쿨 계층   | 보관 액세스 계층 |
| ----------- | ----------- | -----------  | -----------  |
| **가용성** | 99.9%         | 99%         | 오프라인      |
| **사용 요금** | 더 높은 저장소 비용, 낮은 액세스 및 트랜잭션 비용 | 스토리지 비용 더 낮음, 액세스 및 트랜잭션 비용 더 높음 | 스토리지 비용 가장 낮음, 액세스 및 트랜잭션 비용 가장 높음 |
| **최소 데이터 보존 필요**| 해당 없음 | 30일 | 180일 |
| **대기 시간 (첫 번째 바이트 까지의 시간)** | 밀리초 | 밀리초 | 시간 |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure cost model에 대 한 샘플 백업

클라우드를 처음 사용 하는 고객에 게는 종 량 제를 사용 하는 것이 어려울 수 있습니다. 사용 되는 용량에 대해서만 비용을 지불 하는 동안 azure [Express route direct local 또는 Express route 무제한 데이터 요금제](https://azure.microsoft.com/pricing/details/expressroute/) 를 사용 하 여 azure에서 데이터를 수신 하는 경우에도 트랜잭션 (읽기 및 쓰기)에 대해 비용을 지불 하 고 온-프레미스 환경으로 [데이터를](https://azure.microsoft.com/pricing/details/bandwidth/) 다시 전송 합니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 "가상 분석" 분석을 수행할 수 있습니다. 가격 책정 또는 [Azure Storage 예약 된 용량 가격 책정](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)에 대 한 분석을 기반으로 하 여 최대 38% 절감 액을 제공할 수 있습니다. Azure에 백업 하는 월간 비용을 모델링 하는 예제 가격 책정 예제는 다음과 같습니다. 이는 예입니다. *가격은 여기에서 캡처되지 않은 활동으로 인해 달라질 수 있습니다.*

|비용 요소  |월간 비용  |
|---------|---------|
|쿨 저장소에서 100 TB의 백업 데이터     |$1556.48         |
|하루에 작성 된 1TB의 새 데이터 x 30 일     |$39 트랜잭션          |
|월간 예상 합계     |$1595.48         |
|---------|---------|
|공용 인터넷을 통해 5tb에서 온-프레미스로의 일회성 복원   | $491.26         |

> [!NOTE]
> 이 추정치는 미국 동부 종 량 제 가격 책정을 사용 하 여 Azure 가격 계산기에서 생성 되었으며, 초당 65536 PUT 요청 (쓰기 트랜잭션)을 생성 하는 32 MB 하위 청크 크기의 Commvault 기본값을 기반으로 합니다. 이 예는 요구 사항에 적용 되지 않을 수 있습니다.

## <a name="implementation-guidance"></a>구현 지침

이 섹션에서는 온-프레미스 Commvault 배포에 Azure Storage를 추가 하는 방법에 대 한 간략 한 지침을 제공 합니다. 자세한 지침 및 계획 고려 사항은 [Microsoft Azure에 대 한 Commvault 공용 클라우드 아키텍처 가이드](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)를 참조 하세요.

1. Azure Portal를 열고 **저장소 계정을** 검색 합니다. 기본 **저장소 계정** 아이콘을 클릭할 수도 있습니다.

    ![Azure Portal에서 저장소 계정을 추가 하는 방법을 보여 줍니다.](../media/azure-portal.png)
  
    ![Azure Portal의 검색 상자에 저장소를 입력 한 위치를 표시 합니다.](../media/locate-storage-account.png)

2. **만들기** 를 선택 하 여 계정을 추가 합니다. 리소스 그룹을 선택 하거나 만들고, 고유한 이름을 지정 하 고, 지역을 선택 하 고, **표준** 성과를 선택 하 고, 항상 계정 종류를 **Storage** v 2로 두고, sla를 충족 하는 복제 수준을 선택 하 고, 백업 소프트웨어가 적용 될 기본 계층을 선택 합니다. Azure Storage 계정은 단일 계정 내에서 사용할 수 있는 핫, 쿨 및 보관 계층을 만들며 Commvault 정책을 사용 하면 여러 계층을 사용 하 여 데이터의 수명 주기를 효과적으로 관리할 수 있습니다.

    ![포털에서 저장소 계정 설정 표시](../media/account-create-1.png)

3. 지금은 기본 네트워킹 옵션을 그대로 유지 하 고 **데이터 보호** 로 이동 합니다. 여기에서 일시 삭제를 사용 하도록 선택할 수 있습니다 .이를 통해 정의 된 보존 기간 내에 실수로 삭제 된 백업 파일을 복구 하 고 실수로 인 한 삭제 또는 악의적인 삭제 로부터 보호를 제공할 수 있습니다.

    ![포털의 데이터 보호 설정을 표시 합니다.](../media/account-create-2.png)

4. 다음으로 Azure 사용 사례에 대 한 백업에 대 한 **고급** 화면에서 기본 설정을 사용 하는 것이 좋습니다.

    ![포털의 고급 설정 탭을 표시 합니다.](../media/account-create-3.png)

5. 태그 지정을 사용 하 고 계정을 만드는 경우 조직의 태그를 추가 합니다.

6. 이제 Commvault 환경에 계정을 추가 하려면 먼저 두 개의 빠른 단계를 수행 해야 합니다. Azure Portal에서 만든 계정으로 이동 하 여 **Blob service** 메뉴 아래에서 **컨테이너** 를 선택 합니다. 컨테이너를 추가 하 고 의미 있는 이름을 선택 합니다. 그런 다음 **설정** 에서 **액세스 키** 항목으로 이동 하 여 **저장소 계정 이름과** 두 개의 액세스 키 중 하나를 복사 합니다. 다음 단계에서 컨테이너 이름, 계정 이름 및 액세스 키가 필요 합니다.

    ![포털에서 컨테이너 만들기를 표시 합니다.](../media/container.png)

    ![포털에서 액세스 키 설정을 표시 합니다.](../media/access-key.png)

7. (*선택 사항*) 배포에 추가 보안 계층을 추가할 수 있습니다.

    1. 역할 기반 액세스를 구성 하 여 저장소 계정을 변경할 수 있는 사용자를 제한 합니다. 자세한 내용은 [관리 작업을 위한 기본 제공 역할](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)을 참조 하세요.
    1. 회사 네트워크 외부에서 액세스를 시도할 수 없도록 [저장소 방화벽 설정을](../../../../common/storage-network-security.md) 사용 하 여 계정에 대 한 액세스를 특정 네트워크 세그먼트로 제한 합니다.

        ![포털에서 저장소 방화벽 설정을 표시 합니다.](../media/storage-firewall.png)

    1. 계정에 대 한 [삭제 잠금을](../../../../../azure-resource-manager/management/lock-resources.md) 설정 하 여 저장소 계정이 실수로 삭제 되지 않도록 합니다.

        ![포털에서 삭제 잠금을 설정 하는 방법을 보여 줍니다.](../media/resource-lock.png)

    1. 추가 [보안 모범 사례](../../../../../storage/blobs/security-recommendations.md)를 구성 합니다.

1. Commvault 명령 센터에서   ->  **보안**  ->  **자격 증명 관리자** 관리로 이동 합니다. **클라우드 계정**, **공급 업체 유형** **Microsoft Azure Storage** 를 선택 하 고, Azure 간에 데이터를 전송 하는 **MediaAgent** 를 선택 하 고, 저장소 계정 이름 및 액세스 키를 추가 합니다.

    ![Commvault 명령 센터에서 자격 증명 추가를 표시 합니다.](../media/commvault-credential.png)

9. 다음으로,   ->  Commvault 명령 센터에서 저장소 **클라우드** 로 이동 합니다. **추가** 를 선택 합니다. 저장소 계정의 이름을 입력 하 고 **유형** 목록에서 **Microsoft Azure Storage** 을 선택 합니다. Azure Storage에 백업을 전송 하는 데 사용할 미디어 에이전트 서버를 선택 합니다. 만든 컨테이너를 추가 하 고, Azure storage 계정 내에서 사용할 저장소 계층을 선택 하 고, #8 단계에서 만든 자격 증명을 선택 합니다. 마지막으로 중복 제거 된 백업을 전송할지 여부 및 중복 제거 데이터베이스의 위치를 전송할지 여부를 선택 합니다.

     ![Commvault의 클라우드 사용자 추가 인터페이스 스크린샷 보관 드롭다운 메뉴에서 * * Archive * *를 선택 합니다.](../media/commvault-add-storage.png)

10. 마지막으로   ->  **계획** 관리를 백업 대상으로 하 여 Commvault 명령 센터의 기존 또는 새 계획에 새 Azure Storage 리소스를 추가 합니다.

    ![Commvault Command Center 사용자 인터페이스의 스크린샷 왼쪽 탐색의 * * 관리 * *에서 * * 요금제 * *를 선택 합니다.](../media/commvault-plan.png)

11. *(선택 사항)* Azure를 복구 사이트 또는 Commvault로 사용 하 여 서버 및 응용 프로그램을 Azure로 마이그레이션할 계획인 경우 Azure에 VSA 프록시를 배포 하는 것이 가장 좋습니다. 자세한 지침은 [여기](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)에서 찾을 수 있습니다.

## <a name="operational-guidance"></a>운영 가이드

### <a name="azure-alerts-and-performance-monitoring"></a>Azure 경고 및 성능 모니터링

백업을 저장 하는 데 사용 하는 저장소 대상의 경우와 마찬가지로 Azure 리소스와 Commvault의 기능을 모두 모니터링 하는 것이 좋습니다. Azure Monitor와 Commvault Command Center 모니터링의 조합은 환경을 정상 상태로 유지 하는 데 도움이 됩니다.

#### <a name="azure-portal"></a>Azure portal

Azure는 [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md)형식으로 강력한 모니터링 솔루션을 제공 합니다. Azure Monitor를 [구성](../../../../common/monitor-storage.md) 하 여 Azure Storage 용량, 트랜잭션, 가용성, 인증 등을 추적할 수 있습니다. [여기](../../../../blobs/monitor-blob-storage-reference.md)에 수집 된 메트릭의 전체 참조를 찾을 수 있습니다. 추적할 몇 가지 유용한 메트릭은 BlobCapacity입니다. 최대 저장소 계정 용량 제한, 수신 및 송신을 유지 하기 위해 최대 [저장소 계정 용량 제한](../../../../common/scalability-targets-standard-account.md), 수신 및 송신을 유지 하 고 Azure Storage 계정에서 쓰고 있는 데이터의 양을 추적 하 고, Azure Storage 및 MediaAgent로 들어오고 나가는 요청의 왕복 시간을 추적 하기 위해 SuccessE2ELatency.

또한 [로그 경고를 만들어](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) Azure Storage 서비스 상태를 추적 하 고 언제 든 지 [Azure 상태 대시보드](https://status.azure.com/status) 를 볼 수 있습니다.

#### <a name="commvault-command-center"></a>Commvault 명령 센터

- [클라우드 저장소 풀에 대 한 경고 만들기](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- 성능 보고서를 보고, 작업을 완료 하 고, 기본적인 문제 해결을 시작할 수 있는 위치에 대 한 자세한 내용은 [대시보드](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)를 참조 하세요.

### <a name="how-to-open-support-cases"></a>지원 사례를 여는 방법

Azure 솔루션에 대 한 백업에 대 한 도움이 필요한 경우 Commvault와 Azure를 모두 사용 하 여 케이스를 열어야 합니다. 이렇게 하면 필요한 경우 지원 조직이 공동 작업을 할 수 있습니다.

#### <a name="to-open-a-case-with-commvault"></a>Commvault를 사용 하 여 케이스를 열려면

[Commvault 지원 사이트](https://www.commvault.com/support)에서 로그인 하 고 케이스를 엽니다.

사용할 수 있는 지원 계약 옵션을 이해 하려면 [Commvault 지원 옵션](https://ma.commvault.com/support) 을 참조 하세요.

에서를 호출 하 여 사례를 열거나 전자 메일을 통해 Commvault 지원에 도달할 수도 있습니다.

- 무료: + 1 877-780-3077
- [전 세계 지원 전화 번호](https://ma.commvault.com/Support/TelephoneSupport)
- [전자 메일 Commvault 지원](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Azure에서 케이스를 열려면

[Azure Portal](https://portal.azure.com) 의 맨 위에 있는 검색 표시줄에서 **지원** 검색을 검색 합니다. **도움말 +**  ->  **새 지원 요청** 지원을 선택 합니다.

> [!NOTE]
> 케이스를 열 때 Azure Storage 또는 Azure 네트워킹에 대 한 도움이 필요 합니다. Azure Backup 지정 하지 마십시오. Azure Backup는 Azure 서비스의 이름이 며 사례는 잘못 라우팅됩니다.

### <a name="links-to-relevant-commvault-documentation"></a>관련 Commvault 설명서에 대 한 링크

추가 정보는 다음 Commvault 설명서를 참조 하세요.

- [Commvault 사용자 가이드](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Commvault Azure 아키텍처 가이드](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Marketplace 제공

Commvault를 통해 azure Virtual Machines 및 기타 여러 Azure 서비스를 보호 하는 솔루션을 Azure에 쉽게 배포할 수 있었습니다. 자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Marketplace를 통해 Commvault 배포](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Azure 데이터 시트의 Commvault](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [지원 되는 Azure 기능 및 서비스의 Commvault 목록](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Azure에서 Commvault를 사용 하 여 SAP HANA를 보호 하는 방법](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>다음 단계

특수 사용 시나리오에 대 한 자세한 내용은 이러한 추가 Commvault 자료를 참조 하세요.

- [Commvault를 사용 하 여 서버 및 응용 프로그램을 Azure로 마이그레이션](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Commvault를 사용 하 여 Azure에서 SAP 보호](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Commvault로 Office365 보호](https://www.youtube.com/watch?v=dl3nvAacxZU)
