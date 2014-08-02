<properties linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="What is Azure Multi-Factor Authentication?" pageTitle="What is Azure Multi-Factor Authentication?" metaKeywords="" description="Learn more about Azure Multi-Factor Authentication, a method of authentication that requires the use of more than one verification method and adds a critical second layer of security to user sign-ins and transactions." metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="How to Manage Azure Virtual Machines using Ruby" authors="larryfr" solutions="" manager="" editor="" />

Ruby를 사용하여 Azure 가상 컴퓨터를 관리하는 방법
=================================================

이 가이드에서는 Azure 가상 컴퓨터와 관련하여 VM 만들기와 구성 및 데이터 디스크 추가와 같은 일반적인 관리 작업을 프로그래밍 방식으로 구성하는 방법을 보여 줍니다. Azure SDK for Ruby를 통해 Azure 가상 컴퓨터를 포함하여 다양한 Azure 서비스의 서비스 관리 기능에 액세스할 수 있습니다.

목차
----

-   [서비스 관리](#what-is)
-   [개념](#concepts)
-   [관리 인증서 만들기](#setup-certificate)
-   [Ruby 응용 프로그램 만들기](#create-app)
-   [SDK를 사용하도록 응용 프로그램 구성](#configure-access)
-   [Azure 관리 연결 설정](#setup-connection)
-   [방법: 가상 컴퓨터 작업](#virtual-machine)
-   [방법: 이미지 및 디스크 작업](#vm-images)
-   [방법: 클라우드 서비스 작업](#cloud-services)
-   [방법: 저장소 서비스 작업](#storage-services)
-   [다음 단계](#next-steps)

서비스 관리
-----------

Azure에서는 Azure 가상 컴퓨터 관리를 포함하여 [서비스 관리 작업용 REST API](http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx)를 사용할 수 있습니다. Azure SDK for Ruby는 **Azure::VirtualMachineSerivce** 클래스를 통해 가상 컴퓨터의 관리 작업을 노출합니다. [Azure 관리 포털](https://manage.windowsazure.com)을 통해 사용할 수 있는 가상 컴퓨터 관리 기능 중 다수는 이 클래스를 사용해 액세스할 수 있습니다.

서비스 관리 API를 사용하면 Azure에서 호스트되는 다양한 서비스를 관리할 수 있지만, 이 문서에서는 Azure 가상 컴퓨터 관리에 대해서만 자세히 설명합니다.

개념
----

Azure 가상 컴퓨터는 클라우드 서비스 내에서 '역할'로 구현됩니다. 각 클라우드 서비스에는 다수의 배포로 논리적으로 그룹화된 하나 이상의 역할이 포함될 수 있습니다. 역할은 가용 메모리 양, CPU 코어 수 등과 같이 VM의 전반적인 물리적 특징을 정의합니다.

또한 각 VM에는 부팅 가능한 운영 체제가 포함된 OS 디스크가 있습니다. VM에는 응용 프로그램 데이터를 저장하는 데 사용해야 하는 추가 디스크인 데이터 디스크가 한 개 이상 있을 수 있습니다. 디스크는 Azure Blob 저장소에 저장되는 VHD(가상 하드 드라이브)로 구현됩니다. 또한 VHD는 VM 생성 도중 VM에서 사용되는 디스크를 만드는 데 사용하는 템플릿인 '이미지'로 노출될 수 있습니다. 예를 들어 Ubuntu 이미지를 사용하는 새 VM을 만들면 Ubuntu 이미지에서 새 OS 디스크가 만들어집니다.

대부분의 이미지는 Microsoft 또는 파트너에서 제공되지만, 고유한 이미지를 만들거나 Azure에서 호스트되는 VM에서 이미지를 만들 수 있습니다.

Azure 관리 인증서 만들기
------------------------

**Azure::VirtualMachineService** 클래스를 통해 노출된 작업과 같은 서비스 관리 작업을 수행할 때는 Azure 구독 ID 및 구독의 관리 인증서가 포함된 파일을 제공해야 합니다. 두 가지 모두 Azure REST API로 인증할 때 SDK에서 사용됩니다.

Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 구독 ID 및 관리 인증서를 구해야 합니다. xplat-cli 설치 및 구성에 대한 자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](http://www.windowsazure.com/en-us/manage/install-and-configure-cli/)을 참조하십시오.

xplat-cli를 구성하고 나면 다음 단계를 수행하여 Azure 구독 ID를 검색하고 관리 인증서를 내보낼 수 있습니다.

1.  구독 ID를 검색하려면 다음을 사용합니다.

         azure account list

2.  관리 인증서를 내보내려면 다음 명령을 사용합니다.

         azure account cert export

    명령이 완료된 후에는 증명서가 이름이 &lt;azure-subscription-name\>.pem인 파일로 내보내집니다. 예를 들어 구독의 이름이 **mygreatsubscription**으로 지정된 경우 만들어지는 파일에는 **mygreatsubscription.pem**이라는 이름이 지정됩니다.

구독 ID 및 내보낸 인증서가 포함된 PEM 파일의 위치를 기록해 둡니다. 이 정보는 이 문서 뒷부분에서 사용됩니다.

Ruby 응용 프로그램 만들기
-------------------------

새 Ruby 응용 프로그램을 만듭니다. 이 문서에서 사용하는 예는 단일 **.rb** 파일에서 구현할 수 있습니다.

응용 프로그램 구성
------------------

Azure 서비스를 관리하려면 Azure SDK for Ruby가 포함된 Azure gem을 다운로드하여 사용해야 합니다.

### gem 명령을 사용하여 패키지 설치

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(UNIX) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  다음 코드를 사용하여 Azure gem을 설치합니다.

         gem install azure

    다음과 비슷한 결과가 나타나야 합니다.

         Fetching: mini_portile-0.5.1.gem (100%)
         Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
         Fetching: mime-types-1.25.gem (100%)
         Fetching: systemu-2.5.2.gem (100%)
         Fetching: macaddr-1.6.1.gem (100%)
         Fetching: uuid-2.3.7.gem (100%)
         Fetching: azure-0.5.0.gem (100%)
         Successfully installed mini_portile-0.5.1
         Successfully installed nokogiri-1.6.0-x86-mingw32
         Successfully installed mime-types-1.25
         Successfully installed systemu-2.5.2
         Successfully installed macaddr-1.6.1
         Successfully installed uuid-2.3.7
         Successfully installed azure-0.5.0
         7 gems installed

    **참고**

    권한 관련 오류가 나타나는 경우에는 대신 `sudo gem install azure`를 사용하십시오.

### gem 요구

텍스트 편집기를 사용하여 Ruby 응용 프로그램 파일 맨 위에 다음을 추가합니다. 이렇게 하면 Azure gem이 로드되고 Azure SDK for Ruby를 응용 프로그램에 사용할 수 있게 됩니다.

    require 'azure'

방법: 서비스 관리에 연결
------------------------

Azure에서 서비스 관리 작업을 수행하려면 구독 ID 및 [Azure 관리 인증서 만들기](#setup-certificate) 섹션에서 구한 인증서를 지정해야 합니다. 이렇게 하는 가장 쉬운 방법은 다음 환경 변수를 사용하여 ID 및 인증서 파일 경로를 지정하는 것입니다.

-   AZURE\_MANAGEMENT\_CERTIFICATE - 관리 인증서가 포함된 .PEM 파일의 경로

-   AZURE\_SUBSCRIPTION\_ID - Azure 구독의 구독 ID

다음을 사용하여 프로그래밍 방식으로 응용 프로그램에서 이 값을 설정할 수도 있습니다.

    Azure.configure do |config|
      config.management_certificate = 'path/to/certificate'
      config.subscription_id = 'subscription ID'
    end

방법: 가상 컴퓨터 작업
----------------------

Azure 가상 컴퓨터에 대한 관리 작업은 **Azure::VirtualMachineService** 클래스를 사용하여 수행합니다.

### 방법: 새 가상 컴퓨터 만들기

새 가상 컴퓨터를 만들려면 **create\_virtual\_machine** 메서드를 사용합니다. 이 메서드는 다음 매개 변수가 포함된 해시를 허용하고 생성된 VM에 대해 설명하는 **Azure::VirtualMachineManagement::VirtualMachine** 인스턴스를 반환합니다.

**매개 변수**

-   **:vm\_name** - 가상 컴퓨터 이름

-   **:vm\_user** - 루트 또는 관리 사용자 이름

-   **:password** - 루트 또는 관리 사용자에 사용할 암호

-   **:image** - 이 VM의 OS 디스크를 만드는 데 사용할 OS 이미지. OS 디스크는 Blob 저장소에서 생성된 VHD에 저장됩니다.

-   **:location** - VM이 생성될 지역. 이 VM에서 사용되는 디스크가 포함된 저장소 계정의 지역과 동일한 지역이어야 합니다.

다음 예제는 이 매개 변수를 사용하여 새 가상 컴퓨터를 만드는 과정을 보여 줍니다.

    vm_params = {
      :vm_name => 'mygreatvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
      :location = 'East US'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params)

    puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
    puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
    puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**옵션**

VM 생성의 기본 동작을 다시 정의(예: 새로 만드는 대신 기존 저장소 계정 지정)할 수 있는 선택적인 매개 변수의 해시를 제공할 수 있습니다.

다음은 **create\_virtual\_machine** 메서드를 사용할 때 사용할 수 있는 옵션입니다.

-   **:storage\_account\_name** - 디스크 이미지를 저장하는 데 사용하는 저장소 계정의 이름. 저장소 계정이 아직 없는 경우 새로 만들어집니다. 이름을 생략하면 :vm\_name param을 기반으로 한 이름을 사용하여 새 저장소 계정이 만들어집니다.

-   **:cloud\_service\_name** - 가상 컴퓨터를 호스트하는 데 사용하는 클라우드 서비스의 이름. 클라우드 서비스가 아직 없는 경우 새로 만들어집니다. 이름을 생략하면 :vm\_name param을 기반으로 한 이름을 사용하여 새 클라우드 서비스 계정이 만들어집니다.

-   **:deployment\_name** - 가상 컴퓨터 구성을 배포할 때 사용하는 배포 이름

-   **:tcp\_endpoints** - 이 VM에 대해 공개적으로 노출하는 TCP 포트. SSH 끝점(Linux 기반 VM의 경우) 및 WinRM 끝점(Windows 기반 VM의 경우)이 자동으로 만들어지므로 이 끝점을 지정할 필요가 없습니다. 여러 포트를 쉼표로 구분하여 지정할 수 있습니다. 내부 포트를 다른 포트 번호를 사용하는 공용 포트와 연결하려면 **공용 포트:내부 포트** 형식을 사용합니다. 예를 들어 80:8080은 내부 포트 8080을 공용 포트 80으로 노출합니다.

-   **:service\_location** - VM의 대상 인증서 저장소 위치. Windows 기반 VM에만 적용됩니다.

-   **:ssh\_private\_key\_file** - Linux 기반 VM에 대한 보안 SSH 액세스에 사용되는 개인 키가 포함된 파일. HTTPS 전송을 선택한 경우 WinRM 보안을 설정하는 데 사용되는 인증서를 지정하는 데에도 사용됩니다. **:ssh\_private\_key\_file** 및 **:ssh\_certificate\_file**이 생략된 경우 SSH는 암호 인증만 사용합니다.

-   **:ssh\_certificate\_file** - Linux 기반 VM에 대한 보안 SSH 액세스에 사용되는 인증서 파일이 포함된 파일. HTTPS 전송을 선택한 경우 WinRM 보안을 설정하는 데 사용되는 인증서를 지정하는 데에도 사용됩니다. **:ssh\_private\_key\_file** 및 **:ssh\_certificate\_file**이 생략된 경우 SSH는 암호 인증만 사용합니다.

-   **:ssh\_port** - SSH 통신에 사용할 공용 포트. 생략되는 경우 SSH 포트는 기본적으로 22로 설정됩니다.

-   **:vm\_size** - VM의 크기. 이 값에 따라 VM의 메모리 크기, 코어 수, 대역폭, 기타 물리적 특징이 결정됩니다. 사용할 수 있는 크기 및 물리적 특징에 대한 자세한 내용은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx)를 참조하십시오.

-   **:winrm\_transport** - WinRM에서 사용할 수 있는 전송 배열. 유효한 전송은 'http' 및 'https'입니다. 전송으로 'https'가 지정된 경우에는 **:ssh\_private\_key\_file** 및 **:ssh\_certificate\_file**을 사용하여 HTTPS 통신 보안을 유지하는 데 사용되는 인증서도 지정해야 합니다.

다음은 소규모 계산 인스턴스를 사용하고, HTTP(로컬 포트 8080, 공용 포트 80) 및 HTTPS(443) 트래픽용 포트를 공개적으로 노출하며, 지정된 인증서 파일을 사용하여 SSH 세션의 인증서 인증을 사용하도록 설정하는 새 가상 컴퓨터를 만드는 예입니다.

    vm_params = {
      :vm_name => 'myvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
      :location = 'East US'
    }

    vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

### 방법: 가상 컴퓨터 나열

Azure 구독의 기존 가상 컴퓨터를 나열하려면 **list\_virtual\_machines** 메서드를 사용합니다. 이 메서드는 **Azure::VirtualMachineManagement::VirtualMachine** 개체 배열을 반환합니다.

    vm_mgr = Azure::VirtualMachineService.new
    virtual_machines = vm_mgr.list_virtual_machines

### 방법: 가상 컴퓨터에 대한 정보 가져오기

특정 가상 컴퓨터의 **Azure::VirtualMachineManagement::VirtualMachine** 인스턴스를 가져오려면 **get\_virtual\_machine** 메서드를 사용하고 가상 컴퓨터 및 클라우드 서비스 이름을 제공합니다.

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

### 방법: 가상 컴퓨터 삭제

가상 컴퓨터를 삭제하려면 **delete\_virtual\_machine** 메서드를 사용하고 가상 컴퓨터 및 클라우드 서비스 이름을 제공합니다.

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

**경고**

**delete\_virtual\_machine** 메서드는 클라우드 서비스 및 해당 가상 컴퓨터와 연결된 디스크를 삭제합니다.

### 방법: 가상 컴퓨터 종료

가상 컴퓨터를 종료하려면 **shutdown\_virtual\_machine** 메서드를 사용하고 가상 컴퓨터 및 클라우드 서비스 이름을 제공합니다.

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

### 방법: 가상 컴퓨터 시작

가상 컴퓨터를 시작하려면 **start\_virtual\_machine** 메서드를 사용하고 가상 컴퓨터 및 클라우드 서비스 이름을 제공합니다.

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

방법: 가상 컴퓨터 이미지 및 디스크 작업
---------------------------------------

가상 컴퓨터 이미지에 대한 작업은 **Azure::VirtualMachineImageService** 클래스를 사용하여 수행됩니다. 디스크에 대한 작업은 **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService** 클래스를 사용하여 수행됩니다.

### 방법: 가상 컴퓨터 이미지 나열

사용할 수 있는 가상 컴퓨터 이미지를 나열하려면 **list\_virtual\_machine\_images** 메서드를 사용합니다. 그러면 **Azure::VirtualMachineImageService** 개체 배열이 반환됩니다.

    image_mgr = Azure::VirtualMachineImageService.new
    images = image_mgr.list_virtual_machine_images

### 방법: 디스크 나열

Azure 구독의 디스크를 나열하려면 **list\_virtual\_machine\_disks** 메서드를 사용합니다. 그러면 **Azure::VirtualMachineImageManagement::VirtualMachineDisk** 개체 배열이 반환됩니다.

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disks = disk_mgr.list_virtual_machine_disks

### 방법: 디스크 삭제

디스크를 삭제하려면 **delete\_virtual\_machine\_disk** 메서드를 사용하고 삭제할 디스크의 이름을 지정합니다.

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disk_mgr.delete_virtual_machine_disk

방법: 클라우드 서비스 작업
--------------------------

Azure 클라우드 서비스에 대한 관리 작업은 **Azure::CloudService** 클래스를 사용하여 수행합니다.

### 방법: 클라우드 서비스 만들기

새 클라우드 서비스를 만들려면 **create\_cloud\_service** 메서드를 사용하고 옵션의 이름 및 해시를 제공합니다. 유효한 옵션은 다음과 같습니다.

-   **:location** - *필수*. 클라우드 서비스가 만들어질 지역

-   **:description** - 클라우드 서비스에 대한 설명

다음은 미국 동부 지역에 새 클라우드 서비스를 만듭니다.

    cs_mgr = Azure::CloudService.new
    cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

### 방법: 클라우드 서비스 나열

Azure 구독의 클라우드 서비스를 나열하려면 **list\_cloud\_services** 메서드를 사용합니다. 이 메서드는 **Azure::CloudServiceManagement::CloudService** 개체 배열을 반환합니다.

    cs_mgr = Azure::CloudService.new
    cloud_services = cs_mgr.list_cloud_services

### 방법: 클라우드 서비스가 이미 있는지 여부 확인

특정 클라우드 서비스가 이미 있는지 여부를 확인하려면 **get\_cloud\_service** 메서드를 사용하고 클라우드 서비스의 이름을 제공합니다. 지정된 이름의 클라우드 서비스가 있는 경우 **true**가 반환되고, 그렇지 않은 경우 **false**가 반환됩니다.

    cs_mgr = Azure::CloudService.new
    cs_exists = cs_mgr.get_cloud_service('mycloudservice')

### 방법: 클라우드 서비스 삭제

클라우드 서비스를 삭제하려면 **delete\_cloud\_service** 메서드를 사용하고 클라우드 서비스의 이름을 제공합니다.

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service('mycloudservice')

### 방법: 배포 삭제

클라우드 서비스의 배포를 삭제하려면 **delete\_cloud\_service\_deployment** 메서드를 사용하고 클라우드 서비스 이름을 제공합니다.

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service_deployment('mycloudservice')

방법: 저장소 서비스 작업
------------------------

Azure 클라우드 서비스에 대한 관리 작업은 **Azure::StorageService** 클래스를 사용하여 수행합니다.

### 방법: 저장소 계정 만들기

새 저장소 계정을 만들려면 **create\_storage\_account** 메서드를 사용하고 옵션의 이름 및 해시를 제공합니다. 유효한 옵션은 다음과 같습니다.

-   **:location** - *필수*. 저장소 계정이 만들어질 지역

-   **:description** - 저장소 계정에 대한 설명

다음은 '미국 동부' 지역에 새 저장소 계정을 만듭니다.

    storage_mgr = Azure::StorageService.new
    storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

### 방법: 저장소 계정 나열

Azure 구독의 저장소 계정 목록을 가져오려면 **list\_storage\_accounts** 메서드를 사용합니다. 이 메서드는 **Azure::StorageManagement::StorageAccount** 개체 배열을 반환합니다.

    storage_mgr = Azure::StorageService.new
    accounts = storage_mgr.list_storage_accounts

### 방법: 저장소 계정이 있는지 여부 확인

특정 저장소 계정이 있는지 여부를 확인하려면 **get\_storage\_account** 메서드를 사용하고 저장소 계정의 이름을 지정합니다. 저장소 계정이 있는 경우 **true**가 반환되고 그렇지 않은 경우 **false**가 반환됩니다.

    storage_mgr = Azure::StorageService.new
    store_exists = storage_mgr.get_storage_account('mystorage')

### 방법: 저장소 계정 삭제

저장소 계정을 삭제하려면 **delete\_storage\_account** 메서드를 사용하고 저장소 계정의 이름을 지정합니다.

    storage_mgr = Azure::StorageService.new
    storage_mgr.delete_storage_account('mystorage')

다음 단계
---------

이제 프로그래밍 방식으로 Azure 가상 컴퓨터 만들기의 기본 사항을 배웠으므로 다음 링크를 따라 더 많은 VM 작업을 수행하는 방법을 알아보십시오.

-   [가상 컴퓨터](http://www.windowsazure.com/en-us/documentation/services/virtual-machines/)(영문) 기능 페이지를 방문하십시오.
-   다음 MSDN 참조를 확인하십시오. [가상 컴퓨터](http://msdn.microsoft.com/en-us/library/windowsazure/jj156003.aspx)
-   [가상 컴퓨터에서 Ruby on Rails 응용 프로그램](http://www.windowsazure.com/en-us/develop/ruby/tutorials/web-app-with-linux-vm/)을 호스트하는 방법을 알아보십시오.

