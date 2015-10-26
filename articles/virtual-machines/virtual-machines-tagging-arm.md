<properties
   pageTitle="VM에 태그를 지정하는 방법 | Microsoft Azure"
   description="리소스 관리자 배포 모델을 사용하여 만든 Azure 가상 컴퓨터에 태그를 지정하는 방법을 알아봅니다."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/23/2015"
   ms.author="dkshir;memccror"/>

# Azure에서 가상 컴퓨터에 태그를 지정하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


이 문서에서는 Azure 리소스 관리자를 통해 Azure의 가상 컴퓨터에 태그를 지정하는 다양한 방법에 대해 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 15개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. 태그는 Azure 리소스 관리자를 통해 생성된 리소스에 대해서만 지원됩니다.

## 템플릿을 통해 가상 컴퓨터에 태그 지정

먼저 템플릿을 통한 태그 지정을 살펴보겠습니다. [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm)은 계산(가상 컴퓨터), 저장소(저장소 계정) 및 네트워크(공용 IP 주소, 가상 네트워크 및 네트워크 인터페이스) 리소스에 태그를 배치합니다.

[템플릿 링크](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm)에서 **Azure에 배포** 단추를 클릭합니다. 이렇게 하면 이 템플릿을 배포할 수 있는 [Azure Preview 포털](http://portal.azure.com/)로 이동합니다.

![태그가 포함된 간단한 배포](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

이 템플릿에는 *부서*, *응용 프로그램* 및 *만든 사람* 태그가 포함되어 있습니다. 다른 태그 이름을 사용하려는 경우 템플릿에서 직접 이러한 태그를 추가/편집할 수 있습니다.

![템플릿의 Azure 태그](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

여기서 볼 수 있듯이 태그는 콜론(:)으로 구분된 키/값 쌍으로 정의됩니다. 다음 형식으로 태그를 정의해야 합니다.

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

편집을 마친 후 선택한 태그와 함께 템플릿 파일을 저장합니다.

이어서 **매개 변수 편집** 섹션에서 태그의 값을 입력할 수 있습니다.

![Azure 포털에서 태그 편집](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

**만들기**를 클릭하여 태그 값과 함께 이 템플릿을 배포합니다.


## 포털을 통해 태그 지정

태그와 함께 리소스를 만든 후 포털에서 태그를 표시, 추가 및 삭제할 수 있습니다.

태그 아이콘을 선택하여 태그를 표시합니다.

![Azure 포털의 태그 아이콘](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

사용자 고유의 키/값 쌍을 정의하여 포털을 통해 새 태그를 추가하고 저장합니다.

![Azure 포털에서 새 태그 추가](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

이제 리소스에 대한 태그 목록에 새 태그가 나타납니다.

![Azure 포털에서 저장된 새 태그](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## PowerShell을 사용한 태그 지정

PowerShell을 통해 태그를 만들고 추가 및 삭제하려면 먼저 [Azure 리소스 관리자를 사용하여 PowerShell 환경][]을 설정해야 합니다. 설정을 완료한 후 계산, 네트워크 및 저장소 리소스를 만들 때 또는 PowerShell을 통해 리소스를 만든 후에 태그를 배치할 수 있습니다. 이 문서에서는 가상 컴퓨터에 배치된 태그 보기/편집을 중점적으로 살펴봅니다.

먼저 `Get-AzureVM` cmdlet을 통해 가상 컴퓨터로 이동합니다.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

가상 컴퓨터에 이미 태그가 포함되어 있는 경우 해당 리소스의 모든 태그가 표시됩니다.

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

PowerShell을 통해 태그를 추가하려는 경우 `Set-AzureResource` 명령을 사용할 수 있습니다. PowerShell을 통해 태그를 업데이트하는 경우 태그가 전체적으로 업데이트됩니다. 따라서 이미 태그가 있는 리소스에 하나의 태그를 추가하는 경우 리소스에 배치하려는 모든 태그를 포함해야 합니다. 다음은 PowerShell Cmdlet을 통해 리소스에 태그를 더 추가하는 방법의 예입니다.

첫 번째 cmdlet은 `Get-AzureResource` 및 `Tags` 함수를 사용하여 *MyWindowsVM*에 배치된 모든 태그를 *tags* 변수에 설정합니다.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

두 번째 명령은 지정된 변수에 대한 태그를 표시합니다.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

세 번째 명령은 *tags* 변수에 태그를 더 추가합니다. **+=**을 사용하여 *tags* 목록에 새로운 키/값 쌍을 추가합니다.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

네 번째 명령은 *tags* 변수에 정의된 모든 태그를 지정된 리소스에 설정합니다. 이 경우 MyWindowsVM입니다.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

다섯 번째 명령은 리소스의 모든 태그를 표시합니다. 여기서 볼 수 있듯이 *위치*가 이제 값이 *MyLocation*인 태그로 정의되었습니다.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

PowerShell을 통해 태그를 지정하는 방법에 대한 자세한 내용은 [Azure 리소스 Cmdlet][]을 참조하세요.


## Azure CLI를 사용하여 태그 지정

Azure CLI를 통해 이미 생성된 리소스에 대한 태그 지정도 지원됩니다. 먼저 [Azure CLI 환경][]을 설정합니다. Azure CLI를 통해 구독에 로그인하고 ARM 모드로 전환합니다. 다음 명령을 사용하여 태그를 비롯한 지정된 가상 컴퓨터의 모든 속성을 볼 수 있습니다.

        azure vm show -g MyResourceGroup -n MyVM

PowerShell과 달리 이미 태그를 포함하는 리소스에 태그를 추가하는 경우 `azure vm set` 명령을 사용하기 모든 태그(이전 및 새 태그)를 지정할 필요가 없습니다. 대신,이 명령을 사용하여 리소스에 태그를 추가할 수 있습니다. Azure CLI를 통해 새 VM 태그를 추가하려면 태그 매개 변수 **-t**와 함께 `azure vm set` 명령을 사용할 수 있습니다.

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

모든 태그를 제거하려면 `azure vm set` 명령에 **–T** 매개 변수를 사용할 수 있습니다.

        azure vm set – g MyResourceGroup –n MyVM -T


PowerShell, Azure CLI 및 포털을 통해 리소스에 태그를 적용했으므로 이제 사용량 세부 정보를 확인하여 청구 포털에서 태그를 살펴보겠습니다.


## 사용량 세부 정보에서 태그 보기

Azure 리소스 관리자를 통해 계산, 네트워크 및 저장소 리소스에 배치된 태그는 청구 포털의 사용량 세부 정보에 채워집니다.

**사용량 세부 정보 다운로드**를 클릭하여 구독의 사용량 세부 정보를 표시합니다.

![Azure 포털의 사용량 세부 정보](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

청구서 및 **버전 2** 사용량 세부 정보를 선택합니다.

![Azure 포털의 버전 2 미리 보기 사용량 세부 정보](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

사용량 세부 정보의 **태그** 열에서 모든 태그를 확인할 수 있습니다.

![Azure 포털의 태그 열](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

조직에서는 사용량과 함께 이러한 태그를 분석하여 소비 데이터에 대한 새로운 통찰력을 얻을 수 있습니다.


## 추가 리소스

* [Azure 리소스 관리자 개요][]
* [태그를 사용하여 Azure 리소스 구성][]
* [Azure 청구서 이해][]
* [Microsoft Azure 리소스 소비에 대한 통찰력 얻기][]




[Azure 리소스 관리자를 사용하여 PowerShell 환경]: ../powershell-azure-resource-manager.md
[Azure 리소스 Cmdlet]: https://msdn.microsoft.com/ko-KR/library/azure/dn757692.aspx
[Azure CLI 환경]: ./xplat-cli-azure-resource-manager.md
[Azure 리소스 관리자 개요]: ../resource-group-overview.md
[태그를 사용하여 Azure 리소스 구성]: ../resource-group-using-tags.md
[Azure 청구서 이해]: ../billing-understand-your-bill.md
[Microsoft Azure 리소스 소비에 대한 통찰력 얻기]: ../billing-usage-rate-card-overview.md

<!---HONumber=Oct15_HO3-->