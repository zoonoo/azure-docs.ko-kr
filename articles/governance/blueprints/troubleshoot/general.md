---
title: Azure Blueprints를 사용하여 오류 문제 해결
description: 청사진 만들기 및 할당과 관련된 문제를 해결하는 방법을 알아봅니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982293"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Azure Blueprints를 사용하여 오류 문제 해결

청사진을 만들거나 할당할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이러한 오류를 해결하는 방법에 대해 설명합니다.

## <a name="finding-error-details"></a>오류 세부 정보 찾기

대부분의 오류는 청사진을 범위에 할당한 결과입니다. 할당이 실패하면 청사진에서 실패한 배포에 대한 세부 정보를 제공합니다. 이 정보는 해결되면 후속 배포가 성공할 수 있는 문제를 나타냅니다.

1. Azure Portal에서 **모든 서비스**를 클릭하고, 왼쪽 창에서 **정책**을 검색하여 선택함으로써 Azure Blueprints 서비스를 시작합니다. **정책** 페이지에서 **Blueprints**를 클릭합니다.

1. 페이지의 왼쪽에서 **할당된 Blueprint**을 선택하고, 검색 상자를 통해 청사진 할당을 필터링하여 실패한 할당을 찾습니다. 실패한 모든 할당을 그룹화하여 표시하도록 **프로비전 상태** 열을 기준으로 할당 테이블을 정렬할 수도 있습니다.

1. _실패_ 상태인 청사진을 마우스 왼쪽 단추로 클릭하거나, 마우스 오른쪽 단추로 클릭하여 **할당 정보 보기**를 선택합니다.

1. 청사진 할당 페이지의 위쪽에는 실패한 할당임을 알려주는 경고의 빨간색 배너가 있습니다. 자세한 내용을 보려면 배너의 아무 곳을 클릭합니다.

일반적으로 청사진 전체가 아니라 청사진에 포함된 아티팩트로 인해 오류가 발생합니다. 예를 들어 청사진에 Key Vault를 만드는 아티팩트가 포함되어 있지만 Azure 정책에 따라 Key Vault를 만들 수 없는 경우 전체 할당이 실패합니다.

## <a name="general-errors"></a>일반 오류

### <a name="policy-violation"></a>시나리오: 정책 위반

#### <a name="issue"></a>문제

정책 위반으로 인해 템플릿을 배포하지 못했습니다.

#### <a name="cause"></a>원인

정책은 다음과 같은 여러 가지 이유로 배포와 충돌할 수 있습니다.

- 만드는 리소스는 정책(일반적으로 SKU 또는 위치 제한)으로 제한되어 있습니다.
- 배포에서 정책(태그와 함께 사용)으로 구성된 필드를 설정하고 있습니다.

#### <a name="resolution"></a>해결 방법

오류 정보에 나열된 정책과 충돌하지 않도록 청사진을 조정합니다. 이 방법이 가능하지 않으면 대체 옵션으로 정책 할당의 범위를 변경하여 청사진이 더 이상 정책과 충돌하지 않도록 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우, 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
- [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
- 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.