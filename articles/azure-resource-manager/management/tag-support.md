---
title: 리소스에 대한 지원 태그
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b8b1c0b738bb8b94ee53433141f1ae3dbbb3f942
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982330"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](tag-resources.md)를 지원하는지 여부를 설명합니다. **지원 태그라는** 레이블이 붙은 열은 리소스 형식에 태그에 대한 속성이 있는지 여부를 나타냅니다. 비용 보고서에 **태그로** 레이블이 지정된 열은 해당 리소스 유형이 태그를 비용 보고서에 전달하는지 여부를 나타냅니다. [비용 관리 비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) 및 Azure 청구 [송장 및 일일 사용량 데이터에서](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)태그별로 비용을 볼 수 있습니다.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) 파일을 다운로드합니다.

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [마이크로소프트.앱구성](#microsoftappconfiguration)
> - [마이크로소프트.앱플랫폼](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [마이크로소프트.권한 부여](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [마이크로소프트.아즈콘피그](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [마이크로소프트.Azure데이터](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [마이크로소프트.블록체인토큰](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [마이크로소프트.클래식네트워크](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [마이크로소프트.컴퓨트](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [마이크로소프트.고객 잠금 상자](#microsoftcustomerlockbox)
> - [마이크로소프트.사용자 지정 공급자](#microsoftcustomproviders)
> - [마이크로소프트.데이터박스](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [마이크로소프트.데이터레이크애널리틱스](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [마이크로소프트.데이터 쉐어](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [마이크로소프트.데스크톱 가상화](#microsoftdesktopvirtualization)
> - [마이크로소프트.디바이스](#microsoftdevices)
> - [마이크로소프트.데브옵스](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [마이크로소프트.엔터프라이즈 지식그래프](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [마이크로소프트.팔콘](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [마이크로소프트.헬스케어아피](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [마이크로소프트.히드라](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [마이크로소프트.쿠베네테스](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [마이크로소프트.유지 보수](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [마이크로소프트.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [마이크로소프트.마이크로 서비스4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [마이크로소프트.혼합현실](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [마이크로소프트 네트워크](#microsoftnetwork)
> - [마이크로소프트.노트북](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [마이크로소프트.오브젝트스토어](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [마이크로소프트.오퍼랙타인사이트](#microsoftoperationalinsights)
> - [마이크로소프트.오퍼레이션매니지먼트](#microsoftoperationsmanagement)
> - [마이크로소프트.피어링](#microsoftpeering)
> - [마이크로소프트.정책인사이트](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [마이크로소프트.프로젝트바빌론](#microsoftprojectbabylon)
> - [마이크로소프트.퀀텀](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [마이크로소프트.리모트 앱](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [마이크로소프트.검색](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [마이크로소프트.시큐리티인사이트](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [마이크로소프트 서비스](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [마이크로소프트.소프트웨어플랜](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [마이크로소프트.스풀서비스](#microsoftspoolservice)
> - [마이크로소프트 SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [마이크로소프트.스토리지캐시](#microsoftstoragecache)
> - [마이크로소프트.스토리지복제](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [마이크로소프트.VM웨어클라우드심플](#microsoftvmwarecloudsimple)
> - [마이크로소프트.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 예 | 예 |
> | 도메인 서비스 / 오컨테이너 | 예 | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 예 | 예 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 예 | 예 |
> | addsservices | 예 | 예 |
> | agents | 예 | 예 |
> | anonymousapiusers | 예 | 예 |
> | 구성 | 예 | 예 |
> | 로그 | 예 | 예 |
> | reports | 예 | 예 |
> | servicehealthmetrics | 예 | 예 |
> | services | 예 | 예 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 | 예 | 예 |
> | generateRecommendations | 예 | 예 |
> | metadata | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | suppressions | 예 | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | 예 | 예 |
> | 경고 | 예 | 예 |
> | alertsList | 예 | 예 |
> | 알림메타데이터 | 예 | 예 |
> | alertsSummary | 예 | 예 |
> | alertsSummaryList | 예 | 예 |
> | smartDetectorAlertRules | 예 | 예 |
> | smartGroups | 예 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 예 | 예 |
> | 서비스 | 예 | 예 |
> | validateServiceName | 예 | 예 |

## <a name="microsoftappconfiguration"></a>마이크로소프트.앱구성

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 스토어 | 예 | 예 |
> | 구성스토어 / 이벤트그리드필터 | 예 | 예 |

## <a name="microsoftappplatform"></a>마이크로소프트.앱플랫폼

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | Spring | 예 | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 예 | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 예 | 예 |
> | 데이터알리아스 | 예 | 예 |
> | denyAssignments | 예 | 예 |
> | elevateAccess | 예 | 예 |
> | 찾기Orphan역할할당 | 예 | 예 |
> | locks | 예 | 예 |
> | 권한 | 예 | 예 |
> | policyAssignments | 예 | 예 |
> | policyDefinitions | 예 | 예 |
> | policySetDefinitions | 예 | 예 |
> | providerOperations | 예 | 예 |
> | roleAssignments | 예 | 예 |
> | 역할할당사용메트릭 | 예 | 예 |
> | roleDefinitions | 예 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 예 | 예 |
> | 자동화계정 / 구성 | 예 | 예 |
> | 자동화계정 / 채용 정보 | 예 | 예 |
> | 자동화계정 / 사설엔드포인트커넥션프록시 | 예 | 예 |
> | 자동화계정 / 프라이빗엔드포인트커넥션 | 예 | 예 |
> | 자동화계정 / 개인링크자원 | 예 | 예 |
> | 자동화계정 / 런북 | 예 | 예 |
> | 자동화계정 / 소프트웨어업데이트구성 | 예 | 예 |
> | 자동화계정 / 웹후크 | 예 | 예 |

## <a name="microsoftazconfig"></a>마이크로소프트.아즈콘피그

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 스토어 | 예 | 예 |
> | 구성스토어 / 이벤트그리드필터 | 예 | 예 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 예 |
> | 환경 / 계정 | 예 | 예 |
> | 환경 / 계정 / 네임 스페이스 | 예 | 예 |
> | 환경 / 계정 / 네임 스페이스 / 구성 | 예 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 예 | 예 |
> | b2ctenants | 예 | 예 |

## <a name="microsoftazuredata"></a>마이크로소프트.Azure데이터

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 하이브리드 데이터 관리자 | 예 | 예 |
> | 포스트그레스 | 예 | 예 |
> | sqlInstances | 예 | 예 |
> | sqlServer등록 | 예 | 예 |
> | sqlServer등록 / sqlServers | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 클라우드 매니페스트 파일 | 예 | 예 |
> | registrations | 예 | 예 |
> | 등록 / 고객 구독 | 예 | 예 |
> | 등록 / 제품 | 예 | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 예 | 예 |
> | 청구계정/ 계약 | 예 | 예 |
> | 청구계정 / 청구권한 | 예 | 예 |
> | 청구계정 / 청구프로필 | 예 | 예 |
> | 청구계정 / 청구프로필 / 청구권한 | 예 | 예 |
> | 청구계정 / 청구프로필 / 청구역할할당 | 예 | 예 |
> | 청구계정 / 청구프로필 / 청구롤정의 | 예 | 예 |
> | 청구계정 / 청구프로필 / 청구 구독 | 예 | 예 |
> | 청구계정 / 청구 프로필 / 생성청구역할할당 | 예 | 예 |
> | 청구계정 / 청구프로필 / 고객 | 예 | 예 |
> | 청구계정 / 청구프로필 / 지침 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스/ 가격표 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스 / 거래 | 예 | 예 |
> | 청구계정 / 청구프로필 / 청구서섹션 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션 / 청구권한 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션 / 청구역할할당 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션 / 청구역할정의 | 예 | 예 |
> | 청구계정 / 청구계정 / 청구서섹션 / 청구구독 | 예 | 예 |
> | 청구계정 / 청구 프로필 / 인보이스섹션 / 생성청구역할할당 | 예 | 예 |
> | 청구계정 / 청구 프로필 / 인보이스섹션 / 시작전송 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션 /제품 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션 / 제품 / 이전 | 예 | 예 |
> | 청구계정 / 청구 프로필 / 인보이스섹션 / 제품 / 업데이트자동갱신 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션/거래 | 예 | 예 |
> | 청구계정 / 청구프로필 / 인보이스섹션/이체 | 예 | 예 |
> | 청구 계정 / 청구 프로필 / 패치운영 | 예 | 예 |
> | 청구계정 / 청구프로필 / 결제방법 | 예 | 예 |
> | 청구계정 / 청구프로필 / 정책 | 예 | 예 |
> | 청구계정 / 청구프로필 / 가격표 | 예 | 예 |
> | 청구계정 / 청구프로필 / 프라이스시트다운로드운영 | 예 | 예 |
> | 청구계정 / 청구프로필 / 제품 | 예 | 예 |
> | 청구계정 / 청구계정 / 거래프로필/ 거래 | 예 | 예 |
> | 청구계정 / 청구역할할당 | 예 | 예 |
> | 청구계정 / 청구역할정의 | 예 | 예 |
> | 청구계정 / 청구구독 | 예 | 예 |
> | 청구계정 / 청구구독 / 인보이스 | 예 | 예 |
> | 청구 계정 / 만들기청구역할할당 | 예 | 예 |
> | 청구 계정 / createInvoiceSectionOperations | 예 | 예 |
> | 청구계정 / 고객 | 예 | 예 |
> | 청구계정 / 고객 / 청구권한 | 예 | 예 |
> | 청구계정 / 고객 / 청구 구독 | 예 | 예 |
> | 청구계정 / 고객 / 시작 전송 | 예 | 예 |
> | 청구계정 / 고객 / 정책 | 예 | 예 |
> | 청구계정 / 고객 / 제품 | 예 | 예 |
> | 청구계정 / 고객 / 거래 | 예 | 예 |
> | 청구계정 / 고객 / 이체 | 예 | 예 |
> | 청구계정/부서 | 예 | 예 |
> | 청구계정계정/ 등록계정 | 예 | 예 |
> | 청구계정 / 인보이스 | 예 | 예 |
> | 청구계정 / 송장섹션 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 청구구독이동운영 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 청구구독 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 청구구독/ 이전 | 예 | 예 |
> | 청구계정 / 인보이스섹션/ 상승 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 시작전송 | 예 | 예 |
> | 청구계정 / 송장섹션 / 패치운영 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 제품이동운영 | 예 | 예 |
> | 청구계정 / 인보이스섹션 /제품 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 제품/ 이체 | 예 | 예 |
> | 청구계정 / 인보이스섹션 / 제품/ 업데이트자동갱신 | 예 | 예 |
> | 청구계정 / 인보이스섹션/거래 | 예 | 예 |
> | 청구계정 / 인보이스섹션/이체 | 예 | 예 |
> | 청구계정 / 라인오브크레디트 | 예 | 예 |
> | 청구계정 / 패치운영 | 예 | 예 |
> | 청구계정 / 결제 방법 | 예 | 예 |
> | 청구계정 / 제품 | 예 | 예 |
> | 청구계정/거래 | 예 | 예 |
> | billingPeriods | 예 | 예 |
> | 청구권한 | 예 | 예 |
> | billingProperty | 예 | 예 |
> | 청구역할할당 | 예 | 예 |
> | 청구롤정의 | 예 | 예 |
> | 만들기빌링롤할당 | 예 | 예 |
> | departments | 예 | 예 |
> | enrollmentAccounts | 예 | 예 |
> | invoices | 예 | 예 |
> | transfers | 예 | 예 |
> | 전송 / 수락 전송 | 예 | 예 |
> | 전송 / 거절전송 | 예 | 예 |
> | 전송 / 작업상태 | 예 | 예 |
> | 전송 / 유효성 검사 전송 | 예 | 예 |
> | 유효성 검사주소 | 예 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | mapApis | 예 | 예 |
> | updateCommunicationPreference | 예 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 블록체인 회원 | 예 | 예 |
> | 코다 회원 | 예 | 예 |
> | 전문가 | 예 | 예 |

## <a name="microsoftblockchaintokens"></a>마이크로소프트.블록체인토큰

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 토큰 서비스 | 예 | 예 |
> | 토큰 서비스 / 블록체인 네트워크 | 예 | 예 |
> | 토큰 서비스 / 그룹 | 예 | 예 |
> | 토큰 서비스 / 그룹 / 계정 | 예 | 예 |
> | 토큰 서비스 / 토큰 템플릿 | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 예 | 예 |
> | 청사진할당 / 할당운영 | 예 | 예 |
> | 청사진할당/운영 | 예 | 예 |
> | blueprints | 예 | 예 |
> | 청사진 / 아티팩트 | 예 | 예 |
> | 청사진 / 버전 | 예 | 예 |
> | 청사진 / 버전 / 아티팩트 | 예 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | 예 | 예 |
> | 봇 서비스 / 채널 | 예 | 예 |
> | 봇 서비스 / 연결 | 예 | 예 |
> | 언어 | 예 | 예 |
> | 템플릿 | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | 예 | 예 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 예 | 예 |
> | 자동 할당량 증가 | 예 | 예 |
> | 계산교환 | 예 | 예 |
> | calculatePrice | 예 | 예 |
> | 계산구매가격 | 예 | 예 |
> | catalogs | 예 | 예 |
> | commercialReservationOrders | 예 | 예 |
> | 교환 | 예 | 예 |
> | 플레이스구매 주문 | 예 | 예 |
> | reservationOrders | 예 | 예 |
> | 예약 주문 / 계산환불 | 예 | 예 |
> | 예약 주문 / 병합 | 예 | 예 |
> | 예약 주문/예약 | 예 | 예 |
> | 예약 주문/예약/개정 | 예 | 예 |
> | 예약 주문 /반품 | 예 | 예 |
> | 예약 주문 / 분할 | 예 | 예 |
> | 예약 주문 / 스왑 | 예 | 예 |
> | reservations | 예 | 예 |
> | 리소스 공급자 | 예 | 예 |
> | 리소스 | 예 | 예 |
> | validateReservationOrder | 예 | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWeb응용프로그램방화벽관리규칙세트 | 예 | 예 |
> | CdnWeb응용프로그램방화벽정책 | 예 | 예 |
> | edgenodes | 예 | 예 |
> | 프로필 | 예 | 예 |
> | 프로파일 / 끝점 | 예 | 예 |
> | 프로필 / 끝점 / 사용자 정의 도메인 | 예 | 예 |
> | 프로파일 / 끝점 / 원점 그룹 | 예 | 예 |
> | 프로파일 / 끝점 / 원점 | 예 | 예 |
> | validateProbe | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 예 | 예 |
> | 인증서주문/증명서 | 예 | 예 |
> | validateCertificateRegistrationInformation | 예 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | domainNames | 예 | 예 |
> | 도메인 이름 / 기능 | 예 | 예 |
> | 도메인 이름 / 내부로드 밸러저 | 예 | 예 |
> | 도메인 이름 / 서비스인증서 | 예 | 예 |
> | 도메인 이름 / 슬롯 | 예 | 예 |
> | 도메인 이름 / 슬롯 / 역할 | 예 | 예 |
> | 도메인 이름 / 슬롯 / 역할 / 메트릭정의 | 예 | 예 |
> | 도메인 이름 / 슬롯 / 역할 / 메트릭 | 예 | 예 |
> | moveSubscriptionResources | 예 | 예 |
> | operatingSystemFamilies | 예 | 예 |
> | operatingSystems | 예 | 예 |
> | quotas | 예 | 예 |
> | resourceTypes | 예 | 예 |
> | validateSubscriptionMoveAvailability | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | 가상 머신 / 진단설정 | 예 | 예 |
> | 가상 머신 / 메트릭정의 | 예 | 예 |
> | 가상 머신 / 메트릭 | 예 | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 예 | 예 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | 익스프레스루트크로스커넥션 / 피어링 | 예 | 예 |
> | gatewaySupportedDevices | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | quotas | 예 | 예 |
> | reservedIps | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | 가상 네트워크 / 원격가상 네트워크피어링프로시 | 예 | 예 |
> | 가상 네트워크 / 가상 네트워크 피어링 | 예 | 예 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | disks | 예 | 예 |
> | images | 예 | 예 |
> | osImages | 예 | 예 |
> | osPlatformImages | 예 | 예 |
> | publicImages | 예 | 예 |
> | quotas | 예 | 예 |
> | storageAccounts | 예 | 예 |
> | 저장소계정 / blob서비스 | 예 | 예 |
> | 저장소계정 / 파일서비스 | 예 | 예 |
> | 저장소계정 / 메트릭정의 | 예 | 예 |
> | 저장소계정/ 메트릭 | 예 | 예 |
> | 저장소계정/ 큐서비스 | 예 | 예 |
> | 저장소계정/ 서비스 | 예 | 예 |
> | 저장소계정 / 서비스 / 진단설정 | 예 | 예 |
> | 저장소계정 / 서비스 / 메트릭정의 | 예 | 예 |
> | 저장소계정 / 서비스 / 메트릭 | 예 | 예 |
> | 저장소계정 / 테이블서비스 | 예 | 예 |
> | 스토리지계정 / vmImages | 예 | 예 |
> | vmImages | 예 | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 예 | 예 |
> | UsageAggregates | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 예 | 예 |
> | 디스크 암호화 세트 | 예 | 예 |
> | disks | 예 | 예 |
> | galleries | 예 | 예 |
> | 갤러리 / 응용 프로그램 | 예 | 예 |
> | 갤러리 / 응용 프로그램 / 버전 | 예 | 예 |
> | 갤러리 / 이미지 | 예 | 예 |
> | 갤러리 / 이미지 / 버전 | 예 | 예 |
> | 호스트 그룹 | 예 | 예 |
> | 호스트 그룹 / 호스트 | 예 | 예 |
> | images | 예 | 예 |
> | 근접배치그룹 | 예 | 예 |
> | restorePointCollections | 예 | 예 |
> | 복원포인트컬렉션 / 리포지그포인트 | 예 | 예 |
> | 공유VM익스텐션 | 예 | 예 |
> | 공유VM익스 / 버전 | 예 | 예 |
> | sharedVMImages | 예 | 예 |
> | 공유VM이미지 / 버전 | 예 | 예 |
> | 스냅샷 | 예 | 예 |
> | ssh공개키 | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | 가상 머신 / 확장 | 예 | 예 |
> | 가상 머신 / 메트릭정의 | 예 | 예 |
> | virtualMachineScaleSets | 예 | 예 |
> | 가상머신스케일세트/확장 | 예 | 예 |
> | 가상머신스케일세트 / 네트워크인터페이스 | 예 | 예 |
> | 가상머신스케일세트 / 퍼블릭IP주소 | 예 | 예 |
> | 가상머신스케일셋 / 가상머신 | 예 | 예 |
> | 가상머신스케일셋 / 가상머신/ 네트워크인터페이스 | 예 | 예 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 예 | 예 |
> | 잔액 | 예 | 예 |
> | 예산 | 예 | 예 |
> | Charges | 예 | 예 |
> | CostTags | 예 | 예 |
> | credits | 예 | 예 |
> | events | 예 | 예 |
> | 예측 | 예 | 예 |
> | lots | 예 | 예 |
> | Marketplace | 예 | 예 |
> | Pricesheets | 예 | 예 |
> | products | 예 | 예 |
> | ReservationDetails | 예 | 예 |
> | ReservationRecommendations | 예 | 예 |
> | ReservationSummaries | 예 | 예 |
> | ReservationTransactions | 예 | 예 |
> | 태그들 | 예 | 예 |
> | tenants | 예 | 예 |
> | 용어 | 예 | 예 |
> | UsageDetails | 예 | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 예 | 예 |
> | serviceAssociationLinks | 예 | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | registries | 예 | 예 |
> | 레지스트리 / 에이전트풀 | 예 | 예 |
> | 레지스트리 / 빌드 | 예 | 예 |
> | 레지스트리 / 빌드 / 취소 | 예 | 예 |
> | 레지스트리 / 빌드 / getLogLink | 예 | 예 |
> | 레지스트리 / 빌드작업 | 예 | 예 |
> | 레지스트리 / 빌드작업 / 단계 | 예 | 예 |
> | 레지스트리 / 이벤트그리드필터 | 예 | 예 |
> | 레지스트리 / 생성자격 증명 | 예 | 예 |
> | 레지스트리 / getBuildSourceUploadUrl | 예 | 예 |
> | 레지스트리 / 자격 증명 받기 | 예 | 예 |
> | 레지스트리 / 가져오기이미지 | 예 | 예 |
> | 레지스트리 / 개인엔드포인트커넥션프록시 | 예 | 예 |
> | 레지스트리 / 개인엔드포인트커넥션프록시 / 검증 | 예 | 예 |
> | 레지스트리 / 개인엔드포인트연결 | 예 | 예 |
> | 레지스트리 / 개인링크리소스 | 예 | 예 |
> | 레지스트리 / 큐빌드 | 예 | 예 |
> | 레지스트리 / 다시 생성 자격 증명 | 예 | 예 |
> | 레지스트리 / 다시 생성자격 증명 | 예 | 예 |
> | 레지스트리 / 복제 | 예 | 예 |
> | 레지스트리 / 실행 | 예 | 예 |
> | 레지스트리 / 실행 / 취소 | 예 | 예 |
> | 레지스트리 / 일정 실행 | 예 | 예 |
> | 레지스트리 / 범위맵 | 예 | 예 |
> | 레지스트리 / 작업 실행 | 예 | 예 |
> | 레지스트리 / 작업 | 예 | 예 |
> | 레지스트리 / 토큰 | 예 | 예 |
> | 레지스트리 / 업데이트정책 | 예 | 예 |
> | 레지스트리 / 웹 후크 | 예 | 예 |
> | 레지스트리 / 웹 후크 / getCallbackConfig | 예 | 예 |
> | 레지스트리 / 웹 후크 / 핑 | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | 예 | 예 |
> | managedClusters | 예 | 예 |
> | 오픈 시프트관리 클러스터 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 경고 | 예 | 예 |
> | BillingAccounts | 예 | 예 |
> | 예산 | 예 | 예 |
> | 클라우드 커넥터 | 예 | 예 |
> | 커넥터 | 예 | 예 |
> | Departments | 예 | 예 |
> | 차원 | 예 | 예 |
> | EnrollmentAccounts | 예 | 예 |
> | 수출 | 예 | 예 |
> | 외부 결제 계정 | 예 | 예 |
> | 외부 결제 계정 / 경고 | 예 | 예 |
> | 외부 결제 계정 / 차원 | 예 | 예 |
> | 외부 결제 계정 / 예측 | 예 | 예 |
> | 외부 결제 계정 / 쿼리 | 예 | 예 |
> | 외부 구독 | 예 | 예 |
> | 외부 구독 / 경고 | 예 | 예 |
> | 외부 구독 / 치수 | 예 | 예 |
> | 외부 구독 / 예측 | 예 | 예 |
> | 외부 구독 / 쿼리 | 예 | 예 |
> | 예측 | 예 | 예 |
> | 쿼리 | 예 | 예 |
> | register | 예 | 예 |
> | Reportconfigs | 예 | 예 |
> | 보고서 | 예 | 예 |
> | 설정 | 예 | 예 |
> | 쇼백규칙 | 예 | 예 |
> | 보기 | 예 | 예 |

## <a name="microsoftcustomerlockbox"></a>마이크로소프트.고객 잠금 상자

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | requests | 예 | 예 |

## <a name="microsoftcustomproviders"></a>마이크로소프트.사용자 지정 공급자

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 연결 | 예 | 예 |
> | 리소스 공급자 | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | 작업 영역 / dbWork스페이스 | 예 | 예 |
> | 작업 영역 / 저장소암호화 | 예 | 예 |
> | 작업 영역 / 가상 네트워크 피어링 | 예 | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 예 | 예 |
> | 데이터 카탈로그 | 예 | 예 |
> | 데이터 카탈로그 / 데이터 소스 | 예 | 예 |
> | 데이터 카탈로그 / 데이터 소스 / 스캔 | 예 | 예 |
> | 데이터 카탈로그 / 데이터 소스 / 스캔 / 데이터 세트 | 예 | 예 |
> | 데이터 카탈로그 / 데이터 소스 / 스캔 / 트리거 | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 예 | 예 |
> | 데이터공장 / 진단설정 | 예 | 예 |
> | 데이터팩토리 / 메트릭정의 | 예 | 예 |
> | dataFactorySchema | 예 | 예 |
> | factories | 예 | 예 |
> | 공장 / 통합실행시간 | 예 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정 / 데이터레이크스토어계정 | 예 | 예 |
> | 계정 / 저장소 계정 계정 | 예 | 예 |
> | 계정 / 저장소계정 / 컨테이너 | 예 | 예 |
> | 계정 / 이체애분석유닛 | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정 / 이벤트그리드필터 | 예 | 예 |
> | 계정 / 방화벽규칙 | 예 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |
> | 서비스 / 프로젝트 | 예 | 예 |

## <a name="microsoftdatashare"></a>마이크로소프트.데이터 쉐어

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정 / 공유 | 예 | 예 |
> | 계정 / 공유 / 데이터 세트 | 예 | 예 |
> | 계정 / 공유 / 초대 | 예 | 예 |
> | 계정 / 주식 / 공급자 공유 구독 | 예 | 예 |
> | 계정 / 공유 / 동기화설정 | 예 | 예 |
> | 계정 / 공유 구독 | 예 | 예 |
> | 계정 / 공유 구독 / 소비자 소스데이터세트 | 예 | 예 |
> | 계정 / 공유 구독 / 데이터 집합 매핑 | 예 | 예 |
> | 계정 / 공유 구독 / 트리거 | 예 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | 서버 / 고문 | 예 | 예 |
> | 서버 / 키 | 예 | 예 |
> | 서버 / 개인엔드포인트커넥션프록시 | 예 | 예 |
> | 서버 / 개인엔드포인트연결 | 예 | 예 |
> | 서버 / 개인링크자원 | 예 | 예 |
> | 서버 / 쿼리텍스트 | 예 | 예 |
> | 서버 / 복구 서버 | 예 | 예 |
> | 서버 / topQueryStatistics | 예 | 예 |
> | 서버 / 가상 네트워크규칙 | 예 | 예 |
> | 서버 / 대기통계 | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | 서버 / 고문 | 예 | 예 |
> | 서버 / 키 | 예 | 예 |
> | 서버 / 개인엔드포인트커넥션프록시 | 예 | 예 |
> | 서버 / 개인엔드포인트연결 | 예 | 예 |
> | 서버 / 개인링크자원 | 예 | 예 |
> | 서버 / 쿼리텍스트 | 예 | 예 |
> | 서버 / 복구 서버 | 예 | 예 |
> | 서버 / topQueryStatistics | 예 | 예 |
> | 서버 / 가상 네트워크규칙 | 예 | 예 |
> | 서버 / 대기통계 | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 그룹 | 예 | 예 |
> | servers | 예 | 예 |
> | 서버 / 고문 | 예 | 예 |
> | 서버 / 키 | 예 | 예 |
> | 서버 / 개인엔드포인트커넥션프록시 | 예 | 예 |
> | 서버 / 개인엔드포인트연결 | 예 | 예 |
> | 서버 / 개인링크자원 | 예 | 예 |
> | 서버 / 쿼리텍스트 | 예 | 예 |
> | 서버 / 복구 서버 | 예 | 예 |
> | 서버 / topQueryStatistics | 예 | 예 |
> | 서버 / 가상 네트워크규칙 | 예 | 예 |
> | 서버 / 대기통계 | 예 | 예 |
> | 서버v2 | 예 | 예 |
> | 단일 서버 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 아티팩트 소스 | 예 | 예 |
> | rollouts | 예 | 예 |
> | 서비스토폴로지 | 예 | 예 |
> | 서비스토폴로지/ 서비스 | 예 | 예 |
> | 서비스토폴로지 / 서비스 / 서비스단위 | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>마이크로소프트.데스크톱 가상화

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 응용 프로그램 그룹 | 예 | 예 |
> | 응용 프로그램 그룹 / 응용 프로그램 | 예 | 예 |
> | 응용 프로그램 그룹 / 데스크톱 | 예 | 예 |
> | 응용 프로그램 그룹 / 시작 메뉴 항목 | 예 | 예 |
> | 호스트 풀 | 예 | 예 |
> | 호스트 풀 / 세션 호스트 | 예 | 예 |
> | 호스트 풀 / 세션 호스트 / 사용자 세션 | 예 | 예 |
> | 호스트 풀 / 사용자 세션 | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 탄성 풀 | 예 | 예 |
> | 탄성 풀 / IotHubTenants | 예 | 예 |
> | 탄성 풀 / IotHubTenants / 보안설정 | 예 | 예 |
> | IotHubs | 예 | 예 |
> | IotHubs / 이벤트그리드필터 | 예 | 예 |
> | IotHubs / 보안설정 | 예 | 예 |
> | ProvisioningServices | 예 | 예 |
> | usages | 예 | 예 |

## <a name="microsoftdevops"></a>마이크로소프트.데브옵스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 파이프라인 | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | 예 | 예 |
> | labs | 예 | 예 |
> | 실험실 / 환경 | 예 | 예 |
> | 랩 / 서비스 러너 | 예 | 예 |
> | 랩 / 가상 머신 | 예 | 예 |
> | schedules | 예 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 예 | 예 |
> | databaseAccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인 / 도메인소유권 식별자 | 예 | 예 |
> | generateSsoRequest | 예 | 예 |
> | topLevelDomains | 예 | 예 |
> | validateDomainRegistrationInformation | 예 | 예 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 예 | 예 |
> | lcsprojects / 클라우드 배포 | 예 | 예 |
> | lcsprojects / 커넥터 | 예 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>마이크로소프트.엔터프라이즈 지식그래프

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인 / 주제 | 예 | 예 |
> | eventSubscriptions | 예 | 예 |
> | extensionTopics | 예 | 예 |
> | 파트너 네임 스페이스 | 예 | 예 |
> | 파트너네임스페이스 / 이벤트채널 | 예 | 예 |
> | 파트너 등록 | 예 | 예 |
> | 파트너 주제 | 예 | 예 |
> | 파트너주제 / 이벤트 구독 | 예 | 예 |
> | 시스템 주제 | 예 | 예 |
> | 시스템주제 / 이벤트구독 | 예 | 예 |
> | topics | 예 | 예 |
> | topicTypes | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |
> | 네임스페이스/ 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스 / 재해 복구구성 | 예 | 예 |
> | 네임스페이스/ 이벤트 허브 | 예 | 예 |
> | 네임스페이스 / 이벤트 허브 / 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스 / 이벤트 허브 / 소비자 그룹 | 예 | 예 |
> | 네임스페이스 / 네트워크 규칙 집합 | 예 | 예 |

## <a name="microsoftfalcon"></a>마이크로소프트.팔콘

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 기능 공급자 | 예 | 예 |
> | 기능 | 예 | 예 |
> | providers | 예 | 예 |
> | 구독기능등록 | 예 | 예 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | enroll | 예 | 예 |
> | galleryitems | 예 | 예 |
> | generateartifactaccessuri | 예 | 예 |
> | myareas | 예 | 예 |
> | 마이지역/ 영역 | 예 | 예 |
> | 마이지역/ 지역/ 지역 | 예 | 예 |
> | 마이지역 / 지역 / 지역 / 갤러리 항목 | 예 | 예 |
> | 마이지역 / 영역 / 갤러리 아이템 | 예 | 예 |
> | 마이지역/ 갤러리 아이템 | 예 | 예 |
> | register | 예 | 예 |
> | 리소스 | 예 | 예 |
> | retrieveresourcesbyid | 예 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 자동 관리 계정 | 예 | 예 |
> | 자동 관리Vm구성 프로필 | 예 | 예 |
> | 구성 프로필할당 | 예 | 예 |
> | guestConfigurationAssignments | 예 | 예 |
> | software | 예 | 예 |
> | 소프트웨어업데이트프로필 | 예 | 예 |
> | 소프트웨어 업데이트 | 예 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 예 | 예 |
> | sapMonitors | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 전용HSMs | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 클러스터 / 응용 프로그램 | 예 | 예 |

## <a name="microsofthealthcareapis"></a>마이크로소프트.헬스케어아피

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 기계 | 예 | 예 |
> | 기계 / 확장 | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 데이터 관리자 | 예 | 예 |

## <a name="microsofthydra"></a>마이크로소프트.히드라

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | 네트워크 스코프 | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 앱 템플릿 | 예 | 예 |
> | IoTApps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 예 | 예 |
> | hsmPools | 예 | 예 |
> | vaults | 예 | 예 |
> | 볼트 / 액세스 정책 | 예 | 예 |
> | 볼트 / 이벤트그리드필터 | 예 | 예 |
> | 볼트 / 비밀 | 예 | 예 |

## <a name="microsoftkubernetes"></a>마이크로소프트.쿠베네테스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 연결된 클러스터 | 예 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 클러스터 / 첨부 데이터베이스 구성 | 예 | 예 |
> | 클러스터 / 데이터베이스 | 예 | 예 |
> | 클러스터 / 데이터베이스 / 데이터 연결 | 예 | 예 |
> | 클러스터 / 데이터베이스 / 이벤트 허브 연결 | 예 | 예 |
> | 클러스터 / 데이터베이스 / 보안 주체 할당 | 예 | 예 |
> | 클러스터 / 데이터 연결 | 예 | 예 |
> | 클러스터 / 보안 주체 할당 | 예 | 예 |
> | 클러스터 / 공유 ID | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 예 | 예 |
> | users | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 예 | 예 |
> | integrationAccounts | 예 | 예 |
> | 통합서비스환경 | 예 | 예 |
> | 통합서비스환경 / 관리형 | 예 | 예 |
> | 격리된환경 | 예 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 예 | 예 |
> | webServices | 예 | 예 |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | 작업 영역/ 계산 | 예 | 예 |
> | 작업 영역 / 이벤트그리드 필터 | 예 | 예 |

## <a name="microsoftmaintenance"></a>마이크로소프트.유지 보수

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 적용 업데이트 | 예 | 예 |
> | 구성할당 | 예 | 예 |
> | 유지 보수 구성 | 예 | 예 |
> | 업데이트 | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | Identities | 예 | 예 |
> | userAssignedIdentities | 예 | 예 |

## <a name="microsoftmanagedservices"></a>마이크로소프트.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 마켓플레이스등록정의 | 예 | 예 |
> | 등록할당 | 예 | 예 |
> | 등록정의 | 예 | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 예 | 예 |
> | managementGroups | 예 | 예 |
> | 관리 그룹 / 설정 | 예 | 예 |
> | 리소스 | 예 | 예 |
> | startTenantBackfill | 예 | 예 |
> | tenantBackfillStatus | 예 | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정 / 이벤트그리드필터 | 예 | 예 |
> | 계정 / 개인 아틀라스 | 예 | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | offers | 예 | 예 |
> | offerTypes | 예 | 예 |
> | 제공유형/퍼블리셔 | 예 | 예 |
> | 제공유형 /퍼블리셔/ 오퍼 | 예 | 예 |
> | 제공유형 / 퍼블리셔 / 오퍼 / 플랜 | 예 | 예 |
> | 제공유형 / 퍼블리셔 / 제안 / 계획 / 계약 | 예 | 예 |
> | 제공유형 / 퍼블리셔 / 오퍼 / 플랜 / 구성 | 예 | 예 |
> | 제공유형 / 퍼블리셔 / 오퍼 / 플랜 / 구성 / 수입이미지 | 예 | 예 |
> | privategalleryitems | 예 | 예 |
> | 프라이빗 스토어클라이언트 | 예 | 예 |
> | 프라이빗 스토어 | 예 | 예 |
> | 프라이빗 스토어 / 오퍼 | 예 | 예 |
> | products | 예 | 예 |
> | 게시자 | 예 | 예 |
> | 게시자 / 제안 | 예 | 예 |
> | 게시자 / 제안 / 수정 | 예 | 예 |
> | register | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 예 | 예 |
> | updateCommunicationPreference | 예 | 예 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | agreements | 예 | 예 |
> | offertypes | 예 | 예 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 예 | 예 |
> | 미디어 서비스 / 계정 필터 | 예 | 예 |
> | 미디어 서비스 / 자산 | 예 | 예 |
> | 미디어 서비스 / 자산 / 자산필터 | 예 | 예 |
> | 미디어 서비스 / 콘텐츠키정책 | 예 | 예 |
> | 미디어 서비스 / 이벤트그리드필터 | 예 | 예 |
> | 미디어 서비스 / 라이브이벤트운영 | 예 | 예 |
> | 미디어 서비스 / 라이브 이벤트 | 예 | 예 |
> | 미디어 서비스 / 라이브 이벤트 / 라이브 출력 | 예 | 예 |
> | 미디어 서비스 / 라이브 출력운영 | 예 | 예 |
> | 미디어 서비스 / 미디어 그래프 | 예 | 예 |
> | 미디어 서비스 / 스트리밍엔드포인트운영 | 예 | 예 |
> | 미디어 서비스 / 스트리밍엔드포인트 | 예 | 예 |
> | 미디어 서비스 / 스트리밍로케이터 | 예 | 예 |
> | 미디어 서비스 / 스트리밍정책 | 예 | 예 |
> | 미디어 서비스 / 변환 | 예 | 예 |
> | 미디어 서비스 / 변환 / 작업 | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>마이크로소프트.마이크로 서비스4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 앱 클러스터 | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 평가 프로젝트 | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | 이동컬렉션 | 예 | 예 |
> | projects | 예 | 예 |

## <a name="microsoftmixedreality"></a>마이크로소프트.혼합현실

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 홀로그램방송계정 | 예 | 예 |
> | 개체이해계정 | 예 | 예 |
> | 원격 렌더링계정 | 예 | 예 |
> | 공간앵커계정 | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 넷앱 어카운트 | 예 | 예 |
> | netApp계정 / 계정백업 | 예 | 예 |
> | netApp계정 / 용량풀 | 예 | 예 |
> | netApp계정 / 용량풀 / 볼륨 | 예 | 예 |
> | netApp계정 / 용량풀 / 볼륨 / 스냅샷 | 예 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 예 | 예 |
> | 응용 프로그램게이트웨이웹응용방화벽정책 | 예 | 예 |
> | applicationSecurityGroups | 예 | 예 |
> | azureFirewallFqdnTags | 예 | 예 |
> | azureFirewalls | 예 | 예 |
> | 요새호스트 | 예 | 예 |
> | bgpServiceCommunities | 예 | 예 |
> | connections | 예 | 예 |
> | ddosCustomPolicies | 예 | 예 |
> | ddosProtectionPlans | 예 | 예 |
> | dnsOperationStatuses | 예 | 예 |
> | dnszones | 예 | 예 |
> | dnszones / A | 예 | 예 |
> | dnszones / AAAA | 예 | 예 |
> | dnszones / 모든 | 예 | 예 |
> | dnszones / CAA | 예 | 예 |
> | dnszones / CNAME | 예 | 예 |
> | dnszones / MX | 예 | 예 |
> | dnszones / NS | 예 | 예 |
> | dnszones / PTR | 예 | 예 |
> | dnszones / 레코드 세트 | 예 | 예 |
> | dnszones / SOA | 예 | 예 |
> | dnszones / SRV | 예 | 예 |
> | dnszones / TXT | 예 | 예 |
> | expressRouteCircuits | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | 익스프레스루트게이트웨이 | 예 | 예 |
> | 익스프레스루트포트 | 예 | 예 |
> | expressRouteServiceProviders | 예 | 예 |
> | 방화벽정책 | 예 | 예 |
> | frontdoors | 예, 하지만 제한 [(아래 참고](#frontdoor)참조) | 예 |
> | 프론트도어웹응용프로그램관리규칙세트 | 예, 하지만 제한 [(아래 참고](#frontdoor)참조) | 예 |
> | frontdoorWebApplicationFirewallPolicies | 예, 하지만 제한 [(아래 참고](#frontdoor)참조) | 예 |
> | getDnsResourceReference | 예 | 예 |
> | internalNotify | 예 | 예 |
> | loadBalancers | 예 | 예 |
> | localNetworkGateways | 예 | 예 |
> | natGateways | 예 | 예 |
> | networkIntentPolicies | 예 | 예 |
> | networkInterfaces | 예 | 예 |
> | networkProfiles | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | networkWatchers | 예 | 예 |
> | 네트워크 감시자 / 연결모니터 | 예 | 예 |
> | 네트워크 감시자 / 플로우로그 | 예 | 예 |
> | 네트워크 감시자 / 렌즈 | 예 | 예 |
> | 네트워크 감시자 / 핑메시 | 예 | 예 |
> | p2sVpn게이트웨이 | 예 | 예 |
> | privateDns운영 상태 | 예 | 예 |
> | 프라이빗DnsZones | 예 | 예 |
> | 프라이빗DnsZones / A | 예 | 예 |
> | 프라이빗들존 / AAAA | 예 | 예 |
> | 프라이빗DnsZones / 모든 | 예 | 예 |
> | 프라이빗디언스존 / CNAME | 예 | 예 |
> | 프라이빗디존스 / MX | 예 | 예 |
> | 프라이빗디존스 / PTR | 예 | 예 |
> | 프라이빗디존스 / SOA | 예 | 예 |
> | 프라이빗디존스 / SRV | 예 | 예 |
> | 프라이빗디존스 / TXT | 예 | 예 |
> | 프라이빗DnsZones / 가상네트워크링크 | 예 | 예 |
> | 프라이빗 엔드포인트 | 예 | 예 |
> | privateLinkServices | 예 | 예 |
> | publicIPAddresses | 예 | 예 |
> | publicIPPrefixes | 예 | 예 |
> | routeFilters | 예 | 예 |
> | routeTables | 예 | 예 |
> | serviceEndpointPolicies | 예 | 예 |
> | trafficManagerGeographicHierarchies | 예 | 예 |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles/heatMaps | 예 | 예 |
> | 트래픽관리자사용자메트릭키 | 예 | 예 |
> | virtualHubs | 예 | 예 |
> | virtualNetworkGateways | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworkTaps | 예 | 예 |
> | virtualWans | 예 | 예 |
> | vpnGateways | 예 | 예 |
> | vpnSites | 예 | 예 |
> | webApplicationFirewallPolicies | 예 | 예 |

<a id="frontdoor" />

> [!NOTE]
> Azure 정문 서비스의 경우 리소스를 만들 때 태그를 적용할 수 있지만 태그 업데이트 또는 추가는 현재 지원되지 않습니다.


## <a name="microsoftnotebooks"></a>마이크로소프트.노트북

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 노트북 프록시 | 예 | 예 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | 네임스페이스 / 알림허브 | 예 | 예 |

## <a name="microsoftobjectstore"></a>마이크로소프트.오브젝트스토어

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | os네임 스페이스 | 예 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 하이퍼V사이트 | 예 | 예 |
> | 가져오기사이트 | 예 | 예 |
> | 서버 사이트 | 예 | 예 |
> | VMware사이트 | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | linkTargets | 예 | 예 |
> | storageInsightConfigs | 예 | 예 |
> | workspaces | 예 | 예 |
> | 작업 영역 / 데이터수출 | 예 | 예 |
> | 작업 영역 / 데이터 소스 | 예 | 예 |
> | 작업 영역 / 링크된 서비스 | 예 | 예 |
> | 작업 영역 / 링크된저장소 계정 | 예 | 예 |
> | 작업 영역/ 쿼리 | 예 | 예 |
> | 작업 영역 / 범위개인링크프록시 | 예 | 예 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 예 | 예 |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>마이크로소프트.피어링

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 레거시피어링 | 예 | 예 |
> | 피어아시 | 예 | 예 |
> | 피어링 | 예 | 예 |
> | 피어링서비스국가 | 예 | 예 |
> | 피어링서비스프로바이더 | 예 | 예 |
> | 피어링 서비스 | 예 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 예 | 예 |
> | 정책 메타데이터 | 예 | 예 |
> | policyStates | 예 | 예 |
> | policyTrackedResources | 예 | 예 |
> | remediations | 예 | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 예 | 예 |
> | dashboards | 예 | 예 |
> | userSettings | 예 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | capacities | 예 | 예 |

## <a name="microsoftprojectbabylon"></a>마이크로소프트.프로젝트바빌론

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftquantum"></a>마이크로소프트.퀀텀

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 예 | 예 |
> | vaults | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | 네임스페이스/ 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스 / 하이브리드 연결 | 예 | 예 |
> | 네임스페이스 / 하이브리드 연결 / 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스 / wcfrelays | 예 | 예 |
> | 네임스페이스 / wcfrelays / 권한 부여 규칙 | 예 | 예 |

## <a name="microsoftremoteapp"></a>마이크로소프트.리모트 앱

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 컬렉션 | 예 | 예 |
> | 컬렉션 / 응용 프로그램 | 예 | 예 |
> | 컬렉션 / 보안 교장 | 예 | 예 |
> | 템플릿 이미지 | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | 예 | 예 |
> | 리소스변경세부정보 | 예 | 예 |
> | 리소스 변경 | 예 | 예 |
> | 리소스 | 예 | 예 |
> | 리소스기록 | 예 | 예 |
> | subscriptionsStatus | 예 | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 예 | 예 |
> | childAvailabilityStatuses | 예 | 예 |
> | childResources | 예 | 예 |
> | 신흥 이슈 | 예 | 예 |
> | events | 예 | 예 |
> | impactedResources | 예 | 예 |
> | metadata | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 배포 | 예 | 예 |
> | 배포/작업 | 예 | 예 |
> | 배포스크립트 | 예 | 예 |
> | 배포스크립트/로그 | 예 | 예 |
> | 링크 | 예 | 예 |
> | notifyResourceJobs | 예 | 예 |
> | providers | 예 | 예 |
> | resourceGroups | 예 | 예 |
> | 구독 | 예 | 예 |
> | tenants | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | saasresources | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 예 | 예 |
> | searchServices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 적응네트워크강화 | 예 | 예 |
> | advancedThreatProtectionSettings | 예 | 예 |
> | 경고 | 예 | 예 |
> | allowedConnections | 예 | 예 |
> | applicationWhitelistings | 예 | 예 |
> | 평가메타데이터 | 예 | 예 |
> | 평가 | 예 | 예 |
> | 자동 해제 경고규칙 | 예 | 예 |
> | 자동화 | 예 | 예 |
> | AutoProvisioningSettings | 예 | 예 |
> | Compliances | 예 | 예 |
> | dataCollectionAgents | 예 | 예 |
> | 장치 보안 그룹 | 예 | 예 |
> | discoveredSecuritySolutions | 예 | 예 |
> | externalSecuritySolutions | 예 | 예 |
> | InformationProtectionPolicies | 예 | 예 |
> | 이오시큐리티솔루션 | 예 | 예 |
> | iot보안솔루션 / 분석모델 | 예 | 예 |
> | iot보안 솔루션 / 분석모델 / 집계경고 | 예 | 예 |
> | iot보안솔루션 / 분석모델 / 집계추천 | 예 | 예 |
> | jitNetworkAccessPolicies | 예 | 예 |
> | 네트워크데이터 | 예 | 예 |
> | 정책 | 예 | 예 |
> | pricings | 예 | 예 |
> | 규제 준수 표준 | 예 | 예 |
> | 규제 준수 표준 / 규제 준수 규정 제어 | 예 | 예 |
> | 규제 준수 표준 / 규제 준수 제어 / 규제 준수 평가 | 예 | 예 |
> | 보안 점수 제어정의 | 예 | 예 |
> | 보안 점수 컨트롤 | 예 | 예 |
> | 보안 점수 | 예 | 예 |
> | 보안 점수 / 보안 점수컨트롤 | 예 | 예 |
> | securityContacts | 예 | 예 |
> | securitySolutions | 예 | 예 |
> | securitySolutionsReferenceData | 예 | 예 |
> | securityStatuses | 예 | 예 |
> | securityStatusesSummaries | 예 | 예 |
> | 서버취약성평가 | 예 | 예 |
> | 설정 | 예 | 예 |
> | 하위 평가 | 예 | 예 |
> | tasks | 예 | 예 |
> | topologies | 예 | 예 |
> | workspaceSettings | 예 | 예 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |

## <a name="microsoftsecurityinsights"></a>마이크로소프트.시큐리티인사이트

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | aggregations | 예 | 예 |
> | 경고규칙 | 예 | 예 |
> | 경고규칙 템플릿 | 예 | 예 |
> | 책갈피 | 예 | 예 |
> | cases | 예 | 예 |
> | 데이터 커넥터 | 예 | 예 |
> | 데이터 커넥터체크요구사항 | 예 | 예 |
> | 엔터티 | 예 | 예 |
> | 엔터티쿼리 | 예 | 예 |
> | 사건 | 예 | 예 |
> | 사무실동의 | 예 | 예 |
> | 설정 | 예 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | 네임스페이스/ 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스 / 재해 복구구성 | 예 | 예 |
> | 네임스페이스/이벤트그리드필터 | 예 | 예 |
> | 네임스페이스 / 네트워크 규칙 집합 | 예 | 예 |
> | 네임스페이스/큐 | 예 | 예 |
> | 네임스페이스 / 큐 / 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스/ 토픽 | 예 | 예 |
> | 네임스페이스 / 토픽 / 권한 부여 규칙 | 예 | 예 |
> | 네임스페이스 / 토픽 / 구독 | 예 | 예 |
> | 네임 스페이스 / 토픽 / 구독 / 규칙 | 예 | 예 |
> | premiumMessagingRegions | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | 예 | 예 |
> | 클러스터 / 응용 프로그램 | 예 | 예 |
> | containerGroups | 예 | 예 |
> | 컨테이너그룹세트 | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | 에지 클러스터 / 응용 프로그램 | 예 | 예 |
> | managedclusters | 예 | 예 |
> | 관리되는 클러스터/ 노드 유형 | 예 | 예 |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | 비밀 상점 / 인증서 | 예 | 예 |
> | 비밀 상점 / 비밀 | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | containerGroups | 예 | 예 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>마이크로소프트 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 예 | 예 |
> | 공급자등록 /자료유형등록 | 예 | 예 |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | 예 | 예 |
> | 시그널R /이벤트그리드필터 | 예 | 예 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>마이크로소프트.소프트웨어플랜

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 하이브리드사용혜택 | 예 | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitRequests | 예 | 예 |

## <a name="microsoftspoolservice"></a>마이크로소프트.스풀서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 등록된 구독 | 예 | 예 |
> | 스풀 | 예 | 예 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 예 | 예 |
> | 관리 인스턴스 / 데이터베이스 | [예(아래 참고 참조)](#sqlnote) | 예 |
> | 관리인스턴스 / 데이터베이스 / 백업단기 보존 정책 | 예 | 예 |
> | managedInstances / 데이터베이스 / 스키마 / 테이블 / 열 / 감도레이블 | 예 | 예 |
> | 관리인스턴스 / 데이터베이스 / 취약점평가 | 예 | 예 |
> | 관리인스턴스 / 데이터베이스 / 취약점평가 / 규칙 / 기준 | 예 | 예 |
> | 관리인스턴스 / 암호화 보호기 | 예 | 예 |
> | 관리 인스턴스 / 키 | 예 | 예 |
> | 관리인스턴스 / 복원 가능한 데이터베이스 / 백업단기 보존 정책 | 예 | 예 |
> | 관리인스턴스 / 취약점평가 | 예 | 예 |
> | servers | 예 | 예 |
> | 서버 / 관리자 | 예 | 예 |
> | 서버 / 통신링크 | 예 | 예 |
> | 서버 / 데이터베이스 | [예(아래 참고 참조)](#sqlnote) | 예 |
> | 서버 / 암호화 보호기 | 예 | 예 |
> | 서버 / 방화벽규칙 | 예 | 예 |
> | 서버 / 키 | 예 | 예 |
> | 서버 / 복원 가능데이터베이스 | 예 | 예 |
> | 서버 / 서비스 목적 | 예 | 예 |
> | 서버 / tde인증서 | 예 | 예 |
> | 가상 클러스터 | 예 | 예 |

<a id="sqlnote" />

> [!NOTE]
> master 데이터베이스는 태그를 지원하지 않지만, Azure SQL Data Warehouse 데이터베이스를 포함한 다른 데이터베이스는 태그를 지원합니다. Azure SQL Data Warehouse 데이터베이스가 활성(일시 중지 아님) 상태여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 예 | 예 |
> | SqlVirtualMachine그룹 / 가용성그룹리스 | 예 | 예 |
> | SqlVirtualMachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 예 | 예 |
> | 저장소계정 / blob서비스 | 예 | 예 |
> | 저장소계정 / 파일서비스 | 예 | 예 |
> | 저장소계정/ 큐서비스 | 예 | 예 |
> | 저장소계정/ 서비스 | 예 | 예 |
> | 저장소계정 / 서비스 / 메트릭정의 | 예 | 예 |
> | 저장소계정 / 테이블서비스 | 예 | 예 |
> | usages | 예 | 예 |

## <a name="microsoftstoragecache"></a>마이크로소프트.스토리지캐시

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 캐시 | 예 | 예 |
> | 캐시 / 저장소 대상 | 예 | 예 |
> | 사용 모델 | 예 | 예 |

## <a name="microsoftstoragereplication"></a>마이크로소프트.스토리지복제

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 복제 그룹 | 예 | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | 스토리지싱크서비스 /등록서버 | 예 | 예 |
> | 스토리지싱크서비스 /동기화그룹 | 예 | 예 |
> | 스토리지싱크서비스 /동기화그룹/클라우드엔드포인트 | 예 | 예 |
> | 스토리지싱크서비스 / 싱크그룹 / 서버엔드포인트 | 예 | 예 |
> | 스토리지싱크서비스/워크플로 | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | 스토리지싱크서비스 /등록서버 | 예 | 예 |
> | 스토리지싱크서비스 /동기화그룹 | 예 | 예 |
> | 스토리지싱크서비스 /동기화그룹/클라우드엔드포인트 | 예 | 예 |
> | 스토리지싱크서비스 / 싱크그룹 / 서버엔드포인트 | 예 | 예 |
> | 스토리지싱크서비스/워크플로 | 예 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | 스토리지싱크서비스 /등록서버 | 예 | 예 |
> | 스토리지싱크서비스 /동기화그룹 | 예 | 예 |
> | 스토리지싱크서비스 /동기화그룹/클라우드엔드포인트 | 예 | 예 |
> | 스토리지싱크서비스 / 싱크그룹 / 서버엔드포인트 | 예 | 예 |
> | 스토리지싱크서비스/워크플로 | 예 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 예(아래 참고를 참조) | 예 |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | cancel | 예 | 예 |
> | CreateSubscription | 예 | 예 |
> | enable | 예 | 예 |
> | 이름 바꾸기 | 예 | 예 |
> | SubscriptionDefinitions | 예 | 예 |
> | SubscriptionOperations | 예 | 예 |
> | 구독 | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 예 |
> | 환경 / 액세스 정책 | 예 | 예 |
> | 환경 / 이벤트 소스 | 예 | 예 |
> | 환경 / 참조데이터 세트 | 예 | 예 |

## <a name="microsoftvmwarecloudsimple"></a>마이크로소프트.VM웨어클라우드심플

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | 전용 클라우드 노드 | 예 | 예 |
> | 전용 클라우드 서비스 | 예 | 예 |
> | virtualMachines | 예 | 예 |

## <a name="microsoftvnfmanager"></a>마이크로소프트.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | devices | 예 | 예 |
> | 등록된 구독 | 예 | 예 |
> | 공급 업체 | 예 | 예 |
> | 공급 업체 / 스쿠스 | 예 | 예 |
> | 공급 업체 / vnfs | 예 | 예 |
> | 가상 네트워크기능스쿠스 | 예 | 예 |
> | vnfs | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 예 | 예 |
> | api관리계정 / apiAcls | 예 | 예 |
> | api관리계정 / API | 예 | 예 |
> | api관리계정 / API / apiAcls | 예 | 예 |
> | api관리계정 / APIS / 연결Acls | 예 | 예 |
> | api관리계정 / APIS / 연결 | 예 | 예 |
> | api관리계정 / APIS / 연결 / 연결Acls | 예 | 예 |
> | api관리계정 / APIS / 지역화정의 | 예 | 예 |
> | api관리계정 / 연결Acls | 예 | 예 |
> | api관리계정 / 연결 | 예 | 예 |
> | billingMeters | 예 | 예 |
> | certificates | 예 | 예 |
> | connectionGateways | 예 | 예 |
> | connections | 예 | 예 |
> | customApis | 예 | 예 |
> | deletedSites | 예 | 예 |
> | hostingEnvironments | 예 | 예 |
> | 호스팅환경 / 이벤트그리드필터 | 예 | 예 |
> | 호스팅환경 / 멀티롤풀 | 예 | 예 |
> | 호스팅환경 / 워커풀 | 예 | 예 |
> | 쿠베환경 | 예 | 예 |
> | publishingUsers | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | resourceHealthMetadata | 예 | 예 |
> | runtimes | 예 | 예 |
> | serverFarms | 예 | 예 |
> | 서버농장 / 이벤트그리드필터 | 예 | 예 |
> | sites | 예 | 예 |
> | 사이트 / 구성  | 예 | 예 |
> | 사이트 / 이벤트그리드필터 | 예 | 예 |
> | 사이트 / 호스트네임 바인딩 | 예 | 예 |
> | 사이트 / 네트워크구성 | 예 | 예 |
> | 사이트 / 프리미어 애드온 | 예 | 예 |
> | 사이트 / 슬롯 | 예 | 예 |
> | 사이트 / 슬롯 / 이벤트그리드 필터 | 예 | 예 |
> | 사이트 / 슬롯 / 호스트네임 바인딩 | 예 | 예 |
> | 사이트 / 슬롯 / 네트워크구성 | 예 | 예 |
> | sourceControls | 예 | 예 |
> | 정적 사이트 | 예 | 예 |
> | validate | 예 | 예 |
> | verifyHostingEnvironmentVnet | 예 | 예 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 예 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서에 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | componentsSummary | 예 | 예 |
> | monitorInstances | 예 | 예 |
> | monitorInstancesSummary | 예 | 예 |
> | monitors | 예 | 예 |
> | notificationSettings | 예 | 예 |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
