Azure PowerShell은 현재 두 가지 릴리스, 즉, 1.0 및 0.9.8로 사용할 수 있습니다. 기존 스크립트가 있고 지금 변경하지 않으려는 경우 계속해서 0.9.8 릴리스를 사용할 수 있습니다. 1.0 릴리스를 사용하는 경우 프로덕션 환경에서 사용하기 전에 사전 프로덕션 환경에서 스크립트를 주의 깊게 테스트해야 해야 예기치 않는 상황을 방지할 수 있습니다.

1.0 cmdlet는 명명 패턴{verb}-AzureRm{noun}을 따릅니다. 반면 0.9.8 이름은 **Rm**을 포함하지 않습니다.(예를 들어 New-AzureResourceGroup 대신 New-AzureRmResourceGroup) 반면 0.9.8 이름은 **Switch-AzureMode AzureResourceManager** 명령을 실행하여 리소스 관리자 모드를 사용하도록 설정해야 합니다. 이 명령은 1.0에서는 필요하지 않습니다.

새로운 기능은 1.0 릴리스에만 추가됩니다. 릴리스 설치 및 제거 방법을 포함하여 1.0 릴리스에 대한 자세한 내용은 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)를 참조하세요.

