<properties urlDisplayName="Service Management" pageTitle="Azure 서비스 관리 API 사용 방법(PHP)" metaKeywords="" description="Azure PHP 서비스 관리 API를 사용하여 클라우드 서비스 및 기타 Azure 응용 프로그램을 관리하는 방법에 대해 알아봅니다." metaCanonical="" services="" documentationCenter="PHP" title="PHP에서 서비스 관리를 사용하는 방법" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# PHP에서 서비스 관리를 사용하는 방법

이 가이드에서는 PHP에서 프로그래밍 방식으로 일반 서비스 관리 작업을 수행하는 방법을 보여 줍니다. [PHP용 Azure SDK][PHP용 Azure SDK]의 [ServiceManagementRestProxy][ServiceManagementRestProxy] 클래스는 [관리 포털][관리 포털]에서 사용할 수 있는 대부분의 서비스 관리 관련 기능(예: **클라우드 서비스, 배포, 저장소 서비스, 선호도 그룹 만들기, 업데이트 및 삭제**)에 대해 프로그래밍 방식의 액세스를 지원합니다. 이 기능은 서비스 관리에 프로그래밍 방식으로 액세스해야 하는 응용 프로그램을 빌드하는 데 유용할 수 있습니다.

## 목차

-   [서비스 관리][서비스 관리]
-   [개념][개념]
-   [PHP 응용 프로그램 만들기][PHP 응용 프로그램 만들기]
-   [Azure 클라이언트 라이브러리 가져오기][Azure 클라이언트 라이브러리 가져오기]
-   [방법: 서비스 관리에 연결][방법: 서비스 관리에 연결]
-   [방법: 사용 가능한 위치 나열][방법: 사용 가능한 위치 나열]
-   [방법: 클라우드 서비스 만들기][방법: 클라우드 서비스 만들기]
-   [방법: 클라우드 서비스 삭제][방법: 클라우드 서비스 삭제]
-   [방법: 배포 만들기][방법: 배포 만들기]
-   [방법: 배포 업데이트][방법: 배포 업데이트]
-   [방법: 스테이징과 프로덕션 간의 배포 이동][방법: 스테이징과 프로덕션 간의 배포 이동]
-   [방법: 배포 삭제][방법: 배포 삭제]
-   [방법: 저장소 서비스 만들기][방법: 저장소 서비스 만들기]
-   [방법: 저장소 서비스 삭제][방법: 저장소 서비스 삭제]
-   [방법: 선호도 그룹 만들기][방법: 선호도 그룹 만들기]
-   [방법: 선호도 그룹 삭제][방법: 선호도 그룹 삭제]

## <span id="WhatIs"></span></a>서비스 관리

서비스 관리 API는 [관리 포털][관리 포털]을 통해 사용할 수 있는 대부분의 서비스 관리 기능에 대해 프로그래밍 방식의 액세스를 제공합니다. PHP용 Azure SDK를 사용하여 클라우드 서비스, 저장소 계정 및 선호도 그룹을 관리할 수 있습니다.

서비스 관리 API를 사용하려면 [Azure 계정을 만들어야][Azure 계정을 만들어야] 합니다.

## <span id="Concepts"></span></a>개념

PHP용 Azure SDK는 REST API인 [Azure 서비스 관리 API][Azure 서비스 관리 API]를 래핑합니다. 모든 API 작업은 SSL을 통해 수행되고 X.509 v3 인증서를 사용하여 서로 인증됩니다. 관리 서비스는 Azure에서 실행 중인 서비스 내에서 액세스할 수 있거나, HTTPS 요청을 보내고 HTTPS 응답을 받을 수 있는 응용 프로그램에서 인터넷을 통해 직접 액세스할 수 있습니다.

## <span id="CreateApplication"></span></a>PHP 응용 프로그램 만들기

Azure 서비스 관리를 사용하는 PHP 응용 프로그램을 만드는 데 유일한 요구 사항은 코드 내에서 PHP용 Azure SDK의 클래스를 참조하는 것입니다. 응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 서비스 기능을 사용합니다.

## <span id="GetClientLibraries"></span></a>Azure 클라이언트 라이브러리 가져오기

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="Connect"></span></a>방법: 서비스 관리에 연결

서비스 관리 끝점에 연결하려면 Azure 구독 ID 및 유효한 관리 인증서 경로가 있어야 합니다. [관리 포털][관리 포털]을 통해 구독 ID를 얻을 수 있으며 다양한 방법으로 관리 인증서를 만들 수 있습니다. 이 가이드에서는 [Windows용으로 다운로드][Windows용으로 다운로드]하고 콘솔에서 실행할 수 있는 [OpenSSL][OpenSSL]이 사용됩니다.

