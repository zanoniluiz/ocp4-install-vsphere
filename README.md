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
* Algúns módulos pip (por exemplo, boto3, kubernetes, openshift e pylint)

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