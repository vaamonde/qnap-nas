# Projeto de Migração do Servidor Desktop Windows 10 para o Storage NAS QNAP TS-231K

**Objetivo da migração do Desktop Windows 10 para o NAS QNAP:** desativação do Servidor/Desktop Windows 10 e remoção da localização atual para ser utilizado como Desktop de um novo funcionário, a utilização do NAS QNAP irá diminuir o consumo de energia elétrica e possibilitará colocar o mesmo dentro do Rack atual conforme imagens:

Imagem-01: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/01-servidor.jpg<br>
Imagem-02: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/02-nas-qnap.jpg

**Principais arquivos utilizados pela empresa:** Planilhas Eletrônicas desenvolvidas em Microsoft Excel e arquivos de Imagens Gráficas utilizando o CorelDRAW e Adobe Photoshop.

**Desafio da migração:** após a primeira migração feita em Janeiro/2021, a principal reclamação dos usuários da Rede Local estavá  associada a velocidade de Pesquisa e Busca utilizando o recurso do Windows Explorer Search com as opções habilidades de: Opções  Avançadas, Conteúdo do Arquivo e Arquivos de Sistema, essa é a principal ferramente de trabalho da empresa, devido a mesma  trabalhar com vários arquivos de Planilhas Eletrônicas desenvolvidas no Microsoft Office Excel e separadas por várias áreas da  empresa (Produção, Estoque, Vendas, Faturamento, etc...), esse recurso e fundamental para localizar o conteúdo dos arquivos  correspondente a cada pedido de venda da empresa (Obs: a mesma não possui um sistema de Gestão Empresarial para gerenciar todas as fases de produção e venda dos seus produtos).

## **Servidor de Arquivos: Desktop Windows 10 Pro**
	CPU: AMD A10-7860k Radeon R7 - 8GB RAM
	Hard Disk: 01 (um) Seagate Barracuda 500GB 16MB Cache 7200rpm - Sistema de Arquivos: NTFS
	Placa de Rede: Realtek PCIe GbE 10/100/1000Mbps
	Localização dos arquivos da empresa: C:\Users\Usuário\Documents\Servidor
	Compartilhamento dos arquivos da empresa: \\192.168.0.9\Users\Usuário\Documents

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

Obs: Não foi configurado o recurso de RAID-1, nos testes de desempenho de Leitura/Escrita e principalmente na Busca de Arquivos, a velocidade ficou muito abaixo do esperado, ficando pior em relação ao Servidor atual da empresa (Desktop Windows 10).

## **02_ Chaves de Rede e Virtual**
	Interfaces
		Adapter1 - IP Fixo: 192.168.0.250/24
		Adapter2 - IP Fixo: 192.168.0.251/24
		Hostname: Servidor
		NetBIOS/NetBEUI Name: Servidor

Obs: Não foi configurado o recurso de Entrocamento de Porta, nesse cenário as duas Interfaces fazem referência ao NetBIOS do NAS (Balance-rr Round-Robin) fazendo automaticamente o Load Balanced de acesso ao NAS utilizando o Hostname, teste executados via Endereço IPv4 e pelo Hostname para a transferência de arquivos ou leitura foram os mesmos.
		
## **03_ Serviço de Rede e de Arquivos**
	Win/Mac/NFS
		Rede Microsoft
			Habilitar o serviço de arquivos para rede Microsoft
			Servidor independente
				Opções Avançadas
					Versão do SMB: 2.1

Obs: Foi configurado somente o protocolo SMB2.1 nas opções de Maior Versão e Menor Versão do SMB, testes feitos na versão 3.0 deixou lento as Pesquisas e Leitura/Escrita nos compartilhamentos, nesse cenário não foi configurado o Servidor de Controlador de Domínio, trabalhando com Rede Workgroup e aplicando permissões de acesso ao compartilhamento e pastas utilizando usuários e grupos do NAS.
				
## **04_ Privilégios**
	Pastas Compartilhadas
		Permissões avançadas
			Habilitar permissões avançadas de pasta
			Habilitar Windows ACL Support

Obs: Com essa opção habilitada, possibilita os recursos de permissões de acesso aos compartilhamentos e pastas utilizando o Windows Explorer para aplicar as Permissões NTFS e de Compartilhamento, permitindo criar regras de ACL e quebra de herança de pastas, esse recurso será utilizando junto com o ABES (Access-Based Enumeration Service).

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

Obs: Foi optado em não trabalhar com RAID-1, possibilitando trabalhar com dois Hard Disk independente mais sincronizados utilizando o software de Backup HBS3 (Hybrid Backup Sync), nesse cenário também será utilizado o software QSync Client utilizando o Hard Disk de Arquivos (é a pastas dos usuários /home e /homes também ficará nesse hard disk).
		
------------------------------------------------------------------------------------

# Acesso aos compartilhamentos via SMB2.1 no NAS-QNAP TS-231K

## **01_ Montagem do compartilhamento utilizando o comando: Net**
	net use n: \\Servidor\Arquivos /persistent:yes

Obs: Permiti que os arquivos desta unidade tenham o conteúdo indexado junto com as propriedades do arquivo, mapeamento principal, mesmo tipo de mapeamento utilizado atualmente na empresa.
	
## **02_ Montagem do compartilhamento utilizando a opção do Windows Explorer: Mapeamento de Unidade de Rede:**
	Windows Explorer
		Este Computador
			Mapear Unidade de Rede
				n:
				\\Servidor\Arquivos

