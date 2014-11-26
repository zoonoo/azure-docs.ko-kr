<properties linkid="manage-linux-howto-service-management-api" urlDisplayName="Service Management API" pageTitle="How to use the service management API for VMs - Azure" metaKeywords="" description="Learn how to use the Azure Service Management API for a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use the Service Management API" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# 서비스 관리 API를 사용하는 방법

## 초기화

NodeJS에서 Azure IaaS 서비스 관리 API를 호출하려면 `azure` 모듈을 사용합니다.

    var azure = require('azure');

먼저 ServiceManagementService 인스턴스를 만듭니다. API에 대한 모든 호출이 이 개체를 사용합니다. 구독 ID, 자격 증명 및 기타 연결 옵션이 이때 설정됩니다. 두 개 이상의 구독을 관리하려면 두 개 이상의 ServiceManagementService를 만듭니다.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid는 필수 문자열입니다. 액세스할 계정의 구독 ID여야 합니다.
-   Auth는 이 계정에 사용할 개인 키 및 공용 인증서를 지정하는 선택적인 개체입니다.

    -   keyfile - 개인 키가 있는 .pem 파일의 파일 경로입니다. keyvalue가 지정된 경우 이 값은 무시됩니다.
    -   keyvalue - .pem 파일에 저장된 개인 키의 실제 값입니다.
    -   certfile - 공용 인증서가 있는 .pem 파일의 파일 경로입니다. cervalue가 지정된 경우 이 값은 무시됩니다.
    -   certvalue - .pem 파일에 저장된 공용 인증서의 실제 값입니다.
    -   위의 값이 지정되지 않은 경우에는 프로세스 환경 변수 값 `CLIENT_AUTH_KEYFILE` 및 `CLIENT_AUTH_CERTFILE`이 읽히고 사용됩니다. 이 값이 설정되지 않은 경우에는 파일의 기본값인 priv.pem 및 pub.pem이 사용됩니다.
    -   개인 키 및 공용 인증서를 로드할 수 없는 경우 오류가 발생합니다.
-   옵션은 클라이언트에서 사용되는 속성을 제어하는 데 사용할 수 있는 선택적인 개체입니다.

    -   host - Azure 관리 서버의 호스트 이름입니다. 지정되지 않은 경우 기본 호스트가 사용됩니다.
    -   apiversion - HTTP 헤더에서 사용할 버전 문자열입니다. 지정되지 않은 경우 기본 버전이 사용됩니다.
    -   serializetype - XML 또는 JSON일 수 있습니다. 지정되지 않은 경우 기본 serialization이 사용됩니다.

또는 터널링 프록시를 사용하여 HTTPS 요청이 프록시를 통과하도록 할 수 있습니다. IaasClient가 만들어지면 이 클라이언트가 환경 변수 `HTTPS_PROXY`를 처리합니다. 이를 유효한 URL로 설정하는 경우 호스트 이름 및 포트가 URL에서 구문 분석되고 프록시 식별을 위해 후속 요청에서 사용됩니다.

        iaasClient.SetProxyUrl(proxyurl)

SetProxyUrl을 호출하여 프록시 호스트 및 포트를 명시적으로 설정할 수 있습니다. 이 경우 `HTTPS_PROXY` 환경 변수를 설정하는 것과 동일한 효과가 있으며 환경 설정이 재정의됩니다.

## 콜백

모든 API에는 필수 콜백 인수가 있습니다. 요청 완료는 콜백에서 전달된 함수를 호출하여 신호됩니다.

    callback(rsp)

-   콜백에는 응답 개체인 단일 매개 변수가 있습니다.
-   isSuccessful - true 또는 false입니다.
-   statusCode - 응답의 HTTP 상태 코드입니다.
-   response - javascript 개체로 구문 분석된 응답입니다. isSuccessful이 true인 경우 설정됩니다.
-   error - 오류 정보가 포함된 javascript 개체입니다. isSuccessful이 false인 경우 설정됩니다.
-   body - 응답의 실제 본문 문자열입니다.
-   headers - 응답의 실제 HTTP 헤더입니다.
-   reqopts - 요청하는 데 사용되는 Node HTTP 요청 옵션입니다.

