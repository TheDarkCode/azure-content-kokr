<properties
	pageTitle="서비스 관리 모드의 Azure CLI 명령 | Microsoft Azure"
	description="클래식 배포 모델에서 배포를 관리하기 위한 서비스 관리 모드의 Azure CLI(명령줄 인터페이스) 명령"
	services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="danlep"/>

# ASM(Azure 서비스 관리) 모드의 Azure CLI 명령

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)] [모든 리소스 관리자 모델 명령에 대해 알아볼 수도 있습니다](virtual-machines/azure-cli-arm-commands.md).

이 문서에서는 클래식 배포 모델에서 일반적으로 Azure 리소스를 만들고 관리하는 데 사용한 Azure CLI 명령에 대한 구문 및 옵션이 제공됩니다. ARM(Azure 서비스 관리) 모드에서 CLI를 실행하여 이러한 명령에 액세스합니다. 전체 참조는 아니며 CLI 버전에서 약간 다른 명령 또는 매개 변수를 표시할 수도 있습니다.

시작하려면 먼저 [Azure CLI](xplat-cli-install.md)를 설치하고 [Azure 구독에 연결](xplat-cli-connect.md)합니다.

asm 모드의 명령줄에 있는 현재 명령 구문 및 옵션의 경우 `azure help`를 입력합니다. 그렇지 않고 특정 명령에 대한 도움말을 표시하려면 `azure help [command]`를 입력합니다. 또한 설명서에 특정 Azure 서비스 만들기 및 관리에 대한 CLI 예제가 나와 있습니다.

선택적 매개 변수는 대괄호 안에 표시(예: `[parameter]`)됩니다. 모든 다른 매개 변수는 필수 항목입니다.

여기에 언급된 명령 관련 선택적 매개 변수 이외에 요청 옵션, 상태 코드 등과 같은 자세한 출력을 표시하는 데 사용할 수 있는 세 가지 선택적 매개 변수가 있습니다. `-v` 매개 변수는 자세한 정보를 출력하고 `-vv` 매개 변수는 훨씬 더 자세한 정보를 출력합니다. `--json` 옵션은 결과를 원시 JSON 형식으로 출력합니다.

## asm 모드 설정

현재 asm 모드는 CLI를 처음 설치할 때 기본적으로 사용하도록 설정됩니다. 필요한 경우 다음 명령을 사용하여 asm 모드를 설정합니다.

	azure config mode asm

>[AZURE.NOTE] CLI의 Azure Resource Manager 모드와 asm 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

## 계정 정보 및 게시 설정 관리
CLI에서 계정에 연결하는 한 가지 방법은 Azure 구독 정보를 사용하는 것입니다. 기타 옵션을 보려면 [Azure CLI에서 Azure 구독에 연결](xplat-cli-connect.md)을 참조하세요. 여기에 설명된 대로 게시 설정 파일의 Azure 클래식 포털에서 이 정보를 가져올 수 있습니다. CLI에서 후속 작업에 사용할 영구 로컬 구성 설정으로 게시 설정 파일을 가져올 수 있습니다. 게시 설정을 한 번만 가져와야 합니다.

**account download [options]**

이 명령은 Azure 클래식 포털에서 .publishsettings 파일을 다운로드하는 브라우저를 실행합니다.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [options] &lt;file>**


이 명령은 도구에서 이후 작업에 사용할 수 있도록 publishsettings 파일 또는 인증서를 가져옵니다.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

> [AZURE.NOTE] publishsettings 파일은 여러 구독에 대한 세부 정보(즉, 구독 이름 및 ID)를 포함할 수 있습니다. publishsettings 파일을 가져올 때 첫 번째 구독이 기본 설명으로 사용됩니다. 다른 구독을 사용하려면 다음 명령을 실행합니다. <code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**account clear [options]**

이 명령은 가져온 저장된 게시 설정을 제거합니다. 이 컴퓨터에서 도구 사용을 마치고 이후 작업에 이 계정으로 도구를 사용할 수 없도록 하려면 이 명령을 사용합니다.

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [options]**

가져온 구독 나열

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [options] &lt;subscription&gt;**

현재 구독 설정

###선호도 그룹을 관리하는 명령

**account affinity-group list [options]**

이 명령은 Azure 선호도 그룹을 나열합니다.

가상 컴퓨터 그룹이 여러 물리적 컴퓨터에 걸쳐 있는 경우 선호도 그룹을 설정할 수 있습니다. 선호도 그룹은 각 물리적 컴퓨터를 최대한 가깝게 배치하여 네트워크 대기 시간을 줄이도록 지정합니다.

	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [options] &lt;name&gt;**

이 명령은 새 선호도 그룹을 만듭니다.

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [options] &lt;name&gt;**

이 명령은 선호도 그룹에 대한 세부 정보를 표시합니다.

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [options] &lt;name&gt;**

이 명령은 지정된 선호도 그룹을 삭제합니다.

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###계정 환경을 관리하는 명령

**account env list [options]**

계정 환경 목록

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [options] [environment]**

계정 환경 세부 정보 표시

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [options] [environment]**

이 명령은 계정에 환경을 추가합니다.

**account env set [options] [environment]**

이 명령은 계정 환경을 설정합니다.

**account env delete [options] [environment]**

이 명령은 지정된 환경을 계정에서 삭제합니다.

## 클래식 가상 컴퓨터를 관리하는 명령
다음 다이어그램은 Azure 클라우드 서비스의 프로덕션 배포 환경에서 클래식 Azure 가상 컴퓨터가 호스트되는 방법을 보여 줍니다.

