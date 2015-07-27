<properties
   pageTitle="Azure 리소스 관리자 개요"
   description="Azure에서 리소스 배포, 관리 및 액세스 제어용 Azure 리소스 관리자 사용 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 개요

응용 프로그램은 일반적으로 웹 앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. 이러한 구성 요소를 별도 엔터티로 표시하지 않으면, 대신 관련된 단일 엔터티의 상호 종속적으로 부분으로 표시됩니다. 그룹으로 배포, 관리 및 모니터링하려고 합니다. Azure 리소스 관리자를 사용하면 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션와 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다.

Azure 리소스 관리자는 관리 플랫폼으로 액세스 제어를 고유하게 통합하므로 조직에서 사용자는 리소스 그룹에 대해 어떤 작업을 할지 지정할 수 있습니다.

> [AZURE.NOTE]이 항목에서는 개념을 설명하기 위해 미리 보기 포털을 사용하여 리소스, 그룹 및 템플릿을 설명합니다. 하지만 [PowerShell](powershell-azure-resource-manager.md)을 비롯하여 [Mac, Linux 및 Windows용 Azure CLI](virtual-machines/xplat-cli-azure-resource-manager.md)를 사용하여 Azure 리소스를 생성하고, 관리하고, 삭제하는 방법에 대해 설명합니다.

## 리소스 그룹

리소스 그룹은 응용 프로그램에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹은 모든 응용 프로그램에 대한 리소스 또는 논리적으로 그룹화된 리소스만을 포함할 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 어떻게 리소스 그룹에 리소스를 할당할지 결정할 수 있습니다.

리소스 그룹을 정의할 때 고려해야할 몇 가지 중요한 요인이 있습니다.

1. 그룹에서 모든 리소스는 동일한 수명 주기를 공유해야 합니다. 리소스를 함께 배포, 업데이트, 삭제합니다. 데이터베이스 서버와 같은 하나의 리소스에 다양한 배포 주기가 존재하는 경우 다른 리소스 그룹에 있어야 합니다.
2. 각 리소스는 하나의 리소스 그룹에만 있을 수 있습니다.
3. 언제든지 리소스 그룹에 리소스를 추가하거나 제거할 수 있습니다.
4. 리소스 그룹을 다른 지역에 상주하는 리소스를 포함할 수 있습니다.
5. 관리 작업에 대한 액세스 제어 범위를 지정하는 데 리소스 그룹을 사용할 수 있습니다.

Azure Preview 포털에서 모든 새 리소스는 리소스 그룹에 만들어집니다. 방금 웹 사이트와 같은 단일 리소스를 만드는 경우라도 기존 그룹에 해당 리소스를 추가하거나 해당 리소스에 대한 새 그룹을 만들 것인지 결정해야 합니다.

다음 이미지는 웹 사이트, 데이터베이스 및 Application Insights과 함께 리소스 그룹을 보여줍니다.

![리소스 그룹 요약](./media/resource-group-overview/resourcegroupsummary.png)

리소스 그룹은 다른 리소스 그룹의 리소스에도 연결될 수 있습니다. 리소스는 다른 리소스 그룹의 리소스 간에 배포 종속성이 존재할 때 연결된 것으로 간주됩니다. 예를 들어 한 리소스 그룹의 웹 앱이 다른 리소스 그룹의 데이터베이스와 연결하는 경우 해당 리소스 연결된 것입니다.

![링크된 리소스](./media/resource-group-overview/linkedresource.png)

미리 보기 포털에서 비용을 쉽게 보고 이벤트를 모니터링하며 경고를 관리합니다. 다음 이미지는 그룹에 대한 통합된 청구를 보여줍니다.

![청구](./media/resource-group-overview/billing.png)

## 템플릿 배포

Azure 리소스 관리자로 응용 프로그램의 배포 및 구성을 정의하는 간단한 템플릿을(JSON 형식으로) 만들 수 있습니다. Azure 템플릿으로 알려진 이 템플릿은 배포를 정의하는 선언적 방법을 제공합니다. 서식 파일을 사용하여 수명 주기 내내 응용 프로그램을 반복적으로 배포하며 안심하고 일관된 상태로 리소스가 배포할 수 있습니다.

