---
title: "계산 노드에 응용 프로그램 패키지 설치 - Azure 배치 | Microsoft Docs"
description: "Azure 배치의 응용 프로그램 패키지 기능을 사용하여 배치 계산 노드에 설치할 여러 응용 프로그램 및 버전을 간편하게 관리하세요."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: 9c7073e55b98406fc8f9db9a40bf1a6ffc626f47
ms.contentlocale: ko-kr
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>응용 프로그램을 배포하여 Batch 응용 프로그램 패키지에서 노드 계산

Azure 배치의 응용 프로그램 패키지 기능은 풀의 계산 노드에 태스크 응용 프로그램 및 해당 배포를 간편하게 관리할 수 있는 방법을 제공합니다. 응용 프로그램 패키지를 사용하여 지원 파일을 비롯하여 태스크에서 실행하는 여러 응용 프로그램 버전을 업로드하고 관리할 수 있습니다. 풀의 계산 노드에 이러한 응용 프로그램을 하나 이상 자동으로 배포할 수 있습니다.

이 문서에서는 Azure 포털을 사용하여 응용 프로그램 패키지를 업로드하고 관리하는 방법을 알아봅니다. [Batch .NET][api_net] 라이브러리를 사용하여 풀의 계산 노드에 설치하는 방법을 알아봅니다.

> [!NOTE]
> 여기에서 설명하는 응용 프로그램 패키지 기능은 서비스의 이전 버전에 제공되는 "배치 앱" 기능을 대체합니다.
> 
> 

