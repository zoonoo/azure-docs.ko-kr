---
title: Azure Logic Apps에서 Ethereum Blockchain 커넥터 사용
description: Azure Logic Apps와 함께 Ethereum Blockchain 커넥터를 사용 하 여 스마트 계약 함수를 트리거하고 스마트 계약 이벤트에 응답 합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: bb23d6b9b42e1c51646765255870a14a1b5d39f7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579931"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Azure Logic Apps에서 Ethereum Blockchain 커넥터 사용

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 와 함께 [Ethereum blockchain 커넥터](https://docs.microsoft.com/connectors/blockchainethereum/) 를 사용 하 여 스마트 계약 작업을 수행 하 고 스마트 계약 이벤트에 응답 합니다. 예를 들어 blockchain 원장에서 정보를 반환 하는 REST 기반 마이크로 서비스을 만들려고 한다고 가정해 보겠습니다. 논리 앱을 사용 하 여 블록 체인 원장에 저장 된 정보를 쿼리 하는 HTTP 요청을 받을 수 있습니다.

## <a name="prerequisites"></a>필수 조건

선택적 필수 조건 [빠른 시작: Visual Studio Code을 사용 하 여 Azure Blockchain Service consortium 네트워크에 연결](connect-vscode.md)합니다. 빠른 시작에서는 [Ethereum 용 Azure Blockchain 개발 키트](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) 를 설치 하 고 Blockchain 개발 환경을 설정 하는 방법을 안내 합니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

Azure Logic Apps는 시스템 및 서비스를 통합 해야 할 때 비즈니스 프로세스 및 워크플로를 예약 하 고 자동화 하는 데 도움이 됩니다. 먼저 Ethereum Blockchain 커넥터를 사용 하는 논리를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **통합** > **논리 앱**을 선택합니다.
1. **논리 앱 만들기**에서 논리 앱을 만들 위치에 대 한 세부 정보를 제공 합니다. 완료되면 **만들기**를 선택합니다.

    논리 앱을 만드는 방법에 대 한 자세한 내용은 [Azure Logic Apps를 사용 하 여 자동화 된 워크플로 만들기](../../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

1. Azure가 앱을 배포한 후 논리 앱 리소스를 선택 합니다.
1. Logic Apps 디자이너의 **템플릿**에서 **빈 논리 앱**을 선택 합니다.

모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 트리거가 발생될 때마다 Logic Apps 엔진은 워크플로를 시작하고 실행하는 논리 앱 인스턴스를 만듭니다.

Ethereum Blockchain 커넥터에는 하나의 트리거와 여러 작업이 있습니다. 사용할 트리거 또는 작업은 시나리오에 따라 달라 집니다.

워크플로의 경우:

* 블록 체인에서 이벤트가 발생할 때 트리거 [이벤트 트리거를 사용](#use-the-event-trigger)합니다.
* 스마트 계약을 쿼리하거나 배포 하 고 [작업을 사용](#use-actions)합니다.
* 일반적인 시나리오를 따르고 [개발자 키트를 사용 하 여 워크플로를 생성](#generate-a-workflow)합니다.

## <a name="use-the-event-trigger"></a>이벤트 트리거 사용

스마트 계약 이벤트가 발생 한 후 논리 앱을 실행 하려면 Ethereum Blockchain 이벤트 트리거를 사용 합니다. 예를 들어 스마트 계약 함수가 호출 될 때 전자 메일을 보내려고 합니다.

1. Logic Apps 디자이너에서 Ethereum Blockchain 커넥터를 선택 합니다.
1. **트리거** 탭에서 **스마트 계약 이벤트가 발생**하는 경우를 선택 합니다.
1. Azure Blockchain 서비스에 대 한 [API 연결](#create-an-api-connection) 을 변경 하거나 만듭니다.
1. 이벤트를 확인 하려는 스마트 계약에 대 한 세부 정보를 입력 합니다.

    ![이벤트 트리거 속성이 있는 Logic Apps 디자이너](./media/ethereum-logic-app/event-properties.png)

    | 속성 | 설명 |
    |----------|-------------|
    | **ABI 계약** | 계약 ABI (응용 프로그램 이진 인터페이스)는 스마트 계약 인터페이스를 정의 합니다. 자세한 내용은 [계약 ABI 가져오기](#get-the-contract-abi)를 참조 하세요. |
    | **스마트 계약 주소** | 계약 주소는 Ethereum blockchain의 스마트 계약 대상 주소입니다. 자세한 내용은 [계약 주소 가져오기](#get-the-contract-address)를 참조 하세요. |
    | **이벤트 이름** | 확인할 스마트 계약 이벤트를 선택 합니다. 이 이벤트는 논리 앱을 트리거합니다. |
    | **간격** 및 **빈도** | 이벤트를 확인 하려는 빈도를 선택 합니다. |

1. **저장**을 선택합니다.

논리 앱을 완료 하기 위해 Ethereum Blockchain 이벤트 트리거를 기반으로 작업을 수행 하는 새 단계를 추가할 수 있습니다. 예를 들어 전자 메일을 보냅니다.

## <a name="use-actions"></a>작업 사용

논리 앱이 Blockchain 원장에서 작업을 수행 하도록 하려면 Ethereum Blockchain 작업을 사용 합니다. 예를 들어 논리 앱에 대 한 HTTP 요청이 있을 때 스마트 계약 함수를 호출 하는 REST 기반 마이크로 서비스를 만들려고 합니다.

커넥터 작업에는 트리거가 필요 합니다. 마이크로 서비스에 대 한 HTTP 요청 트리거와 같이 Ethereum Blockchain 커넥터 작업을 트리거 뒤의 다음 단계로 사용할 수 있습니다.

1. Logic Apps 디자이너에서 트리거 다음의 **새 단계** 를 선택 합니다.
1. Ethereum Blockchain 커넥터를 선택 합니다.
1. **작업** 탭에서 사용 가능한 작업 중 하나를 선택 합니다.

    ![작업 속성이 있는 Logic Apps 디자이너](./media/ethereum-logic-app/action-properties.png)

1. Azure Blockchain 서비스에 대 한 [API 연결](#create-an-api-connection) 을 변경 하거나 만듭니다.
1. 선택한 작업에 따라 스마트 계약 함수에 대해 다음과 같은 세부 정보를 제공 합니다.

    | 속성 | 설명 |
    |----------|-------------|
    | **ABI 계약** | 계약 ABI는 스마트 계약 인터페이스를 정의 합니다. 자세한 내용은 [계약 ABI 가져오기](#get-the-contract-abi)를 참조 하세요. |
    | **계약 바이트 코드** | 컴파일된 스마트 계약 바이트 코드입니다. 자세한 내용은 [계약 바이트 코드 가져오기](#get-the-contract-bytecode)를 참조 하세요. |
    | **스마트 계약 주소** | 계약 주소는 Ethereum blockchain의 스마트 계약 대상 주소입니다. 자세한 내용은 [계약 주소 가져오기](#get-the-contract-address)를 참조 하세요. |
    | **스마트 계약 함수 이름** | 동작에 대 한 스마트 계약 함수 이름을 선택 합니다. 목록은 계약 ABI의 세부 정보에서 채워집니다. |

    스마트 계약 함수 이름을 선택한 후에는 함수 매개 변수에 대 한 필수 필드가 표시 될 수 있습니다. 시나리오에 필요한 값 또는 동적 콘텐츠를 입력 합니다.

이제 논리 앱을 사용할 수 있습니다. 논리 앱 이벤트가 트리거되면 Ethereum Blockchain 동작이 실행 됩니다. 예를 들어, HTTP 요청 트리거는 Ethereum blockchain 작업을 실행 하 여 스마트 계약 상태 값을 쿼리 합니다. 이 쿼리는 값을 반환 하는 HTTP 응답을 생성 합니다.

## <a name="generate-a-workflow"></a>워크플로 생성

Ethereum Visual Studio Code 확장에 대 한 Azure Blockchain 개발 키트는 일반적인 시나리오에 대 한 논리 앱 워크플로를 생성할 수 있습니다. 다음 네 가지 시나리오를 사용할 수 있습니다.

* Azure SQL Database 인스턴스에 데이터 게시
* Azure Event Grid 또는 Azure Service Bus의 인스턴스에 대 한 이벤트 게시
* 보고서 게시
* REST 기반 마이크로 서비스

 Azure Blockchain 개발 키트는 Truffle을 사용 하 여 블록 체인 개발을 간소화 합니다. 스마트 계약을 기반으로 논리 앱을 생성 하려면 스마트 계약에 대 한 Truffle 솔루션이 필요 합니다. 또한 Azure Blockchain Service consortium 네트워크에 연결 해야 합니다. 자세한 내용은 [Visual Studio Code를 사용 하 여 Azure Blockchain Service consortium 네트워크 빠른 시작에 연결](connect-vscode.md)을 참조 하세요.

예를 들어 다음 단계 **에서는 빠른 시작** 기능을 기반으로 하는 REST 기반 마이크로 서비스 논리 앱을 생성 합니다.

1. Visual Studio Code 탐색기 사이드바에서 솔루션의 **계약** 폴더를 확장 합니다.
1. 마우스 오른쪽 단추로 마우스 오른쪽 **단추로 클릭 하** 고 메뉴에서 **스마트 계약에 대 한 마이크로 서비스 생성** 을 선택 합니다.

    ![스마트 계약에 대 한 마이크로 서비스 생성을 선택 하 Visual Studio Code 창](./media/ethereum-logic-app/generate-logic-app.png)

1. 명령 팔레트에서 **논리 앱**을 선택 합니다.
1. **계약 주소**를 입력 합니다. 자세한 내용은 [계약 주소 가져오기](#get-the-contract-address)를 참조 하세요.
1. 논리 앱에 대 한 Azure 구독 및 리소스 그룹을 선택 합니다.

    논리 앱 구성 및 코드 파일은 **generatedLogicApp** 디렉터리에 생성 됩니다.

1. **GeneratedLogicApp/Lockchain** 디렉터리를 봅니다. 각 스마트 계약 함수, 이벤트 및 속성에 대 한 논리 앱 JSON 파일이 있습니다.
1. **GeneratedLogicApp/서비스/속성을 엽니다. RequestMessage. logicapp** 파일을 열고 내용을 복사 합니다.

    ![복사할 코드가 포함 된 JSON 파일](./media/ethereum-logic-app/requestmessage.png)

1. 논리 앱에서 **논리 앱 코드 보기**를 선택 합니다. 기존 JSON을 생성 된 논리 앱 JSON으로 바꿉니다.

    ![새로운 대체 된 앱 코드로 논리 앱 코드 보기](./media/ethereum-logic-app/code-view.png)

1. 디자이너 뷰로 전환 하려면 **디자이너** 를 선택 합니다.
1. 논리 앱은 시나리오에 대 한 기본 단계를 포함 합니다. 그러나 Ethereum Blockchain 커넥터에 대 한 구성 세부 정보를 업데이트 해야 합니다.
1. **연결** 단계를 선택 하 고 Azure Blockchain 서비스에 대 한 [API 연결](#create-an-api-connection) 을 변경 하거나 만듭니다.

    ![연결 선택이 있는 디자이너 뷰](./media/ethereum-logic-app/microservice-logic-app.png)

1. 이제 논리 앱을 사용할 수 있습니다. REST 기반 마이크로 서비스을 테스트 하려면 논리 앱 요청 URL에 대 한 HTTP POST 요청을 실행 합니다. **Http 요청을 받을 때** 단계에서 **http POST URL** 콘텐츠를 복사 합니다.

    ![HTTP POST URL이 있는 Logic Apps 디자이너 창](./media/ethereum-logic-app/post-url.png)

1. 말아 넘기기를 사용 하 여 HTTP POST 요청을 만듭니다. *HTTP POST URL\>\<* 자리 표시자 텍스트를 이전 단계의 URL로 바꿉니다.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    말아 넘기기 명령은 논리 앱에서 응답을 반환 합니다. 이 경우 응답은 **RequestMessage** 스마트 계약 함수의 출력입니다.

    ![RequestMessage 스마트 계약 함수의 코드 출력](./media/ethereum-logic-app/curl.png)

개발 키트를 사용 하는 방법에 대 한 자세한 내용은 [Ethereum 용 Azure Blockchain 개발 키트 wiki 페이지](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)를 참조 하세요.

## <a name="create-an-api-connection"></a>API 연결 만들기

Ethereum Blockchain 커넥터에는 블록 체인에 대 한 API 연결이 필요 합니다. 여러 논리 앱에 API 커넥터를 사용할 수 있습니다. 일부 속성은 필수 이며 다른 속성은 시나리오에 따라 달라 집니다.

> [!IMPORTANT]
> 블록 체인에서 트랜잭션을 만들려면 개인 키 또는 계정 주소와 암호가 필요 합니다. 한 가지 형태의 인증만 필요 합니다. 개인 키와 계정 세부 정보를 모두 제공할 필요는 없습니다. 계약을 쿼리하면 트랜잭션이 필요 하지 않습니다. 계약 상태를 쿼리 하는 작업을 사용 하는 경우 개인 키 또는 계정 주소와 암호가 필요 하지 않습니다.

Azure Blockchain 서비스 멤버에 대 한 연결을 설정할 수 있도록 다음 목록에는 시나리오에 따라 필요한 속성이 있습니다.

| 속성 | 설명 |
|----------|-------------|
|**연결 이름** | API 연결의 이름입니다. 필수입니다. |
|**Ethereum RPC 끝점** | Azure Blockchain 서비스 트랜잭션 노드의 HTTP 주소입니다. 필수입니다. 자세한 내용은 [RPC 끝점 가져오기](#get-the-rpc-endpoint)를 참조 하세요. |
|**개인 키** | Ethereum 계정 개인 키입니다. 트랜잭션에는 개인 키 또는 계정 주소와 암호가 필요 합니다. 자세한 내용은 [개인 키 가져오기](#get-the-private-key)를 참조 하세요. |
|**계정 주소** | Azure Blockchain 서비스 구성원 계정 주소입니다. 트랜잭션에는 개인 키 또는 계정 주소와 암호가 필요 합니다. 자세한 내용은 [계정 주소 가져오기](#get-the-account-address)를 참조 하세요. |
|**계정 암호** | 계정 암호는 멤버를 만들 때 설정 됩니다. 암호 재설정에 대 한 자세한 내용은 [Ethereum 계정](consortium.md#ethereum-account)을 참조 하세요.|

## <a name="get-the-rpc-endpoint"></a>RPC 끝점 가져오기

Blockchain 네트워크에 연결 하려면 Azure Blockchain 서비스 RPC 끝점 주소가 필요 합니다. Ethereum 또는 Azure Portal 용 Azure Blockchain 개발 키트를 사용 하 여 끝점 주소를 가져올 수 있습니다.

**개발 키트를 사용 하려면 다음을 수행 합니다.**

1. Visual Studio Code의 **Azure Blockchain 서비스** 에서 컨소시엄을 마우스 오른쪽 단추로 클릭 합니다.
1. **RPC 끝점 주소 복사**를 선택 합니다.

    ![RPC 끝점 주소 복사를 선택 하 여 consortium을 보여 주는 Visual Studio Code 창](./media/ethereum-logic-app/devkit-rpc.png)

    RPC 끝점이 클립보드에 복사 됩니다.

**Azure Portal를 사용 하려면 다음을 수행 합니다.**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain 서비스 구성원으로 이동 합니다. **트랜잭션 노드**와 기본 트랜잭션 노드 링크를 선택합니다.

    ![(기본 노드)를 선택 하는 트랜잭션 노드 페이지](./media/ethereum-logic-app/transaction-nodes.png)

1. **액세스 키** > **연결 문자열** 을 선택 합니다.
1. **Https (액세스 키 1)** 또는 **https (액세스 키 2)** 에서 끝점 주소를 복사 합니다.

    ![연결 문자열 액세스 키를 사용 하 여 Azure Portal](./media/ethereum-logic-app/connection-string.png)

    RPC 끝점은 Azure Blockchain 서비스 구성원 트랜잭션 노드의 주소 및 액세스 키를 포함 하는 HTTPS URL입니다.

## <a name="get-the-private-key"></a>개인 키 가져오기

Ethereum 계정의 개인 키를 사용 하 여 블록 체인으로 트랜잭션을 전송할 때 인증할 수 있습니다. Ethereum 계정의 공개 키와 개인 키는 12 단어 니모닉에서 생성 됩니다. Ethereum 용 Azure Blockchain 개발 키트는 Azure Blockchain Service consortium 멤버에 연결 하는 경우 니모닉을 생성 합니다. 개발 키트 확장을 사용 하 여 끝점 주소를 가져올 수 있습니다.

1. Visual Studio Code에서 명령 팔레트 (F1)를 엽니다.
1. **Azure Blockchain: 개인 키 검색**을 선택 합니다.
1. 컨소시엄 멤버에 연결할 때 저장 한 니모닉을 선택 합니다.

    ![니모닉을 선택 하는 옵션이 있는 명령 팔레트](./media/ethereum-logic-app/private-key.png)

    개인 키가 클립보드에 복사 됩니다.

## <a name="get-the-account-address"></a>계정 주소 가져오기

블록 체인으로 트랜잭션을 보낼 때 구성원 계정 및 암호를 사용 하 여 인증할 수 있습니다. 이 암호는 멤버를 만들 때 설정 됩니다.

1. Azure Portal에서 Azure Blockchain 서비스 개요 페이지로 이동 합니다.
1. **구성원 계정** 주소를 복사 합니다.

    ![멤버 계정 주소를 사용 하는 개요 페이지](./media/ethereum-logic-app/member-account.png)

계정 주소 및 암호에 대 한 자세한 내용은 [Ethereum 계정](consortium.md#ethereum-account)을 참조 하세요.

## <a name="get-the-contract-abi"></a>계약 ABI 가져오기

계약 ABI는 스마트 계약 인터페이스를 정의 합니다. 스마트 계약과 상호 작용 하는 방법을 설명 합니다. Ethereum 용 Azure Blockchain 개발 키트를 사용 하 여 계약 ABI를 가져올 수 있습니다. 농담 컴파일러에서 만든 계약 메타 데이터 파일 에서도 가져올 수 있습니다.

**개발 키트를 사용 하려면 다음을 수행 합니다.**

개발 키트 또는 Truffle를 사용 하 여 스마트 계약을 작성 한 경우 확장을 사용 하 여 계약 ABI를 클립보드로 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 농담 프로젝트의 **빌드/계약** 폴더를 확장 합니다.
1. 계약 메타 데이터 JSON 파일을 마우스 오른쪽 단추로 클릭 합니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. **계약 ABI 복사**를 선택 합니다.

    ![복사 계약 ABI 선택이 포함 된 Visual Studio Code 창](./media/ethereum-logic-app/abi-devkit.png)

    계약 ABI는 클립보드로 복사 됩니다.

**계약 메타 데이터 파일을 사용 하려면 다음을 수행 합니다.**

1. 농담 프로젝트의 **빌드/계약** 폴더에 포함 된 계약 메타 데이터 파일을 엽니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. JSON 파일에서 **abi** 섹션을 찾습니다.
1. **Abi** JSON 배열을 복사 합니다.

    ![계약 메타 데이터 파일의 ABI 코드](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>계약 바이트 코드 가져오기

계약 바이트 코드는 Ethereum 가상 머신에서 실행 하는 컴파일된 스마트 계약입니다. Ethereum 용 Azure Blockchain 개발 키트를 사용 하 여 계약 바이트 코드를 가져올 수 있습니다. 또한 색도 컴파일러에서 가져올 수 있습니다.

**개발 키트를 사용 하려면 다음을 수행 합니다.**

개발 키트 또는 Truffle를 사용 하 여 스마트 계약을 작성 한 경우 확장을 사용 하 여 계약 바이트 코드를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 농담 프로젝트의 **빌드/계약** 폴더를 확장 합니다.
1. 계약 메타 데이터 JSON 파일을 마우스 오른쪽 단추로 클릭 합니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. **계약 바이트 코드 복사**를 선택 합니다.

    ![복사본 계약 바이트 코드를 선택 하는 Visual Studio Code 창](./media/ethereum-logic-app/bytecode-devkit.png)

    계약 바이트 코드는 클립보드에 복사 됩니다.

**계약 메타 데이터 파일을 사용 하려면 다음을 수행 합니다.**

1. 농담 프로젝트의 **빌드/계약** 폴더에 포함 된 계약 메타 데이터 파일을 엽니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. JSON 파일에서 **바이트 코드** 요소를 찾습니다.
1. **바이트 코드** 값을 복사 합니다.

    ![메타 데이터에 바이트 코드를 사용 하는 Visual Studio Code 창](./media/ethereum-logic-app/bytecode-metadata.png)

**농담 컴파일러를 사용 하려면 다음을 수행 합니다.**

명령 `solc --bin <smart contract>.sol`를 사용 하 여 계약 바이트 코드를 생성 합니다.

## <a name="get-the-contract-address"></a>계약 주소 가져오기

계약 주소는 Ethereum blockchain의 스마트 계약 대상 주소입니다. 이 주소를 사용 하 여 스마트 계약의 트랜잭션 또는 쿼리 상태를 전송 합니다. Truffle migration output 또는 계약 메타 데이터 파일에서 계약 주소를 가져올 수 있습니다.

**Truffle 마이그레이션 출력을 사용 하려면 다음을 수행 합니다.**

Truffle는 스마트 계약 배포 후 계약 주소를 표시 합니다. 출력에서 **계약 주소** 를 복사 합니다.

![Visual Studio Code의 계약 주소를 사용 하 여 Truffle 마이그레이션 출력](./media/ethereum-logic-app/contract-address-truffle.png)

**계약 메타 데이터 파일을 사용 하려면 다음을 수행 합니다.**

1. 농담 프로젝트의 **빌드/계약** 폴더에 포함 된 계약 메타 데이터 파일을 엽니다. 파일 이름은 스마트 계약 이름 뒤에 **.** i n i n 확장명을 입력 합니다.
1. JSON 파일에서 **networks** 섹션을 찾습니다.
1. 개인 네트워크는 정수 네트워크 ID로 식별 됩니다. 네트워크 섹션에서 주소 값을 찾습니다.
1. **주소** 값을 복사 합니다.

![Visual Studio Code 주소 값을 포함 하는 메타 데이터](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>다음 단계

[Logic Apps를 통해](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)비디오에서 일반적인 시나리오를 시청 하세요.