실제로 서버용(`.cer` 파일)과 클라이언트용(`.pem` 파일)으로 두 개의 인증서를 만들어야 합니다. `.pem` 파일을 만들려면 다음을 실행합니다.

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

`.cer` 인증서를 만들려면 다음을 실행합니다.

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Azure 인증서에 대한 자세한 내용은 [Azure의 인증서 개요][Azure의 인증서 개요]를 참조하세요. OpenSSL 매개 변수에 대한 자세한 설명은 <http://www.openssl.org/docs/apps/openssl.html>(영문)의 자료를 참조하세요.

[Azure 명령줄 도구][Azure 명령줄 도구]를 사용하여 게시 설정 파일을 다운로드하고 가져온 경우 고유한 파일을 만드는 대신 도구에서 만든 `.pem` 파일을 사용할 수 있습니다. 도구에서 자동으로 `.cer`을 만들고 Azure에 업로드하며, 컴퓨터의 사용자 디렉터리에 있는 `.azure` 디렉터리에 해당 `.pem` 파일을 저장합니다.

이러한 파일을 만든 후에는 [관리 포털][관리 포털]을 통해 `.cer` 파일을 Azure에 업로드해야 하고, `.pem` 파일을 저장한 위치를 기록해 두어야 합니다.

구독 ID를 얻어 인증서를 만들고 `.cer` 파일을 Azure에 업로드하고 나면 연결 문자열을 만들고 **ServicesBuilder** 클래스의 **createServiceManagementService** 메서드에 전달하여 Azure 관리 끝점에 연결할 수 있습니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

위 예제에서 `$serviceManagementRestProxy`는 [ServiceManagementRestProxy][ServiceManagementRestProxy] 개체입니다. **ServiceManagementRestProxy** 클래스는 Azure 서비스를 관리하는 데 사용되는 주 클래스입니다.

## <span id="ListAvailableLocations"></span></a>방법: 사용 가능한 위치 나열

