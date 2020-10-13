---
title: Azure Key Vault 개발자 가이드
description: 개발자는 Microsoft Azure 환경 내에서 Azure Key Vault를 사용하여 암호화 키를 관리할 수 있습니다.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 6564804b7003b5e1c166868dae1bfaac7bd28fa5
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940467"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 개발자 가이드

Key Vault를 사용하면 애플리케이션 내에서 중요한 정보를 안전하게 액세스할 수 있습니다.

- 키, 암호 및 인증서는 코드를 직접 작성 하지 않고도 보호 되며 응용 프로그램에서 쉽게 사용할 수 있습니다.
- 핵심 소프트웨어 기능을 제공 하는 데 집중할 수 있도록 고객이 자신의 키, 암호 및 인증서를 소유 하 고 관리 하도록 할 수 있습니다. 이러한 방식으로 응용 프로그램은 고객의 테 넌 트 키, 암호 및 인증서에 대 한 책임 또는 잠재적인 책임을 소유 하지 않습니다.
- 응용 프로그램은 서명 및 암호화를 위해 키를 사용할 수 있지만 응용 프로그램 외부에서 키 관리를 유지 합니다. 키에 대 한 자세한 내용은 [키](../keys/about-keys.md) 정보를 참조 하세요.
- 암호, 액세스 키, Key Vault에 암호를 저장 하는 sas 토큰과 같은 자격 증명을 암호로 관리할 수 있습니다. [비밀 정보](../secrets/about-secrets.md) 를 참조 하세요.
- 인증서를 관리 합니다. 자세한 내용은 [인증서 정보](../certificates/about-certificates.md) 를 참조 하세요.

Azure Key Vault에 대한 일반적인 내용은 [Key Vault란?](overview.md)을 참조하세요.

## <a name="public-previews"></a>공개 미리 보기

새로운 Key Vault 기능의 공개 미리 보기가 정기적으로 릴리스됩니다. 공개 미리 보기 기능을 사용해 보고 의견을 보내 주시기 바랍니다 azurekeyvault@microsoft.com .

## <a name="creating-and-managing-key-vaults"></a>주요 자격 증명 모음 만들기 및 관리

다른 Azure 서비스와 마찬가지로 Key Vault 관리는 Azure Resource Manager 서비스를 통해 수행 됩니다. Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 계정에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 자세한 내용은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) 를 참조 하세요.

관리 계층에 대 한 액세스는 [Azure 역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)를 통해 제어 됩니다. Key Vault 관리 계층 (관리 또는 제어 평면이 라고도 함)을 사용 하 여 데이터 평면에서 관리 되는 키, 암호 및 인증서를 비롯 하 여 키 자격 증명 모음 및 해당 특성을 만들고 관리할 수 있습니다. 미리 정의 된 역할을 사용 하 여 `Key Vault Contributor` Key Vault에 대 한 관리 액세스 권한을 부여할 수 있습니다.     

