---
title: Vnet에서 Azure 스프링 클라우드를 실행 하는 고객 책임
description: 이 문서에서는 vnet에서 Azure 스프링 클라우드를 실행 하는 고객의 책임을 설명 합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0c73d0394486472c2c3c92450aab6a1a0d329cf7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698215"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>VNET에서 Azure 스프링 클라우드를 실행 하는 고객 책임
이 문서에는 가상 네트워크에서 Azure 스프링 클라우드를 사용 하기 위한 사양이 포함 되어 있습니다.

Azure 스프링 클라우드를 가상 네트워크에 배포 하는 경우 가상 네트워크 외부의 서비스에 대 한 아웃 바운드 종속성이 있습니다. 관리 및 운영 목적을 위해 Azure 스프링 클라우드는 특정 포트 및 Fqdn (정규화 된 도메인 이름)에 액세스 해야 합니다. 이러한 끝점은 Azure 스프링 클라우드 관리 평면과 통신 하 고 핵심 Kubernetes 클러스터 구성 요소 및 보안 업데이트를 다운로드 하 고 설치 하는 데 필요 합니다.

기본적으로 Azure 스프링 클라우드는 무제한 아웃 바운드 (송신) 인터넷 액세스를 포함 합니다. 이러한 수준의 네트워크 액세스를 통해 실행 하는 응용 프로그램은 필요에 따라 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한 하려는 경우 유지 관리 작업을 위해 제한 된 수의 포트와 주소를 액세스할 수 있어야 합니다. 아웃 바운드 주소를 안전 하 게 보호 하는 가장 간단한 방법은 도메인 이름을 기반으로 아웃 바운드 트래픽을 제어할 수 있는 방화벽 장치를 사용 하는 것입니다. 예를 들어 Azure 방화벽은 대상의 FQDN에 따라 아웃 바운드 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다. 이러한 필수 포트와 주소를 허용 하도록 기본 방화벽 및 보안 규칙을 구성할 수도 있습니다.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure 스프링 클라우드 리소스 요구 사항 

다음은 Azure 스프링 클라우드 서비스에 대 한 리소스 요구 사항 목록입니다. 일반적인 요구 사항에 따라 Azure 스프링 클라우드와 기본 네트워크 리소스에서 만든 리소스 그룹을 수정 하면 안 됩니다.
- Azure 스프링 클라우드에서 만들고 소유 하 고 있는 리소스 그룹을 수정 하지 마세요.
  - 기본적으로 이러한 리소스 그룹의 이름은 ap svc-rt_ [서비스-인스턴스 이름]_[지역] * 및 ap_[서비스-인스턴스-이름] _ [지역] *입니다.
- Azure 스프링 클라우드에서 사용 하는 서브넷을 수정 하지 마세요.
- 동일한 서브넷에 둘 이상의 Azure 스프링 클라우드 서비스 인스턴스를 만들지 마세요.
- 방화벽을 사용 하 여 트래픽을 제어 하는 경우 서비스 인스턴스를 운영, 유지 관리 및 지 원하는 Azure 스프링 클라우드 구성 요소에 대 한 다음 송신 트래픽을 차단 *하지 마세요* .

## <a name="azure-spring-cloud-network-requirements"></a>Azure 스프링 클라우드 네트워크 요구 사항

  | 대상 끝점 | 포트 | 사용 | 참고 |
  |------|------|------|
  | *: 1194 *또는* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -azurecloud: 1194 | UDP: 1194 | 기본 Kubernetes 클러스터 관리. | |
  | *: 443 *또는* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -azurecloud: 443 | TCP: 443 | Azure 스프링 클라우드 서비스 관리. | "NetworkProfile" 섹션의 리소스 페이로드에 서비스 인스턴스 "requiredTraffics"의 정보를 알 수 있습니다. |
  | *: 9000 *또는* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -azurecloud: 9000 | TCP: 9000 | 기본 Kubernetes 클러스터 관리. |
  | *: 123 *또는* ntp.ubuntu.com:123 | UDP:123 | Linux 노드에서 NTP 시간 동기화. | |
  | *. azure.io:443 *또는* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry입니다. |  [가상 네트워크에서 Azure Container Registry 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)을 사용 하도록 설정 하 여 바꿀 수 있습니다. |
  | *. core.windows.net:443 및 *. core.windows.net:445 *또는* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -저장소: 443 및 저장소: 445 | TCP: 443, TCP: 445 | Azure File Storage |  [가상 네트워크에서 Azure Storage 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)을 사용 하도록 설정 하 여 바꿀 수 있습니다. |
  | *. servicebus.windows.net:443 *또는* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Azure 이벤트 허브. | [가상 네트워크에서](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Event Hubs* 서비스 끝점을 사용 하도록 설정 하 여 바꿀 수 있습니다. |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Azure 스프링 클라우드 FQDN 요구 사항/응용 프로그램 규칙

Azure 방화벽은 정규화 된 도메인 이름 (FQDN) 태그 **AzureKubernetesService** 을 제공 하 여 다음 구성을 간소화 합니다.

  | 대상 FQDN | 포트 | 사용 |
  |------|------|------|
  | *. azmk8s.io | HTTPS:443 | 기본 Kubernetes 클러스터 관리. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | MCR (Microsoft Container Registry). |
  | *.cdn.mscr.io | HTTPS:443 | Azure CDN에 의해 지원 되는 MCR 저장소입니다. |
  | *.data.mcr.microsoft.com | HTTPS:443 | Azure CDN에 의해 지원 되는 MCR 저장소입니다. |
  | <i>management.azure.com</i> | HTTPS:443 | 기본 Kubernetes 클러스터 관리. |
  | <i>login.microsoftonline.com</i> | HTTPS:443 | 인증을 Azure Active Directory 합니다. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Microsoft 패키지 리포지토리. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치 하는 데 필요한 리포지토리입니다. |
  | *mscrl.microsoft.com* | HTTPS: 80 | 필수 Microsoft 인증서 체인 경로입니다. |
  | *crl.microsoft.com* | HTTPS: 80 | 필수 Microsoft 인증서 체인 경로입니다. |
  | *crl3.digicert.com* | HTTPS: 80 | 타사 SSL 인증서 체인 경로입니다. |

## <a name="see-also"></a>참고 항목
* [개인 네트워크에서 애플리케이션에 액세스](spring-cloud-access-app-virtual-network.md)
* [Application Gateway 및 Azure 방화벽을 사용 하 여 앱 노출](spring-cloud-expose-apps-gateway-azure-firewall.md)