---
title: Azure Kubernetes Service (AKS) 진단 로그를 Azure Sentinel에 연결
description: Azure Policy를 사용하여 Azure Kubernetes Service (AKS) 진단 로그를 Azure Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/22/2021
ms.author: yelevin
ms.openlocfilehash: f1ef860f1b84de84c42996a7523af8ce174d5981
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890801"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Azure Kubernetes Service 진단 로그에 연결

AKS(Azure Kubernetes Service)는 클러스터 환경에서 Docker 컨테이너 및 컨테이너 기반 애플리케이션을 배포, 확장 및 관리할 수 있는 완전히 관리되는 오픈 소스 컨테이너 오케스트레이션 서비스입니다.

이 커넥터를 통해 Azure Kubernetes Service (AKS) 진단 로그를 Azure Sentinel로 스트리밍할 수 있으므로 모든 인스턴스에서 작업을 지속적으로 모니터링할 수 있습니다. 

[Azure Kubernetes Service 모니터링](../azure-monitor/containers/container-insights-overview.md) 및 [AKS 진단 원격 분석](../aks/view-control-plane-logs.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

AKS 로그를 Azure Sentinel에 수집하기:

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Policy를 사용하여 AKS 리소스에 로그 스트리밍 정책을 적용하려면 정책 할당 범위에 대한 Owner 역할이 있어야 합니다.

## <a name="connect-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 연결

이 커넥터는 Azure Policy를 사용하여 단일 Azure Kubernetes Service 로그 스트리밍 구성을 범위로 정의된 리소스 컬렉션에 적용합니다. Azure Kubernetes Service에서 **데이터 형식** 아래 커넥터 페이지의 왼쪽에 있는 수집 로그 형식을 확인할 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure Kubernetes Service (AKS)** 를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 **Stream diagnostics logs from your Azure Kubernetes Service (AKS) at scale**(대규모로 AKS(Azure Kubernetes Service)의 진단 로그 스트리밍)을 확장합니다.

1. **Launch Azure Policy Assignment 마법사** 단추를 선택합니다.

    정책 할당 마법사가 열리고 **배포 - Log Analytics 작업 영역에 Azure Kubernetes Service용 진단 설정 구성** 이라는 새 정책을 만들 준비가 됩니다.

    1. **기본** 탭에서 **범위** 아래의 세 점이 있는 단추를 클릭하여 구독을 선택하고, 필요에 따라 리소스 그룹을 선택합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭에서 **효과** 및 **설정 이름** 필드를 그대로 둡니다. **Log Analytics 작업 영역** 드롭다운 목록에서 Azure Sentinel 작업 영역을 선택합니다. 나머지 드롭다운 필드는 사용 가능한 진단 로그 형식을 나타냅니다. 수집하려는 모든 로그 형식을 “True”로 표시된 채로 둡니다.

    1. 정책은 나중에 추가되는 리소스에 적용됩니다. 기존 리소스에도 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 생성** 확인란에 표시합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 지난 14일 동안 특정 시점에 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆에 연결 아이콘)가 *연결됨*(녹색)으로 표시됩니다. 데이터 수집 없이 14일이 경과되면 커넥터는 연결 끊김 상태로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Policy를 사용하여 Azure Kubernetes Service를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 잠재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
