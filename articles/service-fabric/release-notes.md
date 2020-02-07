---
title: Azure Service Fabric 릴리스
description: Azure Service Fabric에 대 한 릴리스 정보입니다. Service Fabric의 최신 기능 및 향상 된 기능에 대 한 정보를 제공 합니다.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064244"
---
# <a name="service-fabric-releases"></a>Service Fabric 릴리스

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">문제 해결 가이드</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">문제 추적</a> 
| 지원 <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">옵션</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">지원 되는 버전</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">코드 샘플</a>

이 문서에서는 최신 릴리스 및 Service Fabric 런타임 및 Sdk 업데이트에 대 한 자세한 정보를 제공 합니다.

## <a name="whats-new-in-service-fabric"></a>Service Fabric의 새로운 기능

### <a name="service-fabric-70"></a>Service Fabric 7.0

이제 Azure Service Fabric 7.0를 사용할 수 있습니다. Azure Portal 또는 Azure Resource Manager 배포를 통해 7.0으로 업데이트할 수 있습니다. 명절 기간에 대 한 고객의 의견으로 인해 1 월까지 자동 업그레이드를 받도록 설정 된 클러스터 자동 업데이트를 시작 하지 않습니다.
1 월에는 표준 롤아웃 절차를 다시 시작 하 고 자동 업그레이드를 사용 하도록 설정 된 클러스터는 7.0 업데이트를 자동으로 수신 하기 시작 합니다. 롤아웃이 시작 되기 전에 다른 알림을 제공 합니다.
또한 계획 된 릴리스 날짜를 업데이트 하 여이 정책을 고려 하 고 있음을 나타낼 것입니다. 향후 [릴리스 일정](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)에 대 한 업데이트는 여기를 참조 하세요.
 
이는 Service Fabric 최신 릴리스 이며 주요 기능 및 향상 된 기능을 통해 로드 됩니다.

### <a name="key-announcements"></a>주요 공지 사항
 - [**응용 프로그램 비밀 (미리 보기)에 대 한 KeyVaultReference 지원**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): [관리 되는 id](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) 를 사용 하는 Service Fabric 응용 프로그램은 이제 환경 변수, 응용 프로그램 매개 변수 또는 컨테이너 리포지토리 자격 증명으로 Key Vault 비밀 URL을 직접 참조할 수 있습니다. Service Fabric는 응용 프로그램의 관리 되는 id를 사용 하 여 암호를 자동으로 확인 합니다. 
     
- **상태 비저장 서비스의 업그레이드 안전성 향상**: 응용 프로그램을 업그레이드 하는 동안 가용성을 보장 하기 위해 [상태 비저장 서비스에 대 한 최소 인스턴스 수](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) 를 정의 하는 새로운 구성을 도입 했습니다. 이전에는이 값이 모든 서비스에 대해 1 였 고 변경 되지 않았습니다. 이 새로운 서비스별 안전 검사를 사용 하 여 서비스는 응용 프로그램 업그레이드 중에 최소 인스턴스 수를 유지 하 고, 클러스터를 업그레이드 하 고, Service Fabric의 상태 및 안전 검사에 의존 하는 기타 유지 관리를 수행할 수 있습니다.
  
- [**사용자 서비스에 대 한 리소스 제한**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): 사용자는 노드의 사용자 서비스에 대 한 리소스 제한을 설정 하 여 Service Fabric 시스템 서비스의 리소스 고갈 등의 시나리오를 방지할 수 있습니다. 
  
- 복제본 유형에 대 한 [**서비스 이동 비용이 매우 높습니다**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) . 이동 비용이 매우 높은 복제본은 다른 방법으로는 해결할 수 없는 제약 조건 위반이 클러스터에 있는 경우에만 이동 됩니다. "매우 높음" 이동 비용을 사용 하는 경우와 추가 고려 사항에 대 한 자세한 내용은 문서를 참조 하세요.
  
-  **추가 클러스터 안전 검사**:이 릴리스에서는 구성 가능한 시드 노드 쿼럼 안전 검사를 도입 했습니다. 이를 통해 클러스터 수명 주기 및 관리 시나리오에서 사용할 수 있어야 하는 시드 노드 수를 사용자 지정할 수 있습니다. 클러스터를 구성 된 값 아래로 이동 하는 작업이 차단 됩니다. 현재 기본값은 시드 노드의 쿼럼입니다. 예를 들어, 시드 노드가 7 개 있는 경우 5 개 시드 노드 아래에서 수행 하는 작업은 기본적으로 차단 됩니다. 이와 같이 변경 하면 한 번에 하나의 시드 노드만 중지 되도록 최소 안전 값 6을 만들 수 있습니다.
   
