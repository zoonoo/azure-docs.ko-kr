
---
title: "Azure Service Fabric 클러스터 설정 변경 | Microsoft Docs"
description: "이 문서에서는 사용자 지정할 수 있는 패브릭 설정 및 패브릭 업그레이드 정책에 대해 설명합니다."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: bee47924092a0b327ef3aa5b936116bf311ce8d7
ms.lasthandoff: 03/29/2017


---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>서비스 패브릭 클러스터 설정 및 패브릭 업그레이드 정책 사용자 지정
이 문서에서는 Service Fabric 클러스터에 대한 다양한 패브릭 설정 및 패브릭 업그레이드 정책을 사용자 지정하는 방법을 설명합니다. 포털에서 또는 Azure Resource Manager 템플릿을 사용하여 사용자 지정할 수 있습니다.

> [!NOTE]
> 포털을 통해 모든 설정을 사용할 수 있는 것은 아닙니다. 아래에 나열된 설정을 포털에서 사용할 수 없는 경우 Azure Resource Manager 템플릿을 사용하여 해당 설정을 사용자 지정합니다.
> 

## <a name="customizing-service-fabric-cluster-settings-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿으로 Service Fabric 클러스터 설정 사용자 지정
아래 단계에서는 새로운 *MaxDiskQuotaInMB* 설정을 *Diagnostics* 섹션에 추가하는 방법을 보여 줍니다.

1. https://resources.azure.com으로 이동합니다.
2. 구독 -> 리소스 그룹 -> Microsoft.ServiceFabric -> 클러스터 이름으로 차례로 확장하여 해당 구독으로 이동합니다.
3. 오른쪽 위 모서리에서 "읽기/쓰기"를 선택합니다.
4. [편집]을 선택하고 `fabricSettings` JSON 요소를 업데이트하고 새 요소를 추가합니다.

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

## <a name="fabric-settings-that-you-can-customize"></a>사용자 지정할 수 있는 패브릭 설정
다음은 사용자 지정할 수 있는 패브릭 설정입니다.

### <a name="section-name-diagnostics"></a>섹션 이름: Diagnostics
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ConsumerInstances |string |DCA 소비자 인스턴스 목록 |
| ProducerInstances |string |DCA 생산자 인스턴스 목록 |
| AppEtwTraceDeletionAgeInDays |int, 기본값: 3 |응용 프로그램 ETW 추적을 포함하고 있는 오래된 ETL 파일을 삭제한 이후 경과한 일 수 |
| AppDiagnosticStoreAccessRequiresImpersonation |bool, 기본값: true |응용 프로그램 대신 진단 저장소에 액세스할 때 가장이 필요한지의 여부 |
| MaxDiskQuotaInMB |int, 기본값: 65536 |Windows Fabric 로그 파일의 디스크 할당량(MB) |
| DiskFullSafetySpaceInMB |int, 기본값: 1024 |DCA에서 사용하지 못하도록 보호하기 위해 남아 있는 디스크 공간(MB) |
| ApplicationLogsFormatVersion |int, 기본값: 0 |응용 프로그램 로그 형식의 버전. 지원되는 값은 0과 1입니다. 버전 1은 버전 0보다 더 많은 ETW 이벤트 레코드 필드를 포함합니다. |
| ClusterId |String |클러스터의 고유 ID입니다. 클러스터를 만들 때 생성됩니다. |
| EnableTelemetry |bool, 기본값: true |원격 분석 사용 여부 |
| EnableCircularTraceSession |bool, 기본값: false |플래그에서 순환 추적 세션을 사용해야 하는지 여부를 나타냅니다. |

### <a name="section-name-traceetw"></a>섹션 이름: Trace/Etw
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| Level |int, 기본값: 4 |ETW 추적 수준은 1, 2, 3, 4 값을 가질 수 있습니다. 지원 받으려면 추적 수준을 4에서 유지해야 합니다. |

### <a name="section-name-performancecounterlocalstore"></a>섹션 이름: PerformanceCounterLocalStore
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| IsEnabled |bool, 기본값: true |플래그에서 로컬 노드의 성능 카운터 수집을 사용하는지 여부를 나타냅니다. |
| SamplingIntervalInSeconds |int, 기본값: 60 |수집되는 성능 카운터의 샘플링 간격 |
| Counters |string |수집할 성능 카운터의 쉼표로 구분된 목록 |
| MaxCounterBinaryFileSizeInMB |int, 기본값: 1 |각 성능 카운터 이진 파일의 최대 크기(MB) |
| NewCounterBinaryFileCreationIntervalInMinutes |int, 기본값: 10 |새 성능 카운터 이진 파일이 만들어지는 최대 간격(초) |

### <a name="section-name-setup"></a>섹션 이름: Setup
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| FabricDataRoot |String |Service Fabric 데이터 루트 디렉터리. Azure에 대한 기본값: d:\svcfab |
| FabricLogRoot |문자열 |Service Fabric 로그 루트 디렉터리. SF 로그 및 추적이 배치되는 위치입니다. |
| ServiceRunAsAccountName |String |패브릭 호스트 서비스를 실행할 계정 이름 |
| ServiceStartupType |String |패브릭 호스트 서비스의 시작 유형 |
| SkipFirewallConfiguration |bool, 기본값: false |시스템에서 방화벽 설정을 설정해야 하는지 여부를 지정합니다. Windows 방화벽을 사용하는 경우에만 적용됩니다. 타사 방화벽을 사용하는 경우 사용할 시스템 및 응용 프로그램에 대한 포트를 열어야 합니다. |

### <a name="section-name-transactionalreplicator"></a>섹션 이름: TransactionalReplicator
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| MaxCopyQueueSize |uint, 기본값: 16384 |복제 작업을 유지하는 큐의 초기 크기를 정의하는 최대 값입니다. 2의 거듭제곱이어야 합니다. 런타임 동안 큐가 이 크기로 증가하면 주 복제자와 보조 복제자 간에 작업이 제한됩니다. |
| BatchAcknowledgementInterval | time(초), 기본값: 0.015 | 시간 간격은 초 단위로 지정합니다. 복제자에서 작업을 받은 후 승인을 다시 보내기 전에 대기하는 시간을 결정합니다. 이 기간 동안 받은 다른 작업은 승인을 단일 메시지로 다시 보내어 네트워크 트래픽을 줄이지만 잠재적으로 복제자의 처리량을 줄입니다. |
| MaxReplicationMessageSize |uint, 기본값: 52428800 | 복제 작업의 최대 메시지 크기. 기본값은 50MB입니다. |
| ReplicatorAddress |string, 기본값: "localhost:0" | Windows Fabric 복제자에서 작업을 보내거나 받기 위해 다른 복제본과의 연결을 설정하는 데 사용되는 문자열 형식의 끝점입니다(예: 'IP:Port'). |
| InitialPrimaryReplicationQueueSize |uint, 기본값: 64 | 주 복제 작업을 유지하는 큐의 초기 크기를 정의합니다. 2의 거듭제곱이어야 합니다.|
| MaxPrimaryReplicationQueueSize |uint, 기본값: 8192 |주 복제 큐에 존재할 수 있는 작업의 최대 수. 2의 거듭제곱이어야 합니다. |
| MaxPrimaryReplicationQueueMemorySize |uint, 기본값: 0 |주 복제 큐의 최대 값(바이트) |
| InitialSecondaryReplicationQueueSize |uint, 기본값: 64 |보조 복제자에서 복제 작업을 유지하는 큐의 초기 크기를 정의합니다. 2의 거듭제곱이어야 합니다. |
| MaxSecondaryReplicationQueueSize |uint, 기본값: 16384 |보조 복제 큐에 존재할 수 있는 작업의 최대 수. 2의 거듭제곱이어야 합니다. |
| MaxSecondaryReplicationQueueMemorySize |uint, 기본값: 0 |보조 복제 큐의 최대 값(바이트) |
| SecondaryClearAcknowledgedOperations |bool, 기본값: false |보조 복제자의 작업이 디스크에 플러시된 후 주 복제에 승인되면 해당 작업을 지우는지 여부를 제어하는 부울 값. True로 설정하면 장애 조치 후 복제본을 따라 잡는 동안 새 주 디스크에서 추가 디스크 읽기가 발생할 수 있습니다. |
| MaxMetadataSizeInKB |int, 기본값: 4 |로그 스트림 메타데이터의 최대 크기 |
| MaxRecordSizeInKB |uint, 기본값: 1024 | 로그 스트림 레코드의 최대 크기 |
| CheckpointThresholdInMB |int, 기본값: 50 |로그 사용량이 이 값을 초과하면 검사점이 시작됩니다. |
| MaxAccumulatedBackupLogSizeInMB |int, 기본값: 800 |지정된 백업 로그 체인 내 백업 로그의 최대 누적 크기(MB)입니다. 증분 백업에서 누적된 백업 로그를 초래하는 백업 로그가 생성되는 경우 관련 전체 백업이 이 크기보다 크기 때문에 증분 백업 요청이 실패합니다. 이러한 경우 사용자는 전체 백업을 수행해야 합니다. |
| MaxWriteQueueDepthInKB |int, 기본값: 0 | 이 복제본과 연결되는 로그에 대해 지정하여 코어 로거에서 사용할 수 있는 최대 쓰기 큐 깊이의 정수 값. 코어 로거에서 업데이트하는 중에 처리하지 않을 수 있는 최대 바이트 수입니다. 코어 로거가 해당 값 또는 4의 배수를 계산하는 경우 0이 될 수 있습니다. |
| SharedLogId |String |공유 로그 식별자. GUID이며 각 공유 로그마다 고유해야 합니다. |
| SharedLogPath |String |공유 로그에 대한 경로. 빈 값인 경우 기본 공유 로그가 사용됩니다. |
| SlowApiMonitoringDuration |시간(초), 기본값: 300 | 경고 상태 이벤트가 발생하기 전에 API 지속 시간을 지정합니다.|
| MinLogSizeInMB |int, 기본값: 0 |트랜잭션 로그의 최소 크기입니다. 로그를 이 설정보다 작은 크기로 자를 수 없습니다. 0은 복제자에서 다른 설정에 따라 최소 로그 크기를 결정 함을 나타냅니다. 이 값을 늘리면 관련 로그 레코드가 잘리는 확률이 낮아지므로 부분 복사 및 증분 백업을 수행할 가능성이 높아집니다. |

