<properties 
   pageTitle="Azure 자동화에서 Runbook 실행"
   description="Azure 자동화의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="bwren" />

# Azure 자동화에서 Runbook 실행


Azure 자동화에서 Runbook을 시작하면 작업이 생성됩니다. 작업은 Runbook의 단일 실행 인스턴스입니다. 각 작업을 실행하기 위해 Azure 자동화 작업자가 할당됩니다. 작업자는 여러 Azure 계정에서 공유하지만 여러 자동화 계정의 작업은 서로 격리됩니다. 사용자는 작업에 대한 요청을 처리할 작업자를 제어할 수 없습니다. 단일 Runbook에서 동시에 여러 작업을 실행할 수 있습니다. Azure 포털에서 Runbook 목록을 확인하면 각 Runbook에 대해 시작된 마지막 작업의 상태가 나열됩니다. 각 Runbook에 대한 작업 목록을 확인하여 각 작업의 상태를 추적할 수 있습니다. 다양한 작업 상태에 대한 설명은 [작업 상태](automation-viewing-the-status-of-a-runbook-job.md#job-statuses)를 참조하세요.

![작업 상태](./media/automation-runbook-execution/job-statuses.png)


Azure 구독에 연결하면 작업에서 Azure 리소스에 액세스할 수 있습니다. 단, 공용 클라우드에서 액세스할 수 있는 데이터 센터의 리소스에만 액세스할 수 있습니다.

## 권한

Azure 자동화의 Runbook은 일반적으로 Azure 구독의 리소스에 액세스할 수 있어야 합니다. [Azure Active Directory를 사용하여 Azure 인증](http://aka.ms/runbookauthor/authentication)에서는 Azure 리소스를 인증하기 위해 Azure 자동화에서 Azure Active Directory 및 [자격 증명]()을 구성하는 방법에 대해 설명합니다. 이 항목에서는 [Add-AzureAccount cmdlet](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount)을 사용하여 사용자가 만든 Runbook에서 Azure 리소스에 액세스하는 방법에 대한 정보도 제공합니다. Runbook의 보안 요구 사항은 가져오는 개별 Runbook에 대한 설명서를 참조하세요.

## 공평 분배

Azure 자동화에서는 클라우드의 모든 Runbook 간에 리소스를 공유할 수 있도록 작업을 실행한 후 3시간 동안 작업을 일시적으로 언로드한 다음 마지막 [검사점](http://aka.ms/runbookauthor/checkpoints)에서 다시 시작합니다. 이 시간 동안 작업은 실행 중, 리소스 대기 상태로 표시됩니다. Runbook에 검사점이 없거나 작업이 언로드되기 전에 첫 번째 검사점에 도달하지 않은 경우에는 처음부터 다시 시작됩니다.

Runbook이 동일한 검사점 또는 Runbook의 처음부터 세 번 연속 다시 시작된 경우에는 실패함, 리소스 대기 상태로 종료됩니다. 이는 Runbook이 완료되지 않고 무기한적으로 실행되어 다시 언로드되지 않은 상태로 다음 검사점에 도달하지 못하도록 하기 위한 것입니다. 이 경우 오류와 함께 다음 예외가 발생합니다.

작업이 동일한 검사점에서 반복적으로 제거되었기 때문에 실행을 계속할 수 없습니다. Runbook이 해당 상태를 유지하지 않고 시간이 오래 걸리는 작업을 수행하지 않는지 확인하세요.

Runbook을 만들 때 두 검사점 간의 모든 활동을 실행할 시간을 3시간을 초과하지 않도록 해야 합니다. 이 3시간 제한에 도달하지 않도록 하기 위해 Runbook에 검사점을 추가해야 할 수도 있습니다. 또한 오래 실행되는 작업을 나누어야 할 수도 있습니다. 예를 들어 Runbook에서 대용량 SQL 데이터베이스의 인덱스를 다시 작성할 수 있습니다. 이 단일 작업이 공평 분배 제한 내에 완료되지 않으면 작업이 언로드되고 처음부터 다시 시작됩니다. 이 경우 다시 인덱싱 작업을 여러 단계로 나눈 다음(예: 한 번의 하나의 테이블을 다시 인덱싱하도록) 작업이 완료할 마지막 작업 이후에 다시 시작될 수 있도록 각 작업 뒤에 검사점을 삽입해야 합니다.

## 관련된 문서

- [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook)
- [Azure 자동화에서 Runbook 작업의 상태 보기](automation-viewing-the-status-of-a-runbook-job)

<!---HONumber=58--> 