서비스를 호스트하는 데 사용할 수 있는 위치를 나열하려면 **ServiceManagementRestProxy-\>listLocations** 메서드를 사용합니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $result = $serviceManagementRestProxy->listLocations();

        $locations = $result->getLocations();

        foreach($locations as $location){
              echo $location->getName()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

클라우드 서비스, 저장소 서비스 또는 선호도 그룹을 만드는 경우 유효한 위치를 제공해야 합니다. **listLocations** 메서드는 항상 현재 사용 가능한 위치의 최신 목록을 반환합니다. 현재 사용 가능한 위치는 다음과 같습니다.

-   미국 전역
-   유럽 전역
-   서유럽
-   아시아 전역
-   동남아시아
-   동아시아
-   미국 중북부
-   북유럽
-   미국 중남부
-   미국 서부
-   미국 동부

> [WACOM.NOTE]
> 뒤에 나오는 코드 예제에서는 위치가 문자열로 메서드에 전달됩니다. 그러나 `WindowsAzure\ServiceManagement\Models\Locations` 클래스를 사용하여 위치를 열거로 전달할 수도 있습니다. 예를 들어 위치를 수락하는 메서드에 "West US"를 전달하는 대신 `Locations::WEST_US`를 전달할 수 있습니다.

## <span id="CreateCloudService"></span></a>방법: 클라우드 서비스 만들기

응용 프로그램을 만들어 Azure에서 실행하면 코드와 구성은 모두 Azure [클라우드 서비스][클라우드 서비스](이전 Azure 릴리스에서는 *호스팅 서비스*라 함)라고 합니다. **createHostedServices** 메서드를 통해 호스팅 서비스 이름(Azure에서 고유해야 함), 레이블(base64로 인코딩된 호스팅 서비스 이름) 및 **CreateServiceOptions** 개체를 제공하여 새 호스팅 서비스를 만들 수 있습니다. [CreateServiceOptions][CreateServiceOptions] 개체를 사용하여 위치 *또는* 서비스의 선호도 그룹을 설정할 수 있습니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Instead of setLocation, you can use setAffinityGroup
        // to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

[ListHostedServicesResult][ListHostedServicesResult] 개체를 반환하는 **listHostedServices** 메서드로 구독의 모든 호스팅 서비스를 나열할 수 있습니다. 그런 다음 **getHostedServices** 메서드를 호출하여 [HostedServices] 개체 배열을 반복하고 서비스 속성을 검색할 수 있습니다.

    $listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

    $hosted_services = $listHostedServicesResult->getHostedServices();

    foreach($hosted_services as $hosted_service){
        echo "Service name: ".$hosted_service->getName()."<br />";
        echo "Management URL: ".$hosted_service->getUrl()."<br />";
        echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
        echo "Location: ".$hosted_service->getLocation()."<br />";
        echo "------<br />";
        }

특정 호스팅 서비스에 대한 정보를 가져오려는 경우 호스팅 서비스 이름을 **getHostedServiceProperties** 메서드에 전달하면 됩니다.

    $getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
        
    $hosted_service = $getHostedServicePropertiesResult->getHostedService();
        
    echo "Service name: ".$hosted_service->getName()."<br />";
    echo "Management URL: ".$hosted_service->getUrl()."<br />";
    echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
    echo "Location: ".$hosted_service->getLocation()."<br />";

클라우드 서비스를 만든 후 [createDeployment][방법: 배포 만들기] 메서드를 사용하여 코드를 서비스에 배포할 수 있습니다.

## <span id="DeleteCloudService"></span></a>방법: 클라우드 서비스 삭제

서비스 이름을 **deleteHostedService** 메서드에 전달하여 클라우드 서비스를 삭제할 수 있습니다.

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

서비스의 모든 배포를 먼저 삭제해야 서비스를 삭제할 수 있습니다. (자세한 내용은 [방법: 배포 삭제][방법: 배포 삭제] 참조)

## <span id="CreateDeployment"></span></a>방법: 배포 만들기

**createDeployment** 메서드는 새 [서비스 패키지][서비스 패키지]를 업로드하고 스테이징 또는 프로덕션 환경에서 새 배포를 만듭니다. 이 메서드의 매개 변수는 다음과 같습니다.

-   **$name**: 호스팅 서비스의 이름입니다.
-   **$deploymentName**: 배포 이름입니다.
-   **$slot**: 스테이징 또는 프로덕션 슬롯을 나타내는 열거입니다.
-   **$packageUrl**: 배포 패키지(.cspgk 파일)의 URL입니다. 패키지 파일은 패키지가 업로드 중인 호스팅 서비스와 동일한 구독의 Azure Blob 저장소 계정에 저장되어야 합니다. [Azure PowerShell cmdlet][Azure PowerShell cmdlet] 또는 [cspack 명령줄 도구][cspack 명령줄 도구]로 배포 패키지를 만들 수 있습니다.
-   **$configuration**: 서비스 구성 파일(.cscfg 파일)입니다.
-   **$label**: base64로 인코딩된 호스팅 서비스 이름입니다.

다음 예제는 `myhostedservice`라는 호스팅된 서비스의 프로덕션 슬롯에 새 배포를 만듭니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
        $packageUrl = "URL_for_.cspkg_file";
        $configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
        $label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
                                                         $deploymentName,
                                                         $slot,
                                                         $packageUrl,
                                                         $configuration,
                                                         $label);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

위 예제에서 **createDeployment** 작업 상태는 **createDeployment**가 반환한 결과를 **getOperationStatus** 메서드에 전달하여 검색할 수 있습니다.

**getDeployment** 메서드로 배포 속성에 액세스할 수 있습니다. 다음 예제는 [GetDeploymentOptions][ListHostedServicesResult] 개체에 배포 슬롯을 지정하여 배포를 검색하지만, 배포 이름을 대신 지정할 수 있습니다. 또한 이 예제는 배포의 모든 인스턴스를 반복합니다.

    $options = new GetDeploymentOptions();
    $options->setSlot(DeploymentSlot::PRODUCTION);
        
    $getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
    $deployment = $getDeploymentResult->getDeployment();

    echo "Name: ".$deployment->getName()."<br />";
    echo "Slot: ".$deployment->getSlot()."<br />";
    echo "Private ID: ".$deployment->getPrivateId()."<br />";
    echo "Status: ".$deployment->getStatus()."<br />";
    echo "Instances: <br />";
    foreach($deployment->getroleInstanceList() as $roleInstance){
        echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
        echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
        echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
    }
    echo "------<br />";

## <span id="UpdateDeployment"></span></a>방법: 배포 업데이트

**changeDeploymentConfiguration** 메서드 또는 **updateDeploymentStatus** 메서드를 사용하여 배포를 업데이트할 수 있습니다.

