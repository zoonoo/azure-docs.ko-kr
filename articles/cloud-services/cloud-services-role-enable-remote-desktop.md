---
title: "Azure 클라우드 서비스에서 원격 데스크톱 사용 | Microsoft Docs"
description: "원격 데스크톱 연결을 허용하기 위해 Azure 클라우드 서비스 응용 프로그램을 구성하는 방법"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: f64c41733f8fa7e34a0b0dfbbff2b565af7cf7db
ms.lasthandoff: 03/25/2017

---

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Azure 클라우드 서비스의 역할에 대해 원격 데스크톱 연결 사용

> [!div class="op_single_selector"]
> * [Azure 포털](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Azure 클래식 포털](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

서비스 정의에 원격 데스크톱 모듈을 포함하여 개발 중에 원격 데스크톱 연결을 사용하도록 설정하거나 원격 데스크톱 확장을 통해 원격 데스크톱을 사용 하도록 선택할 수 있습니다. 권장되는 방법은 원격 데스크톱 확장을 사용하는 것입니다. 이 방법을 사용하면 응용 프로그램이 배포된 후에도 응용 프로그램을 다시 배포할 필요 없이 원격 데스크톱을 사용하도록 설정할 수 있습니다.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Azure 클래식 포털에서 원격 데스크톱 구성
Azure 클래식 포털에서는 응용 프로그램이 배포된 후에도 원격 데스크톱을 사용 가능하게 설정할 수 있도록 원격 데스크톱 확장 접근 방법을 사용합니다. 클라우드 서비스의 **구성** 페이지에서 원격 데스크톱을 사용하도록 설정하거나 가상 컴퓨터에 연결하는 데 사용되는 로컬 관리자 계정, 인증에 사용되는 인증서를 변경하고 만료 날짜를 설정할 수 있습니다.

1. **클라우드 서비스**, 클라우드 서비스의 이름, **구성**을 차례로 클릭합니다.
2. 맨 아래에 있는 **원격** 단추를 클릭합니다.

    ![클라우드 서비스 원격](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > 처음으로 원격 데스크톱을 사용하도록 설정한 후 확인(확인 표시)을 클릭하면 모든 역할 인스턴스가 다시 시작됩니다. 다시 부팅되지 않도록 하려면 암호를 암호화하는 데 사용되는 인증서가 역할에 설치되어 있어야 합니다. 다시 시작되지 않도록 하려면 [클라우드 서비스에 대 한 인증서를 업로드](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) 하고 이 대화 상자로 돌아옵니다.

3. **역할**에서 업데이트할 역할을 선택하거나 모든 역할을 선택하려면 **모두**를 선택합니다.
4. 다음 중 하나를 변경합니다.

   * 원격 데스크톱을 사용하도록 설정하려면 **Enable Remote Desktop** 확인란을 선택합니다. 원격 데스크톱을 사용하지 않도록 설정하려면 확인란을 선택 취소합니다.
   * 역할 인스턴스에 원격 데스크톱을 연결하는 데 사용할 계정을 만듭니다.
   * 기존 계정의 암호를 업데이트합니다.
   * 인증에 사용할 업데이트된 인증서를 선택(**인증서** 페이지에서 **업로드**를 사용하여 인증서 업로드)하거나 새 인증서를 만듭니다.
   * 원격 데스크톱 구성의 만료 날짜를 변경합니다.

5. 구성 업데이트를 마치면 **확인** (확인 표시)을 클릭합니다.

## <a name="remote-into-role-instances"></a>원격으로 역할 인스턴스 액세스
역할에 대해 원격 데스크톱이 사용되도록 설정되면 다양한 도구를 통해 역할 인스턴스에 원격으로 연결할 수 있습니다.

Azure 클래식 포털에서 역할 인스턴스에 연결하려면

1. **인스턴스**를 클릭하여 **인스턴스** 페이지를 엽니다.
2. 원격 데스크톱이 구성된 역할 인스턴스를 선택합니다.
3. **연결**을 클릭하고 지침에 따라 데스크톱을 엽니다.
4. **열기**를 클릭한 후 **연결**을 클릭하여 원격 데스크톱 연결을 시작합니다.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Visual Studio를 사용하여 역할 인스턴스에 원격으로 연결
Visual Studio의 서버 탐색기:

1. **Azure** > **Cloud Services** > **[클라우드 서비스 이름]** 노드를 확장합니다.
2. **스테이징** 또는 **프로덕션**을 확장합니다.
3. 개별 역할을 확장합니다.
4. 역할 인스턴스 중 하나를 마우스 오른쪽 단추로 클릭하고 **원격 데스크톱을 사용하여 연결...**을 클릭한 다음 사용자 이름 및 암호를 입력합니다.

![서버 탐색기 원격 데스크톱](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>PowerShell을 사용하여 RDP 파일 가져오기
[Get-azureremotedesktopfile](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet을 사용하면 RDP 파일을 검색할 수 있습니다. 그런 다음 원격 데스크톱 연결에서 RDP 파일을 사용하여 클라우드 서비스에 액세스할 수 있습니다.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>서비스 관리 REST API를 통해 프로그래밍 방식으로 RDP 파일 다운로드
[RDP 파일 다운로드](https://msdn.microsoft.com/library/jj157183.aspx) REST 작업을 사용하면 RDP 파일을 다운로드할 수 있습니다.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>서비스 정의 파일에서 원격 데스크톱을 구성하려면
이 방법을 사용하면 개발하는 동안 응용 프로그램에 대한 원격 데스크톱을 사용하도록 설정할 수 있습니다. 이 방법을 사용할 경우 암호화된 암호가 서비스 구성 파일에 저장되어야 하며 원격 데스크톱 구성을 업데이트하기 위해서는 응용 프로그램을 재배포해야 합니다. 이러한 단점이 방지하려면 위에서 설명하는 원격 데스크톱 확장 기반 접근 방법을 사용해야 합니다.  

Visual Studio를 사용하여 서비스 정의 파일 접근 방식을 통해 [원격 데스크톱 연결을 사용하도록 설정](../vs-azure-tools-remote-desktop-roles.md) 할 수 있습니다.  
아래 단계에서는 원격 데스크톱을 사용하도록 설정하기 위해 서비스 모델 파일에 필요한 변경 내용에 대해 설명합니다. 게시가 진행되면 Visual Studio에서 이러한 변경을 자동으로 수행합니다.

### <a name="set-up-the-connection-in-the-service-model"></a>서비스 모델에서 연결 설정
**Imports** 요소를 사용하여 **RemoteAccess** 모듈 및 **RemoteForwarder** 모듈을 [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) 파일로 가져옵니다.

서비스 정의 파일은 `<Imports>` 요소가 추가된 다음 예제와 유사해야 합니다.

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
[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) 파일은 `<ConfigurationSettings>` 및 `<Certificates>` 요소가 추가된 다음 예제와 유사해야 합니다. 지정된 인증서를 [클라우드 서비스에 업로드](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service)해야 합니다.

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


## <a name="additional-resources"></a>추가 리소스
[Cloud Services를 구성하는 방법](cloud-services-how-to-configure.md)
[Cloud Services FAQ - 원격 데스크톱](cloud-services-faq.md#remote-desktop)