### <a name="section-name-fabricclient"></a>섹션 이름: FabricClient
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| NodeAddresses |string, 기본값: "" |이름 지정 서비스와 통신하는 데 사용할 수 있는 여러 노드의 주소 컬렉션(연결 문자열). 초기에는 클라이언트에서 주소 중 하나를 임의로 선택하여 연결합니다. 연결 문자열이 둘 이상 제공되고 통신 오류 또는 시간 제한 오류로 인해 연결이 실패하는 경우 Client(클라이언트)에서 다음 주소를 순차적으로 사용하도록 전환합니다. 다시 시도 체계에 대한 자세한 내용은 이름 지정 서비스 주소 다시 시도 섹션을 참조하세요. |
| ConnectionInitializationTimeout |time(초), 기본값: 2 |시간 간격은 초 단위로 지정합니다. 클라이언트에서 게이트웨이에 대한 연결을 열려고 시도 할 때마다 적용되는 연결 시간 제한 간격입니다. |
| PartitionLocationCacheLimit |int, 기본값: 100000 |서비스 확인을 위해 캐시되는 파티션 수(제한하지 않을 경우 0으로 설정) |
| ServiceChangePollInterval |time(초), 기본값: 120 |시간 간격은 초 단위로 지정합니다. 등록된 서비스 변경 알림 콜백을 위해 클라이언트에서 게이트웨이로의 서비스 변경에 대한 연속 폴링 간격입니다. |
| KeepAliveIntervalInSeconds |int, 기본값: 20 |FabricClient 전송에서 연결 유지 메시지를 게이트웨이로 보내는 간격. 0 값인 경우 keepAlive를 사용하지 않도록 설정됩니다. 양수 값이어야 합니다. |
| HealthOperationTimeout |time(초), 기본값: 120 |시간 간격은 초 단위로 지정합니다. 상태 관리자에게 전송되는 보고서 메시지의 시간 제한입니다. |
| HealthReportSendInterval |시간(초), 기본값: 30 |시간 간격은 초 단위로 지정합니다. 보고 구성 요소에서 누적된 상태 보고서를 상태 관리자로 보내는 간격입니다. |
| HealthReportRetrySendInterval |시간(초), 기본값: 30 |시간 간격은 초 단위로 지정합니다. 보고 구성 요소에서 누적된 상태 보고서를 상태 관리자로 다시 보내는 간격입니다. |
| RetryBackoffInterval |time(초), 기본값: 3 |시간 간격은 초 단위로 지정합니다. 작업을 다시 시도하기 전의 백오프 간격입니다. |
| MaxFileSenderThreads |uint, 기본값: 10 |동시에 전송되는 최대 파일 수 |

### <a name="section-name-common"></a>섹션 이름: Common
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| PerfMonitorInterval |time(초), 기본값: 1 |시간 간격은 초 단위로 지정합니다. 성능 모니터링 간격입니다. 0 또는 음수 값으로 설정하면 모니터링을 사용하지 않도록 설정됩니다. |

### <a name="section-name-healthmanager"></a>섹션 이름: HealthManager
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |bool, 기본값: false |클러스터 상태 평가 정책이며, 응용 프로그램 유형별 상태 평가를 사용하도록 설정됩니다. |

### <a name="section-name-fabricnode"></a>섹션 이름: FabricNode
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| StateTraceInterval |시간(초), 기본값: 300 |시간 간격은 초 단위로 지정합니다. 각 노드에서 노드 상태를 추적하고 FM/FMM에서 노드를 추적하는 간격입니다. |

### <a name="section-name-nodedomainids"></a>섹션 이름: NodeDomainIds
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| UpgradeDomainId |string, 기본값: "" |노드가 속한 업그레이드 도메인을 설명합니다. |
| PropertyGroup |NodeFaultDomainIdCollection |노드가 속한 장애 도메인을 설명합니다. 장애 도메인은 데이터 센터에서 노드의 위치를 설명하는 URI를 통해 정의됩니다.  장애 도메인 URI에는 fd:/fd/ 형식 뒤에 URI 경로 세그먼트가 나옵니다.|

### <a name="section-name-nodeproperties"></a>섹션 이름: NodeProperties
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |노드 속성에 대한 문자열 키-값 쌍 컬렉션 |

### <a name="section-name-nodecapacities"></a>섹션 이름: NodeCapacities
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |다양한 메트릭에 대한 노드 용량 컬렉션 |

### <a name="section-name-fabricnode"></a>섹션 이름: FabricNode
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| StartApplicationPortRange |int, 기본값: 0 |호스트 하위 시스템에서 관리하는 응용 프로그램 포트의 시작. Hosting(호스팅)에서 EndpointFilteringEnabled가 true인 경우 필수입니다. |
| EndApplicationPortRange |int, 기본값: 0 |호스팅 하위 시스템에서 관리하는 응용 프로그램 포트의 끝(inclusive 없음). Hosting(호스팅)에서 EndpointFilteringEnabled가 true인 경우 필수입니다. |
| ClusterX509StoreName |string, 기본값: "My" |클러스터 내 통신을 보호하기 위한 클러스터 인증서가 있는 X.509 인증서 저장소의 이름 |
| ClusterX509FindType |string, 기본값: "FindByThumbprint" |ClusterX509StoreName으로 지정한 저장소에서 클러스터 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: "FindByThumbprint", "FindBySubjectName" With "FindBySubjectName". 여러 항목이 일치하는 경우 맨 끝에 있는 만료를 사용하는 항목이 적용됩니다. |
| ClusterX509FindValue |string, 기본값: "" |클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
| ClusterX509FindValueSecondary |string, 기본값: "" |클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
| ServerAuthX509StoreName |string, 기본값: "My" |주 서비스에 대한 서버 인증서가 있는 X.509 인증서 저장소의 이름 |
| ServerAuthX509FindType |string, 기본값: "FindByThumbprint" |ServerAuthX509StoreName에 지정한 저장소에서 서버 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint, FindBySubjectName |
| ServerAuthX509FindValue |string, 기본값: "" |클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
| ServerAuthX509FindValueSecondary |string, 기본값: "" |클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
| ClientAuthX509StoreName |string, 기본값: "My" |FabricClient 기본 관리자 역할에 대한 인증서가 있는 X.509 인증서 저장소의 이름 |
| ClientAuthX509FindType |string, 기본값: "FindByThumbprint" |ClientAuthX509StoreName에 지정한 저장소에서 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint, FindBySubjectName |
| ClientAuthX509FindValue |string, 기본값: "" | FabricClient 기본 관리자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
| ClientAuthX509FindValueSecondary |string, 기본값: "" |FabricClient 기본 관리자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
| UserRoleClientX509StoreName |string, 기본값: "My" |FabricClient 기본 사용자 역할에 대한 인증서가 있는 X.509 인증서 저장소의 이름 |
| UserRoleClientX509FindType |string, 기본값: "FindByThumbprint" |UserRoleClientX509StoreName에 지정한 저장소에서 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint, FindBySubjectName |
| UserRoleClientX509FindValue |string, 기본값: "" |FabricClient 기본 사용자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
| UserRoleClientX509FindValueSecondary |string, 기본값: "" |FabricClient 기본 사용자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |

### <a name="section-name-paas"></a>섹션 이름: Paas
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ClusterId |string, 기본값: "" |구성을 보호하기 위해 패브릭에서 사용하는 X509 인증서 저장소 |

### <a name="section-name-fabrichost"></a>섹션 이름: FabricHost
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| StopTimeout |시간(초), 기본값: 300 |시간 간격은 초 단위로 지정합니다. 호스티드 서비스 활성화, 비활성화 및 업그레이드에 대한 시간 제한 |
| StartTimeout |시간(초), 기본값: 300 |시간 간격은 초 단위로 지정합니다. fabricactivationmanager 시작에 대한 시간 제한 |
| ActivationRetryBackoffInterval |time(초), 기본값: 5 |시간 간격은 초 단위로 지정합니다. 모든 활성화 오류에 대한 백오프 간격입니다. 연속적인 활성화 시도에서 실패할 때마다 시스템에서 MaxActivationFailureCount에 도달할 때까지 활성화를 다시 시도합니다. 모든 시도에서 다시 시도 간격은 연속 활성화 실패와 활성화 백오프 간격을 곱한 값입니다. |
| ActivationMaxRetryInterval |시간(초), 기본값: 300 |시간 간격은 초 단위로 지정합니다. Activation(활성화)를 위한 최대 다시 시도 간격입니다. 연속적인 활성화 시도에서 실패할 때마다 다시 시도 간격은 Min(ActivationMaxRetryInterval; 연속 실패 횟수 * ActivationRetryBackoffInterval)로 계산됩니다. |
| ActivationMaxFailureCount |int, 기본값: 10 |시스템에서 실패한 활성화를 포기하기 전에 다시 시도할 최대 횟수 |
| EnableServiceFabricAutomaticUpdates |bool, 기본값: false |Windows 업데이트를 통해 패브릭을 자동으로 업데이트하도록 설정합니다. |
| EnableServiceFabricBaseUpgrade |bool, 기본값: false |서버에 대한 기본 업데이트를 사용하도록 설정합니다. |
| EnableRestartManagement |bool, 기본값: false |서버를 다시 시작하도록 설정합니다. |


