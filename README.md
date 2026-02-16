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

------------------------------------------------------------------------

## 3️⃣ Estrutura Organizacional

Criação de OUs:

``` powershell
New-ADOrganizationalUnit -Name "Financeiro" -Path "DC=empresa,DC=local"
New-ADOrganizationalUnit -Name "TI" -Path "DC=empresa,DC=local"
```

------------------------------------------------------------------------

## 4️⃣ Grupos e Usuários

Grupos: - GRP_FINANCEIRO - GRP_TI

Usuários criados por departamento e adicionados aos respectivos grupos.

Validação:

``` powershell
Get-ADPrincipalGroupMembership joao.financeiro
```

------------------------------------------------------------------------

## 5️⃣ Servidor de Arquivos

Criação da pasta:

``` powershell
New-Item -Path "C:\Compartilhado" -ItemType Directory
```

Permissões NTFS:

``` powershell
icacls "C:\Compartilhado" /inheritance:r
icacls "C:\Compartilhado" /grant "empresa\GRP_TI:(OI)(CI)F"
icacls "C:\Compartilhado" /grant "empresa\GRP_FINANCEIRO:(OI)(CI)R"
```

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

Criação de escopo:

``` powershell
Add-DhcpServerv4Scope -Name "EscopoEmpresa" -StartRange 192.168.10.50 -EndRange 192.168.10.100 -SubnetMask 255.255.255.0 -State Active
```

Configuração de DNS no escopo:

``` powershell
Set-DhcpServerv4OptionValue -ScopeId 192.168.10.0 -DnsServer 192.168.10.10 -DnsDomain empresa.local
```

Resultado: - Cliente passou a receber IP automaticamente. - DNS
configurado automaticamente. - Integração transparente ao domínio.

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

Projeto desenvolvido com foco em atuação como:

-   Analista de Suporte
-   Analista de Infraestrutura Júnior
-   Técnico de TI Corporativo

Simulando cenários reais encontrados em ambientes empresariais.
