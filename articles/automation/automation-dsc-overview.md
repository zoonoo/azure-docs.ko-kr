<properties 
   pageTitle="Azure 자동화 DSC 개요 | Microsoft Azure" 
   description="Azure 자동화 DSC(필요한 상태 구성)의 개요, 용어 및 알려진 문제" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="09/24/2015"
   ms.author="coreyp"/>

# Azure 자동화 DSC 개요 #

## PowerShell DSC란? ##
DSC(필요한 상태 구성)는 선언적 PowerShell 구문을 사용하여 실제 호스트 및 가상 컴퓨터에 대한 구성을 관리할 수 있도록 해주는 Windows PowerShell의 새로운 관리 플랫폼입니다.

DSC는 Windows PowerShell 언어 확장, 새로운 Windows PowerShell cmdlet 및 소프트웨어 환경 구성 방법을 선언적으로 지정하는 데 사용할 수 있는 리소스 집합을 제공합니다. 또한 기존 구성을 유지 보수 및 관리할 수 있는 방법을 제공합니다.

### 유용한 응용 분야 ###
다음은 기본 제공 DSC 리소스를 사용하여 컴퓨터(대상 노드라고도 함) 집합을 자동화된 방식으로 구성 및 관리할 수 있는 몇 가지 예제 시나리오입니다.

- 서버 역할 및 기능 사용 여부 설정
- 레지스트리 설정 관리
- 파일 및 디렉터리 관리
- 프로세스 및 서비스 시작, 중지, 관리
- 그룹 및 사용자 계정 관리
- 새 소프트웨어 배포
- 환경 변수 관리
- Windows PowerShell 스크립트 실행
- 필요한 상태에서 변경된 구성 수정
- 지정된 노드에서 실제 구성 상태 검색

또한 사용자 지정 리소스를 만들어 모든 응용 프로그램 또는 시스템 설정의 상태를 구성할 수 있습니다.


PowerShell DSC에 대한 자세한 내용은 [DevOps 환경의 구성 - Windows PowerShell 필요한 상태 구성](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx)을 참조하세요.