### <a name="section-name-failovermanager"></a>섹션 이름: FailoverManager
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| UserReplicaRestartWaitDuration |time(초), 기본값: 60.0 * 30 |시간 간격은 초 단위로 지정합니다. 보관된 복제본이 중단되면 상태의 복사본이 필요한 새로운 대체 복제본을 만들기 전에 복제본이 다시 시작될 때까지 Windows Fabric에서 대기합니다. |
| QuorumLossWaitDuration |time(초), 기본값: MaxValue |시간 간격은 초 단위로 지정합니다. 파티션을 쿼럼 손실 상태로 있도록 하는 최대 기간입니다. 이 기간이 경과한 후에도 파티션이 여전히 쿼럼 손실 상태로 있으면 중단된 복제본을 쿼럼 손실로 간주하여 파티션이 쿼럼 손실로부터 복구됩니다. 이 경우 잠재적으로 데이터 손실이 발생할 수 있습니다. |
| UserStandByReplicaKeepDuration |time(초), 기본값: 3600.0 * 24 * 7 |시간 간격은 초 단위로 지정합니다. 보관된 복제본이 중단된 상태에서 돌아올 때 복제본이 이미 대체되었을 수 있습니다. 이 타이머는 FM에서 대기 복제본을 삭제하기 전에 보관할 기간을 결정합니다. |
| UserMaxStandByReplicaCount |int, 기본값: 1 |시스템에서 사용자 서비스를 위해 보관하는 StandBy 복제본의 기본 최대 수 |

### <a name="section-name-namingservice"></a>섹션 이름: NamingService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| TargetReplicaSetSize |int, 기본값: 7 |이름 지정 서비스 저장소의 각 파티션에 대한 복제본 집합 수. 복제본 집합의 수를 늘리면 Windows Fabric에 대한 로드가 증가하고 이름 지정 데이터 업데이트를 수행하는 데 시간을 소모하더라도 이름 지정 서비스 저장소에 있는 정보의 안정성 수준이 높아지고 노드 장애의 결과로 정보가 손실될 변화를 낮춥니다.|
|MinReplicaSetSize | int, 기본값: 3 | 업데이트를 완료하기 위해 작성하는 데 필요한 이름 지정 서비스 복제본의 최소 수. 시스템에 이 활성 값보다 적은 수의 복제본이 있는 경우 Reliability System(안정성 시스템)에서 복제본이 복원될 때까지 이름 지정 서비스 저장소에 대한 업데이트를 거부합니다. 이 값은 반드시 TargetReplicaSetSize 이하여야 합니다. |
|ReplicaRestartWaitDuration | time(초), 기본값: 60.0 * 30| 시간 간격은 초 단위로 지정합니다. 이름 지정 서비스 복제본이 중단될 때 이 타이머가 시작됩니다.  만료되면 FM은 다운되는 복제본을 대체하기 시작합니다(분실된 것으로 간주하지 않음). |
|QuorumLossWaitDuration | time(초), 기본값: MaxValue | 시간 간격은 초 단위로 지정합니다. 이름 지정 서비스가 쿼럼 손실 상태가 될 때 이 타이머가 시작됩니다.  만료되면 FM에서 중단된 복제본을 손실된 것으로 간주하고 쿼럼을 복구하려고 시도합니다. 데이터가 손실될 수 있습니다. |
|StandByReplicaKeepDuration | time(초), 기본값: 3600.0 * 2 | 시간 간격은 초 단위로 지정합니다. 이름 지정 서비스 복제본이 중단된 상태에서 돌아올 때 복제본이 이미 대체되었을 수 있습니다.  이 타이머는 FM에서 대기 복제본을 삭제하기 전에 보관할 기간을 결정합니다. |
|PlacementConstraints | string, 기본값: "" | 이름 지정 서비스에 대한 배치 제약 조건. |
|ServiceDescriptionCacheLimit | int, 기본값: 0 | 이름 지정 저장소 서비스의 LRU 서비스 설명 캐시에 유지되는 최대 항목 수(제한하지 않을 경우 0으로 설정). |
|RepairInterval | time(초), 기본값: 5 | 시간 간격은 초 단위로 지정합니다. 기관 소유자와 이름 소유자 간의 이름 지정 불일치 복구가 시작되는 간격 |
|MaxNamingServiceHealthReports | int, 기본값: 10 | 한 번에 이름 지정 저장소 서비스에서 비정상 상태로 보고하는 느린 작업의 최대 수. 0인 경우 모든 느린 작업이 전송됩니다. |
| MaxMessageSize |int, 기본값: 4*1024*1024 |이름 지정을 사용할 때 클라이언트 노드 통신에 대한 최대 메시지 크기. DOS 공격을 낮추려는 경우 기본값은 4MB입니다. |
| MaxFileOperationTimeout |시간(초), 기본값: 30 |시간 간격은 초 단위로 지정합니다. 파일 저장소 서비스 작업에 허용되는 최대 시간 제한. 더 큰 시간 제한을 지정하는 요청은 거부됩니다. |
| MaxOperationTimeout |time(초), 기본값: 600 |시간 간격은 초 단위로 지정합니다. 클라이언트 작업에 허용되는 최대 시간 제한. 더 큰 시간 제한을 지정하는 요청은 거부됩니다. |
| MaxClientConnections |int, 기본값: 1000 |게이트웨이당 허용되는 최대 클라이언트 연결 수. |
| ServiceNotificationTimeout |시간(초), 기본값: 30 |시간 간격은 초 단위로 지정합니다. 클라이언트에 서비스 알림을 배달할 때 사용되는 시간 제한입니다. |
| MaxOutstandingNotificationsPerClient |int, 기본값: 1000 |게이트웨이에서 클라이언트 등록을 강제로 닫기 전에 처리되지 않은 알림의 최대 수. |
| MaxIndexedEmptyPartitions |int, 기본값: 1000 |다시 연결하는 클라이언트를 동기화하기 위해 알림 캐시에 인덱싱되어 있는 빈 파티션의 최대 수. 이 수를 초과하는 빈 파티션은 모두 인덱스에서 조회 버전 오름차순으로 제거됩니다. 다시 연결하는 클라이언트를 다시 연결하면 빈 파티션 업데이트를 계속 동기화하고 받을 수 있지만 동기화 프로토콜에 더 많은 비용이 듭니다. |
| GatewayServiceDescriptionCacheLimit |int, 기본값: 0 |이름 지정 게이트웨이의 LRU 서비스 설명 캐시에 유지되는 최대 항목 수(제한하지 않을 경우 0으로 설정). |
| PartitionCount |int, 기본값: 3 |만들려는 이름 지정 서비스 저장소의 파티션 수. 각 파티션은 자체의 인덱스에 해당하는 단일 파티션 키를 소유하므로 파티션 키[0, PartitionCount)가 존재합니다. 이름 지정 서비스 파티션의 수를 늘리면 PartitionCount * ReplicaSetSize 서비스 복제본을 유지해야 하므로 리소스 사용이 늘어나더라도 모든 백업 복제본 세트에서 보유하는 평균 데이터 양을 줄여 이름 지정 서비스에서 수행할 수 있는 크기가 늘어납니다.|

### <a name="section-name-runas"></a>섹션 이름: RunAs
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| RunAsAccountName |string, 기본값: "" |RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem"|
|RunAsPassword|string, 기본값: "" |RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

### <a name="section-name-runasfabric"></a>섹션 이름: RunAs_Fabric
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| RunAsAccountName |string, 기본값: "" |RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" |
|RunAsPassword|string, 기본값: "" |RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

### <a name="section-name-runashttpgateway"></a>섹션 이름: RunAs_HttpGateway
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| RunAsAccountName |string, 기본값: "" |RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" |
|RunAsPassword|string, 기본값: "" |RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

### <a name="section-name-runasdca"></a>섹션 이름: RunAs_DCA
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| RunAsAccountName |string, 기본값: "" |RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" |
|RunAsPassword|string, 기본값: "" |RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

### <a name="section-name-httpgateway"></a>섹션 이름: HttpGateway
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
|IsEnabled|bool, 기본값: false | httpgateway를 사용하거나 사용하지 않도록 설정합니다. Httpgateway는 기본적으로 사용하지 않도록 설정되며, 이 구성을 활성화하려면 사용하도록 설정해야 합니다. |
|ActiveListeners |uint, 기본값: 50 | http 서버 큐에 게시할 읽기 수. HttpGateway에서 충족할 수 있는 동시 요청 수를 제어합니다. |
|MaxEntityBodySize |uint, 기본값: 4194304 |  http 요청에서 사용할 수 있는 본문의 최대 크기를 제공합니다. 기본값은 4MB입니다. 본문 크기가 이 값보다 크면 Httpgateway 요청이 실패합니다. 최소 읽기 청크 크기가 4,096바이트이므로 이 값은 4096 이상이어야 합니다. |

### <a name="section-name-ktllogger"></a>섹션 이름: KtlLogger
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
|AutomaticMemoryConfiguration |int, 기본값: 1 | 메모리 설정을 자동 및 동적으로 구성해야 하는지 여부를 나타내는 플래그. 0인 경우 메모리 구성 설정을 직접 사용하고 시스템 조건에 따라 변경하지 않습니다. 1인 경우 메모리 설정을 자동으로 구성하며 시스템 조건에 따라 변경할 수 있습니다. |
|WriteBufferMemoryPoolMinimumInKB |int, 기본값: 8388608 |쓰기 버퍼 메모리 풀에 처음으로 할당할 KB 수. 제한하지 않으려면 0을 사용합니다. 기본값은 아래의 SharedLogSizeInMB와 일치해야 합니다. |
|WriteBufferMemoryPoolMaximumInKB | int, 기본값: 0 |쓰기 버퍼 메모리 풀이 확장할 수 있는 KB 수. 제한하지 않으려면 0을 사용합니다. |
|MaximumDestagingWriteOutstandingInKB | int, 기본값: 0 | 공유 로그가 전용 로그보다 먼저 진행될 수 있도록 허용하는 KB 수. 제한하지 않으려면 0을 사용합니다.
|SharedLogPath |string, 기본값: "" | 공유 로그 컨테이너를 배치할 경로 및 파일 이름. 패브릭 데이터 루트 아래의 기본 경로를 사용하려면 ""을 사용합니다. |
|SharedLogId |string, 기본값: "" |공유 로그 컨테이너에 대한 고유 GUID. 패브릭 데이터 루트 아래의 기본 경로를 사용하는 경우 ""을 사용합니다. |
|SharedLogSizeInMB |int, 기본값: 8192 | 공유 로그 컨테이너에 할당할 MB 수 |

