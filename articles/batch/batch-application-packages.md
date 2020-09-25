---
title: 컴퓨팅 노드에 애플리케이션 패키지 배포
description: Azure Batch의 애플리케이션 패키지 기능을 사용하여 Batch 컴퓨팅 노드에 설치할 여러 애플리케이션 및 버전을 간편하게 관리하세요.
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperfq1
ms.openlocfilehash: 1bacb0c71c05aeb983bfa9ebf71873a22fea39a1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277702"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>애플리케이션을 배포하여 Batch 애플리케이션 패키지에서 노드 컴퓨팅

응용 프로그램 패키지는 Azure Batch 솔루션의 코드를 간소화 하 고 작업에서 실행 하는 응용 프로그램을 보다 쉽게 관리할 수 있도록 합니다. 응용 프로그램 패키지를 사용 하면 지원 파일을 포함 하 여 작업을 실행 하는 여러 버전의 응용 프로그램을 업로드 하 고 관리할 수 있습니다. 풀의 컴퓨팅 노드에 이러한 애플리케이션을 하나 이상 자동으로 배포할 수 있습니다.

애플리케이션 패키지를 만들고 관리하는 API는 [Batch Management .NET](/dotnet/api/overview/azure/batch/management) 라이브러리의 일부입니다. 컴퓨팅 노드에서 애플리케이션 패키지를 설치하는 API는 [Batch .NET](/dotnet/api/overview/azure/batch/client) 라이브러리의 일부입니다. 다른 언어의 경우 상응하는 기능을 Batch API에서 제공합니다.

이 문서에서는 Azure Portal에서 응용 프로그램 패키지를 업로드 하 고 관리 하는 방법을 설명 합니다. 또한 [Batch .net](/dotnet/api/overview/azure/batch/client) 라이브러리를 사용 하 여 풀의 계산 노드에 설치 하는 방법도 보여 줍니다.

## <a name="application-package-requirements"></a>애플리케이션 패키지 요구 사항

