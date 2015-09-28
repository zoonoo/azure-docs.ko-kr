<properties
   pageTitle="Visual Studio를 사용하여 Azure 클라우드 서비스 프로젝트의 역할 관리 | Microsoft Azure"
   description="Azure 클라우드 서비스 프로젝트에 새 역할을 추가 하거나 Visual Studio를 사용하여 기존 역할에서 제거 하는 방법에 알아봅니다."
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
   ms.date="09/08/2015"
   ms.author="kempb" />

# Visual Studio를 사용하여 Azure 클라우드 서비스 프로젝트에서 역할 관리

Azure 클라우드 서비스 프로젝트를 만든 후 새 역할을 추가하거나 기존 역할에서 제거할 수 있습니다. 또한 기존 프로젝트를 가져오고 역할로 변환할 수 있습니다. 예를 들어, ASP.NET 웹 응용 프로그램을 가져오고 웹 역할로 지정할 수 있습니다.

## 역할 추가 또는 제거

**역할을 추가 하려면**

**솔루션 탐색기**에서 클라우드 서비스 프로젝트의 **역할** 노드에 대한 바로 가기 메뉴를 열고 **추가**를 선택합니다. 현재 솔루션에서 기존 웹 역할 또는 작업자 역할을 선택하거나 새 웹 또는 작업자 역할 프로젝트를 만들 수 있습니다. 또는 ASP.NET 웹 응용 프로그램 프로젝트와 같은 적절한 프로젝트를 선택 하고 역할 프로젝트에 연결할 수 있습니다.

**역할 연결을 제거하려면**

솔루션 탐색기의 클라우드 서비스 프로젝트 **역할** 노드에서 역할에 대한 바로 가기 메뉴를 열고 **추가**를 선택합니다.

## 클라우드 서비스에서 역할을 추가 및 제거

클라우드 서비스 프로젝트에서 역할을 제거 하지만 나중에 프로젝트에 역할을 추가 하려면 기본 끝점 및 진단 정보 등의역할 선언과 기본 특성이 추가 됩니다. 어떠한 참조 또는 레퍼런스도 ServiceDefinition.csdef 파일 또는 ServiceConfiguration.cscfg 파일에 추가 되지 않습니다. 이 정보를 추가하려면 이러한 파일에 수동으로 다시 추가해야 합니다.

예를 들어, 웹 서비스 역할을 제거하고 이 솔루션에 다시 이 역할을 추가하도록 나중에 결정할 수 있습니다. 이 작업을 수행 하는 경우 오류가 발생합니다. 이 오류를 방지하려면 다음 XML에 표시된 `<LocalResources>` 요소를 ServiceDefinition.csdef 파일에 다시 추가해야 합니다. 프로젝트에 다시 추가한 웹 서비스 역할의 이름을 **<LocalStorage>** 요소에 대한 이름 특성의 일부로 사용합니다. 이 예제에서 웹 서비스 역할의 이름은 **WCFServiceWebRole1**입니다.

	<WebRole name="WCFServiceWebRole1">
	    <Sites>
	      <Site name="Web">
	        <Bindings>
	          <Binding name="Endpoint1" endpointName="Endpoint1" />
	        </Bindings>
	      </Site>
	    </Sites>
	    <Endpoints>
	      <InputEndpoint name="Endpoint1" protocol="http" port="80" />
	    </Endpoints>
	    <Imports>
	      <Import moduleName="Diagnostics" />
	    </Imports>
	   <LocalResources>
	      <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
	   </LocalResources>
	</WebRole>

## 다음 단계

[Visual Studio를 사용하여 Azure 클라우드 서비스에 대한 역할 구성](vs-azure-tools-configure-roles-for-cloud-service.md)을 읽고 Visual Studio에서 역할을 구성하는 방법을 알아봅니다.

<!---HONumber=Sept15_HO3-->