템플릿 내에서 앱에 대한 인프라, 해당 인프라를 구성하는 방법 및 해당 인프라에 앱 코드를 게시하는 방법을 정의합니다. 리소스가 올바른 순서로 생성되도록 Azure 리소스 관리자가 종속성을 분석하기 때문에 배포 순서를 걱정할 필요가 없습니다.

또한 인프라의 업데이트에 대한 템플릿을 사용할 수 있습니다. 예를 들어 앱에 새 리소스를 추가할 수 있으며 이미 배포된 리소스에 대한 구성 규칙을 추가할 수 있습니다. 템플릿에서 새 리소스 만들기를 지정하지만 해당 리소스가 이미 존재하는 경우 Azure 리소스 관리자는 새 자산을 만드는 대신 업데이트를 수행합니다. Azure 리소스 관리자는 새 것과 동일한 상태로 기존 자산을 업데이트합니다.

사용자 지정 및 배포 유연성을 허용하도록 템플릿에서 매개 변수를 지정할 수 있습니다. 예를 들어 테스트 환경에 배포를 조정하는 매개 변수 값을 전달할 수 있습니다. 매개 변수를 지정하여 모든 앱의 환경에 배포할 때 같은 템플릿을 사용할 수 있습니다.

템플릿을 통해 배포 및 구성에 대한 모든 필수 단계를 수행할 수 있고 남은 수동 단계가 없어야 합니다. Azure 리소스 관리자는 설치에 포함되지 않은 특정 소프트웨어를 설치하는 등의 추가 작업을 할 때 시나리오에 대한 확장을 제공합니다. DSC, Chef 또는 Puppet와 같은 구성 관리 서비스를 이미 사용 중인 경우 확장을 사용하여 해당 서비스로 작업을 계속할 수 있습니다.

Marketplace에서 솔루션을 만들 때 솔루션에 자동으로 배포 템플릿을 포함합니다. 솔루션용 템플릿으로 시작하고 특정 요구 사항에 맞게 사용자 지정할 수 있기 때문에 서식 파일을 처음부터 새로 만들 필요가 없습니다.

마지막으로 템플릿은 앱에 대한 소스 코드의 일부가 됩니다. 소스 코드 리포지토리를 확인하고 앱이 발전하면 업데이트할 수 있습니다. Visual Studio를 통해 템플릿을 편집할 수 있습니다.

템플릿을 정의하는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](./resource-group-authoring-templates.md)를 참조하십시오.