##Azure 자동화 DSC란?##
Azure 자동화 DSC는 PowerShell DSC에 도입된 기본 사항을 기반으로 훨씬 간편한 구성 관리 환경을 제공합니다. Azure 자동화 DSC는 현재 Azure 자동화에서 PowerShell 스크립팅을 위해 제공하는 것과 동일한 관리 계층을 [PowerShell 필요한 상태 구성](https://technet.microsoft.com/library/dn249912.aspx)에 <link> 제공합니다.

Azure 자동화 DSC를 사용하면 클라우드에서 [PowerShell 필요한 상태 구성을 작성 및 관리](https://technet.microsoft.com/library/dn249918.aspx)하고, [DSC 리소스](https://technet.microsoft.com/library/dn282125.aspx)를 가져오고, DSC 노드 구성(MOF 문서)을 생성할 수 있습니다. 이러한 DSC 항목은 클라우드 또는 온-프레미스의 대상 노드(예: 실제 및 가상 컴퓨터)에서 선택하고, 해당 노드에서 지정한 필요한 상태를 자동으로 준수하며, 필요한 상태 준수를 Azure 자동화에 다시 보고할 수 있도록 Azure 자동화 [DSC 끌어오기 서버](https://technet.microsoft.com/library/dn249913.aspx)에 배치됩니다.

읽기보다 비디오를 시청하기가 편하신가요? 2015년 5월 Azure 자동화 DSC가 처음 발표될 당시의 비디오를 아래에서 확인하세요. **참고:** 이 비디오에 언급된 개념 및 수명은 정확하지만 이 비디오가 기록된 이후에 Azure 자동화 DSC는 많이 발전되었습니다. 현재 공개 미리 보기에 제공되는 Azure 포털의 UI는 보다 강력해졌으며 더 많은 기능을 제공하고 있습니다.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]

## Azure 자동화 DSC 용어 ##
### 구성 ###
PowerShell DSC에는 구성이라는 새로운 개념이 도입되었습니다. 구성에서는 PowerShell 구문을 통해 사용자 환경의 필요한 상태를 정의할 수 있습니다. DSC를 사용하여 환경을 구성하려면 먼저 구성 키워드를 사용하여 Windows PowerShell 스크립트 블록을 정의한 다음 그 뒤에 식별자와 블록을 구분하는 중괄호({})를 차례로 둡니다.

![대체 텍스트](./media/automation-dsc-overview/AADSC_1.png)

구성 블록 내에서 정확히 동일하게 구성해야 하는 사용자 환경의 노드(컴퓨터) 집합에 대한 필요한 구성을 지정하는 노드 구성 블록을 정의할 수 있습니다. 이러한 방식으로 노드 구성은 하나 이상의 노드에 대한 "역할"을 나타냅니다. 노드 구성 블록은 노드 키워드로 시작합니다. 이 키워드 뒤에는 역할 이름(변수 또는 식일 수 있음)이 있습니다. 역할 이름 뒤에는 노드 구성 블록을 구분하는 중괄호({})를 사용합니다.

![대체 텍스트](./media/automation-dsc-overview/AADSC_2.png)
 
노드 구성 블록 내에서 특정 DSC 리소스를 구성할 리소스 블록을 정의할 수 있습니다. 리소스 블록은 리소스 이름으로 시작하며, 그 뒤에 해당 블록에 대해 지정할 식별자와 블록을 구분하는 중괄호({})가 차례로 옵니다.

![대체 텍스트](./media/automation-dsc-overview/AADSC_3.png)

구성 키워드에 대한 자세한 내용은 [필요한 상태 구성의 구성 키워드 이해](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "필요한 상태 구성의 구성 키워드 이해")를 참조하세요.

DSC 구성을 실행(컴파일)하면 DSC 노드에서 필요한 상태를 준수하기 위해 적용하는 하나 이상의 DSC 노드 구성(MOF 문서)이 생성됩니다.

Azure 자동화 DSC를 사용하면 Azure 자동화에서 Runbook을 가져오고, 작성하고, 시작하는 것과 유사한 방법으로 DSC 구성을 가져오고, 작성하고, 컴파일할 수 있습니다.

>[AZURE.IMPORTANT]구성은 해당 구성과 이름과 동일한 구성 블록을 Azure 자동화 DSC에 하나 이상 포함해야 합니다.


###노드 구성###

DSC 구성이 컴파일되면 구성의 노드 블록에 따라 하나 이상의 노드 구성이 생성됩니다. 노드 구성은 “MOF” 또는 “구성 문서”(이러한 PS DSC 용어에 친숙한 경우)와 동일하며, 하나 이상의 노드에서 준수해야 하는 필요한 상태인 “역할”(예: 웹 서버 또는 작업자)을 나타냅니다. Azure 자동화 DSC의 노드 구성 이름은 “<Configuration-name>.<Node configuration-block-name>” 형식을 취합니다.

PS DSC 노드는 DSC 밀어넣기 또는 끌어오기 메서드를 통해 적용해야 하는 노드 구성을 인식하게 됩니다. Azure 자동화 DSC는 노드가 Azure 자동화 DSC 끌어오기 서버에서 적용해야 하는 노드 구성을 요청하는 DSC 끌어오기 메서드를 기반으로 합니다. 노드는 Azure 자동화 DSC에 요청하기 때문에 방화벽 뒤에 있을 수 있으며 모든 인바운드 포트가 닫혀 있을 수 있습니다. 인터넷에 대한 아웃바운드 액세스만 필요합니다.


###노드###

DSC 노드는 해당 구성이 DSC에 의해 관리되는 컴퓨터입니다. 이는 Azure VM 또는 온-프레미스 VM/실제 호스트일 수 있습니다. 노드는 해당 노드에서 정의한 필요한 상태를 준수하고 이를 유지 관리하기 위해 노드 구성을 적용하며, 해당 구성 상태 및 준수를 보고 서버에 다시 보고할 수 있습니다.

Azure 자동화 DSC는 Azure 자동화 DSC의 관리를 위한 노드 등록을 용이하게 하며, 각 노드의 서버 쪽에 할당된 노드 구성의 변경을 허용합니다. 따라서 다음에 노드가 서버에서 지침을 확인할 때는 다른 역할을 수행하고 일치하도록 구성하는 방법을 변경합니다. 또한 노드는 해당 상태 및 구성 준수를 Azure 자동화 DSC에 보고합니다.


###리소스###
DSC 리소스는 Windows PowerShell DSC(필요한 상태 구성) 구성을 정의하는 데 사용할 수 있는 구성 요소입니다. DSC는 파일 및 폴더, 서버 기능 및 역할, 레지스트리 설정, 환경 변수, 서비스 및 프로세스 등 리소스를 구성하기 위한 기본 제공 기능 집합과 함께 제공됩니다. 기본 제공 DSC 리소스의 전체 목록 및 이를 사용하는 방법에 대한 자세한 내용은 [기본 제공 Windows PowerShell 필요한 상태 구성 리소스](https://technet.microsoft.com/library/dn249921.aspx)를 참조하세요.

DSC 리소스를 PowerShell 모듈의 일부로 가져와 기본 제공 DSC 리소스 집합을 확장할 수도 있습니다. DSC 노드는 적용해야 하는 노드 구성에 기본이 아닌 리소스에 대한 참조가 포함된 경우 DSC 끌어오기 서버에서 이러한 리소스를 끌어올 수 있습니다. 사용자 지정 리소스를 만드는 방법에 대한 자세한 내용은 [사용자 지정 Windows PowerShell 필요한 상태 구성 리소스 빌드](https://technet.microsoft.com/library/dn249927.aspx)를 참조하세요.

Azure 자동화 DSC는 PS DSC와 모두 동일한 기본 제공 DSC 리소스와 함께 제공됩니다. 리소스가 포함된 PowerShell 모듈을 Azure 자동화로 가져와 Azure 자동화 DSC에 리소스를 추가할 수 있습니다.


###컴파일 작업###
Azure 자동화 DSC의 컴파일 작업은 하나 이상의 노드 구성을 만드는 구성 컴파일 인스턴스입니다. 이는 노드 구성을 만드는 것 외에는 실제로 아무 작업도 수행하지 않는다는 점을 제외하고는 Azure 자동화 Runbook 작업과 유사합니다. 컴파일 작업에서 만든 모든 노드 구성은 Azure 자동화 DSC 끌어오기 서버에 자동으로 배치되며, 이전 버전의 노드 구성(이 구성에 대해 존재하는 경우)을 덮어씁니다. 컴파일 작업에 의해 생성된 노드 구성의 이름은 "<Configuration-name>.<Node configuration-block-name>" 형식입니다. 예를 들어 아래의 구성을 컴파일하면 "MyConfiguration.webserver"라는 단일 노드 구성이 생성됩니다.


![대체 텍스트](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Runbook과 마찬가지로 구성을 게시할 수 있습니다. 이는 DSC 항목을 Azure 자동화 DSC 끌어오기 서버에 두는 것과 관련이 없습니다. 컴파일 작업을 수행하면 DSC 항목이 Azure 자동화 DSC 끌어오기 서버에 배치됩니다. Azure 자동화의 "게시"에 대한 자세한 내용은 [Runbook 게시](https://msdn.microsoft.com/library/dn903765.aspx)를 참조하세요.

Azure 자동화 DSC는 현재 [Azure 리소스 관리자 PowerShell 모듈](https://msdn.microsoft.com/library/mt244122.aspx)에서 다음과 같은 컴파일 작업 관리용 cmdlet을 제공합니다.

-	`Get-AzureAutomationDscCompilationJob`
-	`Get-AzureAutomationDscCompilationJobOutput`
-	`Start-AzureAutomationDscCompilationJob`

##Azure 자동화 DSC 수명 주기##
빈 자동화 계정부터 올바르게 구성된 관리 노드 집합까지에는 구성을 정의하고 해당 구성을 노드 구성으로 전환하며 온보딩 노드를 Azure 자동화 DSC 및 해당 노드 구성으로 전환하는 프로세스 집합이 포함됩니다. 다음 다이어그램에서는 Azure 자동화 DSC 수명 주기를 보여줍니다.

![대체 텍스트](./media/automation-dsc-overview/DSCLifecycle.png)


##알려진 문제##

- Azure 자동화 DSC는 현재 부분 또는 복합 DSC 구성을 지원하지 않습니다.

- Windows용 PowerShell DSC 에이전트가 Azure 자동화와 통신하려면 최신 버전의 WMF 5가 설치되어 있어야 합니다. Linux용 PowerShell DSC 에이전트는 현재 Azure 자동화와의 통신을 지원하지 않습니다. 이는 곧 업데이트될 예정입니다.

- Azure 자동화는 PowerShell 모듈과 함께 사용할 수 없습니다. 따라서 자동화 계정 내의 모든 구성은 해당 자동화 계정으로 가져온 최신 버전의 PowerShell 모듈 및 구성에서 사용하는 모듈이 포함된 PowerShell DSC 리소스를 사용해야 합니다.

- 기존 PowerShell DSC 끌어오기 서버의 경우 **ModuleName\_Version.zip”** 형식의 모듈 압축 파일이 있어야 합니다. Azure 자동화에서 **ModuleName.zip** 형식의 이름으로 PowerShell 모듈을 가져와야 합니다. Azure 자동화로 모듈을 가져오는 데 필요한 통합 모듈 형식에 대한 자세한 내용은 [이 블로그 게시물](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/)을 참조하세요.

- “폴더별 버전” 형식을 사용하여 모듈 내에서 DSC 리소스를 함께 사용하도록 지정하는 PowerShell 모듈은 현재 Azure 자동화에서 작동하지 않습니다.

- Azure 자동화로 가져온 PowerShell 모듈은 .doc 또는.docx 파일을 포함할 수 없습니다. DSC 리소스를 포함하는 일부 PowerShell 모듈은 도움말을 위해 이러한 파일을 포함하고 있습니다. Azure 자동화로 가져오기 전에 모듈에서 이러한 파일을 제거해야 합니다.

- Azure 자동화 계정에 노드를 처음 등록하거나 다른 노드 구성 서버 쪽에 매핑되도록 노드를 변경한 경우 노드의 상태가 현재 매핑된 노드 구성을 실제로 준수하지 않는 경우에도 해당 상태가 ‘준수’하는 것으로 표시됩니다. 노드에서 등록 후 또는 노드 구성 맵핑 변경 후 첫 번째 보고서를 보내면 노드 상태를 신뢰할 수 있습니다.

- `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` 또는 Azure Preview 포털의 Azure 자동화 DSC VM 확장을 사용하여 Azure 자동화 DSC로 관리할 Azure VM을 등록할 때 **컴퓨터 이름이 지정되지 않았으며 구성 디렉터리에 구성 파일이 없습니다**라는 등록 실패 메시지가 나타나는 경우 이는 잘못된 알림이며 실제로는 VM 등록에 성공한 것입니다. 성공적인 등록은 `Get-AzureAutomationDscNode` cmdlet을 사용하여 확인할 수 있습니다.

- `Register-AzureAutomationDscNode`, `Set-AzureVMExtension`을 사용하여 Azure 자동화 DSC를 사용한 관리를 위해 Azure VM을 온보딩하거나, Azure Preview 포털에서 Azure 자동화 DSC VM을 확장할 때, Azure 자동화의 DSC 노드로 VM이 나타날 때까지 최대 1시간 정도 걸립니다. 이것은 Azure 자동화 DSC의 VM을 온보드하기 위해 필요한 Azure VM DSC 확장에 의한 VM의 Windows 관리 프레임워크 5.0의 설치 때문에 꼭 필요한 일입니다.

- 등록 후에는 1년 후 만료되는 인증에 대해 각 노드가 자동으로 고유의 인증서를 협상합니다. 현재 PowerShell DSC 등록 프로토콜이 만료가 임박한 인증서를 자동으로 갱신할 수 없으므로 1년 기한 후 노드를 다시 등록해야 합니다. 다시 등록하기 전에 각 노드가 Windows Management Framework 5.0 RTM을 실행 중인지 확인합니다. 노드의 인증 인증서가 만료되고 노드가 다시 등록되지 않은 경우, 노드가 Azure 자동화와 통신할 수 없으며 '응답 없음'으로 표시됩니다. 재등록은 노드를 처음 등록할 때와 같은 방식으로 수행됩니다. 노드 만료 시점으로부터 90일 안이나, 인증서 만료 이후에 재등록을 수행하면 새 인증서를 생성하여 사용하게 됩니다.

##관련 문서##

- [Azure 자동화 DSC cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
- [Azure 자동화 DSC 가격 책정](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=Oct15_HO1-->