**changeDeploymentConfiguration** 메서드를 통해 새 서비스 구성(`.cscfg`) 파일을 업로드할 수 있으며 이로 인해 일부 서비스 설정(배포의 인스턴스 수 포함)이 변경됩니다. 자세한 내용은 [Azure 서비스 구성 스키마(.cscfg)][Azure 서비스 구성 스키마(.cscfg)]를 참조하세요. 다음 예제는 새 서비스 구성 파일을 업로드하는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $configuration = base64_encode(file_get_contents('path to .cscfg file'));
        $options = new ChangeDeploymentConfigurationOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

위 예제에서 **changeDeploymentConfiguration** 작업 상태는 **changeDeploymentConfiguration**이 반환한 결과를 **get\_operation\_status** 메서드에 전달하여 검색할 수 있습니다.

**updateDeploymentStatus** 메서드를 통해 배포 상태를 RUNNING 또는 SUSPENDED로 설정할 수 있습니다. 다음 예제는 `myhostedservice`라는 호스팅된 서비스의 프로덕션 슬롯에서 배포의 상태를 RUNNING으로 설정하는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="MoveDeployments"></span></a>방법: 스테이징과 프로덕션 간의 배포 이동

Azure에서는 두 가지 배포 환경(스테이징 및 프로덕션)을 제공합니다. 일반적으로 서비스는 스테이징 환경에 배포되어 서비스를 프로덕션 환경에 배포하기 전에 테스트합니다. 서비스를 스테이징 환경에서 프로덕션 환경으로 수준을 올릴 때 해당 서비스를 다시 배포하지 않고 수준을 올릴 수 있습니다. 배포를 교환하여 수준을 올릴 수 있습니다. 배포 교환에 대한 자세한 내용은 [Azure의 배포 관리 개요][Azure의 배포 관리 개요]를 참조하세요.

다음 예제는 **swapDeployment** 메서드를 사용하여 두 배포(이름이 `v1`과 `v2`인 배포)를 교환하는 방법을 보여 줍니다. 예제에서는 **swapDeployment**를 호출하기 전 배포 `v1`은 프로덕션 슬롯에 있고 배포 `v2`는 스테이징 슬롯에 있습니다. **swapDeployment**를 호출한 후 `v2`는 프로덕션에 있고 `v1`은 스테이징에 있습니다.

    require_once 'vendor\autoload.php'; 

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteDeployment"></span></a>방법: 배포 삭제

배포를 삭제하려면 **deleteDeployment** 메서드를 사용합니다. 다음 예제는 [GetDeploymentOptions][ListHostedServicesResult] 개체의 **setSlot** 메서드를 사용하고 **deleteDeployment**에 전달하여 스테이징 환경에서 배포를 삭제하는 방법을 보여 줍니다. 슬롯으로 배포를 지정하는 대신 [GetDepolymentOptions] 클래스의 **setName** 메서드를 사용하여 배포 이름으로 배포를 지정할 수 있습니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateStorageService"></span></a>방법: 저장소 서비스 만들기

[저장소 서비스][저장소 서비스]로 Azure [Blob][Blob], [테이블][테이블], [큐][큐]에 액세스할 수 있습니다. 저장소 서비스를 만들려면 서비스 이름(3자에서 24자 사이의 소문자로서 Azure 내에서 고유해야 함), 레이블(base64로 인코딩된 서비스 이름으로 최대 100자까지 가능) 및 위치 또는 선호도 그룹이 필요합니다. 선택적으로 서비스에 대한 설명을 제공할 수 있습니다. 위치, 선호도 그룹 및 설명은 **createStorageService** 메서드에 전달되는 [CreateServiceOptions][CreateServiceOptions] 개체에서 설정됩니다. 다음 예제에서는 위치를 지정하여 저장소 서비스를 만드는 방법을 보여 줍니다. 선호도 그룹을 사용하려면 먼저 선호도 그룹을 만들고([방법: 선호도 그룹 만들기][방법: 선호도 그룹 만들기]) **CreateServiceOptions-\>setAffinityGroup** 메서드를 통해 설정합니다.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        $options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

위 예제에서 **createStorageService** 작업 상태는 **createStorageService**가 반환한 결과를 **get\_operation\_status** 메서드에 전달하여 검색할 수 있습니다.