### <a name="section-name-applicationgatewayhttp"></a>섹션 이름: ApplicationGateway/Http
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
|IsEnabled |bool, 기본값: false | HttpApplicationGateway를 사용하거나 사용하지 않도록 설정합니다. HttpApplicationGateway는 기본적으로 사용하지 않도록 설정되며, 이 구성을 활성화하려면 사용하도록 설정해야 합니다. |
|NumberOfParallelOperations | uint, 기본값: 1000 | http 서버 큐에 게시할 읽기 수. HttpGateway에서 충족할 수 있는 동시 요청 수를 제어합니다. |
|DefaultHttpRequestTimeout |시간(초). 기본값: 60 |시간 간격은 초 단위로 지정합니다.  http 앱 게이트웨이에서 처리 중인 http 요청에 대한 기본 요청 시간 제한을 지정합니다. |
|ResolveServiceBackoffInterval |time(초), 기본값: 5 |시간 간격은 초 단위로 지정합니다.  실패한 서비스 확인 작업을 다시 시도하기 전의 기본 백오프 간격을 지정합니다. |
|BodyChunkSize |uint, 기본값: 4096 |  본문을 읽는 데 사용되는 청크의 크기(바이트)를 지정합니다. |
|GatewayAuthCredentialType |string, 기본값: "None" | http 앱 게이트웨이 끝점에서 사용할 보안 자격 증명의 유형을 나타냅니다. 유효한 값: "None/X509" |
|GatewayX509CertificateStoreName |string, 기본값: "My" | http 앱 게이트웨이에 대한 서버 인증서가 있는 X.509 인증서 저장소의 이름 |
|GatewayX509CertificateFindType |string, 기본값: "FindByThumbprint" | GatewayX509CertificateStoreName에 지정한 저장소에서 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint, FindBySubjectName |
|GatewayX509CertificateFindValue | string, 기본값: "" | http 앱 게이트웨이 인증서를 찾는 데 사용되는 검색 필터 값. 이 인증서는 https 끝점에서 구성되며, 서비스에서 필요한 경우 앱 ID를 확인하는 데 사용할 수도 있습니다. 먼저 FindValue를 조회하며. 이 FindValue가 없는 경우 FindValueSecondary를 조회합니다. |
|GatewayX509CertificateFindValueSecondary | string, 기본값: "" |http 앱 게이트웨이 인증서를 찾는 데 사용되는 검색 필터 값. 이 인증서는 https 끝점에서 구성되며, 서비스에서 필요한 경우 앱 ID를 확인하는 데 사용할 수도 있습니다. 먼저 FindValue를 조회하며. 이 FindValue가 없는 경우 FindValueSecondary를 조회합니다.|

### <a name="section-name-management"></a>섹션 이름: Management
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ImageStoreConnectionString |SecureString | ImageStore의 루트에 대한 연결 문자열 |
| ImageStoreMinimumTransferBPS | int, 기본값: 1024 |클러스터와 ImageStore 간의 최소 전송 속도. 외부 ImageStore에 액세스할 때 시간 제한을 결정하는 데 사용됩니다. 클러스터와 ImageStore 간에 대기 시간이 높은 경우에만 이 값을 변경하여 클러스터가 외부 ImageStore에서 다운로드하는 데 더 많은 시간을 허용합니다. |
|AzureStorageMaxWorkerThreads | int, 기본값: 25 | 병렬 작업자 스레드의 최대 수 |
|AzureStorageMaxConnections | int, 기본값: 5000 | Azure Storage에 동시에 연결할 수 있는 최대 수 |
|AzureStorageOperationTimeout | time(초), 기본값: 6000 | 시간 간격은 초 단위로 지정합니다. xstore 작업 완료에 대한 시간 제한입니다. |
|ImageCachingEnabled | bool, 기본값: true | 이 구성을 사용하면 캐싱을 사용하거나 사용하지 않도록 설정할 수 있습니다. |
|DisableChecksumValidation | bool, 기본값: false | 이 구성을 사용하면 응용 프로그램 프로비전 중에 체크섬 유효성 검사를 사용하거나 사용하지 않도록 설정할 수 있습니다. |
|DisableServerSideCopy | bool, 기본값: false | 이 구성은 응용 프로그램 프로비전 중에 ImageStore에서 응용 프로그램 패키지의 서버 쪽 복사본을 사용하거나 사용하지 않도록 설정합니다. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>섹션 이름: HealthManager/ClusterHealthPolicy
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ConsiderWarningAsError |bool, 기본값: false |클러스터 상태 평가 정책이며, 경고는 오류로 처리됩니다. |
| MaxPercentUnhealthyNodes | int, 기본값: 0 |클러스터 상태 평가 정책이며, 클러스터가 정상적으로 작동하도록 허용되는 비정상 노드의 최대 백분율입니다. |
| MaxPercentUnhealthyApplications | int, 기본값: 0 |클러스터 상태 평가 정책이며, 클러스터가 정상적으로 작동하도록 허용되는 비정상 응용 프로그램의 최대 백분율입니다. |
|MaxPercentDeltaUnhealthyNodes | int, 기본값: 10 |클러스터 업그레이드 상태 평가 정책이며, 클러스터가 정상적으로 작동하도록 허용되는 델타 비정상 노드의 최대 백분율입니다. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes | int, 기본값: 15 |클러스터 업그레이드 상태 평가 정책이며, 클러스터가 정상적으로 작동하도록 허용되는 업그레이드 도메인의 델타 비정상 노드에 대한 최대 백분율입니다.|

### <a name="section-name-faultanalysisservice"></a>섹션 이름: FaultAnalysisService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| TargetReplicaSetSize |int, 기본값: 0 |NOT_PLATFORM_UNIX_START. FaultAnalysisService의 TargetReplicaSetSize입니다. |
| MinReplicaSetSize |int, 기본값: 0 |FaultAnalysisService의 MinReplicaSetSize입니다. |
| ReplicaRestartWaitDuration |time(초), 기본값: 60분|시간 간격은 초 단위로 지정합니다. FaultAnalysisService의 ReplicaRestartWaitDuration입니다. |
| QuorumLossWaitDuration | time(초), 기본값: MaxValue |시간 간격은 초 단위로 지정합니다. FaultAnalysisService의 QuorumLossWaitDuration입니다. |
| StandByReplicaKeepDuration| time(초), 기본값: 60*24*7분 |시간 간격은 초 단위로 지정합니다. FaultAnalysisService의 StandByReplicaKeepDuration입니다. |
| PlacementConstraints | string, 기본값: ""| FaultAnalysisService의 PlacementConstraints입니다. |
| StoredActionCleanupIntervalInSeconds | int, 기본값: 3600 |저장소를 정리하는 빈도.  터미널 상태에 있는 작업 및 적어도 CompletedActionKeepDurationInSeconds 전에 완료된 작업만 제거합니다. |
| CompletedActionKeepDurationInSeconds | int, 기본값: 604800 | 대체로 터미널 상태에 있는 작업을 유지하는 기간입니다.  또한 정리 작업이 StoredActionCleanupIntervalInSeconds의 간격에서만 수행되기 때문에 이 간격에 따라 달라집니다. 604800은 7일입니다. |
| StoredChaosEventCleanupIntervalInSeconds | int, 기본값: 3600 |저장소를 정리하기 위해 감사하는 빈도. 이벤트 수가 30,000개를 초과하면 정리가 시작됩니다. |

### <a name="section-name-filestoreservice"></a>섹션 이름: FileStoreService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| NamingOperationTimeout |시간(초), 기본값: 60 |시간 간격은 초 단위로 지정합니다. 이름 지정 작업을 수행하기 위한 시간 제한입니다. |
| QueryOperationTimeout | 시간(초), 기본값: 60 |시간 간격은 초 단위로 지정합니다. 쿼리 작업을 수행하기 위한 시간 제한입니다. |
| MaxCopyOperationThreads | uint, 기본값: 0 | 보조 저장소에서 기본 저장소로부터 복사할 수 있는 최대 병렬 파일 수. '0'은 코어 수와 동등합니다. |
| MaxFileOperationThreads | uint, 기본값: 100 | 기본 저장소에서 FileOperations(복사/이동)를 수행할 수 있도록 허용되는 최대 병렬 스레드 수. '0'은 코어 수와 동등합니다. |
| MaxStoreOperations | uint, 기본값: 4096 |기본 저장소에 허용되는 최대 병렬 저장소 트랜잭션 작업 수. '0'은 코어 수와 동등합니다. |
| MaxRequestProcessingThreads | uint, 기본값: 200 |기본 저장소에서 요청을 처리할 수 있도록 허용되는 최대 병렬 스레드 수. '0'은 코어 수와 동등합니다. |
| MaxSecondaryFileCopyFailureThreshold | uint, 기본값: 25| 보조 저장소에서 파일 복사를 포기하기 전에 다시 시도할 최대 횟수 |
| AnonymousAccessEnabled | bool, 기본값: true |FileStoreService 공유에 대한 익명 액세스를 사용하거나 사용하지 않도록 설정합니다. |
| PrimaryAccountType | string, 기본값: "" |FileStoreService에서 공유하는 ACL에 대한 주체의 기본 계정 유형 |
| PrimaryAccountUserName | string, 기본값: "" |FileStoreService에서 공유하는 ACL에 대한 주체의 기본 계정 사용자 이름 |
| PrimaryAccountUserPassword | SecureString, 기본값: 비어 있음 |FileStoreService에서 공유하는 ACL에 대한 주체의 기본 계정 암호 |
| FileStoreService | PrimaryAccountNTLMPasswordSecret | SecureString, 기본값: 비어 있음 | NTLM 인증을 사용할 때 동일한 암호를 생성하기 위해 시드로 사용한 암호 |
| PrimaryAccountNTLMX509StoreLocation | string, 기본값: "LocalMachine"| NTLM 인증을 사용할 때 PrimaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 위치 |
| PrimaryAccountNTLMX509StoreName | string, 기본값: "MY"| NTLM 인증을 사용할 때 PrimaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 이름 |
| PrimaryAccountNTLMX509Thumbprint | string, 기본값: ""|NTLM 인증을 사용할 때 PrimaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 지문 |
| SecondaryAccountType | string, 기본값: ""| FileStoreService에서 공유하는 ACL에 대한 주체의 보조 계정 유형 |
| SecondaryAccountUserName | string, 기본값: ""| FileStoreService에서 공유하는 ACL에 대한 주체의 보조 계정 사용자 이름 |
| SecondaryAccountUserPassword | SecureString, 기본값: 비어 있음 |FileStoreService에서 공유하는 ACL에 대한 주체의 보조 계정 암호  |
| SecondaryAccountNTLMPasswordSecret | SecureString, 기본값: 비어 있음 | NTLM 인증을 사용할 때 동일한 암호를 생성하기 위해 시드로 사용한 암호 |
| SecondaryAccountNTLMX509StoreLocation | string, 기본값: "LocalMachine" |NTLM 인증을 사용할 때 SecondaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 위치 |
| SecondaryAccountNTLMX509StoreName | string, 기본값: "MY" |NTLM 인증을 사용할 때 SecondaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 이름 |
| SecondaryAccountNTLMX509Thumbprint | string, 기본값: ""| NTLM 인증을 사용할 때 SecondaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 지문 |

