---
title: AKS(Azure Kubernetes Service) 진단 개요
description: Azure Kubernetes Service의 자체 진단 클러스터에 대해 알아봅니다.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183365"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes Service 진단(미리 보기) 개요

AKS(Azure Kubernetes Service) 클러스터 문제를 해결하는 작업은 클러스터를 유지 관리하는 데 중요한 부분입니다. 특히 클러스터에서 중요 업무용 워크로드를 실행하는 경우에 해당합니다. AKS 진단은 클러스터의 문제를 식별하고 해결하는 데 도움이 되는 지능형 자체 진단 환경입니다. AKS 진단은 클라우드 네이티브이며 추가 구성 또는 청구 비용 없이 사용할 수 있습니다.

이 기능은 공개 미리 보기 상태입니다.

## <a name="open-aks-diagnostics"></a>AKS 진단 열기

AKS 진단에 액세스하려면

- [Azure Portal](https://portal.azure.com)에서 Kubernetes 클러스터로 이동합니다.
- 왼쪽 탐색에서 **문제 진단 및 해결** 을 클릭하면 AKS 진단이 열립니다.
- 홈페이지 타일의 키워드를 사용하여 클러스터의 문제를 가장 잘 설명하는 범주를 선택하거나, 검색 표시줄에서 문제를 가장 잘 설명하는 키워드(예: _클러스터 노드 문제_)를 입력합니다.

![홈페이지](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

범주를 선택한 후에는 해당 클러스터의 진단 보고서를 볼 수 있습니다. 상태 아이콘이 있는 클러스터에 문제가 있는 경우 진단 보고서를 지능적으로 호출합니다. **자세한 정보** 를 클릭하여 각 항목을 드릴 다운하여 문제에 대한 자세한 설명, 권장 조치, 유용한 문서에 대한 링크, 관련 메트릭, 로깅 데이터를 확인할 수 있습니다. 진단 보고서는 다양한 검사를 실행한 후 클러스터의 현재 상태에 따라 지능적으로 생성됩니다. 진단 보고서는 클러스터의 문제를 정확히 찾아내고, 다음 단계를 찾아 문제를 해결하는 데 유용한 도구일 수 있습니다.

![진단 보고서](./media/concepts-diagnostics/diagnostic-report.png)

![확장된 진단 보고서](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>클러스터 인사이트

다음 진단 검사는 **클러스터 인사이트** 에서 사용할 수 있습니다.

### <a name="cluster-node-issues"></a>클러스터 노드 문제

클러스터 노드는 클러스터를 예기치 않게 동작시킬 수 있는 노드 관련 문제를 검사합니다.

- 노드 준비 문제
- 노드 오류
- 리소스가 부족합니다.
- 노드에 IP 구성이 없습니다.
- 노드 CNI 오류
- 노드가 없습니다.
- 노드 전원 끄기
- 노드 인증 실패
- 노드 kube-프록시 부실

### <a name="create-read-update--delete-operations"></a>만들기, 읽기, 업데이트 및 삭제 작업

CRUD 작업은 클러스터에서 문제를 일으킬 수 있는 CRUD 작업을 검사합니다.

- 사용 중인 서브넷 삭제 작업 오류
- 네트워크 보안 그룹 삭제 작업 오류
- 사용 중인 경로 테이블 삭제 작업 오류
- 참조된 리소스 프로비전 오류
- 공용 IP 주소 삭제 작업 오류
- 배포 할당량으로 인한 배포 실패
- 조직 정책으로 인한 작업 오류
- 누락된 구독 등록
- VM 확장 프로비전 오류
- 서브넷 용량
- 할당량 초과 오류

### <a name="identity-and-security-management"></a>ID 및 보안 관리

ID 및 보안 관리는 클러스터와의 통신을 방해할 수 있는 인증 및 권한 부여 오류를 검색합니다.

- 노드 권한 부여 오류
- 401 오류
- 403 오류

## <a name="next-steps"></a>다음 단계

[AKS Periscope](https://aka.ms/aksperiscope)를 사용하여 클러스터 문제를 추가로 해결하는 데 도움이 되는 로그를 수집합니다.

AKS 2일차 운영 가이드의 [심사 방법 섹션](/azure/architecture/operator-guides/aks/aks-triage-practices)을 참조하세요.

제목에 “[Diag]”를 추가하여 [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) 에서 질문이나 의견을 게시합니다.