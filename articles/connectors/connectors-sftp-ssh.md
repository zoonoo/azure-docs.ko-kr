---
title: Azure Logic Apps에서 SFTP에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 SFTP 서버에 대한 파일을 모니터링, 만들기, 관리, 전송 및 수신하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: cfee4f06479d2504b88f4a5d5a0a2417154e3b03
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064502"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Azure Logic Apps 및 SFTP-SSH를 사용하여 SFTP 파일 모니터링, 만들기 및 관리

Azure Logic Apps 및 SFTP-SSH 커넥터를 사용하면 다음과 같이 다른 작업과 함께 [SFTP](https://www.ssh.com/ssh/sftp/)에 있는 계정을 통해 파일을 모니터링, 만들기, 전송 및 수신하는 자동화된 작업 및 워크플로를 만들 수 있습니다.

* 파일이 추가되거나 변경될 때 모니터링합니다.
* 파일 가져오기/만들기/복사/이름 바꾸기/업데이트/나열/삭제를 수행합니다.
* 폴더를 만듭니다.
* 파일 콘텐츠 및 메타데이터를 가져옵니다.
* 보관을 폴더로 추출합니다.

트리거를 사용하여 SFTP 서버에서 응답을 가져오고 다른 작업에서 출력을 사용하도록 할 수 있습니다. 논리 앱의 작업을 사용하여 SFTP 서버에 있는 파일을 통해 작업을 수행할 수 있습니다. 또한 다른 작업에서 SFTP 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 정기적으로 SFTP 서버에서 파일을 검색하는 경우 Office 365 Outlook 커넥터 또는 Outlook.com 커넥터를 사용하여 해당 파일 및 해당 콘텐츠에 대한 이메일을 보낼 수 있습니다.
논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH와 SFTP 비교

아래에는 SFTP-SSH 커넥터와 [SFTP](../connectors/connectors-create-api-sftp.md) 커넥터 간의 몇 가지 주요 차이점이 나와 있습니다. SFTP-SSH 커넥터는 다음과 같은 기능을 제공합니다.

* .NET용 오픈 소스 SSH(Secure Shell) 라이브러리인 <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET**</a> 라이브러리를 사용합니다.

* 큰 파일(최대 **1GB**)을 지원합니다. 즉, 커넥터가 최대 1GB 크기의 파일을 읽거나 쓸 수 있습니다.

* SFTP 서버의 지정된 경로에 폴더를 만드는 **폴더 만들기** 작업을 제공합니다.

* SFTP 서버의 파일 이름을 바꾸는 **파일 이름 바꾸기** 작업을 제공합니다.

* SFTP 서버에 대한 연결을 캐시합니다. 그러면 서버에 대한 연결 시도 수가 감소하며 성능이 개선됩니다. 

  SFTP 서버에서 <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> 속성을 설정하여 연결을 캐시하는 데 사용되는 기간을 제어할 수 있습니다. 

## <a name="how-trigger-polling-works"></a>트리거 폴링의 작동 방식

SFTP-SSH 트리거는 SFTP 파일 시스템을 폴링하여 마지막 폴링 이후 변경된 파일을 찾는 방식으로 작동합니다. 파일 변경 시 타임스탬프를 유지할 수 있는 도구도 있으므로, 해당 도구를 사용할 때는 이 기능을 사용하지 않도록 설정해야 트리거가 작동합니다. 아래에는 몇 가지 일반적인 설정이 나와 있습니다.

| SFTP 클라이언트 | 조치 | 
|-------------|--------| 
| Winscp | 옵션 → 기본 설정... → 전송 → 편집... → 타임스탬프 유지 → 사용 안 함 |
| FileZilla | 전송 → 전송된 파일의 파일 시간 보존 → 사용 안 함 | 
||| 

트리거는 새 파일을 찾으면 해당 파일이 완전한 상태이며 부분적으로 작성된 것이 아닌지 확인합니다. 예를 들어 트리거가 파일 서버를 확인할 때 파일을 변경하는 중일 수 있습니다. 부분적으로 작성된 파일이 반환되지 않도록 하기 위해 트리거는 최근 변경된 내용이 있는 파일의 타임스탬프를 기록하되 해당 파일을 즉시 반환하지는 않으며, 서버를 다시 폴링할 때만 해당 파일을 반환합니다. 이 동작으로 인해 트리거 폴링 간격의 최대 2배까지 지연이 발생하는 경우도 있습니다. 

파일 콘텐츠를 요청할 때 트리거는 50MB보다 큰 파일을 검색하지 않습니다. 50MB보다 큰 파일을 가져오려면 다음 패턴을 따르세요.

* **파일이 추가되거나 수정된 경우(메타데이터만)** 등의 파일 속성을 반환하는 트리거를 사용합니다. 
* **경로를 사용하여 파일 콘텐츠 가져오기**와 같이 전체 파일을 읽는 작업이 포함된 트리거를 따릅니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* SFTP 계정에 대한 연결을 만들고 해당 계정에 액세스하는 권한을 논리 앱에 부여하는 SFTP 호스트 서버 주소 및 계정 자격 증명

  SSH 개인 키의 전체 콘텐츠를 복사한 후 여러 줄 형식에 따라 **SSH 개인 키** 속성에 붙여넣습니다. 
  Notepad.exe를 사용하여 SSH 개인 키를 제공하는 방법을 보여 주는 샘플 단계는 다음과 같습니다.
    
  1. Notepad.exe에서 SSH 개인 키 파일을 엽니다.
  2. **편집** 메뉴에서 **모두 선택**을 선택합니다.
  3. **편집** > **복사**를 선택합니다.
  4. 연결을 만들 때 **SSH 개인 키** 속성에 키를 붙여넣습니다. **SSH 개인 키** 속성을 수동으로 편집하지는 마세요.

     커넥터는 이러한 SSH 개인 키 형식 및 MD5 지문만 지원하는 SSH.NET 라이브러리를 사용합니다.

     * RSA 
     * DSA

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SFTP 계정에 액세스하려는 논리 앱입니다. SFTP 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). SFTP 동작을 사용하려면 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-sftp"></a>SFTP에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 빈 논리 앱의 경우 검색 상자에서 필터로 “sftp”를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다. 

   또는

   기존 논리 앱의 경우 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 
   검색 상자에서 필터로 “sftp”를 입력합니다. 
   작업 목록에서 원하는 작업을 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