### <a name="section-name-imagestoreservice"></a>섹션 이름: ImageStoreService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| 사용 |bool, 기본값: false |ImageStoreService의 Enabled 플래그입니다. |
| TargetReplicaSetSize | int, 기본값: 7 |ImageStoreService의 TargetReplicaSetSize입니다. |
| MinReplicaSetSize | int, 기본값: 3 |ImageStoreService의 MinReplicaSetSize입니다. |
| ReplicaRestartWaitDuration | time(초), 기본값: 60.0 * 30 | 시간 간격은 초 단위로 지정합니다. ImageStoreService의 ReplicaRestartWaitDuration입니다. |
| QuorumLossWaitDuration | time(초), 기본값: MaxValue | 시간 간격은 초 단위로 지정합니다. ImageStoreService의 QuorumLossWaitDuration입니다. |
| StandByReplicaKeepDuration | time(초), 기본값: 3600.0 * 2 | 시간 간격은 초 단위로 지정합니다. ImageStoreService의 StandByReplicaKeepDuration입니다. |
| PlacementConstraints | string, 기본값: "" | ImageStoreService의 PlacementConstraints입니다. |
| ClientUploadTimeout | time(초), 기본값: 1800 |시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 업로드 요청의 시간 제한 값입니다. |
| ClientCopyTimeout | time(초), 기본값: 1800 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 복사 요청의 시간 제한 값입니다. |
| ClientDownloadTimeout | time(초), 기본값: 1800 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 다운로드 요청의 시간 제한 값입니다. |
| ClientListTimeout | time(초), 기본값: 600 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 목록 요청의 시간 제한 값입니다. |
| ClientDefaultTimeout | time(초), 기본값: 180 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 모든 비업로드/비다운로드 요청(예: exists, delete)의 제한 시간 값입니다. |

### <a name="section-name-imagestoreclient"></a>섹션 이름: ImageStoreClient
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ClientUploadTimeout |time(초), 기본값: 1800 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 업로드 요청의 시간 제한 값입니다. |
| ClientCopyTimeout | time(초), 기본값: 1800 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 복사 요청의 시간 제한 값입니다. |
|ClientDownloadTimeout | time(초), 기본값: 1800 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 다운로드 요청의 시간 제한 값입니다. |
|ClientListTimeout | time(초), 기본값: 600 |시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 최상위 수준 목록 요청의 시간 제한 값입니다. |
|ClientDefaultTimeout | time(초), 기본값: 180 | 시간 간격은 초 단위로 지정합니다. 이미지 저장소 서비스에 대한 모든 비업로드/비다운로드 요청(예: exists, delete)의 제한 시간 값입니다. |

### <a name="section-name-tokenvalidationservice"></a>섹션 이름: TokenValidationService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| 공급자 |string, 기본값: "DSTS" |사용하도록 설정할 쉼표로 구분된 토큰 유효성 검사 공급자 목록(유효한 공급자: DSTS, AAD). 현재 단일 공급자만 언제든지 사용할 수 있습니다. |

### <a name="section-name-upgradeorchestrationservice"></a>섹션 이름: UpgradeOrchestrationService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| TargetReplicaSetSize |int, 기본값: 0 |UpgradeOrchestrationService의 TargetReplicaSetSize입니다. |
| MinReplicaSetSize |int, 기본값: 0 | UpgradeOrchestrationService 용 MinReplicaSetSize입니다.
| ReplicaRestartWaitDuration | time(초), 기본값: 60분| 시간 간격은 초 단위로 지정합니다. UpgradeOrchestrationService의 ReplicaRestartWaitDuration입니다. |
| QuorumLossWaitDuration | time(초), 기본값: MaxValue | 시간 간격은 초 단위로 지정합니다. UpgradeOrchestrationService의 QuorumLossWaitDuration입니다. |
| StandByReplicaKeepDuration | time(초), 기본값: 60*24*7분 | 시간 간격은 초 단위로 지정합니다. UpgradeOrchestrationService의 StandByReplicaKeepDuration입니다. |
| PlacementConstraints | string, 기본값: "" | UpgradeOrchestrationService의 PlacementConstraints입니다. |
| AutoupgradeEnabled | bool, 기본값: true | 목표 상태(goal-state) 파일에 기반한 자동 폴링 및 업그레이드 작업입니다. |
| UpgradeApprovalRequired | bool, 기본값: false | 코드를 업그레이드하도록 설정하려면 계속하기 전에 먼저 관리자의 승인이 필요합니다. |

### <a name="section-name-upgradeservice"></a>섹션 이름: UpgradeService
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| PlacementConstraints |string, 기본값: "" |UpgradeService의 PlacementConstraints입니다. |
| TargetReplicaSetSize | int, 기본값: 3 | UpgradeService의 TargetReplicaSetSize입니다. |
| MinReplicaSetSize | int, 기본값: 2 | UpgradeService의 MinReplicaSetSize입니다. |
| CoordinatorType | string, 기본값: "WUTest"| UpgradeService의 CoordinatorType입니다. |
| BaseUrl | string, 기본값: "" |UpgradeService의 BaseUrl입니다. |
| ClusterId | string, 기본값: "" | UpgradeService의 ClusterId입니다. |
| X509StoreName | string, 기본값: "My"| UpgradeService의 X509StoreName입니다. |
| X509StoreLocation | string, 기본값: "" | UpgradeService의 X509StoreLocation입니다. |
| X509FindType | string, 기본값: ""| UpgradeService의 X509FindType입니다. |
| X509FindValue | string, 기본값: "" | UpgradeService의 X509FindValue입니다. |
| X509SecondaryFindValue | string, 기본값: "" | UpgradeService의 X509SecondaryFindValue입니다. |
| OnlyBaseUpgrade | bool, 기본값: false | UpgradeService의 OnlyBaseUpgrade입니다. |
| TestCabFolder | string, 기본값: "" | UpgradeService의 TestCabFolder입니다. |

