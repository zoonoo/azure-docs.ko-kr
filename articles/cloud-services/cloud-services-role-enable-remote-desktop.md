<properties 
pageTitle="Azure 클라우드 서비스의 역할에 대한 원격 데스크톱 연결 설정" 
description="원격 데스크톱 연결을 허용하기 위해 Azure 클라우드 서비스 응용 프로그램을 구성하는 방법" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Azure에서 역할에 대한 원격 데스크톱 연결 설정
응용 프로그램을 실행하는 클라우드 서비스를 만든 후 해당 응용 프로그램의 역할 인스턴스에 원격으로 액세스하여 설정을 구성하거나 문제를 해결할 수 있습니다. 원격 데스크톱에 대해 클라우드 서비스가 구성되어 있어야 합니다.

* 원격 데스크톱 인증을 사용하도록 설정하기 위한 인증서가 필요한가요? 인증서를 [만들고](cloud-services-certs-create.md) 아래에서 구성합니다.
* 클라우드 서비스에 대한 원격 데스크톱이 이미 설치되어 있나요? 클라우드 서비스에 [연결합니다](#remote-into-role-instances).

## 웹 역할 또는 작업자 역할에 대해 원격 데스크톱 연결 설정
웹 역할 또는 작업자 역할에 대해 원격 데스크톱 연결을 사용하도록 설정하려면 응용 프로그램의 서비스 모델에서 연결을 설정하거나 인스턴스가 실행된 후 Azure 관리 포털을 사용하여 연결을 설정하면 됩니다.

### 서비스 모델에서 연결 설정
**RemoteAccess** 모듈 및 **RemoteForwarder** 모듈을 서비스 모델로 가져오는 서비스 정의 파일에 **Imports** 요소가 추가되어야 합니다. Visual Studio를 사용하여 Azure 프로젝트를 만들면 서비스 모델에 대한 파일이 생성됩니다.

서비스 모델은 [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) 파일과 [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) 파일로 구성됩니다. 클라우드 서비스에 대한 응용 프로그램을 배포할 준비가 되면 정의 파일이 역할 이진과 함께 패키지됩니다. ServiceConfiguration.cscfg 파일은 응용 프로그램 패키지와 함께 배포되어 Azure에서 응용 프로그램 실행 방법을 결정하는 데 사용됩니다.

프로젝트를 만든 후 Visual Studio를 사용하여 [원격 데스크톱 연결을 사용할 수 있습니다](https://msdn.microsoft.com/library/gg443832.aspx).

연결을 구성한 후 서비스 정의 파일은 `<Imports>` 요소가 추가된 다음 예제와 유사해야 합니다.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
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
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```

서비스 구성 파일은 연결을 설정할 때 입력한 값을 사용하는 다음 예제와 유사해야 합니다. `<ConfigurationSettings>` 및 `<Certificates>` 요소를 확인하세요.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```

응용 프로그램을 [패키지](cloud-services-model-and-package.md#cspkg) 및 [게시](cloud-services-how-to-create-deploy-portal.md)하는 경우 **모든 역할에 대해 원격 데스크톱 사용** 확인란을 선택해야 합니다. [이](https://msdn.microsoft.com/library/ff683672.aspx) 문서에서는 Visual Studio 및 클라우드 서비스 사용과 관련된 일반 작업 목록을 제공합니다.

### 실행 중인 인스턴스에 대한 연결 설정
클라우드 서비스에 대한 구성 페이지에서 원격 데스크톱 연결 설정을 사용하거나 수정할 수 있습니다. 자세한 내용은 [역할 인스턴스에 대한 원격 액세스 구성](cloud-services-how-to-configure.md)을 참조하세요.




## 원격으로 역할 인스턴스 액세스
웹 역할 또는 작업자 역할의 인스턴스에 액세스하려면 RDP(원격 데스크톱 프로토콜) 파일을 사용해야 합니다. 관리 포털에서 파일을 다운로드하거나 프로그래밍 방식으로 파일을 검색할 수 있습니다.

### 관리 포털을 통해 RDP 파일 다운로드

다음 단계를 사용하여 관리 포털에서 RDP 파일을 검색한 다음 원격 데스크톱 연결을 사용하여 파일을 통해 인스턴스에 연결합니다.

1.  인스턴스 페이지에서 인스턴스를 선택한 다음 명령 모음에서 **연결**을 클릭합니다.
2.  **저장**을 클릭하여 원격 데스크톱 프로토콜 파일을 로컬 컴퓨터에 저장합니다.
3.  **원격 데스크톱 연결**을 열고 **옵션 표시**를 클릭한 다음 **열기**를 클릭합니다.
4.  RDP 파일을 저장한 위치로 이동하여 파일을 선택하고 **열기**를 클릭한 다음 **연결**을 클릭합니다. 지침을 따라 연결을 완료합니다.

### PowerShell을 사용하여 RDP 파일 가져오기
[Get-azureremotedesktopfile](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet을 사용하면 RDP 파일을 검색할 수 있습니다.

### Visual Studio를 사용하여 RDP 파일 다운로드
Visual Studio에서 서버 탐색기를 사용하면 원격 데스크톱 연결을 만들 수 있습니다.

1.  서버 탐색기에서 **Azure\\Cloud Services\\[클라우드 서비스 이름]** 노드를 확장합니다.
2.  **Staging**(스테이징) 또는 **Production**(프로덕션)을 확장합니다.
3.  개별 역할을 확장합니다.
4.  역할 인스턴스 중 하나를 마우스 오른쪽 단추로 클릭하고 **Connect using Remote Desktop...**(원격 데스크톱을 사용하여 연결...)을 클릭한 다음 사용자 이름 및 암호를 입력합니다.

### 서비스 관리 REST API를 통해 프로그래밍 방식으로 RDP 파일 다운로드
[Download RDP File](https://msdn.microsoft.com/library/jj157183.aspx)(RDP 파일 다운로드) REST 작업을 사용하면 RDP 파일을 다운로드할 수 있습니다. 그런 다음 원격 데스크톱 연결에서 RDP 파일을 사용하여 클라우드 서비스에 액세스할 수 있습니다.

## 다음 단계
응용 프로그램을 [패키지](cloud-services-model-and-package.md) 또는 [업로드(배포)](cloud-services-how-to-create-deploy-portal.md)해야 할 수 있습니다.

<!---HONumber=August15_HO6-->