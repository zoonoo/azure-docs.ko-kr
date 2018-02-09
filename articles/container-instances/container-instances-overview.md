---
title: "Azure Container Instances 개요"
description: "Azure Container Instances 이해"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 01e539856adbdcf02dc4e49087a3ab71b328db5a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

컨테이너는 클라우드 응용 프로그램을 패키지, 배포 및 관리하기 위한 기본 방법으로 신속히 도입되고 있습니다. Azure Container Instances는 어떠한 가상 머신을 프로비전하지 않고 또 더 높은 수준의 서비스를 채택하지 않고도 Azure에서 컨테이너를 실행하는 가장 빠르고 간단한 방법을 제공합니다.

Azure Container Instances는 간단한 응용 프로그램, 작업 자동화 및 빌드 작업 등 격리된 컨테이너에서 작동할 수 있는 모든 시나리오에 적합한 솔루션입니다. 여러 컨테이너 간 서비스 검색, 자동 크기 조정 및 조정된 응용 프로그램 업그레이드를 포함하여 전체 컨테이너 오케스트레이션이 필요한 시나리오에는 [AKS(Azure Container Service)](../aks/index.yml)를 사용하는 것이 좋습니다.

## <a name="fast-startup-times"></a>빠른 시작 시간

컨테이너는 가상 머신을 통한 상당한 시작 이점을 제공합니다. Azure Container Instances를 통해 VM을 프로비전 및 관리할 필요 없이 Azure에서 몇 초 안에 컨테이너를 시작할 수 있습니다.

## <a name="hypervisor-level-security"></a>하이퍼바이저 수준 보안

지금까지는 컨테이너에서 응용 프로그램 종속성 격리 및 리소스 관리를 제공했지만 적대적인 다중 테넌트 사용을 위해 충분히 강화되지 않은 것으로 간주됩니다. Azure Container Instances를 통해 응용 프로그램은 VM에서처럼 컨테이너에 격리됩니다.

## <a name="custom-sizes"></a>사용자 지정 크기

일반적으로 컨테이너는 단일 응용 프로그램만 실행하도록 최적화되었지만 이러한 응용 프로그램의 정확한 요구 사항은 크게 다를 수 있습니다. Azure Container Instances를 통해 CPU 코어 및 메모리를 기준으로 필요한 사항을 정확히 요청할 수 있습니다. 요청한 내용에 따라 비용을 초 단위로 지불하므로 사용자 요구에 따라 지출을 미세하게 최적화할 수 있습니다.

## <a name="public-ip-connectivity"></a>공용 IP 연결

Azure Container Instances를 통해 컨테이너를 공용 IP 주소로 인터넷에 직접 공개할 수 있습니다. 향후에는 가상 네트워크, 부하 분산 장치 및 Azure 네트워킹 인프라의 기타 핵심 부분과의 통합을 포함하도록 네트워킹 기능이 확장될 예정입니다.

## <a name="persistent-storage"></a>영구 저장소

Azure Container Instances를 통해 상태를 검색하고 유지하려면 [Azure Files 공유 탑재](container-instances-mounting-azure-files-volume.md)를 직접 제공합니다.

## <a name="linux-and-windows-containers"></a>Linux 및 Windows 컨테이너

Azure Container Instances에서는 동일한 API로 Windows 및 Linux 컨테이너를 모두 예약할 수 있습니다. [컨테이너 그룹](container-instances-container-groups.md)을 만들 때는 OS 종류만 지정합니다.

일부 기능은 현재 Linux 컨테이너에 제한됩니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="co-scheduled-groups"></a>공동 예약된 그룹

Azure Container Instances는 호스트 컴퓨터, 로컬 네트워크, 저장소 및 수명 주기를 공유하는 [다중 컨테이너 그룹](container-instances-container-groups.md)의 예약을 지원합니다. 이렇게 하면 주 응용 프로그램을 로깅과 같은 지원 역할을 수행하는 다른 항목과 결합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작 가이드](container-instances-quickstart.md)를 사용하여 단일 명령으로 Azure에 컨테이너를 배포해 보세요.