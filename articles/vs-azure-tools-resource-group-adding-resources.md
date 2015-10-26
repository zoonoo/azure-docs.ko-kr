<properties 
   pageTitle="Azure 리소스 그룹에 리소스 추가"
   description="Visual Studio를 사용하여 Azure 리소스 그룹에 리소스를 추가하는 방법에 대해 알아봅니다."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Azure 리소스 그룹에 리소스 추가

리소스 그룹에 리소스를 더 추가 해야하는 경우, Visual Studio의 JSON 개요 창에서 이를 수행할 수 있습니다.

## 리소스 그룹에 리소스 추가

### 리소스 그룹에 리소스를 추가하려면

1. 솔루션 탐색기에서 Azure 리소스 그룹에 대한 JSON 파일을 선택합니다. JSON 파일이 편집기에 표시되며 **JSON 개요** 창도 편집기 옆에 나타납니다. JSON 개요 창을 닫으면 JSON 템플릿 파일(매개 변수 파일이 아닌)의 상황에 맞는 메뉴에서 개요 표시 명령을 선택할 때까지 다시 자동으로 열리지 않습니다.

    ![Azure 리소스 그룹에 대한 JSON 파일](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. **JSON 개요** 창에서 **리소스** 노드를 선택한 다음 상황에 맞는 메뉴에서 **새 리소스 추가** 명령을 선택하거나 JSON 개요 창의 위쪽에서 **리소스 추가** 단추를 선택합니다.

    ![새 리소스를 리소스 그룹에 추가](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. **리소스 추가** 대화 상자의 리소스 목록에서 추가할 리소스를 선택하고 리소스에서 필요한 정보를 제공한 다음 **추가** 단추를 선택합니다. 예를 들어, 저장소 계정을 추가하면 사용자에 대해 생성될 매개 변수의 기본 이름을 제공해야 합니다.
 
    ![리소스 추가 대화 상자](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    리소스가 **JSON 개요** 창의 리소스 목록에 추가되며 리소스를 표시하는 새 JSON이 파일에 나타납니다. 관련된 매개 변수 및/또는 변수가 추가될 수도 있습니다.


    ![JSON 파일에 추가된 리소스](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Azure 리소스 그룹에 새 리소스를 배포합니다. 솔루션 탐색기에서 리소스 그룹 프로젝트의 상황에 맞는 메뉴에서 **배포**를 선택한 다음 리소스 그룹의 이름을 선택합니다.

    ![배포된 Azure 리소스 그룹](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    **리소스 그룹에 배포** 대화 상자가 나타납니다.


1. **배포** 단추를 선택합니다.

1. 사용자가 매개 변수를 지정할 필요가 없는 경우, **매개 변수 편집** 대화 상자가 나타납니다. 필요한 모든 값을 입력한 다음 **저장** 단추를 선택합니다. 새 리소스는 Azure 리소스 그룹에 배포됩니다.

## 참고 항목

[Azure 리소스 그룹 배포 프로젝트 만들기 및 배포](http://go.microsoft.com/fwlink/p/?LinkID=623073)

[Azure 리소스 관리자 Cmdlet](https://msdn.microsoft.com/library/dn654592.aspx)

[Azure 리소스 관리자와 Windows PowerShell 사용](../powershell-azure-resource-manager/)

[Channel9 비디오: Azure 리소스 관리자](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=Oct15_HO3-->