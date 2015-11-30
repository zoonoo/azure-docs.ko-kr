<properties
   pageTitle="서비스 패브릭 응용 프로그램 RunAs 보안 정책의 이해 | Microsoft Azure"
   description="응용 프로그램이 시작되려면 일부 권한 있는 작업을 수행해야 하는 SetupEntrypoint를 포함하여 시스템 및 로컬 보안 계정을 통해 서비스 패브릭 응용 프로그램을 실행하는 방법에 대한 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/21/2015"
   ms.author="mfussell"/>

# RunAs: 다른 보안 권한으로 서비스 패브릭 응용 프로그램 실행
서비스 패브릭은 "RunAs"라고 하는 다른 사용자 계정을 통해 클러스터에서 실행 중인 응용 프로그램을 보호하는 기능을 제공합니다. 또한 파일, 디렉터리, 인증서 등과 같은 사용자 계정을 통해 응용 프로그램에서 사용하는 리소스도 보호합니다.

기본적으로 서비스 패브릭 응용 프로그램은 Fabric.exe 프로세스가 실행되고 있는 계정을 통해 실행됩니다. 또한 응용 프로그램의 매니페스트 내에 지정된 로컬 사용자 계정을 통해 응용 프로그램을 실행하는 기능을 제공합니다. RunAs가 지원되는 계정 유형은 **LocalUser**, **NetworkService**, **LocalService** 및 **LocalSystem**입니다.

> [AZURE.NOTE]도메인 계정은 Active Directory가 제공되는 Windows 서버 배포에서 지원됩니다.

사용자 그룹을 정의하고 만든 후 그 그룹에 사용자를 한 명 이상 추가하여 한꺼번에 관리할 수 있습니다. 이 기능은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우에 특히 유용합니다.

## 설정 진입점에 대한 RunAs 정책 설정

[응용 프로그램 모델](service-fabric-application-model.md)에서 설명한 것처럼 **SetupEntryPoint**는 서비스 패브릭과 같은 자격 증명(일반적으로 *네트워크* 계정)을 사용하여 다른 진입점보다 먼저 실행되는 권한 있는 진입점입니다. **EntryPoint**를 통해 지정되는 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 따라서 별도의 설정 진입점을 두면 한동안은 높은 권한을 사용하여 서비스 호스트 exe를 실행하지 않아도 됩니다. **EntryPoint**를 통해 지정된 실행 파일은 **SetupEntryPoint**가 성공적으로 종료된 후 실행됩니다. 종료되지 않거나 충돌하는 경우 결과 프로세스를 모니터링하여 다시 시작합니다(**SetupEntryPoint**를 사용하여 다시 시작).

아래는 서비스에 대한 SetupEntryPoint 및 기본 EntryPoint를 보여주는 서비스 매니페스트의 간단한 예입니다.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### RunAs 정책 구성

SetupEntryPoint가 있도록 서비스를 구성했으면 응용 프로그램 매니페스트에서 아래에서 실행되는 보안 권한을 변경할 수 있습니다. 아래는 관리자 계정 권한으로 실행되도록 서비스를 구성하는 방법을 보여주는 예입니다.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

먼저, 사용자 이름(예: SetupAdminUser)을 사용하여 **주체** 섹션을 만듭니다. 이 사용자가 관리자 시스템 그룹의 구성원임을 나타내는 이름입니다.

다음으로, **ServiceManifestImport** 섹션에서 정책을 구성하여 이 주체를 **SetupEntryPoint**에 적용합니다. 이렇게 하면 서비스 패브릭에서는 MySetup.bat 파일이 실행될 때 관리자 권한이 있는 RunAs인지 확인합니다. 기본 진입점에 정책을 적용하지 *않으면* MyServiceHost.exe의 코드가 시스템 NetworkService 계정에서 실행됩니다. 이 계정은 모든 서비스 진입점이 RunAs인 기본 계정입니다.

이제 관리자 권한을 테스트하기 위해 MySetup.bat 파일을 Visual Studio 프로젝트에 추가하겠습니다. Visual Studio에서 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 새 파일 MySetup.bat을 추가합니다. 다음으로 이 파일을 서비스 패키지에 포함합니다. 기본적으로 서비스 패키지에 포함되어 있지 않습니다. MySetup.bat 파일이 패키지에 포함되었는지 확인하려면 파일을 선택하고, 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 표시하고, 속성을 선택한 후 속성 대화 상자에서 **출력 디렉터리로 복사**가 **변경된 내용만 복사**로 설정되었는지 확인합니다. 이 내용이 아래 스크린샷에 나와 있습니다.

![SetupEntryPoint 배치 파일에 대한 Visual Studio CopyToOutput][Image1]

이제 MySetup.bat 파일을 열고 다음 명령을 추가합니다. ~~~ REM Set a system environment variable. This requires administrator privilege setx -m TestVariable "MyValue" echo System TestVariable set to > test.txt echo %TestVariable% >> test.txt

REM To delete this system variable us REM REG delete "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment" /v TestVariable /f ~~~

다음으로 솔루션을 빌드하여 로컬 개발 클러스터에 배포합니다. 서비스가 시작되면 서비스 패브릭 탐색기에 보이는 것처럼 MySetup.bat이 두 가지 방법으로 성공한 것을 볼 수 있습니다. PowerShell 명령 프롬프트를 열고 다음을 입력합니다. ~~~ [Environment]::GetEnvironmentVariable("TestVariable","Machine") ~~~ 예: ~~~ PS C:\\ [Environment]::GetEnvironmentVariable("TestVariable","Machine") MyValue ~~~

