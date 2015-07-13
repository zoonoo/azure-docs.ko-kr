<properties
	pageTitle="Azure 명령줄 인터페이스에서 로그인(Azure CLI) | Microsoft Azure"
	description="Azure 명령줄 인터페이스(Azure CLI)에서 Azure 구독에 연결"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Azure 명령줄 인터페이스(Azure CLI)에서 Azure 구독에 연결합니다.

Azure CLI는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합입니다. 이 문서는 Azure CLI에서 Azure 구독에 연결하는 방법을 설명합니다. 설치 지침은 [Azure CLI 설치](xplat-cli-install.md)를 참조하십시오.

<a id="configure"></a>
## Azure 구독에 연결하는 방법

대부분의 명령은 Azure CLI를 통해 제공되는 Azure 계정에 대한 연결이 필요합니다. 다음 두 가지 방법으로 구독과 함께 작동하도록 Azure CLI를 구성할 수 있습니다.

* 회사 또는 학교 계정(조직 계정이라고도 함)을 사용하여 Azure에 로그인합니다. 그러면 Azure Active Directory를 사용하여 자격 증명을 인증합니다.

또는

* 게시 설정 파일을 다운로드하고 사용합니다. 그러면 구독 및 인증서가 유효한 기간 동안 관리 작업을 수행할 수 있게 해 주는 인증서가 설치됩니다.

인증 및 구독 관리에 대한 자세한 내용은 ["계정 기반 인증 및 인증서 기반 인증 간의 차이점"][authandsub]을 참조하십시오.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험][free-trial]을 참조하십시오.

> [AZURE.NOTE]가장 유연한 고급 Azure 서비스는 Azure 리소스 관리자 또는 [ARM 모드](xplat-cli-azure-resource-manager.md)를 사용합니다. ARM 모드는 아래에서 설명하는 로그인 메서드로 회사 또는 학교 계정을 사용하여 Azure에 연결해야 합니다.

### 로그인 방법 사용

이 로그인 방법은 회사 또는 학교 계정으로만 작동합니다. 이 계정은 조직에서 관리되고 조직의 Azure Active Directory에서 정의합니다.

> [AZURE.NOTE]현재 회사나 학교 계정이 없고 개인 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.
>
> 1. [Azure 포털][portal]에 로그인하고 **Active Directory**를 선택합니다.
>
> 2. 디렉터리가 없는 경우 **디렉터리 만들기**를 선택하고 요청된 정보를 제공합니다.
>
> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 이 새 사용자는 회사 또는 학교 계정입니다.
>
>     사용자를 만드는 동안 사용자의 전자 메일 주소와 임시 암호가 제공됩니다. 이 정보는 나중에 필요하므로 저장합니다.
>
> 4. Azure 포털에서 **설정**을 선택한 후 **관리자**를 선택합니다. **추가**를 선택하고 새 사용자를 공동 관리자로 추가합니다. 따라서 회사 또는 학교 계정이 Azure 구독을 관리할 수 있습니다.
>
> 5. 마지막으로, Azure 포털에서 로그아웃한 후 새 회사나 학교 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.
>
> 6. 회사 또는 학교 계정으로 로그인 할 때는 구독을 참조해야 합니다.
>
>회사 또는 학교 계정에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록][signuporg](영문)을 참조하십시오.

회사 또는 학교 계정을 사용하여 Azure CLI에서 로그인하려면 다음 명령을 사용합니다.

	azure login -u <username>

그리고 메시지가 표시되면 암호를 입력합니다.

> [AZURE.NOTE]이러한 자격 증명으로 처음 로그인하는 경우 인증 토큰을 캐시할지 확인하는 메시지가 표시됩니다. 이 메시지는 `azure logout` 아래에 설명된 명령을 이전에 사용한 경우에도 표시됩니다. 자동화 시나리오에서 이 메시지를 표시하지 않으려면 명령에 `-q` 매개 변수를 `azure login` 사용합니다.

로그아웃하려면 다음 명령을 사용합니다.

	azure logout -u <username>

> [AZURE.NOTE]계정과 연결된 구독이 Active Directory에서만 인증된 경우 로그아웃하면 구독 정보가 로컬 프로필에서 삭제됩니다. 그러나 구독에 대해 게시 설정 파일도 가져온 경우에는 로그아웃해도 Active Directory 관련 정보만 로컬 프로필에서 삭제됩니다.

### 게시 설정 파일 방법 사용

> [AZURE.NOTE]이 메서드를 사용하여 연결하면 Azure 서비스 관리(또는 ASM 모드) 명령만 사용할 수 있습니다.