## <a name="application-package-requirements"></a>응용 프로그램 패키지 요구 사항
응용 프로그램 패키지를 사용하려면 [Azure Storage 계정](#link-a-storage-account) 을 배치 계정에 연결해야 합니다.

이 문서에서 설명하는 응용 프로그램 패키지 기능은 2016년 3월 10일 이후에 만들어진 배치 풀하고 *만* 호환됩니다. 이 날짜 전에 만들어진 풀의 계산 노드에는 응용 프로그램 패키지가 배포되지 않습니다.

이 기능은 [Batch REST API][api_rest] 버전 2015-12-01.2.2 및 해당 [Batch .NET][api_net] 라이브러리 버전 3.1.0에서 도입되었습니다. 배치를 사용할 때에는 항상 최신 API 버전을 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 현재 *CloudServiceConfiguration* 풀만 응용 프로그램 패키지를 지원합니다. VirtualMachineConfiguration 이미지를 사용하여 만든 풀에는 응용 프로그램 패키지를 사용할 수 없습니다. 두 개의 서로 다른 구성에 대한 자세한 내용은 [Azure Batch 풀에서 Linux 계산 노드 프로비전](batch-linux-nodes.md)의 [가상 컴퓨터 구성](batch-linux-nodes.md#virtual-machine-configuration) 섹션을 참조하세요.
> 
> 

## <a name="about-applications-and-application-packages"></a>응용 프로그램 및 응용 프로그램 패키지 정보
Azure 배치 내에서 *응용 프로그램* 이란 풀의 계산 노드에 자동으로 다운로드할 수 있는 버전이 지정된 이진 파일의 집합을 말합니다. *응용 프로그램 패키지*는 이러한 이진 파일의 *특정 집합*을 말하며, 지정된 응용 프로그램 *버전*을 나타냅니다.

![응용 프로그램 및 응용 프로그램 패키지에 대한 개략적인 다이어그램][1]

### <a name="applications"></a>응용 프로그램
배치에서 응용 프로그램은 하나 이상의 응용 프로그램을 포함하거나 응용 프로그램에 대한 구성 옵션을 지정합니다. 예를 들어 응용 프로그램은 계산 노드에 설치할 기본 응용 프로그램 패키지 버전, 응용 프로그램 패키지를 업데이트할지 아니면 삭제할 것인지를 지정할 수 있습니다.

### <a name="application-packages"></a>응용 프로그램 패키지
응용 프로그램 패키지는 태스크가 응용 프로그램을 실행하는 데 필요한 응용 프로그램 이진 파일 및 지원 파일이 포함된 .zip 파일입니다. 각 응용 프로그램 패키지는 응용 프로그램의 특정 버전을 나타냅니다.

풀 및 태스크 수준에서 응용 프로그램 패키지를 지정할 수 있습니다. 풀 또는 태스크를 만들 때 이러한 패키지 및 버전(선택 사항)을 하나 이상 지정할 수 있습니다.

* **풀 응용 프로그램 패키지** 는 *모든* 노드에 배포됩니다. 응용 프로그램은 노드가 풀을 조인하고 재부팅되거나 이미지를 다시 설치할 때 배포됩니다.
  
    풀 응용 프로그램 패키지는 풀에 있는 모든 노드가 작업의 태스크를 실행할 때 적합합니다. 풀을 만들 때 하나 이상의 응용 프로그램 패키지를 지정할 수 있으며 기존 풀의 패키지를 추가하거나 업데이트할 수 있습니다. 기존 풀의 응용 프로그램 패키지를 업데이트하는 경우 새 패키지를 설치하려면 해당 노드를 다시 설치해야 합니다.
* **태스크 응용 프로그램 패키지** 는 태스크의 명령줄을 실행하기 바로 전에 태스크를 실행하도록 예약된 계산 노드에만 배포됩니다. 지정된 응용 프로그램 패키지 및 버전이 노드에 이미 있는 경우 다시 배포되지 않으며 기존 패키지가 사용됩니다.
  
    태스크 응용 프로그램 패키지는 공유 풀 환경에서 유용하며 여기서는 다른 작업이 하나의 풀에서 실행되고 작업이 완료될 때 풀이 삭제되지 않습니다. 작업에 있는 태스크가 풀에 있는 노드보다 적은 경우 태스크를 실행하는 노드에만 응용 프로그램을 배포하므로 태스크 응용 프로그램 패키지는 데이터 전송을 최소화할 수 있습니다.
  
    태스크 응용 프로그램 패키지를 활용할 수 있는 다른 시나리오는 특별히 큰 응용 프로그램을 사용하지만 태스크 수는 적은 작업입니다. 예를 들어, 전처리 또는 병합 응용 프로그램이 무거운 전처리 단계 또는 병합 태스크입니다.

> [!IMPORTANT]
> 배치 계정의 응용 프로그램 및 응용 프로그램 패키지 수와 응용 프로그램 패키지의 최대 크기에 대한 제한이 있습니다. 이러한 제한에 대한 자세한 내용은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md) 을 참조하세요.
> 
> 

### <a name="benefits-of-application-packages"></a>응용 프로그램 패키지의 이점
응용 프로그램 패키지는 배치 솔루션의 코드를 단순화하고, 태스크에서 실행하는 응용 프로그램을 관리하는 데 필요한 오버헤드를 낮출 수 있습니다.

풀의 시작 태스크가 노드에 설치할 긴 개별 리소스 파일 목록을 지정할 필요가 없습니다. Azure Storage 또는 노드에서 응용 프로그램 파일의 여러 버전을 수동으로 관리할 필요가 없습니다. 저장소 계정의 파일에 대한 액세스 권한을 제공하기 위해 [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) 을 생성할 필요가 없습니다. 배치는 Azure Storage와 함께 백그라운드에서 작동하여 응용 프로그램 패키지를 저장하고 계산 노드에 배포합니다.

## <a name="upload-and-manage-applications"></a>응용 프로그램 업로드 및 관리
[Azure 포털][portal] 또는 [Batch 관리 .NET](batch-management-dotnet.md)라이브러리를 사용하여 Batch 계정에서 응용 프로그램 패키지를 관리합니다. 다음 섹션에서는 먼저 저장소 계정을 연결한 후 응용 프로그램 및 패키지를 추가하고 포털에서 관리하는 방법을 설명합니다.

### <a name="link-a-storage-account"></a>저장소 계정 연결
응용 프로그램 패키지를 사용하려면 먼저 Azure Storage 계정을 배치 계정에 연결해야 합니다. 아직 배치 계정에 대해 저장소 계정을 구성하지 않은 경우 **배치 계정** 블레이드에서 처음으로 **응용 프로그램** 타일을 클릭하면 Azure Portal에서 경고를 표시합니다.

> [!IMPORTANT]
> 배치는 현재 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)의 5단계 [저장소 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account)에서 설명한 대로 **범용** 저장소 계정 유형*만* 지원합니다. Azure Storage 계정을 배치 계정에 연결할 경우 *범용* 저장소 계정 **만** 연결합니다.
> 
> 

