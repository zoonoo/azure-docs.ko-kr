---
title: 클라우드에 의한 Azure VMware 솔루션 간단함 - vRealize 자동화를 위해 프라이빗 클라우드에 vCenter 설정
description: 클라우드심플 프라이빗 클라우드에서 VMware vRealize 자동화의 엔드포인트로 VMware vCenter 서버를 설정하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024843"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>VMware vRealize 자동화를 위해 프라이빗 클라우드에 vCenter 설정

클라우드심플 프라이빗 클라우드에 VMware vCenter 서버를 VMware vRealize 자동화의 엔드포인트로 설정할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

vCenter 서버를 구성하기 전에 다음 작업을 완료합니다.

* 온-프레미스 환경과 프라이빗 클라우드 간의 [사이트 간 VPN 연결을](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) 구성합니다.
* 프라이빗 클라우드의 DNS 서버로 [온-프레미스 DNS 요청을 DNS 전달하도록 구성합니다.](on-premises-dns-setup.md)
* 다음 표에 나열된 사용 권한 집합을 사용하여 vRealize 자동화 IaaS 관리 사용자를 만들려면 [지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 제출합니다.

| 특성 값 | 사용 권한 |
------------ | ------------- |  
| 데이터 저장소 |  공간 할당 <br> 데이터스토어 찾아보기 |
| 데이터스토어 클러스터 | 데이터스토어 클러스터 구성 |
| 폴더 | 폴더 만들기 <br>폴더 삭제 |
| Global |  사용자 지정 특성 관리<br>사용자 지정 특성 설정 |
| 네트워크 | 네트워크 할당 |
| 사용 권한 | 사용 권한 수정 |
| 리소스 | 리소스 풀에 VM 할당<br>전원이 꺼져 있는 가상 시스템 마이그레이션<br>가상 머신에서 전원을 공급하는 마이그레이션 |
| 가상 컴퓨터 인벤토리 |  기존에서 만들기<br>새로 만들기<br>이동<br>제거 | 
| 가상 컴퓨터 상호 작용 |  CD 미디어 구성<br>콘솔 상호 작용<br>디바이스 연결<br>전원 끄기<br>전원 켜기<br>다시 설정<br>일시 중지됨<br>도구 설치 | 
| Virtual Machine 구성 |  기존 디스크 추가<br>새 디스크 추가<br>추가 또는 제거<br>디스크 제거<br>고급<br>CPU 수 변경<br>리소스 변경<br>가상 디스크 확장<br>디스크 변경 추적<br>메모리<br>장치 설정 수정<br>이름 바꾸기<br>참조 설정(버전 5.0 이상)<br>설정<br>스왑 파일 배치 |
| 프로비전 |  사용자 지정<br>복제 템플릿<br>가상 컴퓨터 복제<br>템플릿 배포<br>사용자 지정 사양 읽기 |
| 가상 컴퓨터 상태 | 스냅샷 만들기<br>스냅샷 제거<br>스냅샷으로 되돌리기 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>온-프레미스 환경에 vRealize 자동화 설치

1. 클라우드 심플 지원이 당신을 위해 만든 IaaS 관리자로 vRealize 자동화 IaaS 서버 어플라이언스에 로그인합니다.
2. vRealize 자동화 끝점에 대한 vSphere 에이전트를 배포합니다.
    1. vra-url:5480/installer로 이동 https://하여 *vra-url이* vRealize 자동화 관리 UI에 액세스하는 데 사용하는 URL입니다.*vra-url*
    2. **IaaS 설치 관리자를** 클릭하여 설치 프로그램을 다운로드합니다.<br>
    설치 관리자 파일의 명명 규칙은*vra-url을*@5480.exesetup_.
    3. 설치 관리자를 실행합니다. 시작 화면에서 **다음**을 클릭합니다.
    4. EULA를 수락하고 **다음을**클릭합니다.
    5. 로그인 정보를 제공하고 인증서 **수락을**클릭한 다음 다음 을 **클릭합니다.**
    ![vRA 자격 증명](media/configure-vra-endpoint-login.png)
    6. **사용자 지정 설치** 및 프록시 **에이전트를** 선택하고 다음 을 **클릭합니다.**
    ![vRA 설치 유형](media/configure-vra-endpoint-install-type.png)
    7. IaaS 서버 로그인 정보를 입력하고 **다음을 클릭합니다.** Active Directory를 사용하는 경우 도메인\사용자 형식으로 사용자 이름을 **입력합니다.** 그렇지 않으면 **user@domain** 형식을 사용합니다.
    ![vRA 로그인 정보](media/configure-vra-endpoint-account.png)
    8. 프록시 설정의 경우 에이전트 유형에 대해 **vSphere를** **입력합니다.** 에이전트의 이름을 입력합니다.
    9. **관리자 서비스 호스트** 및 모델 관리자 웹 서비스 **호스트** 필드에 IaaS 서버 FQDN을 입력합니다. **테스트를** 클릭하여 각 FQDN 값에 대한 연결을 테스트합니다. 테스트에 실패하면 IaaS 서버 호스트 이름이 확인되도록 DNS 설정을 수정합니다.
    10. 프라이빗 클라우드의 vCenter 서버 끝점에 대한 이름을 입력합니다. 구성 프로세스의 나중에 사용할 이름을 기록합니다.

        ![vRA 설치 프록시](media/configure-vra-endpoint-proxy.png)

    11. **다음**을 클릭합니다.
    12. **설치**를 클릭합니다.

## <a name="configure-the-vsphere-agent"></a>vSphere 에이전트 구성

1. *vra-url*/vcachttps:// 로 이동 하 고 **ConfigurationAdmin로**로그인 합니다.
2. **인프라** > **끝점 끝점을** > **Endpoints**선택합니다.
3. **새** > **가상** > **vSphere를**선택합니다.
4. 이전 프로시저에서 지정한 vSphere 끝점 이름을 입력합니다.
5. **주소의**경우 *vcenter-fqdn이 vCenter* 서버의 이름인*vcenter-fqdn/sdkhttps://* 형식에 사설 클라우드 vCenter 서버 URL을 입력합니다.
6. CloudSimple Support가 생성한 vRealize 자동화 IaaS 관리 사용자에 대한 자격 증명을 입력합니다.
7. 사용자 자격 증명의 유효성을 검사하려면 **연결 테스트를** 클릭합니다. 테스트에 실패하면 URL, 계정 정보 및 [끝점 이름을](#verify-the-endpoint-name) 확인하고 다시 테스트합니다.
8. 테스트를 성공적으로 마친 후 **확인을** 클릭하여 vSphere 끝점을 만듭니다.
    ![vRA 끝점 구성 액세스](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>끝점 이름 확인

올바른 vCenter 서버 끝점 이름을 식별하려면 다음을 수행합니다.

1. IaaS 어플라이언스에서 명령 프롬프트를 엽니다.
2. 디렉터리를 C:\프로그램 파일(x86)\VMware\vCAC\Agent\agent-name으로 *변경합니다.*
3. 다음 명령을 실행합니다.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

출력은 다음과 유사합니다. `managementEndpointName` 필드의 값은 끝점 이름입니다.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
