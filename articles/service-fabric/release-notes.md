---
title: Azure 서비스 패브릭 릴리스
description: Azure 서비스 패브릭에 대한 릴리스 정보입니다. 서비스 패브릭의 최신 기능 및 개선 사항에 대한 정보가 포함되어 있습니다.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064244"
---
# <a name="service-fabric-releases"></a>서비스 패브릭 릴리스

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">문제 해결 가이드</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">문제 추적</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">지원 옵션</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">지원 버전</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">코드 샘플</a>

이 문서에서는 서비스 패브릭 런타임 및 SDK에 대한 최신 릴리스 및 업데이트에 대한 자세한 정보를 제공합니다.

## <a name="whats-new-in-service-fabric"></a>서비스 패브릭의 새로운 내용

### <a name="service-fabric-70"></a>서비스 패브릭 7.0

Azure 서비스 패브릭 7.0을 사용할 수 있습니다! Azure 포털을 통해 또는 Azure 리소스 관리자 배포를 통해 7.0으로 업데이트할 수 있습니다. 연말연시 를 전후한 릴리스에 대한 고객 피드백으로 인해 1월까지 자동 업그레이드를 받도록 설정된 클러스터가 자동으로 업데이트되지 않습니다.
1월에 표준 롤아웃 프로시저를 재개하고 자동 업그레이드가 활성화된 클러스터가 7.0 업데이트를 자동으로 수신하기 시작합니다. 롤아웃이 시작되기 전에 또 다른 공지를 제공할 것입니다.
또한 이 정책을 고려하고 있음을 나타내기 위해 예정된 릴리스 날짜도 업데이트할 예정입니다. 향후 릴리스 일정에 대한 업데이트를 보려면 [여기를](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)참조하십시오.
 
이것은 서비스 패브릭의 최신 릴리스이며 주요 기능과 개선 사항이 포함되어 있습니다.

### <a name="key-announcements"></a>주요 공지 사항
 - [**KeyVaultReference 응용 프로그램 암호 지원(미리 보기)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): [관리되는 ID를](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) 사용하도록 설정한 서비스 패브릭 응용 프로그램은 이제 Key Vault 보안 URL을 환경 변수, 응용 프로그램 매개 변수 또는 컨테이너 리포지토리 자격 증명으로 직접 참조할 수 있습니다. 서비스 패브릭은 응용 프로그램의 관리되는 ID를 사용하여 비밀을 자동으로 해결합니다. 
     
- **상태 비수기 서비스에 대한 업그레이드 안전성 향상**: 응용 프로그램 업그레이드 중 가용성을 보장하기 위해 [상태 비수기 서비스에 사용할](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) 수 있는 최소 인스턴스 수를 정의하는 새로운 구성을 도입했습니다. 이전에는 이 값이 모든 서비스에 대해 1이었으며 변경할 수 없었습니다. 이 새로운 서비스별 안전 검사를 통해 서비스 패브릭의 상태 및 안전 검사에 의존하는 응용 프로그램 업그레이드, 클러스터 업그레이드 및 기타 유지 관리 중에 서비스가 최소 한도의 최대 인스턴스를 유지하도록 보장할 수 있습니다.
  
- [**사용자 서비스에 대한 리소스 제한**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): 사용자는 서비스 패브릭 시스템 서비스의 리소스 소모와 같은 시나리오를 방지하기 위해 노드의 사용자 서비스에 대한 리소스 제한을 설정할 수 있습니다. 
  
- 복제본 유형에 대한 [**매우 높은 서비스 이동 비용입니다.**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) 이동 비용이 매우 높은 복제본은 다른 방법으로 수정할 수 없는 클러스터에 제약 조건 위반이 있는 경우에만 이동됩니다. "매우 높은" 이동 비용의 사용이 합리적이고 추가 고려 사항에 대한 자세한 내용은 문서를 참조하십시오.
  
-  **추가 클러스터 안전 검사**: 이 릴리스에서는 구성 가능한 시드 노드 쿼럼 안전 검사를 도입했습니다. 이를 통해 클러스터 수명 주기 및 관리 시나리오 에서 사용할 수 있는 시드 노드 수를 사용자 지정할 수 있습니다. 클러스터를 구성된 값 이하로 하는 작업은 차단됩니다. 오늘날 기본값은 항상 시드 노드의 쿼럼(예: 7개의 시드 노드가 있는 경우) 기본적으로 5개 시드 노드 미만으로 사용할 수 있는 작업이 차단됩니다. 이 변경으로 최소 안전 값 6을 만들 수 있습니다.이 값은 한 번에 하나의 시드 노드만 다운될 수 있습니다.
   
- [**서비스 패브릭 탐색기에서 백업 및 복원 서비스를 관리하기**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)위한 지원이 추가되었습니다. 이렇게 하면 SFX 내에서 직접 백업 및 복원 서비스 검색, 백업 정책 생성, 자동 백업 활성화, adhoc 백업 수행, 복원 작업 트리거 링 및 기존 백업 탐색 등 에서 직접 다음 작업을 수행할 수 있습니다.

