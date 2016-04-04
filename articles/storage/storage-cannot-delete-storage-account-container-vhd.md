<properties
	pageTitle="Azure 저장소 계정, 컨테이너 또는 VHD 삭제 문제 해결| Microsoft Azure"
	description="Azure 저장소 계정, 컨테이너 또는 VHD 삭제 문제 해결"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Azure 저장소 계정, 컨테이너 또는 VHD 삭제 문제 해결

## 요약
[Azure 포털](https://portal.azure.com/) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 Azure 저장소 계정, 컨테이너 또는 VHD를 삭제 하려고 할 때 오류가 발생할 수 있습니다. 다음과 같은 원인으로 인해 이 문제가 발생할 수 있습니다.

-	VM을 삭제해도 디스크 및 VHD가 자동으로 삭제되지 않으며 이로 인해 저장소 계정 삭제에 실패할 수 있습니다. 다른 VM을 마운트할 수 있도록 디스크는 삭제되지 않습니다.

-	디스크 또는 디스크에 연결된 blob에 여전히 임대가 있습니다.

Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow](https://azure.microsoft.com/support/forums/)에서 Azure 포럼을 방문하세요. 이러한 포럼이나 Twitter의 @AzureSupport에 문제를 게시할 수 있습니다. 또한 [Azure 지원](https://azure.microsoft.com/support/options/) 사이트에서 **지원 받기**를 선택하여 Azure 지원 요청을 제출할 수 있습니다.

## 해결 방법
가장 일반적인 문제는 다음과 같은 방법으로 해결할 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)로 전환합니다.
2. **가상 컴퓨터**>**디스크**를 선택합니다.

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. 삭제하려는 저장소 계정, 컨테이너 또는 VHD와 연결된 디스크를 찾습니다. 디스크 위치를 확인합니다. 연결된 저장소 계정, 컨테이너 및 VHD를 찾을 수 있을 것입니다.

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. 디스크의 **연결된 항목** 필드에 나열된 VM이 없는지 확인한 후 디스크를 삭제합니다.

 	> [AZURE.NOTE] 디스크가 VM에 연결되어 있으면 해당 디스크를 삭제할 수 없습니다. 디스크는 삭제된 VM에서 비동기적으로 분리되며, VM이 삭제된 후 몇 분이 지나면 이 필드가 사라집니다.

5. **가상 컴퓨터**>**이미지**를 선택한 다음 저장소 계정, 컨테이너 또는 VHD에 연결된 이미지를 삭제합니다.

그런 다음 저장소 계정, 컨테이너 또는 VHD를 다시 삭제해 보십시오.

> [AZURE.WARNING] 계정을 삭제하기 전에 저장할 내용을 백업했는지 확인합니다. 삭제된 저장소 계정을 복원할 수 없거나 삭제 전에 포함된 콘텐츠를 검색할 수 없습니다. 또한 해당 계정의 리소스에 대해 true를 유지합니다. VHD, Blob, 테이블, 큐 또는 파일을 삭제하면 영구적으로 삭제됩니다. 리소스가 사용되고 있지는 않은지 확인합니다.

## 증상

다음 섹션에서는 Azure 저장소 계정, 컨테이너 또는 VHD를 삭제하려고 할 때 나타나는 일반적인 오류에 대해 설명합니다.

### 시나리오 1 저장소 계정을 삭제할 수 없음

[Azure 포털](https://portal.azure.com/) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)로 이동한 다음 삭제를 선택하여 더 이상 사용하지 않는 저장소 계정을 삭제하려고 시도할 때 다음과 같은 오류 메시지가 표시될 수 없습니다.

**Azure 포털**:

*저장소 계정을(를) 삭제할 수 없습니다<vm-storage-account-name>. 저장소 계정 <vm-storage-account-name>을(를) 삭제할 수 없음: '저장소 계정 <vm-storage-account-name>에 활성 이미지 및/또는 디스크가 있습니다. 이 저장소 계정을 삭제하려면 이러한 이미지 및/또는 디스크를 제거해야 합니다.'.*

**Azure 클래식 포털**:

*저장소 계정 <vm-storage-account-name>에 활성 이미지 및/또는 디스크가 있습니다(예: xxxxxxxxx- xxxxxxxxx-O-209490240936090599). 이 저장소 계정을 삭제하려면 이러한 이미지 및/또는 디스크를 제거해야 합니다.*

또한 다음 오류가 표시될 수 있습니다.

**Azure 포털**:

*저장소 계정 <vm-storage-account-name>에 활성 이미지 및/또는 디스크 아티팩트가 포함된 컨테이너가 1개 있습니다. 이 저장소 계정을 삭제하려면 이미지 리포지토리에서 이러한 아티팩트를 제거해야 합니다*.

**Azure 클래식 포털**:

*제출 실패 저장소 계정 <vm-storage-account-name>에 활성 이미지 및/또는 디스크 아티팩트가 포함된 컨테이너가 1개 있습니다. 이 저장소 계정을 삭제하려면 이미지 리포지토리에서 이러한 아티팩트를 제거해야 합니다. 저장소 계정을 삭제하려고 하는데 해당 저장소 계정과 연결된 활성 디스크가 남아 있는 경우에는 남아 있는 활성 디스크를 삭제해야 한다는 내용의 메시지가 표시됩니다*.

### 시나리오 2 컨테이너를 삭제할 수 없음

저장소 컨테이너를 삭제하려고 할 때 다음 오류가 표시될 수 있습니다.

*저장소 컨테이너 <container name>을(를) 삭제할 수 없습니다. 오류: '현재 컨테이너에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다*.

### 시나리오 3 VHD를 삭제할 수 없음

VM을 삭제한 후 연결된 VHD의 Blob를 삭제하려고 하면 아래와 같은 메시지가 나타납니다.

*Blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'를 삭제하지 못했습니다. 오류: '현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.*

## 자세한 정보

보존되는 V1 VM은 [Azure 포털](https://portal.azure.com/) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 중지됨(할당 취소됨) 상태로 표시됩니다.

**Azure 클래식 포털**:

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Azure 포털**:

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

상태가 "중지됨(할당 취소됨)"이면 CPU, 메모리, 네트워크 등의 계산 리소스는 해제되지만, 사용자가 필요할 때 VM을 신속하게 다시 만들 수 있도록 디스크는 계속 보존됩니다. 이러한 디스크는 Azure 저장소에서 지원하는 VHD를 기반으로 만들어집니다. 저장소 계정에는 이러한 VHD가 있고 디스크에는 이러한 VHD의 임대가 있습니다.

## 참조

- [저장소 계정 삭제](storage-create-storage-account.md#delete-a-storage-account)
- [Microsoft Azure(PowerShell)에서 Blob 저장소의 임대 잠금을 해제하는 방법](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0323_2016-->