![저장소 계정이 구성되지 않았다는 Azure 포털의 경고][9]

배치 서비스는 연결된 저장소 계정을 사용하여 응용 프로그램 패키지를 저장 및 검색합니다. 계정 두 개를 연결한 후에 배치는 연결된 저장소 계정에 저장된 패키지를 계산 노드에 자동으로 배포할 수 있습니다. **경고** 블레이드에서 **저장소 계정 설정**을 클릭한 다음 **저장소 계정** 블레이드에서 **저장소 계정**을 클릭하여 저장소 계정을 배치 계정에 연결합니다.

![Azure 포털에서 저장소 계정 블레이드 선택][10]

*특별히* 배치 계정과 함께 사용할 저장소 계정을 만든 후 여기에서 선택하는 것이 좋습니다. 저장소 계정을 만드는 방법에 대한 자세한 내용은 [Azure Storage 계정 정보](../storage/storage-create-storage-account.md)의 "저장소 계정 만들기"를 참조하세요. 저장소 계정을 만든 후에 **저장소 계정** 블레이드를 사용하여 배치 계정에 연결할 수 있습니다.

> [!WARNING]
> Batch는 Azure Storage를 사용하여 응용 프로그램 패키지를 저장하기 때문에 블록 Blob 데이터에 대한 [요금이 정상적으로 청구][storage_pricing]됩니다. 비용을 최소화할 수 있도록 응용 프로그램 패키지의 크기와 숫자에 신경 쓰고, 사용하지 않는 패키지를 주기적으로 제거해 주세요.
> 
> 

### <a name="view-current-applications"></a>현재 응용 프로그램 보기
배치 계정의 응용 프로그램을 보려면 **배치 계정** 블레이드 왼쪽에 있는 메뉴의 **응용 프로그램** 메뉴 항목을 클릭합니다.

![응용 프로그램 타일][2]

**응용 프로그램** 블레이드가 열립니다.

![응용 프로그램 나열][3]

**응용 프로그램** 블레이드는 계정의 각 응용 프로그램 ID 및 다음 속성을 표시합니다.

* **패키지**--이 응용 프로그램과 연결된 버전 번호입니다.
* **기본 버전**--풀에 대해 응용 프로그램을 설정할 때 버전을 지정하지 않으면 이 버전이 설치됩니다. 이 설정은 선택 사항입니다.
* **업데이트 허용**--패키지의 업데이트, 삭제 및 추가 기능을 허용하는지 여부를 지정하는 값입니다. 이 값을 **아니요**로 설정하면 응용 프로그램에 패키지 업데이트 및 삭제를 사용할 수 없습니다. 새 응용 프로그램 패키지 버전만 추가할 수 있습니다. 기본값은 **예**입니다.

### <a name="view-application-details"></a>응용 프로그램 세부 정보 보기
**응용 프로그램** 블레이드에서 응용 프로그램을 클릭하면 해당 응용 프로그램에 대한 세부 정보 블레이드가 열립니다.

![응용 프로그램 세부 정보][4]

응용 프로그램 세부 정보 블레이드에서 응용 프로그램에 대해 다음 설정을 구성할 수 있습니다.

* **업데이트 허용**--해당 응용 프로그램 패키지를 업데이트하거나 삭제할 수 있는지를 지정합니다. 이 문서에서 아래의 "응용 프로그램 패키지 업데이트 또는 삭제"를 참조하세요.
* **기본 버전**--계산 노드에 배포할 기본 응용 프로그램 패키지를 지정합니다.
* **표시 이름**--배치를 통해 고객에게 제공하는 서비스 UI처럼, 배치 솔루션이 응용 프로그램에 대한 정보를 표시할 때 사용할 수 있는 "친숙한" 이름을 지정합니다.

### <a name="add-a-new-application"></a>새 응용 프로그램 추가
새 응용 프로그램을 만들려면 응용 프로그램 패키지를 추가하고 고유한 새 응용 프로그램 ID를 지정합니다. 새 응용 프로그램 ID를 사용하여 추가하는 첫 번째 응용 프로그램 패키지도 새 응용 프로그램을 만듭니다.

