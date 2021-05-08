---
title: Azure Red Hat OpenShift 책임 할당 매트릭스
description: Azure Red Hat OpenShift 클러스터 작업에 대한 책임 소유권에 대해 알아봅니다.
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, 지원
ms.openlocfilehash: 477fe40dc565bf5e20627b4f4cd0d53273240ec3
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108319614"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대한 책임 개요

이 문서에서는 Azure Red Hat OpenShift 클러스터에 대한 Microsoft, Red Hat 및 고객의 책임에 대해 설명합니다. Azure Red Hat OpenShift 및 해당 구성 요소에 대한 자세한 내용은 Azure Red Hat OpenShift 서비스 정의를 참조하세요.

Microsoft 및 Red Hat이 Azure Red Hat OpenShift 서비스를 관리하는 동안 고객은 클러스터의 기능에 대한 책임을 공유합니다. Azure Red Hat OpenShift 클러스터는 고객 Azure 구독의 Azure 리소스에서 호스트되지만 원격으로 액세스됩니다. 기본 플랫폼 및 데이터 보안은 Microsoft 및 Red Hat에서 소유합니다.

## <a name="overview"></a>개요
<table>
  <tr>
   <td><strong>리소스</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">인시던트 및 작업 관리 </a></strong>
   </td>
   <td><strong><a href="#change-management">변경 관리</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">ID 및 액세스 관리</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">보안 및 규정 준수</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">고객 데이터</a>
   </td>
   <td>고객 </td>
   <td>고객 </td>
   <td>고객 </td>
   <td>고객 </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">고객 애플리케이션</a>
   </td>
   <td>고객 </td>
   <td>고객 </td>
   <td>고객 </td>
   <td>고객 </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">개발자 서비스</a>
   </td>
   <td>고객 </td>
   <td>고객 </td>
   <td>고객 </td>
   <td>고객 </td>
  </tr>
  <tr>
   <td>플랫폼 모니터링 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>로깅 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
  </tr>
  <tr>
   <td>애플리케이션 네트워킹 </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>클러스터 네트워킹 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>가상 네트워킹 </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
   <td>공유됨 </td>
  </tr>
  <tr>
   <td>컨트롤 플레인 노드 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>작업자 노드 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>클러스터 버전 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>공유됨 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>용량 관리 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>공유됨 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>가상 스토리지 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
  <tr>
   <td>물리적 인프라 및 보안 </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
   <td>Microsoft 및 Red Hat </td>
  </tr>
</table>


표 1. 리소스의 책임


## <a name="tasks-for-shared-responsibilities-by-area"></a>영역별 공유 책임에 대한 작업 

### <a name="incident-and-operations-management"></a>인시던트 및 작업 관리 