![Azure 기술 다이어그램](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new**는 Blob 저장소에서 드라이브(즉, 다이어그램의 e:\\)를 만들고, **연결**은 이미 만들어졌지만 연결되지 않은 디스크를 가상 컴퓨터에 연결합니다.

**vm create [options] &lt;dns-name> &lt;image> &lt;userName> [password]**

이 명령은 새 Azure 가상 컴퓨터를 만듭니다. 기본적으로 각 가상 컴퓨터는 자체 클라우드 서비스에서 만들어지지만, 여기에 설명된 대로 -c 옵션을 사용하여 가상 컴퓨터를 기존 클라우드 서비스에 추가하도록 지정할 수 있습니다.

Azure 클래식 포털같은 VM 생성 명령은 프로덕션 배포 환경에서 가상 컴퓨터만 생성할 수 있습니다. 클라우드 서비스의 스테이징 배포 환경에서 가상 컴퓨터를 만들 수 있는 옵션이 없습니다. 구독에 기존 Azure 저장소 계정이 없는 경우 이 명령은 계정을 만듭니다.

--location 매개 변수를 사용하여 위치를 지정하거나 --affinity-group 매개 변수를 사용하여 선호도 그룹을 지정할 수 있습니다. 둘 중 아무것도 제공하지 않을 경우 유효한 위치 목록에서 위치를 선택하라는 메시지가 표시됩니다.

제공된 암호는 8-123자여야 하며 이 가상 컴퓨터에 대해 사용 중인 운영 체제의 암호 복잡성 요구 사항을 충족해야 합니다.

SSH를 사용하여 배포된 Linux 가상 컴퓨터를 관리할 경우(일반적인 경우) 가상 컴퓨터를 만들 때 -e 옵션을 통해 SSH를 사용하도록 설정해야 합니다. 가상 컴퓨터를 만든 이후에는 SSH를 사용하도록 설정할 수 없습니다.

Windows 가상 컴퓨터에서는 포트 3389를 끝점으로 추가하여 나중에 RDP를 사용하도록 설정할 수 있습니다.

이 명령에서 지원되는 선택적 매개 변수는 다음과 같습니다.

**-c, --connect**는 호스팅 서비스에서 이미 만들어진 배포 내에 가상 컴퓨터를 만듭니다. -vmname을 이 옵션과 함께 사용하지 않는 경우 새 가상 컴퓨터의 이름이 자동으로 생성됩니다.<br /> **-n, --vm-name** 가상 컴퓨터의 이름을 지정합니다. 이 매개 변수는 기본적으로 호스팅 서비스 이름을 가져옵니다. -vmname을 지정하지 않은 경우 새 가상 컴퓨터의 이름은 &lt;service-name>&lt;id>로 생성되며, 여기서 &lt;id>는 서비스에 1을 더한 기존 가상 컴퓨터의 번호입니다. 예를 들어 이 명령을 사용하여 기존 가상 컴퓨터가 하나인 호스팅 서비스 MyService에 새 가상 컴퓨터를 추가할 경우 새 가상 컴퓨터의 이름은 MyService2입니다.<br /> **-u, --blob-url** 가상 컴퓨터 시스템 디스크를 생성할 대상 Blob 저장소 URL을 지정합니다. <br /> **-z, --vm-size** 가상 컴퓨터의 크기를 지정합니다. 유효한 값은 다음과 같습니다. "ExtraSmall", "Small", "Medium", "Large", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic\_A0", "Basic\_A1", "Basic\_A2", "Basic\_A3", "Basic\_A4", "Standard\_D1", "Standard\_D2", "Standard\_D3", "Standard\_D4", "Standard\_D11", "Standard\_D12", "Standard\_D13", "Standard\_D14", "Standard\_DS1", "Standard\_DS2", "Standard\_DS3", "Standard\_DS4", "Standard\_DS11", "Standard\_DS12", "Standard\_DS13", "Standard\_DS14", "Standard\_G1", "Standard\_G2", "Standard\_G3", "Standard\_G4", "Standard\_G55". 기본값은 "Small"입니다. <br /> **-r** RDP 연결을 Windows 가상 컴퓨터에 추가합니다. <br /> **-e, --ssh** SSH 연결을 Windows 가상 컴퓨터에 추가합니다. <br /> **-t, --ssh-cert** SSH 인증서를 지정합니다. <br /> **-s** 구독 <br /> **-o, --community** 지정된 이미지는 커뮤니티 이미지입니다. <br /> **-w** 가상 네트워크 이름 <br/> **-l,--location**은 위치를 지정합니다(예: "미국 중 북부"). <br /> **-a, --affinity-group**은 선호도 그룹을 지정합니다.<br /> **-w, --virtual-network-name** 새 가상 컴퓨터에 추가할 가상 네트워크를 지정합니다. Azure 클래식 포털에서 가상 네트워크를 설정 및 관리할 수 있습니다. <br /> **-b, --subnet-names**는 가상 컴퓨터를 할당할 서브넷 이름을 지정합니다.

이 예에서 MSFT\_\_Win2K8R2SP1-120514-1520-141205-01-en-us-30GB는 플랫폼에서 제공되는 이미지입니다. 운영 체제 이미지에 대한 자세한 내용은 vm 이미지 목록을 참조하십시오.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************
	info:   vm create command OK

**vm create-from &lt;dns-name> &lt;role-file>**

이 명령은 JSON 역할 파일에서 새 Azure 가상 컴퓨터를 만듭니다.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [options]**

이 명령은 Azure 가상 컴퓨터를 나열합니다. --json 옵션은 결과를 새 JSON 형식으로 반환하도록 지정합니다.

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [options]**

이 명령은 모든 사용 가능한 Azure 계정 위치를 나열합니다.

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name
	data:   ---------------------  ------------
	data:   Azure Preview  West US
	info:   account location list command OK

**vm show [options] &lt;name>**

이 명령은 Azure 가상 컴퓨터에 대한 세부 정보를 보여 줍니다. --json 옵션은 결과를 새 JSON 형식으로 반환하도록 지정합니다.

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   }
	info:   vm show command OK

**vm delete [options] &lt;name>**

이 명령은 Azure 가상 컴퓨터를 삭제합니다. 기본적으로 이 명령은 운영 체제 디스크와 데이터 디스크를 만드는 Azure Blob을 삭제하지 않습니다. Blob을 기반이 되는 가상 컴퓨터와 함께 삭제하려면 -b 옵션을 지정합니다.

	~$ azure vm delete my-vm
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [options] &lt;name>**

이 명령은 Azure 가상 컴퓨터를 시작합니다.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [options] &lt;name>**

이 명령은 Azure 가상 컴퓨터를 다시 시작합니다.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [options] &lt;name>**

이 명령은 Azure 가상 컴퓨터를 종료합니다. -p 옵션을 사용하여 종료할 때 계산 리소스를 해제하지 않도록 지정할 수 있습니다.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;vm-name> &lt;target-image-name>**

이 명령은 Azure 가상 컴퓨터 이미지를 캡처합니다.

가상 컴퓨터 이미지는 가상 컴퓨터 상태가 **중지됨**인 경우에만 캡처할 수 있습니다. 계속하기 전에 가상 컴퓨터를 종료합니다.

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [options] &lt;vm-name> &lt;file-path>**

이 명령은 Azure 가상 컴퓨터 이미지를 파일로 내보냅니다.

	~$ azure vm export "myvm" "C:"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##  Azure 가상 컴퓨터 끝점을 관리하는 명령
다음 다이어그램은 클래식 가상 컴퓨터의 여러 인스턴스에 대한 일반 배포 아키텍처를 보여 줍니다. 이 예에서 포트 3389는 RDP 액세스를 위해 각 가상 컴퓨터에서 열리고, 각 가상 컴퓨터에는 부하 분산 장치에서 트래픽을 가상 컴퓨터로 라우트하는 데 사용되는 내부 IP 주소(예: 168.55.11.1)가 있습니다. 이 내부 IP 주소를 사용하여 가상 컴퓨터 간에 통신할 수도 있습니다.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

가상 컴퓨터에 대한 외부 요청은 부하 분산 장치를 통해 이동합니다. 따라서 여러 가상 컴퓨터를 포함하는 배포에서 특정 가상 컴퓨터에 대해 요청을 지정할 수 없습니다. 여러 가상 컴퓨터를 포함하는 배포의 경우 가상 컴퓨터(vm 포트)와 부하 분산 장치(lb 포트) 사이의 포트 매핑을 구성해야 합니다.

**vm endpoint create &lt;vm-name> &lt;lb-port> [vm-port]**

이 명령은 가상 컴퓨터 끝점을 만듭니다. -u 또는 --enable-direct-server-return을 사용하여 이 끝점에서 직접 서버 반환을 사용하도록 설정할지 여부(기본적으로 사용하지 않도록 설정됨)를 지정할 수도 있습니다.

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [options] &lt;vm-name> &lt;lb-port>[:&lt;vm-port>[:&lt;protocol>[:&lt;enable-direct-server-return>[:&lt;lb-set-name>[:&lt;probe-protocol>[:&lt;probe-port>[:&lt;probe-path>[:&lt;internal-lb-name>]]]]]]]] {1-*}**