템플릿 스키마에 대해서는 [Azure 리소스 관리자 스키마](https://github.com/Azure/azure-resource-manager-schemas)를 참조하세요.

배포용 템플릿을 사용하는 방법에 대한 정보는 [Azure 리소스 관리자 템플릿으로 응용 프로그램 배포](azure-portal/resource-group-template-deploy.md) 및 [Azure에서 예측 가능한 방식으로 복잡한 응용 프로그램 배포](app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하십시오.

## 태그

Azure 리소스 관리자는 관리 또는 청구에 대한 요구 사항에 따라 리소스를 분류할 수 있는 태그 지정 기능을 제공합니다. 리소스 그룹 및 리소스의 복잡한 컬렉션이 있고 이러한 자산에 가장 적합한 방식으로 시각화할 필요가 있을 때 태그를 사용할 수 있습니다. 예를 들어 조직에서 비슷한 역할을 제공하거나 동일한 부서에 속한 리소스를 태그로 지정할 수 있습니다.

미리 보기 포털에서 태그 아이콘을 클릭하여 태그를 사용하기 시작할 수 있습니다.

![tags](./media/resource-group-overview/tags.png)

리소스는 태그를 공유하는 동일한 리소스 그룹에 있을 필요가 없습니다. 조직의 모든 사용자가 실수로 약간 다른 태그 (예: "dept" 대신 "department")를 적용하지 않고 일반 태그를 사용하는지 확인하려면 사용자 고유의 태그 분류를 만들 수 있습니다.

태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](./resource-group-using-tags.md)을 참조하십시오.

## 액세스 제어

Azure 리소스 관리자를 사용하면 조직에 대한 특정 작업에 액세스하는 사람을 제어할 수 있습니다. 고유하게 관리 플랫폼으로 OAuth 및 역할 기반 액세스 제어(RBAC)를 통합하고 해당 액세스 제어를 리소스 그룹에서 모든 서비스에 적용합니다. 사용자가 미리 정의된 플랫폼 및 리소스 특정 역할을 추가하고 액세스를 제한한 구독, 리소스 그룹 또는 리소스에 해당 역할을 적용할 수 있습니다. 예를 들어 데이터베이스 서버 또는 보안 정책이 아닌 데이터베이스를 관리하는 사용자를 허용하는 SQL DB 참여자를 호출하는 미리 정의된 역할의 기능을 활용할 수 있습니다. 이 유형의 SQL DB 기여자 역할에 액세스를 필요로 하는 조직에서 사용자를 추가하고 구독, 리소스 그룹 또는 리소스에 역할을 적용합니다.

미리 보기 포털에서 액세스 단추를 클릭하여 액세스 제어를 정의할 수 있습니다.

![사용자 액세스 제어](./media/resource-group-overview/access.png)

Azure 리소스 관리자는 자동으로 감사에 대한 사용자 작업을 기록합니다.

또한 사용자가 삭제 및 수정하는 것을 방지하기 위해 명시적으로 중요한 리소스를 잠글 수 있습니다.

역할 기반 액세스 제어에 대한 자세한 내용은 [Microsoft Azure Preview 포털에서 역할 기반 액세스 제어](./role-based-access-control-configure.md)를 참조하십시오.

액세스 정책을 설정하는 예제를 보려면 [리소스에 대한 관리 및 감사 액세스](azure-portal/resource-group-rbac.md)를 참조하십시오.

## 일관적인 관리 계층

Azure 리소스 관리자는 Mac, Linux 및 Windows, Azure 포털 또는 REST API용 Azure PowerShell, Azure CLI를 통해 완전히 호환되는 작업을 제공합니다. 가장 잘 작동하는 인터페이스를 사용하여 혼동 없이 인터페이스 간을 빠르게 이동할 수 있습니다. 포털은 포털 외부에서 수행된 작업에 대한 알림을 표시합니다.

Azure PowerShell에 대한 정보는 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](./powershell-azure-resource-manager.md) 및 nd [Azure 리소스 관리자 Cmdlets](https://msdn.microsoft.com/library/azure/dn757692.aspx)을 참조하십시오.

Azure CLI에 대한 정보는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](./virtual-machines/xplat-cli-azure-resource-manager.md)을 참조하십시오.

REST API에 대한 정보는 [Azure 리소스 관리자 REST API 참조](https://msdn.microsoft.com/library/azure/dn790568.aspx)를 참조하십시오..

## 다음 단계
시작

- [리소스 관리자로 Azure PowerShell 사용](./powershell-azure-resource-manager.md)
- [리소스 관리에서 Azure CLI 사용](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Azure 포털을 사용하여 리소스 관리](azure-portal/resource-group-portal.md)

응용 프로그램 만들기 및 배포

- [템플릿 작성](./resource-group-authoring-templates.md)
- [템플릿 배포](azure-portal/resource-group-template-deploy.md)
- [배포 문제해결](virtual-machines/resource-group-deploy-debug.md)
- [Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-web/app-service-deploy-complex-application-predictably.md)
- [.NET 라이브러리 및 템플릿을 사용하여 배포](virtual-machines/arm-template-deployment.md)
- [템플릿 함수](./resource-group-template-functions.md)
- [고급 템플릿 작업](./resource-group-advanced-template.md)
- [템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas)

리소스 구성

- [태그를 사용하여 리소스 구성](./resource-group-using-tags.md)

액세스 관리 및 감사

- [리소스에 대한 액세스 관리 및 감사](azure-portal/resource-group-rbac.md)
- [미리 보기 포털의 역할 기반 액세스 제어](./role-based-access-control-configure.md)
- [서비스 사용자 인증](./resource-group-authenticate-service-principal.md)
- [Azure 포털을 사용하여 새 서비스 사용자 만들기](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO3-->