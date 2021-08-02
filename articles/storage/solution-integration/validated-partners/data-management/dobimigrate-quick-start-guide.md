---
title: Datadobi DobiMigrate를 사용하여 파일 데이터를 Azure로 마이그레이션
titleSuffix: Azure Storage
description: Datadobi DobiMigrate를 구현하고 데이터를 Azure Files, Azure NetApp Files 또는 ISV NAS 솔루션으로 마이그레이션하기 위한 시작 가이드를 제공합니다.
author: dukicn
ms.author: nikoduki
ms.date: 04/27/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 6984230044dc70e6e5e05c7ae0dcb4789065c436
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955357"
---
# <a name="migrate-data-to-azure-with-datadobi-dobimigrate"></a>Datadobi DobiMigrate를 사용하여 데이터를 Azure로 마이그레이션

이 문서는 Datadobi DobiMigrate 인프라를 Azure Storage와 통합하는 데 도움이 됩니다. 여기에는 필수 구성 요소, 고려 사항, 구현 및 작업 지침이 포함됩니다.

DobiMigrate는 스토리지 플랫폼 간에 파일 및 개체 마이그레이션을 가능하게 합니다. 온-프레미스의 데이터를 빠르고 쉽고 비용 효율적으로 Azure로 마이그레이션합니다.

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 온-프레미스에서 Azure로의 배포 및 Azure 내 배포에 대한 참조 아키텍처를 제공합니다.

:::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-reference-architecture.png" alt-text="DobiMigrate의 기본 설정에 대해 설명하는 참조 아키텍처":::

Azure 연결을 추가하고 구성하여 기존 DobiMigrate 배포를 Azure와 쉽게 통합할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

약간 미리 계획하면 Azure를 오프사이트 백업 대상 및 복구 사이트로 사용하는 데 도움이 됩니다.

### <a name="get-started-with-azure"></a>Azure 시작

Microsoft는 Azure를 시작하기 위해 수행할 수 있는 프레임워크를 제공합니다. CAF([클라우드 채택 프레임워크](/azure/architecture/cloud-adoption/))는 프로덕션 등급 클라우드 도입을 계획하기 위한 엔터프라이즈 디지털 변환 및 포괄적인 가이드에 대한 자세한 접근 방식입니다. CAF에는 빠르고 안전하게 시작 및 실행하는 데 도움이 되는 단계별 [Azure 설치 가이드](/azure/cloud-adoption-framework/ready/azure-setup-guide/)가 포함되어 있습니다. [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)에서 대화형 버전을 찾을 수 있습니다. 샘플 아키텍처, 애플리케이션 배포에 대한 특정 모범 사례 및 Azure 전문 지식을 얻을 수 있는 무료 교육 리소스를 찾을 수 있습니다.

### <a name="considerations-for-migrations"></a>마이그레이션 고려 사항

Azure로 파일 데이터의 마이그레이션을 고려할 때 몇 가지 중요한 측면이 있습니다. 계속하기 전에 자세히 알아보세요.

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [마이그레이션 도구 비교 행렬](./migration-tools-comparison.md)에서 DobiMigrate가 지원하는 최신 기능

프로덕션 애플리케이션에 영향을 주지 않고 마이그레이션을 지원하기에 충분한 네트워크 용량이 필요합니다. 이 섹션에서는 네트워크 요구 사항을 평가하는 데 사용할 수 있는 도구와 기술에 대해 간략하게 설명합니다.

#### <a name="determine-unutilized-internet-bandwidth"></a>사용되지 않은 인터넷 대역폭 확인

일별로 사용할 수 있는, 일반적으로 사용되지 않은 대역폭(또는 *여유 대역폭*)의 양을 파악하는 것이 중요합니다. 이를 통해 다음에 대한 목표를 충족할 수 있는지 여부를 평가할 수 있습니다.

- 오프라인 방법에 Azure Data Box를 사용하지 않는 경우 마이그레이션의 초기 시간
- 대상 파일 서비스로 최종 전환하기 전에 증분 다시 동기화를 수행하는 데 필요한 시간

Azure가 사용할 수 있는 여유 대역폭을 확인하려면 다음 방법을 사용합니다.