고객과 Microsoft 및 Red Hat은 Azure Red Hat OpenShift 클러스터의 모니터링 및 유지 관리에 대한 책임을 공유합니다. 고객은 [고객 애플리케이션 데이터](#customer-data-and-applications) 및 고객이 구성했을 수 있는 모든 사용자 지정 네트워킹에 대한 인시던트 및 작업 관리를 담당합니다.

<table>
  <tr>
   <td><strong>리소스</strong>
   </td>
   <td><strong>Microsoft 및 Red Hat 책임</strong>
   </td>
   <td><strong>고객 책임</strong>
   </td>
  </tr>
  <tr>
   <td>애플리케이션 네트워킹 </td>
   <td>
<ul>

<li>클라우드 부하 분산 장치 및 기본 OpenShift 라우터 서비스를 모니터링하고 경고에 응답합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>서비스 부하 분산 장치 엔드포인트의 상태를 모니터링합니다.

<li>애플리케이션 경로 및 그 뒤의 엔드포인트의 상태를 모니터링합니다.

<li>Microsoft 및 Red Hat에 대한 중단을 보고합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>가상 네트워킹
   </td>
   <td>
<ul>

<li>기본 플랫폼 네트워킹에 필요한 클라우드 부하 분산 장치, 서브넷 및 Azure 클라우드 구성 요소를 모니터링하고 경고에 응답합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>잠재적 문제나 보안 위협에 대해 VNet 간 연결, VPN 연결 또는 Private Link 연결을 통해 선택적으로 구성된 네트워크 트래픽을 모니터링합니다.
</li>
</ul>
   </td>
  </tr>
</table>


표 2. 인시던트 및 운영 관리를 위한 공유 책임


### <a name="change-management"></a>변경 관리

Microsoft 및 Red Hat은 고객이 제어할 클러스터 인프라 및 서비스를 변경하고 마스터 노드, 인프라 서비스 및 작업자 노드에 사용할 수 있는 버전을 유지 관리하는 일을 담당합니다. 고객은 인프라 변경을 시작하고, 클러스터에 선택적 서비스 및 네트워킹 구성을 설치 및 유지 관리하고, 고객 데이터 및 고객 애플리케이션에 대한 모든 변경 작업을 담당합니다.


<table>
  <tr>
   <td><strong>리소스</strong>
   </td>
   <td><strong>Microsoft 및 Red Hat 책임</strong>
   </td>
   <td><strong>고객 책임</strong>
   </td>
  </tr>
  <tr>
   <td>로깅 </td>
   <td>
<ul>

<li>플랫폼 감사 로그를 중앙에서 집계하고 모니터링합니다.

<li>컨테이너용 Azure Monitor를 통해 Log Analytics를 사용하여 애플리케이션 로깅을 사용하도록 설명서를 제공합니다.

<li>고객의 요청에 따라 감사 로그를 제공합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>클러스터에 선택적 기본 애플리케이션 로깅 운영자를 설치합니다.

<li>로깅 사이드카 컨테이너 또는 타사 로깅 애플리케이션과 같은 선택적 앱 로깅 솔루션을 설치, 구성 및 유지 관리합니다.

<li>클러스터의 안정성에 영향을 주는 경우 고객 애플리케이션에 의해 생성되는 애플리케이션 로그의 크기와 빈도를 조정합니다.

<li>특정 인시던트를 조사하기 위해 지원 사례를 통해 플랫폼 감사 로그를 요청합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>애플리케이션 네트워킹
   </td>
   <td>
<ul>

<li>퍼블릭 클라우드 부하 분산 장치 설정

<li>기본 OpenShift 라우터 서비스를 설정합니다. 라우터를 프라이빗으로 설정하고 최대 하나의 라우터 분할된 데이터베이스를 더 추가하는 기능을 제공합니다.

<li>기본 내부 pod 트래픽에 대해 OpenShift SDN 구성 요소를 설치, 구성 및 유지 관리합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>NetworkPolicy 개체를 사용하여 프로젝트 및 pod 네트워크, pod 수신 및 pod 송신에 대한 기본이 아닌 pod 네트워크 권한을 구성합니다.

<li>특정 서비스에 대한 추가 서비스 부하 분산 장치를 요청하고 구성합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>클러스터 네트워킹
   </td>
   <td>
<ul>

<li>공용 또는 프라이빗 서비스 엔드포인트와 같은 클러스터 관리 구성 요소 및 가상 네트워킹 구성 요소와의 필수 통합을 설정합니다.

<li>작업자와 마스터 노드 간의 내부 클러스터 통신에 필요한 내부 네트워킹 구성 요소를 설정합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>클러스터가 프로비저닝될 때 OpenShift Cluster Manager를 통해 필요한 경우 컴퓨터 CIDR, 서비스 CIDR 및 pod CIDR에 대해 기본이 아닌 선택적 IP 주소 범위를 제공합니다.

<li>클러스터를 만들 때 또는 Azure CLI를 통해 클러스터를 만든 후에 API 서비스 엔드포인트를 공용 또는 프라이빗으로 만들도록 요청합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>가상 네트워킹
   </td>
   <td>
<ul>

<li>가상 프라이빗 클라우드, 서브넷, 부하 분산 장치, 인터넷 게이트웨이, NAT 게이트웨이 등을 포함하여 클러스터를 프로비저닝하는 데 필요한 가상 네트워킹 구성 요소를 설정 및 구성합니다.

<li>OpenShift Cluster Manager를 통해 필요에 따라 온-프레미스 리소스, VNet 간 연결 및 Private Link 연결을 사용하여 VPN 연결을 관리할 수 있는 기능을 고객에게 제공합니다.

<li>고객이 서비스 부하 분산 장치에 사용할 퍼블릭 클라우드 부하 분산 장치를 만들고 배포할 수 있습니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>VNet 간 연결, VPN 연결, Private Link 연결 등의 선택적 퍼블릭 클라우드 네트워킹 구성 요소를 설정하고 유지 관리합니다.

<li>특정 서비스에 대한 추가 서비스 부하 분산 장치를 요청하고 구성합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>클러스터 버전
   </td>
   <td>
<ul>

<li>부 버전 및 유지 관리 버전에 대한 업그레이드의 일정 및 상태를 전달합니다.

<li>부 업그레이드 및 유지 관리 업그레이드에 대한 changelogs 및 릴리스 정보 게시
</li>
</ul>
   </td>
   <td>
<ul>

<li>클러스터 업그레이드 시작

<li>호환성을 보장하기 위해 부 버전 및 유지 관리 버전에서 고객 애플리케이션 테스트
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>용량 관리
   </td>
   <td>
<ul>

<li>컨트롤 플레인(마스터 노드)의 사용률 모니터링

<li>서비스 품질을 유지 관리하기 위해 컨트롤 플레인 노드 확장 및/또는 크기 조정

<li>네트워크, 스토리지 및 컴퓨팅 용량을 비롯한 고객 리소스의 사용률을 모니터링합니다. 자동 크기 조정 기능이 사용하도록 설정되지 않은 경우 클러스터 리소스에 필요한 변경 사항(예: 크기 조정할 새 컴퓨팅 노드, 추가 스토리지 등)에 대해 고객에게 경고합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>제공된 OpenShift Cluster Manager 제어를 사용하여 필요에 따라 추가 작업자 노드를 추가하거나 제거합니다.

<li>클러스터 리소스 요구 사항과 관련하여 Microsoft 및 Red Hat 알림에 응답합니다.
</li>
</ul>
   </td>
  </tr>
</table>


표 3. 변경 관리에 대한 공유 책임


### <a name="identity-and-access-management"></a>ID 및 액세스 관리 

ID 및 액세스 관리에는 적절한 개인만 클러스터, 애플리케이션 및 인프라 리소스에 액세스할 수 있도록 보장하는 모든 책임이 포함됩니다. 여기에는 액세스 제어 메커니즘, 인증, 권한 부여 제공 및 리소스에 대한 액세스 관리 등의 작업이 포함됩니다.


<table>
  <tr>
   <td><strong>리소스</strong>
   </td>
   <td><strong>Microsoft 및 Red Hat 책임</strong>
   </td>
   <td><strong>고객 책임</strong>
   </td>
  </tr>
  <tr>
   <td>로깅 </td>
   <td>
<ul>

<li>플랫폼 감사 로그에 대한 업계 표준 기반의 계층화된 내부 액세스 프로세스를 준수합니다.

<li>기본 OpenShift RBAC 기능을 제공합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>프로젝트에 대한 액세스 및 프로젝트의 애플리케이션 로그 확장 기준을 제어하도록 OpenShift RBAC를 구성합니다.

<li>타사 또는 사용자 지정 애플리케이션 로깅 솔루션의 경우 고객은 액세스 관리를 담당합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>애플리케이션 네트워킹
   </td>
   <td>
<ul>

<li>기본 OpenShift RBAC 기능을 제공합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>필요에 따라 경로 구성에 대한 액세스를 제어하도록 OpenShift RBAC를 구성합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>클러스터 네트워킹
   </td>
   <td>
<ul>

<li>기본 OpenShift RBAC 기능을 제공합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Red hat 계정의 Red Hat 조직 구성원을 관리합니다.

<li>Red Hat 조직의 조직 관리자를 관리하여 OpenShift Cluster Manager에 대한 액세스 권한을 부여합니다.

<li>필요에 따라 경로 구성에 대한 액세스를 제어하도록 OpenShift RBAC를 구성합니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>가상 네트워킹
   </td>
   <td>
<ul>

<li>OpenShift Cluster Manager를 통해 고객 액세스 제어를 제공합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>OpenShift Cluster Manager를 통해 퍼블릭 클라우드 구성 요소에 대한 선택적 사용자 액세스를 관리합니다.
</li>
</ul>
   </td>
  </tr>
</table>


표 4. ID 및 액세스 관리에 대한 공동 책임


### <a name="security-and-regulation-compliance"></a>보안 및 규정 준수 

보안 및 규정 준수에는 관련 법률, 정책 및 규정 준수를 보장하는 모든 책임과 제어가 포함됩니다.


<table>
  <tr>
   <td><strong>리소스</strong>
   </td>
   <td><strong>Microsoft 및 Red Hat 책임</strong>
   </td>
   <td><strong>고객 책임</strong>
   </td>
  </tr>
  <tr>
   <td>로깅 </td>
   <td>
<ul>

<li>Microsoft 및 Red Hat SIEM에 클러스터 감사 로그를 보내 보안 이벤트를 분석합니다. 법정 분석을 지원하기 위해 정의된 기간 동안 감사 로그를 유지합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>보안 이벤트에 대한 애플리케이션 로그를 분석합니다. 기본 로깅 스택에 의해 제공되는 것보다 더 긴 보존이 필요한 경우 사이드카 컨테이너 또는 타사 로깅 애플리케이션을 통해 애플리케이션 로그를 외부 엔드포인트로 보냅니다.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>가상 네트워킹
   </td>
   <td>
<ul>

<li>잠재적인 문제 및 보안 위협에 대한 가상 네트워킹 구성 요소를 모니터링합니다.

<li>추가 모니터링 및 보호를 위해 추가 공용 Microsoft 및 Red Hat Azure 도구를 사용합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>잠재적 문제 및 보안 위협에 대한 선택적으로 구성된 가상 네트워킹 구성 요소를 모니터링합니다.

<li>필요에 따라 필수 방화벽 규칙 또는 데이터 센터 보호를 구성합니다.
</li>
</ul>
   </td>
  </tr>
</table>


표 5. 보안 및 규정 준수에 대한 공유 책임


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Azure Red Hat OpenShift를 사용하는 경우 고객 책임 


### <a name="customer-data-and-applications"></a>고객 데이터 및 애플리케이션

고객은 Azure Red Hat OpenShift에 배포하는 애플리케이션, 워크로드 및 데이터를 담당합니다. 그러나 Microsoft 및 Red Hat은 고객이 플랫폼에서 데이터 및 애플리케이션을 관리하는 데 도움이 되는 다양한 도구를 제공합니다.


<table>
  <tr>
   <td><strong>리소스</strong>
   </td>
   <td><strong>Microsoft와 Red Hat이 돕는 방법</strong>
   </td>
   <td><strong>고객 책임</strong>
   </td>
  </tr>
  <tr>
   <td>고객 데이터 </td>
   <td>
<ul>

<li>업계 보안 및 규정 준수 표준에 정의된 대로 데이터 암호화에 대한 플랫폼 수준 표준을 유지 관리합니다. 

<li>비밀 같은 애플리케이션 데이터를 관리하는 데 도움이 되는 OpenShift 구성 요소를 제공합니다.

<li>Azure SQL과 같은 타사 데이터 서비스와의 통합을 사용하여 클러스터 및/또는 Microsoft 및 Red Hat Azure 외부의 데이터를 저장하고 관리할 수 있습니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>플랫폼에 저장된 모든 고객 데이터와 고객 애플리케이션이 이 데이터를 소비하고 노출하는 방식에 대한 책임을 유지 관리합니다.

<li>Etcd 암호화
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>고객 애플리케이션
   </td>
   <td>
<ul>

<li>OpenShift 구성 요소가 설치된 클러스터를 프로비저닝하면 고객이 OpenShift 및 Kubernetes API에 액세스하여 컨테이너화된 애플리케이션을 배포하고 유지 관리할 수 있습니다.

<li>고객이 커뮤니티, 타사, Microsoft 및 Red Hat, Red Hat 서비스를 클러스터에 추가하기 위해 운영자를 설정하는 데 사용할 수 있는 OpenShift API에 대한 액세스를 제공합니다. 

<li>고객 애플리케이션과 함께 사용할 영구 볼륨을 지원하는 스토리지 클래스 및 플러그인을 제공합니다.
</li>
</ul>
   </td>
   <td>
<ul>

<li>고객 및 타사 애플리케이션, 데이터 및 전체 수명 주기에 대한 책임을 유지합니다.

<li>고객이 운영자 또는 외부 이미지를 사용하여 Red Hat, 커뮤니티, 타사, 자체 또는 기타 서비스를 클러스터에 추가하는 경우 고객은 이러한 서비스에 대한 책임이 있으며 문제 해결을 위해 적절한 공급자(Red Hat 포함)를 사용합니다.

<li>제공된 도구 및 기능을 사용하여 <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">구성 및 배포</a>, <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">최신 상태 유지</a>, <a href="https://docs.openshift.com/dedicated/4/applications/working-with-quotas.html"> 리소스 요청 및 제한 설정 </a>, <a href="https://docs.openshift.com/dedicated/4/getting_started/scaling-your-cluster.html">앱을 실행하는 데 충분한 리소스를 갖도록 클러스터 크기 조정</a>, <a href="https://docs.openshift.com/dedicated/4/administering_a_cluster/cluster-admin-role.html">권한 설정</a>, 다른 서비스와 통합, <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">고객이 배포하는 모든 이미지 스트림 또는 템플릿 관리</a>, <a href="https://docs.openshift.com/dedicated/4/cloud_infrastructure_access/dedicated-understanding-aws.html">외부 서비스</a>, 데이터 저장, 백업 및 복원, 고가용성 및 복원력 있는 워크로드 관리 작업을 수행할 수 있습니다.

<li>메트릭을 수집하고 경고를 만드는 소프트웨어 설치 및 운영을 포함하여 Azure Red Hat OpenShift에서 실행되는 애플리케이션 모니터링에 대한 책임을 유지 관리합니다.
</li>
</ul>
   </td>
  </tr>
</table>


표 7. 고객 데이터, 고객 애플리케이션 및 서비스에 대한 고객 책임
