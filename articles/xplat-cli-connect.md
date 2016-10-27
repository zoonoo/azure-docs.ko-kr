<properties
    pageTitle="CLI에서 Azure에 로그인 | Microsoft Azure"
    description="Mac, Linux 및 Windows용 Azure 명령줄 인터페이스(Azure CLI)에서 Azure 구독에 연결"
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"
"/>


# <a name="log-in-to-azure-from-the-azure-cli"></a>Azure CLI에서 Azure에 로그인

Azure CLI는 Azure 리소스 작업을 위한 플랫폼 간 공개 소스 명령 집합입니다. 이 문서에서는 Azure 계정 자격 증명을 제공하여 Azure CLI를 Azure 구독에 연결하는 다양한 방법을 설명합니다.

* `azure login` CLI 명령을 실행하여 Azure Active Directory를 통해 인증을 받습니다. 이 방법을 사용하면 두 [명령 모드](#CLI-command-modes)에서 CLI 명령에 액세스할 수 있습니다. 추가 옵션 없이 명령을 실행하면 `azure login`에서 웹 포털을 통해 대화형으로 계속 로그인할지 묻습니다. 추가 `azure login` 명령 옵션에 대해서는 이 문서의 시나리오를 참조하거나 `azure login --help`를 입력하세요.

* Azure 서비스 관리 모드 CLI 명령만 사용해야 하는 경우(대부분의 새로운 배포에서는 권장되지 않음) 컴퓨터에 게시 설정 파일을 다운로드하고 설치할 수 있습니다. 

이미 CLI를 설치하지 않은 경우 [Azure CLI 설치](xplat-cli-install.md)를 참조하세요. Azure 구독이 없는 경우 몇 분 만에 [무료 계정](http://azure.microsoft.com/free/) 을 만들 수 있습니다. 

다른 계정 ID 및 Azure 구독에 대한 배경 정보는 [Azure 구독과 Azure Active Directory의 연관 관계](./active-directory/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.






## <a name="scenario-1:-azure-login-with-interactive-login"></a>시나리오 1: 대화형 로그인을 사용한 Azure 로그인 

특정 계정에서 CLI는 `azure login`을 실행한 후 웹 포털을 통해 웹 브라우저를 사용해서 로그인 프로세스를 계속 진행하도록 요구합니다. 이 프로세스를 *대화형 로그인*이라고 합니다. 일반적인 이유는 다단계 인증을 요구하도록 설정된 회사 또는 학교 계정(*조직 계정*이라고도 함)이 있는 경우입니다. 또한 Resource Manager 모드 명령을 사용할 경우 Microsoft 계정으로 대화형 로그인을 사용합니다.

대화형 로그인은 쉽습니다. 다음 예제와 같이 아무 옵션도 없이 `azure login`을 입력하면 됩니다.

```
azure login
```                                                                                             

출력은 다음과 유사합니다.

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. 
```
제공된 코드를 명령 출력에 복사하고 브라우저에서 http://aka.ms/devicelogin 또는 지정된 다른 페이지를 엽니다. (동일한 컴퓨터 또는 다른 컴퓨터나 장치에서 브라우저를 열 수 있습니다.) 코드를 입력하면 사용하려는 ID의 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. 해당 프로세스가 완료되면 명령 셸이 로그인을 완료합니다. 다음과 같이 표시될 수 있습니다.

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK
    
>[AZURE.NOTE]  대화형 로그인을 사용할 경우 인증 및 권한 부여는 Azure Active Directory를 사용하여 수행합니다. Microsoft 계정 ID를 사용하는 경우 로그인 프로세스에서 Azure Active Directory 기본 도메인에 액세스합니다. 무료 Azure 계정에 등록한 경우 Azure Active Directory에서 계정에 대한 기본 도메인이 자동으로 생성됩니다.

## <a name="scenario-2:-azure-login-with-a-username-and-password"></a>시나리오 2: 사용자 이름 및 암호를 사용한 Azure 로그인


다단계 인증이 필요 없는 회사 또는 학교 계정을 사용하여 인증하려면 사용자 이름(`-u`) 매개 변수와 함께 `azure login` 명령을 사용합니다. 명령줄에서 암호가 요구되거나 경우에 따라 `azure login` 명령의 추가 매개 변수로 암호를 제공할 수 있습니다. 다음 예제에서는 조직 계정의 사용자 이름을 전달합니다.

    azure login -u myUserName@contoso.onmicrosoft.com
    
그런 다음 암호를 입력하라는 메시지가 표시됩니다.

    info:    Executing command login
    Password: *********
    
그런 후에 로그인 프로세스가 완료됩니다.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

이러한 자격 증명으로 처음 로그인하는 경우 인증 토큰을 캐시할지 확인하는 메시지가 표시됩니다. 이 메시지는 `azure logout` 명령(이 문서의 뒷부분에 설명)을 이전에 사용한 경우에도 표시됩니다. 자동화 시나리오에서 이 메시지를 표시하지 않으려면 `-q` 매개 변수로 `azure login`을(를) 실행합니다.

   

## <a name="scenario-3:-azure-login-with-a-service-principal"></a>시나리오 3: 서비스 사용자와 함께 Azure 로그인 사용

Active Directory 응용 프로그램에 대한 서비스 사용자를 만들고 해당 서비스 사용자에게 구독에 대한 권한이 있는 경우 `azure login` 명령을 사용하여 서비스 사용자를 인증할 수 있습니다. 시나리오에 따라 서비스 사용자의 자격 증명을 `azure login` 명령의 명시적 매개 변수로 제공할 수 있습니다. 예를 들어 다음 명령은 서비스 사용자 이름 및 Active Directory 테넌트 ID를 전달합니다.

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

그런 다음 암호를 입력하라는 메시지가 표시됩니다. CLI 스크립트 또는 응용 프로그램 코드를 통해 자격 증명을 제공하거나 인증서를 사용하여 자동화 시나리오에 맞게 비대화식으로 서비스 사용자를 인증할 수 있습니다. 자세한 내용 및 예제는 [Azure Resource Manager를 사용하여 서비스 사용자 인증](resource-group-authenticate-service-principal-cli.md)을 참조하세요.

## <a name="scenario-4:-use-a-publish-settings-file"></a>시나리오 4: 게시 설정 파일 사용

Azure 서비스 관리 모드 CLI 명령만 사용해야 하는 경우(예를 들어 클래식 배포 모델에서 Azure VM을 배포하려는 경우) 게시 설정 파일을 사용하여 연결할 수 있습니다. 이 방법은 구독 및 인증서가 유효한 경우 관리 작업을 수행할 수 있도록 로컬 컴퓨터에 인증서를 설치합니다. 

* 계정에 대한 **게시 설정 파일을 다운로드하려면** `azure config mode asm`CLI가 서비스 관리 모드인지 확인합니다. 그런 후 다음 명령을 실행합니다.

        azure account download

기본 브라우저가 열리고 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인하라는 메시지가 표시됩니다. 로그인하면 `.publishsettings` 파일이 다운로드됩니다. 이 파일이 저장된 위치를 기록해 둡니다.

>[AZURE.NOTE] 계정이 여러 개의 Azure Active Directory 테넌트와 연결된 경우 게시 설정 파일을 다운로드하려는 Active Directory를 선택하라는 메시지가 표시될 수도 있습니다.

Azure 클래식 포털을 방문하여 다운로드 페이지를 사용하도록 선택한 후에는 선택한 Active Directory가 클래식 포털 및 다운로드 페이지에서 기본값으로 사용됩니다. 기본값이 설정되고 나면 다운로드 페이지의 맨 위에 '__선택 페이지로 돌아가려면 여기를 클릭하십시오.__' 텍스트가 표시됩니다. 제공된 링크를 사용하여 선택 페이지로 돌아갑니다.

* **게시 설정 파일을 가져오려면**다음 명령을 실행합니다.

        azure account import <path to your .publishsettings file>

>[AZURE.IMPORTANT]게시 설정을 가져온 후에는 `.publishsettings` 파일을 삭제해야 합니다. 더 이상 Azure CLI에 필요하지 않으며 구독에 액세스하는 데 사용될 수 있으므로 보안상 위험하기 때문입니다.

## <a name="cli-command-modes"></a>CLI 명령 모드

Azure CLI는 Azure 리소스로 작업하는 두 가지 명령 모드(명령 집합이 서로 다름)를 제공합니다.

* **Resource Manager 모드** - Resource Manager 배포 모델에서 Azure 리소스를 사용합니다. 이 모드를 설정하려면 `azure config mode arm`을 실행합니다.

* **서비스 관리 모드** - 클래식 배포 모델에서 Azure 리소스를 사용합니다. 이 모드를 설정하려면 `azure config mode asm`을 실행합니다.

처음 설치 시 CLI의 현재 릴리스는 Resource Manager 모드입니다.

>[AZURE.NOTE]리소스 관리자 모드와 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

## <a name="multiple-subscriptions"></a>여러 구독

Azure 구독이 여러 개 있는 경우 Azure에 연결하면 자격 증명과 연결된 모든 구독에 액세스할 수 있습니다. 기본 구독으로 한 가지 구독이 선택되며,  작업을 수행할 때 Azure CLI에서 사용됩니다. `azure account list` 명령을 사용하여 현재 기본 구독을 포함하는 구독을 확인할 수 있습니다. 이 명령은 다음과 유사한 정보를 반환합니다.

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

위의 목록에서 **Current** 열은 현재 기본 구독으로 Azure-sub-1을 나타냅니다. 기본 구독을 변경하려면 `azure account set` 명령을 사용하고, 기본으로 사용할 구독을 지정합니다. 예:

    azure account set Azure-sub-2

이 경우 기본 구독이 Azure-sub-2로 변경됩니다.

> [AZURE.NOTE] 기본 구독을 변경하면 즉시 적용되며 전역적으로 변경됩니다. 동일한 명령줄 인스턴스에서 실행되거나 다른 인스턴스를 실행되는 새 Azure CLI 명령은 새 기본 구독을 사용합니다.

Azure CLI와 함께 기본값이 아닌 구독을 사용하지만 현재 기본값을 변경하지 않으려는 경우 해당 명령에 대히 `--subscription` 옵션을 사용하고 작업에 사용할 구독의 이름을 제공할 수 있습니다.

Azure 구독에 연결하게 되면 Azure CLI 명령을 사용하여 Azure 리소스 작업을 시작할 수 있습니다.



## <a name="storage-of-cli-settings"></a>CLI 설정 저장소

`azure login` 명령을 사용하여 로그인하거나 게시 설정을 가져올 때 CLI 프로필과 로그가 `user` 디렉터리에 있는 `.azure` 디렉터리에 저장됩니다. `user` 디렉터리는 운영 체제에 의해 보호됩니다. 그러나 추가 단계를 수행하여 `user` 디렉터리를 암호화하는 것이 좋습니다. 다음과 같은 방법으로 이 작업을 수행할 수 있습니다.

* Windows에서는 디렉터리 속성을 수정하거나 BitLocker를 사용합니다.
* Mac에서는 디렉터리에 대해 FileVault를 켭니다.
* Ubuntu에서는 암호화된 홈 디렉터리 기능을 사용합니다. 기타 Linux 배포에서도 유사한 기능을 제공합니다.

## <a name="logging-out"></a>로그아웃

로그아웃하려면 다음 명령을 사용합니다.

    azure logout -u <username>

계정과 연결된 구독이 Active Directory에서만 인증된 경우 로그아웃하면 구독 정보가 로컬 프로필에서 삭제됩니다. 그러나 구독에 대해 게시 설정 파일도 가져온 경우에는 로그아웃해도 Active Directory 관련 정보만 로컬 프로필에서 삭제됩니다.
## <a name="next-steps"></a>다음 단계

* CLI 명령을 사용하려면 [Resource Manager 모드의 Azure CLI 명령](./virtual-machines/azure-cli-arm-commands.md) 및 [서비스 관리 모드의 Azure CLI 명령](virtual-machines-command-line-tools.md)을 참조하세요.

* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.

* Azure CLI 또는 Azure를 사용하는 데 문제가 있는 경우 [Azure 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)을 방문하세요.





<!--HONumber=Oct16_HO2-->


