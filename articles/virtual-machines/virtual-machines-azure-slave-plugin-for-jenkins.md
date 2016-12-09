---
title: "Jenkins Continuous Integration과 함께 Azure 슬레이브 플러그 인을 사용하는 방법 | Microsoft Docs"
description: "Jenkins Continuous Integration과 함께 Azure 슬레이브 플러그 인을 사용하는 방법을 설명합니다."
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 09233bd4-957d-41bf-bccc-9dd2355ed1bf
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a40d829c65067dba90a212390e858b7995452b6


---
# <a name="how-to-use-the-azure-slave-plugin-with-jenkins-continuous-integration"></a>Jenkins Continuous Integration과 함께 Azure 슬레이브 플러그 인을 사용하는 방법
Jenkins용 Azure 종속 플러그 인을 사용하면 분산된 빌드를 실행할 때 슬레이브 노드를 프로비전할 수 있고 다음을 만들도록 지원합니다.

* SSH 및 Java JNLP(네트워크 시작 프로토콜)를 사용하는 Azure 클라우드에 대한 Windows 슬레이브
  
  * SSH 통해 Windows 이미지를 시작하려면 SSH를 사용하여 이미지를 미리 구성해야 합니다.
  * 사용자 지정 Windows 이미지를 준비하는 방법에 대한 정보는 [Resource Manager 배포 모델에서 Windows 가상 컴퓨터를 캡처하는 방법][windows-image-capture]을 참조하세요.
* SSH를 사용하는 Azure 클라우드에서 Linux 슬레이브
  
  * 사용자 지정 Linux 이미지를 준비하는 방법에 대한 정보는 [Linux 가상 컴퓨터를 캡처하여 Resource Manager 템플릿으로 사용하는 방법][linux-image-capture]을 참조하세요.

### <a name="prerequisites"></a>필수 조건
이 문서의 단계를 시작하기 전에 클라이언트 응용 프로그램을 등록하고 권한을 부여한 다음, 인증 중에 Azure Active Directory로 전송될 클라이언트 ID 및 클라이언트 암호를 검색해야 합니다. 이러한 필수 구성 요소에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Active Directory와 응용 프로그램 통합][integrate-apps-with-AAD]
* [클라이언트 앱 등록][register-client-app]

