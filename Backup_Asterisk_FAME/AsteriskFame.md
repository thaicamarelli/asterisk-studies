## Infraestrutura Telefônica

Aparelhos telefônicos:

* Celulares, telefones, hardphones 
* Central de Comutação (CCC): Conjunto de equipamentos que controla as estações rádio base (ERB) e permite a comunicação com outras CCC -> Assim conseguimos ligar para outras cidades e bairros. Interliga centrais menores. Ex: Minha empresa se comunica com a central através da operadora e ela repassa para o restante do mundo 
* Concentrador remoto: Recebe o sinal de vários aparelhos telefônicos para ai então comunicar com a central telefônica do bairro  
Meios físicos de transmissão: Interligam centrais locais, dependem da estrutura na região para se comunicar com outras centrais.

### VoIP
Combinar redes de voz e dados para reduzir custos ( porém depende da demanda da empresa, pode ser que não seja a alternativa mais barata em alguns casos)

### Voz sobre IP 
Fluxo de áudio através do TCP/IP
Entrega por melhor esforço (UDP) -> O que pode ocasionar o picote se a rede não estiver bem estruturada
Bem recomendado com QoS -> é uma tecnologia presente em roteadores para garantir ao usuário maior controle sobre sua rede Wi-Fi. É possível determinar quais dispositivos e serviços terão maior prioridade de conexão 

Redução de custo quanto infra -> Se eu tiver um softphone na máquina consigo contratar um provedor e começar a fazer ligações 

**Fatores que influenciam:**
Jitter -> Variação de atraso entre pacotes 
Perda de pacotes
Sequência de pacotes -> Estes precisam ser recebidos na mesma ordem que foram enviados 
Latência -> Qualidade menor quando é inferior a 200ms, entre 200ms e 400ms é aceitável 

### Protocolos:

VoIP funciona utilizando diversos protocolos envolvendo várias camadas do modelo OSI:

**Apresentação -> CODECS
Sessão -> Encriptação SIP/SDP /H323
Transporte -> RTP/UDP
Rede -> IP
Ligação de dados ->  Frame Relay/ ATM
Física -> Instalações**

### SIP:
Autenticação e sinalização pra transporte de voz e vídeo 
Negociação de parâmetros entre pares 
Protocolo aberto 
Segundo mais utilizado no mundo 
maior compatibilidade entre equipamentos 

### TCP ou UDP 
Utiliza RTP para transporte de audio 
Sem criptografia 
Não recomendado para uso com NAT
Porta 5060 padrão 

PABX - Central local de telefonia - telefones conectados
Dados em tempo real - audio - voz sobre IP 
Fotos e videos também podem trafegar 
Baixo custo - portabilidade 
Usam a internet TCP/IP

VoIP - clientes (extensões/ramal/linha)

Servidor SIP = PABX no caso do ZAP Sipewise 

SIP - Protocolo de iniciação de sessão, gerenciar e encerrar 
Chamada sessão SIP 
Faz uso do SDP - descrever a sessão e RTP transfere a voz  video pela rede IP, unicast e multicast 


SIP Proxy - sabe os IPs para direcionamento das chamadas 

IAX:
Protocolo para comunicação entre sistemas asterisk -> Quando os sistemas estão sobre NAT, quando temos firewall, ramais externos 
Porta 4569 UDP
Faz tudo usando a porta 4569 -> sinalização autenticação e transporte 
Sem criptografia 

## CODECS:

Abreviação de codificador e decodificador, converte o sinal analógico em sua forma digital para ser transmitido pela rede depois faz o inverso para que a mensagem possa ser ouvida, existem muitos tipo de disponíveis, alguns gratuitos e outros pagos.

A negociação de qual CODEC usar é decidida pelo gateway e aparelhos, feita em cima do SDP

Alguns populares:

G.723.1 - permite chamadas através de links de modem de 28,8 e 33Kbits/s usados nas conexões de escada, possui duas variantes, ambas operam em quadros de áudio de 30 ms

B2BUA - back-to-back, é um agente de usuário em aplicativos SIP. É um tipo de UA SIP que recebe uma solicitação SIP, reformula a solicitação e a envia como uma nova. Diferente de um server proxy, ele mantém o estado do diálogo e participa de todas as solicitações enviadas nos diálogos que recebeu.Quebra a natureza de ponta a ponta do SIP.
Ele opera entre dois terminais de uma chamada telefônica e divide o canal de comunicações em duas pernas - é uma junção de UAC e UAS, participa de todas as sinalizações SIP, entre as duas extremidades da chamada.

Na etapa de chamada de origem o B2BUA atua como servidor (UAS)e processa solicitação como um cliente do agente (UAC) para o destino.

Funções: gerenciamento de chamadas (cobrança, desconexão automática de chamadas, transferência de chamadas), interoperação de rede( talvez com adaptação de protocolos), ocultar internals de rede (endereços privados,topologia de rede, etc.), frequentemente os B2BUA são implementados em gateways para interligar os fluxos de mídia para controle total da sessão.

Exemplos: Muitos sistemas telefônicos corporativos PBX incorporam a lógica B2BUA 
Alguns firewalls também incorporam com a funcionalidade ALG que permite que um firewall autorize o tráfego de mídia e SIP, mantendo um alto nível de segurança.Outro tipo comum de B2BUA é conhecido como um controlador de Borda de Sessão (SBC)

1PCC e 3PCC  - First Party Call Control (UAC controlando a ligação) e Third Party Call Control ( software Agent Desktop que controla toda parte de interação das chamadas, usada dentro de call center)

