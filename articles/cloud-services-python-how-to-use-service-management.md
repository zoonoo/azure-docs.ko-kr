<properties 
	pageTitle="서비스 관리 API를 사용하는 방법(Python) - 기능 가이드" 
	description="Python에서 프로그래밍 방식으로 일반 서비스 관리 작업을 수행하는 방법에 대해 알아봅니다." 
	services="cloud-services" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>




# Python에서 서비스 관리를 사용하는 방법

이 가이드에서는 Python에서 프로그래밍 방식으로 일반 서비스 관리 작업을 수행하는 방법을 보여 줍니다. [Python용 Azure SDK][download-SDK-Python]의 **ServiceManagementService** 클래스는 [관리 포털][management-portal]에서 사용할 수 있는 대부분의 서비스 관리 관련 기능에 대해 프로그래밍 방식의 액세스를 지원합니다(예: **클라우드 서비스, 배포, 데이터 관리 서비스, 가상 컴퓨터, 선호도 그룹을 만들기, 업데이트 및 삭제**). 이 기능은 서비스 관리에 프로그래밍 방식으로 액세스해야 하는 응용 프로그램을 빌드하는 데 유용할 수 있습니다. 

## 목차

* [서비스 관리][]
* [개념][]
* [방법: 서비스 관리에 연결][]
* [방법: 사용 가능한 위치 나열][]
* [방법: 클라우드 서비스 만들기][]
* [방법: 클라우드 서비스 삭제][]
* [방법: 배포 만들기][]
* [방법: 배포 업데이트][]
* [방법: 스테이징과 프로덕션 간의 배포 이동][]
* [방법: 배포 삭제][]
* [방법: 저장소 서비스 만들기][]
* [방법: 저장소 서비스 삭제][]
* [방법: 선호도 그룹 만들기][]
* [방법: 선호도 그룹 삭제][]
* [방법: 사용 가능한 운영 체제 나열][]
* [방법: 운영 체제 이미지 만들기][]
* [방법: 운영 체제 이미지 삭제][]
* [방법: 가상 컴퓨터 만들기][]
* [방법: 가상 컴퓨터 삭제][]
* [다음 단계][]

## <a name="WhatIs"> </a>서비스 관리
서비스 관리 API는 [관리 포털][management-portal]을 통해 사용할 수 있는 대부분의 서비스 관리 기능에 대해 프로그래밍 방식의 액세스를 제공합니다. Python용 Azure SDK를 사용하여 클라우드 서비스, 저장소 계정 및 선호도 그룹을 관리할 수 있습니다.

서비스 관리 API를 사용하려면 [Azure 계정을 만들어야 합니다](http://azure.microsoft.com/pricing/free-trial/). 

## <a name="Concepts"> </a>개념
Python용 Azure SDK는 REST API인 [Azure 서비스 관리 API][svc-mgmt-rest-api]를 래핑합니다. 모든 API 작업은 SSL을 통해 수행되고 X.509 v3 인증서를 사용하여 서로 인증됩니다. 관리 서비스는 Azure에서 실행 중인 서비스 내에서 액세스할 수 있거나, HTTPS 요청을 보내고 HTTPS 응답을 받을 수 있는 응용 프로그램에서 인터넷을 통해 직접 액세스할 수 있습니다.

## <a name="Connect"> </a>방법: 서비스 관리에 연결
서비스 관리 끝점에 연결하려면 Azure 구독 ID 및 유효한 관리 인증서가 있어야 합니다. [관리 포털][management-portal]을 통해 구독 ID를 얻을 수 있습니다.

> [AZURE.NOTE] Python용 Azure SDK v0.8.0부터 이제 Windows에서 실행할 때 OpenSSL로 만든 인증서를 사용할 수 있습니다.  여기에는 Python 2.7.4 이상이 필요합니다.

### Windows의 관리 인증서(MakeCert)

 `makecert.exe`를 사용하여 컴퓨터에 자체 서명된 관리 인증서를 만들 수 있습니다.  **Visual Studio 명령 프롬프트**를 **관리자**로 열고  *AzureCertificate*를 사용할 인증서 이름으로 바꿔서 다음 명령을 사용합니다.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

이 명령은 `.cer` 파일을 만들고 만든 파일을 **개인** 인증서 저장소에 설치합니다. 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)를 참조하세요.

