# Projeto de Migração do Servidor Desktop Windows 10 para o Storage NAS QNAP TS-231K

**Objetivo da migração do Desktop Windows 10 para o NAS QNAP:** desativação do Servidor/Desktop Windows 10 que é utilizado como servidor de armazenamento de arquivos principal da empresa, remoção da sua localização atual (em cima do Rack), reaproveitamento do Desktop para ser utilizado como equipamento de uso pessoal de um novo funcionário, aumento da segurança de acesso e centralização dos arquivos da empresa, backup automático dos arquivos, sincronização dos arquivos com o Cloud e Hard Disk Externo, diminuição do consumo de energia elétrica e a possibilidade de colocar o NAS QNAP dentro do Rack atual conforme imagens:

Imagem-01: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/01-servidor.jpg<br>
Imagem-02: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/02-nas-qnap.jpg

**Principais arquivos utilizados pela empresa:** Planilhas Eletrônicas desenvolvidas em Microsoft Office Excel (várias versões) e arquivos de Imagens Gráficas utilizando o CorelDRAW e Adobe Photoshop.

**Desafio da migração:** após a primeira migração feita em Janeiro/2021, a principal reclamação dos usuários da Rede Local estavá associada a velocidade de Pesquisa e Busca de Conteúdo de arquivos do Microsoft Office Excel utilizando o recurso do Windows Explorer Search (MS-WSP) com as opções habilitadas de: Opções Avançadas, Conteúdo do Arquivo e Arquivos de Sistema. Essa é a principal ferramenta de trabalho utilizada pelos funcionários, a empresa trabalha com vários arquivos de Planilhas Eletrônicas criadas por vários funcionários em diversos setores da empresa (Produção, Estoque, Vendas, Faturamento, Expedição, etc...) e organizadas em vários diretórios diferentes, esse recurso é fundamental para localizar o conteúdo dos arquivos correspondente a cada pedido de venda e o seu status atual de produção ou envio para o cliente. (Obs: a empresa não possui um Sistema de Gestão Empresarial ERP para gerenciar todas as fases de venda e produção dos seus produtos, utilizando como sistema de gestão principal as Planilhas Eletrônicas desenvolvidas individualmente por cada funcionário do seu setor ou interligando essas planilhas via Fórmulas como por exemplo: =PROCV(), =INDEX(), etc... para facilitar a integração dos setores).

**Análise da falha de Indexação dos Arquivos e Diretórios no NAS QNAP** conforme pesquisa em vários How-To, Artigos, Postagens no Fórum Oficial do QNAP ao comparar  essa falha com outros fabricantes ou tecnologias de NAS, Storage, SAMBA-4, etc... (conforme links no final desse artigo), e todos os testes executados em clientes rodando o Microsoft Windows 10 Pro (conforme tópicos abaixo das especificações técnicas, configurações e testes de desempenho) a falha de Indexação utilizando o Protocolo MS-WSP (Windows Search Protocol) e MS-WSS (Windows Search Service) está aparentemente associada ao protocolo utilizado pelo NAS QNAP do SAMBA-4 SMB que não suporta a Indexação de Arquivos em Compartilhamentos SMB/CIFS para as versões mais novas do Windows Search Protocol nos clientes Windows 10 Pro (conforme artigos e documentação da Microsoft esse recurso ficou restrito para Servidores Microsoft ou Clientes Windows mais recente, onde existe a possibilidade de criar um Indexador Local e compartilhar esse indexe com os clientes remotos).

