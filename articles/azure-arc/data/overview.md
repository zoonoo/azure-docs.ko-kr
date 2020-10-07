---
title: Azure Arc 지원 데이터 서비스란?
description: Azure Arc 지원 데이터 서비스 소개
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: 47fef490c5ece577823a14e3fa4c415f0f613ccb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945728"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Azure Arc 지원 데이터 서비스(미리 보기)란?

Azure Arc를 통해 선택한 인프라 및 Kubernetes를 사용하여 온-프레미스, 에지 및 퍼블릭 클라우드 환경의 Azure 데이터 서비스를 실행할 수 있습니다.

현재 다음과 같은 Azure Arc 지원 데이터 서비스는 미리 보기로 제공됩니다.

- SQL Managed Instance
- PostgreSQL 하이퍼스케일

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>항상 최신

Azure Arc 지원 SQL 관리형 인스턴스 및 Azure Arc 지원 PostgreSQL 하이퍼스케일과 같은 Azure Arc 지원 데이터 서비스는 Azure의 환경과 비슷한 서비스 패치와 새로운 기능이 포함된 업데이트를 자주 받습니다. 사용자에게는 Microsoft Container Registry의 업데이트가 제공되며, 배포 상황은 사용자의 정책을 기반으로 사용자가 설정합니다. 이러한 방식으로 온-프레미스 데이터베이스는 제어를 유지하는 동시에 최신 상태를 유지할 수 있습니다. Azure Arc 지원 데이터 서비스는 구독 서비스이므로 더 이상 데이터베이스에 대한 지원이 종료되는 상황을 겪지 않아도 됩니다.

## <a name="elastic-scale"></a>탄력적 확장

온-프레미스의 클라우드와 유사한 탄력성을 통해 인프라의 사용 가능한 용량에 따라 Azure에서와 거의 동일한 방식으로 데이터베이스를 확장 또는 축소할 수 있습니다. 이 기능은 어떤 규모에서든 데이터를 실시간으로 수집 및 쿼리해야 하는 시나리오를 포함하여 일시적 요구 사항이 있는 버스트 시나리오를 1초 미만의 응답 시간으로 충족할 수 있습니다. 또한 Azure Database for PostgreSQL 하이퍼스케일의 고유한 하이퍼 크기 조정 배포 옵션을 사용하여 데이터베이스 인스턴스를 스케일 아웃할 수도 있습니다. 이 기능은 고유한 스케일 *아웃* 읽기 및 쓰기를 사용하여 데이터 워크로드에 용량 최적화를 추가적으로 제공합니다.

## <a name="self-service-provisioning"></a>셀프서비스 프로비저닝

또한 Azure Arc는 빠른 배포 및 대규모 자동화와 같은 다른 클라우드 이점도 제공합니다. Kubernetes 기반 오케스트레이션 덕분에 GUI 또는 CLI 도구를 사용하여 몇 초만에 데이터베이스를 배포할 수 있습니다.

## <a name="unified-management"></a>통합 관리

이제 Azure Portal, Azure Data Studio 및 Azure Data CLI와 같은 익숙한 도구를 사용하여 Azure Arc로 배포된 모든 데이터 자산에 대한 통합된 보기를 얻을 수 있습니다. 사용자 환경 및 Azure에서 다양한 관계형 데이터베이스를 보고 관리할 수 있을 뿐만 아니라, 기본 인프라 용량과 상태를 분석하기 위해 Kubernetes API에서 로그 및 원격 분석을 가져올 수 있습니다. 이제는 지역화된 로그 분석 및 성능 모니터링 외에도 전체 자산 전반에 걸친 종합적인 운영 인사이트에도 Azure Monitor를 활용할 수 있습니다.

## <a name="disconnected-scenario-support"></a>연결되지 않은 시나리오 지원

셀프서비스 프로비저닝, 자동화된 백업/복원 및 모니터링과 같은 대부분의 서비스는 Azure에 대한 직접 연결의 사용 여부에 관계 없이 인프라에서 로컬로 실행할 수 있습니다. Azure에 직접 연결하면 Azure Monitor와 같은 다른 Azure 서비스와의 통합을 위한 추가 옵션이 제공되며, 전 세계 어디에서나 Azure Portal 및 Azure Resource Manager API를 사용하여 Azure Arc 지원 데이터 서비스를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)를 사용하여 빠르게 시작하세요.

[클라이언트 도구 설치](install-client-tools.md)

[Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)(먼저 클라이언트 도구를 설치해야 함)

[Azure Arc에서 Azure SQL 관리형 인스턴스 만들기](create-sql-managed-instance.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)

[Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