- 기존 Azure ExpressRoute 고객인 경우 Azure Portal에서 [회로 사용량](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)을 확인합니다.
- ISP에 연락하여 기존 일별 및 월별 사용률을 보여 주는 보고서를 요청합니다.
- 라우터/스위치 수준에서 네트워크 트래픽을 모니터링하여 사용률을 측정할 수 있는 몇 가지 도구가 있습니다.
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

## <a name="implementation-guidance"></a>구현 지침

이 섹션에서는 Azure DobiMigrate 배포에 온-프레미스에 대한 Azure Files 공유를 추가하는 방법에 대한 간략한 지침을 제공합니다. 

1. Azure Portal을 열고 **스토리지 계정** 을 검색합니다. 

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-locate-storage-account.png" alt-text="Azure Portal의 검색 상자에서 스토리지를 입력한 경우를 보여 줍니다.":::

    기본 **스토리지 계정** 아이콘을 클릭할 수도 있습니다.

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-portal.png" alt-text="Azure Portal에서 스토리지 계정을 추가하는 방법을 보여 줍니다.":::

2. **만들기** 를 선택하여 계정을 추가합니다.
   1. 기존 리소스 그룹을 선택하거나 **새로 만듭니다**.
   2. 스토리지 계정의 고유한 이름을 입력합니다.
   3. 지역을 선택합니다.
   4. 필요에 따라 **표준** 또는 **프리미엄** 성능을 선택합니다. **프리미엄** 을 선택하는 경우 **프리미엄 계정 유형** 에서 **파일 공유** 를 선택합니다.
   5. 데이터 보호 요구 사항을 충족하는 **[중복도](../../../common/storage-redundancy.md)** 를 선택합니다.
   
   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-1.png" alt-text="Portal의 스토리지 계정 설정을 보여 줍니다.":::

3. 다음으로, **고급** 화면에서 권장되는 기본 설정을 알아봅니다. Azure Files로 마이그레이션하는 경우, **대량 파일 공유** 를 사용하는 것이 좋습니다(사용 가능한 경우).

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-2.png" alt-text="Portal의 고급 설정 탭을 보여 줍니다.":::

4. 지금은 기본 네트워킹 옵션을 그대로 유지하고 **데이터 보호** 로 이동합니다. 일시 삭제를 사용하도록 선택하여 정의된 보존 기간 내에 실수로 삭제된 데이터를 복구할 수 있습니다. 일시 삭제는 실수로 또는 악의적으로 삭제되지 않도록 보호 기능을 제공합니다.

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-3.png" alt-text="Portal의 데이터 보호 설정을 보여 줍니다.":::

5. 태그 지정을 사용하는 경우 조직에 대한 태그를 추가하고 계정을 **생성** 합니다.
 
6. 이제 DobiMigrate 환경에 계정을 추가하려면 먼저 두 개의 빠른 단계를 수행해야 합니다. Azure Portal에서 만든 계정으로 이동하고 파일 서비스 메뉴 아래에서 파일 공유를 선택합니다. 파일 공유를 추가하고 의미 있는 이름을 선택합니다. 그런 다음, 설정에서 액세스 키 항목으로 이동하고 스토리지 계정 이름과 두 액세스 키 중 하나를 복사합니다.

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-access-key.png" alt-text="Portal의 액세스 키 설정을 보여 줍니다.":::

7. Azure 파일 공유의 속성으로 이동하고 Azure 연결을 DobiMigrate에 추가하는 데 필요한 URL 주소를 확인합니다.

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-files-endpoint.png" alt-text="Azure 파일 엔드포인트를 찾습니다.":::

8. (_선택 사항_) 배포에 더 많은 보안 계층을 추가할 수 있습니다.
 
   1. 역할 기반 액세스를 구성하여 스토리지 계정을 변경할 수 있는 사용자를 제한합니다. 자세한 내용은 [관리 작업을 위한 기본 제공 역할](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)을 참조하세요.
 
   2.  [스토리지 방화벽 설정](../../../common/storage-network-security.md)을 사용하여 특정 네트워크 세그먼트로 계정에 대한 액세스를 제한합니다. 회사 네트워크 외부에서 액세스하지 못하도록 방화벽 설정을 구성합니다.

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-storage-firewall.png" alt-text="Portal의 스토리지 방화벽 설정을 보여 줍니다.":::

   3.  계정에 대해 [삭제 잠금](../../../../azure-resource-manager/management/lock-resources.md)을 설정하여 스토리지 계정이 실수로 삭제되지 않도록 합니다.

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-resource-lock.png" alt-text="Portal에서 삭제 잠금을 설정하는 방법을 보여 줍니다.":::

   4.  추가 [보안 모범 사례](../../../blobs/security-recommendations.md)를 구성합니다.