서비스 패브릭 탐색기에서 서비스가 배포되어 시작된 노드 이름을 확인합니다. 예를 들어 노드 1로 이동한 후 응용 프로그램 인스턴스 작업 폴더로 이동하여 **TestVariable** 값을 보여주는 .txt 파일을 찾아봅니다. 예를 들어 서비스가 노드 2에 배포된 경우 이 경로로 이동하여 MyApplicationType을 찾아볼 수 있습니다.

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  SetupEntryPoint에서 PowerShell 명령 실행
**SetupEntryPoint**에서 PowerShell을 실행하려면 PowerShell 파일을 가리키는 배치 파일에서 PowerShell.exe를 실행하면 됩니다. 먼저, 서비스 프로젝트(예: MySetup.ps1)에 PowerShell 파일을 추가합니다. 이 파일도 서비스 패키지에 포함되도록 *변경된 내용만 복사*로 설정해야 합니다. 아래는 시스템 환경 변수 *TestVariable*을 설정하는 PowerShell 파일 MySetup.ps1을 실행하는 간단한 배치 파일을 보여주는 예입니다.

PowerShell 파일을 실행하는 MySetup.bat입니다. ~~~ powershell.exe -ExecutionPolicy Bypass -Command ".\\MySetup.ps1" ~~~

PowerShell 파일에서 다음을 추가하여 시스템 환경 변수를 설정합니다. ~~~ [Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine") [Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt ~~~

## 서비스에 RunAs 정책 적용 
위의 단계에서 SetupEntryPoint에 RunAs 정책을 적용하는 방법을 살펴보았습니다. 이번에는 서비스 정책으로 적용할 수 있는 다양한 주체를 만드는 방법을 좀 더 자세히 살펴보겠습니다.

### 로컬 사용자 그룹 만들기
사용자 그룹을 정의하고 만든 후 그 그룹에 사용자를 한 명 이상 추가할 수 있습니다. 이 기능은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우에 특히 유용합니다. 아래는 관리자 권한이 있는 **LocalAdminGroup**이라는 로컬 그룹에 두 사용자 Customer1과 Customer2를 그룹 구성원으로 추가하는 방법을 보여주는 예입니다.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### 로컬 사용자 만들기
응용 프로그램 내에서 서비스를 보호하는 데 사용할 수 있는 로컬 사용자를 만들 수 있습니다. 응용 프로그램 매니페스트의 주체 섹션에서 LocalUser 계정 유형이 지정되면 서비스 패브릭에서는 응용 프로그램이 배포되는 컴퓨터에 로컬 사용자 계정을 만듭니다. 이러한 계정은 기본적으로 응용 프로그램에 지정된 이름과 같은 이름(아래 예에서는 “Customer3”)을 사용하지 않습니다. 그 대신 이름이 동적으로 생성되고 임의의 암호가 부여됩니다.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~
 
<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## 서비스 코드 패키지에 정책 할당
**ServiceManifestImport**에 대한 **RunAsPolicy** 섹션은 주체에서 코드 패키지 실행에 사용할 계정을 지정하고 서비스 매니페스트의 코드 패키지를 주체 섹션의 계정과 연결합니다. 설정 또는 기본 진입점에 대해 이 정책을 적용할 수도 있고 모두로 지정하여 양쪽에 적용할 수도 있습니다. 아래는 여러 정책을 적용하는 방법을 보여주는 예입니다.

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

**EntryPointType**을 지정하지 않으면 기본적으로 EntryPointType=”Main”으로 지정됩니다. 특히 시스템 계정을 통해 특정 고수준 권한 설정 작업을 수행하는 동시에 실제 서비스 코드는 저수준 권한 계정에서 실행될 수 있게 하려면 **SetupEntryPoint**를 지정하는 것이 매우 유용합니다.

### 모든 서비스 코드 패키지에 기본 정책 적용
**DefaultRunAsPolicy** 섹션은 특정 **RunAsPolicy**가 정의되지 않은 모든 코드 패키지에 대해 기본 사용자 계정을 지정하는 데 사용됩니다. 응용 프로그램에서 사용하는 서비스 매니페스트에 지정된 대부분의 코드 패키지가 동일한 RunAs 사용자를 통해 실행되어야 하는 경우 모든 코드 패키지에 대해 **RunAsPolicy**를 지정하는 대신 응용 프로그램에서 해당 사용자 계정을 사용하여 기본 RunAs 정책을 정의할 수 있습니다. 예를 들어 다음은 코드 패키지의 **RunAsPolicy**가 지정되지 않으면 주체 섹션에 지정된 MyDefaultAccount를 통해 코드 패키지가 수행되는 예입니다.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## http 및 https 끝점에 대해 SecurityAccessPolicy 할당
서비스에 RunAs 정책을 적용하고 서비스 매니페스트에서 http 프로토콜을 사용하여 끝점 리소스를 선언할 경우 이러한 끝점에 할당된 포트의 ACL이 서비스가 실행되는 RunAs 사용자 계정에 대해 올바르게 처리되도록 **SecurityAccessPolicy**를 지정해야 합니다. 그렇지 않으면 http.sys에서 해당 서비스에 액세스할 수 없어서 클라이언트의 호출과 함께 오류가 발생합니다. 아래는 *ServiceEndpointName*이라는 끝점에 Customer3라는 계정을 적용하여 전체 액세스 권한을 부여하는 예입니다.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

https 끝점의 경우 응용 프로그램 매니페스트의 인증서 섹션에 **EndpointBindingPolicy** 인증서가 정의된 클라이언트로 돌아갈 수 있도록 인증서 이름을 추가로 지정해야 합니다.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## 완전한 응용 프로그램 매니페스트의 예
아래의 응용 프로그램 매니페스트는 위에서 설명한 여러 설정을 보여줍니다.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <User Name="Customer1" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

* [응용 프로그램 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

[Image1]: media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=Nov15_HO4-->