여러 vm 끝점을 만듭니다.

**vm endpoint delete [options] &lt;vm-name> &lt;endpoint-name>**

이 명령은 가상 컴퓨터 끝점을 삭제합니다.

	~$ azure vm endpoint delete my-vm http
	azure vm endpoint delete my-vm http
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list &lt;vm-name>**

이 명령은 모든 가상 컴퓨터 끝점을 나열합니다. --json 옵션은 결과를 새 JSON 형식으로 반환하도록 지정합니다.

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [options] &lt;vm-name> &lt;endpoint-name>**

이 명령은 다음과 같은 옵션을 사용하여 vm 끝점을 새 값으로 업데이트합니다.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [options] &lt;vm-name>**

이 명령은 vm의 끝점에 대한 세부 정보를 표시합니다.

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

## Azure 가상 컴퓨터 이미지를 관리하는 명령

가상 컴퓨터 이미지는 필요에 따라 복제할 수 있는 이미 구성된 가상 컴퓨터의 캡처입니다.

**vm image list [options]**

이 명령은 가상 컴퓨터 이미지 목록을 가져옵니다. 이미지 유형에는 "MSFT" 접두사가 붙은 Microsoft에서 만든 이미지, 일반적으로 공급업체의 이름이 접두사로 붙는 타사에서 만든 이미지, 사용자가 만든 이미지의 세 가지가 있습니다. 이미지를 만들려면 기존 가상 컴퓨터를 캡처하거나 Blob 저장소에 업로드된 사용자 지정 .vhd에서 이미지를 만듭니다. 사용자 지정 .vhd를 사용하는 방법에 대한 자세한 내용은 vm image create를 참조하십시오. --json 옵션은 결과를 새 JSON 형식으로 반환하도록 지정합니다.

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK

**vm image show [options] &lt;name>**

이 명령은 가상 컴퓨터 이미지에 대한 세부 정보를 표시합니다.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK

**vm image delete [options] &lt;name>**

이 명령은 가상 컴퓨터 이미지를 삭제합니다.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image
	info:   vm image delete command OK

**vm image create &lt;name> [source-path]**

이 명령은 가상 컴퓨터 이미지를 만듭니다. 사용자 지정 .vhd 파일이 Blob 저장소에 업로드된 다음 이 저장소에서 가상 컴퓨터 이미지가 만들어집니다. 그러면 이 가상 컴퓨터 이미지를 사용하여 가상 컴퓨터를 만듭니다. 위치 및 OS 매개 변수는 필수 항목입니다.

