# Automação para Instalação e Configuração de OCP 4.10 e 4.12 IPI em vSphere
Ansible Install OCP 4

> **NOTA**: Este repositório estará em constante atualização

Este repositório fornece uma maneira de implantar OpenShift 4 IPI em vSphere.

Ele foi desenvolvido em módulos, onde será possível executar os módulos definindo **TRUE** para cada módulo.

Cada operação Day2 deste repositório pode ser executada isoladamente (por exemplo, executar a instalação do logging e implantar o OpenShift Data Foundation)

Abaixo mais informações sobre os módulos e como ativa-los

## Recursos

As principais características são:

* Fácil implantação do OpenShift IPI em vSphere, seguindo as melhores práticas de deploy e configuração
* Não será necessário a implantação de nenhum outro software na máquina Bastion
* Configurações PostInstall e Day2Operations
* Modularizado e capaz de executar **Day2Ops** específicos
* Versátil e modular com parâmetros

## Instalação e Pré-requisitos

### Requirements

* Python 3.9
* Unzip
* Ansible
* Algúns módulos pip (por exemplo, boto3, kubernetes, openshift e pylint)

```
yum install python39 unzip -y
```

```
pip-3.9 install ansible
```

### Criar/Personalizar as Variáveis yaml

* Copie ou gere o vars.yml e personalize para atender às suas necessidades:

```
cp -pr examples/vars.yml vars/vars.yml
```

* Preencha os parâmetros do arquivo **vault/vault.yml** e depois gere o Vault-File:

```
$ ansible-vault encrypt vault/vault.yml
```

* Parâmetros do arquivo vault.yml

```
ocp4_pull_secret: '<<< pull_secret_azure >>>'

#vSphere
apiVip: IP API
ingressVip: IP Ingress
vsphereCluster: "Cluster"
vsphereDatacenter: "DC"
vsphereDatastore: "Datastore"
vsphereNetwork: "Network"
vspherePassword: "Password"
vsphereUsername: "User"
vsphereVcenter: "vCenter IP ou FQDN"
vsphereFolder: "VMs Folder"
vsphereDiskType: "Tipo de discos"

#Htpasswd
ocp4_htpasswd_admin: "nome do usuário"
ocp4_htpasswd_admin_password: "senha"
```

para obter o pull_secret vá para [OCP4 Install](https://cloud.redhat.com/openshift/install)

* Gere o arquivo .vault-password-file e coloque a senha

```
touch .vault-password-file
echo "yourpasswordfancy" >> .vault-password-file
```

## Uso

### Implantação automatizada end2end do cluster Openshift4 (end2end)

* Ativando os módulos

Abra o arquivo **vars/modules.yml** e define **True** ou **False**

```
#Checking pre requisites OCP
check_pre_reqs: True

#Deploy pre requisites Playbooks
pre_reqs_install: True

#Install OCP 4.10 or 4.12
only_post_install: True

#Configure Infra Nodes
infra_post_install: True

#Install OCP Logging
install_logging: True

#Configure Backup OCP
bkp_etcd_post_install: True

#Install Operator ODF and configure Machines 
install_odf: True

#Backup files to use deploy and configuration
backup_files: True

#Configure timezone and Chrony
set_timezone_chrony: True

#Configure Auth (Htpasswd and/or LDAP)
set_auth: True
```
Execute e espere um pouco:

```
./auto_deploy.sh
```

## Implantação personalizada

O contêiner para a instalação pode ser usado para Post Install ou Day2Operations sem implantar todo o cluster

* Para implantar apenas day2ops:

```
ansible-playbook -i ,localhost deploy_day2ops.yml --ask-vault-pass
```

* Somente para instalação e sem day2ops:

```
ansible-playbook -i ,localhost deploy_only.yml --ask-vault-pass
```

* Para instalar apenas um day2ops específico:

```
ansible-playbook -i ,localhost deploy_only_<MY_DAY2OPS>.yml
```

## Personalizações (WIP)

#### Kubeconfig

O instalador examinará o kubeconfig específico em {{ user_path }}/auth/kubeconfig, mas você pode usar
seu próprio kubeconfig para implantar este day2ops sempre que estiver usando:

```
kubeconfig: ~/.kube/ocp4-opentlc
```

#### Variáveis Openshift Cluster

```
ocp4_version: '4.10.47'
cluster_name: 'ocp'
ocp4_base_domain: 'example.com'

#Workers - Define size for the machines Workers
diskSizeWorker: "120"
cpuWorkers: "4"
coresPerWorkers: "4"
memoryMBWorkers: "16384"
replicasWorker: "3"

#Masters - Define size for the machines Master
diskSizeMaster: "120"
cpuMaster: "4"
coresPerSocketMaster: "4"
memoryMBMaster: "16384"
replicasMaster: "3"

#Network - Define networks for the PODs, Service and MachineCRD
cluster_Network: "10.128.0.0/14"
machine_Network: "10.0.0.0/24"
service_Network: "172.30.0.0/16"
```