애플리케이션 패키지를 사용하려면 Batch 계정에 [Azure Storage 계정을 연결](#link-a-storage-account)해야 합니다.

Batch 계정 내 애플리케이션 및 애플리케이션 패키지 수와 애플리케이션 패키지의 최대 크기에 대한 제한이 있습니다. 자세한 내용은 [Azure Batch 서비스에 대 한 할당량 및 제한](batch-quota-limit.md)을 참조 하세요.

> [!NOTE]
> 2017 년 7 월 5 일 이전에 만든 Batch 풀은 응용 프로그램 패키지를 지원 하지 않습니다 (Cloud Services 구성을 사용 하 여 2016 년 3 월 10 일 이후에 생성 되지 않은 경우). 여기서 설명하는 애플리케이션 패키지 기능은 이전 버전의 서비스에서 사용할 수 있는 Batch 앱 기능을 대체합니다.

## <a name="understand-applications-and-application-packages"></a>응용 프로그램 및 응용 프로그램 패키지 이해

Azure Batch 내에서 *애플리케이션*은 풀의 컴퓨팅 노드에 자동으로 다운로드할 수 있는 버전이 지정된 이진 파일의 세트를 나타냅니다. 응용 프로그램에는 응용 프로그램의 서로 다른 버전을 나타내는 하나 이상의 *응용 프로그램 패키지가*포함 되어 있습니다.

각 *응용 프로그램 패키지* 는 응용 프로그램 이진 파일 및 지원 파일을 포함 하는 .zip 파일입니다. .Zip 형식만 지원 됩니다.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="응용 프로그램 및 응용 프로그램 패키지의 상위 수준 보기를 보여 주는 다이어그램":::

풀 또는 태스크 수준에서 응용 프로그램 패키지를 지정할 수 있습니다.

- **풀 애플리케이션 패키지**는 모든 노드에 배포됩니다. 애플리케이션은 노드가 풀을 조인하고 재부팅되거나 이미지를 다시 설치할 때 배포됩니다.
  
    풀의 모든 노드가 작업의 태스크를 실행 하는 경우 풀 응용 프로그램 패키지가 적합 합니다. 풀을 만들 때 배포할 응용 프로그램 패키지를 하나 이상 지정할 수 있습니다. 기존 풀의 패키지를 추가 하거나 업데이트할 수도 있습니다. 기존 풀에 새 패키지를 설치 하려면 해당 노드를 다시 시작 해야 합니다.

- **태스크 애플리케이션 패키지**는 태스크의 명령줄을 실행하기 바로 전에 태스크를 실행하도록 예약된 컴퓨팅 노드에만 배포됩니다. 지정된 애플리케이션 패키지 및 버전이 노드에 이미 있는 경우 다시 배포되지 않으며 기존 패키지가 사용됩니다.
  
    작업 응용 프로그램 패키지는 여러 작업이 하나의 풀에서 실행 되 고 작업이 완료 될 때 풀이 삭제 되지 않는 공유 풀 환경에서 유용 합니다. 작업의 태스크가 풀의 노드 수보다 적으면 태스크 응용 프로그램 패키지는 태스크를 실행 하는 노드에만 배포 되기 때문에 데이터 전송을 최소화할 수 있습니다.
  
    태스크 애플리케이션 패키지를 활용할 수 있는 다른 시나리오는 큰 애플리케이션을 실행하지만 태스크 수는 적은 작업입니다. 예를 들어, 작업 응용 프로그램은 고중량 전처리 단계 또는 병합 작업에 유용할 수 있습니다.

애플리케이션 패키지를 사용하면 풀의 시작 태스크가 노드에 설치할 개별 리소스 파일의 긴 목록을 지정할 필요가 없습니다. Azure Storage 또는 노드에서 애플리케이션 파일의 여러 버전을 수동으로 관리할 필요가 없습니다. 그리고 저장소 계정의 파일에 대 한 액세스를 제공 하기 위해 [SAS url](../storage/common/storage-sas-overview.md) 을 생성 하는 것에 대해 걱정할 필요가 없습니다. Batch는 Azure Storage와 함께 백그라운드에서 작동하여 애플리케이션 패키지를 저장하고 컴퓨팅 노드에 배포합니다.

> [!NOTE]
> 리소스 파일 및 환경 변수를 포함한 시작 태스크의 전체 크기는 32768자 이하여야 합니다. 시작 태스크가이 제한을 초과 하는 경우 응용 프로그램 패키지를 사용 하는 것이 또 다른 옵션입니다. 리소스 파일을 포함 하는 .zip 파일을 만들고 Azure Storage blob으로 업로드 한 다음 시작 태스크의 명령줄에서 압축을 풀 수도 있습니다.

## <a name="upload-and-manage-applications"></a>애플리케이션 업로드 및 관리

[Azure Portal](https://portal.azure.com) 또는 Batch Management API를 사용하여 Batch 계정에서 애플리케이션 패키지를 관리합니다. 다음 섹션에서는 저장소 계정을 연결 하는 방법 및 Azure Portal에서 응용 프로그램 및 응용 프로그램 패키지를 추가 하 고 관리 하는 방법을 설명 합니다.

### <a name="link-a-storage-account"></a>저장소 계정 연결

응용 프로그램 패키지를 사용 하려면 [Azure Storage 계정을](accounts.md#azure-storage-accounts) Batch 계정에 연결 해야 합니다. Batch 서비스는 연결 된 저장소 계정을 사용 하 여 응용 프로그램 패키지를 저장 합니다. Batch 계정에 사용 하기 위해 특별히 저장소 계정을 만드는 것이 좋습니다.

저장소 계정을 아직 구성 하지 않은 경우에는 Batch 계정에서 **응용 프로그램** 을 처음 선택할 때 Azure Portal에 경고가 표시 됩니다. Storage 계정을 Batch 계정에 연결 하려면 **경고** 창에서 **저장소 계정** 을 선택 하 고 **저장소 계정** 을 다시 선택 합니다.

계정 두 개를 연결한 후에 Batch는 연결된 스토리지 계정에 저장된 패키지를 컴퓨팅 노드에 자동으로 배포할 수 있습니다.

> [!IMPORTANT]
> [방화벽 규칙](../storage/common/storage-network-security.md)을 사용 하 여 구성 된 Azure Storage 계정이 나 **계층적 네임 스페이스** 를 **사용**으로 설정 하 여 응용 프로그램 패키지를 사용할 수 없습니다.

Batch 서비스는 Azure Storage를 사용하여 애플리케이션 패키지를 블록 Blob으로 저장합니다. 블록 Blob 데이터에 대해서는 [정상적으로 요금이 부과](https://azure.microsoft.com/pricing/details/storage/)되며 각 패킷의 크기는 최대 블록 Blob 크기를 초과할 수 없습니다. 자세한 내용은 [스토리지 계정의 Azure Storage 확장성 및 성능 목표](../storage/blobs/scalability-targets.md)를 참조하세요. 비용을 최소화 하려면 응용 프로그램 패키지의 크기와 수를 고려 하 고 사용 되지 않는 패키지를 주기적으로 제거 해야 합니다.

### <a name="view-current-applications"></a>현재 애플리케이션 보기

Batch 계정의 응용 프로그램을 보려면 왼쪽 탐색 메뉴에서 **응용 프로그램** 을 선택 합니다.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Azure Portal에서 응용 프로그램 메뉴 항목의 스크린샷":::

이 메뉴 옵션을 선택 하면 **응용 프로그램** 창이 열립니다. 이 창은 계정의 각 애플리케이션 ID 및 다음 속성을 표시합니다.

- **패키지**: 이 애플리케이션과 연결된 버전의 수입니다.
- **기본 버전**: 해당 하는 경우 응용 프로그램을 배포할 때 버전이 지정 되지 않은 경우 설치 될 응용 프로그램 버전입니다.
- **업데이트 허용**: 패키지 업데이트 및 삭제가 허용 되는지 여부를 지정 합니다.

계산 노드에서 응용 프로그램 패키지의 [파일 구조](files-and-directories.md) 를 보려면 Azure Portal의 Batch 계정으로 이동 합니다. **풀**을 선택 합니다. 그런 다음 계산 노드를 포함 하는 풀을 선택 합니다. 응용 프로그램 패키지가 설치 된 계산 노드를 선택 하 고 **응용 프로그램** 폴더를 엽니다.

### <a name="view-application-details"></a>애플리케이션 세부 정보 보기

응용 프로그램에 대 한 세부 정보를 보려면 **응용** 프로그램 창에서 응용 프로그램을 선택 합니다. 응용 프로그램에 대해 다음 설정을 구성할 수 있습니다.

- **업데이트 허용**: 응용 프로그램 패키지를 [업데이트 하거나 삭제할](#update-or-delete-an-application-package)수 있는지 여부를 나타냅니다. 기본값은 **예**입니다. **아니요**로 설정 하면 기존 응용 프로그램 패키지를 업데이트 하거나 삭제할 수 없지만 새 응용 프로그램 패키지 버전을 계속 추가할 수 있습니다.
- **기본 버전**: 지정 된 버전이 없는 경우 응용 프로그램이 배포 될 때 사용할 기본 응용 프로그램 패키지입니다.
- **표시 이름**: 응용 프로그램에 대 한 정보를 표시할 때 Batch 솔루션에서 사용할 수 있는 친숙 한 이름입니다. 예를 들어이 이름은 Batch를 통해 고객에 게 제공 하는 서비스의 UI에서 사용할 수 있습니다.

### <a name="add-a-new-application"></a>새 애플리케이션 추가

새 응용 프로그램을 만들려면 응용 프로그램 패키지를 추가 하 고 고유한 응용 프로그램 ID를 지정 합니다.

Batch 계정에서 **응용 프로그램** 을 선택 하 고 **추가**를 선택 합니다.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Azure Portal에서 새 응용 프로그램 만들기 프로세스의 스크린샷":::

다음 정보를 입력 합니다.

- **응용 프로그램 id**: 새 응용 프로그램의 id입니다.
- **Version**": 업로드할 응용 프로그램 패키지의 버전입니다.
- **응용 프로그램 패키지**: 응용 프로그램을 실행 하는 데 필요한 응용 프로그램 이진 파일 및 지원 파일이 포함 된 .zip 파일입니다.

입력 한 **응용 프로그램 ID** 및 **버전** 은 다음 요구 사항을 따라야 합니다.

- Windows 노드에서 ID에는 영숫자, 하이픈 및 밑줄의 모든 조합을 사용할 수 있습니다. Linux 노드에서는 영숫자와 밑줄만 허용됩니다.
- 64 자를 초과할 수 없습니다.
- Batch 계정 내에서 고유해야 합니다.
- Id는 대/소문자를 유지 하 고 대/소문자를 구분 하지 않습니다.

준비가 되면 **제출**을 선택합니다. .Zip 파일이 Azure Storage 계정에 업로드 된 후 포털에 알림이 표시 됩니다. 업로드 하는 파일의 크기와 네트워크 연결 속도에 따라 다소 시간이 걸릴 수 있습니다.

### <a name="add-a-new-application-package"></a>새 애플리케이션 패키지 추가

기존 응용 프로그램에 대 한 응용 프로그램 패키지 버전을 추가 하려면 Batch **계정의 응용 프로그램 섹션에서** 응용 프로그램을 선택한 다음 **추가**를 선택 합니다.

새 응용 프로그램의 경우와 마찬가지로 새 패키지의 **버전** 을 지정 하 고, **응용 프로그램 패키지** 필드에 .zip 파일을 업로드 한 다음, **제출**을 선택 합니다.

### <a name="update-or-delete-an-application-package"></a>애플리케이션 패키지 업데이트 또는 삭제

기존 응용 프로그램 패키지를 업데이트 하거나 삭제 하려면 Batch 계정의 **응용 프로그램** 섹션에서 응용 프로그램을 선택 합니다. 수정 하려는 응용 프로그램 패키지의 행에서 줄임표를 선택 하 고 수행할 작업을 선택 합니다.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Azure Portal의 응용 프로그램 패키지에 대 한 업데이트 및 삭제 옵션을 보여 주는 스크린샷":::

**업데이트**를 선택 하면 새 .zip 파일을 업로드할 수 있습니다. 그러면 해당 버전에 대해 업로드 한 이전 .zip 파일이 대체 됩니다.

**삭제**를 선택 하면 해당 버전의 삭제를 확인 하는 메시지가 표시 됩니다. **확인**을 선택 하면 Batch는 Azure Storage 계정에서 .zip 파일을 삭제 합니다. 응용 프로그램의 기본 버전을 삭제 하면 해당 응용 프로그램에 대 한 **기본 버전** 설정이 제거 됩니다.

## <a name="install-applications-on-compute-nodes"></a>컴퓨팅 노드에 애플리케이션 설치

Azure Portal에서 응용 프로그램 패키지를 관리 하는 방법을 배웠으므로 이제 계산 노드에 배포 하 고 Batch 작업을 통해 실행 하는 방법에 대해 논의할 수 있습니다.

### <a name="install-pool-application-packages"></a>풀 애플리케이션 패키지 설치

풀의 모든 컴퓨팅 노드에 애플리케이션 패키지를 설치하려면 풀에 대해 애플리케이션 패키지 참조를 하나 이상 지정해야 합니다. 풀에 대해 지정 하는 응용 프로그램 패키지는 풀을 조인 하는 각 계산 노드 및 다시 부팅 되거나 이미지로 다시 설치 되는 노드에 설치 됩니다.

Batch .NET에서 새 풀을 만들 때 또는 기존 풀에 대해 [ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) 를 하나 이상 지정 합니다. [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) 클래스는 풀의 컴퓨팅 노드에 설치할 애플리케이션 ID 및 버전을 지정합니다.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 응용 프로그램 패키지 배포가 실패 하면 Batch 서비스에서 노드를 [사용할](/dotnet/api/microsoft.azure.batch.computenode.state)수 없는 것으로 표시 하 고 해당 노드에서 실행 하도록 예약 된 작업이 없습니다. 이 문제가 발생 하는 경우 노드를 다시 시작 하 여 패키지 배포를 다시 시작. 또한 노드를 다시 시작하면 노드에서 작업을 다시 예약할 수 있습니다.

### <a name="install-task-application-packages"></a>태스크 애플리케이션 패키지 설치

풀과 마찬가지로 태스크에 대해 애플리케이션 패키지 참조를 지정합니다. 노드에서 실행하도록 태스크가 예약된 경우 태스크의 명령줄이 실행되기 바로 전에 패키지가 다운로드 및 추출됩니다. 지정된 패키지 및 버전이 노드에 이미 설치되어 있는 경우 패키지는 다운로드되지 않으며 기존 패키지가 사용됩니다.

작업 응용 프로그램 패키지를 설치 하려면 태스크의 [cloudtask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) 속성을 구성 합니다.

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

## <a name="execute-the-installed-applications"></a>설치된 애플리케이션 실행

풀 또는 태스크에 대해 지정한 패키지가 노드의 `AZ_BATCH_ROOT_DIR` 내에 있는 명명된 디렉터리에 다운로드되어 추출됩니다. 또한 Batch는 명명된 디렉터리에 대한 경로가 포함된 환경 변수를 생성합니다. 태스크 명령줄은 노드에서 애플리케이션을 참조할 때 이 환경 변수를 사용합니다.

Windows 노드에서는 변수가 다음과 같은 형식입니다.

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux 노드에서는 형식이 약간 다릅니다. 마침표(.), 하이픈(-) 및 숫자 기호(#)가 환경 변수에서 밑줄로 표시됩니다. 또한 애플리케이션 ID의 대/소문자는 유지됩니다. 다음은 그 예입니다.

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` 및 `version`은 배포를 위해 지정한 애플리케이션 및 패키지 버전에 해당하는 값입니다. 예를 들어 Windows 노드에 *blender* 애플리케이션의 2.7 버전을 설치하도록 지정하면 태스크 명령줄은 다음 환경 변수를 사용하여 해당 파일에 액세스합니다.

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux 노드에서는 환경 변수를 다음 형식으로 지정합니다. 마침표(.), 하이픈(-) 및 숫자 기호(#)를 밑줄로 평면화하고 애플리케이션 ID의 대/소문자를 유지합니다.

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

애플리케이션 패키지를 업로드할 때 컴퓨팅 노드에 배포할 기본 버전을 지정할 수 있습니다. 애플리케이션의 기본 버전을 지정하면 애플리케이션 참조 시 버전 접미사를 생략할 수 있습니다. [애플리케이션 업로드 및 관리](#upload-and-manage-applications)에서 표시된 것처럼 Azure Portal의 **애플리케이션** 창에서 기본 애플리케이션 버전을 지정할 수 있습니다.

예를 들어 *blender* 애플리케이션에 대해 기본 버전으로 "2.7"을 설정한 경우 태스크가 다음 환경 변수를 참조하면 Windows 노드에서 버전 2.7을 실행합니다.

`AZ_BATCH_APP_PACKAGE_BLENDER`

다음 코드 조각은 기본 버전의 *blender* 애플리케이션을 실행하는 태스크 명령줄의 예를 보여 줍니다.

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 컴퓨팅 노드 환경 설정에 대한 자세한 내용은 [태스크에 대한 환경 설정](jobs-and-tasks.md#environment-settings-for-tasks)을 참조하세요.

## <a name="update-a-pools-application-packages"></a>풀의 애플리케이션 패키지 업데이트

기존 풀이 이미 애플리케이션 패키지를 통해 구성된 경우 해당 풀에 대해 새 패키지를 지정할 수 있습니다. 이는 다음을 의미합니다.

- Batch 서비스는 풀에 조인하는 모든 새 노드와 다시 부팅되거나 이미지로 다시 설치되는 기존 노드에 새로 지정된 패키지를 설치합니다.
- 패키지 참조를 업데이트할 때 이미 풀에 있는 Compute 노드는 새 애플리케이션 패키지를 자동으로 설치하지 않습니다. 이러한 컴퓨팅 노드를 재부팅하거나 이미지로 다시 설치하여 새 패키지를 받아야 합니다.
- 새 패키지가 배포될 때 만들어진 환경 변수는 새 애플리케이션 패키지 참조를 반영합니다.

이 예제에서 기존 풀은 *blender* 응용 프로그램의 버전 2.7을 [ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences)중 하나로 구성 했습니다. 풀의 노드를 2.76b 버전으로 업데이트하려면 새 버전을 사용하여 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference)를 지정하고 변경 내용을 커밋합니다.

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

새 버전이 구성되었으므로 Batch 서비스는 풀에 조인하는 모든 새 노드에 2.76b 버전을 설치합니다. 이미 풀에 있는 노드에 2.76b 버전을 설치하려면 노드를 재부팅하거나 이미지로 다시 설치합니다. 다시 부팅 된 노드는 이전 패키지 배포의 파일을 보존 합니다.

## <a name="list-the-applications-in-a-batch-account"></a>Batch 계정의 애플리케이션 나열

[Applicationoperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) 메서드를 사용 하 여 Batch 계정에 응용 프로그램 및 해당 패키지를 나열할 수 있습니다.

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

## <a name="next-steps"></a>다음 단계

- 또한 [Batch REST API](/rest/api/batchservice)는 애플리케이션 패키지로 작업하도록 지원합니다. 예를 들어 설치할 패키지를 지정 하는 방법 및 응용 프로그램 정보를 얻는 방법에 대 한 [응용](/rest/api/batchservice/application) 프로그램에 대 한 [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) 요소를 참조 하세요.
- 프로그래밍 방식으로 [Batch 관리 .NET으로 Azure Batch 계정 및 할당량 관리](batch-management-dotnet.md)를 수행하는 방법을 알아보세요. [Batch 관리.NET](/dotnet/api/overview/azure/batch/management) 라이브러리는 Batch 애플리케이션 또는 서비스에 대해 계정 만들기 및 삭제 기능을 활성화할 수 있습니다.
