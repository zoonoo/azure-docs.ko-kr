---
title: Azure Service Fabric 릴리스
description: 최신 기능 및 향상 된 기능에 대 한 릴리스 정보를 Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 636518688aec1f514b551e34219acfc1c9e81aec
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965393"
---
# <a name="service-fabric-releases"></a>Service Fabric 릴리스

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">문제 해결 가이드</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">문제 추적</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">지원</a>옵션 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">지원</a>버전코드 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">샘플</a>

이 문서에서는 최신 릴리스 및 Service Fabric 런타임 및 Sdk 업데이트에 대 한 자세한 정보를 제공 합니다.

## <a name="whats-new-in-service-fabric"></a>Service Fabric의 새로운 기능

### <a name="service-fabric-65"></a>Service Fabric 6.5

최신 Service Fabric 릴리스에는 클러스터 및 응용 프로그램 수명 주기 관리를 용이 하 게 하는 지원 가능성, 안정성 및 성능 향상, 새로운 기능, 버그 수정 및 향상 된 기능이 포함 되어 있습니다.

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
| 2019 년 6 월 11 일 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019년 7월 2일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 년 7 월 29 일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |


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
| 2019년 5월 28일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
