---
title: "Azure Storage 탐색기 문제 해결 가이드 | Microsoft Docs"
description: "Azure의 두 가지 디버깅 기능 개요"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 2f62de428d1915b1e070350a2837f24c3486f8c7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage 탐색기 문제 해결 가이드

Microsoft Azure Storage 탐색기(미리 보기)는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 사용할 수 있는 독립 실행형 앱입니다. 앱은 Azure, National Clouds 및 Azure Stack에서 호스트되는 저장소 계정에 연결할 수 있습니다.

이 가이드에는 Storage 탐색기에 나타나는 일반적인 문제에 대한 솔루션이 요약되어 있습니다.

## <a name="sign-in-issues"></a>로그인 문제

AAD(Azure Active Directory) 계정만 지원됩니다. ADFS 계정을 사용하는 경우 Storage 탐색기에 대한 로그인은 작동하지 않습니다. 계속하기 전에 응용 프로그램을 다시 시작하고 문제가 해결될 수 있는지 확인합니다.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>오류: 인증서 체인의 자체 서명된 인증서

이 오류가 발생할 수 있는 몇 가지 이유가 있으며 가장 일반적인 두 가지 이유는 다음과 같습니다.

1. 앱이 "투명 프록시"를 통해 연결됩니다. 즉, 회사 서버와 같은 서버가 HTTPS 트래픽을 가로 채고 해독한 다음 자체 서명된 인증서를 사용하여 암호화하는 것을 의미합니다.

2. 수신한 HTTPS 메시지에 자체 서명된 SSL 인증서를 삽입하는 바이러스 백신 소프트웨어와 같은 응용 프로그램을 실행하고 있습니다.

Storage 탐색기에서 문제 중 하나를 발견하면 수신된 HTTPS 메시지가 변조되었는지 여부를 더 이상 알 수 없습니다. 자체 서명된 인증서의 복사본이 있으면 Storage 탐색기에서 이를 신뢰하게 할 수 있습니다. 누가 인증서를 삽입하고 있는지 확실하지 않은 경우 다음 단계를 수행하여 찾습니다.

1. OpenSSL을 설치합니다.

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html)(라이트 버전이면 충분함)

    - Mac 및 Linux: 운영 체제에 포함되어야 합니다.

