---
title: 연결된 서비스 보호
description: 관리 VNet을 사용하여 연결된 서비스를 프로비저닝하고 보호하는 방법 알아보기
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 6be76878a9a07c5f4a1e2a9348bb7b09cb1b10eb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567590"
---
# <a name="secure-a-linked-service-with-private-links"></a>Private Link를 사용하여 연결된 서비스 보호

이 문서에서는 프라이빗 엔드포인트를 사용하여 Synapse에서 연결된 서비스를 보호하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**: Azure Data Lake Gen2를 *원본* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md)에서 만들기 단계를 참조하세요. 스토리지 계정에 액세스하기 위한 Synapse Studio IP 필터링이 있는지 확인하고, **선택한 네트워크** 만 스토리지 계정에 액세스할 수 있도록 허용합니다. 블레이드 **방화벽 및 가상 네트워크** 아래의 설정은 아래 그림과 같이 표시됩니다.

![보안 스토리지 계정](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Private Link를 사용하여 연결된 서비스 만들기

Azure Synapse Analytics에서 연결된 서비스는 다른 서비스에 대한 연결 정보를 정의합니다. 이 섹션에서는 Azure Synapse Analytics 및 Azure Data Lake Gen2를 연결된 서비스로 추가합니다.

1. Azure Synapse Studio를 열고 **관리** 탭으로 이동합니다.
1. **외부 연결** 에서 **연결된 서비스** 를 선택합니다.
1. 연결된 서비스를 추가하려면 **새로 만들기** 를 선택합니다.
1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택하고 **계속** 을 선택합니다.
1. **대화형 작성** 을 사용하도록 설정해야 합니다. 사용하도록 설정하는 데 약 1분 정도 걸릴 수 있습니다. 
1. 인증 자격 증명을 입력합니다. 계정 키, 서비스 주체 및 관리 ID는 현재 지원되는 인증 유형입니다. 연결 테스트를 선택하여 자격 증명이 올바른지 확인합니다.
1. **연결 테스트** 를 선택하면 스토리지 계정에서 프라이빗 엔드포인트를 만들고 승인하지 않고 액세스할 수 없기 때문에 실패합니다. 오류 메시지에 **프라이빗 엔드포인트** 를 만드는 링크가 표시되며, 이 링크를 따라 다음 부분으로 이동할 수 있습니다. 해당 링크를 따라 다음 부분을 건너뜁니다.
1. 작업을 완료하면 **만들기** 를 선택합니다.

## <a name="create-a-managed-private-endpoint"></a>관리형 프라이빗 엔드포인트 만들기

위의 연결을 테스트할 때 하이퍼링크를 선택하지 않은 경우 다음 경로를 따릅니다. 위에서 만든 연결된 서비스에 연결하는 관리형 프라이빗 엔드포인트를 만듭니다.

1. **관리** 탭으로 이동합니다.
1. **관리형 Virtual Network** 섹션으로 이동합니다.
1. 관리형 프라이빗 엔드포인트 아래에서 **+ 새로 만들기** 를 선택합니다.
1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택하고 **계속** 을 선택합니다.
1. 위에서 만든 스토리지 계정의 이름을 입력합니다.
1. **만들기** 를 선택합니다.
1. 몇 초 정도 기다리면 만들어진 프라이빗 링크를 승인해야 한다고 표시됩니다.

## <a name="private-link-approval"></a>프라이빗 링크 승인
1. 위에서 만든 프라이빗 엔드포인트를 선택합니다. 스토리지 계정 수준에서 프라이빗 엔드포인트를 승인하도록 하는 하이퍼링크가 표시될 수 있습니다. *대안은 Azure Portal 스토리지 계정으로 직접 이동하여 **프라이빗 엔드포인트 연결** 블레이드로 이동하는 것입니다.*
1. Studio에서 만든 프라이빗 엔드포인트, **승인** 을 차례로 선택합니다.
1. 설명을 추가하고 **예** 를 선택합니다.
1. **관리** 탭의 **관리형 Virtual Network** 섹션에서 Synapse Studio로 돌아갑니다.
1. 프라이빗 엔드포인트에 대한 승인을 반영하는 데 약 1분 정도 걸립니다.

## <a name="check-the-connection-works"></a>연결이 작동하는지 확인합니다.
1. **관리** 탭으로 이동하여 만든 연결된 서비스를 선택합니다.
1. **대화형 작성** 이 활성화되어 있는지 확인합니다.
1. **연결 테스트** 를 클릭합니다. 연결이 성공한 것으로 표시됩니다.

이제 Synapse와 연결된 서비스 간에 안전한 비공개 연결을 설정했습니다.

## <a name="next-steps"></a>다음 단계


Azure Synapse Analytics에서 관리되는 프라이빗 엔드포인트에 대해 더 자세히 알아보려면 [관리되는 프라이빗 엔드포인트](../security/synapse-workspace-managed-private-endpoints.md)를 참조하세요.


Azure Synapse Analytics의 데이터 통합에 대한 자세한 내용은 [Data Lake로 데이터 수집](data-integration-data-lake.md) 문서를 참조하세요.