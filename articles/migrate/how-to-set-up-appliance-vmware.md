---
title: VMware Vm에 대 한 마이그레이션에 대 한 Azure 마이그레이션 Server 평가/을 어플라이언스를 설정 | Microsoft Docs
description: 검색, 평가 및 Azure 마이그레이션 Server 평가/마이그레이션을 사용 하 여 VMware Vm의 에이전트 없는 마이그레이션에 대 한 어플라이언스를 설정 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811727"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>VMware Vm에 대 한 어플라이언스 설정

이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 VMware Vm을 평가 하거나 Azure 마이그레이션 Server 마이그레이션 도구를 사용 하 여 에이전트 없이 마이그레이션을 사용 하 여 Azure에 VMware Vm을 마이그레이션 하는 경우 Azure Migrate 어플라이언스를 설정 하는 방법을 설명 합니다.

VMware VM 어플라이언스는 다음을 수행 하 여 Azure 마이그레이션 Server 평가/마이그레이션 사용 되는 경량 어플라이언스:

- 온-프레미스 VMware VM을 검색합니다.
- 검색 된 Vm에 Azure 마이그레이션 Server 평가/마이그레이션 메타 데이터 및 성능 데이터를 보냅니다.

[자세한](migrate-appliance.md) Azure Migrate 어플라이언스에 대 한 합니다.


## <a name="appliance-deployment-steps"></a>어플라이언스 배포 단계

설정 하려면 어플라이언스에 있습니다.
- OVA 템플릿 파일을 다운로드 하 고 vCenter Server에 가져옵니다.
- 어플라이언스를 만들고 Azure Migrate Server 평가에 연결할 수 있는지 확인 합니다. 
- 처음에 대 한 어플라이언스를 구성 하 고 Azure Migrate 프로젝트를 사용 하 여 등록 키를 누릅니다.

## <a name="download-the-ova-template"></a>OVA 템플릿 다운로드

1. **마이그레이션 목표** > **서버** > **Azure Migrate: Server 평가**, 클릭 **Discover**합니다.
2. **컴퓨터 검색** > **컴퓨터가 가상화 되어 있습니까?** , 클릭 **예, VMWare vSphere 하이퍼바이저를 사용 하 여**.
3. 클릭 **다운로드** 를 다운로드 합니다. OVA 템플릿 파일입니다.



### <a name="verify-security"></a>보안 확인

배포 하기 전에 OVA 파일이 안전한 인지 확인 합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. OVA에 대 한 해시를 생성 하려면 다음 명령을 실행 합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 어플라이언스 버전 1.0.0.5, 생성된 된 해시는 이러한 설정을 일치 해야 합니다. 

  **알고리즘** | **해시 값**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드 한 파일을 가져오고 VM을 만듭니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.
2. OVF 템플릿 배포 마법사 > **원본**에서 OVA 파일의 위치를 지정합니다.
3. **이름을** 하 고 **위치**, VM에 대 한 이름을 지정 합니다. VM은 호스트 인벤토리 개체를 선택 합니다.
5. **호스트/클러스터**VM를 실행할 클러스터 또는 호스트를 지정 합니다.
6. **저장소**, VM에 대 한 저장소 대상을 지정 합니다.
7. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
8. **네트워크 매핑**, VM에서 연결할 네트워크를 지정 합니다. 네트워크에 Azure Migrate Server 평가에 메타 데이터를 전송 하려면 인터넷에 연결을 해야 합니다.
9. 설정을 검토 및 확인한 다음 **마침**을 클릭합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure 어플라이언스에 액세스할 수 있는지 확인

