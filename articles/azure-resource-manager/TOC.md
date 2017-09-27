# 개요
## [Resource Manager란?](resource-group-overview.md)
## [리소스 공급자 및 형식](resource-manager-supported-services.md)
## [Resource Manager 및 클래식 배포](resource-manager-deployment-model.md)
## [구독 거버넌스](resource-manager-subscription-governance.md)
## [Managed Applications](managed-application-overview.md)

# 시작
## [템플릿 만들기 및 배포](resource-manager-create-first-template.md)
## [템플릿에 VS Code 확장](resource-manager-vscode-extension.md)
## [Resource Manager와 Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 샘플
## [코드 샘플](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## Azure PowerShell
### [템플릿 배포](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [템플릿 배포](resource-manager-samples-cli-deploy.md)

# 방법
## 템플릿 만들기
### [템플릿 섹션](resource-group-authoring-templates.md)
### [템플릿 모범 사례](resource-manager-template-best-practices.md)
### [다른 템플릿에 대한 연결](resource-group-linked-templates.md)
### [리소스 간 종속성 정의](resource-group-define-dependencies.md)
### [여러 인스턴스 만들기](resource-group-create-multiple.md)
### [위치 설정](resource-manager-template-location.md)
### [태그 할당](resource-manager-template-tags.md)
### [리소스 이름 및 유형을 자식 설정](resource-manager-template-child-resource.md)
### [리소스 업데이트](resource-manager-update.md)
### [매개 변수에 대한 개체 사용](resource-manager-objects-as-parameters.md)
### [연결된 템플릿 간 상태 공유](best-practices-resource-manager-state.md)
### [템플릿 설계 패턴](best-practices-resource-manager-design-templates.md)


## 배포
### Azure PowerShell
#### [템플릿 배포](resource-group-template-deploy.md)
#### [SAS 토큰으로 개인 템플릿 배포](resource-manager-powershell-sas-token.md)
#### [템플릿 내보내기 및 재배포](resource-manager-export-template-powershell.md)
### Azure CLI
#### [템플릿 배포](resource-group-template-deploy-cli.md)
#### [SAS 토큰으로 개인 템플릿 배포](resource-manager-cli-sas-token.md)
#### [템플릿 내보내기 및 재배포](resource-manager-export-template-cli.md)
### Azure portal
#### [리소스 배포](resource-group-template-deploy-portal.md)
#### [템플릿 내보내기](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [크로스 리소스 그룹 배포](resource-manager-cross-resource-group-deployment.md)
### [Visual Studio Team Services와의 지속적인 통합](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)

## 관리
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [태그를 사용하여 리소스 구성](resource-group-using-tags.md)
### [새 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)
### [거버넌스 예제](resource-manager-subscription-examples.md)

## 액세스 제어
### 서비스 주체 만들기
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [Azure 포털](resource-group-create-service-principal-portal.md)
### [구독에 액세스하는 인증 API](resource-manager-api-authentication.md)
### [리소스 잠금](resource-group-lock-resources.md)

## 리소스 정책 설정
### [리소스 정책이란?](resource-manager-policy.md)
### [Azure Portal을 사용하여 정책 할당](resource-manager-policy-portal.md)
### [스크립트를 사용하여 정책 할당](resource-manager-policy-create-assign.md)
### 예
#### [태그](resource-manager-policy-tags.md)
#### [명명 규칙](resource-manager-policy-naming-convention.md)
#### [네트워크](resource-manager-policy-network.md)
#### [저장소](resource-manager-policy-storage.md)
#### [Linux VM](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Windows VM](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## 관리되는 응용 프로그램 사용
### [서비스 카탈로그 응용 프로그램 게시](managed-application-publishing.md)
### [서비스 카탈로그 응용 프로그램 사용](managed-application-consumption.md)
### [마켓플레이스 응용 프로그램 게시](managed-application-author-marketplace.md)
### [마켓플레이스 응용 프로그램 사용](managed-application-consume-marketplace.md)
### [UI 정의 만들기](managed-application-createuidefinition-overview.md)

## 감사
### [활동 로그 보기](resource-group-audit.md)
### [배포 작업 보기](resource-manager-deployment-operations.md)

## 문제 해결
### [일반적인 배포 오류](resource-manager-common-deployment-errors.md)
### [배포 오류 이해](resource-manager-troubleshoot-tips.md)
### 오류 해결
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### 가상 컴퓨터 배포 오류
#### Linux
##### [배포 문제](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [프로비저닝 및 할당 문제](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [일반적인 오류 메시지](../virtual-machines/linux/error-messages.md)
#### Windows
##### [배포 문제](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [프로비저닝 및 할당 문제](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [일반적인 오류 메시지](../virtual-machines/windows/error-messages.md)

# 참조
## [템플릿 형식](/azure/templates/)
## [템플릿 함수](resource-group-template-functions.md)
### [배열 및 개체 함수](resource-group-template-functions-array.md)
### [비교 함수](resource-group-template-functions-comparison.md)
### [배포 함수](resource-group-template-functions-deployment.md)
### [논리 함수](resource-group-template-functions-logical.md)
### [숫자 함수](resource-group-template-functions-numeric.md)
### [리소스 함수](resource-group-template-functions-resource.md)
### [문자열 함수](resource-group-template-functions-string.md)
## [UI 정의 함수](managed-application-createuidefinition-functions.md)
## [UI 정의 요소](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST (영문)](/rest/api/resources/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [제한 요청](resource-manager-request-limits.md)
## [비동기 작업 추적](resource-manager-async-operations.md)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