Digitaliza e compacta áudio
Codifica/ Descodifica - Comprime/ Descomprime 
Quanto maior a compressão maior uso de CPU

G711A - ALAW

Padrão mundial, menos nos EUA
Usa 64Kbps (sem cabeçalho) UP+DOWN (128Kbps) - por ramal/ ligação 
PCMA

G711 - ULAW
Padrão nos EUA
PCMU

G729A
Menos largura de banda 
Maior compressão - usa mais CPU
8Kbps (sem cabeçalho) UP+DOWN (16Kbps) - por ramal
Utilizado em links banda larga
Necessita de licença para uso em alguns softphones 
Não vem por padrão no asterisk
Qualidade razoável


GSM
Global System for Mobile communications
13Kbps( sem cabeçalho) UP+DOWN (26Kbps) - por ramal
Qualidade inferior ao G729A
Utiliza menos processamento

ILBC
Internet Low Britate Codec
Baixo uso de banda e boa qualidade
Precisa de muito processamento para compressão
Baixa compatibilidade com equipamentos 
15Kbps(sem cabeçalho) UP+DOWN 30Kbps - por ramal 

SPEEX
Codificação variável 
2Kbps até 22Kbps - quanto maior a taxa de bites melhor a qualidade 
Configurável de acordo com a largura de banda 

MOS( mean Opinion Score) -> Tabela do melhor para o pior CODEC, notas e uso de banda, quaidade do som

Garantias de comunicação: 

QoS - > Qualidade do serviço -> entrega em até 300ms -> largura de banda
DiffServ -> sinalização e tratamento dos pacotes -> priorização do tráfego nos roteadores
MPLS -> Priorização de tráfego na nuvem -> ex.: várias filias se comunicando com a matriz por um link 

Tecnologias para uso de ramais em centrais Asterisk

### FXS: De ramal pra central

ATAS FXS -> Dispositivos configuráveis para efetuar chamadas com ramal asterisk
Fabricantes: Grandstream,AudioCodes, D-Link, LinkSys
Convertem sinal analógico pra digital 
Possui 1 ou duas portas 
Comunicação via SIP
Interface de configuração Web
Após salvar as conf reiniciar para validar

Gateways FXS -> Dispositivos configuráveis para efetuar chamadas com ramal asterisk
Fabricantes: Grandstream,AudioCodes, D-Link, LinkSys e Khomp
Convertem sinal analógico pra digital 
Tem de 4, 8, 16 ou 24 portas
Comunicação via SIP
Interface de configuração Web
Após salvar as conf reiniciar para validar

Telefone IP -> Podem ter 4 linhas(4 ramais configurados)
Fabricantes: Grandstream, Yealink, Intelbras, LinkSys, Cisco, Khomp
Autenticam 1 ou mais ramais de acordo com a quantidade de linhas 
Comunicação via SIP
Utilizado por telefonistas para controle de ligações
Após salvar as conf reiniciar para validar

Softphone -> Podem ser instalados na maquina ou celular
Fabricantes: Conter-Path, Securax Ltd,3CX
Telefones virtuais
Tem pra todos os sistemas
Comunicação via SIP
Não consigo usar vários ramais ao mesmo tempo, mas posso deixar cadastrado e alterar

Channelbank -> 
Fabricantes: Digivoice, Aligera.Cianet
Possuem até 48 portas 
Utiliza protocolo de camada 2 TDMoE -> configuração via endereço MAC
Necessita de placa de rede exclusiva pois o índice de tráfego e conexões é muito alta 
Usa driver DAHDI no asterisk
Qualquer modificação precisa reiniciar o módulo DAHDI e reiniciar o asterisk 
Alguns suportam cascateamento 

### FXO: De central pra fora 

Provedores de serviço de telefonia: Tecnologias em comunicação de centrais Asterisk com as operadoras (PSTN)
Os códigos são muito importante pois pode acabar com os créditos ou vir um gasto maior quando utilizados sem o código na frente configurados no dialplan por exemplo

E1(Linha analógica): Operadoras
OI - 031
Embratel - 021
Intelig - 041
VIVO - 015
Placas E1 para comunicação 
Gateways E1 -> Usando quando o servidor não permite adição de placa de rede 
Especificações: Até 30 canais, discagem direta entre ramais 
Necessita de CALLERID para completar chamada
A operadora fornece um equipamento com entrada coaxial que vai para o balum e do balum vai para sua placa E1 
FXO(Linhas analógica)
Também usa placas ou gateways 
1 linha por canal 
conexão analógica 
Usadas em residências 
Não necessita de CALLERID, não possui faixa DDR somente um número
GSM(chip de celular)
OI - 31
VIVO - 15
CLARO - 21
TIM - 41
NEXTEL - Radio
Modens ou placas com antenas
Mesmas características do FXO 
usam chips de celular para comunicação com operadora
VOIP(internet):
Vono
Gvox
Telfree
Cordia
Tribo
Utiliza a internet como meio de transporte
Precisa de largura de banda no link 
Não usar a mesma internet para VOIP e o resto da empresa separar 
Números de canais ilimitados 

### Componentes de rede: 
Um único UA pode funcionar como ambos 

User Agent Client ( UAC) :
Gera solicitações e envia para o servidor 
Pode ser um sofphone - software 
Hardphone - equipamento 

