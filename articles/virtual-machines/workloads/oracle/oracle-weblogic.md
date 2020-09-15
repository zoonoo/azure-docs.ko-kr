---
title: Azure 솔루션의 Oracle WebLogic 서버
description: Microsoft Azure에서 Oracle WebLogic Server를 실행하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: rezar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: rezar
ms.openlocfilehash: e408f9e245fb78b475a194bc0db6f1edfdf85b41
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069727"
---
# <a name="solutions-for-running-oracle-weblogic-server-on-azure"></a>Azure에서 Oracle WebLogic 서버를 실행 하기 위한 솔루션

이 페이지에서는 Azure virtual machines에서 Oracle WebLogic 서버 (WLS)를 실행 하기 위한 솔루션에 대해 설명 합니다. 이러한 솔루션은 Oracle 및 Microsoft에서 공동으로 개발 됩니다.

WLS는 전 세계에서 중요 업무용 엔터프라이즈 Java 응용 프로그램 중 일부를 실행 하는 최고의 Java 응용 프로그램 서버입니다. WLS는 Oracle 소프트웨어 제품군의 미들웨어 토대를 형성 합니다. Oracle과 Microsoft는 고객이 Azure에서 워크 로드를 최고의 클라우드 플랫폼으로 실행 하는 데 적합 하 고 유연 하 게 WLS 수 있도록 최선을 다하고 있습니다.

Azure WLS 솔루션은 대부분의 상용구 작업을 자동화 하 여 Java EE 응용 프로그램을 Azure virtual machines로 쉽게 리프트 하 고 이동할 수 있도록 하기 위한 것입니다. 이 솔루션은 가상 네트워크, 저장소, Java 및 Linux 리소스를 자동으로 프로 비전 합니다. 최소한의 노력으로 WebLogic 서버가 설치 됩니다. 솔루션은 네트워크 보안 그룹을 사용 하 여 보안을 설정 하 고, Azure 앱 Gateway를 사용 하는 부하 분산 및 Azure Active Directory 인증 Azure PostgreSQL, Azure SQL 및 Oracle 클라우드 또는 Azure의 Oracle DB을 포함 하 여 기존 데이터베이스에 자동으로 연결할 수도 있습니다. 솔루션에 대 한 로드맵 맵에는 Oracle 일관성을 통해 분산 로깅 및 분산 캐싱을 사용 하도록 설정 하는 기능이 포함 되어 있습니다. Microsoft와 Oracle은 웹 논리 및 Azure Kubernetes 서비스 (AKS)에 대해 유사한 기능을 사용 하도록 파트너 관계를 합니다.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Azure Portal를 사용 하 여 Azure에서 WebLogic 서버를 배포할 수 있습니다.":::

여러 가지 시나리오를 충족 하는 데 사용할 수 있는 4 가지 제품이 있습니다. 즉, 관리 서버가 없는 단일 노드, 관리 서버, 클러스터 및 동적 클러스터가 포함 된 단일 노드입니다. 제품을 무료로 사용할 수 있습니다. 이러한 제품에 대해서는 아래에서 설명 하 고 연결 합니다.

_이러한 제품은 사용자 라이선스가 필요합니다_. Oracle을 사용 하 여 이미 적절 한 라이선스를 보유 하 고 있으며 Azure에서 제품을 실행할 수 있는 적절 한 라이선스를가지고 있다고 가정 합니다.

는 기본 이미지 (Oracle Linux 7.6의 WebLogic 서버 14 및 JDK 11)를 통해 다양 한 운영 체제, Java 및 WLS 버전을 지원 합니다. 이러한 기본 이미지는 Azure에서 직접 사용할 수도 있습니다. 기본 이미지는 복잡 하 고 사용자 지정 된 Azure 배포가 필요한 고객에 게 적합 합니다. 현재 기본 이미지 집합은 [여기](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)에서 사용할 수 있습니다.

_엔지니어링 팀이 이러한 제품을 개발 하는 마이그레이션 시나리오를 긴밀 [CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ 하 게 작업 하는 경우 [marketplace 제품 개요 페이지](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)에서 나에 게 연락 단추를 선택 합니다. 프로그램 관리자, 설계자 및 엔지니어가 곧 연락 하 여 가까운 공동 작업을 시작 합니다. 제품을 개발 하는 동안에는 마이그레이션 시나리오에 대 한 공동 작업 기회를 무료로 사용할 수 있습니다.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 단일 노드

이 제품은 단일 가상 머신을 프로 비전 하 고 WLS를 설치 합니다. 도메인을 만들거나 관리 서버를 시작 하지 않습니다. 단일 노드 제안은 사용자 지정 된 도메인 구성이 있는 시나리오에 유용 합니다.

## <a name="oracle-weblogic-server-with-admin-server"></a>관리 서버가 있는 Oracle WebLogic Server

이 제품은 단일 가상 머신을 프로 비전 하 고 WLS를 설치 합니다. 도메인을 만들고 관리 서버를 시작 합니다. 도메인을 관리 하 고 응용 프로그램 배포를 바로 시작할 수 있습니다.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 클러스터

이 제품은 WLS 가상 컴퓨터의 항상 사용 가능한 클러스터를 만듭니다. 관리 서버 및 관리 되는 모든 서버는 기본적으로 시작 됩니다. 클러스터를 관리 하 고 항상 사용 가능한 응용 프로그램을 바로 시작할 수 있습니다.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 동적 클러스터

이 제품은 WLS 가상 머신의 고가용성 및 확장 가능한 동적 클러스터를 만듭니다. 관리 서버 및 관리 되는 모든 서버는 기본적으로 시작 됩니다.

이 솔루션을 사용 하면 비교적 편리 하 게 다양 한 프로덕션 준비 배포 아키텍처를 사용할 수 있습니다. 비즈니스 응용 프로그램 개발에 집중 하 여 가능한 가장 생산적인 방법으로 대부분의 마이그레이션 사례를 충족할 수 있습니다.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Azure에서 복잡 한 WebLogic 서버 배포가 사용 됩니다.":::

솔루션에 의해 자동으로 프로 비전 되는 것 외에도 고객은 배포를 더욱 유연 하 게 사용자 지정할 수 있습니다. 응용 프로그램을 배포할 때 고객이 더 많은 Azure 리소스를 배포와 통합 하 게 됩니다. 고객은 솔루션 향상에 대 한 피드백을 제공 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Azure에서 제공 하는 제품을 살펴보세요.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 단일 노드](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [관리 서버가 있는 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 클러스터](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 동적 클러스터](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
