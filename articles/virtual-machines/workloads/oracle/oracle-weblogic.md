---
title: Oracle WebLogic Server Azure 애플리케이션 | Microsoft Docs
description: Microsoft Azure에서 Oracle WebLogic Server를 실행하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664051"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Oracle WebLogic Server Azure 애플리케이션

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server는 확장 가능한 엔터프라이즈급 Java EE 애플리케이션 서버입니다.

Oracle WebLogic Server는 다중 계층 분산 엔터프라이즈 애플리케이션을 개발하고 배포하기 위한 세계 최초의 클라우드 네이티브 엔터프라이즈 Java 플랫폼 애플리케이션 서버입니다. Azure WebLogic Server를 사용하면 Java EE 애플리케이션을 Azure Cloud로 전환하는 것을 포함하여 WebLogic 마이그레이션에 최소의 노력으로 최대 효과가 있는 선택과 유연성을 제공하여 클라우드 컴퓨팅을 수용할 수 있습니다. 이 제품을 통해 가상 네트워크, 스토리지 및 Linux 리소스를 자동으로 프로비저닝하고, WebLogic Server를 설치하고, 네트워크 보안 그룹을 사용하여 보안을 설정하고, Azure App Gateway를 사용하여 부하를 분산하고, Azure Active Directory를 사용하여 인증하고, 데이터베이스 연결을 간소화하여 비즈니스 애플리케이션을 빠르게 시작할 수 있습니다.

여러 가지 시나리오를 충족하는 데 사용할 수 있는 네 가지 제품(관리 서버가 없는 단일 노드, 관리 서버가 있는 단일 노드, 클러스터 및 동적 클러스터)이 있습니다.  제품은 무료로 제공되므로 사용해보세요.

_이러한 제품은 사용자 라이선스가 필요합니다_. Oracle을 사용하여 적절한 라이선스를 이미 확보했으며 Microsoft Azure에서 제품을 실행할 수 있는 적절한 라이선스를 보유하고 있다고 가정합니다.

[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)에서 _이러한 제품을 개발하는 엔지니어링 팀과 함께 마이그레이션 시나리오에 대해 긴밀하게 작업하려면 [연락처](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) 단추를 클릭하면 됩니다_. 프로그램 관리자, 설계자 및 엔지니어가 곧 연락하여 협업을 시작할 것입니다.

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 단일 노드

이 제품은 단일 가상 머신을 프로비저닝하고 여기에 Oracle WebLogic Server를 설치합니다. 도메인을 만들거나 관리 서버를 시작하지 않습니다. 이는 고도로 사용자 지정된 도메인 구성이 있는 시나리오에 유용합니다.

### <a name="oracle-weblogic-server-with-admin-server"></a>관리 서버가 있는 Oracle WebLogic Server

이 제품은 단일 가상 머신을 프로비저닝하고 여기에 Oracle WebLogic Server를 설치합니다. 도메인을 만들고 도메인을 관리할 수 있는 관리 서버를 시작합니다.

### <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 클러스터

이 제품은 고가용성 Oracle WebLogic Server 가상 머신 클러스터를 만듭니다. 관리 서버 및 관리되는 모든 서버는 기본적으로 시작되며, 이를 통해 도메인을 관리할 수 있습니다.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 동적 클러스터

이 제품은 확장 가능한 고가용성 Oracle WebLogic Server 가상 머신 동적 클러스터를 만듭니다. 관리 서버 및 관리되는 모든 서버는 기본적으로 시작되며, 이를 통해 도메인을 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Marketplace에서 제공하는 제품을 탐색합니다.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 단일 노드](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [관리 서버가 있는 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 클러스터](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 동적 클러스터](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