**응용 프로그램** 블레이드에서 **추가**를 클릭하여 **새 응용 프로그램** 블레이드를 엽니다.

![Azure 포털의 새 응용 프로그램 블레이드][5]

**새 응용 프로그램** 블레이드는 새 응용 프로그램 및 응용 프로그램 패키지의 설정을 지정하는 다음 필드를 제공합니다.

**응용 프로그램 ID**

이 필드는 새 응용 프로그램의 ID를 지정하며, 표준 Azure 배치 ID 유효성 검사 규칙을 따릅니다.

* 하이픈과 밑줄을 포함하여 모든 영숫자 문자 조합을 포함할 수 있습니다.
* 포함할 수 있는 최대 문자는 64자입니다.
* 배치 계정 내에서 고유해야 합니다.
* 대/소문자를 유지하고 대/소문자를 구분합니다.

**버전**

사용자가 업로드하는 응용 프로그램 패키지의 버전을 지정합니다. 버전 문자열은 다음 유효성 검사 규칙을 따릅니다.

* 하이픈, 밑줄, 마침표를 포함하여 모든 영숫자 문자 조합을 포함할 수 있습니다.
* 포함할 수 있는 최대 문자는 64자입니다.
* 응용 프로그램 내에서 고유해야 합니다.
* 대/소문자를 유지하고, 대/소문자를 구분합니다.

**응용 프로그램 패키지**

이 필드는 응용 프로그램 실행에 필요한 응용 프로그램 이진 파일 및 모든 지원 파일을 포함하는 .zip 파일을 지정합니다. **파일 선택** 상자 또는 폴더 아이콘을 클릭하여 응용 프로그램의 파일이 포함된 .zip 파일을 찾고 선택합니다.

파일을 선택했으면 **확인** 을 클릭하여 Azure Storage에 업로드를 시작합니다. 업로드 작업이 완료되면 알림이 표시되고 블레이드가 닫힙니다. 업로드하는 파일의 크기 및 네트워크 연결 속도에 따라 작업 시간이 달라질 수 있습니다.

> [!WARNING]
> 업로드 작업이 완료되기 전에 **새 응용 프로그램** 블레이드를 닫지 마세요. 만약 닫으면 업로드 프로세스가 중지됩니다.
> 
> 

### <a name="add-a-new-application-package"></a>새 응용 프로그램 패키지 추가
기존 응용 프로그램에 대한 새 응용 프로그램 패키지 버전을 추가하려면 **응용 프로그램** 블레이드에서 응용 프로그램을 선택하고, **패키지**를 클릭한 다음 **추가**를 클릭하여 **패키지 추가** 블레이드를 엽니다.

![Azure 포털의 응용 프로그램 패키지 추가 블레이드][8]

여기서 볼 수 있듯이 필드가 **새 응용 프로그램** 블레이드의 필드와 일치하지만 **응용 프로그램 ID** 상자는 비활성화되어 있습니다. 새 응용 프로그램에서 수행한 대로 새 패키지의 **버전**을 지정하고, **응용 프로그램 패키지** .zip 파일을 찾은 다음 **확인**을 클릭하여 패키지를 업로드합니다.

### <a name="update-or-delete-an-application-package"></a>응용 프로그램 패키지 업데이트 또는 삭제
기존 응용 프로그램 패키지를 업데이트하거나 삭제하려면 해당 응용 프로그램의 세부 정보 블레이드를 연 다음 **패키지**를 클릭하여 **패키지** 블레이드를 열고, 수정하려는 응용 프로그램 패키지의 행에서 **...(줄임표)**를 클릭하여 수행할 작업을 선택합니다.

![Azure 포털에서 패키지 업데이트 또는 삭제][7]

**업데이트**

**업데이트**를 클릭하면 *패키지 업데이트* 블레이드가 표시됩니다. 이 블레이드는 *새 응용 프로그램 패키지* 블레이드와 비슷하지만, 패키지 선택 필드만 활성화되므로 업로드할 새 ZIP 파일을 지정할 수 있습니다.

![Azure 포털의 패키지 업데이트 블레이드][11]

**삭제**