인증서를 만든 후에는 [관리 포털][management-portal]에서 "설정" 탭의 "업로드" 동작을 통해 `.cer` 파일을 Azure에 업로드해야 합니다.

구독 ID를 얻어 인증서를 만들고 `.cer` 파일을 Azure에 업로드하고 나면 구독 ID 및 **개인** 인증서 저장소에 있는 인증서의 위치를 **ServiceManagementService**에 전달하여(다시 말해, *AzureCertificate*를 인증서 이름으로 바꿈) Azure 관리 끝점에 연결할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

위 예제에서, `sms`는 **ServiceManagementService** 개체입니다. **ServiceManagementService** 클래스는 Azure 서비스를 관리하는 데 사용되는 주 클래스입니다. 

### Windows/Mac/Linux의 관리 인증서(OpenSSL)
[OpenSSL](http://www.openssl.org/)을 사용하여 관리 인증서를 만들 수 있습니다.  실제로 서버용(`.cer` 파일)과 클라이언트용(`.pem` 파일)으로 두 개의 인증서를 만들어야 합니다. 이 `.pem` 파일을 만들려면 다음을 실행합니다.

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

이 `.cer` 인증서를 만들려면 다음을 실행합니다.

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Azure 인증서에 대한 자세한 내용은 [Azure에서 인증서 관리](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx)를 참조하세요. OpenSSL 매개 변수에 대한 자세한 설명은 [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html)(영문)의 자료를 참조하세요.

이러한 파일을 만든 후에는 [관리 포털][management-portal]에서 "설정" 탭의 "업로드" 동작을 통해 `.cer` 파일을 Azure에 업로드해야 하고, `.pem` 파일을 저장한 위치를 기록해 두어야 합니다.

구독 ID를 얻어 인증서를 만들고, `.cer` 파일을 Azure에 업로드하고 나면 구독 ID 및 `.pem` 파일의 경로를 **ServiceManagementService**에 전달하여 Azure 관리 끝점에 연결할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'
	
	sms = ServiceManagementService(subscription_id, certificate_path)

위 예제에서, `sms`는 **ServiceManagementService** 개체입니다. **ServiceManagementService** 클래스는 Azure 서비스를 관리하는 데 사용되는 주 클래스입니다. 

## <a name="ListAvailableLocations"> </a>방법: 사용 가능한 위치 나열

서비스를 호스트하는 데 사용할 수 있는 위치를 나열하려면 **list\_locations** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

클라우드 서비스, 저장소 서비스 또는 선호도 그룹을 만드는 경우 유효한 위치를 제공해야 합니다. **list\_locations** 메서드는 항상 현재 사용 가능한 위치의 최신 목록을 반환합니다. 현재 사용 가능한 위치는 다음과 같습니다.

- 서유럽 
- 동남아시아 
- 동아시아 
- 미국 중북부 
- 북유럽 
- 미국 중남부 
- 미국 서부 
- 미국 동부

## <a name="CreateCloudService"> </a>방법: 클라우드 서비스 만들기

응용 프로그램을 만들어 Azure에서 실행하면 코드와 구성은 모두 Azure [클라우드 서비스](이전 Azure 릴리스에서는  *hosted service*라 함)라고 합니다. **create\_hosted\_service** 메서드를 통해 호스티드 서비스 이름(Azure에서 고유해야 함), 레이블(base64로 인코드된 호스티드 서비스 이름), 설명 및 위치를 제공하여 새 호스팅 서비스를 만들 수 있습니다. 서비스 위치 대신 선호도 그룹을 지정할 수 있습니다. 

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(name, label, desc, location)

**list\_hosted\_services** 메서드로 구독하는 모든 호스팅 서비스를 나열할 수 있습니다.

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

특정 호스팅 서비스에 대한 정보를 가져오려는 경우 호스팅 서비스 이름을 **get\_hosted\_service\_properties** 메서드에 전달하면 됩니다.

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
	print('Location: ' + hosted_service.hosted_service_properties.location)
			
클라우드 서비스를 만든 후에는 **create\_deployment** 메서드로 코드를 서비스에 배포할 수 있습니다.

## <a name="DeleteCloudService"> </a>방법: 클라우드 서비스 삭제

서비스 이름을 **delete\_hosted\_service** 메서드에 전달하여 클라우드 서비스를 삭제할 수 있습니다.

	sms.delete_hosted_service('myhostedservice')

서비스의 모든 배포를 먼저 삭제해야 서비스를 삭제할 수 있습니다. (자세한 내용은 [방법: 배포 삭제](#DeleteDeployment) 를 참조하세요.

## <a name="CreateDeployment"> </a>방법: 배포 만들기

**create\_deployment** 매서드는 새 [서비스 패키지]를 업로드하고 스테이징 또는 프로덕션 환경에서 새 배포를 만듭니다. 이 메서드의 매개 변수는 다음과 같습니다.

* **이름**: 호스팅 서비스의 이름입니다.
* **deployment\_name**: 배포 이름입니다.
* **슬롯**: 표시하는 문자열은 `staging` or `production` 슬롯입니다.
* **package_url**: 배포 패키지(.cspgk 파일)의 URL입니다. 패키지 파일은 패키지가 업로드 중인 호스팅 서비스와 동일한 구독의 Azure Blob 저장소 계정에 저장되어야 합니다. [Azure PowerShell cmdlet] 또는 [cspack 명령줄 도구]로 배포 패키지를 만들 수 있습니다.
* **구성**: base64로 인코딩된 서비스 구성 파일(.cscfg 파일)입니다.
* **레이블**: 호스팅 서비스 이름의 레이블입니다(base64로 자동 인코딩됨).

다음 예제에서 새 배포 `v1` for a hosted service called `myhostedservice`를 만듭니다.

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	slot = 'production'
	package_url = 'URL_for_.cspkg_file'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
	label = 'myhostedservice'

	result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

위 예제에서 **create\_deployment** 작업 상태는 **create\_deployment**가 반환한 결과를 **get\_operation\_status** 메서드에 전달하여 검색할 수 있습니다.

**get\_deployment\_by\_slot** 또는 **get\_deployment\_by\_name** 메서드를 사용하여 배포 속성에 액세스할 수 있습니다. 다음 예제에서는 배포 슬롯을 지정하여 배포를 검색합니다. 또한 이 예제는 배포의 모든 인스턴스를 반복합니다.

	result = sms.get_deployment_by_slot('myhostedservice', 'production')

	print('Name: ' + result.name)
	print('Slot: ' + result.deployment_slot)
	print('Private ID: ' + result.private_id)
	print('Status: ' + result.status)
	print('Instances:')
	for instance in result.role_instance_list:
		print('Instance name: ' + instance.instance_name)
		print('Instance status: ' + instance.instance_status)
		print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>방법: 배포 업데이트

**change\_deployment\_configuration** 메서드 또는 **update\_deployment\_status** 메서드를 사용하여 배포를 업데이트할 수 있습니다.

**change\_deployment\_configuration** 메서드를 통해 새 서비스 구성 (`.cscfg`) 파일을 업로드할 수 있으며 이로 인해 일부 서비스 설정(배포의 인스턴스 수 포함)이 변경됩니다. 자세한 내용은 [Azure 서비스 구성 스키마(.cscfg)]를 참조하세요. 다음 예제는 새 서비스 구성 파일을 업로드하는 방법을 보여 줍니다.

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

	result = sms.change_deployment_configuration(name, deployment_name, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)


위 예제에서 **change\_deployment\_configuration** 작업 상태는 **change\_deployment\_configuration**가 반환한 결과를 **get\_operation\_status** 메서드에 전달하여 검색할 수 있습니다.

**update\_deployment\_status** 메서드를 통해 배포 상태를 RUNNING 또는 SUSPENDED로 설정할 수 있습니다. 다음 예제에서는  `v1` of a hosted service called `myhostedservice`라는 이름의 배포 상태를 RUNNING으로 설정하는 방법을 보여줍니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'

	result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>방법: 스테이징과 프로덕션 간의 배포 이동

Azure에서는 스테이징 및 프로덕션이라는 두 가지 환경을 제공합니다. 일반적으로 서비스는 스테이징 환경에 배포되어 서비스를 프로덕션 환경에 배포하기 전에 테스트합니다. 서비스를 스테이징 환경에서 프로덕션 환경으로 수준을 올릴 때 해당 서비스를 다시 배포하지 않고 수준을 올릴 수 있습니다. 배포를 교환하여 수준을 올릴 수 있습니다. (배포 교환에 대한 자세한 내용은 [Azure 서비스 배포]를 참조하세요.)

다음 예제는 **swap\_deployment** 메서드를 사용하여 두 배포(`v1`과 `v2`인 배포)를 교환하는 방법을 보여줍니다. 예제에서는 **swap\_deployment**를 호출하기 전 배포 `v1`은 프로덕션 슬롯에 있고 배포 `v2`는 스테이징 슬롯에 있습니다. **swap\_deployment**를 호출한 후, `v2`는 프로덕션에 있고 `v1`는 스테이징에 있습니다.  

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>방법: 배포 삭제

배포를 삭제하려면 **delete\_deployment** 메서드를 사용합니다. 다음 예제에서는 이름이 `v1`인 배포를 삭제하는 방법을 보여줍니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>방법: 저장소 서비스 만들기

[저장소 서비스]로 Azure [Blobs][azure-blobs], [테이블][azure-tables] 및 [ 큐][azure-queues]에 액세스할 수 있습니다. 저장소 서비스를 만들려면 서비스(3자에서 24자 사이의 소문자로서 Azure 내에서 고유해야 함), 설명, 레이블(base64로 자동 인코딩되며 최대 100자까지 가능) 및 위치나 선호도 그룹에 대해 이름이 있어야 합니다. 다음 예제에서는 위치를 지정하여 저장소 서비스를 만드는 방법을 보여 줍니다. 선호도 그룹을 사용하려면 먼저 선호도 그룹을 만들고([방법: 선호도 그룹 만들기](#CreateAffinityGroup)참조) **affinity\_group** 매개변수를 사용하여 설정해야 합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

위 예제에서 **create\_storage\_account** 작업 상태는 **create\_storage\_account**가 반환한 결과를 **get\_operation\_status** 메서드에 전달하여 검색할 수 있습니다.  

**list\_storage\_accounts** 메서드로 저장소 계정 및 해당 속성을 나열할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Affinity group: ' + account.storage_service_properties.affinity_group)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>방법: 저장소 서비스 삭제

저장소 서비스 이름을 **delete\_storage\_account** 메서드에 전달하여 저장소 서비스를 삭제할 수 있습니다. 저장소 서비스를 삭제하면 그 서비스에 저장되어 있는 모든 데이터(Blob, 테이블, 큐)가 삭제됩니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>방법: 선호도 그룹 만들기

선호도 그룹은 최적화된 성능의 서비스를 찾도록 Azure에게 지시하는 Azure 서비스의 논리적 그룹화입니다. 예를 들어 "미국 서부" 위치에 선호도 그룹을 만든 다음 해당 선호도 그룹에 [cloud service](#CreateCloudService) 를 만들 수 있습니다. 그런 다음 동일한 선호도 그룹에서 저장소 서비스를 만들면 Azure는 그 서비스를 "미국 서부" 위치에 놓고 최상의 성능을 위해 동일한 선호도 그룹의 클라우드 서비스로 데이터 센터 내에서 최적화하도록 인식합니다.

선호도 그룹을 만들려면 이름, 레이블(base64로 자동 인코딩됨), 위치가 있어야 합니다. 설명은 선택적으로 제공할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myAffinityGroup'
	label = 'myAffinityGroup'
	location = 'West US'
	desc = 'my affinity group'

	sms.create_affinity_group(name, label, location, desc)

[저장소 서비스를 만들](#CreateStorageService) 때, 선호도 그룹을 만든 후 (위치) 대신 그룹을 지정할 수 있습니다.

**list\_affinity\_groups** 메서드를 호출하여 선호도 그룹을 나열하고 그 속성을 검사할 수 있습니다.

	result = sms.list_affinity_groups()

	for group in result:
		print('Name: ' + group.name)
		print('Description: ' + group.description)
		print('Location: ' + group.location)
		print('')

## <a name="DeleteAffinityGroup"> </a>방법: 선호도 그룹 삭제
	
그룹 이름을 **delete\_affinity\_group** 메서드에 전달하여 선호도 그룹을 삭제할 수 있습니다. 선호도 그룹은 서비스(또는 선호도 그룹을 삭제해야 하는 데 사용하는 서비스)와의 연결을 끊어야만 삭제할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>방법: 사용 가능한 운영 체제 나열

서비스를 호스트하는 데 사용할 수 있는 운영 체제를 나열하려면 **list\_operating\_systems** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

또는 운영 체제를 제품군별로 그룹화하는 **list\_operating\_system\_families** 메서드를 사용할 수 있습니다.

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>방법: 운영 체제 이미지 만들기

운영 체제 이미지를 이미지 리포지토리에 추가하려면 **add\_os\_image** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

사용 가능한 운영 체제 이미지를 나열하려면**list\_os\_images** 메서드를 사용합니다. 여기에는 모든 플랫폼 이미지 및 사용자 이미지가 포함됩니다.

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Affinity group: ' + image.affinity_group)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>방법: 운영 체제 이미지 삭제

배포를 삭제하려면 **delete\_os\_image** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>방법: 가상 컴퓨터 만들기

가상 컴퓨터를 만들려면 먼저 [클라우드 서비스]를 만들어야 합니다(#CreateCloudService).  그런 다음 **create\_virtual\_machine\_deployment** 메서드를 사용하여 가상 컴퓨터 배포를 만듭니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-ko-kr-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>방법: 가상 컴퓨터 삭제

가상 컴퓨터를 삭제하려면 **delete\_deployment** 메서드를 사용하여 먼저 배포를 삭제합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

그런 다음 **delete\_hosted\_service** 메서드를 사용하여 클라우드 서비스를 삭제할 수 있습니다.

	sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>다음 단계

이제 서비스 관리의 기본 사항을 배웠으므로 좀 더 복잡한 작업을 수행하려면 다음 링크를 따라가세요.

-   다음 MSDN 참조를 확인하세요. [클라우드 서비스][]
-   다음 MSDN 참조를 확인하세요. [가상 컴퓨터][]

[서비스 관리]: #WhatIs
[개념]: #Concepts
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
[방법: 사용 가능한 운영 체제 나열]: #ListOperatingSystems
[방법: 운영 체제 이미지 만들기]: #CreateVMImage
[방법: 운영 체제 이미지 삭제]: #DeleteVMImage
[방법: 가상 컴퓨터 만들기]: #CreateVM
[방법: 가상 컴퓨터 삭제]: #DeleteVM
[다음 단계]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/ko-kr/develop/python/common-tasks/install-python/
[클라우드 서비스]:http://windowsazure.com/ko-kr/documentation/articles/cloud-services-what-is
[서비스 패키지]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Azure PowerShell cmdlet]: https://www.windowsazure.com/ko-kr/develop/php/how-to-guides/powershell-cmdlets/
[cspack 명령줄 도구]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[Azure 서비스 배포]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[저장소 서비스]: https://www.windowsazure.com/ko-kr/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/ko-kr/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/ko-kr/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/ko-kr/develop/python/how-to-guides/queue-service/
[Azure 서비스 구성 스키마(.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[클라우드 서비스]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[가상 컴퓨터]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=45--> 
