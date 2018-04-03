---
title: Azure Storage 탐색기에서 Azure Cosmos DB 관리
description: Azure Storage 탐색기에서 Azure Cosmos DB를 관리하는 방법을 알아봅니다.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Storage 탐색기에서 Azure Cosmos DB 문제 해결 가이드 개요

[Azure Storage 탐색기에서 Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)는 Windows, macOS 또는 Linux에서 Sovereign 클라우드 및 Azure에 호스팅되는 Azure Cosmos DB 계정에 연결할 수 있게 해주는 독립 실행형 앱입니다. 사용자는 Azure Cosmos DB 엔터티를 관리하고, 데이터를 조작하고, Storage Blob 및 큐와 같은 다른 Azure 엔터티와 함께 저장된 프로시저 및 트리거를 업데이트할 수 있게 됩니다.

이 가이드에는 Storage 탐색기의 Azure Cosmos DB에 나타나는 일반적인 문제에 대한 솔루션이 요약되어 있습니다.

- [로그인 문제](#Sign-in-issues)
  - [인증서 체인의 자체 서명된 인증서](#Self-signed-certificate-in-certificate-chain)
  - [구독을 검색할 수 없음](#Unable-to-retrieve-subscriptions)
  - [인증 페이지를 볼 수 없음](#Unable-to-see-the-authentication-page)
  - [계정을 제거할 수 없음](#Cannot-remove-account)
- [Http/Https 프록시 문제](#Http/Https-proxy-issue)
- [“로컬 및 연결” 노드 중 “개발” 노드 문제](#Development-node-under-Local-and-Attached-node-issue)
- [“로컬 및 연결” 노드의 Azure Cosmos DB 계정 연결 오류](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Azure Cosmos DB 노드 확장 오류](#Expand-Azure-Cosmos-DB-node-error)
- [다음 단계](#Next-steps)

<h2 id="Sign-in-issues">로그인 문제</h2>

계속하기 전에 응용 프로그램을 다시 시작하고 문제가 해결될 수 있는지 확인합니다.

<h2 id="Self-signed-certificate-in-certificate-chain">인증서 체인의 자체 서명된 인증서</h2>

이 오류가 표시되는 원인에는 몇 가지가 있는데, 가장 일반적인 두 가지는 다음과 같습니다.

1. “투명 프록시” 뒤에 있습니다. 즉, IT 부서와 같은 누군가가 HTTPS 트래픽을 가로 채고 해독한 다음, 자체 서명된 인증서를 사용하여 암호화하는 것을 의미합니다.

2. 수신한 HTTPS 메시지에 자체 서명된 SSL 인증서를 삽입하는 바이러스 백신 소프트웨어와 같은 소프트웨어를 실행하고 있습니다.

Storage 탐색기가 이러한 “자체 서명된 인증서” 중 하나를 발견하면 수신하는 HTTPS 메시지가 변조되었는지 여부를 더 이상 알지 못합니다. 자체 서명된 인증서의 복사본을 갖고 있는 경우 Storage 탐색기에 해당 인증서를 신뢰하도록 지시할 수 있습니다. 인증서를 주입하는 주체가 누구인지 모른다면 다음 단계를 수행하여 알아낼 수 있습니다.

1. OpenSSL을 설치합니다.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)(라이트 버전도 사용 가능)
     - Mac 및 Linux: 운영 체제에 포함되어야 합니다.
2. OpenSSL을 실행합니다.
    - Windows: 설치 디렉터리, **/bin/**으로 이동한 다음, **openssl.exe**를 두 번 클릭합니다.
    - Mac 및 Linux: 터미널에서 **openssl**을 실행합니다.
3. `s_client -showcerts -connect microsoft.com:443` 실행
4. 자체 서명된 인증서를 찾습니다. 어떤 인증서가 자체 서명된 것인지 확실하지 않은 경우 제목(“s:”)과 발급자(“i:”)가 같은 위치를 찾습니다.
5.  자체 서명된 인증서를 찾았으면 각 인증서에 대해 **-----BEGIN CERTIFICATE-----**부터 **-----END CERTIFICATE-----**까지 모든 줄을 복사하여 새 .cer 파일에 붙여넣습니다.
6.  Storage 탐색기를 연 다음, **편집** > **SSL 인증서** > **인증서 가져오기**로 이동합니다. 파일 선택기를 사용하여, 만든 .cer 파일을 찾아서 선택하고 엽니다.

위의 단계를 사용하여 자체 서명된 인증서를 찾을 수 없는 경우 사용자 의견을 보내 도움을 받을 수 있습니다.

<h2 id="Unable-to-retrieve-subscriptions">구독을 검색할 수 없음</h2>

성공적으로 로그인한 후 구독을 검색할 수 없습니다.

- [Azure Portal](http://portal.azure.com/)에 로그인하여 계정이 구독에 액세스할 수 있는지 확인합니다.
- 올바른 환경([Azure](http://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Germany](https://portal.microsoftazure.de/), [Azure US Government](http://portal.azure.us/) 또는 사용자 지정 환경/Azure Stack)을 사용하여 로그인했는지 확인합니다.
- 프록시 뒤에 있는 경우 Storage 탐색기 프록시를 제대로 구성했는지 확인합니다.
- 계정을 제거하고 다시 추가해 봅니다.
- 홈 디렉터리(즉, C:\Users\ContosoUser)에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다.
  - .adalcache
  - .devaccounts
  - .extaccounts
- 로그인할 때 개발자 도구 콘솔(F12 키)에서 오류 메시지를 확인합니다.

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">인증 페이지를 볼 수 없음</h2>  

인증 페이지를 볼 수 없습니다.

- 연결 속도에 따라 로그인 페이지가 로드되는 데 시간이 걸릴 수 있으므로 인증 대화 상자를 닫기 전에 1분 이상 기다립니다.
- 프록시 뒤에 있는 경우 Storage 탐색기 프록시를 제대로 구성했는지 확인합니다.
- F12 키를 눌러 개발자 콘솔을 가져옵니다. 개발자 콘솔의 응답을 보고 인증이 작동하지 않는 이유에 대한 단서를 찾을 수 있는지 확인합니다.

<h2 id="Cannot-remove-account">계정을 제거할 수 없음</h2>

계정을 제거할 수 없거나 재인증 링크가 작동하지 않습니다.

- 홈 디렉터리에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다.
  - .adalcache
  - .devaccounts
  - .extaccounts
- SAS 연결 Storage 리소스를 제거하려면 다음을 삭제합니다.
  - Windows - %AppData%/StorageExplorer 폴더
  - Mac - Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer
  - Linux - ~/.config/StorageExplorer
  - 이러한 파일을 삭제하면 **모든 자격 증명을 다시 입력해야 합니다**.


<h2 id="Http/Https-proxy-issue">Http/Https 프록시 문제</h2>

ASE에서 http/https 프록시를 구성할 때 왼쪽 트리에서 Azure Cosmos DB 노드를 나열할 수 없습니다. 알려진 문제이며 다음 릴리스에서 수정될 예정입니다. 이 시점에서 Azure Portal에서 Azure Cosmos DB 데이터 탐색기를 해결 방법으로 사용할 수 있습니다. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">“로컬 및 연결” 노드 중 “개발” 노드 문제</h2>

왼쪽 트리의 “로컬 및 연결” 노드에서 “개발” 노드를 클릭한 후 응답이 없습니다.  이는 정상적인 동작입니다. Azure Cosmos DB 로컬 에뮬레이터는 다음 릴리스에서 지원됩니다.

![개발 노드](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">“로컬 및 연결” 노드의 Azure Cosmos DB 계정 연결 오류</h2>

“로컬 및 연결” 노드에서 Azure Cosmos DB 계정을 연결한 후 아래 오류가 표시되는 경우, 올바른 연결 문자열을 사용하고 있는지 확인합니다.

![로컬 및 연결 노드에서 Azure Cosmos DB 연결 오류](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Azure Cosmos DB 노드 확장 오류</h2>

왼쪽의 트리 노드를 확장하는 동안 다음 오류가 나타날 수 있습니다. 

![확장 오류](./media/troubleshoot-cosmosdb/expand-error.png)

다음과 같은 제안 사항을 시도해 봅니다.

- Azure Cosmos DB 계정이 프로비전 진행 중인지 확인하고 계정을 성공적으로 생성될 때 다시 시도합니다.
- 계정이 노드 “빠른 액세스” 노드 또는 “로컬 및 연결” 노드에 있는 경우 해당 계정이 삭제되었는지 확인합니다. 이 경우 노드를 수동으로 제거해야 합니다.

<h2 id="Next-steps">다음 단계</h2>

솔루션이 작동하지 않는 경우 문제 해결을 위해 해당 문제에 대한 세부 정보가 포함된 이메일을 Azure Cosmos DB Dev Tooling 팀([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com))으로 보냅니다.