User Agent Server ( UAS):
Recebe solicitações, processa e gera respostas
Servidores:
Proxy - mais comum, usado quando a solicitação é feita pelo cliente, encaminha pra outro servidor ou para o destinatário ( parecido com roteador)
Registrar ( registro ) - aceita solicitações de registro e auxilia os usuários para se autenticar na rede, armazena em um banco de dados, sem um servidor de registro os clientes são obrigados a saber o endereço IP de todos os agentes que querem se comunicar


Location - Fornece locais de um chamador, apenas um servidor de proxy o de redirecionamento podem pedir informações deste 



Redirect - recebe solicitações e procura o destinatário no banco de dados criado pelo servidor de registro, responde com 3xx


URI SIP - Edereço ( semelhante a um e-mail ) permite que uma pessoa ligue para outra

5060 - porta padrão do SIP 


## Arquitetura SIP:

Mensagens:

Invite - requisição 
200 ok - resposta 

Fluxo de chamada:

Invite - 100 trying
invite - 180 ringing


INVITE:
Tentar iniciar sessão de um usuário 


BYE:
Encerra uma sessão já estabelecida - é enviado ponta a ponta ignorando server proxy



REGISTER: -- REVER
Faz o registro do usuário, vai o UAC para um server register 




CANCEL:
Termina uma sessão ainda não estabelecida - pode ser enviada pelo UAC ou UAS
Ex.: Telefone no gancho antes de alguém atender 




ACK: - REVER 
Reconhece uma informação passada, respostas finais a um método invite 



OPTIONS: 
Consulta um UA, descobre sua disponibilidade atual 
Proxy nunca gera essa solicitação 

Método de extensão: 

SUBSCRIBE: 
Cria uma inscrição entre o cliente que deseja informações de um serviço e o servidor 
Ex.: UAC se inscreve para um serviço de correio de voz que acende o led do aparelho informando sobre novas mensagens



NOTIFY: 
Geralmente aparece quando já existe um subscribe 
Informa aos assinantes (clientes) sobre o estado da assinatura 
Ex.: Li todas as informações do correio de voz, ele envia para desligar o led do aparelho, ou ao contrário, chegou uma nova mensagem ele indica para acender o led  



PUBLISH:
Não é enviada dentro de um diálogo 
Usado por um usuário para informar o estado do evento para um servidor 
útil quando há várias fontes de informações ao mesmo tempo



REFER:
Usado para transferência de ligações 



INFO:
Usado de um UA para outro UA, quando já estabelecida a conexão, feito ponta a ponta 
Transmite info de controle 
Sinalização de chamadas 
DTMF - Tons de multifrequência 
Existem muitas aplicações que usam isso ex.: digitação de CPF numa URA

UPDATE: 
Modificar as opções de uma sessão se ainda não estabelecida 



MESSAGE: 
Usada para enviar uma mensagens instantânea com SIP 
Dentro ou fora do dialogo 



PRACK:
Retornar mensagens provisórias do tipo 100 





RESPOSTAS SIP

Sip contém 6 tipos de reposta indo do 100 ao 600

100 ao 500 - emprestadas do HTTP e 600 novo no SIP 

100 = Respostas de informações temporárias
200 = Resposta de confirmação 
300 = Respostas de redirecionamento 
400 = Erros de Cliente 
500 = Erros do Servidor 
600 = Erros Globais 

100 
Indicam o progresso da chamada, normalmente são de ponta a ponta exceto 100 trying, principal objetivo é interromper os invites 

100 trying - caso especial - sempre ponta a ponta 
180 ringing - invite recebido e o fone está tocando 
181 call is being forwarded - Chama está sendo encaminhada ( é enviada do server)
182 call queued - O destino está temporariamente indisponível 
183 session progress - Envia informações extras em uma chamada que ainda está sendo iniciada 
199 Early Dialog Terminated - enviado pelo server indicando que um diálogo inicial foi encerrado 

200
Usada para aceitar um invite ou indica um recebimento bem sucedido 

200 OK - confirma o invite, após o 180 ou bye etc
202 ACCEPTED - indicia que um pedido foi aceito e está em processamento, geralmente usada em transferências, após o REFER

300
Redirecionamento ( geralmente enviadas por servidores de redirecionamento em resposta ao INVITE)





400
Solicitação não pode ser atendida por algum erro do lado do cliente, geralmente enviadas pelo servidor 



400 Bad Request - falta de campos de cabeçalho como TO, FROM ou CALL-ID
401 Unauthorized - A solicitação precisa executar a autenticação ex.: REGISTER - 401 




500
Falhas no servidor 





600

Essa classe indica que o servidor saber que a solicitação vai falhar onde quer que esteja






CABEÇALHO SIP

Componente que transmite informações da mensagem 
Mesma regra do cabeçalho HTTP



Max Forwards: 70 - tamanho único, saltos permitidos para cada mensagem




SDP = Session Description protocol
Descreve sessões multimídia para o propósito de iniciação de sessão 
Define os parâmetros para troca de midia entre os teminais UA
Ajuda o SIP a estabelecer a conexão multimidia 



Composto por tipo e valor 
Transmite propósito da sessão, a mídia, protocolos, formato de CODEC, tempo e informação 


Os campos de v ao t são obrigatórios

v = número da versão do SDP 
v= 0 sempre 0

o = origem 
o = <nome de usuario> <session-id> <rede> <versao> <endereço>
o = thaina 2634 2634 IN IP4 192.168.0.130

s = nome da sessão, possui qualquer número diferente de 0 
s = Session SDP

c= conexão 
c = IN IP4 192.168.0.130
<tipo de rede> definido como IN (internet)
<tipo de endereço> definido como IP4 ou IP6
<endereço de conexão> IP ou Host que enviará os pacotes de mídia 