9.  DobiMigrate에서 구성 -> 파일 서버로 이동합니다. **추가** 를 클릭하여 Microsoft Azure Files를 파일 서버 유형으로 추가합니다.

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-server-type.png" alt-text="Microsoft Azure Files를 서버 유형으로 추가합니다.":::

10. 이름, Azure Files 연결 세부 정보 및 스토리지 계정 자격 증명을 지정합니다.
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-connection-details.png" alt-text="Azure Files 연결 세부 정보를 구성합니다.":::

11. Azure Files 연결에 프록시를 할당하고 **연결 테스트** 를 클릭합니다. 프록시가 Azure Files와 통신할 수 있는지 확인하려면 다음을 수행합니다.
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-connection.png" alt-text="연결 테스트 세부 정보입니다.":::

    연결 테스트 결과가 표시됩니다.

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-results.png" alt-text="연결 테스트 결과를 보여 줍니다.":::

12. **SMB 마이그레이션 공유** 아래에 이 스토리지 계정으로 프로비전된 모든 Azure 파일 공유가 표시됩니다. 마이그레이션 범위에 있는 공유에 대해 **매핑** 을 **수동** 으로 설정합니다. 예를 들면 다음과 같습니다.
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobiprotect-azure-files-shares.png" alt-text="사용 가능한 공유를 보여 줍니다.":::

13. **마침** 을 클릭하여 Azure Files 구성을 완료합니다. 그러면 새 마이그레이션 작업을 시작할 수 있습니다.

### <a name="start-a-new-migration"></a>새 마이그레이션 시작

DobiMigrate는 수동으로 마이그레이션 경로를 추가하거나 대량 가져오기를 사용하여 새 마이그레이션을 설정할 수 있습니다. 대량 가져오기는 공통 마이그레이션 옵션을 사용하여 여러 마이그레이션을 추가합니다.

새 마이그레이션을 시작하려면 다음을 수행합니다.

1. 대시보드에서 **새 마이그레이션** 단추를 클릭합니다.
   
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-new-migration.png" alt-text="새 마이그레이션 작업을 시작합니다.":::

1. 마이그레이션할 경로와 원본을 선택합니다.

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-select-source.png" alt-text="마이그레이션할 경로와 원본을 선택합니다.":::

1. **대상** 을 선택합니다.
2. 프로토콜을 확인하고 마이그레이션 옵션을 확인합니다.
3. **마침** 을 클릭하여 마이그레이션 프로세스를 완료합니다.

## <a name="support"></a>지원 

Azure 솔루션으로 마이그레이션할 때 도움이 필요한 경우 Datadobi 및 Azure 모두에서 사례를 열어야 합니다.

### <a name="to-open-a-case-with-datadobi"></a>Datadobi에서 사례를 열려면 다음을 수행합니다.

[Datadobi 지원 사이트](https://support.datadobi.com/s/)에서 로그인하고 사례를 엽니다.

### <a name="to-open-a-case-with-azure"></a>Azure에서 사례를 열려면 다음을 수행합니다.

[Azure Portal](https://portal.azure.com/)의 맨 위에 있는 검색 창에서 **지원** 을 검색합니다. **도움말 + 지원** -> **새 지원 요청** 을 선택합니다.

## <a name="marketplace"></a>Marketplace

Datadobi를 통해 Azure에서 솔루션을 쉽게 배포하고 Azure Virtual Machines 및 기타 여러 Azure 서비스를 보호할 수 있습니다. 자세한 내용은 다음 참조 문서를 참조하세요.

- [DobiMigrate를 사용하여 Azure로 파일 데이터 마이그레이션](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=overview)

## <a name="next-steps"></a>다음 단계

가이드를 방문하여 자세히 알아보세요.

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [DobiMigrate 사용자 설명서](https://downloads.datadobi.com/NAS/olh/latest/dobimigrate.html)
- [DobiMigrate 필수 구성 요소 가이드](https://downloads.datadobi.com/NAS/guides/latest/prerequisites.html)
- [DobiMigrate 설치 가이드](https://downloads.datadobi.com/NAS/guides/latest/installguide.html)