일부 경우 완료가 요청이 수락되었다는 사실만 나타내기도 합니다. 이 경우 최종 상태를 가져오려면 **GetOperationStatus**를 사용합니다.

## API

**iaasClient.GetOperationStatus(requested, callback)**

-   많은 관리 호출은 작업이 완료되기 전에 반환합니다. 이 호출은 202 Accepted 값을 반환하고 ms-request-id HTPP 헤더에 requested를 넣습니다. 요청의 완료를 폴링하려면 이 API를 사용하고 요청된 값을 전달합니다.
-   콜백이 필요합니다.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename은 이미지의 필수 문자열 이름입니다.
-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 명명된 이미지의 속성이 포함됩니다.

**iaasClient.ListOSImages(callback)**

-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 이미지 개체 배열이 포함됩니다.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName은 이미지의 필수 문자열 이름입니다.
-   mediaLink는 사용할 mediaLink의 필수 문자열 이름입니다.
-   imageOptions는 선택적인 개체입니다. 다음 속성이 포함될 수 있습니다.

    -   Category
    -   Label - 설정되지 않은 경우 기본적으로 imageName으로 설정됩니다.
    -   Location
    -   RoleSize
-   콜백이 필요합니다. (imageOptions가 설정되지 않은 경우 이 항목이 세 번째 매개 변수가 될 수 있습니다.)
-   성공하는 경우 응답 개체에 생성된 이미지의 속성이 포함됩니다.

**iaasClient.ListHostedServices(callback)**

-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 호스티드 서비스 개체 배열이 포함됩니다.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 호스티드 서비스의 속성이 포함됩니다.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   serviceOptions는 선택적인 개체입니다. 다음 속성이 포함될 수 있습니다.

    -   Description - 기본적으로 '서비스 호스트'로 설정됩니다.
    -   Label - 설정되지 않은 경우 기본적으로 serviceName으로 설정됩니다.
    -   위치 - 서비스를 만들 지역
-   콜백이 필요합니다.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 저장소 액세스 키가 포함됩니다.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 명명된 배포의 속성이 포함됩니다.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentSlot은 슬롯(스테이징 또는 프로덕션)의 필수 문자열 이름입니다.
-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 슬롯에 있는 배포의 속성이 포함됩니다.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   VMRole은 배포를 위해 생성될 역할의 속성이 있는 필수 개체입니다.
-   deployOptions는 선택적인 개체입니다. 다음 속성이 포함될 수 있습니다.

    -   DeploymentSlot - 기본적으로 '프로덕션'으로 설정됩니다.
    -   Label - 설정되지 않은 경우 기본적으로 deploymentName으로 설정됩니다.
    -   UpgradeDomainCount - 기본값이 없습니다.
-   콜백이 필요합니다.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleName은 역할의 필수 문자열 이름입니다.
-   콜백이 필요합니다.
-   성공하는 경우 응답 개체에 명명된 역할의 속성이 포함됩니다.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   VMRole은 배포에 추가될 역할의 속성이 있는 필수 개체입니다.
-   콜백이 필요합니다.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleName은 역할의 필수 문자열 이름입니다.
-   VMRole은 역할에서 수정할 속성이 있는 필수 개체입니다.
-   콜백이 필요합니다.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleName은 역할의 필수 문자열 이름입니다.
-   콜백이 필요합니다.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleName은 역할의 필수 문자열 이름입니다.
-   Datadisk는 데이터 디스크를 만드는 방식을 지정하는 데 사용하는 필수 개체입니다.
-   콜백이 필요합니다.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleName은 역할의 필수 문자열 이름입니다.
-   LUN은 데이터 디스크를 식별하는 번호입니다.
-   Datadisk는 데이터 디스크를 수정하는 방식을 지정하는 데 사용하는 필수 개체입니다.
-   콜백이 필요합니다.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleName은 역할의 필수 문자열 이름입니다.
-   LUN은 데이터 디스크를 식별하는 번호입니다.
-   콜백이 필요합니다.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleInstance는 역할 인스턴스의 필수 문자열 이름입니다.
-   콜백이 필요합니다.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleInstance는 역할 인스턴스의 필수 문자열 이름입니다.
-   콜백이 필요합니다.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName은 호스티드 서비스의 필수 문자열 이름입니다.
-   deploymentName은 배포의 필수 문자열 이름입니다.
-   roleInstance는 역할 인스턴스의 필수 문자열 이름입니다.
-   captOptions는 캡처 작업을 정의하는 필수 개체입니다.

    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   콜백이 필요합니다.