t= tempo/hora 
t = 0 0 

m= midia, tipo de midia, lista de formato de transporte e parâmetros
= audio,video,text,application,message,image OU control
m = <media> <port> <proto> <fmt>
m = audio 49171 RTP/AVP 0 

a= atributos, estende o SDP para fornecer mais informações sobre mídia, alinha pode aparecer mais de uma vez
a = rtpmap: 0 PCMU/8000
b = largura de banda 
= largura de banda necessária para a

e= email
= endereço de email do host 

p= telefone 
= número de telefone 

u= URI
u= contém um indicador de URI

Modelo oferta e resposta

INVITE -> SDP Offer (ofertou o codec a para um codec b)
<- 180 ringing
<- 200 OK - SDP Answer
Nisso ele está fornecendo quais os codecs serão utilizados para estabelecer a conexão 
-> ACK
<- RTP (media)

Para resposta (answer) deve ter o mesmo número de m= linhas na mesma ordem 
Fluxos individuais podem ser recusados quando a porta estiver configurada como 0

Chamada em espera - Call On Hold

Quando é colocada temporariamente em espera 
Como? 
É enviado um INVITE com um SDP idêntico ao primeiro porém com o campo a= sendonly
Para retomar é enviado um novo INVITE com o a=sendrecv

Roteamento e PSTN
Processo onde durante uma chamada o IP do UAC é alterado já com uma sessão estabelecida. O que ocorre quando é um dispositivo móvel



Bifurcação (Forking)

Única chamada pode tocar em vários terminais ao mesmo tempo
Ex.: hadphone e no aparelho celular ou sofphone

Paralela: 
UAC manda um INVITE para o proxy e o mesmo manda para o UAC 2 e UAC 3
Ambos retornam com o 180 ringing
e quem atender primeiro o proxy retornar 200 ok 
UAC 1 manda ACK e é estabelecida a conexão RTP ente o 1 e o que atendeu primeiro 
assim o proxy já retornar com CANCEL para o UAC que não atendeu  

Sequencial:
UAC manda um INVITE para o proxy o mesmo manda para o UAC2 e este volta com 486 busy here e se estiver bifurcação ativa no UAC3 
o proxy volta com um 181 call being forward para o UAC 1 e o proxy manda um novo INVITE para o UAC 3 e assim segue o processo, 180 ringing, 200 ok, ACK direto do UAC 1 para UAC 3 e o RTP

TAG e ID

Os IDs ajudam os proxies  a se corresponder em ligações bifurcadas
Sem o ID o proxy não entende a resposta 
O ID está presente no cabeçalho 

Tags são usadas pelo UAC para distinguir respostas dos UAS
O UAS não sabe se a solicitação foi bifurcada ou não, por isso é necessário adicionar tag 

Correio de Voz ( Voicemail)

Aplicação para telefone ex.: URA




Proxies

Servidor stateless: encaminha a mensagem que recebe e não armazena nenhuma informação  = transação rápida 


Servidor stateful: monitora e controla todas as solicitações e respostas recebidas. Armazena informações, se necessário. Pode retransmitir o pedido se não receber resposta = transação não é tão rápida, pode bifurcar

Cabeçalho Via e Record-route

Record-route: O cabeçalho é inserido em solicitações de proxies que devem estar no caminho de solicitações subsequentes para o mesmo Call-ID de chamada
É usado pelo UA para rotear solicitações 

PSTN ( Public Switched Telephone Network)
Rede telefônica pública comutada (infra mundial) interconectadas por voz, tanto comerciais como governamentais,ex.: quando você faz uma ligação para outro estado ou para fora do Brasil. 
Na maioria dos locais hoje ele é quase totalmente digital, mas o link final porém do switch ao usuário final ainda é usado circuito analógico, USA já existe fibra.



Hoje são usados trunks para saída 


Integração entre SIP e PSTN - sistema legacy com um sistema que usa tecnologia IP

São redes que falam idiomas diferentes, precisa de um tradutor(gateway)




PSTN = +55 11 991234567

SIP = URI - sip:Nina@empresa-a.com.br



Asterisk
O Asterisk é um software de código aberto e gratuito para a criação de aplicativos de comunicação VoIP, pode funcionar como PBX IP, Gateway VoIP, servidor de conferência e outras soluções personalizadas, pode usar como linha telefonica analogica e telefones  analogicos com adaptadores para coverter de analogico para digital e vice-versa

FreePBX - interface gráfica de código aberto que gerencia o asterisk, para quem está começando facilita a configuração 

Extensões: Uma extensão se refere a uma linha interna conectada a um PABX VoIP ou não  que não possui uma linha externa separada
Permite que empresas compartilhem algumas linhas externas entre muitos funcionários  
pois nem todo mundo fará chamadas externas ao mesmo tempo
Permite que usuários do mesmo escritório liguem um para o outro discando ramais de poucos dígitos

Ex.: SP =5xxx AM =6xxx


Funciona como um PBX, não só usa VoIP

## NAT

Geralmente usado por firewalls e roteadores para permitir que vários dispositivos em uma LAN com endereços IP privados compartilhem um único endereço IP público

Um endereço IP privado é o endereço que só pode ser endereçado na LAN mas não na internet. Para que este dispositivo se comunique com dispositivos na internet é necessário uma tradução para o IP público.

## VoIP e NAT