- [**Service Fabric Explorer에서 백업 및 복원 서비스를 관리**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)하기 위한 지원이 추가 되었습니다. 이렇게 하면 SFX 내에서 다음 작업을 직접 수행할 수 있습니다. 즉, 백업 및 복원 서비스를 검색 하 고, 백업 정책을 만들고, 자동 백업을 사용 하 고, 임시 백업을 수행 하 고, 복원 작업을 트리거하여 기존 백업을 검색 합니다.

- [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)의 가용성 발표:이 도구는 이동 응용 프로그램을 업그레이드 하는 동안 신뢰할 수 있는 컬렉션에서 사용 된 형식이 이전 버전과 호환 되는지 확인 하는 데 도움이 됩니다. 이를 통해 누락 되거나 호환 되지 않는 형식으로 인해 업그레이드 실패 또는 데이터 손실 및 데이터 손상을 방지할 수 있습니다.

- [**보조 복제본에서 안정적인 읽기 사용**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): 안정적인 읽기는 내보냄 된 값을 반환 하도록 보조 복제본을 제한 합니다.

또한이 릴리스에는 다른 새로운 기능, 버그 수정, 지원 가능성, 안정성 및 성능 향상이 포함 되어 있습니다. 전체 변경 내용 목록은 [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)를 참조 하세요.

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 릴리스

| 릴리스 날짜 | 릴리스 | 추가 정보 |
|---|---|---|
| 2019 년 11 월 18 일 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020 년 1 월 30 일 | [Azure Service Fabric 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020 년 2 월 6 일 | [Azure Service Fabric 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

이 릴리스에는 지원 가능성, 안정성 및 성능 향상, 새로운 기능, 버그 수정 및 향상 된 클러스터 및 응용 프로그램 수명 주기 관리를 위한 향상 된 기능이 포함 되어 있습니다.

> [!IMPORTANT]
> Service Fabric 6.5는 Visual Studio 2015에서 Service Fabric 도구를 지 원하는 최종 릴리스입니다. 고객은 앞으로 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 로 전환 하는 것이 좋습니다.

Service Fabric 6.5의 새로운 같습니다는 다음과 같습니다.

- Service Fabric Explorer는 이미지 저장소에 업로드 한 응용 프로그램을 검사 하기 위한 [Image Store 뷰어](service-fabric-visualizing-your-cluster.md#image-store-viewer) 를 포함 합니다.

- [POA (패치 오케스트레이션 응용 프로그램)](service-fabric-patch-orchestration-application.md) 버전 [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) 에는 많은 자체 진단 기능이 포함 되어 있습니다. POA의 고객은이 버전으로 이동 하는 것이 좋습니다.

- 옵트아웃 (opt out) 하지 않으면 Eventstore 서비스는 Service Fabric 6.5 클러스터에 대해 [기본적으로 사용 하도록 설정 됩니다](service-fabric-visualizing-your-cluster.md#event-store) .

- 상태 저장 서비스에 대 한 [복제본 수명 주기 이벤트](service-fabric-diagnostics-event-generation-operational.md#replica-events) 를 추가 했습니다.

- 시드 노드가 비정상 (*다운*, *제거* 또는 *알 수 없음*) 인 경우 클러스터 수준 경고를 비롯 하 여 [시드 노드 상태를 더 잘 표시](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)합니다.

- [Service Fabric 응용 프로그램 재해 복구 도구](https://github.com/Microsoft/Service-Fabric-AppDRTool) 를 사용 하면 주 클러스터에 재해가 발생할 때 Service Fabric 상태 저장 서비스가 신속 하 게 복구할 수 있습니다. 주 클러스터의 데이터는 정기적인 백업 및 복원을 사용 하 여 보조 대기 응용 프로그램에서 지속적으로 동기화 됩니다.

- [Linux 기반 클러스터에 .Net Core 앱을 게시](service-fabric-how-to-publish-linux-app-vs.md)하기 위한 Visual Studio 지원

- Azure에서 새 Linux 클러스터를 업그레이드 하거나 만들 때 [azure SERVICE FABRIC CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 는 Service Fabric 6.5 이상 버전에 대해 자동으로 설치 됩니다.

- [Sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 은 기본적으로 macos/Linux onebox 클러스터에 설치 됩니다.

자세한 내용은 [Service Fabric 6.5 릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)를 참조 하세요.

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 릴리스

| 릴리스 날짜 | 릴리스 | 추가 정보 |
|---|---|---|
| 2019년 6월 11일 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019년 7월 2일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 년 7 월 29 일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019 년 10 월 14 일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>이전 버전

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 릴리스

| 릴리스 날짜 | 릴리스 | 추가 정보 |
|---|---|---|
| 2018 년 11 월 30 일 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018년 12월 12일 | [Windows 클러스터에 대 한 Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 년 2 월 4 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 년 3 월 4 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 년 4 월 8 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 5 월 2 일, 2019 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 5 월 28 일, 2019 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