**listStorageServices** 메서드로 저장소 계정 및 해당 속성을 나열할 수 있습니다.

    // Create REST proxy.
    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
    $getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
    $storageServices = $getStorageServicesResult->getStorageServices();

    foreach($storageServices as $storageService){
        echo "Service name: ".$storageService->getName()."<br />";
        echo "Service URL: ".$storageService->getUrl()."<br />";
        echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
        echo "Location: ".$storageService->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteStorageService"></span></a>방법: 저장소 서비스 삭제

저장소 서비스 이름을 **deleteStorageService** 메서드에 전달하여 저장소 서비스를 삭제할 수 있습니다. 저장소 서비스를 삭제하면 그 서비스에 저장되어 있는 모든 데이터(Blob, 테이블, 큐)가 삭제됩니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateAffinityGroup"></span></a>방법: 선호도 그룹 만들기

선호도 그룹은 최적화된 성능의 서비스를 찾도록 Azure에게 지시하는 Azure 서비스의 논리적 그룹화입니다. 예를 들어 "미국 서부" 위치에 선호도 그룹을 만든 다음 해당 선호도 그룹에 [클라우드 서비스][방법: 클라우드 서비스 만들기]를 만들 수 있습니다. 그런 다음 동일한 선호도 그룹에 저장소 서비스를 만들면 Azure는 해당 서비스를 "미국 서부" 위치에 놓고 최상의 성능을 위해 동일한 선호도 그룹의 클라우드 서비스로 데이터 센터 내에서 최적화합니다.

선호도 그룹을 만들려면 이름, 레이블(base64로 인코딩된 이름) 및 위치가 필요합니다. 설명은 선택적으로 제공할 수 있습니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

선호도 그룹을 만들고 나면 [저장소 서비스를 만들 때][방법: 저장소 서비스 만들기] 위치 대신 만든 선호도 그룹을 지정할 수 있습니다.

**listAffinityGroups** 메서드를 호출한 다음 [AffinityGroup][AffinityGroup] 클래스의 해당 메서드를 호출하여 선호도 그룹을 나열하고 해당 속성을 검사할 수 있습니다.

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteAffinityGroup"></span></a>방법: 선호도 그룹 삭제

그룹 이름을 **deleteAffinityGroup** 메서드에 전달하여 선호도 그룹을 삭제할 수 있습니다. 선호도 그룹은 서비스(또는 선호도 그룹을 삭제해야 하는 데 사용하는 서비스)와의 연결을 끊어야만 삭제할 수 있습니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // An affinity group must be disassociated from all services 
        // before it can be deleted.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

  [PHP용 Azure SDK]: ../php-download-sdk/
  [ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
  [관리 포털]: https://manage.windowsazure.com/
  [서비스 관리]: #WhatIs
  [개념]: #Concepts
  [PHP 응용 프로그램 만들기]: #CreateApplication
  [Azure 클라이언트 라이브러리 가져오기]: #GetClientLibraries
  [방법: 서비스 관리에 연결]: #Connect
  [방법: 사용 가능한 위치 나열]: #ListAvailableLocations
  [방법: 클라우드 서비스 만들기]: #CreateCloudService
  [방법: 클라우드 서비스 삭제]: #DeleteCloudService
  [방법: 배포 만들기]: #CreateDeployment
  [방법: 배포 업데이트]: #UpdateDeployment
  [방법: 스테이징과 프로덕션 간의 배포 이동]: #MoveDeployments
  [방법: 배포 삭제]: #DeleteDeployment
  [방법: 저장소 서비스 만들기]: #CreateStorageService
  [방법: 저장소 서비스 삭제]: #DeleteStorageService
  [방법: 선호도 그룹 만들기]: #CreateAffinityGroup
  [방법: 선호도 그룹 삭제]: #DeleteAffinityGroup
  [Azure 계정을 만들어야]: /ko-kr/pricing/free-trial/
  [Azure 서비스 관리 API]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460799.aspx
  [Windows용으로 다운로드]: http://www.openssl.org/related/binaries.html
  [OpenSSL]: http://www.openssl.org/
  [Azure의 인증서 개요]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg981935.aspx
  [Azure 명령줄 도구]: ../command-line-tools/
  [클라우드 서비스]: ../cloud-services-what-is/
  [CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
  [ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
  [서비스 패키지]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433093
  [Azure PowerShell cmdlet]: ../install-configure-powershell/
  [cspack 명령줄 도구]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg432988.aspx
  [Azure 서비스 구성 스키마(.cscfg)]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee758710.aspx
  [Azure의 배포 관리 개요]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh386336.aspx
  [저장소 서비스]: ../storage-whatis-account/
  [Blob]: ../storage-php-how-to-use-blobs/
  [테이블]: ../storage-php-how-to-use-table-storage/
  [큐]: ../storage-php-how-to-use-queues/
  [AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php