### <a name="section-name-securityclientaccess"></a>섹션 이름: Security/ClientAccess
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| CreateName |string, 기본값: "Admin" |이름 지정 URI 만들기에 대한 보안 구성 |
| DeleteName |string, 기본값: "Admin" |이름 지정 URI 삭제에 대한 보안 구성 |
| PropertyWriteBatch |string, 기본값: "Admin" |이름 지정 속성의 쓰기 작업에 대한 보안 구성 |
| CreateService |string, 기본값: "Admin" | 서비스 만들기에 대한 보안 구성 |
| CreateServiceFromTemplate |string, 기본값: "Admin" |템플릿에서 서비스 만들기에 대한 보안 구성 |
| UpdateService |string, 기본값: "Admin" |서비스 업데이트에 대한 보안 구성 |
| DeleteService  |string, 기본값: "Admin" |서비스 삭제에 대한 보안 구성 |
| ProvisionApplicationType |string, 기본값: "Admin" | 응용 프로그램 유형 프로비전에 대한 보안 구성 |
| CreateApplication |string, 기본값: "Admin" | 응용 프로그램 만들기에 대한 보안 구성 |
| DeleteApplication |string, 기본값: "Admin" | 응용 프로그램 삭제에 대한 보안 구성 |
| UpgradeApplication |string, 기본값: "Admin" | 응용 프로그램 업그레이드 시작 또는 중단에 대한 보안 구성 |
| RollbackApplicationUpgrade |string, 기본값: "Admin" | 응용 프로그램 업그레이드 롤백에 대한 보안 구성 |
| UnprovisionApplicationType |string, 기본값: "Admin" | 응용 프로그램 유형 프로비전 해제에 대한 보안 구성 |
| MoveNextUpgradeDomain |string, 기본값: "Admin" | 명시적 업그레이드 도메인으로 응용 프로그램 업그레이드 다시 시작에 대한 보안 구성 |
| ReportUpgradeHealth |string, 기본값: "Admin" | 현재 업그레이드 진행으로 응용 프로그램 업그레이드 다시 시작에 대한 보안 구성 |
| ReportHealth |string, 기본값: "Admin" | 상태 보고에 대한 보안 구성 |
| ProvisionFabric |string, 기본값: "Admin" | MSI 및/또는 클러스터 매니페스트 프로비전에 대한 보안 구성 |
| UpgradeFabric |string, 기본값: "Admin" | 클러스터 업그레이드 시작에 대한 보안 구성 |
| RollbackFabricUpgrade |string, 기본값: "Admin" | 클러스터 업그레이드 롤백에 대한 보안 구성 |
| UnprovisionFabric |string, 기본값: "Admin" | MSI 및/또는 클러스터 매니페스트 프로비전 해제에 대한 보안 구성 |
| MoveNextFabricUpgradeDomain |string, 기본값: "Admin" | 명시적 업그레이드 도메인으로 클러스터 업그레이드 다시 시작에 대한 보안 구성 |
| ReportFabricUpgradeHealth |string, 기본값: "Admin" | 현재 업그레이드 진행으로 클러스터 업그레이드 다시 시작에 대한 보안 구성 |
| StartInfrastructureTask |string, 기본값: "Admin" | 인프라 작업 시작에 대한 보안 구성 |
| FinishInfrastructureTask |string, 기본값: "Admin" | 인프라 작업 완료에 대한 보안 구성 |
| ActivateNode |string, 기본값: "Admin" | 노드 활성화에 대한 보안 구성 |
| DeactivateNode |string, 기본값: "Admin" | 단일 노드 비활성화에 대한 보안 구성 |
| DeactivateNodesBatch |string, 기본값: "Admin" | 여러 노드 비활성화에 대한 보안 구성 |
| RemoveNodeDeactivations |string, 기본값: "Admin" | 여러 노드에서 비활성화 되돌리기에 대한 보안 구성 |
| GetNodeDeactivationStatus |string, 기본값: "Admin" | 비활성화 상태 검사에 대한 보안 구성 |
| NodeStateRemoved |string, 기본값: "Admin" | 제거된 노드 상태 보고에 대한 보안 구성 |
| RecoverPartition |string, 기본값: "Admin" | 단일 파티션 복구에 대한 보안 구성 |
| RecoverPartitions |string, 기본값: "Admin" | 여러 파티션 복구에 대한 보안 구성 |
| RecoverServicePartitions |string, 기본값: "Admin" | 서비스 파티션 복구에 대한 보안 구성 |
| RecoverSystemPartitions |string, 기본값: "Admin" | 시스템 서비스 파티션 복구에 대한 보안 구성 |
| ReportFault |string, 기본값: "Admin" | 오류 보고에 대한 보안 구성 |
| InvokeInfrastructureCommand |string, 기본값: "Admin" | 인프라 작업 관리 명령에 대한 보안 구성 |
| FileContent |string, 기본값: "Admin" | 이미지 저장소 클라이언트 파일 전송(클러스터 외부)에 대한 보안 구성 |
| FileDownload |string, 기본값: "Admin" | 이미지 저장소 클라이언트 파일 다운로드 시작(클러스터 외부)에 대한 보안 구성 |
| InternalList |string, 기본값: "Admin" | 이미지 저장소 클라이언트 파일 목록 작업(내부)에 대한 보안 구성 |
| 삭제 |string, 기본값: "Admin" | 이미지 저장소 클라이언트 삭제 작업에 대한 보안 구성 |
| 업로드 |string, 기본값: "Admin" | 이미지 저장소 클라이언트 업로드 작업에 대한 보안 구성 |
| GetStagingLocation |string, 기본값: "Admin" | 이미지 저장소 클라이언트 스테이징 위치 검색에 대한 보안 구성 |
| GetStoreLocation |string, 기본값: "Admin" | 이미지 저장소 클라이언트 저장소 위치 검색에 대한 보안 구성 |
| NodeControl |string, 기본값: "Admin" | 노드 시작, 중지 및 다시 시작에 대한 보안 구성 |
| CodePackageControl |string, 기본값: "Admin" | 코드 패키지 다시 시작에 대한 보안 구성 |
| UnreliableTransportControl |string, 기본값: "Admin" | 동작 추가 및 제거에 대한 신뢰할 수 없는 전송 |
| MoveReplicaControl |string, 기본값: "Admin" | 복제본 이동 |
| PredeployPackageToNode |string, 기본값: "Admin" | 배포 전 API |
| StartPartitionDataLoss |string, 기본값: "Admin" | 파티션에 데이터 손실을 유도합니다. |
| StartPartitionQuorumLoss |string, 기본값: "Admin" | 파티션에 쿼럼 손실을 유도합니다. |
| StartPartitionRestart |string, 기본값: "Admin" | 파티션의 일부 또는 모든 복제본을 동시에 다시 시작합니다. |
| CancelTestCommand |string, 기본값: "Admin" | 실행 중인 특정 TestCommand를 취소합니다. |
| StartChaos |string, 기본값: "Admin" | 아직 시작되지 않은 Chaos(격리 안 함)를 시작합니다. |
| StopChaos |string, 기본값: "Admin" | 시작된 Chaos를 중지합니다. |
| StartNodeTransition |string, 기본값: "Admin" | 노드 전환 시작에 대한 보안 구성 |
| StartClusterConfigurationUpgrade |string, 기본값: "Admin" | 파티션에 StartClusterConfigurationUpgrade를 유도합니다. |
| GetUpgradesPendingApproval |string, 기본값: "Admin" | 파티션에 GetUpgradesPendingApproval을 유도합니다. |
| StartApprovedUpgrades |string, 기본값: "Admin" | 파티션에 StartApprovedUpgrades를 유도합니다. |
| Ping |string, 기본값: "Admin\|\|User" | 클라이언트 ping에 대한 보안 구성 |
| 쿼리 |string, 기본값: "Admin\|\|User" | 쿼리에 대한 보안 구성 |
| NameExists |string, 기본값: "Admin\|\|User" | 이름 지정 URI 존재 확인에 대한 보안 구성 |
| EnumerateSubnames |string, 기본값: "Admin\|\|User" | 이름 지정 URI 열거형에 대한 보안 구성 |
| EnumerateProperties |string, 기본값: "Admin\|\|User" | 이름 지정 속성 열거형에 대한 보안 구성 |
| PropertyReadBatch |string, 기본값: "Admin\|\|User" | 이름 지정 속성 읽기 작업에 대한 보안 구성 |
| GetServiceDescription |string, 기본값: "Admin\|\|User" | 장기 폴링 서비스 알림 및 읽기 서비스 설명에 대한 보안 구성 |
| ResolveService |string, 기본값: "Admin\|\|User" | 불만 기반 서비스 확인에 대한 보안 구성 |
| ResolveNameOwner |string, 기본값: "Admin\|\|User" | 이름 지정 URI 소유자 확인에 대한 보안 구성 |
| ResolvePartition |string, 기본값: "Admin\|\|User" | 시스템 서비스 확인에 대한 보안 구성 |
| ServiceNotifications |string, 기본값: "Admin\|\|User" | 이벤트 기반 서비스 알림에 대한 보안 구성 |
| PrefixResolveService |string, 기본값: "Admin\|\|User" | 불만 기반 서비스 접두사 확인에 대한 보안 구성 |
| GetUpgradeStatus |string, 기본값: "Admin\|\|User" | 응용 프로그램 업그레이드 상태 폴링에 대한 보안 구성 |
| GetFabricUpgradeStatus |string, 기본값: "Admin\|\|User" | 클러스터 업그레이드 상태 폴링에 대한 보안 구성 |
| InvokeInfrastructureQuery |string, 기본값: "Admin\|\|User" | 인프라 작업 쿼리에 대한 보안 구성 |
| 나열 |string, 기본값: "Admin\|\|User" | 이미지 저장소 클라이언트 파일 목록 작업에 대한 보안 구성 |
| ResetPartitionLoad |string, 기본값: "Admin\|\|User" | failoverUnit의 로드 다시 설정에 대한 보안 구성 |
| ToggleVerboseServicePlacementHealthReporting | string, 기본값: "Admin\|\|User" | Verbose ServicePlacement HealthReporting 전환에 대한 보안 구성 |
| GetPartitionDataLossProgress | string, 기본값: "Admin\|\|User" | 데이터 손실 API 호출에 대한 진행률을 가져옵니다. |
| GetPartitionQuorumLossProgress | string, 기본값: "Admin\|\|User" | 쿼럼 손실 API 호출에 대한 진행률을 가져옵니다. |
| GetPartitionRestartProgress | string, 기본값: "Admin\|\|User" | 파티션 다시 시작 API 호출에 대한 진행률을 가져옵니다. |
| GetChaosReport | string, 기본값: "Admin\|\|User" | 지정한 시간 범위 내에서 Chaos(격리 안 함) 상태를 가져옵니다. |
| GetNodeTransitionProgress | string, 기본값: "Admin\|\|User" | 노드 전환 명령의 진행률 가져오기에 대한 보안 구성 |
| GetClusterConfigurationUpgradeStatus | string, 기본값: "Admin\|\|User" | 파티션에 GetClusterConfigurationUpgradeStatus를 유도합니다. |
| GetClusterConfiguration | string, 기본값: "Admin\|\|User" | 파티션에 GetClusterConfiguration을 유도합니다. |

### <a name="section-name-reconfigurationagent"></a>섹션 이름: ReconfigurationAgent
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | time(초), 기본값: 900 |시간 간격은 초 단위로 지정합니다. 긴밀하게 붙어 있는 복제본을 갖춘 서비스 호스트를 종료하기 전에 시스템에서 대기하는 기간입니다. |
| ServiceApiHealthDuration | time(초), 기본값: 30분 | 시간 간격은 초 단위로 지정합니다. ServiceApiHealthDuration은 정상이 아닌 서비스 API로 보고하기 전에 서비스 API가 실행될 때까지 대기하는 시간을 정의합니다. |
| ServiceReconfigurationApiHealthDuration | 시간(초), 기본값: 30 | 시간 간격은 초 단위로 지정합니다. ServiceReconfigurationApiHealthDuration은 다시 구성 중인 서비스를 비정상으로 보고하기까지의 시간을 정의합니다. |
| PeriodicApiSlowTraceInterval | time(초), 기본값: 5분 | 시간 간격은 초 단위로 지정합니다. PeriodicApiSlowTraceInterval은 API 모니터에서 느린 API 호출을 다시 추적할 간격을 정의합니다. |
| NodeDeactivationMaxReplicaCloseDuration | time(초), 기본값: 900 | 시간 간격은 초 단위로 지정합니다. 노드 비활성화를 차단하고 있는 서비스 호스트를 종료하기 전에 대기하는 최대 시간입니다. |
| FabricUpgradeMaxReplicaCloseDuration | time(초), 기본값: 900 | 시간 간격은 초 단위로 지정합니다. 닫지 않은 복제본의 서비스 호스트를 종료하기 전에 RA에서 대기하는 최대 기간입니다. |
| IsDeactivationInfoEnabled | bool, 기본값: true | RA에서 기본 다시 선택을 수행하는 데 비활성화 정보를 사용할지 여부를 결정합니다. 새 클러스터의 경우 이 구성을 true로 설정해야 합니다. 업그레이드 중인 기존 클러스터의 경우 이 기능을 사용하도록 설정하는 방법은 릴리스 정보를 참조하세요. |

