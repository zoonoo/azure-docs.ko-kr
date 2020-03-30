---
title: 클라우드에 의한 Azure VMware 솔루션 - 클라우드 단순 서비스를 위한 보안
description: CloudSimple 서비스의 보안에 대한 공동 책임 모델에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024996"
---
# <a name="cloudsimple-security-overview"></a>클라우드 단순 보안 개요

이 문서에서는 CloudSimple 서비스, 인프라 및 데이터 센터에 의한 Azure VMware 솔루션에서 보안이 구현되는 방법에 대한 개요를 제공합니다. 데이터 보호 및 보안, 네트워크 보안 및 취약점 및 패치 관리 방법에 대해 알아봅니다.

## <a name="shared-responsibility"></a>공동 책임

CloudSimple에 의한 Azure VMware 솔루션은 보안에 대한 공유 책임 모델을 사용합니다. 클라우드에서 신뢰할 수 있는 보안은 서비스 공급자로서 고객과 Microsoft의 공동 책임을 통해 달성됩니다. 이 책임 행렬은 더 높은 보안을 제공하고 단일 실패 지점을 제거합니다.

## <a name="azure-infrastructure"></a>Azure 인프라

Azure 인프라 보안 고려 사항에는 데이터 센터 및 장비 위치가 포함됩니다.

### <a name="datacenter-security"></a>데이터 센터 보안

Microsoft는 Azure를 지원하는 물리적 시설의 설계, 구축 및 운영에 전념하는 전체 부서를 보유하고 있습니다. 이 팀은 최첨단의 물리적 보안을 유지 관리하는 데 투입되었습니다. 물리적 보안에 대한 자세한 내용은 [Azure 시설, 구내 및 물리적 보안을](../security/azure-physical-security.md)참조하십시오.

### <a name="equipment-location"></a>장비 위치

프라이빗 클라우드를 실행하는 베어 메탈 하드웨어 장비는 Azure 데이터 센터 위치에서 호스팅됩니다.  해당 장비가 있는 케이지에는 액세스 권한을 얻으려면 생체 인식 기반 이중 인증이 필요합니다.

## <a name="dedicated-hardware"></a>전용 하드웨어

CloudSimple 서비스의 일환으로 모든 CloudSimple 고객은 다른 테넌트 하드웨어와 물리적으로 격리된 로컬 연결된 디스크가 있는 전용 베어 메탈 호스트를 얻습니다. vSAN이 있는 ESXi 하이퍼바이저는 모든 노드에서 실행됩니다. 노드는 고객 전용 VMware vCenter 및 NSX를 통해 관리됩니다. 테넌자 간에 하드웨어를 공유하지 않는 것은 격리 및 보안 보호의 추가 계층을 제공합니다.

## <a name="data-security"></a>데이터 보안

고객은 데이터를 제어하고 소유합니다. 고객 데이터의 데이터 관리는 고객의 책임입니다.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>미사용 데이터에 대한 데이터 보호 및 내부 네트워크 내 이동 중인 데이터

프라이빗 클라우드 환경에서 미사용 데이터의 경우 vSAN 암호화를 사용할 수 있습니다. vSAN 암호화는 자체 가상 네트워크 또는 온-프레미스에서 VMware 인증 외부 키 관리 서버(KMS)와 함께 작동합니다.  데이터 암호화 키를 직접 제어할 수 있습니다. 프라이빗 클라우드 내에서 이동 중인 데이터의 경우 vSphere는 모든 vmkernel 트래픽(vMotion 트래픽 포함)에 대해 와이어를 통해 데이터 암호화를 지원합니다.

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>공용 네트워크를 통해 이동하는 데 필요한 데이터에 대한 데이터 보호

공용 네트워크를 통해 이동하는 데이터를 보호하려면 프라이빗 클라우드용 IPsec 및 SSL VPN 터널을 만들 수 있습니다. 128바이트 및 256바이트 AES를 포함한 일반적인 암호화 방법이 지원됩니다. 전송 중 데이터(인증, 관리 액세스 및 고객 데이터 포함)는 표준 암호화 메커니즘(SSH, TLS 1.2 및 보안 RDP)으로 암호화됩니다. 중요한 정보를 전송하는 통신은 표준 암호화 메커니즘을 사용합니다.

### <a name="secure-disposal"></a>안전한 폐기