**삭제**를 클릭하면 패키지 버전을 삭제할 것인지 묻는 메시지가 나타나고, 배치가 Azure Storage에서 패키지를 삭제합니다. 응용 프로그램의 기본 버전을 삭제하면 해당 응용 프로그램의 **기본 버전** 설정이 제거됩니다.

![응용 프로그램 삭제][12]

## <a name="install-applications-on-compute-nodes"></a>계산 노드에 응용 프로그램 설치
Azure 포털을 사용하여 응용 프로그램 패키지를 관리하는 방법을 살펴보았으며 응용 프로그램 패키지를 계산 노드에 배포하고 배치 작업을 사용하여 실행하는 방법에 대해 설명할 수 있습니다.

### <a name="install-pool-application-packages"></a>풀 응용 프로그램 패키지 설치
풀의 모든 계산 노드에 응용 프로그램 패키지를 설치하려면 풀에 대해 응용 프로그램 패키지 *참조* 를 하나 이상 지정해야 합니다. 노드가 풀에 조인되거나 재부팅 또는 이미지로 다시 설치되는 경우 풀에 대해 지정하는 응용 프로그램 패키지는 각 계산 노드에 설치됩니다.

Batch .NET에서, 새 풀을 만들 때 또는 기존 풀에 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]를 하나 이상 지정합니다. [ApplicationPackageReference][net_pkgref] 클래스는 풀의 계산 노드에 설치할 응용 프로그램 ID 및 버전을 지정합니다.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 어떤 이유로든 응용 프로그램 패키지 배포가 실패하면 Batch 서비스에서는 노드를 [사용할 수 없으며][net_nodestate] 해당 노드에서 실행하기로 예정된 작업이 없다고 표시합니다. 이 경우에 노드를 **다시 시작** 하여 패키지 배포를 다시 시작해야 합니다. 또한 노드를 다시 시작하면 노드에서 작업을 다시 예약할 수 있습니다.
> 
> 

### <a name="install-task-application-packages"></a>태스크 응용 프로그램 패키지 설치
풀과 마찬가지로 태스크에 대해 응용 프로그램 패키지 *참조* 를 지정합니다. 노드에서 실행하도록 태스크가 예약된 경우 태스크의 명령줄이 실행되기 바로 전에 패키지가 다운로드 및 추출됩니다. 지정된 패키지 및 버전이 노드에 이미 설치되어 있는 경우 패키지는 다운로드되지 않으며 기존 패키지가 사용됩니다.