### <a name="section-name-placementandloadbalancing"></a>섹션 이름: PlacementAndLoadBalancing
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| TraceCRMReasons |bool, 기본값: true |CRM에서 작업 이벤트 채널로의 이동을 발급한 이유를 추적할지 여부를 지정합니다. |
| ValidatePlacementConstraint | bool, 기본값: true | 서비스의 ServiceDescription을 업데이트할 때 서비스에 대한 PlacementConstraint 식의 유효성을 검사할지 여부를 지정합니다. |
| PlacementConstraintValidationCacheSize | int, 기본값: 10000 | 배치 제약 조건 식의 빠른 유효성 검사와 캐싱에 사용되는 테이블의 크기를 제한합니다. |
|VerboseHealthReportLimit | int, 기본값: 20 | 상태 경고를 보고하기 전에 복제본이 배치되지 않아야 하는 횟수를 정의합니다(자세한 상태 보고를 사용하도록 설정한 경우). |
|ConstraintViolationHealthReportLimit | int, 기본값: 50 | 진단을 수행하고 상태 보고서를 내보내기 전에 제약 조건 위반 복제본이 영구적으로 고정되지 않아야 하는 횟수를 정의합니다. |
|DetailedConstraintViolationHealthReportLimit | int, 기본값: 200 | 진단을 수행하고 자세한 상태 보고서를 내보내기 전에 제약 조건 위반 복제본이 영구적으로 고정되지 않아야 하는 횟수를 정의합니다. |
|DetailedVerboseHealthReportLimit | int, 기본값: 200 | 자세한 상태 보고서를 내보내기 전에 배치되지 않은 복제본이 영구적으로 배치되지 않아야 하는 횟수를 정의합니다. |
|ConsecutiveDroppedMovementsHealthReportLimit | int, 기본값: 20 | 진단을 수행하고 상태 경고를 내보내기 전에 ResourceBalancer에서 발급한 Movements(이동)가 삭제되는 연속 횟수를 정의합니다. 음수: 이 조건에서는 경고를 내보내지 않습니다. |
|DetailedNodeListLimit | int, 기본값: 15 | 배치되지 않은 복제본 보고서에서 자르기 전에 포함할 제약 조건당 노드 수를 정의합니다. |
|DetailedPartitionListLimit | int, 기본값: 15 | 진단에서 자르기 전에 포함할 제약 조건의 진단 항목당 파티션 수를 정의합니다. |
|DetailedDiagnosticsInfoListLimit | int, 기본값: 15 | 진단에서 자르기 전에 포함할 제약 조건당 진단 항목 수(세부 정보 포함)를 정의합니다.|
|PLBRefreshGap | time(초), 기본값: 1 | 시간 간격은 초 단위로 지정합니다. PLB에서 상태를 다시 새로 고치기 전에 경과해야 하는 최소 시간을 정의합니다. |
|MinPlacementInterval | time(초), 기본값: 1 | 시간 간격은 초 단위로 지정합니다. 두 번의 연속 배치 라운드를 통과해야 하는 최소 시간을 정의합니다. |
|MinConstraintCheckInterval | time(초), 기본값: 1 | 시간 간격은 초 단위로 지정합니다. 두 번의 연속 제약 조건 확인 라운드가 통과해야 하는 최소 시간을 정의합니다. |
|MinLoadBalancingInterval | time(초), 기본값: 5 | 시간 간격은 초 단위로 지정합니다. 두 번의 연속 분산 라운드를 통과해야 하는 최소 시간을 정의합니다. |
|BalancingDelayAfterNodeDown | time(초), 기본값: 120 |시간 간격은 초 단위로 지정합니다. 노드 작동 중단 이벤트 이후 이 기간 내에 작업 분산을 시작하면 안됩니다. |
|BalancingDelayAfterNewNode | time(초), 기본값: 120 |시간 간격은 초 단위로 지정합니다. 새 노드를 추가한 이후 이 기간 내에 작업 분산을 시작하면 안됩니다. |
|ConstraintFixPartialDelayAfterNodeDown | time(초), 기본값: 120 | 시간 간격은 초 단위로 지정합니다. 노드 작동 중단 이후 이 기간 내에 FaultDomain 및 UpgradeDomain 제한 조건 위반을 수정하면 안됩니다. |
|ConstraintFixPartialDelayAfterNewNode | time(초), 기본값: 120 | 시간 간격은 초 단위로 지정합니다. 새 노드를 추가한 이후 이 기간 내에 FaultDomain 및 UpgradeDomain 제약 조건 위반을 수정하면 안됩니다. |
|GlobalMovementThrottleThreshold | uint, 기본값: 1000 | GlobalMovementThrottleCountingInterval에서 지시한 이전 간격에서 분산 단계에 허용되는 최대 이동 횟수 |
|GlobalMovementThrottleThresholdForPlacement | uint, 기본값: 0 | GlobalMovementThrottleCountingInterval에서 지시한 이전 간격에서 배치 단계에 허용되는 최대 이동 횟수. 0은 제한하지 않음을 나타냅니다.|
|GlobalMovementThrottleThresholdForBalancing | uint, 기본값: 0 | GlobalMovementThrottleCountingInterval에서 지시한 이전 간격에서 분산 단계에 허용되는 최대 이동 횟수. 0은 제한하지 않음을 나타냅니다. |
|GlobalMovementThrottleCountingInterval | time(초), 기본값: 600 | 시간 간격은 초 단위로 지정합니다. GlobalMovementThrottleThreshold와 함께 사용되는 도메인 복제본 이동별로 추적할 이전 간격의 길이를 나타냅니다. 전역 제한을 모두 무시하려면 0으로 설정할 수 있습니다. |
|MovementPerPartitionThrottleThreshold | uint, 기본값: 50 | MovementPerPartitionThrottleCountingInterval에서 지시한 이전 간격에서 파티션의 복제본에 대한 관련 이동의 분산 수가 MovementPerFailoverUnitThrottleThreshold에 도달하거나 초과한 경우 해당 파티션에 대한 분산 관련 작업이 수행되지 않습니다. |
|MovementPerPartitionThrottleCountingInterval | time(초), 기본값: 600 | 시간 간격은 초 단위로 지정합니다. MovementPerPartitionThrottleThreshold와 함께 사용되는 각 파티션에 대한 복제본 이동을 추적할 이전 간격의 길이를 나타냅니다. |
|PlacementSearchTimeout | time(초), 기본값: 0.5 | 시간 간격은 초 단위로 지정합니다. 서비스를 배치할 때 결과를 반환하기 전까지 이 기간에서 최대한 오랫동안 검색합니다. |
|UseMoveCostReports | bool, 기본값: false | 점수 매기기 함수의 비용 요소를 무시하도록 LB에 지시합니다. 보다 효율적인 분산 배치를 위해 잠재적으로 많은 수의 이동이 발생합니다. |
|PreventTransientOvercommit | bool, 기본값: false | PLB에서 시작된 이동으로 인해 확보될 리소스를 즉시 계산해야 하는지 결정합니다. 기본적으로 PLB는 일시적인 초과 커밋을 만들 수 있는 동일한 노드에서 인출과 인입을 시작할 수 있습니다. 이 매개 변수를 true로 설정하면 이러한 종류의 초과 커밋을 방지하고 주문형 조각 모음, 즉 placementWithMove를 사용할 수 없게 됩니다. |
|InBuildThrottlingEnabled | bool, 기본값: false | 빌드 내(in-build) 제한을 사용할 수 있는지 결정합니다. |
|InBuildThrottlingAssociatedMetric | string, 기본값: "" | 이 제한의 관련 메트릭 이름 |
|InBuildThrottlingGlobalMaxValue | int, 기본값: 0 |전역으로 허용되는 빌드 내 복제본의 최대 수 |
|SwapPrimaryThrottlingEnabled | bool, 기본값: false| 스왑-주(swap-primary) 제한을 사용할 수 있는지 결정합니다. |
|SwapPrimaryThrottlingAssociatedMetric | string, 기본값: ""| 이 제한의 관련 메트릭 이름 |
|SwapPrimaryThrottlingGlobalMaxValue | int, 기본값: 0 | 전역으로 허용되는 스왑-주 복제본의 최대 수 |
|PlacementConstraintPriority | int, 기본값: 0 | 배치 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|PreferredLocationConstraintPriority | int, 기본값: 2| 권장되는 위치 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 2: 최적화, 음수: 무시). |
|CapacityConstraintPriority | int, 기본값: 0 | 용량 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|AffinityConstraintPriority | int, 기본값: 0 | 선호도 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|FaultDomainConstraintPriority | int, 기본값: 0 | 장애 도메인 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|UpgradeDomainConstraintPriority | int, 기본값: 1| 업그레이드 도메인 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|ScaleoutCountConstraintPriority | int, 기본값: 0 | 확장 수 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|ApplicationCapacityConstraintPriority | int, 기본값: 0 | 용량 제약 조건의 우선 순위를 결정합니다(0: 하드, 1: 소프트, 음수: 무시) |
|MoveParentToFixAffinityViolation | bool, 기본값: false | 선호도 제약 조건을 수정하기 위해 부모 복제본을 이동할 수 있는지 여부를 결정하는 설정|
|MoveExistingReplicaForPlacement | bool, 기본값: true |배치 중에 기존 복제본을 이동할지 여부를 결정하는 설정 |
|UseSeparateSecondaryLoad | bool, 기본값: true | 다른 보조 로드를 사용할지 여부를 결정하는 설정 |
|PlaceChildWithoutParent | bool, 기본값: true | 부모 복제본이 없는 경우 자식 서비스 복제본을 배치할 수 있는지 여부를 결정하는 설정 |
|PartiallyPlaceServices | bool, 기본값: true | 제한된 적합한 노드가 지정되면 클러스터에 있는 모든 서비스 복제본을 "모두 배치하거나 전혀 배치하지 않을지"를 결정합니다.|
|InterruptBalancingForAllFailoverUnitUpdates | bool, 기본값: false | 모든 유형의 장애 조치(failover) 단위 업데이트에서 고속 또는 저속 분산 실행을 중단해야 하는지 여부를 결정합니다. "false"로 지정한 경우 FailoverUnit:가 만들어지거나 삭제되거나, 복제본이 누락되거나, 주 복제본 위치가 변경되거나, 복제본 수가 변경되면 분산 실행이 중단됩니다. 다른 경우, 즉 FailoverUnit:에 여분의 복제본이 있거나, 모든 복제본 플래그가 변경되거나, 파티션 버전만 변경되거나, 기타 모든 경우에는 분산 실행이 중단되지 않습니다. |