CloudSimple 서비스가 만료되거나 종료되는 경우 귀하는 귀하의 데이터를 제거하거나 삭제할 책임이 있습니다. CloudSimple은 관련 법률에서 개인 데이터의 일부 또는 전부를 보유하는 데 필요한 경우를 제외하고 고객 계약에 명시된 모든 고객 데이터를 삭제하거나 반환하기 위해 귀하와 협력할 것입니다. 개인 데이터를 보유하는 데 필요한 경우 CloudSimple은 데이터를 보관하고 고객 데이터가 추가 처리되지 않도록 합당한 조치를 이행합니다.

### <a name="data-location"></a>데이터 위치

프라이빗 클라우드를 설정할 때 배포할 Azure 지역을 선택합니다. VMware 가상 컴퓨터 데이터는 데이터 마이그레이션 또는 오프사이트 데이터 백업을 수행하지 않는 한 해당 물리적 데이터 센터에서 이동되지 않습니다. 필요에 따라 적절한 경우 워크로드를 호스트하고 여러 Azure 리전 내에 데이터를 저장할 수도 있습니다.

프라이빗 클라우드 하이퍼 컨버지드 노드에 있는 고객 데이터는 테넌트 관리자의 명시적 조치 없이 위치를 트래버스하지 않습니다. 고가용성 방식으로 워크로드를 구현하는 것은 사용자의 책임입니다.

### <a name="data-backups"></a>데이터 백업

CloudSimple은 고객 데이터를 백업하거나 보관하지 않습니다. CloudSimple은 관리 서버의 고가용성을 제공하기 위해 vCenter 및 NSX 데이터의 정기적인 백업을 수행합니다. 백업하기 전에 모든 데이터는 VMware API를 사용하여 vCenter 소스에서 암호화됩니다. 암호화된 데이터는 Azure Blob에 전송되고 저장됩니다. 백업용 암호화 키는 Azure의 CloudSimple 가상 네트워크에서 실행되는 매우 안전한 CloudSimple 관리형 자격 증명 모음에 저장됩니다.

## <a name="network-security"></a>네트워크 보안

CloudSimple 솔루션은 네트워크 보안 계층에 의존합니다.

### <a name="azure-edge-security"></a>Azure 에지 보안

CloudSimple 서비스는 Azure에서 제공하는 기본 네트워크 보안을 기반으로 구축됩니다. Azure는 비정상적인 송신 또는 송신 트래픽 패턴 및 DDoS(분산 서비스 거부) 공격과 관련된 네트워크 기반 공격에 대한 탐지 및 적시 대응을 위한 심층 방어 기술을 적용합니다. 이 보안 제어는 프라이빗 클라우드 환경 및 CloudSimple에서 개발한 제어 평면 소프트웨어에 적용됩니다.

### <a name="segmentation"></a>분할

CloudSimple 서비스에는 사설 클라우드 환경에서 사용자 고유의 개인 네트워크에 대한 액세스를 제한하는 논리적으로 별도의 계층 2 네트워크가 있습니다. 방화벽을 사용하여 프라이빗 클라우드 네트워크를 추가로 보호할 수 있습니다. CloudSimple 포털을 사용하면 Intra Private Cloud 트래픽, 사설 클라우드 간 트래픽, 인터넷에 대한 일반 트래픽 및 IPsec VPN을 통해 온-프레미스로의 네트워크 트래픽을 포함한 모든 네트워크 트래픽에 대한 EW 및 NS 네트워크 트래픽 제어 규칙을 정의할 수 있습니다. 익스프레스루트 연결.

## <a name="vulnerability-and-patch-management"></a>취약점 및 패치 관리

CloudSimple은 관리되는 VMware 소프트웨어(ESXi, vCenter 및 NSX)의 정기적인 보안 패치를 담당합니다.

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

고객은 다단계 인증 또는 SSO를 선호하는 대로 사용하여 Azure 계정(Azure AD)에 인증할 수 있습니다. Azure 포털에서 자격 증명을 다시 입력하지 않고 CloudSimple 포털을 시작할 수 있습니다.

CloudSimple은 프라이빗 클라우드 vCenter에 대한 ID 원본의 선택적 구성을 지원합니다. [온-프레미스 ID 원본,](set-vcenter-identity.md)사설 클라우드의 새 ID 원본 또는 [Azure AD를](azure-ad.md)사용할 수 있습니다.

기본적으로 고객에게는 프라이빗 클라우드 내의 vCenter의 일상적인 작업에 필요한 권한이 부여됩니다. 이 권한 수준에는 vCenter에 대한 관리 액세스가 포함되지 않습니다. 관리 액세스가 일시적으로 필요한 경우 관리 작업을 완료하는 동안 제한된 기간 동안 [권한을 에스컬레이션할](escalate-private-cloud-privileges.md) 수 있습니다.
