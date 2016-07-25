<properties
   pageTitle="Red Hat 업데이트 인프라 (RHUI) | Microsoft Azure"
   description="Microsoft Azure의 주문형 Red Hat Enterprise Linux 인스턴스에 대한 RHUI(Red Hat Update Infrastructure)에 대해 알아봅니다."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# Azure의 주문형 Red Hat Enterprise Linux Vm에 대한 RHUI(Red Hat Update Infrastructure)

Azure 마켓플레이스에서 사용할 수 있는 RHEL(주문형 Red Hat Enterprise Linux)이미지에서 만든 가상 컴퓨터는 Azure에 배포된 RHUI(Red Hat Update Infrastructure)에 액세스하도록 등록됩니다. 주문형 RHEL 인스턴스는 국가별 yum 리포지토리에 액세스하며 증분 업데이트를 받을 수 있습니다.

RHUI에서 관리되는 yum 리포지토리 목록은 프로비전 중 RHEL 인스턴스에 구성됩니다. 따라서 추가 구성을 할 필요가 없습니다. 최신 업데이트를 하려면 RHEL 인스턴스 실행 후 `yum update`를 실행하기만 하면 됩니다.

## RHUI 개요
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure)는 Red Hat 인증 클라우드 공급자가 호스트하는 Red Hat Enterprise Linux 클라우드 인스턴스의 yum 리포지토리 콘텐츠를 관리할 수 있는 확장성이 뛰어난 솔루션을 제공합니다. 업스트림 Pulp 프로젝트를 기반으로, RHUI는 클라우드 공급자가 Red Hat 호스트되는 리포지토리 콘텐츠를 로컬로 미러링하고, 고유한 콘텐츠로 사용자 지정 리포지토리를 만들고, 부하 분산된 콘텐츠 배달 시스템을 통해 이러한 리포지토리를 대규모 최종 사용자 그룹에 제공하도록 허용합니다.

## RHUI 사용 가능한 지역
RHUI는 RHEL 주문형 이미지를 사용할 수 있는 모든 지역에서 제공됩니다. RHUI는 현재 [Azure 상태 대시보드](https://azure.microsoft.com/status/) 페이지에 나열된 모든 공용 지역을 포함합니다. RHEL 주문형 이미지에서 프로비전된 VM에 대한 RHUI 액세스 권한은 해당 가격에 포함되어 있습니다. 지역/국가 클라우드 가용성은 미래에 RHEL 주문형 가용성을 확장하며 업데이트 될 예정입니다.

> [AZURE.NOTE] Azure 호스티드 RHUI에 대한 액세스는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 내의 Vm에 제한됩니다.

## 다른 업데이트 리파지토리에서 업데이트 받기

(Azure 호스티드 RHUI 대신에) 다른 업데이트 리포지토리에서 업데이트를 받아야 할 경우 RHUI에서 인스턴스 등록을 취소하고 원하는 업데이트 인프라(예: Red Hat Satellite 또는 Red Hat 고객 포털 CDN)에 인스턴스를 재등록해야 합니다. 이러한 서비스에 대한 적합한 Red Hat 구독 및 [Azure에서 Red Hat 클라우드 액세스](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)에 대한 등록이 필요합니다.

RHUI 등록을 취소하고 업데이트 인프라에 재등록하려면 다음 단계를 따릅니다.

1.	/etc/yum.repos.d/rh-cloud.repo를 편집하고 모든 `enabled=1`을 `enabled=0`으로 변경합니다. 예:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	/etc/yum/pluginconf.d/rhnplugin.conf를 편집하고 `enabled=0`을 `enabled=1`으로 변경합니다.
3.	그런 다음 Red Hat 고객 포털과 같은 원하는 인프라에 등록합니다. [시스템을 Red Hat 고객 포털에 등록 및 구독하는 방법](https://access.redhat.com/solutions/253273)에 나오는 Red Hat 솔루션 가이드를 따릅니다.

> [AZURE.NOTE] Azure 호스티드 RHUI에 대한 액세스는 RHEL 종량제(PAYG) 이미지 가격에 포함됩니다. Azure 호스티드 RHUI에서 종량제 RHEL VM을 등록 취소해도 가상 컴퓨터를 사용자 라이선스 필요(BYOL) 유형 Vm으로 변환하지 않으므로 동일한 VM을 다른 업데이트 인프라에 등록하는 경우 이중 요금을 초래할 수도 있습니다.
> 
> Azure 호스티드 RHUI 이외의 업데이트 인프라를 지속적으로 사용해야 한다면 [Azure용 RedHat 기반 가상 컴퓨터 만들기 및 업데이트](virtual-machines-linux-redhat-create-upload-vhd.md) 문서에서 설명된 대로 사용자 고유의 (BYOL 유형) 이미지를 만들어 배포하는 것을 고려해 보도록 합니다.

## 다음 단계
Azure 마켓플레이스 종량제 이미지에서 Red Hat Enterprise Linux Vm을 만들고 Azure 호스티드 RHUI를 활용하려면 [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/partners/redhat/)로 갑니다. 아무런 추가 설치도 없이 RHEL 인스턴스에서 `yum update`를 사용할 수 있게 될 것입니다.

<!---HONumber=AcomDC_0713_2016-->