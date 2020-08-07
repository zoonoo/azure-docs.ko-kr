---
title: Azure의 Oracle WebLogic 서버 란 무엇 인가요?
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
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851864"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Azure의 Oracle WebLogic 서버 란 무엇 인가요?

이 페이지에서는 Azure Virtual Machines에서 WebLogic 서버 (WLS)를 실행 하기 위한 솔루션을 설명 합니다.  이러한 솔루션은 Oracle 및 Microsoft에서 공동으로 개발 됩니다.

Oracle WebLogic Server는 다중 계층 분산 엔터프라이즈 애플리케이션을 개발하고 배포하기 위한 세계 최초의 클라우드 네이티브 엔터프라이즈 Java 플랫폼 애플리케이션 서버입니다. Azure WebLogic 서버 제품을 사용 하면 클라우드 컴퓨팅을 수용할 수 있습니다.  Java EE 응용 프로그램을 Azure 클라우드로 리프트 앤 시프트 하는 것을 포함 하 여 WebLogic 마이그레이션에 대 한 더 많은 선택과 유연성을 얻을 수 있습니다.   Azure의 WLS는 약간의 노력으로 큰 영향을 줍니다. 이 제품을 통해 LOB (기간 업무) 응용 프로그램을 신속 하 게 시작할 수 있습니다.  각 제품은 가상 네트워크, 저장소 및 Linux 리소스를 자동으로 프로 비전 합니다.  제로 작업을 통해 WebLogic 서버가 설치 됩니다.  WLS는 네트워크 보안 그룹을 사용 하 여 보안을 설정 하 고, Azure 앱 Gateway를 사용 하 여 부하를 분산 하 고, Azure Active Directory으로 인증 하 고, 기존 데이터베이스에 자동으로 연결 합니다.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Azure Portal을 사용 하 여 Azure에서 WebLogic 서버를 배포할 수 있습니다.":::

여러 가지 시나리오를 충족 하는 데 사용할 수 있는 4 가지 제품이 있습니다. 즉, 관리 서버가 없는 단일 노드, 관리 서버, 클러스터 및 동적 클러스터가 포함 된 단일 노드입니다.  제품을 사용해 보세요. 무료로 제공 됩니다.

_이러한 제품은 사용자 라이선스가 필요합니다_. 사용자는 이미 Oracle을 사용 하 여 적절 한 라이선스를 보유 하 고 있으며 Microsoft Azure에서 제품을 실행할 수 있는 적절 한 라이선스를가지고 있다고 가정 합니다

_엔지니어링 팀이 이러한 제품을 개발 하는 마이그레이션 시나리오를 긴밀 하 게 작업 하려면 Azure Marketplace에서 [나에 게 문의](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) 단추_ 를 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)선택 합니다. 프로그램 관리자, 설계자 및 엔지니어가 곧 연락 하 여 공동 작업을 시작 합니다.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 단일 노드

이 제품은 단일 가상 머신을 프로 비전 하 고 WLS를 설치 합니다. 도메인을 만들거나 관리 서버를 시작 하지 않습니다. 단일 노드는 사용자 지정 된 도메인 구성이 있는 시나리오에 유용 합니다.

## <a name="oracle-weblogic-server-with-admin-server"></a>관리 서버가 있는 Oracle WebLogic Server

이 제품은 단일 가상 머신을 프로 비전 하 고 WLS를 설치 합니다. 도메인을 만들고 도메인을 관리할 수 있는 관리 서버를 시작합니다.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 클러스터

이 제품은 WLS 가상 컴퓨터의 항상 사용 가능한 클러스터를 만듭니다. 관리 서버 및 관리되는 모든 서버는 기본적으로 시작되며, 이를 통해 도메인을 관리할 수 있습니다.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 동적 클러스터

이 제품은 WLS 가상 머신의 고가용성 및 확장 가능한 동적 클러스터를 만듭니다. 관리 서버 및 관리되는 모든 서버는 기본적으로 시작되며, 이를 통해 도메인을 관리할 수 있습니다.

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
