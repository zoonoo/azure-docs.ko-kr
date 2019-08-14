---
title: Azure Storage Explorer 문제 해결 가이드 | Microsoft Docs
description: Azure Storage 탐색기에 대 한 디버깅 기술 개요
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 96a8eab57f1714eed4831bea01508e9140d1dfad
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934979"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage 탐색기 문제 해결 가이드

Microsoft Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 작업할 수 있게 하는 독립 실행형 앱입니다. 앱은 Azure, National Clouds 및 Azure Stack에서 호스트되는 스토리지 계정에 연결할 수 있습니다.

이 가이드에는 Storage Explorer에 나타나는 일반적인 문제에 대한 솔루션이 요약되어 있습니다.

## <a name="role-based-access-control-permission-issues"></a>역할 기반 Access Control 권한 문제

[RBAC (역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview) 는 권한 집합을 _역할로_결합 하 여 Azure 리소스에 대 한 세분화 된 액세스 관리를 제공 합니다. Storage 탐색기에서 RBAC를 작동 하기 위해 수행할 수 있는 몇 가지 제안 사항은 다음과 같습니다.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Storage 탐색기 내 리소스를 보려면 어떻게 해야 하나요?

RBAC를 사용 하 여 저장소 리소스에 액세스 하는 데 문제가 발생 하는 경우 적절 한 역할이 할당 되지 않았기 때문일 수 있습니다. 다음 섹션에서는 현재 저장소 리소스에 액세스 하는 데 필요한 Storage 탐색기 사용 권한을 설명 합니다.

적절 한 역할 또는 권한이 있는지 잘 모르겠으면 Azure 계정 관리자에 게 문의 하세요.

#### <a name="read-listget-storage-accounts"></a>읽기: 저장소 계정 나열/가져오기

저장소 계정을 나열할 수 있는 권한이 있어야 합니다. "읽기 권한자" 역할을 할당 하 여이 권한을 얻을 수 있습니다.

#### <a name="list-storage-account-keys"></a>저장소 계정 키 나열

또한 Storage 탐색기는 계정 키를 사용 하 여 요청을 인증할 수 있습니다. "참가자" 역할과 같은 보다 강력한 역할을 가진 키에 대 한 액세스 권한을 얻을 수 있습니다.

> [!NOTE]
> 액세스 키는이를 보유 한 모든 사용자에 게 무제한 권한을 부여 합니다. 따라서 일반적으로 계정 사용자에 게 전달 하지 않는 것이 좋습니다. 액세스 키를 해지 해야 하는 경우 [Azure Portal](https://portal.azure.com/)에서 다시 생성할 수 있습니다.

#### <a name="data-roles"></a>데이터 역할

리소스에서 읽기 데이터를 액세스할 수 있도록 허용 하는 하나 이상의 역할을 할당 해야 합니다. 예를 들어 blob을 나열 하거나 다운로드 해야 하는 경우 적어도 "저장소 Blob 데이터 판독기" 역할이 필요 합니다.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Storage 탐색기 내 리소스를 보려면 관리 계층 역할이 필요한 이유는 무엇 인가요?

Azure Storage에는 _관리_ 및 _데이터_의 두 가지 액세스 계층이 있습니다. 구독 및 저장소 계정은 관리 계층을 통해 액세스 됩니다. 컨테이너, blob 및 기타 데이터 리소스는 데이터 계층을 통해 액세스 됩니다. 예를 들어 Azure에서 저장소 계정 목록을 가져오려는 경우 관리 끝점에 요청을 보냅니다. 계정에 blob 컨테이너 목록을 원하는 경우 적절 한 서비스 끝점에 요청을 보냅니다.

RBAC 역할에는 관리 또는 데이터 계층 액세스 권한이 포함 될 수 있습니다. 예를 들어, "Reader" 역할은 읽기 전용 액세스 관리 계층 리소스를 부여 합니다.

"읽기 권한자" 역할은 데이터 계층 사용 권한을 제공 하지 않으며 데이터 계층에 액세스 하는 데 필요 하지 않습니다.

Storage 탐색기를 통해 Azure 리소스에 연결 하는 데 필요한 정보를 수집 하 여 리소스에 쉽게 액세스할 수 있습니다. 예를 들어 blob 컨테이너를 표시 하기 위해 Storage 탐색기 컨테이너 요청을 blob service 끝점으로 보냅니다. 이 끝점을 가져오기 위해 Storage 탐색기는 사용자가 액세스할 수 있는 구독 및 저장소 계정 목록을 검색 합니다. 그러나 구독 및 저장소 계정을 찾으려면 Storage 탐색기 관리 계층에 대 한 액세스 권한도 필요 합니다.

관리 계층 사용 권한을 부여 하는 역할이 없으면 데이터 계층에 연결 하는 데 필요한 정보를 가져올 수 Storage 탐색기.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>관리자에 게 필요한 관리 계층 권한을 얻을 수 없는 경우 어떻게 하나요?

지금은 RBAC 관련 솔루션이 아직 없습니다. 이 문제를 해결 하려면 [리소스에 연결할](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri)SAS URI를 요청할 수 있습니다.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>오류: 인증서 체인의 자체 서명된 인증서(및 유사 오류)

인증서 오류는 다음과 같은 두 상황 중 하나에서 발생합니다.

1. 앱이 "투명 프록시"를 통해 연결됩니다. 즉, 회사 서버와 같은 서버가 HTTPS 트래픽을 가로 채고 해독한 다음, 자체 서명된 인증서를 사용하여 암호화하는 것을 의미합니다.
2. 수신 하는 HTTPS 메시지에 자체 서명 된 SSL 인증서를 삽입 하는 응용 프로그램을 실행 하 고 있습니다. 인증서 삽입을 수행하는 애플리케이션에는 바이러스 백신, 네트워크 트래픽 검사 소프트웨어 등이 포함됩니다.

Storage Explorer가 자체 서명된 인증서나 신뢰할 수 없는 인증서를 발견하면, 수신된 HTTPS 메시지가 변경되었는지 여부를 더 이상 알 수 없습니다. 자체 서명된 인증서의 복사본이 있으면 다음 단계를 통해 Storage Explorer가 이를 신뢰하게 할 수 있습니다.

1. 인증서의 Base-64 인코딩 X.509(.cer) 사본 가져오기
2. **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음, 파일 선택기를 사용하여 해당 .cer 파일을 찾고 선택하고 엽니다.

이 문제는 여러 인증서(루트 및 중간)의 결과로 발생할 수도 있습니다. 오류를 해결하려면 인증서를 모두 추가해야 합니다.

인증서가 들어오는 위치를 모를 경우 다음 단계를 수행 하 여 찾을 수 있습니다.

1. OpenSSL을 설치합니다.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html)(라이트 버전이면 충분함)
    * Mac 및 Linux: 운영 체제에 포함되어야 합니다.
2. OpenSSL을 실행합니다.
    * Windows: 설치 디렉터리를 열고 **/bin/** 을 클릭한 다음 **openssl.exe**를 두 번 클릭합니다.
    * Mac 및 Linux: 터미널에서 **openssl**을 실행합니다.
3. `s_client -showcerts -connect microsoft.com:443` 실행
4. 자체 서명된 인증서를 찾습니다. 자체 서명 된 인증서를 잘 모르는 경우에는 주체 `("s:")` 와 발급자 `("i:")` 가 동일한 위치에서 찾을 수 있습니다.
5. 자체 서명된 인증서를 찾았으면 각 인증서에 대해 **----- BEGIN CERTIFICATE -----** 부터 **----- END CERTIFICATE -----** 까지 모든 줄을 복사하여 새 .cer 파일에 붙여넣습니다.
6. Storage Explorer를 열고 **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음, 파일 선택기를 사용하여 만든 .cer 파일을 찾고 선택하고 엽니다.

이전 단계를 사용 하 여 자체 서명 된 인증서를 찾을 수 없는 경우 자세한 도움말은 사용자 의견 도구를 통해 문의해 주세요. 명령줄에서 `--ignore-certificate-errors` 플래그를 사용 하 여 Storage 탐색기를 시작 하도록 선택할 수도 있습니다. 이 플래그로 시작하면 Storage Explorer가 인증서 오류를 무시하게 됩니다.

## <a name="sign-in-issues"></a>로그인 문제

### <a name="blank-sign-in-dialog"></a>빈 로그인 대화 상자

빈 로그인 대화 상자는 일반적으로 전자 메일에서 지원 하지 않는 리디렉션을 수행 하 Storage 탐색기를 요청 하는 ADFS에 의해 발생 합니다. 이 문제를 해결 하려면 로그인에 장치 코드 흐름을 사용 하려고 할 수 있습니다. 이렇게 하려면 다음 단계를 완료합니다.

1. 메뉴가 미리 보기-"장치 코드 로그인 사용"을 > 합니다.
2. 왼쪽 세로 막대의 플러그 아이콘 또는 계정 패널의 “계정 추가”를 통해 연결 대화 상자를 엽니다.
3. 로그인 할 환경을 선택 합니다.
4. "로그인" 단추를 클릭 합니다.
5. 다음 패널의 지침을 따릅니다.

기본 브라우저가 이미 다른 계정에 로그인 되어 있으므로 사용 하려는 계정에 로그인 하는 데 문제가 있는 경우 다음 중 하나를 수행할 수 있습니다.

1. 링크 및 코드를 브라우저의 프라이빗 세션에 수동으로 복사합니다.
2. 링크 및 코드를 다른 브라우저에 수동으로 복사합니다.

### <a name="reauthentication-loop-or-upn-change"></a>재인증 루프 또는 UPN 변경

재인증 루프에 있거나 계정 중 하나의 UPN이 변경 된 경우 다음 단계를 수행 합니다.

1. 모든 계정을 제거한 다음, Storage Explorer를 닫습니다
2. 컴퓨터에서 .IdentityService 폴더를 삭제합니다. Windows에서 폴더는 `C:\users\<username>\AppData\Local`에 있습니다. Mac 및 Linux의 경우 사용자 디렉토리의 루트에서 폴더를 찾을 수 있습니다.
3. Mac 또는 Linux를 사용할 경우에는 ' 키 저장소 ' OS에서 IdentityService 항목을 삭제 해야 합니다. Mac의 경우 키 저장소가 "Gnome 키 집합" 애플리케이션입니다. Linux의 경우 일반적으로 애플리케이션은 "키링"으로 불리는데 이름은 배포에 따라 다를 수 있습니다.

### <a name="conditional-access"></a>조건부 액세스

Storage 탐색기 Windows 10, Linux 또는 macOS에서 사용 되는 경우에는 조건부 액세스가 지원 되지 않습니다. Storage 탐색기에서 사용 하는 AAD 라이브러리의 제한 때문입니다.

## <a name="mac-keychain-errors"></a>Mac 키 집합 오류

macOS 키 집합은 Storage Explorer의 인증 라이브러리에서 문제를 유발하는 상태가 될 수 있습니다. 이 상태에서 키 집합을 가져오려면 다음 단계를 수행 합니다.

1. Storage Explorer를 닫습니다.
2. 키 집합을 엽니다(**cmd+space**, keychain 입력, enter 키 누름).
3. "로그인" 키 집합을 선택합니다.
4. 자물쇠 아이콘을 클릭하여 키 집합을 잠급니다(완료되면 자물쇠가 잠긴 모양으로 바뀌며 열려 있는 앱에 따라 몇 초 정도 걸릴 수 있음).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage Explorer를 시작합니다.
6. "서비스 허브가 키 집합에 액세스하려고 합니다" 같은 팝업 메시지가 나타납니다. Mac 관리자 계정 암호를 입력 하는 경우 Mac 관리자 계정 암호를 입력 하 고 **항상 허용** 을 클릭 합니다 (또는 **항상 허용** 을 사용할 수 없는 경우 **허용** ).
7. 로그인을 시도합니다.

### <a name="general-sign-in-troubleshooting-steps"></a>일반적인 로그인 문제 해결 단계

* MacOS를 사용할 때 로그인 창이 "인증을 기다리는 중 ..." 위에 나타나지 않는 경우 대화 상자를 연 후 다음 [단계](#mac-keychain-errors) 를 수행 합니다.
* Storage Explorer 다시 시작
* 인증 창이 비어 있는 경우 1분 이상 기다렸다가 인증 대화 상자를 닫습니다.
* 머신과 Storage Explorer에 대해 프록시와 인증서 설정이 올바르게 구성되었는지 확인합니다.
* Windows를 사용 하 고 동일한 컴퓨터에서 Visual Studio 2019에 액세스 하 여 로그인 한 경우 Visual Studio 2019에 로그인 해 보세요. Visual Studio 2019에 성공적으로 로그인 하면 Storage 탐색기 열고 계정 패널에서 계정을 볼 수 있습니다.

이러한 방법으로 문제를 해결하지 못한 경우 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다.

### <a name="missing-subscriptions-and-broken-tenants"></a>누락된 구독 및 손상된 테넌트

성공적으로 로그인 한 후 구독을 검색할 수 없는 경우 다음 문제 해결 방법을 시도해 보세요.

* 해당 계정이 원하는 구독에 액세스할 수 있는지 확인합니다. 사용 하려는 Azure 환경에 대 한 포털에 로그인 하 여 액세스를 확인할 수 있습니다.
* 올바른 Azure 환경 (Azure, Azure 중국 21Vianet, Azure 독일, Azure 미국 정부 또는 사용자 지정 환경)을 사용 하 여 로그인 했는지 확인 합니다.
* 프록시를 사용 하는 경우 Storage 탐색기 프록시를 제대로 구성 했는지 확인 합니다.
* 계정을 제거하고 다시 추가해 봅니다.
* "추가 정보" 링크가 있는 경우 실패 하는 테 넌 트에 대해 보고 되는 오류 메시지를 확인 하 고 확인 합니다. 표시 되는 오류 메시지에 대 한 작업을 수행 하는 것이 확실 하지 않은 경우 [GitHub에서 문제](https://github.com/Microsoft/AzureStorageExplorer/issues)를 자유롭게 열어 보세요.

## <a name="cant-remove-attached-account-or-storage-resource"></a>연결 된 계정 또는 저장소 리소스를 제거할 수 없습니다.

UI를 통해 연결 된 계정 또는 저장소 리소스를 제거할 수 없는 경우 다음 폴더를 삭제 하 여 연결 된 모든 리소스를 수동으로 삭제할 수 있습니다.

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> 위의 폴더를 삭제하기 전에 Storage Explorer를 닫습니다.

> [!NOTE]
> SSL 인증서를 가져온 적이 있는 경우 `certs` 디렉터리의 콘텐츠를 백업합니다. 다음에는 백업을 사용하여 SSL 인증서를 다시 가져올 수 있습니다.

## <a name="proxy-issues"></a>프록시 문제

먼저 입력한 다음 정보가 모두 올바른지 확인합니다.

* 프록시 URL 및 포트 번호
* 프록시가 요구하는 경우 사용자 이름과 암호

> [!NOTE]
> Storage 탐색기에서 프록시 설정을 구성 하는 데 프록시 자동 구성 파일을 지원 하지 않습니다.

### <a name="common-solutions"></a>일반적인 솔루션

여전히 문제가 발생 하는 경우 다음과 같은 문제 해결 방법을 시도해 보세요.

* 프록시를 사용하지 않고 인터넷에 연결할 수 있으면 프록시 설정을 사용하지 않고 Storage Explorer가 작동하는지 확인합니다. 이 경우 프록시 설정에 문제가 있을 수 있습니다. 프록시 관리자와 함께 문제를 식별합니다.
* 프록시 서버를 사용하는 다른 애플리케이션이 예상대로 작동하는지 확인합니다.
* 사용 하려는 Azure 환경의 포털에 연결할 수 있는지 확인 합니다.
* 서비스 엔드포인트에서 응답을 수신할 수 있는지 확인합니다. 엔드포인트 URL 중 하나를 브라우저에 입력합니다. 연결할 수 있으면 InvalidQueryParameterValue 또는 유사한 XML 응답을 수신해야 합니다.
* 다른 사용자가 Storage Explorer와 프록시 서버를 함께 사용하고 있는 경우 연결할 수 있는지 확인합니다. 연결할 수 있다면 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

### <a name="tools-for-diagnosing-issues"></a>문제를 진단하기 위한 도구

Windows 용 Fiddler와 같은 네트워킹 도구가 있는 경우 다음과 같이 문제를 진단할 수 있습니다.

* 프록시를 통해 작업해야 하는 경우 프록시를 통해 연결하도록 네트워킹 도구를 구성해야 할 수 있습니다.
* 네트워킹 도구에서 사용하는 포트 번호를 확인합니다.
* Storage Explorer에 로컬 호스트 URL과 네트워킹 도구의 포트 번호를 프록시 설정으로 입력합니다. 올바르게 수행되면 네트워킹 도구가 Storage Explorer에서 만든 네트워크 요청을 관리 및 서비스 엔드포인트에 기록하기 시작합니다. 예를 들어 브라우저 https://cawablobgrs.blob.core.windows.net/ 에서 blob 끝점에 대 한를 입력 하면 다음과 유사한 응답이 수신 됩니다 .이 경우에는 액세스할 수 없는 경우에도 리소스가 있음을 알 수 있습니다.

![코드 샘플](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>프록시 서버 관리자 문의

프록시 설정이 올바른 경우 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

* 프록시가 Azure 관리 또는 리소스 끝점에 대 한 트래픽을 차단 하지 않는지 확인 합니다.
* 프록시 서버에서 사용하는 인증 프로토콜을 확인합니다. Storage 탐색기는 현재 NTLM 프록시를 지원 하지 않습니다.

## <a name="unable-to-retrieve-children-error-message"></a>"하위 항목을 검색할 수 없음" 오류 메시지

프록시를 통해 Azure에 연결 하는 경우 프록시 설정이 올바른지 확인 합니다. 구독 또는 계정의 소유자에 게 서 리소스에 대 한 액세스 권한이 부여 된 경우 해당 리소스에 대 한 읽기 또는 목록 권한이 있는지 확인 합니다.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>연결 문자열에 전체 구성 설정이 없습니다.

이 오류 메시지가 표시 되 면 저장소 계정에 대 한 키를 가져오는 데 필요한 권한이 없을 수 있습니다. 이 경우인지 확인하려면 포털로 이동하고 Storage 계정을 찾습니다. 저장소 계정에 대 한 노드를 마우스 오른쪽 단추로 클릭 하 고 "포털에서 열기"를 클릭 하 여 신속 하 게이 작업을 수행할 수 있습니다. 이렇게 하면 "액세스 키" 블레이드로 이동합니다. 키를 볼 수 있는 권한이 없는 경우 "액세스 권한이 없습니다." 라는 메시지가 포함 된 페이지가 표시 됩니다. 이 문제를 해결 하려면 다른 사용자 로부터 계정 키를 얻거나 이름 및 키를 사용 하 여 연결할 수 있습니다. 또는 저장소 계정에 대 한 SAS를 요청 하 여 저장소 계정에 연결 하는 데 사용할 수 있습니다.

계정 키가 표시 되 면 문제를 해결 하는 데 도움이 될 수 있도록 GitHub에서 문제를 파일에 표시 합니다.

## <a name="issues-with-sas-url"></a>SAS URL 문제

SAS URL을 사용하여 서비스에 연결하고 이 오류가 발생하는 경우:

* URL이 리소스를 읽거나 나열하는 데 필요한 권한을 제공하는지 확인합니다.
* URL이 만료되지 않았는지 확인합니다.
* SAS URL이 액세스 정책을 기반으로 하는 경우 액세스 정책이 철회되지 않았는지 확인합니다.

실수로 잘못된 SAS URL을 사용하여 첨부하고 분리할 수 없는 경우 다음 단계를 따릅니다.

1. Storage Explorer를 실행하는 경우 F12 키를 눌러 개발자 도구 창을 엽니다.
2. [애플리케이션] 탭을 클릭한 다음, 왼쪽 트리에서 로컬 스토리지 &gt; file://을 클릭합니다.
3. 문제가 있는 SAS URI의 서비스 유형과 연결된 키를 찾습니다. 예를 들어 잘못된 SAS URI가 Blob 컨테이너에 대한 것이면 `StorageExplorer_AddStorageServiceSAS_v1_blob`으로 명명된 키를 찾아봅니다.
4. 키의 값은 JSON 배열이어야 합니다. 잘못된 URI와 연결된 개체를 찾아 제거합니다.
5. Storage Explorer를 다시 로드하려면 Ctrl+R 키를 누릅니다.

## <a name="linux-dependencies"></a>Linux 종속성

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> Release.tar.gz 다운로드에 제공 된 Storage 탐색기는 Ubuntu 배포에 대해서만 지원 됩니다. 다른 배포판은 확인 되지 않으며 대체 또는 추가 패키지가 필요할 수 있습니다.

이러한 패키지는 Linux에서 Storage 탐색기 하기 위한 가장 일반적인 요구 사항입니다.

* [.NET Core 2.0 런타임](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` 또는 `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage 탐색기 버전 1.7.0 및 이전 버전에서는 .NET Core 2.0이 필요 합니다. 최신 버전의 .NET Core가 설치 되어 있는 경우 [Storage 탐색기 패치](#patching-storage-explorer-for-newer-versions-of-net-core)를 수행 해야 합니다. Storage 탐색기 1.8.0 이상을 실행 하는 경우에는 .NET Core 2.2을 사용할 수 있어야 합니다. 2\.2 이외의 버전은 현재 작동 하도록 확인 되지 않았습니다.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Storage 탐색기를 다운로드 합니다.
2. [.Net Core 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)설치 합니다.
3. 다음 명령을 실행합니다.
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Storage 탐색기를 다운로드 합니다.
2. [.Net Core 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)설치 합니다.
3. 다음 명령을 실행합니다.
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. 다운로드 Storage 탐색기
2. [.Net Core 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)설치 합니다.
3. 다음 명령을 실행합니다.
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. 다운로드 Storage 탐색기
2. [.Net Core 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)설치 합니다.
3. 다음 명령을 실행합니다.
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>최신 버전의 .NET Core에 대 한 Storage 탐색기 패치

1\.7.0 또는 이전 버전의 Storage 탐색기 Storage 탐색기에서 사용 하는 .NET Core 버전을 패치 해야 할 수 있습니다.

1. [Nuget에서](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)StreamJsonRpc의 1.5.43 버전을 다운로드 합니다. 페이지의 오른쪽에 있는 "패키지 다운로드" 링크를 찾습니다.
2. 패키지를 다운로드 한 후 파일 확장명을에서 `.nupkg` 으로 `.zip`변경 합니다.
3. 패키지의 압축을 풉니다.
4. `streamjsonrpc.1.5.43/lib/netstandard1.1/` 폴더를 엽니다.
5. Storage 탐색기 `StreamJsonRpc.dll` 폴더 내의 다음 위치에 복사 합니다.
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>탐색기에서 열기 Azure Portal 작동 하지 않음

Azure Portal에서 "탐색기에서 열기" 단추가 작동 하지 않으면 호환 되는 브라우저를 사용 하 고 있는지 확인 합니다. 다음 브라우저는 호환성을 테스트 했습니다.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>다음 단계

어떤 솔루션으로도 문제를 해결하지 못할 경우 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다. 왼쪽 아래 모서리의 "GitHub에 문제 보고" 단추를 사용하면 신속하게 GitHub로 이동할 수 있습니다.

![사용자 의견](./media/storage-explorer-troubleshooting/feedback-button.PNG)
