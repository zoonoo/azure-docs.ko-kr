<properties
   pageTitle="Azure 자동화 및 하이브리드 클라우드 관리: Amazon 웹 서비스에서 VM 배포 자동화"
   description="Azure 자동화 및 하이브리드 클라우드 관리: Amazon 웹 서비스에서 VM 배포 자동화"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Azure 자동화 및 하이브리드 클라우드 관리: Amazon 웹 서비스에서 VM 배포 자동화

제가 [클라우드 로드쇼](https://microsoftcloudroadshow.com/cities) 이벤트에서 Azure 자동화에 대해 발표하는 동안 Azure 자동화가 Azure에만 사용하도록 설계되었는지에 대해 수많은 질문을 받았습니다. 다행스럽게도 제가 이 블로그 기사에서 이야기하려는 내용과 관련된 데모가 준비되어 있지만, 답변 내용을 요약하자면 **Azure 자동화는 Azure에만 사용할 수 있는 것이 아니라 온-프레미스 리소스* 그리고* 하이브리드 클라우드 환경 관리에 사용할 수 있습니다.** 예를 들어 AWS(Amazon 웹 서비스) 고객은 Azure 자동화를 사용할 수 있습니다.

## 목표

이 문서에서는 Azure 자동화를 활용하여 AWS에서 VM을 프로비전하고 해당 VM의 이름을 지정하는 방법을 설명합니다. VM 이름 지정을 AWS에서는 VM "태그 지정"이라고 합니다.

## 가정

이 문서의 목적을 위해 여러분이 이미 Azure 자동화 계정을 설정했고 AWS 구독을 갖고 있다고 가정하겠습니다.

Azure 자동화 계정 설정에 대한 자세한 내용은 [설명서 페이지](automation-configuring.md)를 참조하세요.

## 준비: AWS 자격 증명 저장

Azure 자동화가 AWS와 “통신”하려면 AWS 자격 증명을 검색하여 Azure 자동화에 자산으로 저장해야 합니다.

AWS 포털에 로그온한 다음 사용자 이름 아래에 있는 작은 삼각형을 클릭하고 **보안 자격 증명**을 클릭합니다.

![AWS 보안 자격 증명](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "AWS 보안 자격 증명")

**액세스 키**를 클릭합니다.

![AWS 액세스 키](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "AWS 액세스 키")

액세스 키 및 보안 액세스 키를 복사합니다. 원한다면 키 파일을 다운로드하여 안전한 곳에 저장해 둡니다.

![AWS 액세스 키 만들기](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "AWS 액세스 키 만들기")

## Azure 자동화의 AWS 자산

이전 단계에서 AWS **액세스 키 ID** 및 **보안 액세스 키**를 복사하여 저장했습니다. 이제는 Azure 자동화에 저장하겠습니다.

Azure 자동화 계정으로 이동하여 **자산** - **자격 증명**을 선택한 다음 새 자격 증명을 추가하고 이름을 **AWScred**로 지정합니다.

![이미지](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "이미지")

필요하다면 설명을 입력하고 **사용자 이름** 필드에 **액세스 ID**를, **암호** 필드에 **보안 액세스 키**를 입력합니다. AWS 자격 증명을 저장합니다.

## Amazon 웹 서비스 PowerShell 모듈

Microsoft의 VM 프로비전 Runbook은 AWS PowerShell 모듈을 활용하여 작업을 수행합니다. AWS PowerShell 모듈은 [PowerShell 갤러리](http://www.powershellgallery.com/packages/AWSPowerShell/)에 제공되며 **Azure 자동화에 배포** 단추를 사용하여 간단하게 Azure 자동화 계정에 추가할 수 있습니다.

![AA로 AWS PS 모듈 가져오기](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "AA로 AWS PS 모듈 가져오기")

**Azure 자동화에 배포**를 클릭하면 Azure 로그인 페이지로 이동됩니다. 자격 증명을 입력하면 다음 화면이 표시됩니다.

![AWS 모듈 가져오기 마법사](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "AWS 모듈 가져오기 마법사")

새로운 또는 기존의 Azure 자동화 계정을 선택합니다. 기존 계정에 대한 드롭다운 상자가 없으므로 Azure 자동화 계정의 지역 및 리소스 그룹을 오타 없이 정확하게 입력해야 합니다. 나머지 단계를 수행하여 구성을 완료한 다음 **만들기**를 클릭합니다.

선택한 Azure 자동화 계정으로 이동한 다음 **자산** - **모듈**로 이동하면 이제 1427개 활동이 있는 AWS 모듈을 볼 수 있습니다. 정말 놀랍죠!

>[AZURE.NOTE] PowerShell 모듈을 Azure 자동화로 가져오는 작업은 두 단계로 구성됩니다.
>
> 1.  모듈 가져오기
> 2.  cmdlet 추출
>
>모듈이 cmdlet 가져오기 및 추출 작업을 완료할 때까지 활동이 표시되지 않으며, 작업이 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

## AWS VM Runbook 프로비전

모든 필수 조건을 갖추었으니 이제 AWS에서 VM을 프로비전할 Runbook을 만들 시간입니다. PowerShell 워크플로 대신 네이티브 PowerShell 스크립트를 Azure 자동화에 활용하는 방법도 보여 드리겠습니다.

네이티브 PowerShell과 PowerShell 워크플로의 차이점은 [여기](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)를 참조하세요. 그래픽 Runbook을 만드는 방법은 [여기](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/)를 참조하세요.

Runbook에 사용할 PowerShell 스크립트는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript)에서 다운로드할 수 있습니다.

PowerShell 세션을 열고 **Save-Script -Name New-AwsVM -Path <path>**를 입력하여 스크립트를 다운로드할 있습니다.

PowerShell 스크립트를 저장했으면 다음 단계를 수행하여 Azure 자동화에 Runbook으로 추가할 수 있습니다.

**자동화 계정**에서 **Runbook**을 클릭하고 **Runbook 추가**를 선택합니다. **빠른 생성**(새 Runbook 만들기)을 선택하고, Runbook 이름을 입력하고, **Runbook 유형**에서 **PowerShell**을 선택합니다. 그런 다음 **만들기**를 클릭합니다.

![AWS Runbook 만들기](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "AWS Runbook 만들기")

Runbook 편집기가 열리면 PowerShell 스크립트를 복사하여 Runbook 작성 영역에 붙여 넣습니다.

![이미지](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "이미지")

다운로드한 PowerShell 스크립트를 사용할 때 다음 사항에 주의하세요.

-   Runbook은 #ToDo 섹션에서 언급했듯이 수많은 기본 매개 변수 값을 포함하고 있습니다. 모든 기본 값을 평가하고 필요한 경우 업데이트하세요.
-   **자산**에서 **AWScred**라는 이름으로 AWS 자격 증명을 저장했는지 확인하세요.
-   사용하려는 지역은 AWS 구독에 종속됩니다. 보안 자격 증명을 조회했던 AWS 포털에서 계정 옆에 있는 화살표를 클릭하여 지역을 확인하세요.

![AWS 지역](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "AWS 지역")

-   Runbook에서 [어떤 지역](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)을 제공해야 하는지 확인하세요. 예를 들어 제 지역은 미국 서부(오레곤)이고 us-west-2로 번환됩니다.

![AWS 지역](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "AWS 지역")

-   PowerShell ISE를 사용하고, 모듈을 가져오고, AWS에 대해 인증할 때 ISE 환경의 **Get-AutomationPSCredential**을 **$AwsCred = Get-Credential**로 대체하여 이미지 이름 목록을 가져올 수 있습니다. 자격 증명을 요청하는 메시지가 표시되면 사용자 이름과 암호로 **액세스 키 ID** 및 **보안 액세스 키**를 입력하면 됩니다. 아래 샘플을 참조하세요.


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


다음과 같이 출력될 것입니다.

![AWS 이미지 가져오기](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "AWS 이미지 가져오기")

원하는 이미지 이름을 복사하여 Runbook에서 **$InstanceType**으로 참조되는 자동화 변수에 붙여 넣습니다. 저는 무료 AWS 계층을 사용하기 때문에 제 Runbook에서 **t2.micro**를 선택했습니다.

Amazon EC2 인스턴스 유형에 대한 자세한 내용은 [여기](https://aws.amazon.com/ec2/instance-types/)를 참조하세요.

## AWS VM 프로비전 Runbook 테스트

실행 전 검사 목록:

-   AWS에 대한 인증 자산을 만들고 이름을 **AWScred**로 지정했습니다.
-   Azure 자동화에서 AWS PowerShell 모듈을 가져왔습니다.
-   새 Runbook을 만든 후 매개 변수 값을 확인하고 필요한 경우 업데이트했습니다.
-   Runbook 설정 아래에서 **상세 레코드 기록** 및 **진행률 레코드 기록**(옵션)을 **사용**으로 설정했습니다.

**VMname**을 입력하여 새로운 Runbook을 시작해 보겠습니다.

![New-AwsVM Runbook 시작](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "New-AwsVM Runbook 시작")

**상세 레코드 기록** 및 **진행률 레코드 기록**을 활성화했기 때문에 **모든 로그** 아래에서 출력 **스트림**을 볼 수 있습니다.

![스트림 출력](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "스트림 출력")

AWS에서 확인해 보겠습니다.

![AWS 콘솔에서 VM 배포](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "AWS 콘솔에서 VM 배포")

잘됐군요!

이 문서에서는 Amazon 웹 서비스에서 VM을 만들고 사용자 지정 태그를 적용하여 이름을 지정하는 등 Azure 자동화를 사용하여 하이브리드 클라우드를 관리하는 예를 보여 드렸습니다.

자동화를 즐겁게 이용하세요. 다음에 뵙겠습니다!

Tiander.

<!---HONumber=AcomDC_0204_2016-->