Os protocolos VoIP convencionais são projetados de modo que cria um problema no tráfego de VoIP que passa pelo NAT, pois lidam apenas com a sinalização de uma conexão. O tráfego de áudio é tratado por outro protocolo e a porta na qual o tráfego de áudio é enviado é aleatória, o áudio funciona somente de forma unidirecional, o NAT transversal é um grande problema para a implantação do VoIP.

Sem tradução do RTP, telefone toca mas sem audio



## Implementações do SIP

CHAN_PJSIP

Biblioteca de comunicação de código aberto, gratuita que implementa protocolos: SIP,SDP e RTP
Combina com o protocolo SIP com a estrutura de multimídia e a funcionalidade do NAT, sendo assim adequada para qualquer tipo de sistema , desde desktops a aparelhos móveis
Fornece o necessário para criar um aplicativo de comunicação multimídia em tempo real.Sendo: sinalização, recursos de mídia e passagem NAT

Comparação entre PJSIP e SIP:

## Estrutura Asterisk:

/etc/asterisk - > principal, arquivos de conf

/var/lib/asterisk/agi-bin -> arquivos AGI(scripts - programas) executados pelo asterisk, posso fazer em python, shell script,php

/var/lib/asterisk/moh -> arquivos de music on hold

/var/spool/asterisk/monitor -> padrao para os arquivos de gravação de chamada

/var/log/asterisk -> logs

/var/lib/asterisk -> Onde fica o banco padrão do asterisk AstDB ( banco proprio do asterisk não é o CDR)


### Instalação:

CDR -> tabela que guarda as ligações e é la que ficam os logs das ligações e relatórios

Recomendado criar um backup dos arquivos em /etc/asterisk 
Ex.: tar -cvf BackupFullAsterisk.tar * comapcta todos os arquivos dentor da pasta atual

cdr.conf

contexto:

[mysql] // contexto
usegmtime=yes // gerar unique id
loguniqueid=yes 
loguserfield=yes //log do usuario
accountlogs=yes //log que gera no var log asterisk

cdr_mysql.conf

[global]
hostname=127.0.0.1 //IP da maquina
dbname=cdr // nome do banco 
table=cdr // nome da tabela 
password=asterisk // senha
user=cdr_user // usuario 
port=3306 // porta do banco 
sock=/var/run/mysqld/mysqld.sock
timezone=UTC

asterisk.conf -> configurações da CLI

habilitar o verbose descomentando, recomendado 3 

### COMANDOS BÁSICOS:

cdr show status -> ver status do banco

sip show peers -> mostra os ramais disponíveis criados 

sip show registry -> mostra troncos SIP

sip show users - mostra informações do usuário

sip show channels -> mostra as variáveis de canal do SIP

sip show channelstats -> traz informações até de jitter 

sip show inuse -> mostra tudo que existe do tipo sip

Posso usar os mesmos comandos com IAX2

core restart now -> restarta o asterisk 

reload -> reinicia o asterisk 

core restart when convenient

core show version -> mostra versão do asterisk

queue show -> mostra as filas de atendimento

core show translation -> mostra os codecs usados 

core show application Dial -> mostra parametros que podem ser colocados dentro de uma variavel global para executar tarefas especificas ex.: 

OPD = Tkrdn (a variavel OPD vai tertodos os parametros das respectivas letras)

dialplan show globals -> mostra as variaveis globais definidas

Criação de ramal SIP:

[1000] 
type=friend
secret=lDUQcqTgO6A 
host=dynamic
qualify=yes

Lembrar de liberar no iptables o acesso a portas 
exemplo de regra aberta:

Regras liberando registro SIP e trafego de voz:
-A INPUT -p udp -m udp --dport 10000:20000 -j ACCEPT -A INPUT -p udp -m udp --dport 5060 -j ACCEPT

É padrão do asterisk dentro de rtp.conf liberar as portas de 10000 a 20000 para uso

Em extensions.conf 

[general]
static = yes writeprotect = no autofallthrough = yes
clearglobalvars = no priorityjumping = no

para incluir contextos ou funções que estão no mesmo arquivo basta colocar include => [nome do contexto]

Se estiver em outro arquivo usar 
#include 

Os audios do asterisk ficam em /var/lib/asterisk/sounds

é importante colocar a languague dentro de sip.conf em pt_BR caso deseje ser em português 

## Lógica do asterisk

Dentro de extension.conf - dialplan

### [general]
```
[general]
static = yes 
writeprotect = yes 

Servem para validar as alterações feitas no arquivo extensions.conf somente após executar o dialplan reload.

Não é necessário restartar o asterisk
```

```
autofallthrough =yes 

Quando yes se a extensão não tiver nada para fazer o asterisk termina a execução como busy,congestion ou hangup, isso evita ligações presas
```

```
clearglobalvars = no

Caso yes as variaveies globais serão limpas a cada reload
```
### [globals]

Ficam as variaveis globais do asterisk 

```
[globals]
```

### Dialplans 

Blocos de discagem feito em blocos 

```
[nome-do-contexto]
exten => 1234,1,Noop(Printa na tela)
exten => 1234,n,Hangup()

Contextos -> Agrupam as regras de discagem 
Extensões -> Sequencia de caracteres recebidas pelo Asterisk 
Prioridade -> Ordem de interpretação 
Aplicação -> Ação ou comando a ser executado
```

### Máscaras de discagem

X -> dígito de 0 a 9

Z -> dígito de 1 a 9

N -> dígito de 2 a 9

[145-9] -> faixas de dígitos, intervalos ex.: aceita números que começam com 1 ou 4 ou de 5 até 9

. -> usado para qualquer numero 