- [**ReliableCollectionsMissingTypesTool의**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)가용성 발표 : 이 도구는 롤링 응용 프로그램 업그레이드 중에 신뢰할 수 있는 컬렉션에 사용되는 형식이 앞으로 및 이전 버전과 호환되는지 확인하는 데 도움이 됩니다. 이렇게 하면 누락되었거나 호환되지 않는 형식으로 인한 업그레이드 오류 또는 데이터 손실 및 데이터 손상을 방지할 수 있습니다.

- [**보조 복제본에서 안정적인 읽기 사용**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):Stable 읽기는 보조 복제본을 쿼럼이 수행된 값을 반환하도록 제한합니다.

또한 이 릴리스에는 다른 새로운 기능, 버그 수정 및 지원 가능성, 안정성 및 성능 향상이 포함되어 있습니다. 변경 내용의 전체 목록은 릴리스 [노트를](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)참조하십시오.

### <a name="service-fabric-70-releases"></a>서비스 패브릭 7.0 릴리스

| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2019년 11월 18일 | [Azure 서비스 패브릭 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020년 1월 30일 | [Azure 서비스 패브릭 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020년 2월 6일 | [Azure 서비스 패브릭 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>서비스 패브릭 6.5

이 릴리스에는 지원 가능성, 안정성 및 성능 향상, 새로운 기능, 버그 수정 및 클러스터 및 응용 프로그램 수명 주기 관리를 용이하게 하는 향상된 기능이 포함되어 있습니다.

> [!IMPORTANT]
> 서비스 패브릭 6.5는 Visual Studio 2015에서 서비스 패브릭 도구 지원을 지원하는 최종 릴리스입니다. 고객은 앞으로 Visual [Studio 2019로](https://visualstudio.microsoft.com/vs/) 이동하는 것이 좋습니다.

서비스 패브릭 6.5의 새로운 내용은 다음과 같습니다.

- 서비스 패브릭 탐색기에는 이미지 저장소에 업로드한 응용 프로그램을 검사하기 위한 이미지 저장소 [뷰어가](service-fabric-visualizing-your-cluster.md#image-store-viewer) 포함되어 있습니다.

- [패치 오케스트레이션 응용 프로그램(POA)](service-fabric-patch-orchestration-application.md) 버전 [1.4.0에는](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) 많은 자체 진단 개선 사항이 포함되어 있습니다. POA 고객은 이 버전으로 이동하는 것이 좋습니다.

- [EventStore 서비스는](service-fabric-visualizing-your-cluster.md#event-store) 옵트아웃하지 않은 한 서비스 패브릭 6.5 클러스터에 대해 기본적으로 활성화됩니다.

- 상태 관리 서비스에 대한 [복제본 수명 주기 이벤트가](service-fabric-diagnostics-event-generation-operational.md#replica-events) 추가되었습니다.

- [시드 노드가](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)비정상인 경우 클러스터 수준 경고를 포함하여 시드 노드 상태에 대한 가시성*향상(아래,* *제거됨* 또는 *알 수 없음).*

- [서비스 패브릭 응용 프로그램 재해 복구 도구를](https://github.com/Microsoft/Service-Fabric-AppDRTool) 사용하면 기본 클러스터가 재해를 겪을 때 서비스 패브릭 상태 관리 서비스가 빠르게 복구할 수 있습니다. 주별 백업 및 복원을 사용하여 주별 대기 응용 프로그램에서 기본 클러스터의 데이터가 지속적으로 동기화됩니다.

- Linux 기반 [클러스터에 .NET Core 앱을 게시하기 위한](service-fabric-how-to-publish-linux-app-vs.md)Visual Studio 지원.

- Azure에서 새 Linux 클러스터를 업그레이드하거나 만들 때 [Azure 서비스 패브릭 CLI(SFCTL)가](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 서비스 패브릭 6.5(및 이후 버전)에 자동으로 설치됩니다.

- [SFCTL은](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) MacOS/Linux 원박스 클러스터에 기본적으로 설치됩니다.

자세한 내용은 서비스 [패브릭 6.5 릴리스 정보를](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)참조하십시오.

### <a name="service-fabric-65-releases"></a>서비스 패브릭 6.5 릴리스

| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2019년 6월 11일 | [Azure 서비스 패브릭 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019년 7월 2일 | [Azure 서비스 패브릭 6.5 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019년 7월 29일 | [Azure 서비스 패브릭 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure 서비스 패브릭 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019년 10월 14일 | [Azure 서비스 패브릭 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>이전 버전

### <a name="service-fabric-64-releases"></a>서비스 패브릭 6.4 릴리스

| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2018년 11월 30일 | [Azure 서비스 패브릭 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018년 12월 12일 | [Windows 클러스터에 대한 Azure 서비스 패브릭 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019년 2월 4일 | [Azure 서비스 패브릭 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019년 3월 4일 | [Azure 서비스 패브릭 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019년 4월 8일 | [Azure 서비스 패브릭 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019년 5월 2일 | [Azure 서비스 패브릭 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019년 5월 28일 | [Azure 서비스 패브릭 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
