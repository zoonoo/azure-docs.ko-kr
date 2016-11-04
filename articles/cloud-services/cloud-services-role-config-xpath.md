---
title: 클라우드 서비스 역할 구성 XPath 참고 자료 | Microsoft Docs
description: 클라우드 서비스 역할 구성에서 설정을 환경 변수로 노출하기 위해 사용할 수 있는 다양한 XPath 설정입니다.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: adegeo

---
# XPath를 사용하여 역할 구성 설정을 환경 변수로 노출
클라우드 서비스 작업자 또는 웹 역할 서비스 정의 파일에서 런타임 구성 값을 환경 변수로 노출할 수 있습니다. 다음 XPath 값(API 값에 해당)을 사용할 수 있습니다.

이러한 XPath 값을 [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) 라이브러리를 통해서도 사용할 수 있습니다.

## 앱이 에뮬레이터에서 실행 중임
앱이 에뮬레이터에서 실행 중임을 나타냅니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| 코드 |var x = RoleEnvironment.IsEmulated; |

## 배포 ID
인스턴스의 배포 ID를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| 코드 |var deploymentId = RoleEnvironment.DeploymentId; |

## 역할 ID
인스턴스의 현재 역할 ID를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| 코드 |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## 도메인 업데이트
인스턴스의 업데이트 도메인을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| 코드 |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## 장애 도메인
인스턴스의 장애 도메인을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| 코드 |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## 역할 이름
인스턴스의 역할 이름을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| 코드 |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## 구성 설정
지정된 구성 설정의 값을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| 코드 |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## 로컬 저장소 경로
인스턴스에 대한 로컬 저장소 경로를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| 코드 |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## 로컬 저장소 크기
인스턴스의 로컬 저장소 크기를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| 코드 |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## 끝점 프로토콜
인스턴스의 끝점 프로토콜을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| 코드 |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## 끝점 IP
지정된 끝점의 IP 주소를 가져옵니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| 코드 |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## 끝점 포트
인스턴스의 끝점 포트를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| 코드 |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## 예
다음은 [@emulated xpath 값](#app-running-in-emulator)으로 설정된 환경 변수 `TestIsEmulated`를 사용하여 시작 작업을 만드는 작업자 역할의 예입니다.

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## 다음 단계
[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 파일에 대해 자세히 알아봅니다.

[ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) 패키지를 만듭니다.

역할에 대해 [원격 데스크톱](cloud-services-role-enable-remote-desktop.md)을 사용하도록 설정합니다.

<!---HONumber=AcomDC_0810_2016-->