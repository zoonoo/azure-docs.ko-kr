---
title: Azure 빠른 시작 - Batch AI 클러스터 만들기 - Portal | Microsoft Docs
description: 빠른 시작 - 기계 학습 및 AI 모델 학습을 위한 Batch AI 클러스터 만들기 - Azure Portal
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 49e78f1774a2998de7070453f7c098aef1761242
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093231"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Batch AI 학습 작업에 대한 클러스터 만들기

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

이 빠른 시작에서는 Azure Portal을 사용하여 AI 및 기계 학습 모델을 학습하는 데 사용할 수 있는 Batch AI 클러스터를 만드는 방법을 보여 줍니다. Batch AI는 데이터 과학자 및 AI 연구원이 Azure Virtual Machines 클러스터에서 AI 및 기타 기계 학습 모델을 대규모로 학습하기 위한 관리형 서비스입니다.

클러스터는 초기에는 단일 GPU 노드 및 연결된 파일 서버를 포함합니다. 이 빠른 시작을 완료하면 규모를 확대한 후 딥러닝 모델을 학습하는 데 사용할 수 있는 클러스터가 형성됩니다. Batch AI, [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) 도구 또는 [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai)를 사용하여 클러스터에 학습 작업을 제출합니다.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

이 빠른 시작을 완료하려면 SSH 키 쌍이 필요합니다. 기존 SSH 키 쌍을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

SSH 키 쌍을 만들려면 Bash 셸에서 다음 명령을 실행하고 화면의 지침을 따릅니다. 예를 들어 [Azure Cloud Shell](../cloud-shell/overview.md) 또는 Windows의 [Windows Substem for Linux](/windows/wsl/install-win10)를 사용할 수 있습니다. 명령 출력은 공개 키 파일의 파일 이름을 포함합니다. 공개 키 파일(`cat ~/.ssh/id_rsa.pub`)의 내용을 클립보드 또는 이후 단계에서 액세스할 수 있는 다른 위치로 복사합니다.

```bash
ssh-keygen -t rsa -b 2048
```

