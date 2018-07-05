---
title: API 버전 프로필을 사용 하 여 Azure Stack에서 Python 사용 | Microsoft Docs
description: API 버전 프로필을 사용 하 여 Azure Stack에서 Python을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2018
ms.locfileid: "34806839"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>API 버전 프로필을 사용 하 여 Azure Stack에서 Python 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

## <a name="python-and-api-version-profiles"></a>Python 및 API 버전 프로필

Python SDK는 Azure Stack 및 전역 Azure와 같은 다른 클라우드 플랫폼을 대상으로 하는 API 버전 프로필을 지원 합니다. 하이브리드 클라우드를 위한 솔루션을 만드는 API 프로필을 사용할 수 있습니다. Python SDK에는 다음 API 프로필을 지원합니다.

1. **latest**  
    프로필에는 Azure 플랫폼에서 모든 서비스 공급자에 대 한 최신 API 버전을 대상 으로합니다.
2.  **2017-03-09-프로필**  
    **2017-03-09-프로필**  
    Azure Stack에서 지 원하는 리소스 공급자의 API 버전을 대상으로 하는 프로필입니다.

    프로필 API 및 Azure Stack에 대 한 자세한 내용은 참조 하세요. [Azure Stack에서 관리 하는 API 버전 프로필](azure-stack-version-profiles.md)합니다.

## <a name="install-azure-python-sdk"></a>Azure Python SDK 설치

1.  Git를 설치 [공식 사이트](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)합니다.
2.  Python SDK를 설치 하는 지침을 참조 하세요 [Python 개발자 용 Azure](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python)합니다.
3.  사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만들려면 지침은 [Azure Stack에서 제품에 구독을 만들려면](../azure-stack-subscribe-plan-provision-vm.md)합니다. 
4.  서비스 주체를 만들고 해당 ID 및 암호를 저장 합니다. Azure Stack에 대 한 서비스 주체를 만드는 지침은 [Azure Stack에 대 한 응용 프로그램 액세스할](../azure-stack-create-service-principals.md)합니다. 
5.  구독에 서비스 주체에 참가자/소유자 역할이 있는지 확인 합니다. 서비스 주체에 역할을 할당 하는 방법에 지침은 [Azure Stack에 대 한 응용 프로그램 액세스할](../azure-stack-create-service-principals.md)합니다.

## <a name="prerequisites"></a>필수 조건

Azure Stack을 사용 하 여 Python Azure SDK를 사용 하려면 다음 값을 제공 하 고 환경 변수를 사용 하 여 값을 설정 해야 합니다. 운영 체제에서 환경 변수 설정에 대 한 표 다음에 나오는 지침을 참조 하세요. 

| 값 | 환경 변수 | 설명 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| 테넌트 ID | AZURE_TENANT_ID | Azure Stack에 값 [테 넌 트 ID](../azure-stack-identity-overview.md)합니다. |
| 클라이언트 ID | AZURE_CLIENT_ID | 주 응용 프로그램 ID 저장이 문서의 이전 섹션에서 서비스 주체를 만들 때 서비스입니다. |
| 구독 ID | AZURE_SUBSCRIPTION_ID | 합니다 [구독 ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) 제품을 액세스 하는 방법에 Azure Stack에서. |
| 클라이언트 암호 | AZURE_CLIENT_SECRET | 서비스 주체 응용 프로그램 서비스 주체를 만들 때 암호 저장 합니다. |
| Resource Manager 끝점 | ARM_ENDPOINT | 참조 [Azure Stack 리소스 관리자 끝점](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)합니다. |


## <a name="python-samples-for-azure-stack"></a>Azure Stack에 대 한 Python 샘플 

Azure Stack 사용자의 가상 머신에 대 한 일반적인 관리 작업을 수행 하려면 다음 코드 샘플을 사용할 수 있습니다.

코드 샘플을 보여 줍니다.

- 가상 머신을 만듭니다.
    - Linux 가상 머신 만들기
    - Windows 가상 머신 만들기
- 가상 컴퓨터를 업데이트 합니다.
    - 드라이브 확장
    - 가상 머신 태그 지정
    - 데이터 디스크 연결
    - 데이터 디스크 분리
- 가상 컴퓨터를 작동 합니다.
    - 가상 머신 시작
    - 가상 머신 중지
    - 가상 머신 다시 시작
- 가상 머신 나열
- 가상 머신 삭제

이러한 작업을 수행 하는 코드를 검토 하려면 체크 아웃 합니다 **run_example()** Python 스크립트에서 함수 **Hybrid/unmanaged-disks/example.py** GitHub 리포지토리에서 [ virtual machines-python-관리](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88)합니다.

각 작업 주석 및 인쇄 기능을 사용 하 여 레이블이 지정 명확 하 게 됩니다.
예는 반드시 위 목록에 표시 된 순서 대로 되지 않습니다.


## <a name="run-the-python-sample"></a>Python 샘플을 실행 합니다.

1.  사용자는 아직 없는 경우 [Python 설치](https://www.python.org/downloads/)합니다.

    이 샘플 (및 SDK)에 Python 2.7, 3.4, 3.5 및 3.6 호환 됩니다.

2.  Python 개발을 위한 일반적인 권장 사항은 가상 환경을 사용 하는 것입니다. 
    자세한 내용은 참조 하세요. https://docs.python.org/3/tutorial/venv.html
    
    설치 하 고 Python 3에서 "venv" 모듈을 사용 하 여 가상 환경 초기화 (설치 해야 합니다 [virtualenv](https://pypi.python.org/pypi/virtualenv) Python 2.7 용):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  리포지토리를 복제 합니다.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Pip를 사용 하 여 종속성을 설치 합니다.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  만들기는 [서비스 주체](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) Azure Stack과 함께 작동 하도록 합니다. 서비스 주체에 있는지 [참가자/소유자 역할](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) 구독 합니다.

6.  다음 변수를 설정 하 고 현재 shell에 이러한 환경 변수를 내보냅니다. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).