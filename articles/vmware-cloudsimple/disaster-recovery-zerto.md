---
title: CloudSimple 별 Azure VMware 솔루션-사설 클라우드를 온-프레미스 워크 로드에 대 한 재해 사이트로 사용
description: CloudSimple 사설 클라우드를 온-프레미스 VMware 워크 로드에 대 한 재해 복구 사이트로 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77083141"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>클라우드를 온-프레미스 VMware 워크 로드에 대 한 재해 복구 사이트로 설정

클라우드를 재해 발생 시 비즈니스 연속성을 제공 하기 위해 온-프레미스 응용 프로그램에 대 한 복구 사이트로 CloudSimple 사설 클라우드를 설정할 수 있습니다. 복구 솔루션은 Zerto 가상 복제를 복제 및 오케스트레이션 플랫폼으로 기반으로 합니다. 중요 한 인프라 및 응용 프로그램 가상 머신은 온-프레미스 vCenter에서 사설 클라우드로 지속적으로 복제할 수 있습니다. 장애 조치 (failover) 테스트에 사설 클라우드를 사용 하 고 재해 발생 시 응용 프로그램의 가용성을 보장할 수 있습니다. 다른 위치의 복구 사이트로 보호 되는 기본 사이트로 사설 클라우드를 설정 하는 것과 유사한 접근 방식을 사용할 수 있습니다.

> [!NOTE]
> 재해 복구 환경의 크기를 조정 하는 방법에 대 한 지침은 [Zerto 가상 복제에 대 한 Zerto 문서 크기 조정 고려 사항](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) 을 참조 하세요.

CloudSimple 솔루션:

* DR (재해 복구)을 위해 특정 데이터 센터를 설정 하지 않아도 됩니다.
* 전 세계 지리적 복원 력을 위해 CloudSimple이 배포 되는 Azure 위치를 활용할 수 있습니다.
* 에서는 배포 비용과 DR에 대 한 총 소유 비용을 줄일 수 있는 옵션을 제공 합니다.

이 솔루션을 사용 하려면 다음을 수행 해야 합니다.

* 사설 클라우드에서 Zerto를 설치, 구성 및 관리 합니다.
* 사설 클라우드가 보호 된 사이트인 경우 Zerto에 대 한 고유한 라이선스를 제공 합니다. CloudSimple 사이트에서 실행 되는 Zerto를 라이선스에 대 한 온-프레미스 사이트와 쌍으로 연결할 수 있습니다.

다음 그림은 Zerto 솔루션의 아키텍처를 보여 줍니다.

![Architecture](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>솔루션을 배포 하는 방법

다음 섹션에서는 사설 클라우드에서 Zerto 가상 복제를 사용 하 여 DR 솔루션을 배포 하는 방법을 설명 합니다.

1. [전제 조건](#prerequisites)
2. [CloudSimple 사설 클라우드의 선택적 구성](#optional-configuration-on-your-private-cloud)
3. [CloudSimple 사설 클라우드에서 ZVM 및 VRA 설정](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Zerto 가상 보호 그룹 설정](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>사전 요구 사항

온-프레미스 환경에서 사설 클라우드로의 Zerto 가상 복제를 사용 하도록 설정 하려면 다음 필수 구성 요소를 완료 합니다.

1. [온-프레미스 네트워크와 CloudSimple 사설 클라우드 간에 사이트 간 VPN 연결을 설정](set-up-vpn.md)합니다.
2. 사설 클라우드 [관리 구성 요소가 사설 클라우드 DNS 서버로 전달 되도록 DNS 조회를 설정](on-premises-dns-setup.md)합니다.  DNS 조회 전달 기능을 사용 하려면에 대 한 온-프레미스 DNS 서버에서 CloudSimple DNS 서버로 전달 영역 항목을 만듭니다 `*.cloudsimple.io` .
3. 온-프레미스 vCenter 구성 요소가 온-프레미스 DNS 서버에 전달 되도록 DNS 조회를 설정 합니다.  사이트 간 VPN을 통해 CloudSimple 사설 클라우드에서 DNS 서버에 연결할 수 있어야 합니다. [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출 하 여 다음 정보를 제공 합니다.  

    * 온-프레미스 DNS 도메인 이름
    * 온-프레미스 DNS 서버 IP 주소

4. 사설 클라우드에 Windows server를 설치 합니다. 서버는 Zerto Virtual Manager를 설치 하는 데 사용 됩니다.
5. [CloudSimple 권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.
6. Zerto Virtual Manager의 서비스 계정으로 사용할 관리 역할을 사용 하 여 사설 클라우드 vCenter에 새 사용자를 만듭니다.

### <a name="optional-configuration-on-your-private-cloud"></a>사설 클라우드의 선택적 구성

1. 사설 클라우드 vCenter에서 하나 이상의 리소스 풀을 만들어 온-프레미스 환경에서 Vm에 대 한 대상 리소스 풀로 사용 합니다.
2. 사설 클라우드 vCenter에서 온-프레미스 환경의 Vm에 대 한 대상 폴더로 사용할 폴더를 하나 이상 만듭니다.
3. 장애 조치 (failover) 네트워크용 Vlan을 만들고 방화벽 규칙을 설정 합니다. [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 을 엽니다.
4. 장애 조치 (failover) 네트워크 및 테스트 네트워크에 대 한 분산 포트 그룹을 만들어 Vm의 장애 조치를 테스트 합니다.
5. [DHCP 및 DNS 서버](dns-dhcp-setup.md) 를 설치 하거나 사설 클라우드 환경에서 Active Directory 도메인 컨트롤러를 사용 합니다.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>사설 클라우드에서 ZVM 및 VRA 설정

1. 사설 클라우드의 Windows server에 ZVM (Zerto Virtual Manager)을 설치 합니다.
2. 이전 단계에서 만든 서비스 계정을 사용 하 여 ZVM에 로그인 합니다.
3. Zerto 가상 관리자에 대 한 라이선스를 설정 합니다.
4. 사설 클라우드의 ESXi 호스트에 VRA (Zerto Virtual Replication 어플라이언스)를 설치 합니다.
5. 사설 클라우드 ZVM을 온-프레미스 ZVM과 페어링 합니다.

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto 가상 보호 그룹 설정

1. 새 가상 보호 그룹 (VPG)을 만들고 VPG에 대 한 우선 순위를 지정 합니다.
2. 비즈니스 연속성을 보호 해야 하는 가상 머신을 선택 하 고 필요한 경우 부팅 순서를 사용자 지정 합니다.
3. 사설 클라우드로 복구 사이트를 선택 하 고, 사설 클라우드 클러스터 또는 만든 리소스 그룹으로 기본 복구 서버를 선택 합니다. 사설 클라우드의 복구 데이터 저장소에 대해 **Vsandatastore 저장소** 를 선택 합니다.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > VM 설정 옵션에서 개별 Vm에 대 한 호스트 옵션을 사용자 지정할 수 있습니다.

4. 필요에 따라 저장소 옵션을 사용자 지정 합니다.
5. 장애 조치 (failover) 네트워크 및 장애 조치 (failover) 테스트 네트워크에서 이전에 만든 분산 포트 그룹으로 사용할 복구 네트워크를 지정 하 고 필요에 따라 복구 스크립트를 사용자 지정 합니다.
6. 필요한 경우 개별 Vm에 대 한 네트워크 설정을 사용자 지정 하 고 VPG를 만듭니다.
7. 복제가 완료 되 면 테스트 장애 조치 (failover)

## <a name="reference"></a>참고

[Zerto 설명서](https://www.zerto.com/myzerto/technical-documentation/)