또한 이 문서에 있는 [Azure의 슬레이브 노드에서 실행되는 Jenkins 작업을 만들기](#create-jenkins-project) 섹션의 단계를 완료하려면 GitHub에서 프로젝트 설정이 있어야 합니다.

<a name="install-azure-slave-plugin"></a>

## <a name="install-the-azure-slave-plugin"></a>Azure 슬레이브 플러그 인 설치
1. Jenkins 대시보드 내에서 **Manage Jenkins**를 클릭합니다.
   
    ![Jenkins 관리][jenkins-dashboard-manage]
2. **Jenkins 관리** 페이지에서 **플러그 인 관리**를 클릭합니다.
   
    ![플러그 인 관리][jenkins-manage-plugins]
3. **사용 가능** 탭을 클릭하고 필터로 "Azure"를 입력한 다음 **Azure 슬레이브 플러그 인**을 선택합니다. 
   
    ![Azure 슬레이브 플러그 인][search-plugins]
   
    사용 가능한 플러그 인 목록을 스크롤하도록 선택하면 **클러스터 관리 및 분산 빌드** 섹션에 나열된 Azure 슬레이브 플러그 인을 찾을 수 있습니다.
4. **다시 시작 없이 설치** 또는 **지금 다운로드 및 다시 시작 후에 설치**를 클릭합니다.
   
    ![플러그 인 설치][install-plugin]

이제 플러그 인이 설치되었으며, 다음 단계는 Azure 구독 프로필로 플러그 인을 구성하고 슬레이브 노드에 대한 가상 컴퓨터를 만드는 데 사용될 템플릿을 만드는 것입니다.

## <a name="configure-the-azure-slave-plugin-to-use-your-subscription-profile"></a>Azure 슬레이브 플러그 인을 구성하여 구독 프로필 사용
게시 설정으로도 참조되는 구독 프로필은 보안 자격 증명 및 개발 환경에서 Azure로 작업할 때 필요한 일부 추가 정보를 포함하는 XML 파일입니다. Azure 슬레이브 플러그 인을 구성하려면 다음이 필요합니다.

* 구독 ID
* 구독에 대한 관리 인증서

다음 예제과 유사한 [구독 프로필]에서 찾을 수 있습니다.

        <?xml version="1.0" encoding="utf-8"?>
        <PublishData>
            <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">
                <Subscription
                    ServiceManagementUrl="https://management.core.windows.net"
                    Id="<Subscription ID value>"
                    Name="Pay-As-You-Go"
                    ManagementCertificate="<Management certificate value>" />
            </PublishProfile>
        </PublishData>

구독 프로필을 얻은 다음, 이 단계를 따라 Azure 슬레이브 플러그 인을 구성합니다.

1. Jenkins 대시보드 내에서 **Manage Jenkins**를 클릭합니다.
   
    ![Jenkins 관리][jenkins-dashboard-manage]
2. **Configure System**을 클릭합니다.
   
    ![시스템 구성][jenkins-configure-system]
3. 페이지를 아래로 스크롤해서 **Cloud** 섹션을 찾습니다.
4. **새 클라우드 추가**를 클릭한 다음 **Microsoft Azure**를 선택합니다.
   
    ![새 클라우드 추가][cloud-section]
   
    그러면 구독 세부 정보를 입력해야 하는 필드가 표시됩니다.
   
    ![구독 구성][subscription-configuration]
5. 구독 프로필에서 구독 ID 및 관리 인증서 값을 복사하여 해당 필드에 붙여넣습니다.
   
    구독 ID 및 관리 인증서를 복사할 때 값을 묶고 있는 따옴표는 포함하지 마세요.
6. **구성 확인**을 클릭하여 지정한 매개 변수가 유효한지 확인한 다음 **저장**을 클릭합니다.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plugin"></a>Azure 슬레이브 플러그 인용 가상 컴퓨터 템플릿 설정
이 섹션에서는 가상 컴퓨터 템플릿을 추가하고 여기서 Azure 슬레이브 플러그 인이 Azure에 슬레이브 노드를 만들 때 사용할 매개 변수를 정의합니다. 다음 단계에서는 Ubuntu 가상 컴퓨터용 템플릿을 만들 것입니다.

1. Jenkins 대시보드 내에서 **Manage Jenkins**를 클릭합니다.
   
    ![Jenkins 관리][jenkins-dashboard-manage]
2. **Configure System**을 클릭합니다.
   
    ![시스템 구성][jenkins-configure-system]
3. 페이지를 아래로 스크롤해서 **Cloud** 섹션을 찾습니다.
4. **클라우드** 섹션에서 이전 섹션에 추가한 클라우드에 대해 **Azure 가상 컴퓨터 템플릿 추가**를 찾은 다음 **추가**를 클릭합니다.
   
    ![Azure 가상 컴퓨터 템플릿 추가][add-vm-template]
   
    Jenkins는 만드는 템플릿에 대한 세부 정보를 입력할 필드를 포함하는 형식을 표시합니다.
   
    ![빈 일반 구성][blank-general-configuration]
5. 템플릿의 **일반 구성** 정보를 입력합니다.
   
   1. **이름** 상자에 새 템플릿 이름을 입력합니다. 이 항목은 사용자만이 사용하며 가상 컴퓨터를 프로비전하는 데 사용되지 않습니다.
   2. **Description** 상자에 만드는 템플릿을 설명하는 텍스트를 입력합니다. 이는 레코드만을 위한 것이며 가상 컴퓨터 프로비저닝에는 사용되지 않습니다.
   3. **Labels** 상자는 만드는 템플릿을 식별하는 데 사용되며, 추후 Jenkins 작업을 만들 때 참조에 사용됩니다. 용도에 맞게 사용하기 위해 이 상자에 "**linux**"를 입력합니다.
   4. **지역** 목록에서 가상 컴퓨터를 만들 Azure 지역을 클릭합니다.
   5. **가상 컴퓨터 크기** 목록의 드롭 다운 메뉴에서 적절한 크기를 선택합니다.
   6. **Storage Account Name** 상자에서 가상 컴퓨터를 만들 저장소 계정을 지정합니다. Jenkins로 기본값 "jenkinsarmst"를 사용하여 저장소 계정을 만드는 경우 이 상자를 비워 둘 수 있습니다.
   7. **보존 시간**은 Jenkins가 유휴 슬레이브를 삭제하기까지의 시간을 분 단위로 지정합니다. 이 항목을 기본값인 60으로 둡니다.
      
      * 슬레이브가 유휴 상태일 때 삭제하는 대신 종료하도록 선택할 수도 있습니다. 이를 수행하려면 **Shutdown Only (Do Not Delete) After Retention Time** 확인란을 선택합니다.
      * 또한 유휴 슬레이브를 자동으로 삭제하지 않으려는 경우 0을 지정할 수 있습니다.
   8. **사용** 목록의 다음 옵션 중에서 선택할 수 있습니다.
      
      * **이 노드 최대한 활용** - Jenkins는 가능하면 슬레이브에서 모든 작업을 실행할 수 있습니다.
      * **연결된 작업에 이 노드 두기** - Jenkins는 해당 프로젝트가 구체적으로 해당 노드에 연결된 경우 이 노드에서 프로젝트 또는 작업을 빌드합니다. 이렇게 하면 슬레이브를 특정 종류의 작업에 예약할 수 있습니다.
      
      이 자습서는 **이 노드 최대한 활용**을 클릭합니다.
      
      이때 양식은 다음과 유사한 형식으로 표시됩니다.
      
      ![일반 템플릿 구성][general-template-config]
6. 템플릿의 **이미지 구성**을 제공합니다.
   
   1. 이미지 제품군의 경우 두 가지 옵션이 있습니다.
      
      * **사용자 지정 사용자 이미지** - 이 옵션을 사용하려면 슬레이브 노드를 만들 동일한 저장소 계정에 있는 사용자 지정 이미지의 URL을 제공해야 합니다.
      * **이미지 참조** - 이 옵션을 사용하려면 이미지의 *게시자*, *제품* 및 *SKU*를 지정해야 합니다. 이 항목은 [Azure 가상 컴퓨터 마켓플레이스][azure-images]에서 찾을 수 있습니다.
        
        이 자습서에서는 **이미지 참조**를 선택하고 다음 값을 사용합니다.
      * **이미지 게시자**: Canonical
      * **이미지 제품**: UbuntuServer
      * **이미지 SKU**: 14.04.4-LTS
   2. **메서드 시작** 목록에는 **SSH** 또는 **JNLP**라는 두 가지 옵션이 있습니다. 이 자습서에서는 **SSH**를 선택합니다. 그러나 launch 메서드를 선택할 때 고려해야 할 몇 가지 주의 사항이 있습니다.
      
      * Linux 슬레이브는 SSH를 사용해야만 실행될 수 있습니다.
      * Windows 슬레이브는 SSH 또는 JNLP 중 하나를 사용할 수 있지만 Windows 슬레이브에 SSH를 사용하면 이미지는 SSH 서버로 사용자 지정 준비되어야 합니다.
        
        JNLP를 launch 메서드로 사용하는 경우 다음 항목이 구성되어 있는지 확인해야 합니다.
      * 따라서 방화벽을 구성해야 하므로 Azure 슬레이브는 Jenkins URL에 연결할 수 있어야 합니다. **Jenkins 관리** 및 **시스템 구성**을 클릭하여 Jenkins URL을 찾고 **Jenkins 위치** 섹션을 찾을 수 있습니다.
   3. JNLP를 사용하여 시작되는 Azure 슬레이브는 Jenkins TCP 포트를 연결할 수 있어야 합니다. 따라서 이런 이유로 모든 방화벽을 구성할 수 있도록 고정된 TCP 포트를 사용하는 것이 좋습니다. **Jenkins 관리** 및 **전역 보안 구성**을 클릭하여 Jenkins TCP 포트를 지정하고 **보안 설정**에 대한 옵션을 확인하고 **고정** 포트를 사용한 다음 사용할 포트를 지정할 수 있습니다.
   4. **초기화 스크립트**의 경우 다음 사항을 고려하여 가상 컴퓨터를 만든 후에 초기화 스크립트를 실행을 제공해야 합니다.
      
      * 스크립트는 최소한 Java를 설치해야 합니다.
      * JNLP를 사용하는 경우 초기화 스크립트를 PowerShell에서 작성해야 합니다.
      * 초기화 스크립트가 큰 패키지를 설치하는 등 실행하려면 시간이 오래 걸리는 경우 초기화 스크립트를 통해 설치하는 것이 아니라 모든 필요한 소프트웨어를 미리 설치하여 사용자 지정 이미지를 준비하는 것이 좋습니다.
        
        이 자습서의 경우 아래 스크립트를 복사하여 **초기화 스크립트** 상자에 붙여넣습니다.
        
              # Install Java
              sudo apt-get -y update
              sudo apt-get install -y openjdk-7-jdk
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y openjdk-7-jdk
              # Install git
              sudo apt-get install -y git
              # Install ant
              sudo apt-get install -y ant
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y ant
        
        위의 예제에서 초기화 스크립트는 *Java*, *Git* 및 *ant*를 설치합니다.
   5. **사용자 이름** 및 **암호** 상자에 가상 컴퓨터에서 만들어질 관리자 계정에 대한 기본 설정값을 입력합니다.
      
      이때 양식은 다음과 유사한 형식으로 표시됩니다.
      
      ![Jenkins 이미지 구성][jenkins-image-configuration]
7. **템플릿 확인**을 클릭하여 지정한 매개 변수가 유효한지 확인한 다음 **저장**을 클릭합니다.
   
    ![Jenkins 템플릿 저장][jenkins-save-template]

<a name="create-jenkins-project"></a>

## <a name="create-a-jenkins-job-which-runs-on-a-slave-node-on-azure"></a>Azure의 슬레이브 노드에서 실행되는 Jenkins 작업 만들기
이 섹션에서는 Azure의 슬레이브 노드에서 실행할 Jenkins 작업을 만듭니다. 이러한 단계를 완료하려면 GitHub에서 프로젝트가 실행되어야 합니다.

1. Jenkins 대시보드 내에서 **New Item**을 클릭합니다.
   
    ![Jenkins 대시보드 새 항목][jenkins-dashboard-new-item]
2. 만들려는 태스크의 이름을 입력하고 프로젝트 형식에서 **자유 형식 프로젝트**를 클릭한 다음 **확인**을 클릭합니다.
   
    ![Jenkins 새 항목 만들기][jenkins-create-new-item]
3. 태스크 구성 페이지에서 **이 프로젝트를 실행할 수 있는 제한**을 선택하고 **레이블 식** 상자에서 "**linux**"를 입력합니다. 이 값은 이전 섹션에서 만든 슬레이브 템플릿의 레이블과 일치합니다.
   
    ![Jenkins 새 항목 제한][jenkins-new-item-restrict]
4. **빌드** 섹션에서 **빌드 단계 추가**를 클릭하고 **셸 실행**을 선택합니다.
   
    ![Jenkins 새 항목 빌드][jenkins-new-item-build]
5. 다음 스크립트를 편집하여 **(GitHub 계정 이름)**, **(프로젝트 이름)** 및 **(프로젝트 디렉터리)**를 적절한 값으로 대체하고, 편집한 스크립트를 표시되는 텍스트 영역에 붙여넣습니다.
   
            # Clone from git repo
            currentDir="$PWD"
            if [ -e (your project directory) ]; then
                cd (your project directory)
                git pull origin master
            else
                git clone https://github.com/(your GitHub account name)/(your project name).git
            fi
            # change directory to project
            cd $currentDir/(your project directory)
            #Execute build task
            ant
   
    폼 모양은 다음 예제와 유사합니다.
   
    ![Jenkins 새 항목 스크립트][jenkins-new-item-script]
6. 모든 구성 단계를 완료한 경우 **저장**을 클릭합니다.
   
    ![Jenkins 새 항목 저장][jenkins-new-item-save]
7. Jenkins 대시보드에서 방금 만든 프로젝트 옆의 드롭다운 화살표를 클릭한 다음 **지금 빌드**를 클릭합니다.
   
    ![Jenkins 지금 빌드][jenkins-build-now]

Jenkins는 이전 섹션에서 만든 템플릿을 사용하여 슬레이브 노드를 만들고 이 작업에 대한 빌드 단계에서 지정한 스크립트를 실행합니다.

<a name="image-template-considerations"></a>

## <a name="considerations-when-working-with-image-templates"></a>이미지 템플릿 사용 시 고려 사항
다음 섹션은 다양한 템플릿을 구성하기 위한 몇 가지 유용한 정보를 포함합니다.

### <a name="when-you-are-using-ubuntu-image-templates"></a>Ubuntu 이미지 템플릿을 사용하는 경우
* 초기화 스크립트가 큰 패키지를 설치하는 등 실행하려면 시간이 오래 걸리는 경우 초기화 스크립트를 통해 설치하는 것이 아니라 모든 필요한 소프트웨어를 미리 설치하여 사용자 지정 Ubuntu 이미지를 준비하는 것이 좋습니다.
* 스크립트는 최소한 Java를 설치해야 합니다. 이전 예제에서와 같이 다음 스크립트를 사용하여 Java, Git 및 ant를 설치할 수 있습니다.
  
        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk
        # Install git
        sudo apt-get install -y git
        # Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y ant

### <a name="when-you-are-using-windows-image-templates-and-jnlp-launch-method"></a>Windows 이미지 템플릿 및 JNLP 시작 메서드를 사용하는 경우
* Jenkins 마스터 보안이 구성되지 않은 경우:
  
  * 슬레이브에서 실행되는 기본 스크립트에 대해서 **초기화 스크립트** 필드를 비워 둡니다.
* Jenkins 마스터 보안이 구성된 경우: 
  
  * [Windows 슬레이브 설치][windows-slaves-setup]에서 스크립트를 복사하고 Jenkins 자격 증명으로 수정합니다.
  * 스크립트는 최소한 Jenkins 사용자 ID와 API 토큰으로 수정되어야 합니다. 사용자에 대한 API 토큰을 검색하려면 다음 단계를 사용합니다.
    
    1. Jenkins 대시보드 내에서 **사람들**을 클릭합니다.
    2. 적절한 사용자 계정을 클릭합니다.
    3. 왼쪽 메뉴에서 **구성**을 클릭합니다.
    4. **API 토큰 표시...**를 클릭합니다.

<a name="see-also"></a>

## <a name="see-also"></a>참고 항목
Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

Jenkins용 Azure 슬레이브 플러그 인에 대한 자세한 내용은 GitHub의 [Azure 슬레이브 플러그 인] 프로젝트를 참조하세요.

<!-- URL List -->

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[Azure 슬레이브 플러그 인]: https://github.com/jenkinsci/azure-slave-plugin/
[구독 프로필]: http://go.microsoft.com/fwlink/?LinkID=396395
[azure-images]: https://azure.microsoft.com/marketplace/virtual-machines/all/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[windows-slaves-setup]: https://gist.github.com/snallami/5aa9ea2c57836a3b3635

<!-- IMG List -->

[jenkins-dashboard-manage]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-manage.png
[jenkins-manage-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-manage-plugins.png
[jenkins-configure-system]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-configure-system.png
[jenkins-dashboard-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-new-item.png
[search-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/install-plugin.png
[jenkins-create-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-create-new-item.png
[cloud-section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add-vm-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank-general-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[general-template-config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[jenkins-new-item-restrict]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-restrict.png
[jenkins-new-item-build]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-build.png
[jenkins-new-item-script]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-script.png
[jenkins-new-item-save]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-save.png
[jenkins-build-now]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-build-now.png
[jenkins-image-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-image-configuration.png
[jenkins-save-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-save-template.png
[windows-image-capture]: ./virtual-machines-windows-classic-capture-image.md
[linux-image-capture]: ./virtual-machines-linux-capture-image.md



<!--HONumber=Nov16_HO3-->


