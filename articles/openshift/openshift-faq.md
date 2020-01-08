---
title: Azure Red Hat OpenShift에 대 한 질문과 대답 | Microsoft Docs
description: Microsoft Azure Red Hat OpenShift에 대 한 일반적인 질문에 대 한 답변은 다음과 같습니다.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: 2b4e78db9f3aa3a8f678212c7fcd1b97ed4834b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378214"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift FAQ

이 문서에서는 Red Hat OpenShift Microsoft Azure에 대 한 Faq (질문과 대답)를 다룹니다.

## <a name="which-azure-regions-are-supported"></a>지원 되는 Azure 지역은 무엇 인가요?

Azure Red Hat OpenShift가 지원 되는 글로벌 지역 목록은 지원 되는 [리소스](supported-resources.md#azure-regions) 를 참조 하세요.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>클러스터를 기존 가상 네트워크에 배포할 수 있나요?

아닙니다. 그러나 피어 링을 통해 Azure Red Hat OpenShift 클러스터를 기존 VNET에 연결할 수 있습니다. 자세한 내용은 [기존 가상 네트워크에 클러스터의 가상 네트워크 연결](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) 을 참조 하세요.

## <a name="what-cluster-operations-are-available"></a>사용할 수 있는 클러스터 작업은 무엇 인가요?

계산 노드 수를 확장 하거나 축소할 수만 있습니다. `Microsoft.ContainerService/openShiftManagedClusters` 리소스를 만든 후에는 다른 수정 작업을 수행할 수 없습니다. 최대 계산 노드 수는 20 개로 제한 됩니다.

## <a name="what-virtual-machine-sizes-can-i-use"></a>어떤 가상 머신 크기를 사용할 수 있나요?

Azure Red Hat OpenShift 클러스터에서 사용할 수 있는 가상 머신 크기 목록은 [Azure Red Hat openshift 가상 머신 크기](supported-resources.md#virtual-machine-sizes) 를 참조 하세요.

## <a name="is-data-on-my-cluster-encrypted"></a>내 클러스터의 데이터가 암호화 되나요?

기본적으로 미사용 암호화가 있습니다. Azure Storage 플랫폼은 데이터를 유지 하기 전에 자동으로 암호화 하 고 검색 하기 전에 데이터를 해독 합니다. 자세한 내용은 [미사용 데이터에 대 한 서비스 암호화 Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 를 참조 하세요.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>프로메테우스/Grafana를 사용 하 여 응용 프로그램을 모니터링할 수 있나요?

예, 네임 스페이스에 프로메테우스를 배포 하 고 네임 스페이스에서 응용 프로그램을 모니터링할 수 있습니다.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>프로메테우스/Grafana를 사용 하 여 클러스터 상태 및 용량과 관련 된 메트릭을 모니터링할 수 있나요?

아니요, 현재는 그렇지 않습니다.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker 레지스트리는 외부에서 사용할 수 있으므로 Jenkins와 같은 도구를 사용할 수 있나요?

Docker 레지스트리는 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/`에서 사용할 수 있지만 강력한 저장소 내구성 보장은 제공 되지 않습니다. [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)를 사용할 수도 있습니다.

## <a name="is-cross-namespace-networking-supported"></a>네임 스페이스 간 네트워킹이 지원 되나요?

고객 및 개별 프로젝트 관리자는 `NetworkPolicy` 개체를 사용 하 여 프로젝트 별로 상호 네임 스페이스 네트워킹 (거부 포함)을 사용자 지정할 수 있습니다.

## <a name="can-an-admin-manage-users-and-quotas"></a>관리자가 사용자 및 할당량을 관리할 수 있나요?

예. Azure Red Hat OpenShift 관리자는 사용자가 만든 모든 프로젝트에 액세스 하는 것 외에도 사용자 및 할당량을 관리할 수 있습니다.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>클러스터를 특정 Azure AD 사용자로만 제한할 수 있나요?

예. Azure ad 응용 프로그램을 구성 하 여 클러스터에 로그인 할 수 있는 Azure AD 사용자를 제한할 수 있습니다. 자세한 내용은 [방법: 사용자 집합으로 앱 제한](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users) 을 참조 하세요.

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>클러스터가 여러 Azure 지역에 계산 노드를 가질 수 있나요?

아닙니다. Azure Red Hat OpenShift 클러스터의 모든 노드는 동일한 Azure 지역에서 시작 되어야 합니다.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)를 사용 하는 경우 마스터 및 인프라 노드가 추출 되나요?

아닙니다. 클러스터 마스터를 비롯 한 모든 리소스는 고객 구독에서 실행 됩니다. 이러한 유형의 리소스는 읽기 전용 리소스 그룹에 배치 됩니다.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>OSBA (Open Service Broker for Azure)가 지원 되나요?

예. Azure Red Hat OpenShift와 함께 OSBA를 사용할 수 있습니다. 자세한 내용은 [Azure에 대 한 Open Service Broker](https://github.com/Azure/open-service-broker-azure#openshift-project-template) 를 참조 하세요.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>다른 구독의 가상 네트워크에 피어 링 하려고 하지만 `Failed to get vnet CIDR` 오류가 발생 했습니다.

가상 네트워크가 있는 구독에서 `Microsoft.ContainerService` 공급자를 `az provider register -n Microsoft.ContainerService --wait`에 등록 해야 합니다. 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Azure Red Hat OpenShift (ARO) 유지 관리 프로세스는 무엇 인가요?

ARO는 업그레이드, etcd 데이터 백업 및 복원, 클라우드 공급자 시작 유지 관리의 세 가지 유형이 있습니다.

+ 업그레이드에는 소프트웨어 업그레이드와 CVEs가 포함 됩니다. CVE 업데이트는 `yum update`를 실행 하 여 시작 시 발생 하며 즉각적인 완화를 위해 제공 됩니다.  병렬로 새 이미지 빌드는 나중에 클러스터 만들기를 위해 만들어집니다.

+ Etcd 데이터의 백업 및 관리는 작업에 따라 클러스터 가동 중지 시간이 필요할 수 있는 자동화 된 프로세스입니다. Etcd 데이터베이스가 백업에서 복원 되는 경우 가동 중지 시간이 발생 합니다. Etcd를 매시간 백업 하 고 최근 6 시간 동안 백업을 유지 합니다.

+ 클라우드 공급자에서 시작 된 유지 관리에는 네트워크, 저장소 및 지역 중단이 포함 됩니다. 유지 관리는 클라우드 공급자에 따라 다르며 공급자가 제공한 업데이트를 사용 합니다.

## <a name="what-is-the-general-upgrade-process"></a>일반적인 업그레이드 프로세스는 무엇 인가요?

업그레이드 실행은 안전 하 게 실행 해야 하며 클러스터 서비스를 중단 해서는 안 됩니다. 새 버전을 사용할 수 있거나 CVEs가 해결 되지 않으면 SRE에서 업그레이드 프로세스를 트리거할 수 있습니다.

사용 가능한 업데이트는 단계 환경에서 테스트 한 다음 프로덕션 클러스터에 적용 됩니다. 적용 되 면 새 노드가 일시적으로 추가 되 고 pod에서 복제본 수를 유지 관리할 수 있도록 회전 방식으로 노드가 업데이트 됩니다. 다음 모범 사례에 따라 가동 중지 시간을 최소화 하는 데 도움이 됩니다.

보류 중인 업그레이드 또는 업데이트의 심각도에 따라 업데이트를 신속 하 게 적용 하 여 서비스에서 CVE로의 노출을 완화할 수 있다는 점에서 프로세스가 다를 수 있습니다. 새 이미지는 비동기적으로 빌드되고 테스트 되 고 클러스터 업그레이드로 롤아웃 됩니다. 그 외에는 응급 유지 관리와 계획 된 유지 관리 간에 차이가 없습니다. 계획 된 유지 관리는 고객과 prescheduled 되지 않습니다.

고객에 대 한 통신이 필요한 경우 ICM 및 전자 메일을 통해 알림을 보낼 수 있습니다.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>비상 및 계획 된 유지 관리 기간에 대 한 정보

두 가지 유형의 유지 관리를 구분 하지 않습니다. Microsoft 팀은 24/7/365을 사용할 수 있으며 기존의 예약 된 "시간 제한" 유지 관리 기간을 사용 하지 않습니다.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>운영 체제 및 OpenShift 소프트웨어를 호스트 하는 방법은 무엇입니까?

호스트 운영 체제 및 OpenShift 소프트웨어는 일반적인 업그레이드 및 이미지 빌드 프로세스를 통해 업데이트 됩니다.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>업데이트 된 노드를 다시 부팅 하는 프로세스는 무엇 인가요?

업그레이드의 일부로 처리 되어야 합니다.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>데이터는 ARO로 암호화 된 etcd에 저장 되나요?

Etcd 수준에서 암호화 되지 않습니다. 이 기능을 설정 하는 옵션은 현재 지원 되지 않습니다. OpenShift는이 기능을 지원 하지만도로 지도에서 엔지니어링 노력을 해야 합니다. 데이터는 디스크 수준에서 암호화 됩니다. 자세한 내용은 [데이터 저장소 계층에서 데이터 암호화](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) 를 참조 하세요.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>기본 Vm의 로그를 고객 로그 분석 시스템으로 스트리밍할 수 있나요?

Syslog, docker 로그, 저널 및 dmesg는 관리 서비스에서 처리 되며 고객에 게 노출 되지 않습니다.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>사용자가 노드 수준에서 CPU/메모리와 같은 메트릭에 액세스 하 여 확장, 문제 해결 등의 작업을 수행 하는 방법에 대해 알아봅니다. ARO 클러스터에서 `kubectl top`를 실행 하는 것으로 보일 수 없습니다.

고객은 명령 `oc adm top nodes`를 사용 하 여 노드 수준에서 CPU/메모리 메트릭에 액세스 하거나 고객 관리 clusterrole를 `kubectl top nodes` 수 있습니다.  또한 고객은 명령 `oc adm top pods` 또는을 사용 하 여 `pods`의 CPU/메모리 메트릭에 액세스할 수 있습니다 `kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO의 기본 pod 스케줄러 구성은 무엇입니까?

ARO는 OpenShift에서 제공 되는 기본 스케줄러를 사용 합니다. ARO에서 지원 되지 않는 몇 가지 추가 메커니즘이 있습니다. 자세한 내용은 [기본 scheduler 설명서](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) 및 [마스터 스케줄러 설명서](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) 를 참조 하세요.

고급/사용자 지정 일정은 현재 지원 되지 않습니다. 자세한 내용은 [일정 설명서](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) 를 참조 하세요.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>배포를 강화 하는 경우 Azure 장애 도메인을 pod 배치로 매핑하여 서비스에 대 한 모든 pod 단일 장애 도메인의 오류로 인해 끊으면 되지 않도록 할 수 있나요?

Azure에서 가상 머신 확장 집합을 사용 하는 경우 기본적으로 5 개의 장애 도메인이 있습니다. 확장 집합의 각 가상 머신 인스턴스는 이러한 장애 도메인 중 하나에 배치 됩니다. 이렇게 하면 클러스터의 계산 노드에 배포 된 응용 프로그램이 별도의 오류 도메인에 배치 됩니다.

자세한 내용은 [가상 머신 확장 집합의 올바른 장애 도메인 수 선택](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) 을 참조 하세요.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Pod 배치를 관리 하는 방법이 있나요?

고객은 노드를 가져오고 레이블을 고객 관리로 볼 수 있습니다.  이렇게 하면 확장 집합의 모든 VM을 대상으로 지정할 수 있습니다.

특정 레이블을 사용 하는 경우 주의 해야 합니다.

- 호스트 이름을 사용 하지 않아야 합니다. 호스트 이름은 업그레이드 및 업데이트로 자주 회전 되며 변경 될 수 있습니다.

- 고객이 특정 레이블이나 배포 전략에 대 한 요청을 수행 하는 경우이 작업은 가능 하지만 엔지니어링 노력이 필요 하며 현재 지원 되지 않습니다.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO 클러스터의 최대 pod 수는 얼마 인가요?  ARO의 노드당 최대 pod 수는 얼마 인가요?

 Azure Red Hat OpenShift 3.11에는 [20 개의 계산 노드 제한이](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)있는 pod의 노드당 50-pod가 있으므로, aro 클러스터에서 지원 되는 최대 수를 50 * 20 = 1000로 제한 합니다.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>사설 VNET에서 배포 하기 위한 IP 범위를 지정 하 여 다른 회사 Vnet 피어 링 한 후 충돌을 방지할 수 있나요?

Azure Red Hat OpenShift는 VNET 피어 링을 지원 하며, 고객이 피어 링을 제공할 수 있도록 하 고, OpenShift 네트워크에서 작동 하는 VNET CIDR을 제공 합니다.

ARO에서 만든 VNET은 보호 되며 구성 변경을 허용 하지 않습니다. 피어 링 된 VNET은 고객에 의해 제어 되며 구독에 상주 합니다.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>클러스터가 고객 구독에 상주 합니까? 

Azure 관리 되는 응용 프로그램은 고객 구독과 함께 잠긴 리소스 그룹에 상주 합니다. 고객은 해당 RG에서 개체를 볼 수 있지만 수정할 수는 없습니다.

## <a name="is-the-sdn-module-configurable"></a>SDN 모듈을 구성할 수 있나요?

SDN은 openshift-ovs-networkpolicy 이며 구성할 수 없습니다.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>마스터/인프라/앱 노드에 사용할 수 있는 UNIX 권한 (IaaS)은 무엇 인가요?

이 제품에는 적용 되지 않습니다. 노드 액세스를 사용할 수 없습니다.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>어떤 OCP 권한이 있나요? 클러스터 관리자 인가요? 프로젝트 관리자 인가요?

자세한 내용은 Azure Red Hat OpenShift [클러스터 관리 개요](https://docs.openshift.com/aro/admin_guide/index.html)를 참조 하세요.

## <a name="which-kind-of-federation-with-ldap"></a>LDAP를 사용 하는 페더레이션 종류는 무엇 인가요?

Azure AD 통합을 통해이를 달성할 수 있습니다. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>다른 고객과 공유 하는 ARO 요소가 있나요? 또는 모두 독립적 입니까?

각 Azure Red Hat OpenShift 클러스터는 특정 고객을 전담 하며 고객의 구독 내에 있습니다. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>OCS와 같은 영구 저장소 솔루션을 선택할 수 있나요? 

Azure Disk 및 Azure File에서 두 개의 저장소 클래스를 선택할 수 있습니다.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>클러스터가 어떻게 업데이트 됩니까 (취약점으로 인해 majors 및 미성년자 미성년자 포함)?

[일반적인 업그레이드 프로세스는 무엇 인가요?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO에서 사용 하는 Azure 부하 분산 장치는 무엇 인가요?  Standard 또는 Basic 이며 구성 가능 한가요?

ARO는 표준 Azure Load Balancer를 사용 하며 구성할 수 없습니다.

## <a name="can-aro-use-netapp-based-storage"></a>ARO는 NetApp 기반 저장소를 사용할 수 있나요?

지금은 지원 되는 저장소 옵션만 Azure Disk 및 Azure File storage 클래스입니다. 


