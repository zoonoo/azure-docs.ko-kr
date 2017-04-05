

사용자 지정 스크립트 확장은 도입된 이후 Windows 및 Linux VM 둘 다에서 작업을 구성하는 데 널리 사용되었습니다. Azure 리소스 관리자 템플릿이 도입되면서 이제 사용자는 VM을 프로비전할 뿐만 아니라 VM의 작업을 구성하는 단일 템플릿을 만들 수 있습니다.

## <a name="about-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 정보
Azure Resource Manager 템플릿을 사용하면 리소스 간의 종속성을 정의하여 JSON 언어에서 Azure IaaS 인프라를 선언적으로 지정할 수 있습니다. Azure 리소스 관리자 템플릿에 대한 자세한 개요는 다음 문서를 참조하세요.

* [리소스 그룹 개요](../articles/azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell을 사용하여 템플릿 배포](../articles/virtual-machines/windows/ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>필수 조건
1. [여기](https://azure.microsoft.com/downloads/)에서 해당 운영 체제에 대한 Azure 명령줄 도구를 다운로드합니다.
2. 기존 VM에서 스크립트를 실행하려는 경우, 해당 VM에서 VM 에이전트를 사용하도록 설정되어 있는지 확인하고, 사용하도록 설정되어 있지 않으면 [Linux](../articles/virtual-machines/linux/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 또는 [Windows](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)의 지침에 따라 설치합니다.
3. VM에서 실행할 스크립트를 Azure 저장소에 업로드합니다. 단일 저장소 컨테이너 또는 여러 저장소 컨테이너의 스크립트를 업로드할 수 있습니다.
4. 또는 스크립트를 Github 계정에 업로드할 수 있습니다.
5. 확장을 통해 시작되는 엔트리 스크립트가 다른 스크립트를 시작하는 방식으로 스크립트를 작성해야 합니다.

## <a name="using-the-custom-script-extension"></a>사용자 지정 스크립트 확장 사용
템플릿을 배포하는 경우 Azure 서비스 관리 API에 사용할 수 있는 동일한 버전의 사용자 지정 스크립트 확장을 사용합니다. 확장은 Azure Storage 계정 또는 GitHub 위치에 파일을 업로드하는 동일한 매개 변수 및 시나리오를 지원합니다. 템플릿을 사용하는 동안 중요한 차이점은 majorversion.* 형식에서 버전을 지정하는 대신 확장의 정확한 버전을 지정해야 한다는 점입니다.

