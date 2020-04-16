---
title: Azure 시냅스 분석에서 연결된 서비스 프로비저닝 및 보안
description: 관리형 Vnet으로 연결된 서비스를 프로비저닝하고 보호하는 방법 알아보기
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430553"
---
# <a name="securing-a-linked-service-with-private-links"></a>개인 링크로 연결된 서비스 보안 

이 문서에서는 개인 끝점을 사용하여 Synapse에서 연결된 서비스를 보호하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 없는 경우 시작하기 전에 [무료 Azure 계정을](https://azure.microsoft.com/free/) 만듭니다.
* **Azure 저장소 계정**: Azure Data Lake Gen 2를 *원본* 데이터 저장소로 사용합니다. 저장소 계정이 없는 경우 [Azure Storage 계정 만들기를](../../storage/blobs/data-lake-storage-quickstart-create-account.md) 참조하여 계정을 만드는 단계를 확인합니다. 저장소 계정에 액세스하기 위한 Synapse Studio IP 필터링이 있는지, **선택한 네트워크만** 저장소 계정에 액세스할 수 있도록 허용해야 합니다. 블레이드 **방화벽 및 가상 네트워크** 아래의 설정은 아래 그림과 같아야 합니다.

![보안 저장소 계정](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>비공개 링크로 연결된 서비스 만들기

Azure Synapse Analytics에서 연결된 서비스는 다른 서비스에 대한 연결 정보를 정의하는 곳입니다. 이 섹션에서는 Azure 시냅스 분석 및 Azure Data Lake Gen 2를 연결된 서비스로 추가합니다.

1. Azure 시냅스 스튜디오를 열고 **관리** 탭으로 이동합니다.
1. **외부 연결에서** **연결된 서비스를**선택합니다.
1. 연결된 서비스를 추가하려면 **새**을 클릭합니다.
1. 목록에서 Azure 데이터 호수 저장소 Gen2 타일을 선택하고 **계속을**클릭합니다.
1. **대화형 작성을**사용하도록 설정해야 합니다. 활성화하는 데 약 1분이 걸릴 수 있습니다. 
1. 인증 자격 증명을 입력합니다. 계정 키, 서비스 주체 및 관리되는 ID는 현재 지원되는 인증 유형입니다. 테스트 연결을 클릭하여 자격 증명이 올바른지 확인합니다.
1. **테스트 연결을**선택하면 저장소 계정이 개인 끝점을 만들고 승인하지 않고 액세스 권한을 부여하지 않으므로 실패해야 합니다. 오류 메시지에다음 부분으로 이동하기 위해 따를 수 있는 **개인 끝점을** 만드는 링크가 표시됩니다. 해당 링크를 따르는 경우 다음 부분을 건너뜁니다.
1. 작업을 완료하면 **만들기**를 선택합니다.

## <a name="create-a-managed-private-endpoint"></a>관리되는 개인 끝점 만들기

위의 연결을 테스트할 때 하이퍼링크를 클릭하지 않은 경우 다음 경로를 따르십시오. 이제 위에서 만든 연결된 서비스에 연결할 관리되는 개인 끝점을 만들어야 합니다.

1. **관리** 탭으로 이동합니다.
1. **관리되는 가상 네트워크** 섹션으로 이동합니다.
1. 관리되는 비공개 끝점에서 **+ 새** 것을 선택합니다.
1. 목록에서 Azure Data Lake 저장소 Gen2 타일을 선택하고 **계속을**선택합니다.
1. 위에서 만든 저장소 계정의 이름을 입력합니다.
1. **만들기** 선택
1. 몇 초 동안 기다린 후에 만든 개인 링크가 승인이 필요한지 알 수 있습니다.

## <a name="approval-of-a-private-link"></a>비공개 링크 승인
1. 위에서 만든 비공개 끝점을 선택합니다. 저장소 계정 수준에서 비공개 엔드포인트를 승인할 수 있는 하이퍼링크를 볼 수 있습니다. *다른 방법은 Azure 포털 저장소 계정으로 직접 이동하여 **개인 끝점 연결** 블레이드로 이동하는 것입니다.*
1. 스튜디오에서 만든 비공개 끝점을 선택하고 **승인을**선택합니다.
1. 설명을 추가하고 **'예'를** 클릭합니다.
1. **관리**탭의 **관리되는 가상 네트워크** 섹션 아래에 있는 Synapse Studio로 돌아갑니다.
1. 개인 끝점에 대한 승인을 반영하는 데 약 1분이 소요됩니다.

## <a name="check-the-connection-works"></a>연결 작동 확인
1. **관리** 탭으로 이동하여 만든 연결된 서비스를 선택합니다.
1. **대화형 작성이** 활성 상태인지 확인합니다.
1. **연결 테스트**를 클릭합니다. 연결이 성공한 것을 볼 수 있습니다.

이제 Synapse와 연결된 서비스 간에 안전하고 사적인 연결을 설정했습니다!

## <a name="next-steps"></a>다음 단계

Synapse Analytics에서 관리되는 개인 끝점에 대한 추가 이해를 개발하려면 [Synapse 관리형 개인 끝점 문서와 관련된 개념을 참조하세요.](data-integration-data-lake.md)

Synapse Analytics의 데이터 통합에 대한 자세한 내용은 데이터 레이크 문서에 [데이터를 수집하십시오.](data-integration-data-lake.md)