Sempre usar o _ antes de números que contenham as máscaras 
Pode ser usado em qualquer extensão somente para padrão ex.:

exten => _[0-9]XXXXXXX,1,Noope(xxxx) ou

exten => _22465509,n,Hangup()

### Vaiáveis no asterisk

Para criar uma variável usamos a função SET 

exten => 2001,1,SET(RAMAL=2001)

### Variáveis de canal 

São as variaveis que vem em cada ligação ex.:

${CALLERID(num)} -> numero de origem que fez a chamada 
${CALLERID(name)} -> nome id da chamada
${CONTEXT} -> contexto atual
${CHANNEL} -> nome do canal atual
${DIALSTATUS} -> status da chamada
${HANGUPCAUSE} -> causa do termino da chamada 

Função DumpChan() -> Mostra todas as informações de variaveis e outras info da chamada ex.:

exten => 123,1,DumpChan()

Rancar dígitos de uma variavel ex.:

VAR=123456

${VAR:1} -> fica 23456 (quero retirar a partir do primeiro numero 1 digito)
${VAR:0:1} -> fica 1 (pega somente o primeiro digito)
${VAR:2:4} -> fica 3456 ( pega a apartir do 2 e mostra os proximos 4)
${VAR:2} -> fica 3456 ( tira os 2 primeiros dois digitos )

### Macros: 

Rotinas usadas mais de uma vez no dialplan (bloco - contexto)

Sempre usam a extensão s e não não o numero da extensão como o número de telefone ou ramal pois este vai vir de outro contexto

Assim que executada ela vai fazer o que está na macro e em seguida voltar a seguir as instruções do contexto, não pula para um novo contexto como o Goto ou GotoIf

ex.:

```
[nome do contexto macro]

exten => s,1,Answer()
exten => s,n,Noop(${CALLERID(num)})
exten => s,n,Playback(/caminho do audio) 
```

Diferente do GOTO que troca o contexto, joga a ligação para outro bloco e não retorna para o contexto original 

ex.: 

[contexto]

exten => 12345,1,Noop(redirecionamento)
exten => 12345,n,Goto(contexto,s,1)


### Planos de discagem

Oganizar e definir permissões para efetuar chamadas utilizando rotas de saída

Cada ramal possui somente 1 plano de discagem e este vai ter as rotas permitidas para completar chamadas

### Tecnologias de Voz e Provedor VoIP 

O VoIP traz a redução de custo operacional devido o uso de uma única rede para transportar dados e voz, felixibilidade pois facilita tarefas e provê serviços não suportados pelo sistema de telefonia 

Para uso do VoIP é necessário a existência de uma rede de telecomunicações móvel ou fixa, que de suporte a esse conjunto de tecnologias.

Tem algum parâmetros que precisamos alterar quando temos um firewall ou outro estrutura para sair para internet 

Para receber e efetuar chamadas para fora é preciso contratar um provedor e fazer o entrocamento com o mesmo 

O provedor VoIp ira fornecer um IP dele de registro um login e uma senha para autenticar

```
Registrar tronco usando SIP


[general]
bindport=5060
rtptimeout=60
rtpholdtimeout=300
rtpkeepalive=1
maxexpirey=1800
defaultexpirey=60
rtcachefriends=yes
canreinvite=no
nat=no
disallow=all
allow=g729

register => LoginProvedor:Senha@IP

[contexto] ; ex.
username=LOGIN
secret=SENHA
type=friend
host=SIPHOST
canreinvite=no
nat=yes
insecure=invite,port
dtmfmode=rfc2833
qualify=yes
disallow=all
allow=g729
allow=ulaw
```

Configuração para o dialplan

```
[general]
static=yes
writeprotect=no
clearglobalvars=yes

[CONTEXTO DE ENTRADA]
exten => _LOGIN,1,NoOP(Origem – ${CALLERID(num)} Destino – ${EXTEN:-10})
exten => _LOGIN,n,Dial(SIP/ENTREGADONUMERO,45,Ttr) ;alterar o ENTREGADONUMERO para o local de entrega.
exten => _LOGIN,n,Hangup
```

### Gravar ligações 

As gravações são armazenadas dentro de /var/spool/asterisk/monitor 

podemos criar uma macro para gravar as ligações, um exemplo:

```
[macro-record]
exten => s,1,DumpChan()
exten => s,n,NoOP(${CALLERID(num)})
exten => s,n,Set(ARG1=${CALLERID(num)})
exten => s,n,Set(CALLFILENAME=CHANNEL-${ARG1}-${STRFTIME(${EPOCH},,%Y%m%d-%H%M%S)}-${UNIQUEID})
exten => s,n,Set(TIME=${STRFTIME(${EPOCH},,%Y%m%d)})
exten => s,n,MixMonitor(/var/spool/asterisk/monitor/${TIME}/${CALLFILENAME}${TRANSFER}.wav49,${BRIDGE}a)
exten => s,n,Set(AUDIOHOOK_INHERIT(MixMonitor)=yes)
exten => s,n,Set(CDR(monitor)=${CALLFILENAME}${TRANSFER}.WAV)

Para rodar a macro colocar antes da ligação de entrada ou saida ex.:

[meu contexto de saida]

exten => _9[2-9]XXXXXXX,1,Noop(Ligação de saida)
exten => _9[2-9]XXXXXXX,n,Macro(record)
exten => _9[2-9]XXXXXXX,n,Dial(SIP/PEERPROVEDOR/${EXTEN})
exten => _9[2-9]XXXXXXX,n,Hangup()
```

### URA

