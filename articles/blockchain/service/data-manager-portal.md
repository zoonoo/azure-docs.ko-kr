---
title: Blockchain Data Manager 구성-Azure Portal
description: Azure Portal를 사용 하 여 블록 체인 Data Manager를 만들고 관리 하는 방법
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 1f46fe92fd6650daa3ba4b9a930c4d781925d3fc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518256"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Azure Portal를 사용 하 여 블록 체인 Data Manager 구성

Blockchain 데이터를 캡처하여 Azure Event Grid 토픽으로 전송 하도록 Azure Blockchain 서비스에 대 한 Blockchain Data Manager를 구성 합니다.

Blockchain Data Manager 인스턴스를 구성 하려면 다음을 수행 합니다.

* Azure Blockchain 서비스 트랜잭션 노드에 대 한 Blockchain Data Manager 인스턴스 만들기
* 블록 체인 응용 프로그램 추가

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 완료: Azure Portal 또는 [빠른 시작: Azure CLI를 사용 하 여 Azure Blockchain 서비스 blockchain 구성원 만들기](create-member-cli.md) 를 [사용 하 여 블록 체인 구성원 만들기](create-member.md)
* [Event Grid 토픽](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) 만들기
* [Azure Event Grid에서 이벤트 처리기](../../event-grid/event-handlers.md) 에 대해 알아보기

## <a name="create-instance"></a>인스턴스 만들기

Blockchain Data Manager 인스턴스는 Azure Blockchain 서비스 트랜잭션 노드를 연결 하 고 모니터링 합니다. 트랜잭션 노드에 대 한 액세스 권한이 있는 사용자만 연결을 만들 수 있습니다. 인스턴스는 트랜잭션 노드에서 모든 원시 블록 및 원시 트랜잭션 데이터를 캡처합니다.

아웃 바운드 연결은 blockchain 데이터를 Azure Event Grid 보냅니다. 인스턴스를 만들 때 단일 아웃 바운드 연결을 구성 합니다. Blockchain Data Manager는 지정 된 Blockchain Data Manager 인스턴스에 대해 여러 Event Grid 토픽 아웃 바운드 연결을 지원 합니다. 블록 체인 데이터를 단일 대상으로 전송 하거나 블록 체인 데이터를 여러 대상으로 보낼 수 있습니다. 다른 대상을 추가 하려면 인스턴스에 대 한 아웃 바운드 연결을 추가 하면 됩니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. Blockchain Data Manager에 연결 하려는 Azure Blockchain 서비스 구성원으로 이동 합니다. **Blockchain Data Manager**를 선택 합니다.
1. **추가**를 선택합니다.

    ![블록 체인 Data Manager 추가](./media/data-manager-portal/add-instance.png)

    다음 세부 정보를 입력합니다.

    설정 | 설명
    --------|------------
    Name | 연결 된 Blockchain Data Manager의 고유 이름을 입력 합니다. Blockchain Data Manager 이름에는 소문자와 숫자가 포함 될 수 있으며 최대 길이는 20 자입니다.
    트랜잭션 노드 | 트랜잭션 노드를 선택 합니다. 읽기 권한이 있는 트랜잭션 노드만 나열 됩니다.
    연결 이름 | Blockchain 트랜잭션 데이터가 전송 되는 아웃 바운드 연결의 고유 이름을 입력 합니다.
    Event grid 끝점 | Blockchain Data Manager 인스턴스와 동일한 구독에서 event grid 항목을 선택 합니다.

