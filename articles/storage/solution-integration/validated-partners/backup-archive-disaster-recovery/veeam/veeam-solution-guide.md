---
title: Veeam을 사용 하 여 Azure에 데이터 백업
titleSuffix: Azure Storage
description: Veeam 백업 및 복구에 대 한 저장소 대상 및 복구 위치로 Azure를 사용 하기 위해 고려해 야 할 요소 및 단계에 대 한 개요를 제공 합니다.
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15ab808bcb8521b0bd3a5ebd0f56bf336571d676
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562028"
---
# <a name="backup-to-azure-with-veeam"></a>Veeam을 사용 하 여 Azure에 백업

이 문서는 Veeam 인프라를 Azure Blob storage와 통합 하는 데 도움이 됩니다. 여기에는 필수 구성 요소, 고려 사항, 구현 및 작업 지침이 포함 됩니다. 이 문서에서는 Azure를 오프 사이트 백업 대상으로 사용 하 고 재해 발생 시 복구 사이트를 사용 하 여 기본 사이트 내에서 정상적인 작업을 방지 하는 방법을 설명 합니다.

> [!NOTE]
> 또한 veeam은 낮은 RTO (복구 시간 목표) 솔루션인 Veeam 복제를 제공 합니다. 이 솔루션을 사용 하면 Azure 프로덕션 환경에서 재해가 발생 한 경우 보다 신속 하 게 복구 하는 데 도움이 되는 대기 VM을 사용할 수 있습니다. Veeam에는 Azure 및 Office 365 리소스를 백업 하는 전용 도구가 포함 되어 있습니다. 이러한 기능은이 문서의 범위를 벗어납니다.

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 온-프레미스와 Azure 간 배포에 대 한 참조 아키텍처를 제공 합니다.

![Azure 참조 아키텍처 다이어그램에 대 한 veeam입니다.](../media/veeam-architecture.png)

기존 Veeam 배포는 Azure storage 계정 또는 여러 계정을 클라우드 백업 리포지토리로 추가 하 여 Azure와 쉽게 통합할 수 있습니다. Veeam을 사용 하면 azure 내의 온-프레미스에서 백업을 복구 하 여 Azure에서 주문형 복구 사이트를 제공할 수도 있습니다.

## <a name="veeam-interoperability-matrix"></a>Veeam 상호 운용성 매트릭스

| 작업 | GPv2 및 Blob Storage | 쿨 계층 지원 | 보관 계층 지원 | Data Box 제품군 지원 |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| 온-프레미스 Vm/데이터 | v10a | v10a | 해당 없음 | 10a<sup>*</sup> |
| Azure VM | v10a | v10a | 해당 없음 | 10a<sup>*</sup> |
| Azure Blob | v10a | v10a | 해당 없음 | 10a<sup>*</sup> |
| Azure 파일 | v10a | v10a | 해당 없음 | 10a<sup>*</sup> |

<sup>*</sup>Veeam 백업 및 복제 지원은 Azure Data Box에 대해서만 REST API. 따라서 Azure Data Box Disk 지원 되지 않습니다.

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

Azure로 초기 기준 전송에 대 한 변경 비율 및 전체 백업 세트 크기를 결정 하기 위해 여러 평가 옵션을 사용할 수 있습니다. 평가 및 보고 도구의 몇 가지 예는 다음과 같습니다.

