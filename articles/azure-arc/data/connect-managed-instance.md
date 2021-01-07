---
title: Azure Arc 사용 SQL Managed Instance에 연결
description: Azure Arc 사용 SQL Managed Instance에 연결
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: abd27e15ccf5b421e69e78b2b726d192ffdecacb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372364"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 사용 SQL Managed Instance에 연결

이 문서에서는 Azure Arc 사용 SQL Managed Instance에 연결할 수 있는 방법을 설명 합니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Azure Arc 사용 SQL 관리 되는 인스턴스 보기

Azure Arc 사용 SQL Managed Instance 및 외부 끝점을 보려면 다음 명령을 사용 합니다.

```console
azdata arc sql mi list
```

출력은 다음과 같습니다.

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

AKS 또는 kubeadm 또는 OpenShift 등을 사용 하는 경우 여기에서 외부 IP 및 포트 번호를 복사 하 고 즐겨 사용 하는 도구를 사용 하 여 Azure Data Studio 또는 SQL Server Management Studio와 같은 SQL Sever/Azure SQL 인스턴스에 연결할 수 있습니다.  그러나 빠른 시작 VM을 사용 하는 경우 Azure 외부에서 해당 VM에 연결 하는 방법에 대 한 특수 정보는 아래를 참조 하세요. 

> [!NOTE]
> 회사 정책에서 IP 및 포트에 대 한 액세스를 차단할 수 있습니다. 특히 공용 클라우드에서 생성 된 경우입니다.

## <a name="connect"></a>연결 

Azure Data Studio, SQL Server Management Studio 또는 SQLCMD를 사용 하 여 연결

Azure Data Studio를 열고 위의 외부 끝점 IP 주소와 포트 번호를 사용 하 여 인스턴스에 연결 합니다. Azure VM을 사용 하는 경우 [azure 가상 머신 배포에 대 한 특별 정보](#special-note-about-azure-virtual-machine-deployments)를 사용 하 여 식별할 수 있는 _공용_ IP 주소가 필요 합니다.

예를 들어:

- 서버: 52.229.9.30, 30913
- 사용자 이름: sa
- 암호: 프로 비전 시 지정 된 SQL 암호

> [!NOTE]
> Azure Data Studio 사용 하 여 [SQL 관리 되는 인스턴스 대시보드를 볼](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)수 있습니다.

SQLCMD 또는 Linux 또는 Windows를 사용 하 여 연결 하려면 다음과 같은 명령을 사용할 수 있습니다. 메시지가 표시 되 면 SQL 암호를 입력 합니다.

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 가상 머신 배포에 대 한 특수 정보

Azure 가상 컴퓨터를 사용 하는 경우 끝점 IP 주소는 공용 IP 주소를 표시 하지 않습니다. 외부 IP 주소를 찾으려면 다음 명령을 사용 합니다.

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

그런 다음 공용 IP 주소를 포트와 결합 하 여 연결을 만들 수 있습니다.

NSG (네트워크 보안 게이트웨이)를 통해 sql 인스턴스의 포트를 노출 해야 할 수도 있습니다. NSG ()를 통한 트래픽을 허용 하려면 다음 명령을 사용 하 여 수행할 수 있는 규칙을 추가 해야 합니다.

규칙을 설정 하려면 아래 명령을 사용 하 여 찾을 수 있는 NSG의 이름을 알아야 합니다.

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG의 이름이 있으면 다음 명령을 사용 하 여 방화벽 규칙을 추가할 수 있습니다. 여기에 있는 예제 값은 포트 30913에 대 한 NSG 규칙을 만들고 **모든** 원본 IP 주소에서 연결할 수 있도록 허용 합니다.  이 방법은 보안 모범 사례가 아닙니다.  팀 또는 조직의 IP 주소를 포함하는 IP 주소 범위 또는 클라이언트 IP 주소와 관련된 -source-address-prefixes 값을 지정하면 더 잘 잠글 수 있습니다.

`--destination-port-ranges`아래 매개 변수 값을 위의 F 명령에서 가져온 포트 번호로 바꿉니다 `azdata sql instance list` .

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>다음 단계

- [SQL 관리 되는 인스턴스 대시보드 보기](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Azure Portal에서 SQL Managed Instance 보기](view-arc-data-services-inventory-in-azure-portal.md)
