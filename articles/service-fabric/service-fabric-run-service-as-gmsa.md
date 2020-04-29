---
title: GMSA 계정으로 Azure Service Fabric 서비스를 실행 합니다.
description: Service Fabric Windows 독립 실행형 클러스터에서 서비스를 gMSA (그룹 관리 서비스 계정)로 실행 하는 방법에 대해 알아봅니다.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76988399"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>그룹 관리 서비스 계정으로 서비스 실행

Windows Server 독립 실행형 클러스터에서 *RunAs* 정책을 사용 하 여 gMSA ( *그룹 관리 서비스 계정* )로 서비스를 실행할 수 있습니다.  기본적으로 Service Fabric 응용 프로그램은 `Fabric.exe` 프로세스가 실행 되는 계정에서 실행 됩니다. 서로 다른 계정에서 애플리케이션을 실행하면 공유 호스트 환경에서도 서로를 더욱 안전하게 보호할 수 있습니다. gMSA를 사용하면 애플리케이션 매니페스트에 저장되는 암호나 암호화된 암호가 없습니다.  [Active Directory 사용자 또는 그룹](service-fabric-run-service-as-ad-user-or-group.md)으로 서비스를 실행할 수도 있습니다.

다음 예제에서는 *.svc-Test $* 라는 gMSA 계정을 만드는 방법, 클러스터 노드에 관리 서비스 계정을 배포 하는 방법 및 사용자 보안 주체를 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> 독립 실행형 Service Fabric 클러스터와 함께 gMSA를 사용 하려면 Azure Active Directory (Azure AD)가 아닌 도메인 내에서 온-프레미스 Active Directory 해야 합니다.

필수 조건:

- 도메인에 KDS 루트 키가 필요합니다.
- 도메인에 Windows Server 2012 (또는 R2) DC가 하나 이상 있어야 합니다.

1. Active Directory 도메인 관리자가 cmdlet을 `New-ADServiceAccount` 사용 하 여 그룹 관리 서비스 계정을 만들고에 `PrincipalsAllowedToRetrieveManagedPassword` 모든 Service Fabric 클러스터 노드가 포함 되어 있는지 확인 합니다. `AccountName`, `DnsHostName` 및 `ServicePrincipalName`은 고유해야 합니다.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. 각 Service Fabric 클러스터 노드(예: `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`)에서 gMSA를 설치하고 테스트합니다.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. 사용자 보안 주체를 구성 하 고 `RunAsPolicy` [사용자](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas)를 참조 하도록를 구성 합니다.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> 서비스에 RunAs 정책을 적용하고 서비스 매니페스트가 HTTP 프로토콜로 엔드포인트 리소스를 선언하면 **SecurityAccessPolicy**를 지정해야 합니다.  자세한 내용은 [HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당](service-fabric-assign-policy-to-endpoint.md)을 참조하세요.
>

다음 문서에서는 다음 단계를 안내 합니다.

- [애플리케이션 모델의 이해](service-fabric-application-model.md)
- [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
- [응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