1. 연결에 필요한 세부 정보를 입력한 다음, **만들기**를 선택합니다.

1. 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="examples"></a>예

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP 트리거: 파일이 추가되거나 수정되는 경우

이 트리거는 SFTP 서버에서 파일이 추가되거나 변경되는 경우를 트리거에서 감지하면 논리 앱 워크플로를 시작합니다. 따라서 예를 들어 해당 콘텐츠가 지정된 조건을 충족하는지 여부에 따라 파일의 콘텐츠를 확인하고 해당 콘텐츠를 가져올 것인지를 결정하는 조건을 추가할 수 있습니다. 마지막으로 파일의 콘텐츠를 가져오는 작업을 추가하고 해당 콘텐츠를 SFTP 서버의 폴더에 넣을 수 있습니다. 

**엔터프라이즈 예제**: 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 SFTP 폴더를 모니터링할 수 있습니다. 그런 다음, **파일 콘텐츠 가져오기**와 같은 SFTP 작업을 사용할 수 있으므로 추가로 처리할 주문의 콘텐츠를 가져오고 주문 데이터베이스에 해당 주문을 저장할 수 있습니다.

### <a name="sftp-action-get-content"></a>SFTP 작업: 콘텐츠 가져오기

이 작업은 SFTP 서버의 파일에서 콘텐츠를 가져옵니다. 따라서 예를 들어 이전 예제의 트리거와 파일의 콘텐츠가 충족해야 하는 조건을 추가할 수 있습니다. 조건이 true인 경우 콘텐츠를 가져오는 작업을 실행할 수 있습니다. 

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/sftpconnector/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.