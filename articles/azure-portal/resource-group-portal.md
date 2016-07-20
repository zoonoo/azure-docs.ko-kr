<properties 
	pageTitle="Azure 포털을 사용하여 Azure 리소스 관리 | Microsoft Azure" 
	description="Azure 포털 및 Azure 리소스 관리자를 사용하여 리소스를 관리합니다. 리소스를 모니터링하는 대시보드와 타일을 사용하는 방법을 보여줍니다." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2016" 
	ms.author="tomfitz"/>


# 포털을 통해 Azure 리소스 관리

> [AZURE.SELECTOR]
- [포털](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [노드](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

이 항목에서는 [Azure Resource Manager](../resource-group-overview.md)를 포함한 [Azure 포털](https://portal.azure.com)을 사용하여 Azure 리소스를 관리하는 방법을 보여줍니다. 포털을 통해 리소스를 배포하는 방법을 알아보려면 [Resource Manager 템플릿 및 Azure 포털을 사용하여 리소스 배포](../resource-group-template-deploy-portal.md)를 참조하세요.

현재 일부 서비스에서만 포털이나 리소스 관리자를 지원합니다. 이러한 서비스의 경우 [클래식 포털](https://manage.windowsazure.com)을 사용해야 합니다. 각 서비스의 상태는 [Azure 포털 가용성 차트](https://azure.microsoft.com/features/azure-portal/availability/)를 참조하세요.

<a id="access-control-for-azure-dashboards" />
## 대시보드를 사용자 지정하여 리소스 모니터링

포털은 리소스 모니터링과 관리에 사용할 수 있는 대시보드를 제공합니다. 대시보드는 완전히 사용자 지정이 가능하며, 여러 개의 대시보드를 만들어서 다양한 구독을 손쉽게 볼 수 있습니다.

![dashboard](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] 대시보드를 사용하는 방법을 알아보려면 [Microsoft Azure 포털에서 사용자 지정 대시보드 빌드](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards) 비디오를 시청하는 것이 가장 좋습니다.

### Azure 대시보드 및 액세스 제어 공유
대시보드를 구성한 후에는 이를 게시하고 조직 내의 다른 사용자와 공유할 수 있습니다. Azure [역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)는 포털에서 타일이 표시하는 정보에 대한 액세스를 관리합니다. 모든 게시된 대시보드는 Azure 리소스로 구현됩니다. 액세스 제어 관점에서 대시보드는 가상 컴퓨터 또는 저장소 계정과 차이가 없습니다.

다음은 예제입니다. Azure 구독을 보유하고 구독의 **소유자**, **참여자** 또는 **읽기 권한자** 역할에 할당된 팀의 다양한 멤버가 있다고 가정해 보겠습니다. 소유자 또는 참여자인 사용자는 구독 내에서 대시보드를 나열, 보기, 만들기, 수정 또는 삭제할 수 있습니다. 읽기 권한자인 사용자는 대시보드를 나열 및 볼 수 있지만 수정 또는 삭제할 수 없습니다. 읽기 권한자 액세스를 보유한 사용자는 게시된 대시보드에 대한 로컬 편집을 만들 수 있지만(예: 문제 해결 시) 해당 변경 내용을 서버로 다시 게시할 수 없습니다. 대시보드의 개인 복사본을 직접 만들 수 있는 옵션을 갖게 됩니다.

대시보드의 개별 타일은 표시하는 리소스에 따라 고유한 액세스 제어 요구 사항을 적용합니다. 그러므로 개별 타일에 있는 데이터를 보호하면서 광범위하게 공유할 수 있는 대시보드를 디자인할 수 있습니다.

## 리소스 그룹 관리

1. 구독에서 모든 리소스 그룹을 보려면 **리소스 그룹**을 참조하세요.

    ![리소스 그룹 찾아보기](./media/resource-group-portal/browse-groups.png)

2. 관리하려면 특정 리소스 그룹을 선택합니다. 그룹의 모든 리소스 목록을 포함하여 해당 리소스 그룹에 대한 정보를 제공하는 리소스 그룹 블레이드가 표시됩니다.

    ![리소스 그룹 요약](./media/resource-group-portal/group-summary.png)

    리소스를 선택하면 리소스에 대한 상세 정보가 보입니다.

3. 리소스 블레이드에서 요약 아래에 **섹션 추가**를 선택하면 더 많은 그래프와 표를 추가할 수 있습니다.

    ![섹션 추가](./media/resource-group-portal/add-section.png)

4. 블레이드에 포함하려는 정보를 선택하기 위한 타일 갤러리가 나타납니다. 표시되는 타일 유형은 리소스 유형으로 필터링됩니다. 선택하는 리소스가 달라지면 사용할 수 있는 타일이 변경됩니다.

    ![섹션 추가](./media/resource-group-portal/tile-gallery.png)

5. 필요한 타일을 사용 가능한 공간으로 끕니다.

    ![타일 끌기](./media/resource-group-portal/drag-tile.png)

6. 포털 맨 위에서 **완료**를 선택하면 새 보기가 블레이드에 포함됩니다.

    ![타일 표시](./media/resource-group-portal/show-lens.png)

7. 리소스 그룹에 대한 빠른 액세스의 경우 대시보드에 블레이드를 고정할 수 있습니다.

    ![리소스 그룹 고정](./media/resource-group-portal/pin-group.png)

    또는 섹션 위에 줄임표(...)를 선택하여 대시보드에 블레이드의 한 섹션을 고정할 수 있습니다. 블레이드의 섹션 크기를 사용자 지정하거나 완전히 제거할 수도 있습니다. 다음 이미지는 CPU 및 메모리 섹션을 고정, 사용자 지정 또는 제거하는 방법을 보여 줍니다.

    ![선택 고정](./media/resource-group-portal/pin-cpu-section.png)

8. 대시보드에 섹션을 고정하면 대시보드에 요약이 표시됩니다.

    ![대시보드 보기](./media/resource-group-portal/view-startboard.png)

그리고 즉시 선택하면 데이터에 대한 세부 정보로 이동합니다.

## 리소스 태그 지정

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 포털을 통한 태그 사용에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하세요.

## 구독 및 비용 보기

모든 리소스에 대한 롤업 비용 및 구독에 대한 정보를 볼 수 있습니다. **구독** 및 표시할 구독을 선택합니다. 선택할 구독이 하나만 있을 수 있습니다.

![(구독당)](./media/resource-group-portal/select-subscription.png)

구독 블레이드 내에 진행 속도가 표시됩니다.

![진행 속도](./media/resource-group-portal/burn-rate.png)

그리고 리소스 유형별 비용 분석이 표시됩니다.

![리소스 비용](./media/resource-group-portal/cost-by-resource.png)

## 템플릿 내보내기

리소스 그룹을 설정한 후에는 리소스 그룹에 대한 Resource Manager 템플릿을 살펴볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

1. 모든 인프라가 템플릿에 정의되어 있기 때문에 향후 솔루션 배포를 간단하게 자동화할 수 있습니다.

2. 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해질 수 있습니다.

단계별 지침은 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](../resource-manager-export-template.md)를 참조하십시오.

## 리소스 그룹 또는 리소스 삭제

리소스 그룹을 삭제하면 그 안에 포함된 모든 리소스가 삭제됩니다. 리소스 그룹 내부의 개별 리소스를 삭제할 수도 있습니다. 삭제하려는 리소스 그룹에 다른 리소스 그룹의 리소스가 연결되었을 수 있으므로 리소스 그룹을 삭제할 때는 주의해야 합니다. 연결된 리소스는 삭제될 수는 없지만 예상대로 작동하지 않을 수 있습니다.

![그룹 삭제](./media/resource-group-portal/delete-group.png)


## 다음 단계

- 감사 로그를 보려면 [Resource Manager로 작업 감사](../resource-group-audit.md)를 참조하세요.
- 배포 오류를 해결하려면 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.
- 포털을 통해 리소스를 배포하려면 [Resource Manager 템플릿 및 Azure 포털을 사용하여 리소스 배포](../resource-group-template-deploy-portal.md)를 참조하세요.

<!---HONumber=AcomDC_0706_2016-->