Unidade de resposta audível, assim que ligar em um número vai reproduzir um audio , esse audio geralmente apresenta opções,
digite 1 para suporte, 2 para vendas etc.Podendo direcionar para um ramal, uma fila ou até uma outra URA (cascateamento)

A URA é criada pelo dialplan com regras para quando entrar por aquele numero o mesmo consiga direcionar para um ramal, fila ou outra ura

Exemplo de URA:

```
[ura]
exten => s,1,Answer()
exten => s,n,Set(TIMEOUT(response)=10)
exten => s,n,Set(TIMEOUT(digit)=2)

exten => s,n(retorna),Background(/var/lib/asterisk/sounds/original) // audio que sera reproduzido 
exten => s,n,Wait(5)
exten => s,n,Goto(t,sair)

exten => 1,1,Goto(fametreinamentos,1000,1) // vai direcionar para um contexto e dentro dele para um ramal
;exten => 1,1,Dial(SIP/1000,30,${OPD}) // aqui direciona direto para um ramal 
exten => 1,n,Hangup()

exten => 2,1,Dial(SIP/1001)
exten => 2,n,Hengup()

exten => 3,1,Dial(SIP/1002)
exten => 3,n,Goto()

exten => i,1,Playback(invalid)
exten => i,n,Goto(s,retorna)

exten => t,1(sair),Playback(/var/lib/asterisk/sounds/vm-goodbye)
exten => t,n,Hangup()
```

Dentro do extensions.conf é feito um contexto que inclui o arquivo caso este esteja separado ex:

```
[ura]
#include Custom/ura.conf
```

### Callback 

Faz o retorno das chamadas de forma automática, quando um cliente liga para a empresa por exemplo, fica esperando na fila e desliga, um callback consegue retornar para o cliente e em seguida transferir para uma fila ou para um ramal 

Quando usado em produção precisamos ter um DDR específico para isso, não podemos usar um mesmo DDR para URA,Callback ou fila

O callback é feito no dialplan também e pode ser feito em forma de macro ex.: 

```
[macro-callback]
exten => s,1,set(CALLBACK-FILE=/var/spool/asterisk/tmp/${MACRO_EXTEN}-${CALLERID(num)}.call)
exten => s,n,system(echo Channel: SIP/PROVEDORVOIP/${CALLERID(num)} > ${CALLBACK-FILE})
exten => s,n,system(echo Context: saida-callback >> ${CALLBACK-FILE})
exten => s,n,system(echo Extension: 1000 >> ${CALLBACK-FILE})
exten => s,n,system(echo Callerid: ${CALLERID(num)} >> ${CALLBACK-FILE})
exten => s,n,system(echo MaxRetries: 2 >> ${CALLBACK-FILE})
exten => s,n,system(echo RetryTime: 30 >> ${CALLBACK-FILE})
exten => s,n,system(echo WaitTime: 30 >> ${CALLBACK-FILE})
exten => s,n,Wait(5)
exten => s,n,system(mv /var/spool/asterisk/tmp/${MACRO_EXTEN}-${CALLERID(num)}.call /var/spool/asterisk/outgoing/)
exten => s,n,MacroExit 

Essa macro precisa de um contexto de entrada e um de saída:

ENTRADA:
exten = numero,1,Noop(RECEBIMENTO DE LIGACOES – TRONCO FAME TREINAMENTOS)
exten = numero,n,Noop(Chamada Entrante TESTE)
exten = numero,n,Noop(${CALLERID(num)})
exten = numero,n,Macro(callback,)
exten = numero,n,Hangup()

SAIDA:
[saida-callback]
exten => ramal,1,Answer()
exten => ramal,n,Wait(1)
exten => ramal,n,Read(NUMERO)
exten => ramal,n,Noop(${NUMERO})
exten => ramal,n,Macro(record)
exten => ramal,n,Dial(SIP/PROVEDORVOIP/5585${NUMERO},40,tg)
exten => ramal,n,Hangup()
```

### Filas 

Está ligada a callcenter para receber ligações 

Configurada no arquivo queues.conf ex:

```
[general]
persistentmembers=yes 
keepstats=no
autofill=no
monitor-type=MixMonitor
shared_lastcall=no

persistentmembers yes -> quando usamos agente dinamico as configurações ficam salvas no banco de dados do asterisk, dessa forma  os agentes permaneceram na fila e não serão apagados

keepstats no -> mantem as estatisticas durante um reload 

autofill yes -> conforme for tendo disponibilidade de operadores as chamadas em espera serão distribuidas 

monitor-type -> so chama a aplicação mixmonitor para gravar as ligações 

sahred_lastcall yes -> no caso do agente pertencer a mais de uma fila sera respeitado o tempo de espera 
```

Parametros do contexto da fila:

```
[nome-da-fila]
musicclass=default ; classe para musica de espera, configurado no arquivo musiconhold.conf
strategy=ringall ; tipo de estratégia de fila
timeout=15 ; tempo em segundos que a ligação vai tocar em um ramal
retry=5 ; tempo em segundos antes de voltar a chamar naquele agente
weight=0 ; peso da fila, se aquela fila terá mais valor que outra no atendimento para agentes que estao em mais de uma fila
autopause=no ; Se um agente nao responder a uma chamada será colocado em espera caso yes
maxlen=0 ; numero maximo de clientes que podem estar aguardando na fila, 0 = sem limite 
```
```
Tipos de estratégia:

Leastrecent: Atribui a chamada para o agente que está a mais tempo sem atender uma chamada

 Fewestcalls: Atribui a chamada para o agente que menos atendeu chamadas

 Random: Atribui a chamada aleatóriamente para qualquer agente disponível

 Ringall: Chama todos os agentes de uma unica vez e os
primeiros a escolher é que irão atender as chamadas

 RRMemory: Distribui as chamdas entre os agente e lembra o utilimo agente que tentou chamar.

 Linear: Distribui as chamadas de acordo com as configurações desse arquivo.

 Wrandom: Atribui a chamada aleatoriamente usando uma métrica baseada em penalidaes
```