어플라이언스 VM에 연결할 수 있는지 확인 [Azure Url](migrate-support-matrix-vmware.md#assessment-url-access-requirements)합니다.


## <a name="configure-the-appliance"></a>어플라이언스 구성

처음에 대 한 어플라이언스를 설정 합니다.

1. vSphere Client 콘솔에서 VM > **Open Console**을 마우스 오른쪽 단추로 클릭합니다.
2. 어플라이언스에 대 한 언어, 표준 시간대 및 암호를 제공 합니다.
3. VM에 연결 하 고 어플라이언스 웹 앱의 URL을 열 수 있는 모든 컴퓨터에서 브라우저를 열고: **https://*어플라이언스 이름 또는 IP 주소*: 44368**.

   또는 앱 바로 가기를 클릭 하 여 어플라이언스 바탕 화면에서 앱을 열 수 있습니다.
4. 웹 앱에서 > **필수 조건 설정**, 다음을 수행 합니다.
    - **라이선스**: 사용 조건에 동의하고 타사 정보를 읽습니다.
    - **연결**: 앱의 VM이 인터넷에 액세스할 수 있는지 확인 합니다. 프록시를 사용 하는 VM:
        - 클릭 **프록시 설정**, 형식 프록시 주소 및 수신 포트를 지정 하 고 http://ProxyIPAddress 또는 http://ProxyFQDN 합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
    - **동기화 시간**: 시간을 확인 합니다. 어플라이언스의 시간이 제대로 작동 하려면 검색에 대 한 인터넷 시간과 동기화 해야 합니다.
    - **업데이트 설치**: Azure Migrate는 최신 어플라이언스 업데이트가 설치 되어 있는지 확인 합니다.
    - **VDDK 설치**: Azure Migrate는 VMWare vSphere 가상 디스크 개발 키트 (VDDK)가 설치 되어 있는지 확인 합니다.
        - Azure Migrates Azure로 마이그레이션하는 동안 컴퓨터를 복제 하는 VDDK를 사용 합니다.
        - VMware에서 VDDK 6.7을 다운로드 하 고 어플라이언스에 지정한 위치로 다운로드 한 zip을 풉니다.

## <a name="register-the-appliance-with-azure-migrate"></a>어플라이언스를 Azure Migrate로 등록

1. 클릭 **로그인**합니다. 표시 되지 않으면 브라우저에서 팝업 차단을 사용 하지 않도록 설정 했으므로 있는지 확인 합니다.
2. 새 탭에서 Azure 자격 증명을 사용 하 여 로그인 합니다. 
    - 사용자 이름 및 암호를 로그인 합니다.
    - PIN으로 로그인이 지원 되지 않습니다.
3. 성공적으로 로그인 한 후 웹 앱으로 다시 이동 합니다.
2. Azure Migrate 프로젝트가 만들어진 구독을 선택 합니다. 다음 프로젝트를 선택 합니다.
3. 어플라이언스에 대 한 이름을 지정 합니다. 이름은를 14 자까지 영숫자 또는 이어야 합니다.
4. **등록**을 클릭합니다.


## <a name="start-continuous-discovery"></a>연속 검색 시작

이제, 기기에서 vCenter Server에 연결 하 고 VM 검색을 시작 합니다. 

1. **vCenter Server 세부 정보 지정**, 이름 (FQDN) 또는 vCenter Server의 IP 주소를 지정 합니다. 기본 포트를 유지 하거나 vCenter Server를 수신 하는 사용자 지정 포트를 지정할 수 있습니다.
2. **사용자 이름** 하 고 **암호**, 어플라이언스는 vCenter server에서 Vm을 검색 하기 위해 사용 하는 읽기 전용 계정 자격 증명을 지정 합니다. 계정에 있는지 확인 합니다 [검색에 필요한 권한](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)합니다.
3. 클릭 **연결의 유효성을 검사** 어플라이언스는 vCenter Server에 연결할 수 있는지 확인 합니다.
4. 연결이 설정 되 면 클릭 **저장 및 검색 시작**합니다.


이 검색을 시작합니다. 포털에 표시 하려면 검색 된 Vm의 메타 데이터에 대 한 약 15 분이 걸립니다. 


## <a name="next-steps"></a>다음 단계

에 대 한 자습서를 검토 하세요 [VMware 평가](tutorial-assess-vmware.md) 하 고 [에이전트 없는 마이그레이션](tutorial-migrate-vmware.md)합니다.
