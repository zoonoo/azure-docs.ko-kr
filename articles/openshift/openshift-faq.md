---
title: Azure Red Hat 오픈시프트에 대한 자주 묻는 질문
description: 다음은 Microsoft Azure Red Hat OpenShift에 대한 일반적인 질문에 대한 답변입니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619496"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure 레드햇 오픈시프트 자주 묻는 질문

이 문서에서는 Microsoft Azure Red Hat OpenShift에 대한 자주 묻는 질문(FAQ)을 다룹니다.

## <a name="which-azure-regions-are-supported"></a>어떤 Azure 지역이 지원되나요?

Azure Red Hat OpenShift가 지원되는 글로벌 리전 목록은 [지원되는 리소스를](supported-resources.md#azure-regions) 참조하십시오.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>기존 가상 네트워크에 클러스터를 배포할 수 있습니까?

아니요. 그러나 피어링을 통해 Azure Red Hat OpenShift 클러스터를 기존 VNET에 연결할 수 있습니다. 자세한 내용은 [클러스터의 가상 네트워크 연결을 기존 가상 네트워크에 연결합니다.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="what-cluster-operations-are-available"></a>어떤 클러스터 작업을 사용할 수 있습니까?

계산 노드 수만 확장하거나 축소할 수 있습니다. 생성 후 리소스에 `Microsoft.ContainerService/openShiftManagedClusters` 대한 다른 수정은 허용되지 않습니다. 최대 계산 노드 수는 20개로 제한됩니다.

## <a name="what-virtual-machine-sizes-can-i-use"></a>어떤 가상 컴퓨터 크기를 사용할 수 있습니까?

[Azure Red Hat OpenShift 클러스터에서](supported-resources.md#virtual-machine-sizes) 사용할 수 있는 가상 시스템 크기 목록은 Azure Red Hat OpenShift 가상 컴퓨터 크기를 참조하십시오.

## <a name="is-data-on-my-cluster-encrypted"></a>클러스터의 데이터가 암호화되어 있습니까?

기본적으로 미사용 암호화가 있습니다. Azure Storage 플랫폼은 데이터를 지속하기 전에 자동으로 암호화하고 검색하기 전에 데이터를 해독합니다. 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 서비스 암호화를](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 참조하십시오.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>프로메테우스/그라파나를 사용하여 응용 프로그램을 모니터링할 수 있습니까?

예. 네임스페이스에 Prometheus를 배포하고 네임스페이스에서 응용 프로그램을 모니터링할 수 있습니다.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Prometheus/Grafana를 사용하여 클러스터 상태 및 용량과 관련된 메트릭을 모니터링할 수 있습니까?

아니요, 현재 는 아닙니다.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker 레지스트리를 외부에서 사용할 수 있으므로 Jenkins와 같은 도구를 사용할 수 있습니까?

Docker 레지스트리는 그러나 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` 강력한 저장소 내구성 보장이 제공되지 않습니다. Azure 컨테이너 [레지스트리를](https://azure.microsoft.com/services/container-registry/)사용할 수도 있습니다.

## <a name="is-cross-namespace-networking-supported"></a>네임스페이스 간 네트워킹이 지원되고 있습니까?

고객 및 개별 프로젝트 관리자는 개체를 사용하여 `NetworkPolicy` 프로젝트별로 교차 네임스페이스 네트워킹(거부 포함)을 사용자 지정할 수 있습니다.

## <a name="can-an-admin-manage-users-and-quotas"></a>관리자가 사용자 및 할당량을 관리할 수 있습니까?

예. Azure Red Hat OpenShift 관리자는 사용자가 만든 모든 프로젝트에 액세스하는 것 외에도 사용자 및 할당량을 관리할 수 있습니다.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>클러스터를 특정 Azure AD 사용자로만 제한할 수 있습니까?

예. Azure AD 응용 프로그램을 구성하여 클러스터에 로그인할 수 있는 Azure AD 사용자를 제한할 수 있습니다. 자세한 내용은 [방법: 앱 사용자 집합으로 제한 참조](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>사용자가 프로젝트를 만들지 못하도록 제한할 수 있습니까?

예. Azure Red Hat OpenShift 관리자로 클러스터에 로그인하여 다음 명령을 실행합니다.

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

자세한 내용은 [자체 프로비저닝 비활성화에](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)대한 OpenShift 설명서를 참조하십시오.

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>클러스터에 여러 Azure 지역에 걸쳐 계산 노드가 있을 수 있습니까?

아니요. Azure Red Hat OpenShift 클러스터의 모든 노드는 동일한 Azure 리전에서 시작되어야 합니다.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>마스터 및 인프라 노드는 AZURE Kubernetes 서비스(AKS)와 마찬가지로 추상화되어 있습니까?

아니요. 클러스터 마스터를 포함한 모든 리소스는 고객 구독에서 실행됩니다. 이러한 유형의 리소스는 읽기 전용 리소스 그룹에 배치됩니다.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>AZURE(OSBA)에 대한 개방형 서비스 브로커가 지원됩니까?

예. Azure Red Hat 오픈시프트에서 OSBA를 사용할 수 있습니다. 자세한 내용은 [Azure에 대한 서비스 브로커 열기를](https://github.com/Azure/open-service-broker-azure#openshift-project-template) 참조하십시오.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>다른 구독에서 가상 네트워크를 피어려고하지만 오류가 `Failed to get vnet CIDR` 발생합니다.

가상 네트워크가 있는 구독에서 `Microsoft.ContainerService` 공급자를`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Azure Red Hat 오픈시프트(ARO) 유지 관리 프로세스는 무엇입니까?

ARO에 대한 유지 관리에는 업그레이드, etcd 데이터의 백업 및 복원, 클라우드 공급자가 시작한 유지 관리의 세 가지 유형이 있습니다.

+ 업그레이드에는 소프트웨어 업그레이드 및 CV가 포함됩니다. CVE 업데이트 적용은 실행 `yum update` 시 발생하며 즉각적인 완화를 제공합니다.  동시에 향후 클러스터 생성을 위해 새 이미지 빌드가 만들어집니다.

+ etcd 데이터의 백업 및 관리는 작업에 따라 클러스터 가동 중지 시간이 필요할 수 있는 자동화된 프로세스입니다. 백업에서 etcd 데이터베이스를 복원하는 경우 가동 중지 시간이 있습니다. 우리는 시간당 etcd를 백업하고 백업의 마지막 6 시간을 유지합니다.

+ 클라우드 공급자가 시작한 유지 관리에는 네트워크, 저장소 및 지역 정전이 포함됩니다. 유지 관리는 클라우드 공급자에 따라 달라지며 공급자가 제공한 업데이트에 의존합니다.

## <a name="what-is-the-general-upgrade-process"></a>일반적인 업그레이드 프로세스는 무엇입니까?

업그레이드를 실행하는 것은 안전한 프로세스여야 하며 클러스터 서비스를 중단해서는 안 됩니다. SRE는 새 버전을 사용할 수 있거나 CvE가 미해결인 경우 업그레이드 프로세스를 트리거할 수 있습니다.

사용 가능한 업데이트는 단계 환경에서 테스트한 다음 프로덕션 클러스터에 적용됩니다. 적용하면 새 노드가 일시적으로 추가되고 노드가 회전 방식으로 업데이트되어 포드가 복제본 수를 유지합니다. 모범 사례를 따르면 가동 중지 시간을 최소화하고 중단시간을 최소화할 수 있습니다.

보류 중인 업그레이드 또는 업데이트의 심각도에 따라 서비스가 CVE에 노출되는 것을 완화하기 위해 업데이트가 신속하게 적용될 수 있다는 점에서 프로세스가 다를 수 있습니다. 새 이미지는 비동기적으로 빌드되고 테스트되며 클러스터 업그레이드로 롤아웃됩니다. 그 외에는 비상 사태와 계획된 유지 보수 사이에 차이가 없습니다. 계획된 유지 관리는 고객과 미리 예약되지 않습니다.

고객과의 연락이 필요한 경우 ICM 및 이메일을 통해 알림을 보낼 수 있습니다.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>계획된 유지 보수 기간과 대비 비상 사태는 어떻습니까?

우리는 유지 보수의 두 가지 유형을 구별하지 않습니다. 우리 팀은 24/7/365를 사용할 수 있으며 기존의 "시간 외" 유지 관리 창을 사용하지 않습니다.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>호스트 운영 체제 및 OpenShift 소프트웨어는 어떻게 업데이트되나요?

호스트 운영 체제 및 OpenShift 소프트웨어는 일반적인 업그레이드 및 이미지 빌드 프로세스를 통해 업데이트됩니다.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>업데이트된 노드를 재부팅하는 프로세스는 무엇입니까?

업그레이드의 일부로 처리해야 합니다.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>ARO에서 etcd에 저장된 데이터가 암호화되어 있습니까?

그것은 etcd 수준에서 암호화되지 않습니다. 이 옵션을 켜는 옵션은 현재 지원되지 않습니다. OpenShift는 이 기능을 지원하지만 로드맵에서 이를 만들기 위해 엔지니어링 노력이 필요합니다. 데이터는 디스크 수준에서 암호화됩니다. 자세한 내용은 [데이터스토어 계층의 데이터 암호화를](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) 참조하십시오.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>기본 VM 로그를 고객 로그 분석 시스템으로 스트리밍할 수 있습니까?

Syslog, 도커 로그, 저널 및 dmesg는 관리되는 서비스에서 처리되며 고객에게 노출되지 않습니다.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>고객이 노드 수준에서 CPU/메모리와 같은 메트릭에 액세스하여 확장, 디버그 문제 등을 수행하기 위한 방법을 사용할 수 있습니다. ARO 클러스터에서 `kubectl top` 실행할 수 없습니다.

고객은 명령을 `oc adm top nodes` 사용하거나 `kubectl top nodes` 고객 관리자 클러스터롤을 사용하여 노드 수준에서 CPU/메모리 메트릭에 액세스할 수 있습니다.  고객은 명령또는 `pods` `oc adm top pods``kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO의 기본 포드 스케줄러 구성은 무엇입니까?

ARO는 OpenShift에서 제공되는 기본 스케줄러를 사용합니다. ARO에서 지원되지 않는 몇 가지 추가 메커니즘이 있습니다. 자세한 내용은 [기본 스케줄러 설명서](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) 및 [마스터 스케줄러 설명서를](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) 참조하십시오.

고급/사용자 지정 예약은 현재 지원되지 않습니다. 자세한 내용은 [예약 설명서를](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) 참조하십시오.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>배포를 확장하는 경우 Azure 오류 도메인이 포드 배치로 매핑되어 서비스의 모든 포드가 단일 오류 도메인의 오류로 인해 끊어지지 않도록 하려면 어떻게 해야 합니까?

Azure에서 가상 시스템 크기 집합을 사용할 때 기본적으로 5개의 오류 도메인이 있습니다. 스케일 세트의 각 가상 시스템 인스턴스는 이러한 장애 도메인 중 하나에 배치됩니다. 이렇게 하면 클러스터의 계산 노드에 배포된 응용 프로그램이 별도의 오류 도메인에 배치됩니다.

자세한 내용은 [가상 시스템 규모 집합에 대해 적절한 수의 오류 도메인 선택을](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) 참조하십시오.

## <a name="is-there-a-way-to-manage-pod-placement"></a>포드 배치를 관리하는 방법이 있습니까?

고객은 노드를 얻고 레이블을 고객 관리자로 볼 수 있습니다.  이렇게 하면 스케일 집합의 모든 VM을 대상으로 지정할 수 있습니다.

특정 레이블을 사용할 때는 주의해야 합니다.

- 호스트 이름을 사용할 수 없습니다. 호스트 이름은 업그레이드 및 업데이트와 함께 자주 회전되며 변경이 보장됩니다.

- 고객이 특정 레이블이나 배포 전략에 대한 요청이 있는 경우 이 작업을 수행할 수 있지만 엔지니어링 노력이 필요하며 현재는 지원되지 않습니다.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO 클러스터의 최대 포드 수는 무엇입니까?ARO의 노드당 최대 포드 수는 몇 개입니까?

 Azure Red Hat OpenShift 3.11에는 [ARO가 20계산 노드 제한을 갖는](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)노드당 50포드 제한이 있으므로 ARO 클러스터에서 지원되는 최대 포드 수를 50*20 = 1000으로 제한합니다.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>프라이빗 VNET에 배포할 IP 범위를 지정하여 피어를 한 번 피어로 처리한 다른 회사 VNET와의 충돌을 방지할 수 있습니까?

Azure Red Hat OpenShift는 VNET 피어링을 지원하며 고객이 OpenShift 네트워크가 작동하는 VNET 및 VNET CIDR을 제공할 수 있도록 합니다.

ARO에서 만든 VNET은 보호되며 구성 변경을 허용하지 않습니다. 피어인 VNET은 고객이 제어하고 구독에 상주합니다.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>클러스터가 고객 구독에 상주합니까? 

Azure 관리되는 응용 프로그램은 고객 구독이 있는 잠긴 리소스 그룹에 있습니다. 고객은 해당 RG에서 객체를 볼 수 있지만 수정할 수는 없습니다.

## <a name="is-the-sdn-module-configurable"></a>SDN 모듈을 구성할 수 있습니까?

SDN은 오픈 시프트-ovs-네트워크 정책이며 구성할 수 없습니다.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>마스터/인프라/앱 노드에서 사용할 수 있는 UNIX 권한(IaaS)은 무엇입니까?

이 오퍼링에는 적용되지 않습니다. 노드 액세스는 금지되어 있습니다.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>우리는 어떤 OCP 권리를 가지고 있습니까? 클러스터 관리자? 프로젝트 관리자?

자세한 내용은 Azure Red Hat OpenShift [클러스터 관리 개요를](https://docs.openshift.com/aro/admin_guide/index.html)참조하십시오.

## <a name="which-kind-of-federation-with-ldap"></a>LDAP와의 페더레이션은 무엇입니까?

이는 Azure AD 통합을 통해 달성됩니다. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>ARO에 다른 고객과 공유하는 요소가 있습니까? 아니면 모든 것이 독립적입니까?

각 Azure Red Hat OpenShift 클러스터는 지정된 고객 전용이며 고객의 구독 에 속합니다. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>OCS와 같은 영구 스토리지 솔루션을 선택할 수 있습니까? 

두 개의 저장소 클래스에서 선택할 수 있습니다.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>클러스터는 어떻게 업데이트됩니다(취약성으로 인한 전공 및 미성년자 포함)?

[일반 업그레이드 프로세스는 어떻게 됩니까?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO에서 사용하는 Azure 로드 밸러블러는 무엇입니까?표준 또는 기본이며 구성 할 수 있습니까?

ARO는 표준 Azure 로드 밸러커를 사용하며 구성할 수 없습니다.

## <a name="can-aro-use-netapp-based-storage"></a>ARO는 넷앱 기반 스토리지를 사용할 수 있습니까?

현재 지원되는 유일한 저장소 옵션은 Azure 디스크 및 Azure File 저장소 클래스입니다. 


