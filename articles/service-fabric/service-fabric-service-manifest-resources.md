<properties
   pageTitle="서비스 패브릭 서비스 매니페스트 리소스"
	description="서비스 매니페스트에 리소스를 설명하는 방법"
	services="service-fabric"
	documentationCenter=".net"
	authors="sumukhs"
	manager="anuragg"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/26/2015"
	ms.author="sumukhs"/>

# 서비스 매니페스트 리소스

## 개요

서비스 매니페스트는 컴파일된 코드를 변경하지 않고도 서비스에서 사용하는 리소스를 선언/변경할 수 있게 해줍니다. 서비스 패브릭은 서비스에 대한 끝점 리소스 구성을 지원합니다. 서비스 매니페스트에 지정된 리소스에 대한 액세스는 응용 프로그램 매니페스트의 SecurityGroup을 통해 제어할 수 있습니다. 리소스를 선언하면 서비스에 새로운 구성 메커니즘을 도입하지 않고도 배포 시에 이러한 리소스를 변경할 수 있습니다.

## 끝점

서비스 매니페스트에 끝점 리소스가 정의되면 서비스 패브릭에서는 예약된 응용 프로그램 포트 범위에 포함되는 포트를 할당합니다. 또한 서비스에서 리소스의 특정 포트를 요청할 수도 있습니다. 다른 클러스터 노드에서 실행되는 서비스 복제본을 다른 포트 번호에 할당할 수 있으며, 같은 노드에서 실행되는 같은 서비스의 복제본은 같은 포트를 공유합니다. 서비스 복제본에서 이러한 포트를 복제, 클라이언트 요청 수신 등의 다양한 용도에 사용할 수 있습니다.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

config 패키지 설정 파일(settings.xml)의 끝점 참조에 대한 자세한 내용은 [상태 저장 신뢰할 수 있는 서비스 구성](../Service-Fabric/service-fabric-reliable-services-configuration.md)을 참조하세요.

## 샘플
다음 서비스 매니페스트는 &lt;Resources&gt; 요소에서 1 TCP 끝점 리소스 및 2 HTTP 끝점 리소스를 정의합니다.

HTTP 끝점은 서비스 패브릭에 의해 자동으로 ACL 처리됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=August15_HO9-->