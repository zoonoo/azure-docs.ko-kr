<properties
   pageTitle="Red Hat Enterprise Linux 이미지에 대한 업데이트 인프라 | Microsoft Azure"
   description="Azure의 주문형 Red Hat Enterprise Linux 인스턴스에 대한 yum 업데이트 서비스를 소개합니다."
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="kyliel"/>

# Red Hat Enterprise Linux 이미지에 대한 업데이트 인프라

Azure에서 RHUI(Red Hat Update Infrastructure)를 사용하여 Azure RHEL(Red Hat Enterprise Linux) 이미지의 yum 리포지토리 콘텐츠를 관리할 수 있습니다. Azure 마켓플레이스에서 만든 주문형 RHEL 인스턴스는 국가별 yum 리포지토리에 액세스할 수 있습니다. 이를 통해 RHEL 인스턴스에 증분 업데이트를 적용할 수 있습니다.

RHUI에서 관리되는 yum 리포지토리 목록은 프로비전 중 RHEL 인스턴스에 구성됩니다. 따라서 추가 구성을 수행할 필요가 없습니다. RHEL 인스턴스 실행 후 `yum update`를 실행하기만 하면 됩니다.

## RHUI 개요
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure)는 Red Hat 인증 클라우드 공급자가 호스트하는 Red Hat Enterprise Linux 클라우드 인스턴스의 yum 리포지토리 콘텐츠를 관리할 수 있는 확장성이 뛰어난 솔루션을 제공합니다. 업스트림 Pulp 프로젝트를 기반으로, RHUI는 클라우드 공급자가 Red Hat 호스트되는 리포지토리 콘텐츠를 로컬로 미러링하고, 고유한 콘텐츠로 사용자 지정 리포지토리를 만들고, 부하 분산된 콘텐츠 배달 시스템을 통해 이러한 리포지토리를 대규모 최종 사용자 그룹에 제공하도록 허용합니다.

## RHUI 배포 지역
RHUI는 [Azure 상태 대시보드에 나열된 모든 공용 지역에 배포됩니다](https://azure.microsoft.com/status/). 따라서 이러한 지역에서는 추가 비용 없이 yum 업데이트 서비스를 얻을 수 있습니다. 이 정보는 나중에 업데이트될 예정입니다.

## 온-프레미스 업데이트 리포지토리(예: Red Hat Network Satellite)에서 업데이트 받기

온-프레미스 업데이트 리포지토리에서 업데이트를 받으려면 Red Hat 클라우드 액세스 라이선스 및 기존 Red Hat Azure 구독이 있어야 합니다.

그런 다음 RHUI 등록을 취소하고 온-프레미스 업데이트 인프라를 등록해야 합니다. 자세한 단계는 다음과 같습니다.

1.	/etc/yum.repos.d/rh-cloud.repo를 편집하고 모든 `enabled=1`을 `enabled=0`으로 변경합니다. 예:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	/etc/yum/pluginconf.d/rhnplugin.conf를 편집하고 `enabled=0`을 `enabled=1`으로 변경합니다.
3.	그런 다음 RHN(Red Hat Network)에 등록합니다.

        rhn_register

    또는

        rhnreg_ks


> [AZURE.NOTE] 아웃바운드 데이터 전송 요금이 청구됩니다([가격 정보](http://azure.microsoft.com/pricing/details/data-transfers/) 참조). 추가 비용이 발생하지 않도록 기본 RHUI를 사용하여 증분 업데이트를 받는 것이 좋습니다.

## 다음 단계
이제 Azure의 RHUI를 이해했으므로 [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/partners/redhat/)에서 RHEL 이미지를 만들고 RHEL 인스턴스에서 `yum update`를 사용할 수 있습니다.

<!---HONumber=AcomDC_0224_2016-->