### <a name="section-name-security"></a>섹션 이름: Security
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ClusterProtectionLevel |None 또는 EncryptAndSign |비보안 클러스터의 경우 None(기본값), 보안 클러스터의 경우 EncryptAndSign |

### <a name="section-name-hosting"></a>섹션 이름: Hosting
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |시간(초), 기본값: 300 |ServiceType을 패브릭에 등록하는 데 허용되는 최대 시간 |
| ServiceTypeDisableFailureThreshold |정수, 기본값: 1 |해당 노드에서 서비스 유형을 사용하지 않도록 설정하고 다른 노드에 배치를 시도하도록 FM(FailoverManager)에 알리기까지 허용되는 실패 횟수에 대한 임계값입니다. |
| ActivationRetryBackoffInterval |시간(초), 기본값: 5 |모든 활성화 오류의 백오프 간격. 모든 연속 활성화 실패 시 시스템은 MaxActivationFailureCount까지 활성화를 다시 시도합니다. 모든 시도에서 다시 시도 간격은 연속 활성화 실패와 활성화 백오프 간격을 곱한 값입니다. |
| ActivationMaxRetryInterval |시간(초), 기본값: 300 |모든 연속 활성화 실패 시 시스템은 ActivationMaxFailureCount까지 활성화를 다시 시도합니다. ActivationMaxRetryInterval은 모든 활성화 실패 후 다시 시도하기 전에 대기 시간 간격을 지정합니다. |
| ActivationMaxFailureCount |정수, 기본값: 10 |시스템이 포기하기 전에 실패한 활성화를 다시 시도하는 횟수 |

### <a name="section-name-failovermanager"></a>섹션 이름: FailoverManager
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |시간(초), 기본값: 10 |FM이 새 부하 보고서를 확인하는 빈도를 결정합니다. |

### <a name="section-name-federation"></a>섹션 이름: Federation
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| LeaseDuration |시간(초), 기본값: 30 |노드 및 해당 이웃 노드 간에 임대가 지속되는 기간 |
| LeaseDurationAcrossFaultDomain |시간(초), 기본값: 30 |장애 도메인에서 노드 및 해당 이웃 노드 간에 임대가 지속되는 기간 |

### <a name="section-name-clustermanager"></a>섹션 이름: ClusterManager
| **매개 변수** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- |
| UpgradeStatusPollInterval |시간(초), 기본값: 60 |응용 프로그램 업그레이드 상태에 대한 풀링 간격. 이 값은 GetApplicationUpgradeProgress 호출에 대한 업데이트 속도를 결정합니다. |
| UpgradeHealthCheckInterval |시간(초), 기본값: 60 |모니터링되는 응용 프로그램 업그레이드 동안 시스템 상태 확인 빈도 |
| FabricUpgradeStatusPollInterval |시간(초), 기본값: 60 |패브릭 업그레이드 상태에 대한 풀링 간격. 이 값은 GetFabricUpgradeProgress 호출에 대한 업데이트 속도를 결정합니다. |
| FabricUpgradeHealthCheckInterval |시간(초), 기본값: 60 |모니터링되는 패브릭 업그레이드 동안 시스템 상태 확인 빈도 |
|InfrastructureTaskProcessingInterval | 시간(초), 기본값: 10 |시간 간격은 초 단위로 지정합니다. 인프라 태스크 처리 상태 시스템에서 사용하는 처리 간격입니다. |
|TargetReplicaSetSize |int, 기본값: 7 |ClusterManager의 TargetReplicaSetSize입니다. |
|MinReplicaSetSize |int, 기본값: 3 |ClusterManager의 MinReplicaSetSize입니다. |
|ReplicaRestartWaitDuration |time(초), 기본값: 60.0 * 30|시간 간격은 초 단위로 지정합니다. ClusterManager의 ReplicaRestartWaitDuration입니다. |
|QuorumLossWaitDuration |time(초), 기본값: MaxValue | 시간 간격은 초 단위로 지정합니다. ClusterManager의 QuorumLossWaitDuration입니다. |
|StandByReplicaKeepDuration | time(초), 기본값: 3600.0 * 2|시간 간격은 초 단위로 지정합니다. ClusterManager의 StandByReplicaKeepDuration입니다. |
|PlacementConstraints | string, 기본값: "" |ClusterManager의 PlacementConstraints입니다. |
|SkipRollbackUpdateDefaultService | bool, 기본값: false |CM에서 응용 프로그램 업그레이드 롤백 중에 업데이트된 기본 서비스 되돌리기를 건너뜁니다. |
|EnableDefaultServicesUpgrade | bool, 기본값: false |응용 프로그램 업그레이드 중에 기본 서비스 업그레이드를 사용하도록 설정합니다. 업그레이드 후에 기본 서비스 설명을 덮어씁니다. |
|InfrastructureTaskHealthCheckWaitDuration |time(초), 기본값: 0| 시간 간격은 초 단위로 지정합니다. 인프라 태스크를 사후 처리한 후 상태 검사를 시작하기 전까지 대기하는 시간입니다. |
|InfrastructureTaskHealthCheckStableDuration | time(초), 기본값: 0| 시간 간격은 초 단위로 지정합니다. 인프라 태스크의 사후 처리를 성공적으로 완료하기 전에 연속적으로 통과한 상태 검사를 관찰하는 데 걸리는 시간입니다. 실패한 상태 검사를 관찰하면 이 타이머가 다시 설정됩니다. |
|InfrastructureTaskHealthCheckRetryTimeout | 시간(초), 기본값: 60 |시간 간격은 초 단위로 지정합니다. 인프라 태스크를 사후 처리하는 동안 실패한 상태 검사를 다시 시도하는 데 걸리는 시간입니다. 통과한 상태 검사를 관찰하면 이 타이머가 다시 설정됩니다. |
|ImageBuilderTimeoutBuffer |time(초), 기본값: 3 |시간 간격은 초 단위로 지정합니다. 이미지 작성기 특정 시간 제한 오류를 클라이언트에 반환할 수 있도록 허용되는 시간입니다. 이 버퍼가 너무 작으면 클라이언트에서 서버보다 먼저 시간을 초과하고 일반적인 시간 제한 오류를 가져옵니다. |
|MinOperationTimeout | 시간(초), 기본값: 60 |시간 간격은 초 단위로 지정합니다. ClusterManager에서 내부적으로 작업을 처리하기 위한 최소 전역 시간 제한입니다. |
|MaxOperationTimeout |time(초), 기본값: MaxValue | 시간 간격은 초 단위로 지정합니다. ClusterManager에서 내부적으로 작업을 처리하기 위한 최대 전역 시간 제한입니다. |
|MaxTimeoutRetryBuffer | time(초), 기본값: 600 |시간 간격은 초 단위로 지정합니다. 시간 제한으로 인해 내부적으로 다시 시도하는 경우 최대 작업 시간 제한은 <Original Timeout> + <MaxTimeoutRetryBuffer>입니다. 추가 시간 제한은 MinOperationTimeout 증분 단위로 추가됩니다. |
|MaxCommunicationTimeout |time(초), 기본값: 600 |시간 간격은 초 단위로 지정합니다. ClusterManager와 다른 시스템 서비스(예: 이름 지정 서비스, 장애 조치 관리자 등) 간의 내부 통신에 대한 최대 시간 제한입니다. 각 클라이언트 작업의 시스템 구성 요소 간에 여러 통신이 있을 수 있으므로 이 시간 제한은 전역 MaxOperationTimeout보다 작아야 합니다. |
|MaxDataMigrationTimeout |time(초), 기본값: 600 |시간 간격은 초 단위로 지정합니다. 패브릭 업그레이드를 수행한 이후 데이터 마이그레이션 복구 작업에 대한 최대 시간 제한입니다. |
|MaxOperationRetryDelay |time(초), 기본값: 5| 시간 간격은 초 단위로 지정합니다. 오류가 발생하는 경우 내부 다시 시도에 대한 최대 지연 시간입니다. |
|ReplicaSetCheckTimeoutRollbackOverride |time(초), 기본값: 1200 | 시간 간격은 초 단위로 지정합니다. ReplicaSetCheckTimeout을 최대 DWORD 값으로 설정하면 롤백을 위해 이 구성의 값으로 재정의됩니다. 롤포워드에 사용되는 값은 절대로 재정의되지 않습니다. |
|ImageBuilderJobQueueThrottle |int, 기본값: 10 |응용 프로그램 요청의 이미지 작성기 프록시 작업 큐에 대한 스레드 수 제한입니다. |

## <a name="next-steps"></a>다음 단계
클러스터 관리에 대한 자세한 내용은 다음 문서를 읽어보세요.

[Azure 클러스터에서 인증서 추가, 롤오버, 제거 ](service-fabric-cluster-security-update-certs-azure.md) 


