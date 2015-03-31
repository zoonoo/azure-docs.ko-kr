<properties 
	pageTitle="스크립팅 탄력적인 확장 및 스크립트" 
	description="PowerShell 및 Azure 자동화 서비스 Runbook을 사용하여 탄력적인 확장 작업의 스크립트를 작성합니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="Joseidz@microsoft.com"/>

# 스크립트를 통해 탄력적인 확장 관리


## Azure 자동화 서비스 

[Azure 자동화](http://azure.microsoft.com/documentation/services/automation/)는 Azure 플랫폼에 반드시 필요한 효율적인 PowerShell 워크플로 실행 서비스를 제공합니다. 이제 일반적인 Azure 포털 환경 내에서 어려운 유지 관리 작업을 자동화할 수 있습니다.  PowerShell 워크플로(Azure 자동화에서는 **Runbook**이라고 함)를 작성하고 클라우드에 업로드한 다음 원하는 Runbook 실행 시간을 예약하기만 하면 됩니다. 이 문서에서는 몇 가지 분할된 데이터베이스 예제에서 Azure 자동화의 전체 설정을 제공합니다. 자세한 내용은 [미리 보기 알림](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx)을 참조하거나 Azure [구독](https://account.windowsazure.com/PreviewFeatures?fid=automation)에 등록하세요.

이 예제에서는 Azure 자동화가 일정 및 작업 실행 프레임워크로 사용됩니다. Azure 자동화는 [클라우드의 SQL 에이전트](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/)로 생각하면 됩니다. 

문서 외에도 다음과 같은 리소스가 있습니다.

* [Azure 자동화 시작](http://azure.microsoft.com/documentation/articles/automation-create-runbook-from-samples/)
* [단계별 작업: 새 Microsoft Azure 자동화 미리 보기 기능 시작](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Microsoft Azure 자동화](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* Azure 자동화 관련 질문은 [자동화 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)에 문의하세요.  


## 필수 조건

Microsoft Azure 자동화 미리 보기 서비스에 [등록](http://azure.microsoft.com/services/preview/)하여 서비스 관련 사항을 [숙지](http://azure.microsoft.com/documentation/articles/automation-create-runbook-from-samples/)합니다. 


## 분할된 데이터베이스의 탄력성 PowerShell 파일

PowerShell 파일의 다음 집합은 Azure 자동화를 사용하는 수평적 및 수직적 크기 조정 시나리오를 수행하는 기본 명령을 포함합니다. 

다음 예제에서는 PowerShell 샘플 모듈을 사용하여 기본 분할된 데이터베이스 탄력성 작업을 수행하는 방법을 보여 줍니다. Microsoft Azure 자동화 서비스와 해당 Azure 자동화 Runbook을 함께 사용하면 규칙의 집합을 기반으로 새 분할된 데이터베이스를 프로비전하거나 특정 분할된 데이터베이스의 자동화 수준을 변경하는 자동화 및 예약된 작업을 만들 수 있습니다. 

**SetupShardedEnvironment.ps1**: 이 PowerShell Runbook은 분할된 데이터베이스 맵 관리자 및 범위 분할된 데이터베이스 맵과 함께 분할된 환경 전체를 한 번 설치합니다. 

**ProvisionByDate.ps1**: 예정된 날짜의 작업보다 먼저 새 데이터베이스를 프로비전합니다. 데이터베이스가 만들어지고 날짜 스탬프(YYYYMMDD)에 따라 이름이 지정되며 분할된 데이터베이스 맵 관리자에 [YYYYMMDD, YYYYMMDD + 1D) 범위로 등록됩니다. 

**ProvisionBySize.ps1**: 현재 데이터베이스 용량이 부족한 경우 새 데이터베이스를 프로비전합니다. 

**ReduceServiceTier.ps1**: 제공된 분할된 데이터베이스 맵의 분할된 데이터베이스를 반복하여 개별 분할된 데이터베이스의 성능 계층이 감소될 수 있는지 확인합니다. 분할된 데이터베이스의 성능 계층이 낮아질 수 있는지 여부를 확인하는 두 가지 조건은 1) 분할된 데이터베이스의 현재 서비스 계층 및 2) 데이터베이스의 보존 기간입니다.  

**ShardManagement.psm1**: 분할된 데이터베이스 맵 관리자를 조작하는 메서드 집합을 제공합니다. 

**SqlDatabaseHelpers.psm1**: Azure SQL 데이터베이스를 조작하는 메서드 집합을 제공합니다. 

**ShardElasticity.psm1**: 수평적 크기 조정뿐 아니라 수직적 크기 조정 작업을 수행하는 메서드 집합을 제공합니다. 

**ShardElasticityModule.psd1**: 탄력적인 확장 및 Azure SQL DB를 조작하는 메서드 집합을 제공합니다. 

## 비용

PowerShell 예제 스크립트를 실행하면 구독 소유자에게 실제 비용을 부과하는 데이터베이스가 생성됩니다. 기본 Azure SQL DB의 요금은 다른 Azure SQL DB 데이터베이스와 같은 [요율로 청구](http://azure.microsoft.com/pricing/details/sql-database/)됩니다.  11월 1일부터 시작하는 비용은 다음과 같습니다. 

* SetupShardedEnvironment Runbook은 기본 데이터베이스(시간당 8원)에서 분할된 데이터베이스 맵 관리자를 생성하고 기본 데이터베이스(시간당 8원)에서 첫 번째 분할된 데이터베이스도 프로비전합니다. 

* ProvisionBySize 및 ProvisionByDate Runbook은 모두 표준 S0 데이터베이스(시간당 23원)를 제공합니다.  이러한 비용을 반대로 정산하기 위해 ReduceServiceTier Runbook과 함께 실행하는 경우 새로 프로비전된 데이터베이스의 서비스 계층이 하루가 지난 후 Standard S0($0.0208/시간)에서 Basic($0.0069/시간)으로 감소됩니다. 

마지막으로, 제공된 예제의 범위 내에서 [Azure 자동화](http://azure.microsoft.com/pricing/details/automation/)를 사용해도 현재는 구독 소유자에게 비용이 청구되지 않습니다.  자세한 내용은 [Azure 자동화 가격 페이지](http://azure.microsoft.com/pricing/details/automation/)를 참조하세요. 

## Runbook을 로드하려면 

1. **ShardElasticity.zip** 파일을 다운로드하여 해당 내용을 추출합니다.
2. [NuGet을 사용하여 탄력적인 확장 이진 파일에 대한 참조를 추가합니다.](./sql-database-elastic-scale-add-references-visual-studio.md)
3. 탄력적인 확장 클라이언트 이진 파일(**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**)을 찾습니다.
4. ShardElasticityModule 폴더에 DLL을 저장하고 폴더를 압축합니다. 
3. Azure 자동화 계정에서 ShardElasticityModule.zip 파일을 **자산**으로 업로드합니다. 
4. Azure 자동화에서 Azure SQL 데이터베이스 서버의 사용자 이름 및 암호를 포함하는 **자산 자격 증명** *ElasticScaleCredential*을 만듭니다. 
5. 정규화된 Azure SQL 데이터베이스 서버 이름에 대한 **자산 변수** *SqlServerName*을 만듭니다. 
5. **SetupShardedEnvironment.ps1**, **ProvisionBySize.ps1**, **ProvisionByDate.ps1**, **ProvisionByDate.ps1**을 Runbook으로 업로드합니다. 
6. 일회성 작업으로 **SetupShardedEnvironment.ps1** Runbook을 테스트하여 분할된 환경을 설치합니다. 
7. 나머지 Runbook 중 하나 이상을 게시하고 Runbook을 일정에 연결합니다. 
8. **작업** 탭을 통해 Runbook의 출력을 검토합니다. 

빠른 예제 지침이 성공하지 못한 경우 아래의 자세한 예제 지침을 참조하세요.  

## Runbook을 사용하려면

1. PowerShell 모듈을 작성 및 패키지화합니다. 
2. Microsoft Azure 자동화 계정을 생성합니다. 
3. Azure 자동화에 PowerShell 모듈을 자산으로 업로드 
4. Azure 자동화 자격 증명 및 변수 자산을 생성합니다. 
5. Azure 자동화에 PowerShell Runbook을 업로드합니다. 
6. 분할된 환경을 설치합니다. 
7. 자동화 Runbook을 테스트합니다. 
8. Runbook을 게시합니다. 
9. Runbook을 예약합니다. 


## PowerShell 모듈을 작성 및 패키지화합니다. 

첫 번째 단계는 탄력적인 확장 어셈블리를 참조하는 PowerShell 모듈을 만들고 자산으로 Azure 자동화 서비스에 업로드할 수 있도록 이 모듈을 패키지화하는 것입니다. 

1. "ShardElasticity.zip" 파일을 다운로드합니다.
2. 모든 콘텐츠를 추출합니다.

    ![Extract all][1]
3. 탄력적인 확장 클라이언트 DLL(Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll)을 가져온 후 1단계에서 다운로드한 로컬 "ShardElasticityModule" 폴더에 다음 파일을 복사합니다.  이 작업은 두 가지 방법으로 수행할 수 있습니다. 그 중 하나는 탄력적인 확장 NuGet 패키지 [링크]를 통해 DLL을 다운로드하는 것이고, 다른 하나는 미리 작성된 탄력적인 확장 시작 키트 프로젝트 [링크]에서 \bin\Debug\로 이동하여 DLL을 가져오는 것입니다.

    ![Obtain Dll][2]

4. ShardElasticityModule 폴더를 압축합니다. 

    참고: Azure 자동화에서는 여러 가지 이름 규칙을 사용해야 합니다. 모듈 이름이 ShardElasticityModule.psm1인 경우 zip 파일 이름도 해당 파일과 정확히 일치하는 ShardElasticityModule.zip이어야 합니다. Zip 파일에는 psm1 파일을 포함하는 ShardElasticityModule(모듈의 이름과 일치하는 이름) 폴더가 포함됩니다. 이 구조를 따르지 않으면 Azure 자동화가 모듈 압축을 풀 수 없습니다.

5.    압축 폴더의 구조와 내용이 요구 사항과 일치하는지 확인한 후 다음 단계를 진행합니다. 이 폴더의 구조는 다음과 같아야 합니다.

    ![dll][3]


## Azure 자동화 미리 보기에 등록

1. [Azure 미리 보기 기능](http://azure.microsoft.com/services/preview/)으로 이동합니다.
    
2. **사용해 보세요.**를 클릭합니다.

    ![Click Try It][8]

2. [Microsoft Azure 포털](https://manage.windowsazure.com/microsoft.onmicrosoft.com)로 이동합니다.
3. **자동화**를 클릭합니다.

    ![Automation][4]
4. 화면 아래쪽에서 **만들기**를 클릭합니다. 
5. 아래에 표시된 프롬프트에서 유효한 계정 이름을 입력하고 상자의 오른쪽 아래에서 확인란을 클릭합니다.

    ![Prompt][5] 
5. 다음 단계를 진행합니다. 작업이 정상적으로 진행되면 아래 그림과 같은 화면이 표시됩니다.

    ![success][6]

## Azure 자동화에 PowerShell 모듈을 자산으로 업로드 

위쪽의 PowerShell 모듈을 Azure 자동화 계정에 업로드합니다. 예를 들어 모듈에는 분할된 데이터베이스 탄력성 기능 및 Runbook에서 참조될 수 있는 탄력적인 확장 DLL의 집합이 포함됩니다. 

1. 화면 위쪽의 리본에서 **자산**을 클릭합니다.
2. 페이지 아래쪽에서 **모듈 가져오기**를 클릭합니다. 
3. **파일 찾아보기...**를 클릭하고 위쪽에서 **ShardElasticityModule.zip** 파일을 찾습니다. 
4. 올바른 파일을 선택한 후 상자 오른쪽 아래의 확인란을 클릭하여 파일을 가져옵니다. Azure 자동화 서비스가 모듈을 가져옵니다. 
5. 다음 단계를 진행합니다. 성공 시 다음과 유사한 그래픽이 표시됩니다. 모듈을 가져오지 못한 경우에는 zip 파일이 위에서 설명한 규칙을 따르는지 확인하세요.

    ![Assets][10] 
      
## Azure 자동화 자격 증명 및 변수 자산을 생성합니다. 

하드 코딩 자격 증명 및 Runbook에 대한 자주 사용되는 변수 대신, Azure 자동화는 여러 Runbook 간에 참조할 수 있는 자격 증명 및 변수 자산을 만들 수 있습니다. 예를 들어 암호 변경은 한 위치에서만 발생합니다. 

1. 방금 만든 새 Azure 자동화 계정을 선택합니다.
2. **ShardElasticityExamples** 계정에서 리본의 **자산**을 클릭합니다.
3. 화면 아래쪽의 **설정 추가**를 클릭합니다.  
4. **자격 증명 추가**를 클릭합니다. 

    ![Add credential][9]
4. **자격 증명 유형**으로 **Windows PowerShell 자격 증명**을 선택하고 이름으로 **ElasticScaleCredential**을 선택합니다. 설명은 선택 사항입니다.  
5. 상자 오른쪽 아래의 화살표를 클릭합니다. 

    참고: 수정하지 않고 Rubook을 사용하려면 변수 이름을 지침에 제공된 그대로 정확하게 사용합니다. 변수 이름은 Runbook에서 참조됩니다. 
5. 분할된 데이터베이스 탄력성 예제를 실행하려는 Azure SQL DB 서버의 사용자 이름 및 암호를 두 번 입력합니다. 
 
6. 변수 자산을 만들려면 **설정 추가**를 클릭한 다음 **변수 추가**를 선택합니다. 

    ![Add variable][7]
7. 이 자습서에서는 분할된 데이터베이스 맵 관리자 및 분할된 데이터베이스가 상주하는 정규화된 Azure SQL DB 서버 이름에 대한 변수를 만듭니다. **변수 유형**으로 **문자열**을 선택하고 **SqlServerName**을 입력합니다. 화살표를 클릭하여 진행합니다. 
8. 정규화된 Azure SQL DB 서버 이름을 값으로 입력하고 확인 표시를 클릭합니다. 
9. 이제 분할된 데이터베이스 탄력성 Runbook에 사용되는 자격 증명 및 변수 자산을 만들었습니다.  다음 단계를 진행합니다. 성공 시 다음과 같은 그래픽이 표시됩니다. 
    

## Azure 자동화에 PowerShell Runbook을 업로드합니다. 

제공된 4개의 예제 PowerShell Runbook을 업로드합니다. 

1. 새 Azure 자동화 Runbook을 업로드하려면 리본에서 **Runbook**을 클릭합니다. 
2. 화면 아래쪽에서 **가져오기**를 클릭합니다. 
3. 파일이 있는 폴더로 이동한 다음 **SetupShardedEnvironment.ps1**을 선택하고 확인 표시를 클릭합니다. 
4. 남은 3개의 PowerShell Runbook(**ProvisionByDate.ps1**, **ProvisionBySize.ps1**, **ReduceServiceTier.ps1**)에 대해 2단계와 3단계를 반복합니다. 
5. 다음 단계를 진행합니다. 

## 분할된 환경을 설치합니다. 

다음 단계에서는 **SetupShardedEnvironment** Runbook을 실행합니다. 이 Runbook은 분할된 환경을 프로비전하며, 프로비전이 완료되면 분할된 데이터베이스 맵 DB, 범위 분할된 데이터베이스 맵 및 현재 날짜의 분할된 데이터베이스가 생성됩니다.   

1. 이름을 클릭하여 **SetupShardedEnvironment** Runbook을 선택합니다. 
2. 리본에서 **작성**을 클릭합니다. 
3. 이 화면에 Runbook을 구성하는 코드가 표시됩니다. 원하는 경우에는 이 코드를 편집할 수도 있습니다. 분할된 환경을 설정하려면 화면 아래쪽에서 **테스트** 단추를 클릭합니다. Runbook을 저장하고 테스트할 것임을 확인합니다. 
4. 출력 창에서 작업 상태를 확인할 수 있습니다. 작업이 완료되면 지정한 Azure SQL DB 서버에 분할된 데이터베이스 맵 관리자 데이터베이스와 분할된 데이터베이스가 저장됩니다. **SetupShardedEnvironment** Runbook은 분할된 환경 프로비전만 수행하며 되풀이 일정으로 실행할 수는 없습니다. 다음 단계를 진행합니다.  

## 자동화 Runbook 테스트 

Runbook을 게시 및 예약하기 전에 각 Runbook의 성공적인 실행을 테스트합니다. 

1. 페이지 위쪽의 리본에서 **Runbook**을 클릭합니다. 
2. **ProvisionByDate** Runbook을 클릭합니다.
3. 페이지 위쪽의 리본에서 **작성**을 클릭합니다. 
4. **저장**과 **테스트**를 차례로 클릭합니다.
5. **ReduceServiceTier**에 대해 이 작업을 반복합니다. 

    **ProvisionBySize** 및 **ProvisionByDate**은 각기 다른 알고리즘을 사용하여 새 분할된 데이터베이스를 프로비전하므로 지금은 **ProvisionByDate**를 실행할 필요가 없습니다. 

## Runbook 게시 
다음 단계는 정기적으로 실행을 예약할 수 있도록 Runbook을 게시하는 것입니다. 

1. 페이지 아래쪽에서 **게시**를 클릭합니다. 
2. **게시됨**을 클릭합니다. 
3. 다음 단계를 진행합니다.
 
## Runbook 예약 

마지막 단계는 일정을 만들어 위에서 게시된 Runbook에 연결하는 것입니다. 

1. 페이지 위쪽에서 **일정**을 클릭합니다. 
2. **새 일정에 연결**을 클릭합니다.
3. 일정의 이름을 적절히 지정하고 오른쪽 화살표 단추를 클릭합니다.
4. 일정을 구성합니다.
5. 완료되면 상자의 아래쪽에 있는 확인 표시를 클릭합니다.
6. 작업이 이전에 설정된 일정에 따라 실행되면 페이지 위쪽의 리본에서 **작업** 옵션을 클릭한 다음 예약된 작업을 선택합니다. 

## 결론 

이제 PowerShell 모듈을 성공적으로 작성 및 패키지화하고, Azure 자동화에 자산으로 PowerShell 모듈을 업로드하고, Runbook을 생성, 테스트, 게시 및 예약했습니다.  Runbook의 상태를 모니터하려면 대시보드 및 작업 탭을 사용합니다. 

제공된 예제는 탄력적인 확장, Azure SQL DB 및 Azure 자동화와 결합될 때 가능한 작업 중 일부일 뿐입니다. 이러한 예제를 실험하고 기반으로 사용하여 Azure SQL DB의 탄력적인 확장 응용 프로그램에서 수평적, 수직적 또는 두 가지 모두의 크기 조정을 수행할 수 있습니다. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-scripting/assets.png

<!--HONumber=47-->
