<properties 
   pageTitle="서비스 버스 릴레이된 메시징 자습서 | Microsoft Azure"
   description="서비스 버스 릴레이 메시징을 사용하여 서비스 버스 클라이언트 응용 프로그램과 서비스를 빌드합니다."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/11/2015"
   ms.author="sethm" />

# 서비스 버스 릴레이 메시징 자습서

이 자습서에서는 서비스 버스 "릴레이" 기능을 사용하여 서비스 버스 클라이언트 응용 프로그램 및 서비스를 빌드하는 방법을 설명합니다. 서비스 버스 “조정된" 또는 비동기 메시징 기능을 사용하는 응용 프로그램 빌드 방법에 대해 설명하는 해당 자습서는 [서비스 버스 조정된 메시징 .NET 자습서](https://msdn.microsoft.com/library/hh367512.aspx)를 참조하세요. 서비스 버스 [조정된 메시징](service-bus-messaging-overview.md/#Brokered-messaging)을 사용하는 유사한 자습서는 [서비스 버스 조정된 메시징 .NET 자습서](https://msdn.microsoft.com/library/hh367512.aspx)를 참조하세요.

이 자습서를 통해 서비스 버스 클라이언트 및 서비스 응용 프로그램을 만드는 데 필요한 단계를 이해할 수 있습니다. WCF 대응처럼 서비스는 하나 이상의 끝점을 노출하는 구문으로, 각각 하나 이상의 서비스 작업을 노출합니다. 서비스 끝점은 서비스를 찾을 수 있는 주소, 클라이언트가 서비스와 통신해야 하는 정보가 포함된 바인딩, 서비스가 클라이언트에 제공하는 기능을 정의하는 계약을 지정합니다. WCF와 서비스 버스 서비스 간의 가장 큰 차이는 끝점이 컴퓨터의 로컬이 아닌 클라우드에서 노출된다는 점입니다.

