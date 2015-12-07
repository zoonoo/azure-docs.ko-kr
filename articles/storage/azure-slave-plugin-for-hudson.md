<properties
    pageTitle="Hudson Continuous Integration과 함께 Azure 슬레이브 플러그인을 사용하는 방법"
    description="Hudson Continuous Integration과 함께 Azure 슬레이브 플러그인을 사용하는 방법을 설명합니다."
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="v-dedomi"/>

#Hudson Continuous Integration과 함께 Azure 슬레이브 플러그인을 사용하는 방법

Hudson용 Azure 종속 플러그인을 사용하면 분산된 빌드를 실행할 때 슬레이브 노드를 프로비전할 수 있습니다.

## Azure 슬레이브 플러그인 설치
1. Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2. **Manage Hudson** 페이지에서 **Manage Plugins**를 클릭합니다.
3. **Available** 탭을 클릭합니다.
4. **Search**를 클릭하고 **Azure**를 입력하여 관련 플러그인 목록을 제한합니다.

	사용 가능한 플러그인 목록을 스크롤하려면 **Others** 탭의 **Cluster Management and Distributed Build** 섹션에서 Azure 슬레이브 플러그인을 찾을 수 있습니다.
	 
5. **Azure Slave Plugin**에 대한 확인란을 선택합니다.
6. **Install**을 클릭합니다.
7. Hudson을 다시 시작합니다.

이제 플러그인이 설치되었으며, 다음 단계는 Azure 구독 프로필로 플러그인을 구성하고 슬레이브 노드에 대한 VM을 만드는 데 사용될 템플릿을 만드는 것입니다.


## 구독 프로필로 Azure 슬레이브 플러그인 구성

게시 설정으로도 참조되는 구독 프로필은 보안 자격 증명 및 개발 환경에서 Azure로 작업할 때 필요한 일부 추가 정보를 포함하는 XML 파일입니다. Azure 슬레이브 플러그인을 구성하려면 다음이 필요합니다.

* 구독 ID
* 구독에 대한 관리 인증서

이는 구독 프로필에서 찾을 수 있습니다. 구독 프로필의 사본이 없다면 [여기](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0)에서 다운로드할 수 있습니다. 다음은 구독 프로필의 예입니다.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

구독 프로필이 있으면 이 단계를 따라 Azure 슬레이브 플러그인을 구성합니다.

1. Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2. **Configure System**을 클릭합니다.
3. 페이지를 아래로 스크롤해서 **Cloud** 섹션을 찾습니다.
4. **Add new cloud > Microsoft Azure**를 클릭합니다.



	![새 클라우드 추가](./media/azure-slave-plugin-for-hudson/hudson-setup-addcloud.png)

	그러면 구독 세부 정보를 입력해야 하는 필드가 표시됩니다.

	![프로필 구성](./media/azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png)

5. 구독 프로필에서 구독 ID 및 관리 인증서를 복사하여 해당 필드에 붙여넣습니다.

	구독 ID 및 관리 인증서를 복사할 때 값을 묶고 있는 따옴표는 포함하지**마십시오**.
	
6. **Verify configuration**을 클릭합니다.
7. 구성이 성공적으로 확인되면 **Save**를 클릭합니다.

## Azure 슬레이브 플러그인용 가상 컴퓨터 템플릿을 설정합니다.

가상 컴퓨터 템플릿은 플러그인이 Azure에 슬레이브 노드를 만들 때 사용할 매개 변수를 정의합니다. 다음 단계에서는 Ubuntu VM용 템플릿을 만들 것입니다.

1. Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2. **Configure System**을 클릭합니다.
3. 페이지를 아래로 스크롤해서 **Cloud** 섹션을 찾습니다.
4. **Cloud** 섹션 내에서 **Add Azure Virtual Machine Template**을 찾은 후 **Add** 단추를 클릭합니다.



	![VM 템플릿 추가](./media/azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png)