1. **확인**을 선택합니다.

    블록 체인 Data Manager 인스턴스를 만드는 데 1 분 미만이 걸립니다. 인스턴스가 배포 된 후에는 자동으로 시작 됩니다. 실행 중인 Blockchain Data Manager 인스턴스는 트랜잭션 노드에서 Blockchain 이벤트를 캡처하고 아웃 바운드 연결에 데이터를 보냅니다. 또한 트랜잭션 노드에서 디코딩된 이벤트 및 속성 데이터를 캡처하려면 Blockchain Data Manager 인스턴스에 대 한 블록 체인 응용 프로그램을 만듭니다.

    새 인스턴스는 Azure Blockchain 서비스 구성원의 블록 체인 Data Manager 인스턴스 목록에 표시 됩니다.

    ![Blockchain 데이터 멤버 인스턴스 목록](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>블록 체인 응용 프로그램 추가

Blockchain 응용 프로그램을 추가 하는 경우 Blockchain Data Manager 응용 프로그램에 대 한 이벤트 및 속성 상태를 디코딩합니다. 그렇지 않으면 원시 블록 및 원시 트랜잭션 데이터만 전송 됩니다. Blockchain Data Manager 계약을 배포할 때 계약 주소도 검색 합니다. 블록 체인 Data Manager 인스턴스에 여러 블록 체인 응용 프로그램을 추가할 수 있습니다.

Blockchain Data Manager에는 응용 프로그램을 추가 하는 스마트 계약 ABI 및 바이트 코드 파일이 필요 합니다.

### <a name="get-contract-abi-and-bytecode"></a>계약 ABI 및 바이트 코드 가져오기

계약 ABI는 스마트 계약 인터페이스를 정의 합니다. 스마트 계약과 상호 작용 하는 방법을 설명 합니다. [Ethereum 확장에 대 한 Azure Blockchain 개발 키트](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) 를 사용 하 여 계약 ABI를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 농담 프로젝트의 **빌드/계약** 폴더를 확장 합니다.
1. 계약 메타 데이터 JSON 파일을 마우스 오른쪽 단추로 클릭 합니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. **계약 ABI 복사**를 선택 합니다.

    ![복사 계약 ABI 선택이 포함 된 Visual Studio Code 창](./media/data-manager-portal/abi-devkit.png)

    계약 ABI는 클립보드로 복사 됩니다.

1. **Abi** 배열을 JSON 파일로 저장 합니다. 예: *abi*. 이후 단계에서 파일을 사용 합니다.

계약 바이트 코드는 Ethereum 가상 머신에서 실행 하는 컴파일된 스마트 계약입니다. 확장을 사용 하 여 계약 바이트 코드를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 농담 프로젝트의 **빌드/계약** 폴더를 확장 합니다.
1. 계약 메타 데이터 JSON 파일을 마우스 오른쪽 단추로 클릭 합니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. **계약 바이트 코드 복사**를 선택 합니다.

    ![복사본 계약 바이트 코드를 선택 하는 Visual Studio Code 창](./media/data-manager-portal/bytecode-devkit.png)

    계약 바이트 코드는 클립보드에 복사 됩니다.

1. **바이트 코드** 값을 JSON 파일로 저장 합니다. 예를 들면 *바이트 코드*입니다. 16 진수 값만 저장 합니다. 이후 단계에서 파일을 사용 합니다.

다음 예제에서는 VS Code 편집기에 열려 있는 *abi* 및 *바이트 코드의 json* 파일을 보여 줍니다. 파일이 유사 하 게 표시 됩니다.

![Abi 및 바이트 코드 json 파일의 예](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>계약 ABI 및 바이트 코드 URL 만들기

Blockchain Data Manager을 사용 하려면 응용 프로그램을 추가할 때 URL에서 계약 ABI 및 바이트 코드 파일에 액세스할 수 있어야 합니다. Azure Storage 계정을 사용 하 여 개인적으로 액세스할 수 있는 URL을 제공할 수 있습니다.

#### <a name="create-storage-account"></a>스토리지 계정 만들기

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>계약 파일 업로드

1. 저장소 계정에 대 한 새 컨테이너를 만듭니다. **컨테이너 > 컨테이너**를 선택 합니다.

    ![저장소 계정 컨테이너 만들기](./media/data-manager-portal/create-container.png)

    | 필드 | 설명 |
    |-------|-------------|
    | Name  | 컨테이너의 이름을로 합니다. 예: *smartcontract* |
    | 공용 액세스 수준 | *비공개 (익명 액세스 없음)를* 선택 합니다. |

1. **확인**을 선택하여 컨테이너를 만듭니다.
1. 컨테이너를 선택 하 고 **업로드**를 선택 합니다.
1. [계약 ABI 및 바이트 코드 가져오기](#get-contract-abi-and-bytecode) 섹션에서 만든 JSON 파일을 모두 선택 합니다.

    ![Blob 업로드](./media/data-manager-portal/upload-blobs.png)

    **업로드**를 선택합니다.

#### <a name="generate-url"></a>URL 생성

각 blob에 대해 공유 액세스 서명을 생성 합니다.

1. ABI JSON blob을 선택 합니다.
1. **SAS 생성** 을 선택 합니다.
1. 원하는 액세스 서명 만료 **를 설정 하 고 BLOB SAS 토큰 및 URL 생성**을 선택 합니다.

    ![SAS 토큰 생성](./media/data-manager-portal/generate-sas.png)

1. **BLOB SAS URL** 을 복사 하 여 다음 섹션에 저장 합니다.
1. 바이트 코드 JSON blob에 대 한 [URL 생성](#generate-url) 단계를 반복 합니다.

### <a name="add-application-to-instance"></a>인스턴스에 응용 프로그램 추가

1. 인스턴스 목록에서 Blockchain Data Manager 인스턴스를 선택 합니다.
1. **블록 체인 응용 프로그램**을 선택 합니다.
1. **추가**를 선택합니다.

    ![블록 체인 응용 프로그램 추가](./media/data-manager-portal/add-application.png)

    블록 체인 응용 프로그램의 이름과 스마트 계약 ABI 및 바이트 코드 Url을 입력 합니다.

    설정 | 설명
    --------|------------
    Name | 추적할 blockchain 응용 프로그램의 고유 이름을 입력 합니다.
    ABI 계약 | 계약 ABI 파일에 대 한 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조 하세요.
    계약 바이트 코드 | 바이트 코드 파일에 대 한 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조 하세요.

1. **확인**을 선택합니다.

    응용 프로그램을 만들면 블록 체인 응용 프로그램 목록에 응용 프로그램이 표시 됩니다.

    ![블록 체인 응용 프로그램 목록](./media/data-manager-portal/artifact-list.png)

Azure Storage 계정을 삭제 하거나,이를 사용 하 여 더 많은 블록 체인 응용 프로그램을 구성할 수 있습니다. Azure Storage 계정을 삭제 하려면 리소스 그룹을 삭제 하면 됩니다. 리소스 그룹을 삭제하면 연결된 스토리지 계정과 기타 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

## <a name="stop-instance"></a>인스턴스 중지

블록 체인 이벤트 캡처를 중지 하 고 아웃 바운드 연결에 데이터를 전송 하려는 경우 Blockchain 관리자 인스턴스를 중지 합니다. 인스턴스가 중지 되 면 Blockchain Data Manager에 대 한 요금이 발생 하지 않습니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/blockchain-service)을 참조하세요.

1. **개요** 로 이동 하 고 **중지**를 선택 합니다.

    ![인스턴스 중지](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>다음 단계

[Azure Event Grid에서 이벤트 처리기](../../event-grid/event-handlers.md)에 대해 자세히 알아보세요.
