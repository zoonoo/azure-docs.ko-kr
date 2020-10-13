---
title: 연결 된 서비스 보안
description: 관리 VNet을 사용 하 여 연결 된 서비스를 프로 비전 하 고 보호 하는 방법을 알아봅니다.
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f5a3c73d60f038820de100f99c554eec27fd6f55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033186"
---
# <a name="secure-a-linked-service-with-private-links"></a>개인 링크를 사용 하 여 연결 된 서비스 보호 

이 문서에서는 개인 끝점을 사용 하 여 Synapse에서 연결 된 서비스를 보호 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**: Azure Data Lake Gen2를 *원본* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../../storage/blobs/data-lake-storage-quickstart-create-account.md)에서 만들기 단계를 참조하세요. 저장소 계정에 액세스 하기 위한 Synapse Studio IP 필터링이 있는지 확인 하 고, **선택한 네트워크** 만 저장소 계정에 액세스할 수 있도록 허용 합니다. 블레이드 **방화벽 및 가상 네트워크** 아래의 설정은 아래 그림과 같이 표시 됩니다.

![보안 저장소 계정](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>개인 링크를 사용 하 여 연결 된 서비스 만들기

Azure Synapse Analytics에서 연결된 서비스는 다른 서비스에 대한 연결 정보를 정의합니다. 이 섹션에서는 Azure Synapse Analytics 및 Azure Data Lake Gen2를 연결된 서비스로 추가합니다.

1. Azure Synapse Studio를 열고 **관리** 탭으로 이동 합니다.
1. **외부 연결**에서 **연결된 서비스**를 선택합니다.
1. 연결 된 서비스를 추가 하려면 **새로 만들기**를 선택 합니다.
1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택 하 고 **계속**을 선택 합니다.
1. **대화형 작성**을 사용하도록 설정해야 합니다. 사용하도록 설정하는 데 약 1분 정도 걸릴 수 있습니다. 
1. 인증 자격 증명을 입력합니다. 계정 키, 서비스 주체 및 관리 ID는 현재 지원되는 인증 유형입니다. 연결 테스트를 선택 하 여 자격 증명이 올바른지 확인 합니다.
1. **연결 테스트**를 선택 하면 저장소 계정에서 개인 끝점을 만들고 승인 하지 않고 액세스할 수 없기 때문에 실패 합니다. 오류 메시지에는 다음 부분으로 이동 하기 위해 수행할 수 있는 **개인 끝점** 을 만들기 위한 링크가 표시 됩니다. 해당 링크를 팔 로우 하는 경우 다음 부분을 건너뜁니다.
1. 작업을 완료하면 **만들기**를 선택합니다.

## <a name="create-a-managed-private-endpoint"></a>관리형 프라이빗 엔드포인트 만들기

위의 연결을 테스트할 때 하이퍼링크를 선택 하지 않은 경우 다음 경로를 따르세요. 위에서 만든 연결 된 서비스에 연결할 관리 되는 개인 끝점을 만듭니다.

1. **관리** 탭으로 이동합니다.
1. **관리 되는 가상 네트워크** 섹션으로 이동 합니다.
1. 관리 되는 개인 끝점에서 **+ 새로 만들기** 를 선택 합니다.
1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택 하 고 **계속**을 선택 합니다.
1. 위에서 만든 스토리지 계정의 이름을 입력합니다.
1. **만들기**
1. 몇 초 정도 기다리면 만들어진 프라이빗 링크를 승인해야 한다고 표시됩니다.

## <a name="private-link-approval"></a>개인 링크 승인
1. 위에서 만든 프라이빗 엔드포인트를 선택합니다. 저장소 계정 수준에서 개인 끝점을 승인할 수 있도록 하는 하이퍼링크가 표시 됩니다. *대신 Azure Portal Storage 계정으로 직접 이동 하 여 **개인 끝점 연결** 블레이드로 이동 합니다.*
1. Studio에서 만든 개인 끝점을 틱 하 고 **승인**을 선택 합니다.
1. 설명을 추가 하 고 **예** 를 선택 합니다.
1. [ **관리** ] 탭의 [ **관리 되는 가상 네트워크** ] 섹션에서 Synapse Studio로 돌아갑니다.
1. 개인 끝점에 대 한 승인을 반영 하는 데 1 분 정도가 소요 됩니다.

## <a name="check-the-connection-works"></a>연결 작동 확인
1. **관리** 탭으로 이동 하 여 만든 연결 된 서비스를 선택 합니다.
1. **대화형 작성** 이 활성화 되어 있는지 확인 합니다.
1. **연결 테스트**를 클릭합니다. 연결이 성공 하는 것을 볼 수 있습니다.

이제 Synapse와 연결 된 서비스 간에 안전 하 고 개인 연결을 설정 했습니다.

## <a name="next-steps"></a>다음 단계

Synapse Analytics에서 관리 되는 개인 끝점에 대 한 추가 이해를 개발 하려면 [Synapse 관리 개인 끝점 관련 개념](data-integration-data-lake.md) 문서를 참조 하세요.

Synapse Analytics의 데이터 통합에 대 한 자세한 내용은 [수집 data to a Data Lake](data-integration-data-lake.md) 문서를 참조 하세요.