5. **Name** 필드에서 클라우드 서비스 이름을 지정합니다. 지정한 이름이 기존 클라우드 서비스를 참조하는 경우, VM은 해당 서비스를 프로비전합니다. 그렇지 않은 경우 Azure는 새로운 서비스를 만듭니다.
6. **Description** 필드에 만드는 템플릿을 설명하는 텍스트를 입력합니다. 이 정보는 설명 목적이며 VM 프로비저닝에는 사용되지 않습니다.
7. **Labels** 필드에 **linux**를 입력합니다. 이 레이블은 만드는 템플릿을 식별하는 데 사용되며, 추후 Hudson 작업을 만들 때 참조에 사용됩니다. 
8. VM을 만들 하위 지역을 선택합니다.
9. 적절한 VM 크기를 선택합니다.
10. VM을 만들 저장소 계정을 지정합니다. 사용할 클라우드 서비스와 동일한 하위 지역에 있는지 확인하십시오. 이후 만들어질 새 저장소를 사용하려는 경우 이 필드는 비워두어도 됩니다.
11. 보존 시간은 Hudson이 유휴 슬레이브를 삭제하기까지의 시간을 분 단위로 지정합니다. 기본값인 60으로 둡니다.
12. **Usage**에서 슬레이브 노드가 사용되는 경우에 적절한 조건을 선택합니다. 지금은 **Utilize this node as much as possible**을 선택합니다.



	이때 다음과 유사한 형식으로 표시되게 됩니다.

	![템플릿 구성](./media/azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png)

13. **Image Family or Id**에서 VM에 설치할 시스템 이미지를 지정해야 합니다. 이미지 제품군 목록에서 선택하거나 사용자 지정 이미지를 지정할 수 있습니다.

	이미지 제품군 목록에서 선택하려는 경우, 이미지 제품군 이름의 첫 글자(대/소문자 구분)를 입력합니다. 예를 들어 **U**를 입력하면 Ubuntu Server 제품군 목록이 표시됩니다. 목록에서 선택하면 VM을 프로비저닝할 때 Jenkins가 해당 제품군에서 시스템 이미지의 최신 버전을 사용하게 됩니다.

	![OS 제품군 목록](./media/azure-slave-plugin-for-hudson/hudson-oslist.png)
	
	이미지 제품군 목록에서 선택하는 대신 사용하고자 하는 사용자 지정 이미지가 있는 경우, 해당 사용자 지정 이미지의 이름을 입력합니다. 사용자 지정 이미지 이름은 목록에 표시되지 않으므로 이름을 올바르게 입력했는지 확인해야 합니다.

	이 자습서에서는 **U**를 입력하여 Ubuntu 이미지 목록을 표시하고 **Ubuntu Server 14.04 LTS**를 선택합니다.
 
14. **Launch method**에 대해서는 **SSH**를 선택합니다.
15. 다음 스크립트를 복사하여 **Init script** 필드에 붙여넣습니다.

		# Install Java
		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk



		# Install git

		sudo apt-get install -y git



		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

	**Init script**는 VM이 만들어진 다음에 실행됩니다. 이 예에서는 이 스크립트가 Java, git 및 ant를 설치합니다.
	
16. **Username** 및 **Password** 필드에는 VM에 만들어질 관리자 계정에 대한 기본 설정값을 입력합니다.
17. **Verify Template**을 클릭하여 지정한 매개 변수가 유효한지 확인합니다.
18. **Save**를 클릭합니다.


## Azure의 슬레이브 노드에서 실행되는 Hudson 작업 만들기

이 섹션에서는 Azure의 슬레이브 노드에서 실행할 Hudson 작업을 만듭니다.

1. Hudson 대시보드에서 **New Job**을 클릭합니다. 
2. 만들 작업에 대한 이름을 입력합니다.
3. 작업 유형에 대해서는 **Build a free-style software job**을 선택합니다.
4. **Ok**를 클릭합니다.
5. 작업 구성 페이지에서 **Restrict where this project can be run**을 선택합니다.
6. **Node and label menu**를 선택하고 **linux**를 선택합니다(이전 섹션에서 가상 컴퓨터 템플릿을 만들 때 이 레이블을 지정했습니다). 

7. **Build** 섹션에서 **Add build step**을 클릭하고 **Execute shell**을 선택합니다.
8. 다음 스크립트를 편집하여 **(your github account name)**, **(your project name)** 및 **(your project directory)**를 적절한 값으로 대체하고, 편집한 스크립트를 표시되는 텍스트 영역에 붙여넣습니다.


		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your github account name)/(your project name).git

		fi
		
		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant
		
9. **Save**를 클릭합니다.
10. Hudson 대시보드에서 방금 만든 작업을 찾아서 **Schedule a build** 아이콘을 클릭합니다. 

그러면 Hudson은 이전 섹션에서 만든 템플릿을 사용하여 슬레이브 노드를 만들고 이 작업에 대한 빌드 단계에서 지정한 스크립트를 실행합니다.






  

  

<!---HONumber=AcomDC_1125_2015-->