---
title: Azure Service Fabric 릴리스
description: 최신 기능 및 Service Fabric의 향상 된 기능에 대 한 릴리스 정보입니다.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165351"
---
# <a name="service-fabric-releases"></a>Service Fabric 릴리스

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">문제 해결 가이드</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">문제 추적</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">옵션을 지 원하는</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">지원 되는 버전</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">코드 샘플</a>

이 문서에서는 Service Fabric 런타임 및 Sdk에 대 한 업데이트 확인 하 고 최신 버전에서 자세한 정보를 제공합니다.

## <a name="whats-new-in-service-fabric"></a>**Service Fabric의 새로운 기능**

### <a name="service-fabric-65"></a>Service Fabric 6.5

최신 Service Fabric 릴리스용 지원 가능성, 안정성 및 성능 향상, 새로운 기능, 버그 수정 및 클러스터 및 응용 프로그램 수명 주기 관리를 위해 향상 된 기능을 포함 합니다.

> [!IMPORTANT]
> Service Fabric 6.5 도구는 Visual Studio 2015에서 지원 되는 Service Fabric을 사용 하 여 마지막 릴리스입니다. 고객은 이동할 것이 좋습니다 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 앞으로 이동 합니다.

Service Fabric 6.5의 새로운 기능 다음과 같습니다.

- Service Fabric Explorer에 포함 되어는 [이미지를 저장 하는 뷰어](service-fabric-visualizing-your-cluster.md#image-store-viewer) 이미지 저장소에 업로드 한 응용 프로그램을 검사 하는 것에 대 한 합니다.

- [패치 오케스트레이션 응용 프로그램 (POA)](service-fabric-patch-orchestration-application.md) 버전 [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) 자체 진단 향상 된 기능이 많이 포함 되어 있습니다. 이 버전으로 전환 POA 고객을 사용 하는 것이 좋습니다.

- [EventStore 서비스는 기본적으로 사용 됩니다](service-fabric-visualizing-your-cluster.md#event-store) 선택 하지 않으면 Service Fabric 6.5 클러스터에 대 한 합니다.

- 추가 [복제본 수명 주기 이벤트](service-fabric-diagnostics-event-generation-operational.md#replica-events) 상태 저장 서비스에 대 한 합니다.

- [시드 노드 상태의 가시성을 향상](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), 시드 노드가 정상 상태가 아니면 클러스터 수준 경고를 포함 하 여 (*아래로*, *제거* 하거나 *알 수 없는*).

- [서비스 패브릭 응용 프로그램 재해 복구 도구](https://github.com/Microsoft/Service-Fabric-AppDRTool) Service Fabric 상태 저장 서비스가 기본 클러스터에서 재해가 발생 하는 경우 신속 하 게 복구할 수 있습니다. 기본 클러스터의에서 데이터를 정기적으로 백업 및 복원을 사용 하 여 대기 보조 응용 프로그램에서 지속적으로 동기화 됩니다.

- Visual Studio 지원 [Linux 기반 클러스터에.NET Core 앱 게시](service-fabric-how-to-publish-linux-app-vs.md)합니다.

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) Service Fabric 6.5 (및 이상 버전)에 대 한 자동으로 설치 업그레이드 또는 Azure에서 새 Linux 클러스터를 만들 때.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) MacOS/Linux OneBox 클러스터에서 기본적으로 설치 됩니다.

자세한 내용은 참조는 [Service Fabric 6.5 릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)합니다.

## <a name="previous-versions"></a>이전 버전

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 릴리스

| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2018 년 11 월 30 일 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018년 12월 12일 | [Azure Service Fabric 6.4 새로 고침 릴리스에 대 한 Windows 클러스터](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 년 2 월 4 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 년 3 월 4 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 년 4 월 8 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 년 5 월 2 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 년 5 월 28 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
