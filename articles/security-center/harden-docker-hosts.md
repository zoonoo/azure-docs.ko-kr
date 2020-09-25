---
title: Azure Security Center를 사용 하 여 Docker 호스트를 강화 하 고 컨테이너를 보호 합니다.
description: 방법-Docker 호스트를 보호 하 고 CI Docker 벤치 마크를 준수 하는지 확인
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3f59bae9864e533270b0bfea829f64ccf541a8a3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301434"
---
# <a name="harden-your-docker-hosts"></a>Docker 호스트 강화

Azure Security Center IaaS Linux Vm 또는 Docker 컨테이너를 실행 하는 다른 Linux 컴퓨터에서 호스트 되는 관리 되지 않는 컨테이너를 식별 합니다. Security Center은 이러한 컨테이너의 구성을 지속적으로 평가 합니다. 그런 다음 이러한 항목을 [CIS (Internet Security) Docker 벤치 마크](https://www.cisecurity.org/benchmark/docker/)와 비교 합니다.

Security Center에는 CIS Docker 벤치 마크의 전체 규칙 집합이 포함 되며 컨테이너가 컨트롤을 충족 하지 않는 경우 경고를 표시 합니다. 구성이 잘못 되 면 Security Center에서 보안 권장 사항을 생성 합니다. 권장 사항을 확인 하 고 문제를 해결 하려면 Security Center의 **권장 사항 페이지** 를 사용 합니다.

취약점이 발견 되 면 단일 권장 사항 내에서 그룹화 됩니다.

>[!NOTE]
> 이러한 CIS 벤치 마크 확인은 AKS 관리 되는 인스턴스 또는 Databricks 관리 Vm에서 실행 되지 않습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|[서버용 Azure Defender](defender-for-servers-introduction.md) 가 필요 합니다.|
|필요한 역할 및 사용 권한:|호스트에서 연결 하는 작업 영역에 대 한 **읽기 권한자**|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/no-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Docker 구성에서 보안 취약점 식별 및 해결

1. Security Center의 메뉴에서 **권장** 구성 페이지를 엽니다.

1. **컨테이너 보안 구성의** 권장 사항 취약성에 대 한 필터링을 재구성 하 고 권장 사항을 선택 합니다.

    권장 사항 페이지는 영향을 받는 리소스 (Docker 호스트)를 표시 합니다. 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="컨테이너 보안 구성의 취약성을 해결 하기 위한 권장 사항 ":::

1. 특정 호스트가 실패 한 CI 컨트롤을 보고 수정 하려면 조사 하려는 호스트를 선택 합니다. 

    > [!TIP]
    > 자산 인벤토리 페이지에서 시작 하 여이 권장 사항에 도달한 경우 권장 사항 페이지의 **작업 수행** 단추를 선택 (.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="작업 단추를 사용 하 여 시작 Log Analytics":::

    사용자 지정 작업을 실행할 준비가 되 면 Log Analytics 열립니다. 기본 사용자 지정 쿼리는 문제를 해결 하는 데 도움이 되는 지침과 함께 평가 된 모든 실패 한 규칙의 목록을 포함 합니다.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="실패 한 모든 CI 컨트롤을 표시 하는 쿼리가 있는 Log Analytics 페이지":::

1. 필요한 경우 쿼리 매개 변수를 조정 합니다.

1. 명령이 적절 하 고 호스트에 대해 준비 되 면 **실행**을 선택 합니다.


## <a name="next-steps"></a>다음 단계

Docker 강화는 Security Center의 컨테이너 보안 기능 중 하나일 뿐입니다. 

[Security Center의 컨테이너 보안에](container-security.md)대해 자세히 알아보세요.