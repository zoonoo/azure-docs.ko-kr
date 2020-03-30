---
title: 리소스 유형별로 작업 지원 이동
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 2250283136608161956716abadb63b9f706bf581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460418"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해야 할 특수 조건에 대한 정보도 제공합니다.

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [마이크로소프트.앱구성](#microsoftappconfiguration)
> - [마이크로소프트.앱플랫폼](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [마이크로소프트.권한 부여](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [마이크로소프트.Azure데이터](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [마이크로소프트.클래식네트워크](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [마이크로소프트.컴퓨트](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [마이크로소프트.사용자 지정 공급자](#microsoftcustomproviders)
> - [마이크로소프트.데이터박스](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [마이크로소프트.데이터레이크애널리틱스](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [마이크로소프트.데이터 보호](#microsoftdataprotection)
> - [마이크로소프트.데이터 쉐어](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [마이크로소프트.디바이스](#microsoftdevices)
> - [마이크로소프트.데브옵스](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [마이크로소프트.디지털 트윈스](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [마이크로소프트.엔터프라이즈 지식그래프](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [마이크로소프트.팔콘](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [마이크로소프트.헬스케어아피](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [마이크로소프트.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [마이크로소프트.마이크로 서비스4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [마이크로소프트 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [마이크로소프트.오브젝트스토어](#microsoftobjectstore)
> - [마이크로소프트.오퍼랙타인사이트](#microsoftoperationalinsights)
> - [마이크로소프트.오퍼레이션매니지먼트](#microsoftoperationsmanagement)
> - [마이크로소프트.피어링](#microsoftpeering)
> - [마이크로소프트.정책인사이트](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [마이크로소프트.프로젝트바빌론](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [마이크로소프트.공급자허브](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [마이크로소프트.검색](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [마이크로소프트.시큐리티인사이트](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [마이크로소프트 서비스](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [마이크로소프트.소프트웨어플랜](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [마이크로소프트 Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [마이크로소프트 지원](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [마이크로소프트 토큰](#microsofttoken)
> - [마이크로소프트.비주얼 스튜디오](#microsoftvisualstudio)
> - [마이크로소프트.VM웨어클라우드심플](#microsoftvmwarecloudsimple)
> - [마이크로소프트.VS온라인](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | 예 | 예 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | 예 | 예 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | suppressions | 예 | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 규칙 | yes | yes |
> | 경고 | 예 | 예 |
> | 경고요약 | 예 | 예 |
> | 스마트 검출기경고규칙 | yes | yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | yes | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서비스 | yes | yes |

> [!IMPORTANT]
> 소비 SKU로 설정된 API 관리 서비스는 이동할 수 없습니다.

## <a name="microsoftappconfiguration"></a>마이크로소프트.앱구성

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 저장소 | yes | yes |

## <a name="microsoftappplatform"></a>마이크로소프트.앱플랫폼

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 봄 | yes | yes |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 예 | 예 |
> | appidentities | 예 | 예 |
> | gateways | 예 | 예 |

> [!IMPORTANT]
> [앱 서비스 이동 지침을](./move-limitations/app-service-move-limitations.md)참조하십시오.

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Checkaccess | 예 | 예 |
> | 거부 할당 | 예 | 예 |
> | findorphanrole할당 | 예 | 예 |
> | locks | 예 | 예 |
> | 권한 | 예 | 예 |
> | policyassignments | 예 | 예 |
> | 정책 정의 | 예 | 예 |
> | 정책 집합 정의 | 예 | 예 |
> | 역할 할당 | 예 | 예 |
> | 역할 할당사용메트릭 | 예 | 예 |
> | 역할 정의 | 예 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | yes | yes |
> | 자동화 계정 / 구성 | yes | yes |
> | 자동화 계정 / 런북 | yes | yes |

> [!IMPORTANT]
> Runbook은 자동화 계정과 동일한 리소스 그룹에 있어야 합니다.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | yes | yes |

## <a name="microsoftazuredata"></a>마이크로소프트.Azure데이터

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 하이브리드 데이터 관리자 | 예 | 예 |
> | 사후 인스턴스 | 예 | 예 |
> | sqlinstances | 예 | 예 |
> | sqlserver 등록 | yes | yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | yes | yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | yes | yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | fileservers | 예 | 예 |
> | jobs | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 청구 기간 | 예 | 예 |
> | 청구 권한 | 예 | 예 |
> | 청구 롤할당 | 예 | 예 |
> | 청구롤롤 정의 | 예 | 예 |
> | 생성청구롤할당 | 예 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | 예 | 예 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | 예 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 예 | 예 |
> | 전문가 | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 예 | 예 |
> | blueprints | 예 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | yes | yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | yes | yes |

> [!IMPORTANT]
> Redis 인스턴스용 Azure 캐시가 가상 네트워크로 구성된 경우 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한 을](./move-limitations/networking-move-limitations.md)참조하십시오.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplication방화벽정책 | yes | yes |
> | 프로필 | yes | yes |
> | 프로파일 / 끝점 | yes | yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | yes | yes |

> [!IMPORTANT]
> [앱 서비스 이동 지침을](./move-limitations/app-service-move-limitations.md)참조하십시오.

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | yes | 예 |
> | virtualmachines | yes | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침을](./move-limitations/classic-model-move-limitations.md)참조하십시오. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 사용하여 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 예 | 예 |
> | reservedips | 예 | 예 |
> | virtualnetworks | 예 | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침을](./move-limitations/classic-model-move-limitations.md)참조하십시오. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 사용하여 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | yes | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침을](./move-limitations/classic-model-move-limitations.md)참조하십시오. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 사용하여 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | yes | yes |
> | 디스크 암호화 세트 | 예 | 예 |
> | disks | yes | yes |
> | galleries | 예 | 예 |
> | 갤러리 / 이미지 | 예 | 예 |
> | 갤러리 / 이미지 / 버전 | 예 | 예 |
> | 호스트 그룹 | 예 | 예 |
> | 호스트 그룹 / 호스트 | 예 | 예 |
> | images | yes | yes |
> | proximityplacementgroups | yes | yes |
> | restorepointcollections | 예 | 예 |
> | sharedvmimages | 예 | 예 |
> | 공유vmimages / 버전 | 예 | 예 |
> | 스냅샷 | yes | yes |
> | sshpublickeys | 예 | 예 |
> | virtualmachines | yes | yes |
> | 가상 머신/ 확장 | yes | yes |
> | virtualmachinescalesets | yes | yes |

> [!IMPORTANT]
> [가상 컴퓨터 이동 지침을](./move-limitations/virtual-machines-move-limitations.md)참조하십시오.

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 집계 비용 | 예 | 예 |
> | 균형 | 예 | 예 |
> | 예산 | 예 | 예 |
> | 요금 | 예 | 예 |
> | 코스트태그 | 예 | 예 |
> | credits | 예 | 예 |
> | events | 예 | 예 |
> | 예측 | 예 | 예 |
> | lots | 예 | 예 |
> | 시장 | 예 | 예 |
> | 작업 결과 | 예 | 예 |
> | 작업 상태 | 예 | 예 |
> | 가격표 | 예 | 예 |
> | products | 예 | 예 |
> | 예약 세부 정보 | 예 | 예 |
> | 예약 권장 사항 | 예 | 예 |
> | 예약 요약 | 예 | 예 |
> | 예약 거래 | 예 | 예 |
> | tags | 예 | 예 |
> | tenants | 예 | 예 |
> | terms | 예 | 예 |
> | 사용 정보 | 예 | 예 |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 예 | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 예 | 예 |
> | 서비스 연결 링크 | 예 | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | yes | yes |
> | 레지스트리 / 빌드 작업 | yes | yes |
> | 레지스트리 / 복제 | yes | yes |
> | 레지스트리 / 작업 실행 | yes | yes |
> | 레지스트리 / 작업 | yes | yes |
> | 레지스트리 / 웹 후크 | yes | yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 예 | 예 |
> | managedclusters | 예 | 예 |
> | openshiftmanagedclusters | 예 | 예 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 예 | 예 |
> | 예산 | 예 | 예 |
> | 커넥터 | yes | yes |
> | 차원 | 예 | 예 |
> | 내보내기 | 예 | 예 |
> | 외부 구독 | 예 | 예 |
> | forecast | 예 | 예 |
> | Query | 예 | 예 |
> | 보고서 구성 | 예 | 예 |
> | reports | 예 | 예 |
> | 쇼백 규칙 | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 예 | 예 |

## <a name="microsoftcustomproviders"></a>마이크로소프트.사용자 지정 공급자

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 연결 | 예 | 예 |
> | 리소스 공급자 | yes | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 예 | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | yes | yes |
> | 데이터 카탈로그 | 예 | 예 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 예 | 예 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 예 | 예 |
> | 플랜 | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | yes | yes |
> | factories | yes | yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 예 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |
> | 서비스 / 프로젝트 | 예 | 예 |
> | slots | 예 | 예 |

## <a name="microsoftdataprotection"></a>마이크로소프트.데이터 보호

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 백업 볼트 | 예 | 예 |

## <a name="microsoftdatashare"></a>마이크로소프트.데이터 쉐어

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | yes | yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | yes | yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | 예 | 예 |
> | servers | yes | yes |
> | 서버v2 | yes | yes |
> | 단일 서버 | yes | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | yes | yes |
> | rollouts | yes | yes |
> | servicetopologies | yes | yes |
> | 서비스토폴로지/ 서비스 | yes | yes |
> | 서비스토폴로지 / 서비스 / 서비스 단위 | yes | yes |
> | 단계 | yes | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | 예 | 예 |
> | 탄성 풀 / iothubtenants | 예 | 예 |
> | iothubs | yes | yes |
> | provisioningservices | yes | yes |

## <a name="microsoftdevops"></a>마이크로소프트.데브옵스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 파이프라인 | yes | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | yes | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 예 | 예 |
> | labs | yes | 예 |
> | 실험실 / 환경 | yes | yes |
> | 실험실 / 서비스 러너 | yes | yes |
> | 랩 / 가상 머신 | yes | 예 |
> | schedules | yes | yes |

## <a name="microsoftdigitaltwins"></a>마이크로소프트.디지털 트윈스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 디지털트윈스인스턴스 | 예 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | yes | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | yes | yes |

## <a name="microsoftenterpriseknowledgegraph"></a>마이크로소프트.엔터프라이즈 지식그래프

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | yes | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | yes | yes |
> | eventSubscriptions | 아니오 - 독립적으로 이동할 수는 없지만 구독된 리소스로 자동으로 이동할 수 없습니다. | 아니오 - 독립적으로 이동할 수는 없지만 구독된 리소스로 자동으로 이동할 수 없습니다. |
> | 이벤트 구독 | 아니오 - 독립적으로 이동할 수는 없지만 구독된 리소스로 자동으로 이동할 수 없습니다. | 아니오 - 독립적으로 이동할 수는 없지만 구독된 리소스로 자동으로 이동할 수 없습니다. |
> | 확장 주제 | 예 | 예 |
> | 파트너 이름 공백 | yes | yes |
> | 파트너 주제 | yes | yes |
> | 시스템 주제 | yes | yes |
> | topics | yes | yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |
> | 네임스페이스 | yes | yes |

## <a name="microsoftfalcon"></a>마이크로소프트.팔콘

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 게스트 구성 할당 | 예 | 예 |
> | software | 예 | 예 |
> | 소프트웨어 업데이트 프로필 | 예 | 예 |
> | 소프트웨어 업데이트 | 예 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 예 | 예 |
> | 삽 모니터 | yes | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>마이크로소프트.헬스케어아피

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | yes | yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 기계 | yes | yes |
> | 기계 / 확장 | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | yes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | yes | yes |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | yes | yes |
> | activitylogalerts | 예 | 예 |
> | alertrules | yes | yes |
> | autoscalesettings | yes | yes |
> | baseline | 예 | 예 |
> | calculatebaseline | 예 | 예 |
> | components | yes | yes |
> | 진단 설정 | 예 | 예 |
> | 진단 설정범주 | 예 | 예 |
> | eventtypes | 예 | 예 |
> | 확장 진단 설정 | 예 | 예 |
> | 로그 정의 | 예 | 예 |
> | 로그 | 예 | 예 |
> | metricalerts | 예 | 예 |
> | 메트릭 기준선 | 예 | 예 |
> | 메트릭 정의 | 예 | 예 |
> | 메트릭 이름 공백 | 예 | 예 |
> | 메트릭 | 예 | 예 |
> | 마이워크북 | 예 | 예 |
> | 개인 링크 스코프 | yes | yes |
> | scheduledqueryrules | yes | yes |
> | 토폴로지 | 예 | 예 |
> | 트랜잭션 | 예 | 예 |
> | vminsightson@osen.co.화. | 예 | 예 |
> | webtests | yes | yes |
> | workbooks | yes | yes |
> | 통합 문서 템플릿 | yes | yes |

> [!IMPORTANT]
> 새 구독으로 이동해도 구독 할당량이 초과되지 않는지 [확인합니다.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | yes | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | yes | yes |
> | graph | yes | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | yes | yes |

> [!IMPORTANT]
> 디스크 암호화에 사용되는 키 볼트는 동일한 구독또는 구독 간에 리소스 그룹으로 이동할 수 없습니다.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 예 | 예 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 예 | 예 |
> | integrationaccounts | yes | yes |
> | integrationserviceenvironments | yes | 예 |
> | 통합 서비스 환경 / 관리 | yes | 예 |
> | isolatedenvironments | 예 | 예 |
> | workflows | yes | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | yes | yes |
> | webservices | yes | 예 |
> | workspaces | yes | yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 예 | 예 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | 계정 / 작업 영역 | 예 | 예 |
> | 계정 / 작업 영역 / 프로젝트 | 예 | 예 |
> | teamaccounts | 예 | 예 |
> | 팀 계정 / 작업 영역 | 예 | 예 |
> | 팀 계정 / 작업 영역 / 프로젝트 | 예 | 예 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 예 | 예 |
> | 작업 영역/ 계산 | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 예 | 예 |
> | userassignedidentities | 예 | 예 |

## <a name="microsoftmanagedservices"></a>마이크로소프트.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 등록 할당 | 예 | 예 |
> | 등록 정의 | 예 | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 예 | 예 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | yes | yes |
> | 미디어 서비스 / 라이브 이벤트 | yes | yes |
> | 미디어 서비스 / 스트리밍 엔드포인트 | yes | yes |

## <a name="microsoftmicroservices4spring"></a>마이크로소프트.마이크로 서비스4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 앱 클러스터 | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | yes | yes |
> | migrateprojects | yes | yes |
> | projects | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 예 | 예 |
> | 넷앱 계정 / 백업 정책 | 예 | 예 |
> | 넷앱계정 / 용량 풀 | 예 | 예 |
> | 넷앱계정 / 용량 풀 / 볼륨 | 예 | 예 |
> | 넷앱계정 / 용량 풀 / 볼륨 / 마운트 타겟 | 예 | 예 |
> | 넷앱계정 / 용량 풀 / 볼륨 / 스냅 샷 | 예 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 예 | 예 |
> | 응용 프로그램 게이트웨이 웹 응용 프로그램 방화벽 정책 | 예 | 예 |
> | applicationsecuritygroups | yes | yes |
> | azurefirewalls | yes | yes |
> | bastionhosts | 예 | 예 |
> | connections | yes | yes |
> | ddoscustompolicies | yes | yes |
> | ddosprotectionplans | 예 | 예 |
> | dnszones | yes | yes |
> | expressroutecircuits | 예 | 예 |
> | expressroutegateways | 예 | 예 |
> | 방화벽 정책 | yes | yes |
> | frontdoors | 예 | 예 |
> | frontdoorwebapplicationfirewallpolicies | 예 | 예 |
> | ip 그룹 | yes | yes |
> | loadbalancers | 예 - 기본 SKU<br>아니오 - 표준 SKU | 예 - 기본 SKU<br>아니오 - 표준 SKU |
> | localnetworkgateways | yes | yes |
> | natgateways | yes | yes |
> | 네트워크 실험 프로필 | yes | yes |
> | networkintentpolicies | yes | yes |
> | networkinterfaces | yes | yes |
> | networkprofiles | 예 | 예 |
> | networksecuritygroups | yes | yes |
> | networkwatchers | yes | 예 |
> | 네트워크 감시자 / 연결 모니터 | yes | 예 |
> | 네트워크 감시자 / 플로우로그 | yes | 예 |
> | 네트워크 감시자 / 핑메시 | yes | 예 |
> | p2svpngateways | 예 | 예 |
> | 프라이빗 스존 | yes | yes |
> | 프라이빗스존 / 가상네트워크링크 | yes | yes |
> | 프라이빗 엔드포인트다이렉트맵 | 예 | 예 |
> | 프라이빗 엔드포인트 | yes | yes |
> | privatelinkservices | 예 | 예 |
> | publicipaddresses | 예 - 기본 SKU<br>아니오 - 표준 SKU | 예 - 기본 SKU<br>아니오 - 표준 SKU |
> | publicipprefixes | yes | yes |
> | routefilters | 예 | 예 |
> | routetables | yes | yes |
> | serviceendpointpolicies | yes | yes |
> | trafficmanagerprofiles | yes | yes |
> | virtualhubs | 예 | 예 |
> | virtualnetworkgateways | yes | yes |
> | virtualnetworks | yes | yes |
> | virtualnetworktaps | 예 | 예 |
> | 가상 라우터 | yes | yes |
> | virtualwans | 예 | 예 |
> | vpn게이트웨이(가상 WAN) | 예 | 예 |
> | vpn서버 구성 | 예 | 예 |
> | VPN 사이트(가상 WAN) | 예 | 예 |
> | webapplicationfirewallpolicies | yes | yes |

> [!IMPORTANT]
> [네트워킹 이동 지침을](./move-limitations/networking-move-limitations.md)참조하십시오.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |
> | 네임스페이스 / 알림 허브 | yes | yes |

## <a name="microsoftobjectstore"></a>마이크로소프트.오브젝트스토어

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 오스네임 스페이스 | yes | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 스토리지인사이트컨피그 | 예 | 예 |
> | workspaces | yes | yes |

> [!IMPORTANT]
> 새 구독으로 이동해도 구독 할당량이 초과되지 않는지 [확인합니다.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 예 | 예 |
> | managementconfigurations | yes | yes |
> | solutions | yes | yes |
> | 뷰 | yes | yes |

## <a name="microsoftpeering"></a>마이크로소프트.피어링

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 피어링 | yes | yes |
> | 피어링 서비스 | 예 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 정책 이벤트 | 예 | 예 |
> | 정책 상태 | 예 | 예 |
> | 정책 추적 리소스 | 예 | 예 |
> | remediations | 예 | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | yes | yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | 예 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | yes | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | yes | yes |

## <a name="microsoftprojectbabylon"></a>마이크로소프트.프로젝트바빌론

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftproviderhub"></a>마이크로소프트.공급자허브

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 백업 보호 항목 | 예 | 예 |
> | 복제 자격 검사 결과 | 예 | 예 |
> | vaults | yes | yes |

> [!IMPORTANT]
> [복구 서비스 이동 지침을](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)참조하십시오.

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 쿼리 | yes | yes |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 가용성 상태 | 예 | 예 |
> | 자녀 가용성 상태 | 예 | 예 |
> | 아동 자원 | 예 | 예 |
> | events | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 배포 스크립트 | 예 | 예 |
> | 링크 | 예 | 예 |
> | tags | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | yes | yes |

> [!IMPORTANT]
> 한 번의 작업에서는 다른 지역에서 여러 검색 리소스를 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 적응형 네트워크 경화 | 예 | 예 |
> | 고급위협 보호 설정 | 예 | 예 |
> | 평가 메타데이터 | 예 | 예 |
> | 평가 | 예 | 예 |
> | 자동화 | yes | yes |
> | 규정 준수 결과 | 예 | 예 |
> | 준수 | 예 | 예 |
> | 데이터 수집 에이전트 | 예 | 예 |
> | 장치 보안 그룹 | 예 | 예 |
> | 정보 보호 정책 | 예 | 예 |
> | iot보안솔루션 | yes | yes |
> | 서버 취약성 평가 | 예 | 예 |

## <a name="microsoftsecurityinsights"></a>마이크로소프트.시큐리티인사이트

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 예 | 예 |
> | alertrules | 예 | 예 |
> | 경고 규칙 템플릿 | 예 | 예 |
> | 책갈피 | 예 | 예 |
> | cases | 예 | 예 |
> | 데이터 커넥터 | 예 | 예 |
> | 데이터 커넥터 검사 요구 사항 | 예 | 예 |
> | 엔터티 | 예 | 예 |
> | 엔터티 쿼리 | 예 | 예 |
> | 사건 | 예 | 예 |
> | 사무실 동의 | 예 | 예 |
> | 설정 | 예 | 예 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 예 | 예 |
> | nodes | 예 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | yes | yes |
> | 클러스터 / 응용 프로그램 | 예 | 예 |
> | containergroups | 예 | 예 |
> | containergroupsets | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | managedclusters | 예 | 예 |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | yes |
> | gateways | yes | yes |
> | networks | yes | yes |
> | secrets | yes | yes |
> | volumes | yes | yes |

## <a name="microsoftservices"></a>마이크로소프트 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | yes | yes |

## <a name="microsoftsoftwareplan"></a>마이크로소프트.소프트웨어플랜

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 하이브리드 유스 혜택 | 예 | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitrequests | 예 | 예 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 인스턴스 풀 | 예 | 예 |
> | managedinstances | 예 | 예 |
> | 관리되는 인스턴스 / 데이터베이스 | 예 | 예 |
> | servers | yes | yes |
> | 서버 / 데이터베이스 | yes | yes |
> | 서버 / 탄성 풀 | yes | yes |
> | 서버 / 작업 계정 | yes | yes |
> | 서버 / 작업 에이전트 | yes | yes |
> | virtualclusters | yes | yes |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | yes | yes |
> | sqlvirtualmachines | yes | yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | yes | yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | yes | yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | yes | yes |

> [!IMPORTANT]
> 스트림 애널리틱스 작업은 실행 중인 상태에서 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | 환경 / 이벤트 소스 | 예 | 예 |
> | 인스턴스 | 예 | 예 |
> | 인스턴스/ 환경 | 예 | 예 |
> | 인스턴스 / 환경 / 이벤트 소스 | 예 | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구독 만들기 | 예 | 예 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supporttickets | 예 | 예 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 예 | 예 |
> | 리소스 | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | yes | yes |
> | 환경 / 이벤트 소스 | yes | yes |
> | 환경 / 참조 데이터 집합 | yes | yes |

## <a name="microsofttoken"></a>마이크로소프트 토큰

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | yes | yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 예 | 예 |
> | 계정 / 확장 | yes | yes |
> | 계정 / 프로젝트 | yes | yes |

> [!IMPORTANT]
> Azure DevOps에 대한 구독을 변경하려면 [청구에 사용되는 Azure 구독 변경을](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)참조하십시오.

## <a name="microsoftvmwarecloudsimple"></a>마이크로소프트.VM웨어클라우드심플

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 전용 클라우드 노드 | 예 | 예 |
> | 전용 클라우드 서비스 | 예 | 예 |
> | virtualmachines | 예 | 예 |

## <a name="microsoftvsonline"></a>마이크로소프트.VS온라인

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |
> | 플랜 | yes | yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | 예 | yes |
> | connectiongateways | yes | yes |
> | connections | yes | yes |
> | customapis | yes | yes |
> | hostingenvironments | 예 | 예 |
> | serverfarms | yes | yes |
> | sites | yes | yes |
> | 사이트 / 프리미어 애드온 | yes | yes |
> | 사이트 / 슬롯 | yes | yes |
> | 정적 사이트 | 예 | 예 |

> [!IMPORTANT]
> [앱 서비스 이동 지침을](./move-limitations/app-service-move-limitations.md)참조하십시오.

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 예 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 예 | 예 |
> | 모니터 인스턴스 | 예 | 예 |
> | monitors | 예 | 예 |
> | 알림 설정 | 예 | 예 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