### Entroncamento - Centrais 

Com entroncamento de centrais você reduz a zero o custo de ligação entre centrais ex.: Um escritório de Fortaleza precisa ligar para o do Rio 

Assim posso utilizar vários asterisks registrando em cada um e fazer a comunicação direta entre ramais.

Com um contexto de entrada, um de saída e um tronco eu consigo comunicar minha matriz com a filial com custo zero.

É preciso criar o register em ambos os servidores para que reconheçam os ramais

Primeiro precisamos de um ramal diferente da faixa utilizada, se a faixa é 1000 então o ramal pode ser 300 por exemplo:

```
Asterisk 1 -> 

Crio um ramal no sip.conf com uma faixa diferente da que estou usando nos demais ex se uso 1000, 1001 esse sera 300 e um register 

sip.conf 

[300]
type=friend
context=basico
secret=xxxxxx
host=dynamic
qualify=yes

register => 400:Olxbr1040@192.168.15.125

Rota de saída dialplan uso o ramal que quero ligar da outra central ex.: 

[contexto_saida]
exten => 2000,1,Noop(Ligacao entre Servidores)
exten => 2000,n,Macro(record)
exten => 2000,n,Dial(SIP/300/${EXTEN},34,)
exten => 2000,n,Hangup()
```

```
Asterisk 2 -> 

Criar ramal no sip.conf com uma faixa diferente do que estou usando nesse servidor ex.: 400 e um register 

sip.conf

[400]
type=friend
context=basico
secret=xxxxxx
host=dynamic
qualify=yes

register => 300:Olxbr1040@192.168.15.134

Rota de entrada(que será a mesma que saída)

[contexto_entrada]
exten => 2000,1,Noop(Ligacao recebida)
exten => 2000,n,Macro(record)
exten => 2000,n,Dial(SIP/${EXTEN},35)
exten => 2000,n,Hangup()
```

Lembrando que o parametro no sip.conf insecure=invite,port precisa estar dessa forma, isso indica que posso ligar sem estar registrado como trunk

### Sistemas baseados em asterisk

Alguns sistemas possuem ISO e são baseados em asterisk como asteriskNOW, Elastix, Vicidial e outros 

possuem interface web que podem ser modificadas com php por exemplo 

### Segurança Asterisk

É possivel contestar com a operadora em caso de invasão 

Pontos de atenção de segurança:

* Regras Iptables 
* Manager.conf
* Externip/LocalNet 
* Senha dos ramais
* Planos de discagem 
* Logs

**Exemplo de conf iptables:**

/etc/sysconfig/iptables
Ou posso criar um arquivo com essas regras e usar o comando iptables-restore < nomedoarquivo 

:INPUT DROP [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -p udp -m udp -s 192.168.15.0/24 --dport 5060 -j ACCEPT
-A INPUT -p udp -m udp -s 192.168.15.0/24 --dport 10000:20000 -j ACCEPT
-A INPUT -p tcp -s 192.168.15.0/24 --dport 22 -j ACCEPT
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -l lo -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited COMMIT

Aqui eu so libero para minha rede autenticar, caso queira outro IP posso criar um grupo comentando com # e abaixo adicionando as regras referente ao IP de origem onde precisa se registrar ex.: 

# regras externas
-A INPUT -s 187.65.0.10/32 -p tcp --dport 5060 -j ACCEPT
-A INPUT -p udp -m udp -s 187.65.0.10/32 --dport 10000:20000 -j ACCEPT

**Manager** 

Usado em integrações, comando manager show events por exemplo mostra os eventos rodando 

por padrão no arquivo o contexto [general] já vem como enabled=no, mas caso seja usado passa a ser yes. Posso usar com AMI.

Pode ser restringido da seguinte maneira:

```
manager.conf

[contexto]
secret=password
deny=0.0.0.0/0.0.0.0
permit=209.16.236.73/255.255.255.0

Somente serão permitidos conectar no manager aqueles de origem do IP colocado nada mais.
```

**Logs**

Posso ver logs em /var/log/asterisk/messages -> detalhamento de informações 

var/log/secure -> mais informações como quem esta tentando conectar no manager 

Habilitar logs dentro do asterisk:

/etc/asterisk/logger.conf -> habilitar logs especificos para a cli por exemplo como liberando o full que cria o arquivo /var/log/asterisk/full

### Relatórios e registros de chamadas 

Existem um arquivo padrão para registro de todas as ligações tac /var/log/asterisk/cdr-csv/Master.csv | more -> por aqui é possível analisar o que houve com as ligações e também com ele posso criar uma interface de relatórios web para visualização. Essas informações são armazenadas dentro do banco cdr

Sempre fazer backup diário do asterisk tanto da pasta /etc/asterisk como da /var/spool/asterisk/monitor onde se localizam as gravações, o recomendado é usar o cron pra fazer isso. Também realizar um mysqldump e fazer um backup do banco de dados 

Combinação de backup CDR + Gravações + Configurações 


### Estudar - conhecer 

* **Fail2ban**
 



