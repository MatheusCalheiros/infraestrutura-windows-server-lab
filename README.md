# 🏢 Implementação de Infraestrutura Corporativa com Windows Server (Laboratório Prático)

## 📌 Objetivo

Implementar um ambiente corporativo virtualizado simulando a
infraestrutura de uma empresa, incluindo:

-   Active Directory (AD DS)
-   DNS integrado ao domínio
-   DHCP corporativo
-   Estrutura organizacional (OU)
-   Grupos de segurança
-   Servidor de arquivos com permissões NTFS e SMB
-   Integração de estação cliente ao domínio
-   Troubleshooting de autenticação e permissões

------------------------------------------------------------------------

## 🖥️ Ambiente Utilizado

-   VirtualBox
-   Windows Server 2025 (Server Core)
-   Windows 10 (Cliente)
-   Rede interna isolada (LAB-EMPRESA)
-   Domínio: `empresa.local`
-   Servidor: `SRV-DC01`
-   Cliente: `CLIENTE01`

------------------------------------------------------------------------

## 1️⃣ Configuração Inicial do Servidor

-   Instalação do Windows Server (Server Core)
-   Configuração de IP estático:
    -   IP: 192.168.10.10
    -   Máscara: 255.255.255.0
    -   DNS: 192.168.10.10

------------------------------------------------------------------------

## 2️⃣ Active Directory

Instalação da role:

``` powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

Promoção para Controlador de Domínio:

``` powershell
Install-ADDSForest
```

Domínio criado: `empresa.local`
<img width="979" height="668" alt="1 - Get-ADDomain" src="https://github.com/user-attachments/assets/4963f912-5cfc-427a-9831-b8fd2d19b42c" />

------------------------------------------------------------------------

## 3️⃣ Estrutura Organizacional

Criação de OUs:

``` powershell
New-ADOrganizationalUnit -Name "Financeiro" -Path "DC=empresa,DC=local"
New-ADOrganizationalUnit -Name "TI" -Path "DC=empresa,DC=local"
```
<img width="967" height="670" alt="2 - Get-ADOrganizationalUnit - Filter" src="https://github.com/user-attachments/assets/e7cc1684-4e10-4612-9510-2d724569d3a0" />

------------------------------------------------------------------------

## 4️⃣ Grupos e Usuários

Grupos: - GRP_FINANCEIRO - GRP_TI

Usuários criados por departamento e adicionados aos respectivos grupos.

Validação:

``` powershell
Get-ADPrincipalGroupMembership joao.financeiro
```
<img width="715" height="222" alt="3 - Get-ADUser -Filter Select Name, DistinguishedName" src="https://github.com/user-attachments/assets/545307ff-88a4-4a2d-82b0-a4e083a10644" />

------------------------------------------------------------------------

## 5️⃣ Servidor de Arquivos

Criação da pasta:

``` powershell
New-Item -Path "C:\Compartilhado" -ItemType Directory
```
<img width="774" height="184" alt="8 - Arquivos Compartlhados" src="https://github.com/user-attachments/assets/b5d5301c-9626-4d8b-8ee3-af21c53fcf45" />

Permissões NTFS:

``` powershell
icacls "C:\Compartilhado" /inheritance:r
icacls "C:\Compartilhado" /grant "empresa\GRP_TI:(OI)(CI)F"
icacls "C:\Compartilhado" /grant "empresa\GRP_FINANCEIRO:(OI)(CI)R"
```
<img width="484" height="239" alt="9 - Permissões dos arquivos compartilhados" src="https://github.com/user-attachments/assets/dd84768f-ecd9-4c53-91b4-be6e616304a3" />

### Troubleshooting Realizado

-   Problema de acesso por associação incorreta ao grupo.
-   Necessidade de novo logon para atualização do token de segurança.
-   Entendimento da diferença entre permissões NTFS e SMB (a mais
    restritiva prevalece).

------------------------------------------------------------------------

## 6️⃣ Integração do Cliente ao Domínio

-   Entrada no domínio `empresa.local`
-   Login com usuário de domínio
-   Teste de acesso ao servidor

Validação:

``` cmd
ping empresa.local
```
<img width="625" height="321" alt="ping empresa local" src="https://github.com/user-attachments/assets/e9e323ce-a77e-4e3a-84ca-1ca42f327053" />

------------------------------------------------------------------------

## 7️⃣ Implementação de DHCP

Instalação:

``` powershell
Install-WindowsFeature DHCP -IncludeManagementTools
```

Autorização:

``` powershell
Add-DhcpServerInDC -DnsName "SRV-DC01.empresa.local" -IpAddress 192.168.10.10
```
<img width="853" height="116" alt="6 - DHCP" src="https://github.com/user-attachments/assets/a6e0e02f-c04a-43eb-a585-b780b82c9e7b" />

Criação de escopo:

``` powershell
Add-DhcpServerv4Scope -Name "EscopoEmpresa" -StartRange 192.168.10.50 -EndRange 192.168.10.100 -SubnetMask 255.255.255.0 -State Active
```

Configuração de DNS no escopo:

``` powershell
Set-DhcpServerv4OptionValue -ScopeId 192.168.10.0 -DnsServer 192.168.10.10 -DnsDomain empresa.local
```
<img width="953" height="287" alt="7 - DHCP Scope ID" src="https://github.com/user-attachments/assets/d7092e9c-f856-4d51-87a9-fef107be0a2d" />

Resultado: - Cliente passou a receber IP automaticamente. - DNS
configurado automaticamente. - Integração transparente ao domínio.

<img width="1014" height="746" alt="11 - Acesso ao servidor" src="https://github.com/user-attachments/assets/62026797-a80c-45ba-8051-fd1b398ff403" />

------------------------------------------------------------------------

## 🎯 Resultado Final

Ambiente corporativo funcional contendo:

-   Controlador de Domínio
-   Servidor DNS
-   Servidor DHCP
-   Servidor de Arquivos
-   Estrutura organizacional por departamento
-   Controle de acesso baseado em grupo
-   Estação cliente integrada ao domínio
-   Resolução de incidentes reais de permissão

------------------------------------------------------------------------

## 🚀 Objetivo Profissional

Projeto desenvolvido com foco em atuação como estagiário ou júnior em:

-   Analise de Suporte
-   Analise de Infraestrutura
-   Técnico de TI Corporativo
-   Áreas da tecnologia correlatas

Simulando cenários reais encontrados em ambientes empresariais.