이 자습서의 항목을 순서대로 진행한 후에는 실행되는 서비스와, 서비스 작업을 호출할 수 있는 클라이언트가 있을 것입니다. 첫번째 항목에서는 계정을 설정하는 방법을 설명합니다. 다음 단계에서는 계약을 사용하는 서비스 정의 방법, 서비스 구현 방법, 클라우드에서의 서비스 구성 방법을 설명합니다. 또한 서비스 호스팅 및 실행 방법을 설명합니다. 만들어지는 서비스는 자체 호스팅되며 클라이언트와 서비스가 동일한 컴퓨터에서 실행됩니다. 코드 또는 구성 파일을 사용하여 서비스를 구성할 수 있습니다. 자세한 내용은 [서비스 버스에 등록할 WCF 서비스 구성](https://msdn.microsoft.com/library/ee173579.aspx) 및 [서비스 버스용 서비스 빌드](https://msdn.microsoft.com/library/ee173564.aspx)를 참조하세요.

마지막 세 단계에서는 클라이언트 응용 프로그램 만들기 및 구성 방법과, 호스트의 기능에 액세스할 수 있는 클라이언트 만들기 및 사용 방법을 설명합니다. 자세한 내용은 [서비스 버스 클라이언트 응용 프로그램 빌드](https://msdn.microsoft.com/library/ee173543.aspx) 및 [서비스 버스 서비스 검색 및 노출](https://msdn.microsoft.com/library/dd582704.aspx)을 참조하세요.

이 섹션의 모든 항목에서는 모든 개발 환경으로 Visual Studio를 사용한다고 가정합니다.

## 계정 등록

첫 단계는 서비스 버스 네임스페이스를 만들고 SAS(공유 액세스 서명) 키를 확보합니다. 서비스 네임스페이스는 서비스 버스를 통해 노출되는 각 응용 프로그램에 대한 응용 프로그램 경계를 제공합니다. 서비스 네임스페이스 및 SAS 키 조합은 서비스 버스에 자격 증명을 제공하여 응용 프로그램에 대한 액세스를 인증합니다.

네임스페이스를 만들려면 [방법: 서비스 버스 서비스 네임스페이스 만들기 또는 수정](https://msdn.microsoft.com/library/hh690931.aspx)에서 설명하는 단계를 따릅니다.

>[AZURE.NOTE]클라이언트와 서비스 응용 프로그램에 동일한 네임스페이스를 사용할 필요는 없습니다.

1. Azure 포털의 주 창에서 이전 단계에서 만든 서비스 네임스페이스의 이름을 클릭합니다.

2. **구성**을 클릭하여 서비스 네임스페이스에 대한 기본 공유 액세스 정책을 확인합니다.

3. **RootManageSharedAccessKey** 정책에 대한 기본 키를 기록해 두거나 클립보드로 복사합니다. 이 자습서의 뒷부분에서 이 값을 사용하게 됩니다.

## 서비스 버스에 사용할 WCF 서비스 계약을 정의합니다.

서비스 계약은 서비스가 지원하는 작업(메서드 또는 함수에 대한 웹 서비스 용어)을 지정합니다. 계약은 C++, C#, 또는 Visual Basic 인터페이스를 정의하여 만듭니다. 인터페이스의 각 메서드는 특정 서비스 작업에 해당합니다. 각 인터페이스에는 [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 특성이, 각 작업에는 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 특성이 적용되어 있어야 합니다. [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 특성을 포함하는 인터페이스의 메서드에 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 특성이 없으면 해당 메서드는 드러나지 않습니다. 이 작업을 위한 코드는 과정을 수행하면서 예제에 제공됩니다. 계약 정의 방법에 대한 자세한 내용은 [서비스 버스용 WCF 계약 디자인](https://msdn.microsoft.com/library/ee173585.aspx)을 참조하세요. 계약 및 서비스에 대한 더 자세한 논의는 WCF 설명서의 [서비스 디자인 및 구현](https://msdn.microsoft.com/library/ms729746.aspx)을 참조하세요.

### 인터페이스와 함께 서비스 버스 계약을 만들려면

1. **시작 메뉴**에서 프로그램을 마우스 오른쪽 단추로 누른 다음 **관리자 권한으로 실행**을 선택하여 Visual Studio를 관리자 권한으로 엽니다.

1. 새 콘솔 응용 프로그램 프로젝트를 만듭니다. **파일** 메뉴를 클릭하고 **새로 만들기**를 선택한 다음 **프로젝트**를 클릭합니다. **새 프로젝트** 대화 상자에서 **Visual C#**를 클릭합니다(**Visual C#**가 표시되지 않을 경우 **다른 언어** 아래 확인). **콘솔 응용 프로그램** 템플릿을 클릭한 다음 이름을 **EchoService**로 지정합니다. 기본 **위치**를 사용합니다. **확인**을 클릭하여 프로젝트를 만듭니다.

1. 프로젝트에 `System.ServiceModel.dll`에 대한 참조 추가: 솔루션 탐색기에서 프로젝트 폴더 아래 **참조** 폴더를 마우스 오른쪽 단추로 클릭한 후 **참조 추가**를 클릭합니다. **참조 추가** 대화 상자의 **.NET** 탭을 선택하고 **System.ServiceModel**이 보일 때까지 아래로 스크롤합니다. 해당 항목을 선택하고 **확인**을 클릭합니다.

1. 솔루션 탐색기에서 Program.cs 파일을 두 번 클릭하여 편집기에서 엽니다.

1. System.ServiceModel 네임스페이스에 대한 `using` 문을 추가합니다.

	```
	using System.ServiceModel;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx)은 WCF의 기본 기능에 프로그래밍 방식의 액세스를 가능하게 하는 네임스페이스입니다. 서비스 버스는 WCF의 많은 개체와 특성을 사용하여 서비스 계약을 정의합니다.

1. 네임스페이스 이름을 **EchoService**의 기본 이름에서 **Microsoft.ServiceBus.Samples**로 변경합니다.

	>[AZURE.IMPORTANT]이 자습서에서는 C# 네임스페이스 Micr**osoft.ServiceBus.Samples**를 사용합니다. 이것은 6단계: WCF 클라이언트 구성의 구성 파일에서 사용된 계약 관리 유형의 네임스페이스입니다. 이 샘플을 빌드할 때 아무 네임스페이스나 지정할 수 있지만, 응용 프로그램 구성 파일에서 계약과 서비스의 네임스페이스를 그에 맞게 수정하지 않으면 자습서가 작동하지 않습니다. App.config 파일에서 지정한 네임스페이스는 C# 파일에서 지정한 네임스페이스와 동일합니다.

1. `Microsoft.ServiceBus.Samples` 네임스페이스 선언 직후 네임스페이스 안에서 이름이 `IEchoContract`인 새 인터페이스를 정의하고 네임스페이스 값이 ****http://samples.microsoft.com/ServiceModel/Relay/**인 `ServiceContractAttribute` 특성을 해당 인터페이스에 적용합니다. 네임스페이스 값은 코드 전반에 사용하는 네임스페이스에 따라 다릅니다. 대신 네임스페이스 값은 이 계약에 대한 고유 식별자로 사용됩니다. 네임스페이스를 명시적으로 지정하면 기본 네임스페이스 값이 계약 이름에 추가되는 경우를 방지합니다.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	}
	```

	>[AZURE.NOTE]일반적으로 서비스 계약 네임스페이스는 버전 정보가 들어있는 명명 체계를 포함합니다. 서비스 계약 네임 스페이스에 버전 정보를 포함하면 서비스에서 새로운 네임스페이스로 새 서비스 계약을 정의하고 새 끝점에 노출함으로써 주요 변경 내용을 분리할 수 있습니다. 이렇게 하면 클라이언트가 업데이트 없이도 기존의 서비스 계약을 지속적으로 사용할 수 있습니다. 버전 정보는 날짜 또는 빌드 번호로 구성될 수 있습니다. 자세한 내용은 [서비스 버전 관리](http://go.microsoft.com/fwlink/?LinkID=180498)를 참조하십시오. 이 자습서에서는 서비스 계약 네임 스페이스의 명명 체계에 버전 정보가 포함되지 않았습니다.

1. IEchoContract 인터페이스 내에서 `IEchoContract` 계약이 인터페이스에 노출하는 단일 작업에 대한 메서드를 선언하고 공개 서비스 버스 계약의 일부로 노출하려는 메서드에 `OperationContractAttribute` 특성을 적용합니다.

	```
	[OperationContract]
	string Echo(string text);
	```

1. 계약의 외부에서 다음과 같이 `IEchoChannel` 및 `IClientChannel` 인터페이스에서 모두 상속되는 채널을 선언합니다.

	```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    publicinterface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

	채널은 호스트 및 클라이언트가 서로 정보를 전달하는 WCF 개체입니다. 나중에 두 응용 프로그램 간에 정보를 에코할 채널에 대한 코드를 작성합니다.

1. **빌드** 메뉴에서 **솔루션 빌드**를 클릭하거나 F6을 눌러 작업의 정확성을 확인합니다.

### 예

다음 코드는 서비스 버스 계약을 정의하는 기본 인터페이스를 보여줍니다.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

이제 인터페이스를 만들었으므로 해당 인터페이스를 구현할 수 있습니다.

## 서비스 버스를 사용하도록 WCF 계약 구현

서비스 버스 서비스를 만들려면 첫째로 계약을 만들어야 하는데, 계약은 인터페이스를 사용하여 정의됩니다. 인터페이스를 만드는 방법에 자세한 내용은 이전 단계를 참조하십시오. 다음 단계는 인터페이스를 구현합니다. 여기에는 사용자 정의 `IEchoContract` 인터페이스를 구현하는 이름이 `EchoService`인 클래스를 만드는 단계가 포함됩니다. 인터페이스를 구현한 후 App.config 구성 파일을 사용하여 인터페이스를 구현합니다. 구성 파일은 서비스 이름, 계약 이름, 서비스 버스와 통신에 사용되는 프로토콜 유형과 같은 응용 프로그램에 필요한 정보를 포함합니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다. 서비스 계약 구현 방법에 대한 더 일반적인 논의는 Windows Communication Foundation(WCF) 설명서의 [서비스 계약 구현](https://msdn.microsoft.com/library/ms733764.aspx)을 참조하세요.

1. `IEchoContract` 인터페이스 정의 바로 뒤에 이름이 `EchoService`인 클래스를 새로 만듭니다. `EchoService` 클래스가 `IEchoContract` 인터페이스를 구현합니다. 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	다른 인터페이스 구현과 유사하게, 다른 파일에 정의를 구현할 수 있습니다. 하지만, 이 자습서에서는 구현이 인터페이스 정의 및 `Main` 메서드와 같은 파일에 위치합니다.

1. 서비스 이름과 네임스페이스를 표시하는 [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) 특성을 적용합니다.

	```[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. `EchoService` 클래스의 `IEchoContract` 인터페이스에서 정의된 `Echo` 메서드를 구현합니다.

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. **빌드** 메뉴를 클릭하고 **솔루션 빌드**를 클릭하여 작업의 정확성을 확인합니다.

### 서비스 호스트에 대한 구성을 정의하려면

1. 구성 파일은 WCF 구성 파일과 매우 유사합니다. 여기에는 서비스 이름, 끝점(즉, 클라이언트와 호스트가 서로 통신하도록 서비스 버스가 노출하는 위치) 및 바인딩(통신에 사용되는 프로토콜 유형)이 포함되어 있습니다. 주요 차이점은 구성된 서비스 끝점이 .NET Framework에 속하지 않는 [netTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)을 참조한다는 것입니다. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx)은 서비스 버스에서 정의한 바인딩 중 하나입니다.

1. **솔루션 탐색기**에서 현재 다음과 같은 XML 요소를 포함하는 App.config를 클릭합니다.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	</configuration>
	```

1. `<system.serviceModel>` XML 요소를 App.config 파일에 추가합니다. 이것은 하나 이상의 서비스를 정의하는 WCF 요소입니다. 이 예제에서는 서비스 이름 및 끝점을 정의하는 데 사용합니다.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
  
	  </system.serviceModel>
	</configuration>
	```

1. `<system.serviceModel>` 태그 안에 `<services>` 요소를 추가합니다. 단일 구성 파일 내에 여러 서비스 버스 응용 프로그램을 정의할 수 있습니다. 그러나 이 자습서에서는 하나만 정의합니다.
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. `<services>` 요소 안에서 `<service>` 요소를 추가하여 서비스의 이름을 정의합니다.

	```
	<servicename="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. `<service>` 요소 안에서 끝점 계약의 위치와 해당 끝점의 바인딩 형식을 정의합니다.

	```
	<endpointcontract="Microsoft.ServiceBus.Samples.IEchoContract"binding="netTcpRelayBinding"/>
	```

	끝점은 클라이언트가 호스트 응용 프로그램을 검색하는 위치를 정의합니다. 나중에 이 자습서에서는 이 단계를 사용하여 서비스를 버스를 통해 호스트를 완전히 노출하는 URI를 만듭니다. 바인딩은 서비스 버스와의 통신에 TCP를 프로토콜로 사용한다고 선언합니다.


1. `<services>` 요소 바로 뒤에 다음 바인딩 확장을 추가합니다.
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. **빌드** 메뉴에서 **솔루션 빌드**를 클릭하여 작업의 정확성을 확인합니다.

### 예

다음 코드에서는 서비스 계약의 구현을 보여줍니다.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

다음 예제에서는 서비스 호스트와 관련된 App.config 파일의 기본 형식을 보여줍니다.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## 서비스 버스에 등록할 기본 웹 서비스 호스팅 및 실행

이 단계에서는 기본 서비스 버스 서비스를 실행하는 방법을 설명합니다.

### 서비스 버스 자격 증명을 만들려면

1. Microsoft.ServiceBus.dll에 대한 참조를 프로젝트에 추가: [NuGet 서비스 버스 패키지 사용](https://msdn.microsoft.com/library/dn741354.aspx)을 참조하세요.

	>[AZURE.NOTE]명령줄 컴파일러를 사용하는 경우어셈블리에 대한 경로도 제공해야 합니다.

1. Program.cs에서 Microsoft.ServiceBus 네임스페이스에 `using` 문을 추가합니다.

	```
	using Microsoft.ServiceBus;
	```

1. `Main()`에서 콘솔 창으로부터 읽은 네임스페이스와 SAS 키를 저장할 두 변수를 만듭니다.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	SAS 키는 나중에 서비스 버스 프로젝트에 액세스하는 데 사용됩니다. 서비스 네임스페이스는 서비스 URI를 만들기 위해 매개 변수 형태로 `CreateServiceUri`에 전달됩니다.

4. [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) 개체를 사용하여 자격 증명 형식으로 사용할 SAS 키를 선언합니다. 마지막 단계에서 추가한 코드 바로 뒤에 다음 코드를 추가합니다.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### 서비스에 대한 기본 주소를 만들려면

1. 마지막 단계에서 추가한 코드 다음에 서비스의 기본 주소에 대한 `Uri` 인스턴스를 만듭니다. 이 URI는 서비스 버스 체계, 네임스페이스 및 서비스 인터페이스의 경로를 지정합니다.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	"sb"는 Service Bus scheme(서비스 버스 체계)의 약어로, TCP를 프로토콜로 사용할 것임을 나타냅니다. 이 항목은 또한 바인딩에서 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx)을 지정할 때 구성 파일에서 표시되었습니다.
	
	이 자습서에 대한 URI는 `sb://putServiceNamespaceHere.windows.net/EchoService`입니다.

### 서비스 호스트를 만들고 구성하려면

1. 연결 모드를 `AutoDetect`로 설정합니다.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	연결 모드는 HTTP 또는 TCP 등, 서비스가 서비스 버스와의 통신에 사용하는 프로토콜을 설명합니다. 서비스는 기본 설정 `AutoDetect`를 사용하여 TCP가 사용 가능하면 TCP를 통해, 사용할 수 없으면 HTTP를 통해 서비스 버스에 대한 연결을 시도합니다. 이 항목은 클라이언트 통신에 대해 서비스가 지정한 프로토콜과는 다릅니다. 해당 프로토콜은 사용한 바인딩에 따라 결정됩니다. 예를 들어, 서비스에서 끝점이 HTTP를 통해 클라이언트와 통신하도록 지정하는 [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) 바인딩(서비스 버스에 노출)을 사용할 수 있습니다. 동일한 서비스에서 서비스가 TCP를 통해 서비스 버스와 통신하도록 **ConnectivityMode.AutoDetect**를 지정할 수 있습니다.

1. 이 섹션 앞부분에서 만든 URI를 사용하여 서비스 호스트를 만듭니다.

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	서비스 호스트는 서비스를 인스턴스화하는 WCF 개체입니다. 이 개체에 만들려는 서비스 형식(`EchoService` 형식)을 전달하며 서비스를 노출하려는 주소에도 전달합니다.

1. Program.cs 파일의 위쪽에 [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) 및 [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx)에 대한 참조를 추가합니다.

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. `Main()`으로 돌아가 공용 액세스가 가능하도록 끝점을 구성합니다.

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	이 단계에서는 응용 프로그램이 프로젝트에 대한 서비스 버스 ATOM 피드를 조사하여 공개적으로 찾을 수 있는 서비스 버스를 알립니다. **DiscoveryType**를 **개인**으로 설정해도 클라이언트는 계속 서비스에 액세스할 수 있습니다. 그러나 서비스 버스 네임스페이스를 검색할 때는 서비스가 표시되지 않습니다. 대신 클라이언트 끝점 경로를 미리 알고 있어야 합니다.

1. 서비스 자격 증명을 App.config 파일에 정의된 서비스 끝점에 적용합니다.

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	이전 단계에서 설명한 것처럼 구성 파일에서 여러 서비스와 끝점을 선언할 수도 있습니다. 그렇게 한 경우 이 코드는 구성 파일을 트래버스하여 자격 증명을 적용해야 하는 모든 끝점을 검색합니다. 그러나 이 자습서에서는 구성 파일에 단 하나의 끝점이 있습니다.

### 서비스 호스트를 열려면

1. 서비스를 엽니다.

	```
	host.Open();
	```

1. 서비스가 실행 중임을 사용자에게 알리고 서비스 종료 방법을 설명합니다.

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. 완료되면 서비스 호스트를 닫습니다.

	```
	host.Close();
	```

1. F6을 눌러 프로젝트를 빌드합니다.

### 예

다음 예제는 자습서에 포함된 이전 단계의 구현 및 서비스 계약을 포함하고 콘솔 응용 프로그램에 서비스를 호스트합니다. 다음을 이름이 EchoService.exe인 실행 파일로 컴파일합니다.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## 서비스 계약에 대한 WCF 클라이언트 만들기

다음 단계는 기본 서비스 버스 클라이언트 응용 프로그램을 만들고 이후 단계에서 구현하는 서비스 계약을 정의하는 것입니다. 이 단계의 대부분은 App.config 파일을 편집하며 자격 증명을 사용하여 서비스 버스에 연결하는 등, 서비스를 만드는 단계와 유사합니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다.

1. 다음을 수행하여 클라이언트에 대 해 현재 Visual Studio 솔루션에서 새 프로젝트를 만듭니다.
	1. 솔루션 탐색기에서, 서비스를 포함하는 동일한 솔루션 안에서 현재 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭합니다. 그런 다음 **새 프로젝트**를 클릭합니다.
	2. **새 프로젝트 추가** 대화 상자에서 **Visual C#**을 클릭하고(**Visual C#**이 보이지 않으면 **다른 언어** 아래 확인) **콘솔 응용 프로그램** 서식 파일을 선택하여 이름을 **EchoClient**로 지정합니다.
	3. **확인**을 클릭합니다.<br />

1. 솔루션 탐색기에서 Program.cs 파일을 두 번 클릭하여 편집기에서 **EchoClient** 프로젝트를 엽니다.

1. 네임스페이스 이름을 기본 이름인 `EchoClient`에서 `Microsoft.ServiceBus.Samples`로 변경합니다.

1. 프로젝트에 대해 System.ServiceModel.dll 참조를 추가합니다.
	1. 솔루션 탐색기의 **EchoClient** 프로젝트에서 **참조**를 마우스 오른쪽 단추로 클릭합니다. 그런 다음 **참조 추가**를 클릭합니다.
	2. 이미 이 자습서의 첫 단계에서 이 어셈블리에 대한 참조를 추가했으므로 이제 **최근에 사용한 항목** 탭에 나열됩니다. **최근에 사용한 항목**을 클릭한 다음 목록에서 **System.ServiceModel.dll**을 선택합니다. 그런 후 **OK**를 클릭합니다. **최근에 사용한 항목** 탭에 **System.ServiceModel.dll**이 표시되지 않으면 **찾아보기** 탭을 클릭하여 **C:\\Windows\\Microsoft.NET\\Framework\\v3.0\\Windows Communication Foundation**으로 이동합니다. 여기에서 어셈블리를 선택합니다. <br />

1. Program.cs 파일에서[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) 네임스페이스에 대한 `using` 문을 추가합니다.

	```
	using System.ServiceModel;
	```

1. 앞의 단계를 반복하여 Microsoft.ServiceBus.dll에 대한 참조와 [Microsoft.ServiceBus](https://msdn.microsoft.com/library/microsoft.servicebus.aspx) 네임스페이스를 프로젝트에 추가합니다.

1. 다음 예제와 같이 서비스 계약 정의를 네임스페이스에 추가합니다. 이 정의는 **서비스** 프로젝트에 사용되는 정의와 동일합니다. 이 코드는 `Microsoft.ServiceBus.Samples` 네임스페이스 위쪽에 추가해야 합니다.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. F6을 눌러 클라이언트를 빌드합니다.

### 예

다음 코드에서는 EchoClient 프로젝트에서 Program.cs 파일의 현재 상태를 보여줍니다.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## WCF 클라이언트 구성

이 단계에서는 이 자습서의 앞에서 만든 서비스에 액세스하는 기본 클라이언트 응용 프로그램의 App.config 파일을 만듭니다. 이 App.config 파일은 계약, 바인딩 및 끝점의 이름을 정의합니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다.

1. 솔루션 탐색기의 클라이언트 프로젝트에서 **App.config**를 두 번 클릭하여 파일을 엽니다. 현재 여기에는 다음 XML 요소가 포함되어 있습니다.

	```
	<?xmlversion="1.0"?>
	<configuration>
	  <startup>
	    <supportedRuntimeversion="v4.0"sku=".NETFramework,Version=v4.0"/>
	  </startup>
	</configuration>
	```

1. `system.serviceModel`에 대한 App.config 파일에 XML 요소를 추가합니다.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	
	  </system.serviceModel>
	</configuration>
	```
	
	이 요소는 응용 프로그램이 WCF 스타일 끝점을 사용하도록 선언합니다. 앞서 설명한 것처럼 서비스 버스 응용 프로그램의 구성은 대부분 WCF 응용 프로그램과 동일합니다. 주요 차이점은 구성 파일이 지정하는 위치입니다.

1. System.serviceModel 요소 안에서 `<client>` 요소를 추가합니다.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	이 단계에서는 WCF 스타일 클라이언트 응용 프로그램을 정의함을 선언합니다.

1. `client` 요소 내에서 끝점의 이름, 계약, 바인딩 형식을 정의합니다.

	```
	<endpointname="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	이 단계에서는 끝점 이름, 서비스에서 정의한 계약, 클라이언트가 TCP를 사용하여 서비스 버스와 통신한다는 사실을 정의합니다. 끝점 이름은 이 끝점 구성을 서비스 URI에 연결하는 다음 절차에서 사용됩니다.

1. <client> 요소 바로 뒤에 다음 바인딩 확장을 추가합니다.
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. **파일**을 클릭한 다음 **모두 저장**을 클릭합니다.

## 예

다음 코드는 Echo 클라이언트에 대한 App.config 파일을 보여줍니다.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## 서비스 버스를 호출하는 WCF 클라이언트 구현

이 단계에서는 이 자습서의 앞에서 만든 서비스에 액세스하는 기본 클라이언트 응용 프로그램을 구현합니다. 서비스와 마찬가지로, 클라이언트는 서비스 버스 액세스와 많은 동일한 작업을 수행합니다.

1. 연결 모드를 설정합니다.

1. 호스트 서비스를 찾는 URI를 만듭니다.

1. 보안 자격 증명을 정의합니다.

1. 연결에 자격 증명을 적용합니다.

1. 연결을 엽니다.

1. 응용 프로그램 특정 작업을 수행합니다.

1. 연결을 닫습니다.

그러나 주요 차이 중 하나는 클라이언트 응용 프로그램이 채널을 사용하여 서비스 버스에 연결하는 것과 달리 서비스는 **ServiceHost**에 대한 호출을 사용한다는 점입니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다.

### 클라이언트 응용 프로그램을 구현 하려면

1. 연결 모드를 **AutoDetect**로 설정합니다. 클라이언트 응용 프로그램의 `Main()` 메서드 안에 다음 코드를 추가합니다.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. 서비스 네임스페이스에 대한 값과, 콘솔에서 읽은 SAS 키를 담을 변수를 정의합니다.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. 서비스 버스 프로젝트에서 호스트의 위치를 정의하는 URI를 만듭니다.

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. 서비스 네임스페이스 끝점에 대한 자격 증명 개체를 만듭니다.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. App.config 파일에 설명된 구성을 로드하는 채널 팩터리를 만듭니다.

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	채널 팩터리는 서비스 및 클라이언트 응용 프로그램이 통신하기 위해 경유하는 채널을 만드는 WCF 개체입니다.

1. 서비스 버스 자격 증명을 적용합니다.

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. 서비스에 대한 채널을 만들어 엽니다.

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. 에코에 대해 기본 사용자 인터페이스 및 기능을 작성합니다.

	```
	Console.WriteLine("Enter text to echo (or [Enter] to exit):");
	string input = Console.ReadLine();
	while (input != String.Empty)
	{
	    try
	    {
	        Console.WriteLine("Server echoed: {0}", channel.Echo(input));
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error: " + e.Message);
	    }
	    input = Console.ReadLine();
	}
	```

	이 코드에서는 서비스에 대한 프록시로 채널 개체의 인스턴스를 사용합니다.

1. 채널을 닫고, 팩터리를 닫습니다.

	```
	channel.Close();
	channelFactory.Close();
	```

## 클라이언트 응용 프로그램을 실행하려면

1. F6을 눌러 솔루션을 빌드합니다. 그러면 이 자습서의 앞 단계에서 만든 클라이언트 프로젝트와 서비스 프로젝트가 모두 빌드되고 각각에 대한 실행 파일을 만듭니다.

1. 클라이언트 응용 프로그램을 실행하기 전에 서비스 응용 프로그램이 실행 중인지 확인합니다.

	이제 이름이 EchoService.exe인 에코 서비스 응용 프로그램의 실행 파일이 서비스 프로젝트 폴더 \\bin\\Debug\\EchoService.exe(디버그 구성) 또는 \\bin\\Release\\EchoService.exe(릴리스 구성) 아래에 있을 것입니다. 서비스 응용 프로그램을 시작하려면 이 파일을 두 번 클릭합니다.

1. 콘솔 창이 열리고 네임스페이스를 입력하라는 메시지가 표시됩니다. 이 콘솔 창에서 서비스 네임스페이스를 입력하고 Enter를 누릅니다.

1. 다음으로, SAS 키를 입력하라는 메시지가 표시됩니다. SAS 키를 입력하고 ENTER를 누릅니다.

	콘솔 창의 출력 예는 다음과 같습니다. 여기서 입력한 값은 오직 예시용입니다.

	`Your Service Namespace: myNamespace`

	`Your SAS Key: <SAS key value>`

	서비스 응용 프로그램이 시작되고 다음 예제에서처럼 콘솔 창에 수신 중인 주소가 출력됩니다.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`
    
1. 클라이언트 응용 프로그램을 실행합니다. 이제 이름이 EchoClient.exe인 에코 클라이언트 응용 프로그램의 실행 파일이 클라이언트 프로젝트 폴더 \\bin\\Debug\\EchoClient.exe(디버그 구성) 또는 \\bin\\Release\\EchoClient.exe(릴리스 구성) 아래에 있을 것입니다. 클라이언트 응용 프로그램을 시작하려면 이 파일을 두 번 클릭합니다.

1. 콘솔 창이 열리고 앞서 서비스 응용 프로그램에 대해 입력한 것과 같은 정보를 입력하라는 메시지가 표시됩니다. 앞의 단계에 따라 클라이언트 응용 프로그램에 대해 서비스 네임스페이스, 발급자 이름, 발급자 암호에 동일한 값을 입력합니다.

1. 이 값을 입력한 후 클라이언트가 서비스에 대한 채널을 열고 다음 콘솔 출력 예제에서 보이는 것처럼 여러 텍스트를 입력하라는 메시지가 표시됩니다.

	`Enter text to echo (or [Enter] to exit):`

	서비스 응용 프로그램에 보낼 여러 텍스트를 입력하고 Enter를 누릅니다. 이 텍스트는 에코 서비스 작업을 통해 서비스에 전달되며 다음 예제 출력에서처럼 서비스 콘솔 창에 표시됩니다.

	`Echoing: My sample text`

	클라이언트 응용 프로그램은 원본 텍스트인 `Echo` 작업의 반환 값을 받아 콘솔 창에 출력합니다. 클라이언트 콘솔 창의 출력 예는 다음과 같습니다.

	`Server echoed: My sample text`

1. 이런 방식으로 클라이언트에서 서비스로 문자 메시지를 지속적으로 보낼 수 있습니다. 작업을 마치면 클라이언트와 서비스 콘솔 창에서 Enter를 눌러 두 응용 프로그램을 모두 종료합니다.

## 예

다음 예제에서는 클라이언트 응용 프로그램을 만드는 방법, 서비스 작업 호출 방법 및 작업 호출 완료 후 클라이언트를 닫는 방법 등을 설명합니다.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

클라이언트를 시작하기 전에 서비스가 실행 중인지 확인합니다.

## 다음 단계

이 자습서에서는 서비스 버스 "릴레이" 기능을 사용하여 서비스 버스 클라이언트 응용 프로그램 및 서비스를 빌드하는 방법을 보여줍니다. 서비스 버스 [조정된 메시징](service-bus-messaging-overview.md/#Brokered-messaging)을 사용하는 유사한 자습서는 [서비스 버스 조정된 메시징 .NET 자습서](https://msdn.microsoft.com/library/hh367512.aspx)를 참조하세요.

서비스 버스에 대한 자세한 내용은 다음 항목을 참조하세요.

- [서비스 버스 메시징 개요](service-bus-messaging-overview.md)
- [서비스 버스 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
- [서비스 버스 아키텍처](service-bus-architecture.md)

<!---HONumber=Sept15_HO3-->