- [MiTrend](https://mitrend.com/)
- [Apt](https://www.veritas.com/insights/aptare-it-analytics)
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
| **최소 데이터 보존 필요** | 해당 없음 | 30일 | 180일 |
| **대기 시간 (첫 번째 바이트 까지의 시간)** | 밀리초 | 밀리초 | 시간 |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure cost model에 대 한 샘플 백업

클라우드를 처음 사용 하는 고객에 게는 종 량 제를 사용 하는 것이 어려울 수 있습니다. 사용 되는 용량에 대해서만 비용을 지불 하는 동안 azure [Express route direct local 또는 Express route 무제한 데이터 요금제](https://azure.microsoft.com/pricing/details/expressroute/) 를 사용 하 여 azure에서 데이터를 수신 하는 경우에도 트랜잭션 (읽기 및 쓰기)에 대해 비용을 지불 하 고 온-프레미스 환경으로 [데이터를](https://azure.microsoft.com/pricing/details/bandwidth/) 다시 전송 합니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 "가상 분석" 분석을 수행할 수 있습니다. 가격 책정 또는 [Azure Storage 예약 된 용량 가격 책정](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)에 대 한 분석을 기반으로 하 여 최대 38% 절감 액을 제공할 수 있습니다. Azure에 백업 하는 월간 비용을 모델링 하는 예제 가격 책정 예제는 다음과 같습니다. 이는 예입니다. *가격은 여기에서 캡처되지 않은 활동으로 인해 달라질 수 있습니다.*

|비용 요소  |월간 비용  |
|---------|---------|
|쿨 저장소에서 100 TB의 백업 데이터     |$1556.48         |
|하루에 작성 된 1TB의 새 데이터 x 30 일     |$72 트랜잭션          |
|월간 예상 합계     |$1628.48         |
|---------|---------|
|공용 인터넷을 통해 5tb에서 온-프레미스로의 일회성 복원   | $527.26         |

> [!Note]
> 이러한 추정치는 미국 동부 종 량 제 가격 책정을 사용 하 여 Azure 가격 계산기에서 생성 되었으며 WAN 전송을 위한 Veeam 기본값 256 kb 청크 크기를 기준으로 합니다. 이 예는 요구 사항에 적용 되지 않을 수 있습니다.

## <a name="implementation-guidance"></a>구현 지침

이 섹션에서는 온-프레미스 Veeam 배포에 Azure Storage를 추가 하는 방법에 대 한 간략 한 지침을 제공 합니다. 자세한 지침 및 계획 고려 사항은 [Veeam 클라우드 연결 백업 가이드](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)를 참조 하세요.

1. Azure Portal를 열고 **저장소 계정을** 검색 합니다. 기본 서비스 아이콘을 클릭할 수도 있습니다.

      ![Azure Portal에서 저장소 계정을 추가 하는 방법을 보여 줍니다.](../media/azure-portal.png)

      ![Azure Portal의 검색 상자에 저장소를 입력 한 위치를 표시 합니다.](../media/locate-storage-account.png)

2. **만들기** 를 선택 하 여 계정을 추가 합니다. 리소스 그룹을 선택 하거나 만들고, 고유한 이름을 지정 하 고, 지역을 선택 하 고, **표준** 성과를 선택 하 고, 항상 계정 종류를 **Storage** v 2로 두고, sla를 충족 하는 복제 수준을 선택 하 고, 백업 소프트웨어가 적용 될 기본 계층을 선택 합니다. Azure Storage 계정은 단일 계정 내에서 사용할 수 있는 핫, 쿨 및 보관 계층을 설정 하 고 Veeam 정책을 사용 하면 여러 계층을 사용 하 여 데이터의 수명 주기를 효과적으로 관리할 수 있습니다.

    ![포털에서 저장소 계정 설정 표시](../media/account-create-1.png)

3. 지금은 기본 네트워킹 옵션을 그대로 유지 하 고 **데이터 보호** 로 이동 합니다. 여기에서 일시 삭제를 사용 하도록 선택할 수 있습니다 .이를 통해 정의 된 보존 기간 내에 실수로 삭제 된 백업 파일을 복구 하 고 실수로 인 한 삭제 또는 악의적인 삭제 로부터 보호를 제공할 수 있습니다.

    ![포털의 데이터 보호 설정을 표시 합니다.](../media/account-create-2.png)

4. 다음으로 Azure 사용 사례에 대 한 백업에 대 한 **고급** 화면에서 기본 설정을 사용 하는 것이 좋습니다.

    ![포털의 고급 설정 탭을 표시 합니다.](../media/account-create-3.png)

5. 태그 지정을 사용 하 고 계정을 만드는 경우 조직의 태그를 추가 합니다.

6. 이제 Veeam 환경에 계정을 추가 하려면 먼저 두 개의 빠른 단계를 수행 해야 합니다. Azure Portal에서 만든 계정으로 이동 하 여 **Blob service** 메뉴 아래에서 **컨테이너** 를 선택 합니다. 컨테이너를 추가 하 고 의미 있는 이름을 선택 합니다. 그런 다음 **설정** 에서 **액세스 키** 항목으로 이동 하 여 **저장소 계정 이름과** 두 개의 액세스 키 중 하나를 복사 합니다. 다음 단계에서 컨테이너 이름, 계정 이름 및 액세스 키가 필요 합니다.

    ![포털에서 컨테이너 만들기를 표시 합니다.](../media/container-b.png)

    ![포털에서 액세스 키 설정을 표시 합니다.](../media/access-key.png)

    > [!Note]
    > Veeam 백업 및 복제는 Azure에 연결 하기 위한 추가 옵션을 제공 합니다. 이 문서의 사용 사례에서 백업 대상으로 Blob Storage Microsoft Azure을 사용 하는 것이 가장 좋은 방법입니다.

7. *(선택 사항)* 배포에 더 많은 보안 계층을 추가할 수 있습니다.

     1. 역할 기반 액세스를 구성 하 여 저장소 계정을 변경할 수 있는 사용자를 제한 합니다. 자세한 내용은 [관리 작업을 위한 기본 제공 역할](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)을 참조 하세요.

     1. 회사 네트워크 외부에서 액세스를 시도할 수 없도록 [저장소 방화벽 설정을](../../../../common/storage-network-security.md) 사용 하 여 계정에 대 한 액세스를 특정 네트워크 세그먼트로 제한 합니다.

        ![포털에서 저장소 방화벽 설정을 표시 합니다.](../media/storage-firewall.png)

     1. 계정에 대 한 [삭제 잠금을](../../../../../azure-resource-manager/management/lock-resources.md) 설정 하 여 저장소 계정이 실수로 삭제 되지 않도록 합니다.

        ![리소스 잠금](../media/resource-lock.png)

     1. 추가 [보안 모범 사례](../../../../../storage/blobs/security-recommendations.md)를 구성 합니다.

8. Veaam 백업 및 복제 관리 콘솔에서 **백업 인프라** -> 개요 창을 마우스 오른쪽 단추로 클릭 하 고 **백업 리포지토리 추가** 를 선택 하 여 구성 마법사를 엽니다. 대화 상자에서 **개체 저장소**  ->  **Microsoft Azure Blob Storage**  ->  **Azure Blob Storage** 를 선택 합니다.

    ![Veeam 리포지토리 마법사에서 개체 저장소를 선택 하는 방법을 보여 줍니다.](../media/veeam-repo-a.png)

    ![Veeam 리포지토리 마법사에서 Microsoft Azure Blob Storage를 선택 하는 방법을 보여 줍니다.](../media/veeam-repo-b.png)

    ![Veeam 리포지토리 마법사에서 Azure Blob Storage를 선택 하는 방법을 보여 줍니다.](../media/veeam-repo-c.png)

9. 다음으로 새 Blob storage 리포지토리의 이름 및 설명을 지정 합니다.

    ![Veeam 리포지토리 마법사에서 리포지토리의 이름을 입력 하는 방법을 보여 줍니다.](../media/veeam-repo-d.png)

10. 다음 단계에서는 Azure storage 계정에 액세스 하기 위한 자격 증명을 추가 합니다. 클라우드 자격 증명 관리자에서 **Microsoft Azure Storage 계정** 을 선택 하 고 저장소 계정 이름 및 액세스 키를 입력 합니다. 지역 선택기에서 **Azure Global** 을 선택 하 고 해당 하는 경우 게이트웨이 서버를 선택 합니다.

    ![Veeam 리포지토리 마법사에서 계정을 지정 하는 방법을 보여 줍니다.](../media/veeam-repo-e.png)

    > [!Note]
    > Veeam 게이트웨이 서버를 사용 하지 않도록 선택 하는 경우 모든 스케일 아웃 리포지토리 익스텐트가 직접 인터넷에 액세스할 수 있는지 확인 합니다.

11. 컨테이너 레지스터에서 Azure Storage 컨테이너를 선택 하 고 백업을 저장할 폴더를 선택 하거나 만듭니다. Veeam에서 사용할 전체 저장소 용량에 대 한 소프트 제한을 정의할 수도 있습니다 (권장). 요약 섹션에 표시 된 정보를 검토 하 고 구성 도구를 완료 합니다. 이제 백업 작업 구성에서 새 리포지토리를 선택할 수 있습니다.

    ![Veeam 리포지토리 마법사에서 컨테이너를 지정 하는 방법을 보여 줍니다.](../media/veeam-repo-f.png)

    ![Veeam 리포지토리 마법사에서 폴더를 만드는 방법을 보여 줍니다.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>운영 가이드

### <a name="azure-alerts-and-performance-monitoring"></a>Azure 경고 및 성능 모니터링

백업을 저장 하는 데 사용 하는 모든 저장소 대상에서와 마찬가지로 Azure 리소스 및 Veeam의 기능을 모두 모니터링 하는 것이 좋습니다. Azure Monitor 및 Veeam의 모니터링 기능 (Veeam 관리 콘솔의 **작업** 노드에 있는 **통계** 탭 또는 Veeam One Reporter 같은 고급 옵션)의 조합은 환경을 정상 상태로 유지 하는 데 도움이 됩니다.

#### <a name="azure-portal"></a>Azure portal

Azure는 [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md)형식으로 강력한 모니터링 솔루션을 제공 합니다. Azure Monitor를 [구성](../../../../common/monitor-storage.md) 하 여 Azure Storage 용량, 트랜잭션, 가용성, 인증 등을 추적할 수 있습니다. 추적 된 메트릭의 전체 참조는 [여기](../../../../blobs/monitor-blob-storage-reference.md)에서 찾을 수 있습니다. 추적할 몇 가지 유용한 메트릭은 BlobCapacity 이며, 최대 [저장소 계정 용량 제한](../../../../common/scalability-targets-standard-account.md), 수신 및 송신을 유지 하 고, Azure storage 계정에서 쓰고 읽는 데이터의 양을 추적 하 고, Azure Storage 및 MediaAgent로 들어오고 나가는 요청에 대 한 왕복 시간을 추적 하는 SuccessE2ELatency.

또한 [로그 경고를 만들어](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) Azure Storage 서비스 상태를 추적 하 고 언제 든 지 [Azure 상태 대시보드](https://status.azure.com/status) 를 볼 수 있습니다.

#### <a name="veeam-reporting"></a>Veeam 보고

- [Veeam 단일 보고 구성](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam 백업 및 복제 경보](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>지원 사례를 여는 방법

Azure 솔루션에 대 한 백업에 대 한 도움이 필요한 경우 Veeam과 Azure를 모두 사용 하 여 케이스를 열어야 합니다. 이렇게 하면 필요한 경우 지원 조직이 공동 작업을 할 수 있습니다.

#### <a name="to-open-a-case-with-veeam"></a>Veeam을 사용 하 여 케이스를 열려면

[Veeam 고객 지원 사이트](https://www.veeam.com/support.html)에서 로그인 하 고 케이스를 엽니다.

Veeam에서 사용할 수 있는 지원 옵션을 이해 하려면 [Veeam 고객 지원 정책](https://www.veeam.com/veeam_software_support_policy_ds.pdf)을 참조 하세요.

을 호출 하 여 대/소문자를 열 수도 있습니다. [전 세계 지원 번호](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Azure에서 케이스를 열려면

[Azure Portal](https://portal.azure.com) 의 맨 위에 있는 검색 표시줄에서 **지원** 검색을 검색 합니다. **도움말 +**  ->  **새 지원 요청** 지원을 선택 합니다.

> [!NOTE]
> 케이스를 열 때 Azure Storage 또는 Azure 네트워킹에 대 한 도움이 필요 합니다. Azure Backup 지정 하지 마십시오. Azure Backup는 Azure 서비스의 이름이 며 사례는 잘못 라우팅됩니다.

### <a name="links-to-relevant-veeam-documentation"></a>관련 Veeam 설명서에 대 한 링크

추가 정보는 다음 Veeam 설명서를 참조 하세요.

- [Veeam 사용자 가이드](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Veeam 아키텍처 가이드](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Marketplace 제공

알고 있고 신뢰할 수 있는 Veeam 솔루션을 계속 사용 하 여 Azure에서 실행 되는 워크 로드를 보호할 수 있습니다. Veeam을 통해 Azure에서 솔루션을 쉽게 배포 하 고 Azure Virtual Machines 및 기타 여러 Azure 서비스를 보호할 수 있었습니다.

- [Azure Marketplace을 통해 Veeam 백업 & 복제 배포](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Veeam의 Azure backup 및 복구 웹 사이트](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>다음 단계

특수 사용 시나리오에 대 한 자세한 내용은 Veeam 웹 사이트에서 다음 리소스를 참조 하세요.

- [Veeam 방법 비디오](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Veeam 기술 설명서](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam 기술 자료 및 FAQ](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