**Observação-01:** trabalhando com a Distribuição GNU/Linux UCS Univention Core Free v4.4.x (https://www.univention.com/products/ucs/) utilizando o SAMBA-4 v4.10.x (https://wiki.samba.org/index.php/Samba_4.10_Features_added/changed) com Suporte ao MS-WSP/MS-WSS desenvolvida pela distribuição (https://pt.slideshare.net/Univention/ucs-product-roundtrip-highlights-2016-and-lookout-2017 - https://forge.univention.org/bugzilla/show_bug.cgi?id=43485) essa falha de lentidão de Busca de Conteúdo de Arquivos utilizando o Windows Explorer Search não acontece devido ao suporte de indexação dos arquivos nos compartilhamentos configurados com os Protocolos SMB/CIFS (idem para Servidores Microsoft Windows Server 2008/2012/2016 e 2019).

**Observação-02:** está sendo avaliado a utilização de um Hard Disk SSD (exemplo: SSD Kingston A400, 960GB, SATA-III, Leitura 500MB/s, Gravação 450MB/s - SA400S37/960G) para o Armazenamento dos Arquivos principais da empresa com objetivo de acelerar a busca do conteúdo dos arquivos nos compartilhados, devido a lentidão é a falta de suporte de Indexação de arquivos nos compartilhamentos, nessa documentação os valores de teste da Leitura e Escrita dos HD são baseados nos disco instalados atualmente no QNAP.

------------------------------------------------------------------------------------

## **Servidor de Arquivos: Desktop Windows 10 Pro**
	CPU: AMD A10-7860k Radeon R7 - 8GB RAM
	Hard Disk: 01 (um) Seagate Barracuda 500GB 16MB Cache 7200rpm - Sistema de Arquivos: NTFS
	Placa de Rede: Realtek PCIe GbE 10/100/1000Mbps
	Localização dos arquivos da empresa: C:\Users\Usuário\Documents\Servidor
	Compartilhamento dos arquivos da empresa: \\DesktopWin10\Users\Usuário\Documents

## **NAS Modelo: QNAP TS-231K**
	Firmware: 4.5.2.1566 (01/03/2021)
	CPU: Cortex-A15 Quad-Core 1.7Ghz - 1.0GB RAM
	Hard Disk: 02 (dois) Seagate Barracuda 2TB 256MB Cache 7200rpm
	Licença: exFAT Driver

## **Infraestrutura de Rede**
	Switch: 01 (um) TP-Link T-1600G-28TS 24 Portas RJ45 10/100/1000Mbps
	Switch PoE: 01 (um) TP-Link TL-SG1005P 05 Portas RJ45 10/100/1000Mbps
	Access Point: 02 (dois) Ubiquiti Unifi UAP-AC-LR 01 Porta RJ45 PoE 10/100/1000Mbps

Imagem-07: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/07-Topology.png

------------------------------------------------------------------------------------

# Configurações principais do NAS-QNAP TS-231K

## **01_ Armazenamento e Instantâneos**
	Armazenamento
		Conjunto de Armazenamento 1 (1.81TB - 1.31TB)
			HD-1 (Sistema) - Volume espesso (Limiar 80%)
			Sys Reservado: 85.1GB
			Volume espesso: 1.4TB
			Espaço Livre: 0,3TB
			Assinatura: 81,9%	
		Conjunto de Armazenamento 2 (1.81TB - 1.31TB)
			HD-2 (Arquivos) - Volume espesso (Limiar 80%)
			Sys Reservado: 85.1GB
			Volume espesso: 1.4TB
			Espaço Livre: 0,3TB
			Assinatura: 81,9%

Obs: Não foi configurado o recurso de RAID-1, nos testes de desempenho de Leitura e Escrita principalmente na Busca de Arquivos e Conteúdos, a velocidade ficou muito abaixo do esperado, ficando pior em relação ao Servidor atual da empresa (Desktop Windows 10) em comparação aos volumes individuais sem RAID-1.

## **02_ Chaves de Rede e Virtual**
	Interfaces
		Adapter1 - IP Fixo: 192.168.0.250/24
		Adapter2 - IP Fixo: 192.168.0.251/24
		Hostname: Servidor
		NetBIOS/NetBEUI Name: Servidor

Obs: Não foi configurado o recurso de Entrocamento de Porta, nesse cenário as duas Interfaces fazem referência ao NetBIOS do NAS (Balance-rr Round-Robin) fazendo automaticamente o Load Balanced de acesso ao NAS utilizando o Hostname, testes executados utilizando o Endereço IPv4 ou Hostname para a transferência ou leitura e escrita de arquivos foram os mesmos.
		
## **03_ Serviço de Rede e de Arquivos**
	Win/Mac/NFS
		Rede Microsoft
			Habilitar o serviço de arquivos para rede Microsoft
			Servidor independente
				Opções Avançadas
					Versão do SMB: 2.1

Obs: Foi configurado somente o protocolo SMB2.1 nas opções de Maior Versão e Menor Versão do SMB, testes feitos na versão 3.0 deixaram lentas as Pesquisas de Conteúdo e Leitura/Escrita nos compartilhamentos, nesse cenário não foi configurado o Servidor de Controlador de Domínio, trabalhando com Rede Workgroup e aplicando permissões de acesso ao compartilhamento e diretórios, utilizando os usuários e grupos locais do NAS.
				
## **04_ Privilégios**
	Pastas Compartilhadas
		Permissões avançadas
			Habilitar permissões avançadas de pasta
			Habilitar Windows ACL Support

Obs: Com essa opção configurada, possibilitará os recursos de permissões de acesso aos compartilhamentos e diretórios utilizando o Windows Explorer para alterar as Permissões NTFS (Hard Disk) e de Compartilhamento (SMB/CIFS), permitindo criar regras de ACL (Access Control List) e quebra de herança de diretórios, esse recurso será utilizando junto com o ABES (Access-Based Enumeration Service) para ocultar automáticamente os diretórios/arquivos que os Grupos (departamentos da empresa) não tem permissão de acesso (Leitura/Escrita).

------------------------------------------------------------------------------------

# Configurações de Armazenamento do NAS-QNAP TS-231K

## **01_ HD-1 (Sistema)**
	Compartilhamento: Arquivos-BKP <-- Share sincronização dos arquivos da empresa
		Suporte: Lixeira, Instantâneo (Snapshot), ACL Avançadas, ABES, Backup HBS3
	
	Compartilhamento: Backup <-- Share de backup dos arquivos da empresa
		Suporte: Lixeira, Instantâneo (Snapshot), ACL Avançadas, ABES, Backup HBS3

## **02_ HD-2 (Arquivos)**
	Compartilhamento: Arquivos <-- Share principal dos arquivos da empresa
		Suporte: Lixeira, Instantâneo (Snapshot), ACL Avançadas, ABES

Obs: Decidimos não trabalhar com RAID-1, possibilitando trabalhar com os dois Hard Disk independente mais sincronizados utilizando o software de Backup HBS3 (Hybrid Backup Sync), nesse cenário também será utilizado o software QSync Client configurado no Hard Disk de Arquivos (a pastas dos usuários /home e /homes ficará nesse hard disk).
		
------------------------------------------------------------------------------------

# Acesso aos compartilhamentos via SMB2.1 no NAS-QNAP TS-231K

## **01_ Montagem do compartilhamento utilizando o comando: Net**
	net use n: \\Servidor\Arquivos /persistent:yes

Obs: Essa opção permitirá que os arquivos desta unidade de rede tenham o seu conteúdo indexado junto com as propriedades dos arquivos, mapeamento principal de acesso aos arquivos e diretórios, mesma configuração de mapeamento utilizado atualmente na empresa.
	
## **02_ Montagem do compartilhamento utilizando a opção do Windows Explorer: Mapeamento de Unidade de Rede:**
	Windows Explorer
		Este Computador
			Mapear Unidade de Rede
				n:
				\\Servidor\Arquivos

Obs: Essa opção permitirá que os arquivos desta unidade de rede tenham o seu conteúdo indexado junto com as propriedades dos arquivos, igual ao comando: net use.
			
## **03_ Montagem do compartilhamento utilizando a opção do Windows Explorer: Adicionar um Local de Rede:**
	Windows Explorer
		Este Computador
			Adicionar um local de rede
				Escolher um local de rede personalizado
				\\Servidor\Arquivos
				Arquivos

Obs: Conforme documentação da Microsoft, essa opção acelera a indexação dos arquivos nos compartilhamentos, nesse cenário a vantagem é a facilidade de não utilizar uma Letra de Unidade de Rede, nos testes de busca de arquivos e conteúdo o tempo para localizar o conteúdo desejado foi o mesmo que da unidade de rede mapeada (itens 01 e 02).

## **04_ Montagem do compartilhamento utilizando a opção do Windows Explorer: WebDAV:**
	Windows Explorer
		Este Computador
			Adicionar um local de rede
				Escolher um local de rede personalizado
				https://Servidor:5001/Arquivos

Obs: Igual a opção de adicionar um Local de Rede, nesse cenário utilizamos o acesso ao servidor via protocolo WebDAV (Web-based Distributed Authoring and Versioning) que foi configurado nas permissões de compartilhamento da diretório Arquivos, nos testes de busca de arquivos e conteúdo o tempo para localizar o conteúdo desejado foi maior do que a unidade de rede mapeada ou quando adicionamos a unidade de rede no Windows Explorer, sua vantagem estará na utilização de Dispositivos Móveis (Smartphone, Tablets, etc...) e acesso via Navegador Remoto ou aplicativos que tenham suporte ao Protocolo WebDAV (nesse cenário foi utilizado somente como testes, sendo desativado em produção).

## **05_ Montagem do compartilhamento utilizando o comando: Mklink:**
	Prompt de Comando como Administrador
		cd \users\usuário\Documentos
		mklink /D  Arquivos \\Servidor\Arquivos

Obs: Conforme a documentação da Microsoft, essa opção acelera a indexação dos arquivos no compartilhamento, foi feito a criação do Link Simbólico de Diretório dentro da pasta Documentos (usuário de teste), foi executado a indexação e feito a busca de arquivos e conteúdo, nesse cenário o processo demorou mais tempo para localizar os arquivos igual a opção do WebDAV (item 04).

## **06_ Montagem do compartilhamento utilizando o comando: Mount:**
	mount -o anon \\Servidor\Arquivos n:

Obs: Foi habilitado o suporte ao protocolo NFS (Network File System) no Windows 10, nessa configuração tivemos incompatibilidade de caracteres no compartilhamento, velocidade de busca de arquivos e conteúdo demorou bastante, aparentemente devido a incompatibilidade de caracteres o projeto não foi mais testado e nem verificado a falha dos caracteres ou configuração, igual ao WebDAV foi utilizado somente como teste.

## **07_ Montagem do compartilhamento utilizando o comando: iSCSI Target:**

Obs: Nesse projeto não utilizamos do iSCSI Initiator/Target pois afetaria as configurações manuais em cada cliente na rede, mesmo que na maioria dos Artigos/How-To indicam utilizar o iSCSI para resolver o problema de Indexação em equipamentos de NAS, entendemos que esse cenário seria mais indicado para Servidores não para Clientes (mesmo tempo suporte para isso no Windows 10 Pro).

------------------------------------------------------------------------------------

# Transferências dos arquivos para o NAS-QNAP TS-231K

## **01_ Transferências dos arquivos do Servidor Desktop Windows 10 utilizando o comando: Robocopy**
	robocopy S:\Servidor N:\ /S /E /R:0 /W:0 /V /NP /ETA /LOG:robocopy.log
	
## **Parâmetros utilizados no Robocopy**
	/S = Copia subdiretórios. Essa opção exclui automaticamente os diretórios vazios.
	/E = Copia subdiretórios. Essa opção inclui automaticamente diretórios vazios.
	/R:0 = Especifica o número de repetições em cópias com falha. O valor padrão de n é 1 milhão (1 milhão tentativas).
	/W:0 = Especifica o tempo de espera entre as tentativas, em segundos. O valor padrão de n é 30 (tempo de espera de 30 segundos).
	/V = Produz a saída detalhada e mostra todos os arquivos ignorados.
	/NP = Especifica que o progresso da operação de cópia (o número de arquivos ou diretórios copiados até o momento) não será exibido.
	/ETA = Mostra o tempo estimado de chegada (ETA) dos arquivos copiados.
	/LOG: = Grava a saída de status no arquivo de log (substitui o arquivo de log existente).

------------------------------------------------------------------------------------

# Arquivos e Diretórios armazenados no NAS-QNAP TS-231K

## **01_ Total de Arquivos e Diretórios no Compartilhamento: Arquivos (Share principal da empresa)**
	Total (01/03/2021): 250,61GB
	Arquivos (01/03/2021): 163.092
	Diretórios (01/03/2021): 9.288

Imagem-03: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/03-Shares.png<br>
Imagem-04: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/04-FilesFolders.png

-------------------------------------------------------------------------------------

# Testes de Desempenho de Leitura/Escrita e Largura de Banda do NAS-QNAP TS-231K

## **01_ Teste de desempenho de Leitura e Escrita utilizando o software: NAS Performance Tester**
	http://www.808.dk/?code-csharp-nas-performance

Obs: Foram feitos testes de desempenho de Leitura e Escrita do NAS QNAP TS-231K utilizando o Software NAS Performance Tester em um desktop Windows 10 Pro conectado na Porta GigabitEthernet (Desktop/Switch), as velocidades ficaram na média de: Read(Leitura): 104,44MB/sec e Write(Escrita): 95,19MB/sec conforme imagem:

Imagem-05: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/05-NAS-ReadWrite.jpeg

## **02_ Teste de velocidade de Rede utilizando o software: iPerf3**
	https://iperf.fr/iperf-download.php
	https://www.qnapclub.eu/en/qpkg/180

Obs: Foram feitos testes de desempenho de Largura de Banda do NAS QNAP TS-231K utilizando o Software iPerf3 em um desktop Windows 10 Pro conectado na Porta GigabitEthernet (Desktop/Switch), as velocidades ficaram na média de: Sender(Enviado): 936Mb/s e Receiver(Recebido): 936Mb/s conforme imagem:

Imagem-06: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/06-NAS-iPerf3.jpeg

------------------------------------------------------------------------------------

# Testes de Busca de Arquivos e Conteúdos no NAS-QNAP TS-231K

## **01_ Teste de pesquisa utilizando o Windows Explorer Search**

Obs: Testamos utilizando o Windows Explorer Search com as opções avançadas de Pesquisa de Conteúdo habilitada, pesquisando somente o conteúdo do arquivo na localização principal dos arquivos da empresa onde é mais utilizado a busca dos arquivos/conteúdo (Planilhas Eletrônicas em Excel), a pesquisa demorou cerca de 04m:25s e localizou apenas alguns arquivos principais (02 arquivos de Planilhas de Excel que contia o conteúdo pesquisado), não localizando todos os arquivos e com o desempenho inferior ao servidor atual (Desktop Windows 10).

Imagem-09: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/09-WindowsSearch.jpeg

Mesmo teste executado no servidor atual da empresa demorou cerca de: 03s e localizou vários arquivos que já estão indexados no servidor e no cliente Windows 10 Pro.

Imagem-11: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/11-IndexWin10.jpeg

## **02_ Teste de pesquisa utilizando o Software Agent Ransack Free**
	https://www.mythicsoft.com/agentransack/

Obs: Testamos utilizando o Software Agent Ransack Free para localizar o conteúdo de arquivos *.XLSX com filtro de conteúdo, a pesquisa demorou cerca de 01m:14s (03m:11s mais rápido que o Windows Search) e localizou mais arquivos com o conteúdo selecionado que a pesquisa do Windows Search (04 arquivos de Planilhas de Excel que contia o conteúdo pesquisado). A desvantagem desse recurso é que ele não é nativo ou integrado no Windows Explorer.

Imagem-08: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/08-AgentRansack.jpeg


## **03_ Teste de pesquisa utilizando o Software DocFetcher Open Source**
	http://docfetcher.sourceforge.net/en/download.html

Obs: Testamos utilizando o Software DocFetcher para localizar o conteúdo de arquivos *.XLSX com filtro de conteúdo, infelizmente o aplicativo não fornece recursos de pesquisas de conteúdo, ficando fora dos testes finais e não sendo indicado para produção.

Imagem-10: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/10-DocFetcher.jpeg

------------------------------------------------------------------------------------

# Material de apoio para os testes executados na migração

## **01_ Link de referência das pesquisas sobre a velocidade do Windows Explorer Search e da Indexação do Windows em Storage NAS QNAP e outras soluções de Storage**

**Keywords google:** qnap slow smb performance, qnap slow search share windows, qnap indexing files, qnap windows search, qnap nas offline files, Mapped network drive is offline, windows search indexer samba4, qnap smb windows search index, qnap ms-wsp, freenas Speeding search, freenas indexing share

01: https://forum.qnap.com/viewtopic.php?t=159277<br>
02: https://forum.qnap.com/viewtopic.php?t=110401<br>
03: https://forum.qnap.com/viewtopic.php?t=138959<br>
04: https://forum.qnap.com/viewtopic.php?t=44092<br>
05: https://forum.qnap.com/viewtopic.php?t=25708<br>
06: https://forum.qnap.com/viewtopic.php?t=140883<br>

01: https://answers.microsoft.com/en-us/windows/forum/windows_7-files/add-nas-shares-to-index/d6a6cb41-1d66-476c-ab1e-70329dd55cc5<br>
02: https://petri.com/add-nas-based-network-locations-windows-10-fast-search<br>
03: https://www.online-tech-tips.com/windows-10/add-network-folder-to-windows-search-index/<br>
04: https://redmondmag.com/articles/2020/12/02/5-steps-to-fix-windows-indexing.aspx<br>
05: https://social.technet.microsoft.com/Forums/en-US/cef10e47-2fe1-482b-be62-ae74116a4e5f/mapped-network-drive-is-offline?forum=win10itpronetworking<br>
06: https://www.onmsft.com/how-to/how-to-use-network-file-shares-while-offline-in-windows-10<br>
07: https://www.reddit.com/r/freenas/comments/2z90u6/speeding_up_file_search_on_network_shares/<br>
08: https://www.truenas.com/community/threads/windows-libraries-search-best-solution.20604/<br>
09: https://social.technet.microsoft.com/Forums/windows/en-US/afb904c1-1c61-4aae-b6b1-5cf525b9f8de/how-do-i-get-windows-7-to-index-a-network-mapped-drive?forum=w7itpronetworking<br>
10: https://forge.univention.org/bugzilla/show_bug.cgi?id=43485<br>
11: https://pt.slideshare.net/Univention/ucs-product-roundtrip-highlights-2016-and-lookout-2017<br>
12: https://www.reddit.com/r/qnap/comments/a0x8ry/can_the_ts453be_index_files_via_windows_explorer/