**Key vault 관리에 대 한 API 및 Sdk:**

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault)<br>[빠른 시작](quick-create-cli.md)|[참조](/powershell/module/az.keyvault)<br>[빠른 시작](quick-create-powershell.md)|[참조](/rest/api/keyvault/)|[참조](/azure/templates/microsoft.keyvault/vaults)|[참조](/dotnet/api/microsoft.azure.management.keyvault)|[참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[참조](/java/api/com.microsoft.azure.management.keyvault)|[참조](/javascript/api/@azure/arm-keyvault)|

설치 패키지 및 소스 코드는 [클라이언트 라이브러리](client-libraries.md) 를 참조 하세요.

Key Vault 관리 평면에 대 한 자세한 내용은 [Key Vault 관리 평면](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac) 을 참조 하세요.

## <a name="authenticate-to-key-vault-in-code"></a>코드의 Key Vault에 대 한 인증

Azure ad 보안 주체에 액세스 권한을 부여 해야 하는 Azure AD 인증을 사용 하 Key Vault입니다. Azure AD 보안 주체는 사용자, 응용 프로그램 서비스 주체, [azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)또는 모든 유형의 보안 주체 그룹 일 수 있습니다.

### <a name="authentication-best-practices"></a>인증 모범 사례

Azure에 배포 된 응용 프로그램에 관리 되는 id를 사용 하는 것이 좋습니다. 관리 id를 지원 하지 않거나 응용 프로그램을 온-프레미스에 배포 하는 경우 Azure 서비스를 사용 하는 경우 인증서를 사용 하는 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 를 사용할 수 있습니다. 이 시나리오에서는 인증서를 Key Vault 저장 하 고 자주 회전 해야 합니다. 보안을 사용 하는 서비스 주체는 개발 및 테스트 환경에 사용할 수 있으며, 로컬 또는 Cloud Shell 사용자 계정을 사용 하 여 사용 하는 것이 좋습니다.

환경 당 권장 되는 보안 주체:
- **프로덕션 환경**:
  - 인증서를 사용 하는 관리 id 또는 서비스 주체
- **테스트 및 개발 환경**:
  - 관리 id, 인증서 또는 서비스 사용자가 암호를 사용 하는 서비스 주체
- **로컬 개발**:
  - 암호를 사용 하는 사용자 계정 또는 서비스 주체

위의 인증 시나리오는 **Azure id 클라이언트 라이브러리** 에서 지원 되 고 Key Vault sdk와 통합 됩니다. Azure Id 라이브러리는 코드를 변경 하지 않고도 다양 한 환경 및 플랫폼에서 사용할 수 있습니다. 또한 azure Id는 Azure CLI, Visual Studio, Visual Studio Code 등을 사용 하 여 Azure 사용자에 게 로그인 한 인증 토큰을 자동으로 검색 합니다. 

Azure Id 클라이언트 li바 항에 대 한 자세한 내용은 다음을 참조 하세요.

### <a name="azure-identity-client-libraries"></a>Azure Id 클라이언트 라이브러리
| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Id SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure Id SDK Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Azure Id SDK Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure Id SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

응용 프로그램에서 Key Vault을 인증 하는 방법에 대 한 자습서는 다음을 참조 하세요.
- [.NET의 VM에서 호스팅된 응용 프로그램의 Key Vault에 대 한 인증](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- [Python의 VM에서 호스트 되는 응용 프로그램의 Key Vault에 대 한 인증](https://docs.microsoft.com/azure/key-vault/general/tutorial-python-virtual-machine)
- [App Service를 사용 하 여 Key Vault에 인증](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)

## <a name="manage-keys-certificates-and-secrets"></a>키, 인증서 및 비밀 관리

키, 암호 및 인증서에 대 한 액세스는 데이터 평면에 의해 제어 됩니다. 데이터 평면 액세스 제어는 로컬 자격 증명 모음 액세스 정책 또는 RBAC (미리 보기)를 사용 하 여 수행할 수 있습니다.

**키 Api 및 Sdk**


| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/key)<br>[빠른 시작](../keys/quick-create-cli.md)|[참조](/powershell/module/az.keyvault/)<br>[빠른 시작](../keys/quick-create-powershell.md)|[참조](/rest/api/keyvault/#key-operations)|N/A|[참조](/dotnet/api/azure.security.keyvault.keys)|[참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[빠른 시작](../keys/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[참조](/javascript/api/@azure/keyvault-keys/)|

**인증서 Api 및 Sdk**


| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/certificate)<br>[빠른 시작](../certificates/quick-create-cli.md)|[참조](/powershell/module/az.keyvault)<br>[빠른 시작](../certificates/quick-create-powershell.md)|[참조](/rest/api/keyvault/#certificate-operations)|N/A|[참조](/dotnet/api/azure.security.keyvault.certificates)|[참조](/python/api/overview/azure/keyvault-certificates-readme)<br>[빠른 시작](../certificates/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[참조](/javascript/api/@azure/keyvault-certificates/)|

**비밀 Api 및 Sdk**


| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/secret)<br>[빠른 시작](../secrets/quick-create-cli.md)|[참조](/powershell/module/az.keyvault/)<br>[빠른 시작](../secrets/quick-create-powershell.md)|[참조](/rest/api/keyvault/#secret-operations)|[참조](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[빠른 시작](../secrets/quick-create-template.md)|[참조](/dotnet/api/azure.security.keyvault.secrets)<br>[빠른 시작](../secrets/quick-create-net.md)|[참조](/python/api/overview/azure/keyvault-secrets-readme)<br>[빠른 시작](../secrets/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[빠른 시작](../secrets/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-secrets/)<br>[빠른 시작](../secrets/quick-create-node.md)|

설치 패키지 및 소스 코드는 [클라이언트 라이브러리](client-libraries.md) 를 참조 하세요.

데이터 평면 보안 Key Vault에 대 한 자세한 내용은 [Key Vault 데이터 평면 및 액세스 정책](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) 및 [Key Vault 데이터 평면 및 RBAC (미리 보기)](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview) 를 참조 하세요.

### <a name="code-examples"></a>코드 예제

애플리케이션에서 Key Vault를 사용하는 전체 예제는 다음을 참조하세요.

- [Azure Key Vault 코드 샘플](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault에 대한 코드 샘플입니다. 

## <a name="how-tos"></a>방법

다음 문서 및 시나리오에서는 Azure Key Vault 사용에 대한 작업별 지침을 제공합니다.

- [방화벽 뒤 Key Vault 액세스](access-behind-firewall.md) - 주요 자격 증명 모음에 액세스하려면 주요 자격 증명 모음 클라이언트 애플리케이션은 다양한 기능에 대한 여러 엔드포인트에 액세스할 수 있어야 합니다.
- Key Vault- [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows), [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) 에서 vm에 인증서를 배포 하는 방법-Azure의 vm에서 실행 되는 클라우드 응용 프로그램에는 인증서가 필요 합니다. 지금 이 VM으로 인증서를 가져오려면 어떻게 하나요?
- [Key Vault를 통해 Azure Web App 인증서 배포](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- 액세스 정책 할당 ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  [포털](assign-access-policy-portal.md))을 선택 합니다. 
- [CLI로 Key Vault 일시 삭제를 사용하는 방법](soft-delete-cli.md)에서는 활성화된 일시 삭제를 사용하는 Key Vault 및 다양한 Key Vault 개체의 사용 및 수명 주기에 대해 설명합니다.
- [배포하는 동안 보안 값(예: 암호)을 전달하는 방법](../../azure-resource-manager/templates/key-vault-parameter.md) - 배포하는 동안 보안 값(예: 암호)을 매개 변수로 전달해야 하는 경우 이 값을 Azure Key Vault에 암호로 저장하고 다른 Resource Manager 템플릿에서 이 값을 참조할 수 있습니다.

## <a name="integrated-with-key-vault"></a>주요 자격 증명 모음과 통합됨

다음 문서에서는 사용하거나 Key Vault와 통합하는 다른 시나리오 및 서비스에 대한 정보를 다룹니다.

- [휴지 상태의 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) 는 데이터가 유지 될 때 데이터의 인코딩 (암호화)을 허용 합니다. 데이터 암호화 키는 액세스를 더욱 제한 하기 위해 Azure Key Vault의 키 암호화 키로 암호화 되는 경우가 많습니다.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key)을 통해 테넌트 키를 직접 관리할 수 있습니다. 예를 들어, Microsoft가 테넌트 키를 관리하는 대신(기본값) 테넌트 키를 직접 관리하여 해당 조직에 적용되는 특정 규정을 준수할 수 있습니다. 테넌트 키 직접 관리는 BYOK(Bring Your Own Key)라고 하기도 합니다.
- [Azure 개인 링크 서비스](private-link-service.md) 를 사용 하면 가상 네트워크의 개인 끝점을 통해 azure 서비스 (예: Azure Key Vault, Azure Storage 및 Azure Cosmos DB)와 azure에서 호스트 되는 고객/파트너 서비스에 액세스할 수 있습니다.
- [Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault) 와 통합 Key Vault 키 자격 증명 모음에 저장 된 암호의 상태가 변경 되 면 사용자에 게 알릴 수 있습니다. 새 버전의 암호를 응용 프로그램에 배포 하거나 거의 만료 비밀을 회전 하 여 중단을 방지할 수 있습니다.
- Key Vault에서 원치 않는 액세스 로부터 [Azure Devops](https://docs.microsoft.com/azure/devops/pipelines/release/azure-key-vault) 비밀을 보호할 수 있습니다.
- [DataBricks의 Key Vault에 저장 된 암호를 사용 하 여 Azure Storage에 연결](https://docs.microsoft.com/azure/key-vault/general/integrate-databricks-blob-storage)
- Kubernetes에서 [비밀 저장소 CSI 드라이버](https://docs.microsoft.com/azure/key-vault/general/key-vault-integrate-kubernetes) 에 대 한 Azure Key Vault 공급자를 구성 하 고 실행 합니다.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 개요 및 개념

- [Key Vault 일시 삭제 동작](soft-delete-overview.md)에서는 삭제가 실수인지, 의도적인 것인지에 관계없이 삭제된 개체를 복구할 수 있는 기능에 대해 설명합니다.
- [Key Vault 클라이언트 제한](overview-throttling.md)에서는 제한의 기본 개념을 소개하고 앱에 맞는 접근 방식을 제공합니다.
- [Key Vault 보안 권역](overview-security-worlds.md)에서는 지역과 보안 영역 간의 관계를 설명합니다.

## <a name="social"></a>소셜

- [키 자격 증명 모음 블로그](https://aka.ms/kvblog)
- [키 자격 증명 모음 포럼](https://aka.ms/kvforum)
