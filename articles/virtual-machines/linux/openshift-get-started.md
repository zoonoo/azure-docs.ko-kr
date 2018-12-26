---
title: Azure의 OpenShift 개요 | Microsoft Docs
description: Azure의 OpenShift 개요입니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085442"
---
# <a name="openshift-in-azure"></a>Azure의 OpenShift

OpenShift는 엔터프라이즈에 Docker 및 Kubernetes를 제공하는 확장 가능한 개방형 컨테이너 애플리케이션 플랫폼입니다.  

OpenShift는 컨테이너 오케스트레이션 및 관리를 위한 Kubernetes를 포함합니다. 추가로 다음을 지원하는 개발자 중심 및 작업 중심 도구를 제공합니다.

- RAD(신속한 응용 프로그램 개발)
- 간편한 배포 및 확장
- 팀 및 애플리케이션에 대한 장기 수명 유지 관리

OpenShift의 여러 버전을 사용할 수 있습니다.

- OpenShift Container Platform
- OpenShift On Azure(CY2019 초에 제공될 완전 관리형 OpenShift)
- OKD(이전의 OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

이 문서에 설명된 5가지 버전 중 2가지 즉, OpenShift Container Platform 및 OKD만 고객이 Azure에서 배포할 수 있습니다.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform은 Red Hat이 제공하고 지원하는 엔터프라이즈급 [상용](https://www.openshift.com) 버전입니다. 이 버전을 통해 고객은 OpenShift Container Platform에 필요한 자격을 구매하고 전체 인프라의 설치 및 관리를 담당합니다.

고객이 전체 플랫폼을 “소유”하기 때문에 온-프레미스 데이터 센터나 공용 클라우드(Azure, AWS, Google 등)에 설치할 수 있습니다.

## <a name="openshift-on-azure"></a>OpenShift On Azure

OpenShift On Azure는 Azure에서 실행되는 OpenShift의 완전 관리형 제품입니다. 이 서비스는 Microsoft 및 Red Hat에서 공동으로 관리 및 지원합니다. 클러스터는 고객의 Azure 구독에 배포됩니다. 이 서비스는 현재 비공개 미리 보기로 제공되며 CY 2019 초에 GA될 예정입니다. GA가 좀 더 가까워지면 추가 정보가 제공될 것입니다.

## <a name="okd-formerly-openshift-origin"></a>OKD(이전의 OpenShift Origin)

OKD는 커뮤니티가 지원하는 OpenShift의 [오픈 소스](https://www.okd.io/) 업스트림 프로젝트입니다. OKD는 CentOS 또는 RHEL(Red Hat Enterprise Linux)에 설치할 수 있습니다.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated는 OpenShift Container Platform을 사용하는 Red Hat이 관리하는 *단일 테넌트* OpenShift입니다. Red Hat이 모든 기본 인프라(VM, OpenShift 클러스터, 네트워킹, 저장소 등)를 관리합니다. 클러스터는 한 고객 전용이며 공용 클라우드(예: AWS 또는 Google)에서 실행됩니다. 시작 클러스터에는 4개의 응용 프로그램 노드가 포함되며 모든 비용은 연 단위로 선불로 지불됩니다.

## <a name="openshift-online"></a>OpenShift Online

Online은 Container Platform을 사용하는 Red Hat이 관리하는 *다중 테넌트* OpenShift입니다. Red Hat이 모든 기본 인프라(예: VM, OpenShift 클러스터, 네트워킹, 저장소 등)를 관리합니다. 

이 버전을 통해 고객은 컨테이너를 배포하지만 컨테이너가 실행되는 호스트를 제어할 수 없습니다. Online은 다중 테넌트이므로 컨테이너가 다른 고객의 컨테이너와 동일한 VM 호스트에 함께 배치될 수 있습니다. 비용은 컨테이너당 청구됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 OpenShift에 대한 일반적인 필수 조건 구성](./openshift-prerequisites.md)
- [Azure에서 OpenShift Container Platform 배포](./openshift-container-platform.md)
- [Azure에 OKD 배포](./openshift-okd.md)
- [Azure Stack에 OpenShift 배포](./openshift-azure-stack.md)
- [배포 후 작업](./openshift-post-deployment.md)
- [OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
