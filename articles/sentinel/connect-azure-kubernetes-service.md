---
title: Azure Kubernetes 서비스 (AKS) 진단 로그를 Azure 센티널에 연결
description: Azure Policy를 사용 하 여 azure Kubernetes Service 진단 로그를 Azure 센티널에 연결 하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: b85c280f99dc1bb4837169c2167b8b2ffb902b7d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491291"
---
# <a name="connect-azure-kubernetes-service-diagnostics-and-auditing-logs"></a>Azure Kubernetes Service 진단 및 감사 로그 연결

AKS (Azure Kubernetes Service)는 클러스터 환경에서 Docker 컨테이너 및 컨테이너 기반 응용 프로그램을 배포, 확장 및 관리할 수 있는 완전히 관리 되는 오픈 소스 컨테이너 오케스트레이션 서비스입니다.

이 커넥터를 통해 azure Kubernetes 서비스 (AKS) 진단 로그를 Azure 센티널로 스트리밍할 수 있으므로 모든 인스턴스에서 작업을 지속적으로 모니터링할 수 있습니다. 

[Azure Kubernetes Service 모니터링](../azure-monitor/containers/container-insights-overview.md) 및 [AKS 진단 원격 분석](../aks/view-control-plane-logs.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>전제 조건

AKS 로그를 Azure 센티널에 수집 하려면:

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Policy를 사용 하 여 로그 스트리밍 정책을 AKS 리소스에 적용 하려면 정책 할당 범위에 대 한 소유자 역할이 할당 되어야 합니다.

## <a name="connect-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 연결

이 커넥터는 Azure Policy를 사용 하 여 단일 Azure Kubernetes 서비스 로그 스트리밍 구성을 범위로 정의 된 인스턴스 컬렉션에 적용 합니다. 커넥터의 왼쪽에 있는 **데이터 형식** 에서 Azure Kubernetes Service에서 수집 로그 유형을 확인할 수 있습니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure Kubernetes 서비스 (AKS)** 를 선택 하 고 미리 보기 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. 커넥터 페이지의 **구성** 섹션에서 **Azure Kubernetes Service에서 진단 로그 사용 (AKS)** 을 확장 합니다.

1. **Azure Policy 할당 시작 마법사** 단추를 선택 합니다.

    정책 할당 마법사가 열리고 **Log Analytics 작업 영역에 대 한 Azure Kubernetes Service에 대 한 진단 설정 구성** 이라는 새 정책을 만들 준비가 되었습니다.

    1. **기본** 탭에서 **범위** 아래의 세 점이 있는 단추를 클릭 하 여 구독을 선택 하 고 필요에 따라 리소스 그룹을 선택 합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭의 **Log Analytics 작업 영역** 드롭다운 목록에서 Azure 센티널 작업 영역을 선택 합니다. 나머지 드롭다운 필드는 사용 가능한 진단 로그 유형을 나타냅니다. 수집 하려는 모든 로그 형식으로 "True"로 표시 된 채로 둡니다.

    1. 기존 리소스에 정책을 적용 하려면 **관리** 탭을 선택 하 고 **수정 작업 만들기** 확인란을 선택 합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당 됩니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용 하 여 지난 2 주 동안 데이터가 수집 된 경우에만 연결 상태 표시기 (데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆의 연결 아이콘)가 *연결* 됨 (녹색)으로 표시 됩니다. 두 주가 데이터 수집 없이 전달 되 면 커넥터는 연결 끊김으로 표시 됩니다. 더 많은 데이터가 제공 되는 순간에 *연결* 된 상태는를 반환 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 azure Kubernetes Service를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