>[AZURE.NOTE]현재 이 명령은 고정된.vhd 파일 업로드만 지원합니다. 동적 .vhd 파일을 업로드하려면 [GO용 Azure VHD 유틸리티](https://github.com/Microsoft/azure-vhd-utils-for-go)를 사용합니다.

일부 시스템에서는 프로세스당 파일 설명자 제한을 부과합니다. 이 제한이 초과되면 도구에서 파일 설명자 제한 오류를 표시합니다. -p &lt;number> 매개 변수와 함께 명령을 다시 실행하여 최대 병렬 업로드 수를 줄일 수 있습니다. 기본 최대 병렬 업로드 수는 96입니다.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

## Azure 가상 컴퓨터 데이터 디스크를 관리하는 명령

데이터 디스크는 가상 컴퓨터에서 사용할 수 있는 Blob 저장소에 있는 .vhd 파일입니다. 데이터 디스크를 Blob 저장소에 배포하는 방법에 대한 자세한 내용은 앞에 나오는 Azure 기술 다이어그램을 참조하십시오.

데이터 디스크 연결 명령(azure vm disk attach 및 azure vm disk attach-new)은 SCSI 프로토콜에 필요한 LUN(논리 단위 번호)을 연결된 데이터 디스크에 할당합니다. 가상 컴퓨터에 연결된 첫 번째 데이터 디스크에는 LUN 0이 할당되고, 다음 데이터 디스크에는 LUN 1이 할당됩니다.

azure vm disk detach 명령을 사용하여 데이터 디스크를 분리할 경우 &lt;lun&gt; 매개 변수를 사용하여 분리할 디스크를 나타냅니다.

> [AZURE > 참고] 항상 할당된 번호가 가장 높은 LUN부터 시작하여 역순으로 데이터 디스크를 분리해야 합니다. Linux SCSI 계층에서는 번호가 더 높은 LUN이 연결된 상태에서 더 낮은 번호의 LUN을 분리할 수 없습니다. 예를 들어 LUN 1이 연결된 상태에서 LUN 0을 분리해서는 안 됩니다.

**vm disk show [options] &lt;name>**

이 명령은 Azure 디스크에 대한 세부 정보를 보여 줍니다.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [options] [vm-name]**

이 명령은 Azure 디스크 또는 지정된 가상 컴퓨터에 연결된 디스크를 나열합니다. 가상 컴퓨터 이름 매개 변수를 사용하여 실행하면 가상 컴퓨터에 연결된 모든 디스크를 반환합니다. Lun 1이 가상 컴퓨터와 함께 만들어지고, 모든 다른 나열된 디스크가 개별적으로 연결됩니다.

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK

가상 컴퓨터 이름 매개 변수 없이 이 명령을 실행하면 모든 디스크가 반환됩니다.

	~$ azure vm disk list
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [options] &lt;name>**

이 명령은 Azure 디스크를 개인 리포지토리에서 삭제합니다. 디스크를 삭제하기 전에 가상 컴퓨터에서 디스크를 분리해야 합니다.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052
	info:   vm disk delete command OK

**vm disk create &lt;name> [source-path]**

이 명령은 Azure 디스크를 등록하며, --blob-url, --location 또는 --affinity-group을 지정해야 합니다. 이 명령을 [소스 경로]와 함께 사용할 경우 지정된 .vhd 파일이 업로드되고 새 이미지가 만들어집니다. 그런 다음 vm disk attach를 사용하여 이 이미지를 가상 컴퓨터에 연결할 수 있습니다.

일부 시스템에서는 프로세스당 파일 설명자 제한을 부과합니다. 이 제한이 초과되면 도구에서 파일 설명자 제한 오류를 표시합니다. -p &lt;number> 매개 변수와 함께 명령을 다시 실행하여 최대 병렬 업로드 수를 줄일 수 있습니다. 기본 최대 병렬 업로드 수는 96입니다.

	~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [options] &lt;source-path> &lt;blob-url> &lt;storage-account-key>**

이 명령을 사용하여 vm 디스크를 업로드할 수 있습니다.

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach &lt;vm-name> &lt;disk-image-name>**

이 명령은 Blob 저장소에 있는 기존 디스크를 클라우드 서비스에 배포된 기존 가상 컴퓨터에 연결합니다.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new &lt;vm-name> &lt;size-in-gb> [blob-url]**

이 명령은 데이터 디스크를 Azure 가상 컴퓨터에 연결합니다. 이 예에서 20은 연결할 새 디스크의 크기(GB)입니다. 필요에 따라 Blob URL을 마지막 인수로 사용하여 만들 대상 Blob을 명시적으로 지정할 수 있습니다. Blob URL을 지정하지 않을 경우 Blob 개체가 자동으로 생성됩니다.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach &lt;vm-name> &lt;lun>**

이 명령은 Azure 가상 컴퓨터에 연결된 데이터 디스크를 분리합니다. &lt;lun>은 분리할 디스크를 식별합니다. 디스크를 분리하기 전에 디스크와 연결된 디스크 목록을 가져오려면 vm disk-list &lt;vm-name>을 사용합니다.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

## Azure 클라우드 서비스를 관리하는 명령

Azure 클라우드 서비스는 웹 역할 및 작업자 역할에 호스트되는 응용 프로그램 및 서비스입니다. 다음 명령을 사용하여 Azure 클라우드 서비스를 관리할 수 있습니다.

**service create [options] &lt;serviceName>**

이 명령은 새 클라우드 서비스를 만듭니다.

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [options] &lt;serviceName>**

이 명령은 Azure 클라우드 서비스에 대한 세부 정보를 표시합니다.

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [options]**

이 명령은 Azure 클라우드 서비스를 나열합니다.

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [options] &lt;name>**

이 명령은 Azure 클라우드 서비스를 삭제합니다.

	~$ azure service delete myservice
	info:   Executing command service delete myservice
	info:   cloud-service delete command OK

강제로 삭제하려면 `-q` 매개 변수를 사용합니다.


## Azure 인증서를 관리하는 명령

Azure 서비스 인증서는 Azure 계정에 연결된 SSL 인증서입니다. Azure 인증서에 대한 자세한 내용은 [인증서 관리](http://msdn.microsoft.com/library/azure/gg981929.aspx)를 참조하세요.

**service cert list [options]**

이 명령은 Azure 인증서를 나열합니다.

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services
	+ Fetching certificates
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
	info:   service cert list command OK

**service cert create &lt;dns-prefix> &lt;file> [password]**

이 명령은 인증서를 업로드합니다. 암호로 보호되지 않는 인증서의 경우 암호 프롬프트를 비워 두십시오.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password:
	+ Creating certificate
	info:   service cert create command OK

**service cert delete [options] &lt;thumbprint>**

이 명령은 인증서를 삭제합니다.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate
	info:   nghinazz : cert deleted
	info:   service cert delete command OK

## 웹앱을 관리하는 명령

Azure 웹 앱은 URI로 액세스할 수 있는 웹 구성입니다. 웹 앱은 가상 컴퓨터에서 호스팅되지만 가상 컴퓨터 자체를 만들어서 배포할 때 세부 정보를 고려할 필요가 없습니다. 이러한 세부 정보는 Azure에서 자동으로 처리됩니다.

**site list [options]**

이 명령은 웹 앱을 나열합니다.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [options] [name]**

이 명령은 웹앱 [name]에 대한 구성 옵션을 설정합니다.

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [options]**

이 명령은 사용자 지정 배포 스크립트를 생성합니다.

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [options] [name]**

이 명령은 새 웹 앱 및 로컬 디렉터리를 만듭니다.

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

> [AZURE.NOTE] 사이트 이름은 고유해야 합니다. 기존 사이트와 같은 DNS 이름을 가진 사이트를 만들 수 없습니다.

**site browse [options] [name]**

이 명령은 브라우저에서 웹 앱을 엽니다.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [options] [name]**

이 명령은 웹 앱에 대한 세부 정보를 표시합니다.

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [options] [name]**

이 명령은 웹 앱을 삭제합니다.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [options] [name]**

이 명령은 두 웹 앱 슬롯을 바꿉니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

**-q 또는 **--quiet**: 확인 프롬프트를 표시하지 않습니다. 자동화된 스크립트에서 이 옵션을 사용합니다.


**site start [options] [name]**

이 명령은 웹 앱을 시작합니다.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [options] [name]**

이 명령은 웹 앱을 중지합니다.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

**site restart [options] [name]

이 명령은 지정된 웹 앱을 중지한 후 시작합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

**--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.


**site location list [options]**

이 명령은 웹 앱 위치를 나열합니다.

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###웹 앱 응용 프로그램 설정을 관리하는 명령

**site appsetting list [options] [name]**

이 명령은 웹 앱에 추가된 앱 설정을 나열합니다.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [options] &lt;keyvaluepair> [name]**

이 명령은 앱 설정을 웹 앱에 키 값 쌍으로 추가합니다.

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [options] &lt;key> [name]**

이 명령은 지정된 앱 설정을 웹 앱에서 삭제합니다.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [options] &lt;key> [name]**

이 명령은 지정된 앱 설정에 대한 세부 정보를 표시합니다.

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###웹 앱 인증서를 관리하는 명령

**site cert list [options] [name]**

이 명령은 웹 앱 인증서 목록을 표시합니다.

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [options] &lt;certificate-path> [name]**

**site cert delete [options] &lt;thumbprint> [name]**

**site cert show [options] &lt;thumbprint> [name]**

이 명령은 인증서 세부 정보를 표시합니다.

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###웹 앱 연결 문자열을 관리하는 명령

**site connectionstring list [options] [name]**

**site connectionstring add [options] &lt;connectionname> &lt;value> &lt;type> [name]**

**site connectionstring delete [options] &lt;connectionname> [name]**

**site connectionstring show [options] &lt;connectionname> [name]**

###웹 앱 기본 문서를 관리하는 명령

**site defaultdocument list [options] [name]**

**site defaultdocument add [options] &lt;document> [name]**

**site defaultdocument delete [options] &lt;document> [name]**

###웹 앱 배포를 관리하는 명령

**site deployment list [options] [name]**

**site deployment show [options] &lt;commitId> [name]**

**site deployment redeploy [options] &lt;commitId> [name]**

**site deployment github [options] [name]**

**site deployment user set [options] [username] [pass]**

###웹 앱 도메인을 관리하는 명령

**site domain list [options] [name]**

**site domain add [options] &lt;dn> [name]**

**site domain delete [options] &lt;dn> [name]**

###웹 앱 처리기 매핑을 관리하는 명령

**site handler list [options] [name]**

**site handler add [options] &lt;extension> &lt;processor> [name]**

**site handler delete [options] &lt;extension> [name]**

###웹 작업을 관리하는 명령

**site job list [options] [name]**

이 명령은 웹 앱의 모든 웹 작업을 나열합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-type** &lt;job-type>: Optional. 웹 작업 유형입니다. 유효한 값은 "triggered" 또는 "continuous"입니다. 기본적으로 모든 종류의 웹 작업을 반환합니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

**site job show [options] &lt;jobName> &lt;jobType> [name]**

이 명령은 특정 웹 작업의 세부 정보를 보여 줍니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name>: Required. 웹 작업의 이름입니다.
+ **--job-type** &lt;job-type>: Required. 웹 작업 유형입니다. 유효한 값은 "triggered" 또는 "continuous"입니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

**site job delete [options] &lt;jobName> &lt;jobType> [name]**

이 명령은 지정된 웹 작업을 삭제합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name> required. 웹 작업의 이름입니다.
+ **--job-type** &lt;job-type> required. 웹 작업 유형입니다. 유효한 값은 "triggered" 또는 "continuous"입니다.
+ **-q** 또는 **--quiet**: 확인 프롬프트를 표시하지 않습니다. 자동화된 스크립트에서 이 옵션을 사용합니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

**site job upload [options] &lt;jobName> &lt;jobType> <jobFile> [name]**

이 명령은 지정된 웹 작업을 삭제합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name>: Required. 웹 작업의 이름입니다.
+ **--job-type** &lt;job-type>: Required. 웹 작업 유형입니다. 유효한 값은 "triggered" 또는 "continuous"입니다.
+ **--job-file** &lt;job-file>: Required. 작업 파일입니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

**site job start [options] &lt;jobName> &lt;jobType> [name]**

이 명령은 지정된 웹 작업을 시작합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name>: Required. 웹 작업의 이름입니다.
+ **--job-type** &lt;job-type>: Required. 웹 작업 유형입니다. 유효한 값은 "triggered" 또는 "continuous"입니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

**site job stop [options] &lt;jobName> &lt;jobType> [name]**

이 명령은 지정된 웹 작업을 중지합니다. 연속 작업만 중지할 수 있습니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name>: Required. 웹 작업의 이름입니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

###웹 작업 기록을 관리하는 명령

**site job history list [options] [jobName] [name]**

이 명령은 지정된 웹 작업의 실행에 대한 기록을 표시합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name>: Required. 웹 작업의 이름입니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

**site job history show [options] [jobName] [runId] [name]**

이 명령은 지정된 웹 작업에 대한 실행 작업의 세부 정보를 가져옵니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--job-name** &lt;job-name>: Required. 웹 작업의 이름입니다.
+ **--run-id** &lt;run-id>: Optional. 실행 기록의 id입니다. 지정하지 않으면 가장 최근 실행을 표시합니다.
+ **--slot** &lt;slot>: 다시 시작할 슬롯의 이름입니다.

###웹 앱 진단을 관리하는 명령

**site log download [options] [name]**

웹 앱 진단을 포함하는 .zip 파일을 다운로드합니다.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [options] [name]**

이 명령은 터미널을 로그 스트리밍 서비스에 연결합니다.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [options] [name]**

이 명령은 웹 앱에 대한 진단 옵션을 구성합니다.

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###웹 앱 리포지토리를 관리하는 명령

**site repository branch [options] &lt;branch> [name]**

**site repository delete [options] [name]**

**site repository sync [options] [name]**

###웹 앱 크기 조정을 관리하는 명령

**site scale mode [options] &lt;mode> [name]**

**site scale instances [options] &lt;instances> [name]**


## Azure 모바일 서비스를 관리하는 명령

Azure 모바일 서비스는 앱에서 백엔드 기능을 사용할 수 있도록 하는 다양한 Azure 서비스를 통합합니다. 모바일 서비스 명령은 다음과 같은 범주로 세분화됩니다.

+ [모바일 서비스 인스턴스를 관리하는 명령](#Mobile_Services)
+ [모바일 서비스 구성을 관리하는 명령](#Mobile_Configuration)
+ [모바일 서비스 테이블을 관리하는 명령](#Mobile_Tables)
+ [모바일 서비스 스크립트를 관리하는 명령](#Mobile_Scripts)
+ [예약된 작업을 관리하는 명령](#Mobile_Jobs)
+ [모바일 서비스의 크기를 조정하는 명령](#Mobile_Scale)

다음은 대부분의 모바일 서비스 명령에 적용되는 옵션입니다.

+ **-h** 또는 **--help**: 출력 사용 정보를 표시합니다.
+ **-s `<id>`** 또는 **--subscription `<id>`**: `<id>`에서 지정한 특정 구독을 사용합니다.
+ **-v** 또는 **--verbose**: 자세한 정보를 출력합니다.
+ **--json**: JSON 출력을 씁니다.

### <a name="Mobile_Services"></a>모바일 서비스 인스턴스를 관리하는 명령

**mobile locations [options]**

이 명령은 모바일 서비스에서 지원되는 지리적 위치를 나열합니다.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US
	info:    North Europe

**mobile create [options] [servicename] [sqlAdminUsername] [sqlAdminPassword]**

이 명령은 SQL 데이터베이스 및 서버와 함께 모바일 서비스를 만듭니다.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-r `<sqlServer>`** 또는 **--sqlServer `<sqlServer>`**: `<sqlServer>`로 지정된 기존 SQL 데이터베이스 서버를 사용합니다.
+ **-d `<sqlDb>`** 또는 **--sqlDb `<sqlDb>`**: `<sqlDb>`로 지정된 기존 SQL 데이터베이스를 사용합니다.
+ **-l `<location>`** 또는 **--location `<location>`**: `<location>`로 지정된 특정 위치에 서비스를 만듭니다. azure mobile locations를 실행하여 사용 가능한 위치를 가져옵니다.
+ **--sqlLocation `<location>`**: 특정 `<location>`에 SQL Server를 만듭니다. 기본 위치는 모바일 서비스 위치입니다.

**mobile delete [options] [servicename]**

이 명령은 SQL 데이터베이스 및 서버와 함께 모바일 서비스를 삭제합니다.

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-d** 또는 **--deleteData**: 이 모바일 서비스의 모든 데이터를 데이터베이스에서 삭제합니다.
+ **-a** 또는 **--deleteAll**: SQL 데이터베이스 및 서버를 삭제합니다.
+ **-q** 또는 **--quiet**: 확인 프롬프트를 표시하지 않습니다. 자동화된 스크립트에서 이 옵션을 사용합니다.

**mobile list [options]**

이 명령은 모바일 서비스를 나열합니다.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [options] [servicename]**

이 명령은 모바일 서비스에 대한 세부 정보를 표시합니다.

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK

**mobile restart [options] [servicename]**

이 명령은 모바일 서비스 인스턴스를 다시 시작합니다.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [options] [servicename]**

이 명령은 `error`를 제외한 모든 로그 유형을 필터링하여 모바일 서비스 로그를 반환합니다.

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-r `<query>`** 또는 **--query `<query>`**: 지정된 로그 쿼리를 실행합니다.
+ **-t `<type>`** 또는 **--type `<type>`**: 항목 `<type>`로 반환된 로그를 필터링하며, `information`, `warning` 또는 `error`일 수 있습니다.
+ **-k `<skip>`** 또는 **--skip `<skip>`**: `<skip>`로 지정된 행 수를 건너뜁니다.
+ **-p `<top>`** 또는 **--top `<top>`**: `<top>`로 지정된 특정 수의 행을 반환합니다.

> [AZURE.NOTE] **--query** 매개 변수는 **--type**, **--skip** 및 **--top**보다 우선합니다.

**mobile recover [options] [unhealthyservicename] [healthyservicename]**

이 명령은 다른 지역의 정상 모바일 서비스로 이동하는 방식으로 비정상 모바일 서비스를 복구합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

**-q** 또는 **--quiet**: 복구 확인 프롬프트를 표시하지 않습니다.

**mobile key regenerate [options] [servicename] [type]**

이 명령은 모바일 서비스 응용 프로그램 키를 다시 생성합니다.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

키 유형은 `master` 및 `application`입니다.

> [AZURE.NOTE] 키를 다시 생성하면 이전 키를 사용하는 클라이언트는 모바일 서비스에 액세스할 수 없습니다. 응용 프로그램 키를 다시 생성할 경우 앱을 새 키 값으로 업데이트해야 합니다.

**mobile key set [options] [servicename] [type] [value]**

이 명령은 모바일 서비스 키를 특정 값으로 설정합니다.


### <a name="Mobile_Configuration"></a>모바일 서비스 구성을 관리하는 명령

**mobile config list [options] [servicename]**

이 명령은 모바일 서비스에 대한 구성 옵션을 나열합니다.

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config list [options] [servicename]**

이 명령은 모바일 서비스에 대한 특정 구성 옵션(이 경우 동적 스키마)을 가져옵니다.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [options] [servicename] [key] [value]**

이 명령은 모바일 서비스에 대한 특정 구성 옵션(이 경우 동적 스키마)을 설정합니다.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


### <a name="Mobile_Tables"></a>모바일 서비스 테이블을 관리하는 명령

**mobile table list [options] [servicename]**

이 명령은 모바일 서비스의 모든 테이블을 나열합니다.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [options] [servicename] [tablename]**

이 명령은 특정 테이블에 대한 반환 세부 정보를 표시합니다.

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [options] [servicename] [tablename]**

이 명령은 테이블을 만듭니다.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-p `&lt;permissions>`** 또는 **--permissions `&lt;permissions>`**: 쉼표로 구분된 `<operation>`=`<permission>` 쌍이며, 여기서 `<operation>`은 `insert`, `read`, `update` 또는 `delete`이며 `&lt;permissions>`는 `public`, `application`(기본값), `user` 또는 `admin`입니다.

**mobile data read [options] [servicename] [tablename] [query]**

이 명령은 테이블에서 데이터를 읽습니다.

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-k `<skip>`** 또는 **--skip `<skip>`**: `<skip>`로 지정된 행 수를 건너뜁니다.
+ **-t `<top>`** 또는 **--top `<top>`**: `<top>`로 지정된 특정 수의 행을 반환합니다.
+ **-l** 또는 **--list**: 데이터를 목록 형식으로 반환합니다.

**mobile table update [options] [servicename] [tablename]**

이 명령은 테이블에 대한 삭제 권한을 관리자만으로 변경합니다.

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-p `&lt;permissions>`** 또는 **--permissions `&lt;permissions>`**: 쉼표로 구분된 `<operation>`=`<permission>` 쌍이며, 여기서 `<operation>`은 `insert`, `read`, `update` 또는 `delete`이며 `&lt;permissions>`는 `public`, `application`(기본값), `user` 또는 `admin`입니다.
+ **--deleteColumn `<columns>`**: 쉼표로 구분된 삭제할 열 목록이며 `<columns>`로 지정됩니다.
+ **-q** 또는 **--quiet**: 확인 메시지를 표시하지 않고 열을 삭제합니다.
+ **--addIndex `<columns>`**: 인덱스에 포함할 열의 쉼표로 구분된 목록입니다.
+ **--deleteIndex `<columns>`**: 인덱스에서 제외할 열의 쉼표로 구분된 목록입니다.

**mobile table delete [options] [servicename] [tablename]**

이 명령은 테이블을 삭제합니다.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

확인하지 않고 테이블을 삭제하려면 -q 매개 변수를 지정합니다. 이렇게 하면 자동화 스크립트가 차단되지 않습니다.

**mobile data truncate [options] [servicename] [tablename]**

이 명령은 모든 데이터 행을 테이블에서 제거합니다.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>스크립트를 관리하는 명령

이 섹션에 나오는 명령을 사용하여 모바일 서비스에 속하는 서버 스크립트를 관리할 수 있습니다. 자세한 내용은 [모바일 서비스에서 서버 스크립트 작업](mobile-services/mobile-services-how-to-use-server-scripts.md)(영문)을 참조하십시오.

**mobile script list [options] [servicename]**

이 명령은 테이블 스크립트와 스케줄러 스크립트를 포함하여 등록된 스크립트를 나열합니다.

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [options] [servicename] [scriptname]**

이 명령은 TodoItem 테이블의 삽입 스크립트를 `table` 하위 폴더의 `todoitem.insert.js` 파일로 다운로드합니다.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-p `<path>`** 또는 **--path `<path>`**: 스크립트를 저장할 파일 내 위치입니다. 기본값은 현재 작업 디렉터리입니다.
+ **-f `<file>`** 또는 **--file `<file>`**: 스크립트를 저장할 파일의 이름입니다.
+ **-o** 또는 **--override**: 기존 파일을 덮어씁니다.
+ **-c** 또는 **--console**: 파일 대신 콘솔에 스크립트를 씁니다.

**mobile script upload [options] [servicename] [scriptname]**

이 명령은 `table` 하위 폴더에서 `todoitem.insert.js`라는 새 스크립트를 업로드합니다.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

파일 이름은 테이블 및 작업 이름에서 작성되어야 하며, 명령이 실행되는 위치를 기준으로 table 하위 폴더에 있어야 합니다. **-f `<file>`** 또는 **--file `<file>`** 매개 변수를 사용하여 등록할 스크립트가 포함된 파일에 다른 파일 이름 및 경로를 지정할 수도 있습니다.


**mobile script delete [options] [servicename] [scriptname]**

이 명령은 기존 삽입 스크립트를 TodoItem 테이블에서 제거합니다.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>예약된 작업을 관리하는 명령

이 섹션에 나오는 명령을 사용하여 모바일 서비스에 속하는 예약된 작업을 관리할 수 있습니다. 자세한 내용은 [작업 예약](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx)을 참조하십시오.

**mobile job list [options] [servicename]**

이 명령은 예약된 작업을 나열합니다.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [options] [servicename] [jobname]**

이 명령은 시간 단위로 실행하도록 예약된 `getUpdates`라는 새 작업을 만듭니다.

	~$azure mobile job create -i 1 -u hour todolist getUpdates
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-i `<number>`** 또는 **--interval `<number>`**:작업 간격을 정수로 나타냅니다. 기본값은 `15`입니다.
+ **-u `<unit>`** 또는 **--intervalUnit `<unit>`**: _interval_의 단위이며 다음 값 중 하나일 수 있습니다.
	+ **분**(기본값)
	+ **시간**
	+ **일**
	+ **월**
	+ **없음**(주문형 작업)
+ **-t `<time>`** **--startTime `<time>`** ISO 형식의 스크립트에 대한 첫 번째 실행의 시작 시간입니다. 기본값은 `now`입니다.

> [AZURE.NOTE] 스크립트를 계속 업로드해야 하므로 새 작업은 사용 안 함 상태로 만들어집니다. **mobile script upload** 명령을 사용하여 스크립트를 업로드하고 **mobile job update** 명령을 사용하여 작업을 사용하도록 설정합니다.

**mobile job update [options] [servicename] [jobname]**

다음 명령은 사용하지 않도록 설정된 `getUpdates` 작업을 사용하도록 설정합니다.

	~$azure mobile job update -a enabled todolist getUpdates
	info:    Executing command mobile job update
	info:    mobile job update command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-i `<number>`** 또는 **--interval `<number>`**:작업 간격을 정수로 나타냅니다. 기본값은 `15`입니다.
+ **-u `<unit>`** 또는 **--intervalUnit `<unit>`**: _interval_의 단위이며 다음 값 중 하나일 수 있습니다.
	+ **분**(기본값)
	+ **시간**
	+ **일**
	+ **월**
	+ **없음**(주문형 작업)
+ **-t `<time>`** **--startTime `<time>`** ISO 형식의 스크립트에 대한 첫 번째 실행의 시작 시간입니다. 기본값은 `now`입니다.
+ **-a `<status>`** 또는 **--status `<status>`**: 작업 상태를 나타내며 `enabled` 또는 `disabled`입니다.

**mobile job delete [options] [servicename] [jobname]**

이 명령은 getUpdates 예약된 작업을 TodoList 서버에서 제거합니다.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

> [AZURE.NOTE] 작업을 삭제하면 업로드된 스크립트도 삭제됩니다.

### <a name="Mobile_Scale"></a>모바일 서비스의 크기를 조정하는 명령

이 섹션에 나오는 명령을 사용하여 모바일 서비스를 크기 조정할 수 있습니다. 자세한 내용은 [모바일 서비스 확장](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx)을 참조하십시오.

**mobile scale show [options] [servicename]**

이 명령은 현재 계산 모드, 인스턴스 수를 비롯한 배율 정보를 표시합니다.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [options] [servicename]**

이 명령은 모바일 서비스의 배율을 free 모드에서 premium 모드로 변경합니다.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-c `<mode>`** 또는 **--computeMode `<mode>`**: 계산 모드는 `Free` 또는 `Reserved`여야 합니다.
+ **-i `<count>`** 또는 **--numberOfInstances `<count>`**: reserved 모드에서 실행 중일 때 사용되는 인스턴스 수를 나타냅니다.

> [AZURE.NOTE] 계산 모드를 `Reserved`로 설정한 경우 같은 지역의 모든 모바일 서비스가 premium 모드로 실행됩니다.


###모바일 서비스에 대한 미리 보기 기능을 사용하는 명령

**mobile preview list [options] [servicename]**

이 명령은 지정된 서비스에서 사용할 수 있는 미리 보기 기능 및 이 기능이 사용되는지를 표시합니다.

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [options] [servicename] [featurename]**

이 명령은 모바일 서비스에 대해 지정된 미리 보기 기능을 사용하도록 설정합니다. 한번 사용하도록 설정한 모바일 서비스는 사용하지 않도록 설정할 수 없습니다.

###모바일 서비스 API를 관리하는 명령

**mobile api list [options] [servicename]**

이 명령은 사용자가 모바일 서비스에 대해 만든 모바일 서비스 사용자 지정 API 목록을 표시합니다.

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [options] [servicename] [apiname]**

모바일 서비스 사용자 지정 API를 만듭니다.

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

**-p** 또는 **--permissions** &lt;permissions>: 쉼표로 구분된 &lt;method>=&lt;permission> 쌍 목록입니다.

**mobile api update [options] [servicename] [apiname]**

이 명령은 지정된 모바일 서비스 사용자 지정 API를 업데이트합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-p** 또는 **--permissions** &lt;permissions>: 쉼표로 구분된 &lt;method>=&lt;permission> 쌍 목록입니다.
+ **-f** 또는 **--force**: 사용 권한 메타데이터 파일에 대한 사용자 지정 변경 내용을 재정의합니다.

**mobile api delete [options] [servicename] [apiname]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

이 명령은 지정된 모바일 서비스 사용자 지정 API를 삭제합니다.

###모바일 응용 프로그램 앱 설정을 관리하는 명령

**mobile appsetting list [options] [servicename]**

이 명령은 지정된 서비스에 대한 모바일 응용 프로그램 앱 설정을 표시합니다.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [options] [servicename] [name] [value]**

이 명령은 모바일 서비스에 대한 사용자 지정 응용 프로그램 설정을 추가합니다.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [options] [servicename] [name]**

이 명령은 모바일 서비스에 대한 지정된 응용 프로그램 설정을 제거합니다.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [options] [servicename] [name]**

이 명령은 모바일 서비스에 대한 지정된 응용 프로그램 설정을 제거합니다.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

## 도구 로컬 설정 관리

로컬 설정은 구독 ID 및 기본 저장소 계정 이름입니다.

**config list [options]**

이 명령은 구성 설정을 표시합니다.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [options] &lt;name&gt;,&lt;value&gt;**

이 명령은 구성 설정을 변경합니다.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

## 서비스 버스를 관리하는 명령

이러한 명령을 사용하여 서비스 버스 계정을 관리할 수 있습니다.

**sb namespace check [options] &lt;name>**

서비스 버스 네임스페이스가 올바르며 사용 가능한지 확인합니다.

**sb namespace create &lt;name> &lt;location>**

새 서비스 버스 네임스페이스를 만듭니다.

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete &lt;name>**

네임스페이스를 제거합니다.

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

계정에 대해 만들어진 모든 네임스페이스를 나열합니다.

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

모든 사용 가능한 네임스페이스 위치 목록을 표시합니다.

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show &lt;name>**

특정 네임스페이스에 대한 세부 정보를 표시합니다.

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify &lt;name>**

네임스페이스를 사용할 수 있는지 확인합니다.

## 저장소 개체를 관리하는 명령

###저장소 계정을 관리하는 명령

**storage account list [options]**

이 명령은 구독에 대한 저장소 계정을 표시합니다.

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [options] <name>**

이 명령은 URI 및 계정 속성을 포함하여 지정된 저장소 계정에 대한 정보를 표시합니다.

**storage account create [options] <name>**

이 명령은 제공된 옵션에 따라 저장소 계정을 만듭니다.

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-e** 또는 **--label** &lt;label>: 저장소 계정에 대한 레이블입니다.
+ **-d** 또는 **--description** &lt;description>: 설명 저장소 계정입니다.
+ **-l** 또는 **--location** &lt;name>: 저장소 계정을 만들 지리적 영역입니다.
+ **-a** 또는 **--affinity-group** &lt;name>: 저장소 계정을 연결할 선호도 그룹입니다.
+ **--type**: 만드는 계정 유형을 나타냅니다. 중복 옵션이 있는 표준 저장소(LRS/ZRS/GRS/RAGRS) 또는 프리미엄 저장소(PLRS).

**storage account set [options] <name>**

이 명령은 지정된 저장소 계정을 업데이트합니다.

	~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-e** 또는 **--label** &lt;label>: 저장소 계정에 대한 레이블입니다.
+ **-d** 또는 **--description** &lt;description>: 설명 저장소 계정입니다.
+ **-l** 또는 **--location** &lt;name>: 저장소 계정을 만들 지리적 영역입니다.
+ **--type**: 새 계정 유형을 나타냅니다. 중복 옵션이 있는 표준 저장소(LRS/ZRS/GRS/RAGRS) 또는 프리미엄 저장소(PLRS).

**storage account delete [options] <name>**

이 명령은 지정된 저장소 계정을 삭제합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

**-q** 또는 **--quiet**: 확인 프롬프트를 표시하지 않습니다. 자동화된 스크립트에서 이 옵션을 사용합니다.

###저장소 계정 키를 관리하는 명령

**storage account keys list [options] <name>**

이 명령은 지정된 저장소 계정에 대한 기본 및 보조 키를 나열합니다.

**storage account keys renew [options] <name>**

###저장소 컨테이너를 관리하는 명령

**storage container list [options] [prefix]**

이 명령은 지정된 저장소 계정에 대한 저장소 컨테이너 목록을 표시합니다. 저장소 계정은 연결 문자열이나 저장소 계정 이름 및 계정 키로 지정됩니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **-p** 또는 **-prefix** &lt;prefix>: 저장소 컨테이너 이름 접두사입니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그 모드에서 저장소 명령을 실행합니다.

**storage container show [options] [container]** **storage container create [options] [container]**

이 명령은 지정된 저장소 계정에 대한 저장소 컨테이너를 만듭니다. 저장소 계정은 연결 문자열이나 저장소 계정 이름 및 계정 키로 지정됩니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-p** 또는 **-prefix** &lt;prefix>: 저장소 컨테이너 이름 접두사입니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열
+ **--debug**: 디버그 모드에서 저장소 명령을 실행합니다.

**storage container delete [options] [container]**

이 명령은 지정된 저장소 컨테이너를 삭제합니다. 저장소 계정은 연결 문자열이나 저장소 계정 이름 및 계정 키로 지정됩니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-p** 또는 **-prefix** &lt;prefix>: 저장소 컨테이너 이름 접두사입니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그 모드에서 저장소 명령을 실행합니다.

**storage container set [options] [container]**

이 명령은 저장소 컨테이너에 대한 액세스 제어 목록을 설정합니다. 저장소 계정은 연결 문자열이나 저장소 계정 이름 및 계정 키로 지정됩니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-p** 또는 **-prefix** &lt;prefix>: 저장소 컨테이너 이름 접두사입니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그 모드에서 저장소 명령을 실행합니다.

###저장소 Blob을 관리하는 명령

**storage blob list [options] [container] [prefix]**

이 명령은 지정된 저장소 컨테이너에 있는 저장소 Blob 목록을 반환합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-p** 또는 **-prefix** &lt;prefix>: 저장소 컨테이너 이름 접두사입니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그 모드에서 저장소 명령을 실행합니다.

**storage blob show [options] [container] [blob]**

이 명령은 지정된 저장소 Blob에 대한 세부 정보를 표시합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-p** 또는 **-prefix** &lt;prefix>: 저장소 컨테이너 이름 접두사입니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그에서 저장소 명령을 실행합니다.

**storage blob delete [options] [container] [blob]**

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-b** 또는 **--blob** &lt;blobName>: 삭제할 Blob 저장소의 이름입니다.
+ **-q** 또는 **--quiet**: 확인하지 않고 지정된 저장소 Blob을 제거합니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그에서 저장소 명령을 실행합니다.

**storage blob upload [options] [file] [container] [blob]**

이 명령은 지정된 파일을 지정된 저장소 Blob에 업로드합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-b** 또는 **--blob** &lt;blobName>: 업로드할 Blob 저장소의 이름입니다.
+ **-t** 또는 **-blobtype** & l t; blobtype >: 저장소 blob 유형, 페이지 또는 블록입니다.
+ **-p** 또는 **--properties** &lt;properties>: 업로드된 파일에 대한 저장소 Blob 속성입니다. 속성은 키=값 쌍이며 세미콜론 (;)으로 구분합니다. 사용 가능한 속성은 contentType, contentEncoding, contentLanguage, cacheControl입니다.
+ **-m** 또는 **--metadata** &lt;metadata>: 업로드된 파일에 대한 저장소 Blob 메타데이터입니다. 메타데이터는 키=값 쌍이며 세미콜론 (;)으로 구분합니다.
+ **--concurrenttaskcount** &lt;concurrenttaskcount>: 최대 동시 업로드 요청 수입니다.
+ **-q** 또는 **--quiet**: 확인하지 않고 지정된 저장소 Blob을 덮어씁니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그에서 저장소 명령을 실행합니다.

**storage blob download [options] [container] [blob] [destination]**

이 명령은 지정된 저장소 Blob을 다운로드합니다.

이 명령은 다음과 같은 추가 옵션을 지원합니다.

+ **--container** &lt;container>: 만들려는 저장소 컨테이너의 이름입니다.
+ **-b** 또는 **--blob** &lt;blobName>: Blob 저장소 이름입니다.
+ **-d** 또는 **--destination** [destination]: 다운로드 대상 파일 또는 디렉토리 경로입니다.
+ **-m** 또는 **--checkmd5**: 다운로드된 파일에 대한 check md5sum입니다.
+ **--concurrenttaskcount** &lt;concurrenttaskcount> 최대 동시 업로드 요청 수입니다.
+ **-q** 또는 **--quiet**: 확인하지 않고 대상 파일을 덮어씁니다.
+ **-a** 또는 **--account-name** &lt;accountName>: 저장소 계정 이름입니다.
+ **-k** 또는 **--account-key** &lt;accountKey>: 저장소 계정 키입니다.
+ **-c** 또는 **--connection-string** &lt;connectionString>: 저장소 연결 문자열입니다.
+ **--debug**: 디버그에서 저장소 명령을 실행합니다.

## SQL 데이터베이스를 관리하는 명령

이러한 명령을 사용하여 Azure SQL 데이터베이스를 관리할 수 있습니다.

###SQL Server를 관리하는 명령.

이러한 명령을 사용하여 SQL Server를 관리할 수 있습니다.

**sql server create &lt;administratorLogin> &lt;administratorPassword> &lt;location>**

새 데이터베이스 서버 만들기

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show &lt;name>**

서버 세부 정보를 표시합니다.

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

서버 목록을 가져옵니다.

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete &lt;name>**

서버 삭제

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###SQL 데이터베이스를 관리하는 명령

이러한 명령을 사용하여 SQL 데이터베이스를 관리합니다.

**sql db create [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

새 데이터베이스 인스턴스 만들기

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

데이터베이스 세부 정보를 표시합니다.

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [options] &lt;serverName> &lt;administratorPassword>**

데이터베이스를 나열합니다.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

데이터베이스를 삭제합니다.

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###SQL Server 방화벽 규칙을 관리하는 명령

이러한 명령을 사용하여 SQL Server 방화벽 규칙을 관리할 수 있습니다.

**sql firewallrule create [options] &lt;serverName> &lt;ruleName> &lt;startIPAddress> &lt;endIPAddress>**

SQL Server에 대한 새 방화벽 규칙을 만듭니다.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [options] &lt;serverName> &lt;ruleName>**

방화벽 규칙 세부 정보를 표시합니다.

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [options] &lt;serverName>**

방화벽 규칙을 나열합니다.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [options] &lt;serverName> &lt;ruleName>**

이 명령은 방화벽 규칙을 삭제합니다.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

## 가상 네트워크를 관리하는 명령

이러한 명령을 사용하여 가상 네트워크를 관리할 수 있습니다.

**network vnet create [options] &lt;location>**

새 가상 네트워크를 만듭니다.

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show &lt;name>**

가상 네트워크의 세부 정보를 표시합니다.

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**network vnet list**

모든 기존 가상 네트워크를 나열합니다.

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete &lt;name>**

지정된 가상 네트워크를 삭제합니다.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [file-path]**

고급 네트워크 구성의 경우 네트워크 구성을 로컬로 내보낼 수 있습니다. 보내낸 네트워크 구성은 DNS 서버 설정, 가상 네트워크 설정, 로컬 네트워크 사이트 설정 및 기타 설정을 포함합니다.

**network import [file-path]**

로컬 네트워크 구성을 가져옵니다.

**network dnsserver register [options] &lt;dnsIP>**

네트워크 구성에서 이름을 확인하는 데 사용할 DNS 서버를 등록합니다.

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

네트워크 구성에 등록된 모든 DNS 서버를 나열합니다.

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [options] &lt;dnsIP>**

DNS 서버 항목을 네트워크 구성에서 제거합니다.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!---HONumber=AcomDC_0629_2016-->