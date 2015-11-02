<properties 
	pageTitle="Azure 클라우드 서비스 - 서비스 정의 및 서비스 구성 - XML 인증서" 
	description="서비스 정의 및 구성 파일에서 인증서를 구성하는 방법에 대해 알아봅니다." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015"
	ms.author="adegeo"/>



# 인증서에 대한 서비스 정의 및 구성 파일 구성

사용자의 웹 또는 작업자 역할을 실행하는 가상 컴퓨터에는 그와 관련된 인증서를 둘 수 있습니다. 포털에 인증서를 업로드하고 나면 인증서에 대한 서비스 정의(.csdef) 파일과 서비스 구성(.cscfg) 파일을 구성해야 합니다.

설치 후에 가상 컴퓨터는 인증서의 개인 키에 액세스할 수 있습니다. 이 때문에 프로세스에 대한 액세스를 elevated(승격된) 권한으로 제한하는 것이 필요할 수 있습니다.

## 서비스 정의 예제

서비스 정의 파일에 정의된 인증서의 예입니다.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### 권한
권한(`permisionLevel` 특성)은 다음 중 하나로 설정될 수 있습니다.

| 권한 값 | 설명 |
| ----------------  | ----------- |
| limitedOrElevated | **(기본값)** 모든 역할 프로세스는 개인 키에 액세스할 수 있습니다. |
| elevated | 승격된 프로세스만 개인 키에 액세스할 수 있습니다.|

## 서비스 구성 예제

서비스 구성 파일에 정의된 인증서의 예입니다.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**참고** `name` 특성이 일치합니다.

## 다음 단계
[서비스 정의 XML](https://msdn.microsoft.com/library/azure/ee758711.aspx) 스키마와 [서비스 구성 XML](https://msdn.microsoft.com/library/azure/ee758710.aspx) 스키마를 검토합니다.

<!---HONumber=Oct15_HO4-->