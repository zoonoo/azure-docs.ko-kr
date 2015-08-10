
<properties 
    pageTitle="Azure RemoteApp 이미지 요구 사항"
    description="RemoteApp과 함께 사용할 이미지를 만들기 위한 요구 사항 알아보기" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2015" 
    ms.author="elizapo" />



# Azure RemoteApp 이미지에 대한 요구 사항
Azure RemoteApp은 Windows Server 2012 R2 이미지를 사용하여 사용자와 공유할 모든 프로그램을 호스트합니다. 사용자 지정 이미지를 만들려면, 기존 이미지로 시작하거나 [새 이미지를 만듭니다](remoteapp-create-custom-image.md).

> [AZURE.TIP]Azure RemoteApp 구독에서 사용자가 고유의 템플릿 이미지를 만드는데 사용할 수 있는 Azure VM 갤러리의 Windows Server 2012 R2 이미지에 액세스할 수 있다는 사실을 아십니까? [확인하십시오](remoteapp-image-on-azurevm.md).


Azure RemoteApp과 사용하기 위해 업로드할 수 있는 이미지의 요구 사항은 다음과 같습니다.


- 사용자 지정 응용 프로그램은 이미지에 데이터를 로컬로 저장하지 않습니다. 이러한 이미지는 상태 비저장 방식이며 응용 프로그램만 포함해야 합니다.
- 이미지는 손실될 수 있는 데이터를 포함하지 않습니다.
- 이미지 크기는 MB 단위의 배수여야 합니다. 크기가 정확한 배수가 아닌 이미지는 업로드에 실패합니다.
- 이미지 크기는 127GB 이하여야 합니다. 
- VHD 파일에 있어야 합니다. VHDX 파일은 현재 지원되지 않습니다.
- VHD는 세대 2 가상 컴퓨터가 아니어야 합니다.
- VHD는 고정 크기이거나 동적으로 확장될 수 있습니다. 고정 크기의 VHD 파일보다 Azure에 업로드하는 시간이 짧으므로 동적으로 확장되는 VHD가 권장됩니다.
- 디스크는 MBR(마스터 부트 레코드) 파티션 스타일을 사용하여 초기화해야 합니다. GPT(GUID 파티션 테이블) 파티션 스타일은 지원되지 않습니다. 
- VHD는 Windows Server 2012 R2의 단일 설치를 포함해야 합니다. 여러 볼륨을 포함할 수 있지만 한 볼륨만 Windows의 설치를 포함합니다. 
- RDSH(원격 데스크톱 세션 호스트) 역할 및 데스크톱 경험 기능을 설치해야 합니다.
- 원격 데스크톱 연결 브로커 역할은 설치하면 *안 됩니다*.
- EFS(파일 시스템 암호화)를 사용하지 않도록 설정해야 합니다.
- 이미지에 **/oobe /generalize /shutdown** 매개 변수를 사용하여 sysprep을 실행해야 합니다. **/mode:vm** 매개 변수는 사용하지 마세요.
- 스냅숏 체인으로부터의 VHD 업로드는 지원되지 않습니다.
 

<!---HONumber=July15_HO5-->