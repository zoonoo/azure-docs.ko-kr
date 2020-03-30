---
title: Azure Kubernetes 서비스(AKS) 진단 개요
description: Azure Kubernetes 서비스에서 자체 진단 클러스터에 대해 알아봅니다.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126598"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes 서비스 진단(미리 보기) 개요

특히 클러스터가 미션 크리티컬 워크로드를 실행하는 경우 AKS(Azure Kubernetes) 클러스터 문제를 해결하는 것은 클러스터를 유지하는 데 중요한 부분입니다. AKS 진단은 클러스터의 문제를 식별하고 해결하는 데 도움이 되는 지능형 자체 진단 환경입니다. AKS 진단은 클라우드 네이티브이며 추가 구성 이나 청구 비용 없이 사용할 수 있습니다.

이 기능은 이제 공개 미리 보기상태입니다.

## <a name="open-aks-diagnostics"></a>AKS 진단 열기

AKS 진단에 액세스하려면 다음을 수행하십시오.

- [Azure 포털에서](https://portal.azure.com)Kubernetes 클러스터로 이동합니다.
- AKS 진단을 여는 왼쪽 탐색에서 **진단 및 해결 문제를** 클릭합니다.
- 홈페이지 타일의 키워드를 사용하여 클러스터 문제를 가장 잘 설명하는 범주를 선택하거나 검색 표시줄에서 문제를 가장 잘 설명하는 키워드(예: _클러스터 노드 문제)를_입력합니다.

![홈페이지](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

범주를 클릭한 후 클러스터와 관련된 진단 보고서를 볼 수 있습니다. 상태 아이콘이 있는 클러스터에 문제가 있는 경우 진단 보고서가 지능적으로 호출됩니다. **추가 정보를** 클릭하여 각 주제를 드릴다운하여 문제에 대한 자세한 설명, 권장 작업, 유용한 문서에 대한 링크, 관련 메트릭 및 로깅 데이터를 확인할 수 있습니다. 진단 보고서는 다양한 검사를 실행한 후 클러스터의 현재 상태에 따라 지능적으로 생성됩니다. 진단 보고서는 클러스터의 문제를 정확히 파악하고 문제를 해결하기 위한 다음 단계를 찾는 데 유용한 도구가 될 수 있습니다.

![진단 보고서](./media/concepts-diagnostics/diagnostic-report.png)

![확장된 진단 보고서](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>클러스터 인사이트

다음 진단 검사는 **클러스터 Insights에서**사용할 수 있습니다.

### <a name="cluster-node-issues"></a>클러스터 노드 문제

클러스터 노드 문제는 클러스터가 예기치 않게 발생할 수 있는 노드 관련 문제를 확인합니다.

- 노드 준비 문제
- 노드 오류
- 부족한 리소스
- 노드 누락 IP 구성
- 노드 CNI 오류
- 노드를 찾을 수 없습니다.
- 노드 전원 끄기
- 노드 인증 실패
- 노드 쿠베 프록시 부실

### <a name="create-read-update--delete-operations"></a>작업 삭제 & 만들기, 읽기, 업데이트

CRUD 연산은 클러스터에 문제를 일으킬 수 있는 CRUD 작업을 확인합니다.

- 사용 중인 서브넷 삭제 작업 오류
- 네트워크 보안 그룹 삭제 작업 오류
- 사용 중인 경로 테이블 삭제 작업 오류
- 참조된 리소스 프로비저닝 오류
- 공용 IP 주소 삭제 작업 오류
- 배포 할당량으로 인한 배포 실패
- 조직 정책으로 인한 작업 오류
- 구독 등록 누락
- VM 확장 프로비저닝 오류
- 서브넷 용량
- 할당량 초과 오류

### <a name="identity-and-security-management"></a>ID 및 보안 관리

ID 및 보안 관리는 클러스터에 대한 통신을 방해할 수 있는 인증 및 권한 부여 오류를 검색합니다.

- 노드 권한 부여 실패
- 401 오류
- 403 오류

## <a name="next-steps"></a>다음 단계

로그를 수집하여 [AKS Periscope를](https://aka.ms/aksperiscope)사용하여 클러스터 문제를 추가로 해결할 수 있습니다.

제목에 "[Diag]"를 추가하여 [UserVoice에](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) 질문이나 피드백을 게시합니다.