태스크 응용 프로그램 패키지를 설치하려면 태스크의 [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] 속성을 구성합니다.

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>설치된 응용 프로그램 실행
풀 또는 태스크에 대해 지정한 패키지가 노드의 `AZ_BATCH_ROOT_DIR` 내에 있는 명명된 디렉터리에 다운로드되어 추출됩니다. 또한 배치는 명명된 디렉터리에 대한 경로가 포함된 환경 변수를 생성합니다. 태스크 명령줄은 노드에서 응용 프로그램을 참조할 때 이 환경 변수를 사용합니다. 변수는 다음 형식으로 되어 있습니다.

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID` 및 `version`은 배포를 위해 지정한 응용 프로그램 및 패키지 버전에 해당하는 값입니다. 예를 들어 *blender* 응용 프로그램의 2.7 버전을 설치하도록 지정하면 태스크 명령줄은 이 환경 변수를 참조하여 해당 파일에 액세스합니다.

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

응용 프로그램 패키지를 업로드할 때 계산 노드에 배포할 기본 버전을 지정할 수 있습니다. 응용 프로그램의 기본 버전을 지정하면 응용 프로그램 참조 시 버전 접미사를 생략할 수 있습니다. [응용 프로그램 업로드 및 관리](#upload-and-manage-applications)에서 표시된 것처럼 Azure Portal의 응용 프로그램 블레이드에서 기본 응용 프로그램 버전을 지정할 수 있습니다.

예를 들어 *blender*응용 프로그램에 대해 기본 버전으로 "2.7"을 설정한 경우에는 태스크가 다음 환경 변수를 참조할 수 있으며 버전 2.7을 실행합니다.

`AZ_BATCH_APP_PACKAGE_BLENDER`

다음 코드 조각은 기본 버전의 *blender* 응용 프로그램을 실행하는 태스크 명령줄의 예를 보여 줍니다.

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 계산 노드 환경 설정에 대한 자세한 내용은 [배치 기능 개요](batch-api-basics.md)의 [태스크 환경 설정](batch-api-basics.md#environment-settings-for-tasks)을 참조하세요.
> 
> 

## <a name="update-a-pools-application-packages"></a>풀의 응용 프로그램 패키지 업데이트
기존 풀이 이미 응용 프로그램 패키지를 통해 구성된 경우 해당 풀에 대해 새 패키지를 지정할 수 있습니다. 풀에 대한 새 패키지 참조를 지정하면 다음이 적용됩니다.

* 풀에 조인하는 새 노드 및 재부팅되거나 이미지로 다시 설치되는 기존 노드는 모두 새로 지정된 패키지를 설치합니다.
* 패키지 참조를 업데이트할 때 이미 풀에 있는 계산 노드는 새 응용 프로그램 패키지를 자동으로 설치하지 않습니다. 이러한 계산 노드를 재부팅하거나 이미지로 다시 설치하여 새 패키지를 받아야 합니다.
* 새 패키지가 배포될 때 만들어진 환경 변수는 새 응용 프로그램 패키지 참조를 반영합니다.

이 예에서는 기존 풀의 *blender* 응용 프로그램 2.7 버전이 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] 중 하나로 구성되었습니다. 풀의 노드를 2.76b 버전으로 업데이트하려면 새 버전을 사용하여 [ApplicationPackageReference][net_pkgref]를 지정하고 변경 내용을 커밋합니다.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

새 버전이 구성되었으므로 풀에 조인하는 모든 *새* 노드에 2.76b 버전이 배포될 것입니다. *이미* 풀에 있는 노드에 2.76b 버전을 설치하려면 노드를 재부팅하거나 이미지로 다시 설치합니다. 재부팅된 노드는 이전 패키지 배포의 파일을 보존합니다.

## <a name="list-the-applications-in-a-batch-account"></a>배치 계정의 응용 프로그램 나열
[ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] 메서드를 사용하여 Batch 계정의 응용 프로그램 및 응용 프로그램 패키지를 나열할 수 있습니다.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>마무리
응용 프로그램 패키지를 사용하면 고객이 작업에 대한 응용 프로그램을 선택하고 배치 지원 서비스를 통해 작업을 처리할 때 사용할 정확한 버전을 지정할 수 있습니다. 또한 고객이 서비스에서 자신의 고유한 응용 프로그램을 업로드 및 추적하는 기능을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 또한 [Batch REST API][api_rest]는 응용 프로그램 패키지로 작업하도록 지원합니다. 예를 들어 REST API를 사용하여 설치할 패키지를 지정하는 방법에 대한 자세한 내용은 [계정에 풀 추가][rest_add_pool]에서 [applicationPackageReferences][rest_add_pool_with_packages] 요소를 참조하세요. Batch REST API를 사용하여 응용 프로그램 정보를 얻는 방법에 대한 자세한 내용은 [응용 프로그램][rest_applications]을 참조하세요.
* 프로그래밍 방식으로 [배치 관리 .NET으로 Azure 배치 계정 및 할당량 관리](batch-management-dotnet.md)를 수행하는 방법을 알아보세요. [Batch 관리.NET][api_net_mgmt] 라이브러리는 Batch 응용 프로그램 또는 서비스에 대해 계정 만들기 및 삭제 기능을 활성화할 수 있습니다.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "응용 프로그램 패키지에 대한 개략적인 다이어그램"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure 포털의 응용 프로그램 타일"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure 포털의 응용 프로그램 블레이드"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure 포털의 응용 프로그램 세부 정보 블레이드"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure 포털의 새 응용 프로그램 블레이드"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure 포털의 패키지 업데이트 또는 삭제 드롭다운"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure 포털의 새 응용 프로그램 패키지 블레이드"
[9]: ./media/batch-application-packages/app_pkg_09.png "연결된 저장소 계정 없음 경고"
[10]: ./media/batch-application-packages/app_pkg_10.png "포털에서 저장소 계정 블레이드 선택"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure 포털의 패키지 업데이트 블레이드"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure 포털의 패키지 삭제 확인 대화 상자"

