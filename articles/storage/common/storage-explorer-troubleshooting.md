---
title: Azure Storage Explorer 문제 해결 가이드 | Microsoft Docs
description: Azure 저장소 탐색기의 디버깅 기술 개요
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: aec8048c7ef2eb0d944cdd2a863e23578f4f87e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561683"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer 문제 해결 가이드

Microsoft Azure 저장소 탐색기는 Windows, macOS 및 Linux에서 Azure 저장소 데이터로 쉽게 작업할 수 있는 독립 실행형 앱입니다. 앱은 Azure, 국가 클라우드 및 Azure 스택에서 호스팅되는 저장소 계정에 연결할 수 있습니다.

이 가이드에서는 저장소 탐색기에서 일반적으로 볼 수 있는 문제에 대한 해결 방안을 요약합니다.

## <a name="rbac-permissions-issues"></a>RBAC 권한 문제

역할 기반 액세스 제어 [RBAC를](https://docs.microsoft.com/azure/role-based-access-control/overview) 사용하면 사용 권한 집합을 _역할에_결합하여 Azure 리소스에 대한 매우 세분화된 액세스 관리를 수행할 수 있습니다. 다음은 저장소 탐색기에서 RBAC가 최적으로 작동하도록 하는 몇 가지 전략입니다.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>저장소 탐색기에서 내 리소스에 액세스하려면 어떻게 해야 합니까?

RBAC를 통해 저장소 리소스에 액세스하는 데 문제가 있는 경우 적절한 역할이 할당되지 않았을 수 있습니다. 다음 섹션에서는 저장소 탐색기현재 저장소 리소스에 액세스하는 데 필요한 사용 권한에 대해 설명합니다. 적절한 역할이나 권한이 있는지 확실하지 않은 경우 Azure 계정 관리자에게 문의하십시오.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"읽기: 목록/저장소 계정 받기" 권한 문제

저장소 계정을 나열할 수 있는 권한이 있어야 합니다. 이 권한을 얻으려면 _Reader_ 역할이 할당되어야 합니다.

#### <a name="list-storage-account-keys"></a>스토리지 계정 키 나열

저장소 탐색기는 계정 키를 사용하여 요청을 인증할 수도 있습니다. _기여자_ 역할과 같은 더 강력한 역할을 통해 계정 키에 액세스할 수 있습니다.

> [!NOTE]
> 액세스 키는 액세스 키를 보유한 모든 사람에게 무제한 권한을 부여합니다. 따라서 계정 사용자에게 이러한 키를 나눠주는 것은 권장되지 않습니다. 액세스 키를 취소해야 하는 경우 [Azure 포털](https://portal.azure.com/)에서 다시 생성할 수 있습니다.

#### <a name="data-roles"></a>데이터 역할

리소스에서 읽기 데이터에 대한 액세스 권한을 부여하는 역할중 하나 이상 할당되어야 합니다. 예를 들어 Blob을 나열하거나 다운로드하려면 저장소 _Blob 데이터 리더_ 역할이 필요합니다.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>저장소 탐색기에서 내 리소스를 보려면 관리 계층 역할이 필요한 이유는 무엇입니까?

Azure Storage에는 _관리_ 및 _데이터라는_두 가지 액세스 계층이 있습니다. 구독 및 저장소 계정은 관리 계층을 통해 액세스됩니다. 컨테이너, Blob 및 기타 데이터 리소스는 데이터 계층을 통해 액세스됩니다. 예를 들어 Azure에서 저장소 계정 목록을 얻으려면 관리 끝점으로 요청을 보냅니다. 계정의 Blob 컨테이너 목록을 원하는 경우 해당 서비스 끝점으로 요청을 보냅니다.

RBAC 역할에는 관리 또는 데이터 계층 액세스에 대한 사용 권한이 포함될 수 있습니다. 예를 들어 Reader 역할은 관리 계층 리소스에 대한 읽기 전용 액세스 권한을 부여합니다.

엄밀히 말하면 Reader 역할은 데이터 계층 권한을 제공하지 않으며 데이터 계층에 액세스할 필요가 없습니다.

저장소 탐색기를 사용하면 Azure 리소스에 연결하는 데 필요한 정보를 수집하여 리소스에 쉽게 액세스할 수 있습니다. 예를 들어 Blob 컨테이너를 표시하기 위해 저장소 탐색기는 Blob 서비스 끝점에 "목록 컨테이너" 요청을 보냅니다. 이러한 끝점을 얻으려면 저장소 탐색기는 액세스 권한이 있는 구독 및 저장소 계정 목록을 검색합니다. 구독 및 저장소 계정을 찾으려면 저장소 탐색기도 관리 계층에 액세스해야 합니다.

관리 계층 권한을 부여하는 역할이 없는 경우 Storage Explorer는 데이터 계층에 연결하는 데 필요한 정보를 얻을 수 없습니다.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>관리자로부터 필요한 관리 계층 권한을 얻을 수 없는 경우 어떻게 해야 합니까?

현재 이 문제에 대한 RBAC 관련 솔루션이 없습니다. 해결 방법으로 리소스에 연결하도록 SAS URI를 요청할 [수 있습니다.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)

### <a name="recommended-built-in-rbac-roles"></a>권장기본 제공 RBAC 역할

저장소 탐색기를 사용하는 데 필요한 권한을 제공할 수 있는 몇 가지 기본 제공 RBAC 역할이 있습니다. 이러한 역할 중 일부는 다음과 같습니다.
- [소유자](/azure/role-based-access-control/built-in-roles#owner): 리소스에 대한 액세스를 포함하여 모든 것을 관리합니다. **참고**: 이 역할은 키 액세스 권한을 부여합니다.
- [기여자](/azure/role-based-access-control/built-in-roles#contributor): 리소스에 대한 액세스를 제외한 모든 항목을 관리합니다. **참고**: 이 역할은 키 액세스 권한을 부여합니다.
- [판독기](/azure/role-based-access-control/built-in-roles#reader): 읽기 및 목록 리소스.
- [저장소 계정 참여자](/azure/role-based-access-control/built-in-roles#storage-account-contributor): 저장소 계정의 전체 관리. **참고**: 이 역할은 키 액세스 권한을 부여합니다.
- [저장소 Blob 데이터 소유자](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner): Azure 저장소 Blob 컨테이너 및 데이터에 대한 전체 액세스 권한입니다.
- [저장소 Blob 데이터 참여자](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): Azure 저장소 컨테이너 및 Blob을 읽고 쓰고 삭제합니다.
- [저장소 Blob 데이터 판독기](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader): Azure 저장소 컨테이너 및 Blob을 읽고 나열합니다.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>오류: 인증서 체인에서 자체 서명된 인증서(및 이와 유사한 오류)

인증서 오류는 일반적으로 다음 상황 중 하나에서 발생합니다.

- 앱은 _투명한 프록시를_통해 연결되며, 이는 서버(예: 회사 서버)가 HTTPS 트래픽을 가로채고 해독한 다음 자체 서명된 인증서를 사용하여 암호화하는 것을 의미합니다.
- 수신하는 HTTPS 메시지에 자체 서명된 SSL 인증서를 삽입하는 응용 프로그램을 실행하고 있습니다. 인증서를 삽입하는 응용 프로그램의 예로는 바이러스 백신 및 네트워크 트래픽 검사 소프트웨어가 있습니다.

Storage Explorer에서 자체 서명되거나 신뢰할 수 없는 인증서가 있으면 수신된 HTTPS 메시지가 변경되었는지 여부를 더 이상 알 수 없습니다. 자체 서명된 인증서의 복사본이 있는 경우 Storage Explorer에 다음 단계를 수행하여 인증서를 신뢰하도록 지시할 수 있습니다.

1. 인증서의 기본 64 인코딩된 X.509(.cer) 복사본을 가져옵니다.
2. **SSL 인증서** > **가져오기 인증서** **편집으로** > 이동한 다음 파일 선택기를 사용하여 .cer 파일을 찾고 선택하고 엽니다.

이 문제는 여러 인증서(루트 및 중간)가 있는 경우에도 발생할 수 있습니다. 이 오류를 해결하려면 두 인증서를 모두 추가해야 합니다.

인증서의 출신 위치를 잘 모르는 경우 다음 단계를 수행하여 인증서를 찾습니다.

1. OpenSSL을 설치합니다.
    * [윈도우](https://slproweb.com/products/Win32OpenSSL.html): 라이트 버전 중 어느 것이든 충분해야 합니다.
    * 맥과 리눅스: 운영 체제에 포함 되어야 합니다.
2. OpenSSL을 실행합니다.
    * 윈도우: 설치 디렉토리를 열고 **/bin/** 를 선택한 다음 **opensl.exe를**두 번 클릭합니다.
    * 맥과 리눅스 : 터미널에서 실행합니다. `openssl`
3. `s_client -showcerts -connect microsoft.com:443`을 실행합니다.
4. 자체 서명된 인증서를 찾습니다. 어떤 인증서가 자체 서명되었는지 잘 모르는 경우 주체와 `("s:")` 발급자가 `("i:")` 동일한 곳에 기록하십시오.
5. 자체 서명된 인증서를 찾으면 각 인증서에 대해 복사하여 새 `-----BEGIN CERTIFICATE-----` .cer 파일에 복사하여 `-----END CERTIFICATE-----` 붙여넣습니다.
6. 저장소 탐색기를 열고**SSL 인증서** > 가져오기 인증서 **편집으로** > **이동합니다.** 그런 다음 파일 선택기를 사용하여 만든 .cer 파일을 찾고 선택하고 엽니다.

다음 단계에 따라 자체 서명된 인증서를 찾을 수 없는 경우 피드백 도구를 통해 문의하십시오. `--ignore-certificate-errors` 플래그를 사용하여 명령줄에서 저장소 탐색기를 열 수도 있습니다. 이 플래그로 열면 저장소 탐색기는 인증서 오류를 무시합니다.

## <a name="sign-in-issues"></a>로그인 문제

### <a name="blank-sign-in-dialog-box"></a>빈 로그인 대화 상자

빈 로그인 대화 상자는 Active Directory 페더레이션 서비스(AD FS)가 전자에서 지원되지 않는 리디렉션을 수행하라는 저장소 탐색기를 묻는 메시지가 표시될 때 가장 자주 발생합니다. 이 문제를 해결하려면 로그인에 장치 코드 흐름을 사용할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 왼쪽 수직 도구 모음에서 **설정을**엽니다. 설정 패널에서 응용 **프로그램** > **로그인으로**이동합니다. **장치 코드 흐름 로그인 사용**사용 을 활성화합니다.
2. **연결** 대화 상자를 엽니다(왼쪽 세로 막대의 플러그 아이콘을 통해 또는 계정 패널에서 **계정 추가를** 선택하여).
3. 로그인할 환경을 선택합니다.
4. **로그인**을 선택합니다.
5. 다음 패널의 지침을 따릅니다.

기본 브라우저가 이미 다른 계정에 로그인되어 있으므로 사용하려는 계정에 로그인할 수 없는 경우 다음 중 하나를 수행합니다.

- 링크 및 코드를 브라우저의 프라이빗 세션에 수동으로 복사합니다.
- 링크 및 코드를 다른 브라우저에 수동으로 복사합니다.

### <a name="reauthentication-loop-or-upn-change"></a>재인증 루프 또는 UPN 변경

다시 인증 루프에 있거나 계정 중 하나의 UPN을 변경한 경우 다음 단계를 따르세요.

1. 모든 계정을 제거한 다음 저장소 탐색기를 닫습니다.
2. 컴퓨터에서 .IdentityService 폴더를 삭제합니다. Windows에서 폴더는 `C:\users\<username>\AppData\Local`에 있습니다. Mac 및 Linux의 경우 사용자 디렉토리의 루트에서 폴더를 찾을 수 있습니다.
3. Mac 또는 Linux를 실행 중인 경우 운영 체제의 키 저장소에서 Microsoft.Developer.IdentityService 항목을 삭제해야 합니다. Mac에서 키 저장소는 *그놈 키체인* 응용 프로그램입니다. Linux에서 응용 프로그램은 일반적으로 _Keyring이라고_하지만 배포판에 따라 이름이 다를 수 있습니다.

### <a name="conditional-access"></a>조건부 액세스

저장소 탐색기에서 사용하는 Azure AD 라이브러리의 제한 때문에 Windows 10, Linux 또는 macOS에서 저장소 탐색기를 사용할 때 조건부 액세스가 지원되지 않습니다.

## <a name="mac-keychain-errors"></a>Mac 키 집합 오류

macOS 키체인은 저장소 탐색기 인증 라이브러리에 문제를 일으키는 상태로 입력할 수 있습니다. 키체인을 이 상태로 벗어나려면 다음 단계를 따르십시오.

1. Storage Explorer를 닫습니다.
2. 키체인 을 엽니다 (명령 + 스페이스 바를 누르고 **키 체인을**입력하고 Enter를 누릅니다).
3. "로그인" 키체인을 선택합니다.
4. 자물쇠 아이콘을 선택하여 키체인을 잠급전이 합니다. (프로세스가 완료되면 자물쇠가 잠긴 것처럼 보입니다. 열려 있는 앱에 따라 몇 초 정도 걸릴 수 있습니다.

    ![자물쇠 아이콘](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage Explorer를 엽니다.
6. "서비스 허브가 키체인에 액세스하려고 합니다." 등의 메시지가 표시됩니다. Mac 관리자 계정 암호를 입력하고 **항상 허용(또는** **항상 허용이** 없는 경우 **허용)을** 선택합니다.
7. 로그인을 시도합니다.

### <a name="general-sign-in-troubleshooting-steps"></a>일반적인 로그인 문제 해결 단계

* macOS를 사용 중이고 **인증 대기** 대화 상자 위에 로그인 창이 나타나지 않는 경우 [다음 단계를](#mac-keychain-errors)시도해 보십시오.
* 저장소 탐색기를 다시 시작합니다.
* 인증 창이 비어 있는 경우 1분 이상 기다렸다가 인증 대화 상자를 닫습니다.
* 컴퓨터와 저장소 탐색기 모두에 대해 프록시 및 인증서 설정이 제대로 구성되어 있는지 확인합니다.
* Windows를 실행 중이고 동일한 컴퓨터에서 로그인 자격 증명에 대한 Visual Studio 2019에 액세스할 수 있는 경우 Visual Studio 2019에 로그인해 보십시오. Visual Studio 2019에 성공적으로 로그인한 후 저장소 탐색기를 열고 계정 패널에서 계정을 볼 수 있습니다.

이러한 메서드가 작동하지 않으면 [GitHub 에서 문제를 엽니다.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>누락된 구독 및 손상된 테넌트

성공적으로 로그인한 후 구독을 검색할 수 없는 경우 다음 문제 해결 방법을 시도해 보십시오.

* 해당 계정이 원하는 구독에 액세스할 수 있는지 확인합니다. 사용하려는 Azure 환경에 대한 포털에 로그인하여 액세스를 확인할 수 있습니다.
* 올바른 Azure 환경(Azure, Azure China 21Vianet, Azure 독일, Azure 미국 정부 또는 사용자 지정 환경)을 통해 로그인했는지 확인합니다.
* 프록시 서버 뒤에 있는 경우 저장소 탐색기 프록시를 올바르게 구성했는지 확인합니다.
* 계정을 제거하고 다시 추가해 봅을 사용해 보세요.
* "추가 정보" 링크가 있는 경우 실패한 테넌트의 보고되는 오류 메시지를 확인합니다. 오류 메시지에 응답하는 방법을 잘 모르는 경우 [GitHub 에서 문제를 열어](https://github.com/Microsoft/AzureStorageExplorer/issues)보십시오.

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>연결된 계정 또는 저장소 리소스를 제거할 수 없습니다.

UI를 통해 연결된 계정 또는 저장소 리소스를 제거할 수 없는 경우 다음 폴더를 삭제하여 연결된 모든 리소스를 수동으로 삭제할 수 있습니다.

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> 이러한 폴더를 삭제하기 전에 저장소 탐색기를 닫습니다.

> [!NOTE]
> SSL 인증서를 가져온 적이 있는 경우 `certs` 디렉터리 내용을 백업합니다. 다음에는 백업을 사용하여 SSL 인증서를 다시 가져올 수 있습니다.

## <a name="proxy-issues"></a>프록시 문제

먼저 입력한 다음 정보가 올바른지 확인합니다.

* 프록시 URL 및 포트 번호
* 프록시에 필요한 경우 사용자 이름 및 암호

> [!NOTE]
> 저장소 탐색기프록시 설정을 구성하기 위한 프록시 자동 구성 파일을 지원하지 않습니다.

### <a name="common-solutions"></a>일반적인 솔루션

그래도 문제가 발생하면 다음 문제 해결 방법을 시도해 보십시오.

* 프록시를 사용하지 않고 인터넷에 연결할 수 있는 경우 저장소 탐색기가 프록시 설정을 사용하지 않고 작동하는지 확인합니다. 이 경우 프록시 설정에 문제가 있을 수 있습니다. 관리자와 협력하여 문제를 식별합니다.
* 프록시 서버를 사용하는 다른 응용 프로그램이 예상대로 작동하는지 확인합니다.
* 사용하려는 Azure 환경에 대한 포털에 연결할 수 있는지 확인합니다.
* 서비스 엔드포인트에서 응답을 수신할 수 있는지 확인합니다. 엔드포인트 URL 중 하나를 브라우저에 입력합니다. 연결할 수 있는 경우 InvalidQueryParameterValue 또는 유사한 XML 응답을 받아야 합니다.
* 다른 사용자가 Storage Explorer와 프록시 서버를 함께 사용하고 있는 경우 연결할 수 있는지 확인합니다. 할 수 있는 경우 프록시 서버 관리자에게 문의해야 할 수 있습니다.

### <a name="tools-for-diagnosing-issues"></a>문제를 진단하기 위한 도구

Windows용 Fiddler와 같은 네트워킹 도구가 있는 경우 다음과 같이 문제를 진단할 수 있습니다.

* 프록시를 통해 작업해야 하는 경우 프록시를 통해 연결하도록 네트워킹 도구를 구성해야 할 수 있습니다.
* 네트워킹 도구에서 사용하는 포트 번호를 확인합니다.
* Storage Explorer에 로컬 호스트 URL과 네트워킹 도구의 포트 번호를 프록시 설정으로 입력합니다. 이 작업을 올바르게 수행하면 네트워킹 도구가 Storage Explorer에서 관리 및 서비스 끝점으로 네트워크 요청을 로깅하기 시작합니다. 예를 들어 `https://cawablobgrs.blob.core.windows.net/` 브라우저에서 Blob 끝점에 대해 입력하면 다음과 유사한 응답을 받게 됩니다.

  ![코드 샘플](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  이 응답은 리소스에 액세스할 수 없더라도 리소스가 있음을 나타냅니다.

### <a name="contact-proxy-server-admin"></a>프록시 서버 관리자 문의

프록시 설정이 올바르면 프록시 서버 관리자에게 다음 을 문의해야 할 수 있습니다.

* 프록시가 Azure 관리 또는 리소스 끝점에 대한 트래픽을 차단하지 않는지 확인합니다.
* 프록시 서버에서 사용하는 인증 프로토콜을 확인합니다. 저장소 탐색기는 현재 NTLM 프록시를 지원하지 않습니다.

## <a name="unable-to-retrieve-children-error-message"></a>"하위 항목을 검색할 수 없음" 오류 메시지

프록시를 통해 Azure에 연결되어 있는 경우 프록시 설정이 올바른지 확인합니다. 구독 또는 계정 소유자로부터 리소스에 대한 액세스 권한이 부여된 경우 해당 리소스에 대한 사용 권한을 읽거나 나열했는지 확인합니다.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>연결 문자열에 전체 구성 설정이 없습니다.

이 오류 메시지가 발생하면 저장소 계정에 대한 키를 가져오는 데 필요한 권한이 없을 수 있습니다. 이러한 경우를 확인하려면 포털로 이동하여 저장소 계정을 찾습니다. 저장소 계정의 노드를 마우스 오른쪽 단추로 클릭하고 **Portal에서 열기를**선택하여 이 작업을 수행할 수 있습니다. 그런 다음 **액세스 키** 블레이드로 이동합니다. 키를 볼 수 있는 권한이 없는 경우 '액세스 권한이 없습니다'라는 메시지가 표시됩니다. 이 문제를 해결하려면 다른 사람의 계정 키를 구하여 이름과 키를 연결하거나 다른 사람에게 저장소 계정에 SAS를 요청하고 저장소 계정을 연결하는 데 사용할 수 있습니다.

계정 키가 표시되면 문제를 해결하는 데 도움이 되도록 GitHub에 문제를 제출하십시오.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>새 연결을 추가하는 동안 오류가 발생했습니다: TypeError: 정의되지 않은 속성 '버전'을 읽을 수 없음

사용자 지정 연결을 추가하려고 할 때 이 오류 메시지가 발생하면 로컬 자격 증명 관리자에 저장된 연결 데이터가 손상되었을 수 있습니다. 이 문제를 해결하려면 손상된 로컬 연결을 삭제한 다음 다시 추가하십시오.

1. 저장소 탐색기를 시작합니다. 메뉴에서**개발자 도구를 전환하는**데 도움이 **됩니다.** > 
2. 응용 **프로그램** 탭에서 열린 창에서 **로컬** 저장소(왼쪽)> **file://.**
3. 문제가 있는 연결 유형에 따라 해당 키를 찾은 다음 해당 값을 텍스트 편집기에 복사합니다. 값은 다음과 같이 사용자 지정 연결 이름의 배열입니다.
    * Storage 계정
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob 컨테이너
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * 파일 공유
        * `StorageExplorer_CustomConnections_Files_v1`
    * 큐
        * `StorageExplorer_CustomConnections_Queues_v1`
    * 테이블
        * `StorageExplorer_CustomConnections_Tables_v1`
4. 현재 연결 이름을 저장한 후 개발자 도구의 `[]`값을 로 설정합니다.

손상되지 않은 연결을 유지하려는 경우 다음 단계를 사용하여 손상된 연결을 찾을 수 있습니다. 기존 연결을 모두 잃어버리는 것이 아니라면 다음 단계를 건너뛰고 플랫폼별 지침을 따라 연결 데이터를 지울 수 있습니다.

1. 텍스트 편집기에서 각 연결 이름을 개발자 도구에 다시 추가한 다음 연결이 여전히 작동하는지 확인합니다.
2. 연결이 올바르게 작동하면 손상되지 않고 안전하게 그대로 둘 수 있습니다. 연결이 작동하지 않는 경우 개발자 도구에서 해당 값을 제거하고 나중에 다시 추가할 수 있도록 기록합니다.
3. 모든 연결을 검사할 때까지 반복합니다.

모든 연결을 거치면 다시 추가되지 않은 모든 연결 이름에 대해 손상된 데이터(있는 경우)를 지우고 Storage Explorer의 표준 단계를 사용하여 다시 추가해야 합니다.

# <a name="windows"></a>[Windows](#tab/Windows)

1. **시작** 메뉴에서 자격 **증명 관리자를** 검색하고 엽니다.
2. Windows **자격 증명으로 이동합니다.**
3. 일반 자격 증명에서 `<connection_type_key>/<corrupted_connection_name>` 키가 있는 항목(예: `StorageExplorer_CustomConnections_Accounts_v1/account1`예:)을 **찾습니다.**
4. 이러한 항목을 삭제하고 연결을 다시 추가합니다.

# <a name="macos"></a>[macOS](#tab/macOS)

1. 스포트라이트 열기(명령+스페이스바)를 열고 **키체인 액세스를**검색합니다.
2. `<connection_type_key>/<corrupted_connection_name>` 키가 있는 항목(예: )을 `StorageExplorer_CustomConnections_Accounts_v1/account1`찾습니다.
3. 이러한 항목을 삭제하고 연결을 다시 추가합니다.

# <a name="linux"></a>[Linux](#tab/Linux)

로컬 자격 증명 관리는 Linux 배포판에 따라 다릅니다. Linux 배포판에서 로컬 자격 증명 관리를 위한 기본 제공 GUI 도구를 제공하지 않는 경우 타사 도구를 설치하여 로컬 자격 증명을 관리할 수 있습니다. 예를 들어 Linux 로컬 자격 증명을 관리하기 위한 오픈 소스 GUI 도구인 [Seahorse를](https://wiki.gnome.org/Apps/Seahorse/)사용할 수 있습니다.

1. 로컬 자격 증명 관리 도구를 열고 저장된 자격 증명을 찾습니다.
2. `<connection_type_key>/<corrupted_connection_name>` 키가 있는 항목(예: )을 `StorageExplorer_CustomConnections_Accounts_v1/account1`찾습니다.
3. 이러한 항목을 삭제하고 연결을 다시 추가합니다.
---

이러한 단계를 실행한 후에도 이 오류가 발생하거나 연결이 손상된 것으로 의심되는 내용을 공유하려면 GitHub 페이지에서 [문제를 엽니다.](https://github.com/microsoft/AzureStorageExplorer/issues)

## <a name="issues-with-sas-url"></a>SAS URL 문제

SAS URL을 통해 서비스에 연결중오류가 발생하는 경우:

* URL이 리소스를 읽거나 나열하는 데 필요한 권한을 제공하는지 확인합니다.
* URL이 만료되지 않았는지 확인합니다.
* SAS URL이 액세스 정책을 기반으로 하는 경우 액세스 정책이 철회되지 않았는지 확인합니다.

잘못된 SAS URL을 사용하여 실수로 연결한 경우 분리할 수 없는 경우 다음 단계를 따르십시오.

1. 저장소 탐색기를 실행 중인 경우 F12를 눌러 개발자 도구 창을 엽니다.
2. 응용 **프로그램** 탭에서 왼쪽트리에서 **로컬 저장소** > **file://** 선택합니다.
3. 문제가 있는 SAS URI의 서비스 유형과 연결된 키를 찾습니다. 예를 들어 잘못된 SAS URI가 Blob 컨테이너에 대한 것이면 `StorageExplorer_AddStorageServiceSAS_v1_blob`으로 명명된 키를 찾아봅니다.
4. 키의 값은 JSON 배열이어야 합니다. 잘못된 URI와 연결된 개체를 찾은 다음 삭제합니다.
5. Storage Explorer를 다시 로드하려면 Ctrl+R 키를 누릅니다.

## <a name="linux-dependencies"></a>Linux 종속성

저장 탐색기 1.10.0 이상은 스냅 스토어에서 스냅으로 사용할 수 있습니다. 저장소 탐색기 스냅은 모든 종속성을 자동으로 설치하고 새 버전의 스냅을 사용할 수 있을 때 업데이트됩니다. 저장소 탐색기 스냅을 설치하는 것이 권장되는 설치 방법입니다.

저장소 탐색기는 암호 관리자를 사용해야 하며, 저장소 탐색기가 올바르게 작동하기 전에 수동으로 연결해야 할 수 있습니다. 다음 명령을 실행하여 저장소 탐색기를 시스템의 암호 관리자에 연결할 수 있습니다.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

응용 프로그램을 .tar.gz 파일로 다운로드할 수도 있지만 종속성을 수동으로 설치해야 합니다.

> [!IMPORTANT]
> .tar.gz 다운로드에 제공된 저장소 탐색기는 우분투 배포판에만 지원됩니다. 다른 배포판은 확인되지 않았으며 대체 또는 추가 패키지가 필요할 수 있습니다.

이러한 패키지는 Linux의 저장소 탐색기에서 가장 일반적인 요구 사항입니다.

* [.NET Core 2.2 런타임](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` 또는 `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> 저장소 탐색기 버전 1.7.0 및 이전 버전에는 .NET 코어 2.0이 필요합니다. .NET Core의 최신 버전이 설치되어 있는 경우 [저장소 탐색기를 패치해야](#patching-storage-explorer-for-newer-versions-of-net-core)합니다. 저장소 탐색기 1.8.0 이상을 실행하는 경우 .NET Core 2.2까지 사용할 수 있습니다. 2.2를 초과하는 버전은 현재 작동하도록 확인되지 않았습니다.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. 스토리지 탐색기를 다운로드합니다.
2. [.NET 코어 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)설치합니다.
3. 다음 명령 실행:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. 스토리지 탐색기를 다운로드합니다.
2. [.NET 코어 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)설치합니다.
3. 다음 명령 실행:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. 스토리지 탐색기를 다운로드합니다.
2. [.NET 코어 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)설치합니다.
3. 다음 명령 실행:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[우분투 14.04](#tab/1404)

1. 스토리지 탐색기를 다운로드합니다.
2. [.NET 코어 런타임을](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)설치합니다.
3. 다음 명령 실행:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>.NET 코어의 최신 버전에 대한 스토리지 탐색기 패치

저장소 탐색기 1.7.0 이상의 경우 저장소 탐색기에서 사용하는 .NET 코어 버전을 패치해야 할 수 있습니다.

1. [NuGet에서](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)StreamJsonRpc의 버전 1.5.43을 다운로드합니다. 페이지 오른쪽에 있는 "패키지 다운로드" 링크를 찾습니다.
2. 패키지를 다운로드한 후 파일 확장프로그램을 `.nupkg` `.zip`에서 로 변경합니다.
3. 패키지의 압축을 풀수 있습니다.
4. `streamjsonrpc.1.5.43/lib/netstandard1.1/` 폴더를 엽니다.
5. 저장소 `StreamJsonRpc.dll` 탐색기 폴더의 다음 위치로 복사합니다.
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure 포털에서 "탐색기에서 열기"가 작동하지 않습니다.

Azure 포털의 **탐색기 열기** 버튼이 작동하지 않으면 호환되는 브라우저를 사용하고 있는지 확인합니다. 호환성을 테스트한 브라우저는 다음과 같습니다.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>다음 단계

이러한 해결 방법 중 아무 것도 작동하지 않으면 [GitHub 에서 문제를 엽니다.](https://github.com/Microsoft/AzureStorageExplorer/issues) 왼쪽 아래 모서리에 있는 **GitHub 단추에 대한 보고서 문제를** 선택하여 이 작업을 수행할 수도 있습니다.

![사용자 의견](./media/storage-explorer-troubleshooting/feedback-button.PNG)