Obs: Permiti que os arquivos desta unidade tenham o conteúdo indexado junto com as propriedades do arquivo, idem do comando: net use.
			
## **03_ Montagem do compartilhamento utilizando a opção do Windows Explorer: Adicionar um Local de Rede:**
	Windows Explorer
		Este Computador
			Adicionar um local de rede
				Escolher um local de rede personalizado
				\\Servidor\Arquivos
				Arquivos

Obs: Conforme documentação da Microsoft, essa opção acelera a indexação dos arquivos nos compartilhamento, nesse cenário a facilidade será não utilizar uma Letra de Unidade, os testes de busca de arquivos demoraram o mesmo tempo que a unidade mapeada.

## **04_ Montagem do compartilhamento utilizando a opção do Windows Explorer: WebDAV:**
	Windows Explorer
		Este Computador
			Adicionar um local de rede
				Escolher um local de rede personalizado
				https://Servidor:5001/Arquivos

Obs: Igual a opção de adicionar um Local de Rede, nesse cenário utilizando o acesso ao servidor WebDAV que foi configurado nas permissões de compartilhamento da pasta, nos teste de velocidade de busca de arquivos, demorou mais para encontrar e localizar o conteúdo.

## **05_ Montagem do compartilhamento utilizando o comando: Mklink:**
	Prompt de Comando como Administrador
		cd \users\usuário\Documentos
		mklink /D  Arquivos \\Servidor\Arquivos

Obs: Conforme a documentação da Microsoft, essa opção acelera a indexação dos arquivos nos compartilhamento, foi feito a criação do Link de Diretório na pasta: Documentos, executado a indexação e feito a busca de arquivos e conteúdo, nesse cenário o processo demorou mais tempo para localizar os arquivos.

## **06_ Montagem do compartilhamento utilizando o comando: Mount:**
	mount -o anon \\Servidor\Arquivos n:

Obs: Habilitado o suporte ao protocolo NFS no Windows 10, nessa configuração tivemos incompatibilidade de caracteres no compartilhamento, velocidade de busca de arquivos demorou bastante devido a incompatibilidade de caracteres (projeto não foi mais testado e nem verificado a falha dos caracteres).
		
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
	Arquivos (01/03/2021): 163092
	Diretórios (01/03/2021): 9288

Imagem-03: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/03-Shares.png<br>
Imagem-04: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/04-FilesFolders.png

-------------------------------------------------------------------------------------

# Testes de Desempenho de Leitura/Escrita e Largura de Banda do NAS-QNAP TS-231K

## **01_ Teste de desempenho de Leitura e Escrita utilizando o software: NAS Performance Tester**
	http://www.808.dk/?code-csharp-nas-performance

Obs: Foi feito os testes de desempenho de Leitura e Escrita do NAS QNAP utilizando o Software NAS Performance Tester em um desktop Windows 10 Pro conectado na Porta GigabitEthernet, as velocidades ficaram na média de: Read: 104,44MB/sec e Write: 95,19MB/sec conforme imagem:

Imagem-05: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/05-NAS-ReadWrite.jpeg

## **02_ Teste de velocidade de Rede utilizando o software: iPerf3**
	https://iperf.fr/iperf-download.php
	https://www.qnapclub.eu/en

Obs: Foi feito os testes de desempenho de Largura de Banda do NAS QNAP utilizando o Software iPerf3 em um desktop Windows 10 Pro conectado na Porta GigabitEthernet, as velocidades ficaram na média de: Sender: 936Mb/s e Receiver: 936Mb/s conforme imagem:

Imagem-06: https://github.com/vaamonde/qnap-nas/blob/main/projeto-01/imagens/06-NAS-iPerf3.jpeg

------------------------------------------------------------------------------------

# Testes de Busca de Arquivos e Conteúdos no NAS-QNAP TS-231K

## **01_ Teste de pesquisa utilizando o Windows Explorer Search**

Obs: Teste feito utilizando o Windows Explorer Search com as opções avançadas de Pesquisa de Conteúdo habilitada, pesquisando somente o conteúdo do arquivo, a pesquisa demorou cerca de 04m:25s e localizou apenas os arquivos principais (02 arquivos de Planilhas de Excel que contia o conteúdo pesquisado), não localizando todos os arquivos e com o desempenho inferior ao servidor atual.

Imagem-08: 

## **02_ Teste de pesquisa utilizando o Software Agent Ransack Free**
	https://www.mythicsoft.com/agentransack/

Obs: Teste feito utilizando o Software Agent Ransack Free para localizar o conteúdo de arquivos *.XLSX com filtro de conteúdo, a pesquisa demorou cerca de 01m:14s e localizou mais arquivos com o conteúdo selecionado que a pesquisa do Windows Search (04 arquivos de Planilhas de Excel que contia o conteúdo pesquisado).

Imagem-09:

------------------------------------------------------------------------------------

# Material de apoio para os testes executados na migração

## **01_ Link de referência das pesquisas sobre a velocidade do Windows Explorer Search e da Indexação do Windows em Storage NAS QNAP e outras soluções de Storage**

**Keywords google:** qnap slow smb performance, qnap slow search share windows, qnap indexing files, qnap windows search, qnap nas offline files, Mapped network drive is offline, windows search indexer samba4, qnap smb windows search index, qnap ms-wsp, 

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