SSH 키 쌍을 만드는 방법에 대한 자세한 내용은 [Azure에서 Linux VM용 SSH 공개-개인 키 쌍 만들기 및 사용](../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-a-batch-ai-workspace"></a>Batch AI 작업 영역 만들기

먼저 Batch AI 작업 영역을 만들어 Batch AI 리소스를 구성합니다. 작업 영역에는 하나 이상의 클러스터 또는 다른 Batch AI 리소스가 포함될 수 있습니다.

1. **모든 서비스**를 선택하고 **Batch AI**를 필터링합니다.

2. **작업 영역 추가**를 선택합니다.

3. **계정 이름** 및 **리소스 그룹** 값을 입력합니다. 원할 경우 작업 영역의 **구독** 및 **위치**에 대해 다른 옵션을 선택합니다. **작업 영역 만들기**를 선택합니다.

   ![Batch AI 작업 영역 만들기](./media/quickstart-create-cluster-portal/create-workspace.png)

**배포 성공** 메시지가 나타나면 만든 리소스로 이동한 후 작업 영역을 선택합니다.

## <a name="create-a-file-server"></a>파일 서버 만들기

Batch AI 파일 서버는 클러스터 노드에 자동으로 탑재될 수 있는 단일 노드 NFS입니다. 학습 작업의 입력 데이터 및 출력 데이터를 위한 저장소를 제공하는 여러 가지 방법 중 하나입니다.

1. 작업 영역에서 **파일 서버** > **Batch AI 파일 서버 추가**를 선택합니다.

2. **파일 서버 이름** 및 **VM 크기** 값을 입력합니다. 이 빠른 시작에서는 파일 서버의 VM 크기는 *Standard D1_v2*가 권장됩니다. 대량의 학습 작업 입/출력 데이터를 저장해야 하는 경우 다른 크기를 선택합니다.

3. **관리자 사용자 이름**을 입력하고 SSH 공개 키 파일의 내용을 **SSH 키**로 복사합니다. 나머지 값은 기본값으로 유지하고 **파일 서버 만들기**를 선택합니다.

   ![Batch AI 파일 서버 만들기](./media/quickstart-create-cluster-portal/create-file-server.png)

파일 서버를 배포하는 데 몇 분 정도 걸립니다.

서버가 생성되면 **속성**을 클릭하고 **탑재 설정**을 기록해 둡니다. 서버의 공용 IP 주소에 대해 SSH를 수행하여 지정된 디렉터리(*/data*)에 학습 데이터 및 출력 파일을 업로드 및 다운로드할 수 있습니다.

![파일 서버 속성](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>클러스터 만들기

다음 단계에서는 단일 GPU 노드가 있는 클러스터를 만듭니다. 클러스터 노드는 호스트 컨테이너 기반 애플리케이션으로 디자인된 기본 Ubuntu Server 이미지를 실행합니다. 이 이미지는 대부분의 학습 워크로드에 사용할 수 있습니다. 클러스터 노드는 해당 탑재 지점에 파일 서버를 탑재합니다. 

1. Batch AI 작업 영역에서 **클러스터** > **Batch AI 클러스터 추가**를 선택합니다.

2. **클러스터 이름** 및 다음 설정의 값을 입력합니다. VM의 권장 크기는 1개의 NVIDIA Tesla K80 GPU입니다.
  
   |설정  |값  |
   |---------|---------|
   |**VM 크기**     |표준 NC6|
   |**노드의 대상 수**     |1|

3. **관리자 사용자 이름**을 입력하고 SSH 공개 키 파일의 내용을 **SSH 키**로 복사합니다. 이 페이지의 나머지 값을 기본값으로 유지하고 **다음: 노드 설정**을 선택합니다.

   ![기본 클러스터 정보 입력](./media/quickstart-create-cluster-portal/create-cluster.png)

4. **탑재 볼륨**에서 **파일 서버 참조** > **추가**를 선택합니다. 이전에 만든 파일 서버를 선택합니다. 각 클러스터 노드에서 파일 서버가 탑재되는 **상대 탑재 경로**를 입력합니다. **저장 및 계속**을 선택합니다.

   ![파일 서버 참조 추가](./media/quickstart-create-cluster-portal/file-server-reference.png)

노드 설정을 저장하고 **클러스터 만들기**를 선택합니다.

Batch AI에서 노드를 할당하는 데 몇 분 정도 걸립니다. 이 시간 동안 클러스터의 **할당 상태**는 **크기 조정 중**입니다. 몇 분 후에 클러스터의 상태는 **Steady**(안정)가 되며 노드가 시작됩니다.

![클러스터 시작 중](./media/quickstart-create-cluster-portal/cluster-resizing.png)

노드의 상태를 확인할 클러스터 이름을 선택합니다. 노드의 상태가 **Idle**(유휴)이면 학습 작업을 실행할 준비가 된 것입니다.

### <a name="list-cluster-nodes"></a>클러스터 노드 나열

애플리케이션을 설치하거나 유지 관리를 수행하기 위해 클러스터 노드(이 경우 단일 노드)에 연결해야 할 경우 포털에서 연결 정보를 가져옵니다. 클러스터가 생성된 후 **노드**를 클릭하고 SSH **연결** 설정(IP 주소 및 포트 번호)을 기록해 둡니다.

![클러스터 노드](./media/quickstart-create-cluster-portal/cluster-nodes.png)

이 정보를 사용하여 노드로의 SSH 연결을 수행합니다. 예를 들어, 다음 명령에서 노드의 올바른 IP 주소 및 포트 번호를 대체합니다.

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>클러스터 크기 조정

클러스터를 사용하여 모델을 학습하는 경우 추가 계산 리소스가 필요할 수 있습니다. 예를 들어, 분산 학습 작업을 위해 해당 크기를 2개 노드로 늘리려면 **크기 조정**을 선택하고 **대상 노드 수**를 2로 설정합니다. 구성을 저장합니다.

![클러스터 크기 조정](./media/quickstart-create-cluster-portal/scale-cluster.png)

클러스터 크기가 조정될 때까지 몇 분 정도 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

Batch AI 자습서 및 샘플을 계속 사용하려면 Batch AI 작업 영역, 파일 서버 및 이 빠른 시작에서 만든 클러스터를 사용합니다.

작업이 예약되지 않은 경우에도, 기본 가상 머신이 실행되는 동안 Batch AI 클러스터 및 파일 서버 비용이 청구됩니다. 실행할 작업이 없는 경우 클러스터 구성을 유지하려면 클러스터 크기를 0개 노드로 조정합니다. 나중에 작업을 실행하기 위해 하나 이상의 노드로 크기를 조정합니다. 

더 이상 필요 없는 경우 클러스터 및 파일 서버를 포함하는 Batch AI 작업 영역을 삭제합니다. 이렇게 하려면 Batch AI 작업 영역을 선택하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 Batch AI 클러스터 및 연결된 파일 서버를 만드는 방법을 배웠습니다. Batch AI 클러스터를 사용하여 모델을 학습하는 방법을 자세히 알아보려면 딥러닝 모델 학습을 위한 빠른 시작을 계속 진행하세요.

> [!div class="nextstepaction"]
> [딥러닝 모델 학습](./quickstart-tensorflow-training-cli.md)