계정에 대한 게시 설정을 다운로드하려면 다음 명령을 사용합니다.

	azure account download

기본 브라우저가 열리고 [Azure 포털][portal]에 로그인하라는 메시지가 표시됩니다. 로그인하면 `.publishsettings` 파일이 다운로드됩니다. 이 파일이 저장된 위치를 기록해 둡니다.

> [AZURE.NOTE]계정이 여러 개의 Azure Active Directory 테넌트와 연결된 경우 게시 설정 파일을 다운로드하려는 Active Directory를 선택하라는 메시지가 표시될 수도 있습니다.
>
> 다운로드 페이지를 사용하거나 Azure 포털을 방문하여 선택한 후에는 선택한 Active Directory가 포털 및 다운로드 페이지에서 기본값으로 사용됩니다. 기본값이 설정되고 나면 다운로드 페이지의 맨 위에 '__선택 페이지로 돌아가려면 여기를 클릭하세요.__' 텍스트가 표시됩니다. 제공된 링크를 사용하여 선택 페이지로 돌아갑니다.

다음 명령을 실행하여 `.publishsettings` 파일을 가져옵니다.

	azure account import <path to your .publishsettings file>

게시 설정을 가져온 후 `.publishsettings` 파일을 삭제해야 합니다. 더 이상 Azure CLI에 필요하지 않으며 구독에 액세스하는 데 사용될 수 있으므로 보안상 위험하기 때문입니다.

> [AZURE.NOTE]회사 또는 학교 계정으로 로그인할 때나 게시 설정을 가져올 때나 Azure 구독에 액세스하기 위한 정보가 `user` 디렉터리에 있는 `.azure` 디렉터리에 저장됩니다. `user` 디렉터리는 운영 체제에서 보호됩니다. 그러나 추가 단계를 수행하여 `user` 디렉터리를 암호화하는 것이 좋습니다. 다음과 같은 방법으로 이 작업을 수행할 수 있습니다.
>
> * Windows에서는 디렉터리 속성을 수정하거나 BitLocker를 사용합니다.
> * Mac에서는 디렉터리에 대해 FileVault를 켭니다.
> * Ubuntu에서는 암호화된 홈 디렉터리 기능을 사용합니다. 기타 Linux 배포에서도 동등한 기능을 제공합니다.

### 여러 구독

Azure 구독이 여러 개 있는 경우 Azure에 연결하면 자격 증명과 연결된 모든 구독에 액세스할 수 있습니다. 한 가지 구독이 기본 구독으로 선택되며, 작업을 수행할 때 Azure CLI에서 사용됩니다. 구독 및 기본적으로 사용되는 구독을 볼 수 있지만 `azure account list` 명령을 사용해야 합니다. 이 명령은 다음과 유사한 정보를 반환합니다.

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

위의 목록에서 **Current** 열은 현재 기본 구독으로 Azure-sub-1을 나타냅니다. 기본 구독을 변경하려면 `azure account set` 명령을 사용하고, 기본으로 사용할 구독을 지정합니다. 예:

	azure account set Azure-sub-2

이 경우 기본 구독은 Azure-sub-2로 변경됩니다.

> [AZURE.NOTE]기본 구독 변경은 즉시 적용되며 전체적으로 변경됩니다. 동일한 명령줄 인스턴스에서 실행되었든 또는 다른 인스턴스에서 실행되었든 관계없이 새 Azure CLI 명령은 새 기본 구독을 사용합니다.

Azure CLI와 함께 기본값이 아닌 구독을 사용하지만 현재 기본값을 변경하지 않으려는 경우 해당 명령에 대히 `--subscription` 옵션을 사용하고 작업에 사용할 구독의 이름을 제공할 수 있습니다.

Azure 구독에 연결하게 되면 Azure CLI 명령을 사용하여 시작할 수 있습니다. 자세한 내용은 [Azure를 사용하는 방법](xplat-cli.md)을 참조하십시오.

<a id="additional-resources"></a>
## 추가 리소스

* [서비스 관리(또는 ASM 모드) 명령과 함께 Azure CLI 사용하기][cliasm]

* [리소스 관리(또는 ASM 모드) 명령과 함께 Azure CLI 사용하기][cliarm]

* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.

* Azure CLI 또는 Azure를 사용하는 데 문제가 있는 경우 [Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home)을 방문하세요.

* Azure에 대한 자세한 내용은 [http://azure.microsoft.com/](http://azure.microsoft.com)을 참조하세요.





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=July15_HO1-->