## 데이터 개체

API는 배포, 역할 또는 디스크를 만들거나 수정할 때 입력으로 개체를 가져옵니다. 다른 API는 가져오기 또는 목록 작업에서 비슷한 개체를 반환합니다.
이 섹션에서는 개체 속성을 나열합니다.
배포

-   Name - 문자열
-   DeploymentSlot - '스테이징' 또는 '프로덕션'
-   Status - 문자열 - 출력만
-   PrivateID - 문자열 - 출력만
-   Label - 문자열
-   UpgradeDomainCount - 숫자
-   SdkVersion - 문자열
-   Locked - true 또는 false
-   RollbackAllowed - true 또는 false
-   RoleInstance - 개체 - 출력만
-   Role - VMRole 개체
-   InputEndpointList - InputEndpoint 배열

**VMRole**

-   RoleName - 문자열 만들기에 필요합니다.
-   RoleSize - 문자열 만들기에 사용할 수 있습니다.
-   RoleType - 문자열 만들기에서 지정되지 않은 경우 기본적으로 'PersistentVMRole'로 설정됩니다.
-   OSDisk - 개체 만들기에 필요합니다.
-   DataDisks - 개체 배열 만들기에 사용할 수 있습니다.
-   ConfigurationSets - 구성 개체 배열

**RoleInstance**

-   RoleName - 문자열
-   InstanceName - 문자열
-   InstanceStatus - 문자열
-   InstanceUpgradeDomain - 숫자
-   InstanceFaultDomain - 숫자
-   InstanceSize - 문자열
-   IpAddress - 문자열

**OSDisk**

-   SourceImageName - 문자열 만들기에 필요합니다.
-   DisableWriteCache - true 또는 false
-   DiskName - 문자열
-   MediaLink - 문자열

**DataDisk**

-   DisableReadCache - true 또는 false
-   EnableWriteCache - true 또는 false
-   DiskName - 문자열
-   MediaLink - 문자열
-   LUN - 숫자(0-15)
-   LogicalDiskSizeInGB - 숫자
-   SourceMediaLink - 문자열
-   만들기 과정에서 디스크를 지정하는 3가지 방법, 즉 이름 기준, 미디어 기준, 크기 기준이 있습니다. 지정 옵션에 따라 작동 방식이 결정됩니다. 자세한 내용은 RDFE API 문서를 참조하십시오.

**ProvisioningConfiguration**

-   ConfigurationSetType - 'ProvisioningConfiguration'
-   AdminPassword - 문자열
-   MachineName - 문자열
-   ResetPasswordOnFirstLogon - true 또는 false

**NetworkConfiguration**

-   ConfigurationSetType - 'NetworkConfiguration'
-   InputEndpoints - ExternalEndpoints 배열

**InputEndpoint**

-   RoleName
-   Vip
-   포트

**ExternalEndpoint**

-   LocalPort
-   이름
-   포트
-   프로토콜

## 샘플 코드

다음은 호스티드 서비스 및 배포를 만든 후 배포의 완료 상태를 폴링하는 샘플 javascript 코드입니다.

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
