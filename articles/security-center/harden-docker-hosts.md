---
title: Azure Security Center를 사용 하 여 Docker 호스트를 강화 하 고 컨테이너를 보호 합니다.
description: 방법-Docker 호스트를 보호 하 고 CI Docker 벤치 마크를 준수 하는지 확인
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 500fa45db7e0e6bffb587d9d352ee1ab49f14703
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712275"
---
# <a name="harden-your-docker-hosts"></a>Docker 호스트 강화

Azure Security Center는 IaaS Linux VM 또는 Docker 컨테이너를 실행하는 다른 Linux 머신에 호스트되는 관리되지 않는 컨테이너를 식별합니다. Security Center는 이러한 컨테이너의 구성을 지속적으로 평가합니다. 그런 다음, 이러한 항목을 [CIS(Center for Internet Security) Docker 벤치마크](https://www.cisecurity.org/benchmark/docker/)와 비교합니다.

Security Center는 CIS Docker 벤치마크의 전체 규칙 세트를 포함하며 컨테이너가 규칙을 위반할 경우 경고를 표시합니다. Security Center는 잘못된 구성을 발견하면 보안 권장 사항을 생성합니다. Security Center의 **권장 사항 페이지**를 사용하여 권장 사항을 살펴보고 문제를 해결하세요.

취약점이 발견 되 면 단일 권장 사항 내에서 그룹화 됩니다.

>[!NOTE]
> 이러한 CIS 벤치 마크 확인은 AKS 관리 되는 인스턴스 또는 Databricks 관리 Vm에서 실행 되지 않습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Azure Defender](defender-for-servers-introduction.md) 필요|
|필요한 역할 및 권한:|호스트에서 연결 하는 작업 영역에 대 한 **읽기 권한자**|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
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
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="컨테이너 보안 구성의 취약성을 해결 하기 위한 권장 사항 ":::

    사용자 지정 작업을 실행할 준비가 되 면 Log Analytics 열립니다. 기본 사용자 지정 쿼리는 문제를 해결 하는 데 도움이 되는 지침과 함께 평가 된 모든 실패 한 규칙의 목록을 포함 합니다.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="컨테이너 보안 구성의 취약성을 해결 하기 위한 권장 사항 ":::

1. 필요한 경우 쿼리 매개 변수를 조정 합니다.

1. 명령이 적절 하 고 호스트에 대해 준비 되 면 **실행**을 선택 합니다.


## <a name="next-steps"></a>다음 단계

Docker 강화는 Security Center의 컨테이너 보안 기능 중 하나일 뿐입니다. 

[Security Center의 컨테이너 보안에](container-security.md)대해 자세히 알아보세요.