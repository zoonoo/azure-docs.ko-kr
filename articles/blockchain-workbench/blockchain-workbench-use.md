---
title: Azure Blockchain Workbench에서 응용 프로그램 사용
description: Azure Blockchain Workbench에서 응용 프로그램 계약을 사용하는 방법.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 35a45947c2766ccc5e64f4c3523ce163a9680680
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench에서 응용 프로그램 사용

Blockchain Workbench를 사용하여 계약에서 만들고 작업을 수행할 수 있습니다. 또한 상태와 트랜잭션 기록 같은 계약 세부 정보를 볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Blockchain Workbench 배포. 자세한 내용은 배포에 대한 세부 사항을 위한 [Azure Blockchain Workbench 배포](blockchain-workbench-deploy.md)를 참조하세요.
* Blockchain Workbench에 배포된 블록체인 응용 프로그램. [Azure Blockchain Workbench에서 블록체인 응용 프로그램 만들기]()를 참조하세요.

브라우저에서 [Blockchain Workbench를 엽니다](blockchain-workbench-deploy.md#blockchain-workbench-web-url).

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Blockchain Workbench의 구성원으로 로그인해야 합니다. 나열된 응용 프로그램이 없는 경우 Blockchain Workbench의 구성원이지만 모든 응용 프로그램의 구성원은 아닙니다. Blockchain Workbench 관리자는 응용 프로그램에 구성원을 할당할 수 있습니다.

## <a name="create-new-contract"></a>새 계약 만들기 

새 계약을 만들려면 **AllowedInstanceRoles** 역할의 구성원이 되어야 합니다. 

1. Blockchain Workbench 응용 프로그램 섹션에서 만들 계약이 포함된 응용 프로그램 타일을 선택합니다. 활성 계약 목록이 표시됩니다.

2. 새 계약을 만들려면 **새 계약**을 선택합니다.

    ![새 계약 단추](media/blockchain-workbench-use/contract-list.png)

3. **새 계약** 창이 표시됩니다. 초기 매개 변수 값을 지정합니다. **만들기**를 선택합니다.

    ![새 계약 창](media/blockchain-workbench-use/new-contract.png)

    새로 만든 계약이 다른 활성 계약이 포함된 목록에 표시됩니다.

    ![활성 계약 목록](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>계약에서 작업 수행

1. Blockchain Workbench 응용 프로그램 섹션에서 작업을 수행할 계약이 포함된 응용 프로그램 타일을 선택합니다.

    ![응용 프로그램 목록](media/blockchain-workbench-use/apps-list.png)

2. 목록에서 계약을 선택합니다.

    ![계약 목록](media/blockchain-workbench-use/select-contract.png)

    계약에 대한 세부 정보는 서로 다른 섹션에 표시됩니다. 

    ![계약 세부 정보](media/blockchain-workbench-use/contract-details.png)

    | 섹션  | 설명  |
    |---------|---------|
    | 상태 | 계약 단계 내에서 현재 진행 상태를 나열합니다. |
    | 세부 정보 | 계약의 현재 값 |
    | 조치 | 마지막 동작에 대한 세부 정보 |
    | 작업 | 계약의 트랜잭션 기록 |
    
3. **동작** 섹션에서 **작업 수행**을 선택합니다.

4. 계약의 현재 상태에 대한 세부 정보가 창에 표시됩니다. 드롭다운 목록에서 수행하려는 작업을 선택합니다. 

    ![작업 수행](media/blockchain-workbench-use/take-action.png)

5. **실행**을 선택하여 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 문제 해결 방법](blockchain-workbench-troubleshooting.md)