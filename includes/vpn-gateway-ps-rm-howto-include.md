모듈은 두 가지 방법 즉, PowerShell 갤러리와 웹 플랫폼 설치 관리자를 통해 설치할 수 있습니다. 설치 방식은 컴퓨터에는 기본적으로 설치되는 위치에 따라 선택할 수 있지만 최종 결과는 거의 동일합니다.

PowerShell 갤러리를 통해 설치하면 파일은 기본적으로 *%ProgramFiles%\\WindowsPowerShell\\Modules*에 위치합니다. 웹 플랫폼 설치 관리자를 통해 설치하면 파일은 기본적으로 *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell*에 위치합니다. 이 때문에 나중에 cmdlet를 업데이트할 때 오류를 방지하기 위해 둘 중 하나의 방식을 고수하게 됩니다. 웹 플랫폼 설치 관리자는 매월 업데이트된 cmdlet을 받게 됩니다. 갤러리는 cmdlet가 릴리스될 때 업데이트된 버전을 받습니다. 이런 이유로 일부 사용자는 갤러리 사용을 선호합니다.

Azure PowerShell 설치에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

**PowerShell 갤러리를 통해 모듈을 설치하려면**

1. 갤러리에서 직접 리소스 관리자 모듈을 설치하려면 관리자 권한으로 Windows PowerShell을 열고 다음을 입력합니다.

		Install-Module AzureRM
		Install-AzureRM

2. 모듈을 설치하고 나면 이를 가져와 사용해야 합니다.

		Import-AzureRM

**웹 플랫폼 설치 관리자를 사용하여 모듈을 설치하려면**

- [웹 플랫폼 설치 관리자](http://aka.ms/webpi-azps)를 사용하여 모듈을 설치할 수 있습니다. 링크를 클릭하면 설치 프로그램이 시작됩니다.

- 웹 플랫폼 설치 관리자를 사용했을 때 오류가 발생하는 경우 이는 갤러리를 사용하여 이전 버전의 cmdlet가 설치되었기 때문일 수 있습니다. 이 [블로그 게시물](https://azure.microsoft.com/blog/azps-1-0/)을 참조하여 이전 버전의 모듈을 제거하여 백업한 다음 실행하세요. 일반적으로 웹 플랫폼 설치 관리자를 사용했다가 갤러리로 전환하는 경우 또는 그 반대인 경우 오류가 발생합니다. 이 문제를 해결하려면 이전에 설치한 모듈을 제거한 다음 새로운 위치에 설치하면 됩니다.

<!---HONumber=AcomDC_1217_2015-->