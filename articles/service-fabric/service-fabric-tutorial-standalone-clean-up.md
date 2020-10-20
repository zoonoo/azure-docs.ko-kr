---
title: 독립 실행형 클러스터 정리
description: 이 자습서에서는 독립 실행형 Service Fabric 클러스터에 대한 AWS 또는 Azure 리소스를 삭제하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842889"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>자습서: 독립 실행형 클러스터 정리

Service Fabric 독립 실행형 클러스터는 Service Fabric를 호스팅할 자체 환경을 선택할 수 있는 옵션을 제공합니다. 이 자습서 시리즈에서는 AWS 또는 Azure에서 호스팅되는 독립 실행형 클러스터를 만들고 여기에 애플리케이션을 배포합니다.

이 자습서는 시리즈의 4부입니다. 자습서의 이 부분에서는 Service Fabric 클러스터를 호스트하기 위해 만든 AWS 또는 Azure 리소스를 삭제하는 방법을 보여줍니다.

이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric 클러스터 제거
> * AWS 또는 Azure 리소스 삭제

## <a name="remove-a-service-fabric-cluster"></a>Service Fabric 클러스터 제거

1. Service Fabric을 설치하는 데 사용한 VM으로 RDP 수행
2. PowerShell을 엽니다.
3. 디렉터리를 두 번째 자습서에서 추출된 폴더로 변경합니다.
4. 다음 명령을 실행하여 Service Fabric 클러스터를 제거합니다.

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. 메시지가 표시되면 `Y`를 입력합니다. 성공한 경우 출력은 다음과 같이 표시됩니다(고유한 IP 주소 포함).

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="delete-aws-resources"></a>AWS 리소스 삭제

1. AWS 계정에 로그인합니다.
2. EC2 콘솔로 이동합니다.
3. 자습서의 1부에서 만든 세 개의 노드를 선택합니다.
4. **작업** > **인스턴스 상태** > **종료**를 선택합니다.

## <a name="delete-azure-resources"></a>Azure 리소스 삭제

1. Azure Portal에 로그인합니다.
2. **가상 머신** 섹션으로 이동합니다.
3. 자습서의 1부에서 만든 세 개의 노드에 대한 확인란을 선택합니다.
4. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 이전 단계에서 만든 리소스를 삭제하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 정리

> [!div class="nextstepaction"]
> [처음으로](service-fabric-tutorial-standalone-create-infrastructure.md)