2. OpenSSL을 실행합니다.

    - Windows: 설치 디렉터리를 열고 **/bin/**을 클릭한 다음 **openssl.exe**를 두 번 클릭합니다.
    - Mac 및 Linux: 터미널에서 **openssl**을 실행합니다.

3. s_client -showcerts -connect microsoft.com :443을 실행합니다.

4. 자체 서명된 인증서를 찾습니다. 어떤 인증서가 자체 서명된 것인지 확실하지 않은 경우 제목("s:")과 발급자("i:")가 같은 위치를 찾습니다.

5. 자체 서명된 인증서를 찾았으면 각 인증서에 대해 **----- BEGIN CERTIFICATE -----**부터 **----- END CERTIFICATE -----**까지 모든 줄을 복사하여 새 .cer 파일에 붙여넣습니다.

6. Storage 탐색기를 열고 **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음 파일 선택기를 사용하여 만든 .cer 파일을 찾고 선택하고 엽니다.

위의 단계를 사용하여 자체 서명된 인증서를 찾을 수 없는 경우 사용자 의견 도구를 통해 당사에 문의하여 도움을 받습니다.

### <a name="unable-to-retrieve-subscriptions"></a>구독을 검색할 수 없음

성공적으로 로그인한 후 구독을 검색할 수 없는 경우 다음 단계에 따라 이 문제를 해결합니다.

- Azure Portal에 로그인하여 계정이 구독에 액세스할 수 있는지 확인합니다.

- 올바른 환경(Azure, Azure China, Azure Germany, Azure US Government 또는 사용자 지정 환경/Azure Stack)을 사용하여 로그인했는지 확인합니다.

- 프록시 뒤에 있는 경우 Storage 탐색기 프록시를 제대로 구성했는지 확인합니다.

- 계정을 제거하고 다시 추가해 봅니다.

- 루트 디렉터리(즉, C:\Users\ContosoUser)에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다.

    - .adalcache

    - .devaccounts

    - .extaccounts

- 로그인할 때 F12 키를 눌러 개발자 도구 콘솔에서 오류 메시지를 확인합니다.

![개발자 도구](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>인증 페이지를 볼 수 없음

인증 페이지를 볼 수 없는 경우 다음 단계에 따라 이 문제를 해결합니다.

- 연결 속도에 따라 로그인 페이지가 로드되는 데 시간이 걸릴 수 있으므로 인증 대화 상자를 닫기 전에 1분 이상 기다립니다.

- 프록시 뒤에 있는 경우 Storage 탐색기 프록시를 제대로 구성했는지 확인합니다.

- F12 키를 눌러 개발자 콘솔을 확인합니다. 개발자 콘솔의 응답을 보고 인증이 작동하지 않는 이유에 대한 단서를 찾을 수 있는지 확인합니다.

### <a name="cannot-remove-account"></a>계정을 제거할 수 없음

계정을 제거할 수 없거나 재인증 링크가 작동하지 않는 경우 다음 단계에 따라 이 문제를 해결합니다.

- 루트 디렉터리에서 다음 파일을 삭제한 다음 계정을 다시 추가합니다.

    - .adalcache

    - .devaccounts

    - .extaccounts

- SAS 연결 Storage 리소스를 제거하려면 다음 파일을 삭제합니다.

    - Windows - %AppData%/StorageExplorer 폴더

    - Mac - Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer

    - Linux - ~/.config/StorageExplorer

> [!NOTE]
>  앞의 파일을 삭제한 후 계정에 다시 로그인해야 합니다.

## <a name="proxy-issues"></a>프록시 문제

먼저 입력한 다음 정보가 모두 올바른지 확인합니다.

- 프록시 URL 및 포트 번호

- 프록시가 요구하는 경우 사용자 이름과 암호

### <a name="common-solutions"></a>일반적인 솔루션

여전히 문제가 발생하는 경우 다음 단계에 따라 문제를 해결합니다.

- 프록시를 사용하지 않고 인터넷에 연결할 수 있으면 프록시 설정을 사용하지 않고 Storage 탐색기가 작동하는지 확인합니다. 이 경우 프록시 설정에 문제가 있을 수 있습니다. 프록시 관리자와 함께 문제를 식별합니다.

- 프록시 서버를 사용하는 다른 응용 프로그램이 예상대로 작동하는지 확인합니다.

- 웹 브라우저를 사용하여 Microsoft Azure Portal에 연결할 수 있는지 확인합니다.

- 서비스 끝점에서 응답을 수신할 수 있는지 확인합니다. 끝점 URL 중 하나를 브라우저에 입력합니다. 연결할 수 있으면 InvalidQueryParameterValue 또는 유사한 XML 응답을 수신해야 합니다.

- 다른 사용자가 Storage 탐색기와 프록시 서버를 함께 사용하고 있는 경우 연결할 수 있는지 확인합니다. 연결할 수 있다면 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

### <a name="tools-for-diagnosing-issues"></a>문제를 진단하기 위한 도구

Windows용 Fiddler와 같은 네트워킹 도구가 있으면 다음과 같은 문제를 진단할 수 있습니다.

- 프록시를 통해 작업해야 하는 경우 프록시를 통해 연결하도록 네트워킹 도구를 구성해야 할 수 있습니다.

- 네트워킹 도구에서 사용하는 포트 번호를 확인합니다.

- Storage 탐색기에 로컬 호스트 URL과 네트워킹 도구의 포트 번호를 프록시 설정으로 입력합니다. 올바르게 수행되면 네트워킹 도구가 Storage 탐색기에서 만든 네트워크 요청을 관리 및 서비스 끝점에 기록하기 시작합니다. 예를 들어 브라우저에서 Blob 끝점에 대한 https://cawablobgrs.blob.core.windows.net/을 입력하면 다음과 유사한 응답이 수신됩니다. 이 응답을 통해 리소스에 액세스할 수는 없지만 리소스가 있음을 알 수 있습니다.

![코드 샘플](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>프록시 서버 관리자 문의

프록시 설정이 올바른 경우 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

- 프록시가 Azure 관리 또는 리소스 끝점에 대한 트래픽을 차단하지 않는지 확인합니다.

- 프록시 서버에서 사용하는 인증 프로토콜을 확인합니다. Storage 탐색기는 현재 NTLM 프록시를 지원하지 않습니다.

## <a name="unable-to-retrieve-children-error-message"></a>"하위 항목을 검색할 수 없음" 오류 메시지

프록시를 통해 Azure에 연결된 경우 프록시 설정이 올바른지 확인합니다. 구독 또는 계정 소유자로부터 리소스에 대한 액세스가 부여된 경우 해당 리소스에 대한 읽기 또는 목록 권한이 있는지 확인합니다.

### <a name="issues-with-sas-url"></a>SAS URL 문제
SAS URL을 사용하여 서비스에 연결하고 이 오류가 발생하는 경우:

- URL이 리소스를 읽거나 나열하는 데 필요한 권한을 제공하는지 확인합니다.

- URL이 만료되지 않았는지 확인합니다.

- SAS URL이 액세스 정책을 기반으로 하는 경우 액세스 정책이 철회되지 않았는지 확인합니다.

실수로 잘못된 SAS URL을 사용하여 첨부하고 분리할 수 없는 경우 다음 단계를 따릅니다.
1.  Storage 탐색기를 실행 하는 경우 F12 키를 눌러 개발자 도구 창을 엽니다.
2.  [응용 프로그램] 탭을 클릭한 다음 왼쪽 트리에서 로컬 저장소 > file://을 클릭합니다.
3.  문제가 있는 SAS URI의 서비스 유형과 연결된 키를 찾습니다. 예를 들어 잘못된 SAS URI가 Blob 컨테이너에 대한 것이면 `StorageExplorer_AddStorageServiceSAS_v1_blob`으로 명명된 키를 찾아봅니다.
4.  키의 값은 JSON 배열이어야 합니다. 잘못된 URI와 연결된 개체를 찾아 제거합니다.
5.  Storage 탐색기를 다시 로드하려면 Ctrl+R 키를 누릅니다.

## <a name="linux-dependencies"></a>Linux 종속성

Ubuntu 16.04 이외의 다른 Linux 배포판의 경우 몇 가지 종속성을 수동으로 설치해야 합니다. 일반적으로 다음과 같은 패키지가 필요합니다.
* libgconf-2-4
* libsecret
* 최신 GCC

배포판에 따라 다른 패키지를 설치해야 할 수 있습니다. Storage 탐색기 [릴리스 정보](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)는 일부 배포판에 대한 특정 단계를 포함합니다.

## <a name="next-steps"></a>다음 단계

해결할 수 있는 솔루션이 없으면 당사에서 문제 해결을 위해 연락할 수 있도록 사용자 의견 도구를 통해 메일로 문제에 대한 자세한 내용을 최대한 포함하여 문제를 제출합니다.

이렇게 하려면 **도움말** 메뉴를 클릭한 다음 **피드백 보내기**를 클릭합니다.

![사용자 의견](./media/storage-explorer-troubleshooting/4022503_en_1.png)
