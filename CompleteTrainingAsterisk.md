## Conexão 

Comando /etc/init.d/asterisk restart -> reinicia o serviço do asterisk

Precisa de uma maquina com placa de rede, algumas empresas usa uma placa USB para conectar com a rede ISDN (Rede digital de serviços integrados) esse equipamento é como um modem que conecta tanto no telefone do uauário como no computador, assim é usado o mesmo canal para transmissão de voz e dados. Podemos contratar também uma linha analogica com a operadora. 

Atualmente é usado a tecnologia SIP para conexão com operadora de voz,através de trunks. Com isso podemos contratar diversas linhas em um mesmo circuito. 
Tudo por ser conectado em um switch que manda para a internet 

- LTS ->Long Term Support

## Macros vs Gosub

No asterisk 16 não existe mais o conceito de Macro, agora é Gosub. A macro funcionava de seguinte maneira

MACRO:
[macro-teste]
exten => s,1,Dial(${ARG1},20,t)
exten => s,n,Goto(${DIALSTATUS})
exten => s,n,hangup()
exten => s,n(BUSY),voicemail(b${MACRO_EXTEN})

[from-internal]
exten => 6000,1,Macro(teste,SIP/6000)  - O sip/6000 aqui é o arg1

GOSUB:
[teste]
exten => s,1,Dial(${ARG1},20,tT)
exten => s,n,Goto(${DIALSTATUS})
exten => s,n,hangup()
exten => s,n(BUSY),voicemail(${ARG2},b)

[from-internal]
exten => 6000,1,Gosub(teste,s,1(SIP/6000,${EXTEN}))  - O sip/6000 aqui é o arg1

## Dimensão do Hardware

- 80% dos usuários de Asterisk tem menos de 30 ligações simultâneas e até 240 usuários. Qualquer servidor moderno pode usar isso
- 20% com 3000 ligações simultâneas precisam usar o SC=4(security coefficient) e ter dimensões para 750 usuários 
- Para cada mil ligações por segundo = 100Mb por segundo nesse caso é recomendado usar uma placa de internet GB
Para conexão com SIP trunks só é preciso do login e de uma placa de internet 
Para conexão com linhas analógicas como E1 por exemplo são usadas placas específicas que podem ser compradas por empresas como: Digium,Sangoma,OpenVox,Aligera

## Fatores de pejuizo
- Ligações simultaneas
- dispositivos registrados
- Trancodificação (codec G729 precisa de mais CPU para isso)
- CAPS(Call attempts per second) - Tentativas de chamadas por segundo (carregar com mais de 100 é um problema )
- Quantidade de gravações indo para o banco de dados (HD só suportam 75 operações por segundo)

## Ferramentas de teste de tráfego
Sip - Traffic Generators

## Pacotes de instalação 
- dahdi-linux: drivers das placas
- dahdi-tools: utilitários para as placas
- libpri: usado para placas ISDN

## Registo de ramais 

Caso va utilizar sip teste primeiro desabilitando o modulo pjsip em mosules.conf para não apresentar conflito
IMPORTANTE: Desabilitar o STUN ou ICE dos softphones pois pegam o IP externo do servidor caso estejam em NAT e pode apresentar problemas com o registro do IP privado  

## SIP

### sip.conf

Ramais sip são cadastrados da seguinte maneira

[general]
bindport=5060 porta padrão para uso da tecnologia
bindaddr=0.0.0.0 ; houve um endereço IPV4 específico ou habilita para todos 
context=dummy ; contexto padrão de recebimento de chamadas
disallow=all ; bloqueia tudo 
allow=ulaw ; permite somente ulaw
alwaysauthreject=yes ; sempre rejeita invites ou registers desconhecidos, segurança para invasores
allowguest=no ; não permite convidados 

[nome ou numero do ramal] ; usado para autenticar
type=friend ; 
secret=#supersecret# ; senha do ramal
host=dynamic ; tipo de host que será usado para conexão 
qualify=yes ; verifica de tempos em tempos se o dispositivo ainda está online
directmedia=no ; asterisk fica intermedio entre o NAT da rede do usuario e o audio a midia nao passa direto
context=from-internal ; contexto do ramal

register => login:passwd@domain:port/9999 ; login e senha@dominio ou ip da operadora: porta/ ramal callback, que vai receber as ligações vindas desse registro definida na sessão de configuração do trunk

rtptimeout=60 -> Em caso de perda de conexão após um canal ja ter sido estabelecido, uma queda de internet por exemplo sem envio do BYE o rtptimeout fica com o papel de derrubar uma chamada presa quando está pendurada. Se nenhum pacote SIP, ACK ou qualquer outro naquele canal ocorrer após 60 segundos ele derruba a ligação  

### Qos Tagging

- Nao dita as regras de QOS apenas taggeia para que o switch priorize  

tos_sip=cs3
tos_audio=ef
tos_video=af41
tos_text=af41

cos_sip=3
cos_audio=5
cos_video=4
cos_text=3

### Tipos de par ou peer

Friend -> Recebe e envia ligações
Peer -> Enviar ligações 
User -> Receber ligações 

Geralmente quando peer a autenticação é feita por IP e não por usuario como em type user

- Primeiro uma chamada tenta achar um par tipo user, caso não encontre ela vai procurar um tipo peer e ver se o IP está batendo, em caso de não existir nenhum dos dois só será aceita se a opção allowguest estiver habilitada e o contexto for definido na general section em sip.conf

### sip trunks

Posso ter troncos sip que são digitais ao invés de linhas T1 ou E1
Os troncos sip possuem alto volume digital limitado apenas à largura de banda

[general]
srvlookup=yes ; se a operadora usar um DNS é preciso colocar essa linha, se for IP não é necessária a linha

[siptrunk]
type=peer ; para trunks é peer, o friend manda e recebe ligações o peer apenas envia, atualmente também recebe 
defaultuser=login ; esse login e senha não necessariamente será igual o de registro 
secret=senha
port=5600 ; uma porta diferente impede ataques para maquinas abertas a internet
insecure=invite ; para nao solicitar auteticação para cada ligação 
host=dominio/IP ; so aceita receber ligações desse endereço
fromuser=login
fromdomain=dominio
dtmfmode=rfc2833
context=from-sip
disallow=all
allow=ulaw

## Parametros de registro com operadoras 

Para auteticar com a operadora é preciso do IP da operadora, login e senha 
Alguns também vão disponibilizar o tech prefixo (geralmente 4 ou 5 digitos usados antes do número da ligação )

O registro não é o login, quando você se registra no servidor está apenas informando onde está (sua localização)
IMPORTANTE: Muito fornecedores vão permitir a saida de ligação quando não registrado neles porém nunca o recebimento 

## DTMF

RFC2833 - mais usado
INFO - para info message 
Audio - como audio no canal

## Dialplan

* Extensions format:
8000
Alexander
4321/1234 -> só vai se conectar ao ramal 1234 se for chamado por 4321
_4XXX -> expressão regular, qualquer número que comece com 4 e tenha mais 3 digitos
s -> extensio padrão

* Priorities format
1-9
n -> próximo da sequencia
s -> mesma prioridade

x -> 0-9
z -> 1-9
n -> 2-9

### Padrões de extensões Asterisk

i -> invalido, quando é pressionado algo invalido no contexto
s -> inicio, quando não ha número associado a essa chamada 
h -> hangup, processa algumas coisas após o desligamento
t -> timeout, para timeout em URAS
T -> tempo limite absoluto setado
o -> operador, manda para a operadora
a -> usado quando o * é pressionado no menu de voicemail
Talk -> usado para detectar background

### Lógica 

Posso ter as seguintes operações 

$[expression1 operator expression2]

- Matematicos
- operator -> + - * / %

- Logicos
- (& and) (| or) (! no)

- Comparativos
- =,>,>=,<,<=,!=

- Expressões regulares
- :, ou =~

- Condicionais
- expression1 ? expression2 :: expression3

### IVR - URA

[from-siptrunk]
include = ivr

[ivr]
exten => 9999,1,Answer()
exten => 9999,n,Background(greeting) -> toca o audio e você pode digitar um numero 
exten => 9999,n,Dial(${OPERATOR}) -> caso nada for digitado seguira para discar para o ramal setado em OPERATOR
exten => 1,1,Dial(SIP/zoiper) -> caso for digitado 1 cai aqui
exten => 2,1,Dial(SIP/xlite)
exten => 3,1,Dial(SIP/blink)

## Voicemail - Correio de voz e Conference - Conferência 

### voicemail.conf
Para recebimento de uma caixa de correio é preciso criar uma caixa de correio

[default]
<Mailbox ID>=<pincode>,<fullname>,<email address>,<pagermail>,<option=value>,<option=value>

1234=>1234,Joe Doe,joedoe@somecompany.com,joedoe@somecompany.com,saycid=yes

Para colocar em funcionamento usamos algo para chamar isso, como uma Macro por exemplo 
* Dialplan:

[macro-stdexten]
exten =>s,1,Dial(${ARG1},20,t) -> o ARG um é setado futuramente quando chamamos a Macro 
exten =>s,n,Goto(${DIALSTATUS}) -> o Dial status vai ser pego a partir da chamada sendo um dos a seguir : Busy,noanswer,cancel,chanunavail,congestion
exten =>s,n,Hangup()
exten =>s,n(BUSY),voicemail(b${MACRO_EXTEN}) -> b representa a mensagem busy e macro_exten é a extensão chamada pela macro
exten =>s,n,Hangup()
exten =>s,n(NOANSWER),voicemail(u${MACRO_EXTEN}) -> unattended message
exten =>s,n,Hangup()
exten =>s,n(CANCEL),hangup
exten =>s,n(CHANUNAVAIL),hangup 
exten =>s,n(CONGESTION),hangup -> caso for um dos casos acima simplesmente desliga a ligação

[from-internal]
exten=> 6000,Macro(stdexten,SIP/6000) -> Aqui eu setei o ARG 1
exten => 6001,Macro(stdexten,SIP/6001) - > o primeira numero de entrada no caso 6001 será o macro_exten

## Aplicativos

#### VoiceMailMain
VoiceMailMain()

exten => 9000,1,VoiceMailMain() -> com esse aplicativo posso ouvir a minha caixa de correio

Level#1
- 0 Mailbox options
- 1 Read Voicemail messages 
- 2 Change folders
- ***Help** 
- **# Exit**  

### Confbridge
Confbridge(conference[,bridge_profile[,user_profile[,menu]]])    
Funciona como uma sala de conferência, é necessário especificar o número ou nome da conferência
 
Se duas pessoas ou mais discarem o mesmo número todas serão redirecionadas para a conferência 

### Dial
Dial(type/identifier/timeout,potions,URL)

exten =>_6xxx,1,Dial(SIP/${EXTEN},60,Tt) -> o Tt permite transferência para o chamador e destinatário

- Consultar informações: core show application Dial
- Após executar um Dial ele seta algumas variáveis

VARIAVEIS IMPORTANTES:

- $ {DIALEDTIME} - Tempo para a ligação (segundos). Só é definido se a chamada foi atendida.
- $ {ANSWEREDTIME} - Tempo desde a resposta até o desligamento (segundos)
- $ {DIALEDPEERNAME} - nome do par discado
- $ {DIALEDPEERNUMBER} - número de par discado
- ${DIALSTATUS}  - Status da chamada: (CHANUNAVAIL | CONGESTION | BUSY | NOANSWER | ANSWER | CANCEL | DONTCALL | TORTURE)

OPÇÕES:

D -> manda o DTMF (codigo) especifico depois que o outro lado atender
L -> Estipula um limite de tempo de chamada, x,y,z manda um aviso sobre o desligamento e encerra após o periodo estipulado
r -> Gera um ring falso, setado em indications.conf tonezone 
S -> Encerra a chamada após algum tempo sem aviso
T -> Habilita a transferência após digitar o codigo escolhido em features.conf
x ou X -> Habilita a gravação após digitar o codigo exolhido em automixmonitor no arquivo features.conf


### Asnwer
- O comando Answer() atende um canal que está tocando

### Progress
- O Progress manda uma sinalização 183 Session Progress que indica que uma sessão SDP foi estabelecida, indicando um audio bi direcional (para conferências)
Caso eu não queira atender uma chamada e somente passar um audio posso iniciar com Progress() e em seguida chamar o Playback()

### Background 
Background(filename[$filename2][options][angoverride][context]) -> toca um audio e também aceita digitos de entrada 
s -> pula a mensagenm
n -> não abre o canal antes de tocar o arquivo de audio
m -> interrompe a mensagem quando um digito da extensão é pressionado

exten => s,1,Answer()
exten => s,n,background(greeting)
exten => s,n,Dial(${OPERATOR})
exten => 6000,1,Dial(SIP/6000)
exten => 6000,1,Dial(SIP/6000)

### Read
Read(variable[|filename][|maxdigits][|options][|attempts][|timeout]) -> Le o que foi digitado e adiciona em uma variavel
maxdigits -> Número maximo de caracteres aceitos
s -> retorna se a linha não estiver acima 
i -> toca o arquivo indicado de tom em indications.conf
n -> le os digitos mesmo sem linha acima

### Record
Grava audios e salva na pasta padrão /var/asterisk/sounds

exten => _4.,1,Record(${EXTEN:1}:gsm) -> qualquer numero que começa com 4 e tenha digitos ou letras apos vai ser gravado o audio como o numero sem o primeiro digito ou seja o 4, com o formato gsm
exten => _4.,n,wait(1)
exten => _4.,n,Playback(${EXTEN:1}) -> toca o audio gravado
exten => _4.,n,hangup() -> para terminar a gravação basta digitar o # ou desligar a ligação 

## Features

### Call Tranfer - Transferência

### Transferência assistida -> Fala com a pessoa da outra ponta primeiro 

- É habilitada em features.conf não está por default com atxfer => *2
- Após configurado é preciso reiniciar o modulo - rasterisk - module reload features
- Caso o aparelho não tenha uma botão de transferência é preciso habilitar em features.conf o número que será discado para a transferência geralmente 2
- Após transferido é conversao com a pessoa da outra ponta para somente depois desligar e encerrar a chamada e o sistema vai seguir com a transferência

### Transferência sega

- Somente transfere a ligação, esse tipo de transferência precisa ser configurada no Dial() com as opções T ou t, liberando transferência para o originador ou o destino
- Pressione # e a extensão que deseja transferir
- Se caso o outro lado não atender a ligação volta a tocar no ramal que estava com a ligação 
- Ja vem por padrão com a tecla # em features.conf

### Call parking - Ligação estacionada

- O contexto padrão fica localizado dentro de res_parking.conf - context => parkedcalls  

- Posso realizar uma transferência e esta ficar presa em um slot (como uma fila de espera) 
- O asterisk vai te informar qual o numero do slot em que a ligação está aguardando
- Basta ligar no número do slot passado e a ligação vai entrar pra você 
- Funciona como várias salas de espera, e a pessoa que está aguardando vai ouvir a música de espera

[general]
parkext => 700
parkpos => 701-720

context=> parkedcalls

### Call Pickup - Captura de chamadas 

- A captura de chamada é quando tem um telefone tocando em uma mesa e estou em outra mesa, após digitar a facilidade (geralmente 8) posso capturar essa chamada para o meu telefone 
- Posso definir grupos de captura
- Habilitado em features.conf

callgroup=1 -> Estou no grupo de captura 1
pickupgroup=1 -> Só posso capturar chamadas do grupo 1

callgroup=3 -> Estou no grupo de captura 3 
pickupgroup=1,2,3 -> Posso capturar chamadas dos grupos 1 2 e 3

### Call Recording - Gravação de chamada

MixMonitor(file.ext[|option[command]]) -> Grava a ligacão em um canal específico

Opções:

- b : Somente salva a gravação enquanto o canal estiver em ponte
- a : Anexa ao arquivo ao invés de sobrescrever 

- Gravação em linha : Automixmon()
- Precisa da opção X no Dial() e pressionar 1 para começar a gravar

### Music on Hold - Musica de espera

- As classes são definidas dentro de musiconhold.conf
- É separada por classes que podem ser definidas com Set(CHANNEL(musicclass=...))
- Puxa os audios de /var/lib/asterisk/sounds

[default] -> classe
mode=files -> files são arquivos de audio mp3 por exemplo
directory=moh -> o diretório pode ser criado em /var/lib/asterisk/sounds que é o diretório padrão que puxa essas informações

### Follow me - Siga me

- Configurado em followme.conf

[6000]  -> ID para ser chamado no dialplan
context=from-internal
number=>91354523323,30 -> qualquer ligação que acusar busy ou noanswer vai ser transbordada para esse numero por 30 segundos e caso não tenha resposta vai discar no outro número por mais 30 segundos 
number=>91354523324,30

- extensions.conf

[from-internal] 
exten=6000,1,Dial(SIP/Zoiper) -> aqui entra uma ligação e disca para o zoiper, caso ele não atenda a ligação vai para o followme e toca nos numeros configurados
exten=6000,n,followme(6000) 

## SIP Headers - Cabeçalhos SIP 

**Requisição SIP:**

INVITE sip:flavio@opensips.org SIP/2.0 -> metodo URI Versao do protocolo 
Via: SIP/2.0/TCP 192.168.11.1:30849;branch=z9hG4bK-d8754z-1d44ba47680bb22d-1---d8754z-;rport. -> indica para onde as respostas serão enviadas branch = indica o numro da transação 
Max-Forward: 70. -> Previne loops, cai a cada salto entre os gateways  
Contact: <sip:bordan@192.168.11.1:54716;transport=TCP>. -> endereço para enviar o BYE ou encerramento da chamada
To: "Flavio E. Goncalves"<sip:flavio@opensips.org>. -> Destino inicial da mensagem, alguns GW usam esse campo para enviar, porem a maioria usa a URI
From "Bogdan Andrei lancu"<sip:bogdan@opensips.org>;tag=bc210957. -> Caller ID ou o originador da chamada
Call-ID: OwyadhGHgasbwkmddhjaTjbk.. -> identificador da chamada, todas as transações em uma ligação terão esse mesmo ID
CSeq: 1 INVITE. -> É incrementado a casa requisição sequencial, 1,2,3
Allow: INVITE,ACK,CANCEL,OPTIONS,BYE... -> metodos permitidos 
Content-Type:application/sdp 
User-Agent: X-Lite release 1100|stamp 47546 -> identifica o tipo de dispositivo

## Codecs

* G.711(PSTN) -> opera em 64Kb, é conhecido como o avô da telefonia digital, existem duas versões desse codec o A-LAW e o U-LAW, no Brasil é usado o ALAW transportados por um tronco T1 de 24 e 30 canais dependendo da largura de banda. É ideal para bandas largas mas pode ser usado para LANS

* G.729(WAN) ->

* OPUS(INTERNET) ->

* G.722(HD) ->

* IMPORTANTE: Os codecs mesmo trabalhando com 8Kb por exemplo como o G.729 podem chegar a 32Kb devido os cabeçalhos dos frames seguindo o modelo OSI, cabeçalhos do UDP, TCP/IP, RTP e assim por diante
* Reduzir banda :
- Apicar o payload pode reduzir a banda em até 16Kb, porém não está habilitado em todos os softphones

- Podemos reduzir a banda também empacotando ou reduzindo os cabeçalhos ex.: IP,UDP,RTP e Payload, reduzimos para um único e mantemos somente o payload, isso é feito pelo roteador, somente disponivel em PPP ou Frame-relay

## PJSIP

- Podemos definir mais de um transporte 

### pjsip.conf 
- Para configurar um ramal (extension) é preciso de 3 sessões: endpoint, auth e aor

[xlite]
type=endpoint
transport=transport-udp-main
context=from-internal
disallow=all
allow=ulaw
aors=xlite
auth=xlite

[xlite]
type=auth
auth_type=userpass
username=xlite
password=#supersecret#

[xlite]
type=aor
max_contacts=1

### pjsip trunk
- Para configurar o trunk é preciso de 5 sessões: endpoint,aor,auth,registration,identify

[siptrunk]
type=endpoint
transport=transport-udp-main
context=from-siptrunk
direct_media=no
disallow=all
allow=ulaw
outbound_auth=siptrunk
aors=siptrunk

[siptrunk]
type=aor
contact=sip:sip.api4com.com:5060

[siptrunk]
type=auth
auth_type=userpass
username=1020
password=supersecret

;As próximas sessões são para registro e recebimento de ligações 

[siptrunk]
type=registration
outbound_auth=siptrunk
server_uri=sip:1020@sip.api4com.com:5060
client_uri=sip:1020@sip.api4com.com
contact=9999

[siptrunk] -> Essa sessão é responsavel por passar a chamada para o endpoint
type=identify  -> identifica por IP uma chamada de entrada e envia ela para o endpoint 
endpoint=siptrunk
match=sip.api4com.com

### Transformar SIP em PJSIP

- /usr/src/asterisk-version/contrib/scripts/sip_to_pjsip
- Aqui ficam os scripts para transformar um arquivo em outro, o script usado para isso é o sip_to_pjsip.py

- Precisa ser instalado o Python e a sintaxe fica

* $ ./sip_to_pjsip.py caminho_do_arquivo_sip nome_do_novo_arquivo
* $ $ ./sip_to_pjsip.py /etc/asterisk/sip.conf pjsip_new.conf

- Comando para troubleshooting pjsip: pjsip set history on
- pjsip set history off -> Para parar o debbug
- pjsip show history 

### PJSIP Dial

[from-outbound]
exten => _6XXX,1,Dial(PJSIP/${EXTEN})
exten => _6XXX,1,Dial(${PJSIP_DIAL_CONTACTS(${EXTEN})}) -> Aqui se eu tiver dois ramais conectados ao mesmo endpoint(softphones ou hardphone) pode fazer a chamada simultanea para os dois
exten => _9.,1,Dial(PJSIP/siptrunk/sip:${EXTEN:1}@sip.api4com.com) -> forma tradicional de ligação para tronco
exten => _9.,1,Dial(PJSIP/${EXTEN:1}@siptrunk) -> Outra maneira de ligar para o tronco

### ACL -> Proteção 
- Definido em pjsip.conf ou em acl.conf
- Em acl.conf opera em qualquer entrada SIP

[acl]
type=acl
deny=0.0.0.0/0.0.0.0
permit=192.168.0.0/24
deny=192.168.0.1

## Desabilitar SIP 

- modules.conf 
- noload = chan_sip.so
- reiniciar o serviço do asterisk

## NAT

- O NAT pode afetar o cabeçalho SIP, alterando o header Via, contact e o corpo SDP, como media address

#### Tipos de NAT

* Full Cone
- Configurado um Nat manual no router ou no equipamento de saida, um IP externo estático que pode receber solicitações da internet e que vai passar para um IP interno 

* Restricted Cone
- Parecido com o full cone porém o router ou o equipamento de saída não tem o IP externo (NAT) estático e sim dinamico, assim que chega a solicitação da internet é gerado o IP 

* Port Restricted Cone
- Parecido com o Restricted Cone é dinamico o mapeamento NAT porém o firewall vai checar o IP e a porta que está enviando a solicitação antes de permitir a entrada do pacote na rede 

* Symetric
- É similar ao Port Restricted Cone, o firewall checa o IP e porta antes de liberação mas refaz o mapeamento NAT para cada novo endereço externo ex.: 1 -> IP 200.180.4.168 porta 1234 e para uma segunda maquina ele fica IP -> 200.180.4.168 porta 5678. E isso causa problemas porque algumas tecnologias para resolver problemas de NAT não funcionam nesse tipo de NAT

### Praticas para atravessar o NAT

- Usar RFC3581(Somente cabeçalhos VIA) -> habilitar nat=yes no asterisk ou force_rport
- Traversal of UDP over relay NAT - TURN
- STUN -> Testa e verifica qual o tipo de NAT para dar o tratamento correto, porém não suporta o symetric NAT
- Traversal of UDP over relay NAT -> 
- ICE
- Application Layer Gateway -> www.voip-info.org/routers-sip-alg/ Se suspeitar de um problema de ALG pode trocar a porta UDP de 5060 para outra e o cliente também precisa usar outra porta no softphone

- Usar o force_rport
- Usar STUN e COMEDIA quando possivel, o comedia tem alguns parametros para a camada de sessão que força o cliente a enviar o pacote RTP logo no inicio da ligação 
- Detectar o symetric NAT e usar o TURN nesses casos 

### NAT em chan_sip

- qualify=yes -> é um ping para verificar se o cliente ainda está de pé 
- directmedia=no -> encaminha a midia pelo Asterisk
- nat=force_rport,comedia

- nat=auto_force_rport -> usado quando desejo que funcione de uma forma para rede externa e outra para rede interna 
- nat=auto_comedia -> usado quando desejo que funcione de uma forma para rede externa e outra para rede interna 

### NAT em chan_pjsip

[phone]
type=endpoint
direct_media=no
rtp_symmetric=yes
force_rport=yes

[phone]
type=aor
qualify_frequency=15

### Servidor atrás de NAT chan_sip

- Em servidores como AWS que possuem NAT configurar:

externaddr=12.34.56.78 -> Será o endereço de IP que o Asterisk vai inserir na requisição SIP nas solcitações que vão para internet e não as internas
localnet=192.168.0.0/255.255.0.0
localnet=10.0.0.0/255.0.0.0
localnet=172.16.0.0/12

externhost=foo.dyndns.net -> usado para recarregar uma conexão com DNS dinamico
externrefresh=180

- configurar NAT=yes 
- direct_media=no

### Servidor atrás de NAT chan_pjsip

[transport-udp-nat]
type=transport
protocol=udp
bind=0.0.0.0
local_net=192.168.0.0/16
external_media_address=203.0.113.1
external_signaling_address=203.0.113.1
external_signaling_port=5060

## Topicos avançados de dialplan

[general]
static=yes 
writeprotect=yes -> as atualizações no dialplan serão salvas na memoria
autofallthrough=yes -> sempre depois da ultima prioridade encerra a chamada com busy ou hangup
clearglobalvars=no -> não apaga as variaveis 
userscontext=default -> Se usar o arquivo users.conf o contexto padrão será o default 

## AStDB

Banco de dados do asterisk, para setar uma variavel dentro do banco segue:

exten => 456,1,Set(DB(teste/contagem)=1)

- Familia teste
- Chave contagem 
- valor 1

## Limitar ligações simultâneas

A limitação é feita definindo grupos ex.:

exten => _214X,1,set(GROUP()=Rio)
exten => _214X,n,GotoIf($[$[GROUP_COUNT()] > 1]?overflow)
exten => _214X,n,Dial(SIP/${EXTEN})
exten => _214X,n,hangup
exten => _214X,n,(overflow),playback(exceededcalls)
exten => _214X,n,hangup

## CDR

O CDR padrão é localizado em /var/log/asterisk/Master.csv
Posso habilitar os outros modelos em cdr-custom e cel-custom dentro de /etc/asterisk e habilitando no arquivo cdr_custom.conf por exemplo

Podemos setar um codigo de conta ou uma tag no arquivo de canal como sip.conf ou pjsip.conf

O que temos em um arquivo de cdr como Master.csv segue:

accountcode, src, dst, dcontext, clid, channel...

Para desabilitar o cdr padrão e deixar somente o customizado use o comando: module unload cdr_csv.so 

### Funções do CDR

Set(CDR(name)=value) -> posso setar variaveis 
ResetCDR() -> resetar o CDR
ForkCDR() -> gerar novo CDR

## CDR ODBC 

Serve para conectar qualquer banco de dados ao Asterisk

VERIFICAR - LAB

## Troubleshooting Asterisk

Passos para uma boa resolução 

- Reproduzir o problema 
- Coletar rotas e logs
- Documentar a solução
- Fazer o troubleshooting a partir do modelo OSI

7 - Application | OpenSIPS
----------- | ---------
6 - Presentation | CODECS
5 - Session | SIP
4 - Transport | UDP/TCP/TLS/RTP/RTCP
3 - Network | IP
2 - Datalink | Frame-Relay/ATM
1 - Physical | ETHERNET  

- Podemos realizar o PING e ja eliminar as três primeiras camadas
- Depois verificar a ligação se passa RTP se estabelece a comunicação SIP

- Em casos de ter muitos sistemas conectados como por exemplo um PBX conectado a um softwitch e esse conectado a rede ITSP, conectar direto o PBX na rede e ver se a qualidade está boa para isolar o problema

## E quando o Asterisk não inicia?

- Podemos usar o comando : asterisk -vvvc 
- Isso fará com que abra o console e cheque se algum modulo não está carregando 

- Problemas com modulos como por exemplo não rodar comandos ex.: sip show peers
- Tentar carregar manualmente o modulo -> module load chan_sip.so
- Checar as portas que estão funcionando no servidor (Usado também para verificar conflito em portas) - usar netstat - tanpu

- Para paradas no Asterisk posso usar o comando: asterisk -g, isso vai me informar qual era a ultima coisa rodando no ASterisk 

## Logs

- Logs podem ser vistos na pasta /var/log/asterisk e são configurados pelo logger.conf 
- Podemos setar o verbose também : core set verbose 15 
- Ou o debug: core set debug 6

- Podemos trocar o Noop() pelo Verbose() - mesmo com o verbose em 0 na CLI o que for definido dentro desse será apresentado na CLI

### Logger.conf

- Aqui podemos setar opções do log 
- Habilitar somente full para debugs pois pode deixar o disco cheio

- Setar no crontab o logger rotate para sempre deletar arquivos antigos 

## Segurança 

- Ter senhas com minimo de 8 caracteres e especiais
- Instalar o Fail2Ban -> Para detectar multiplas tentativas de autenticação 
- Configurar IPTABLES
- Limitar ssh somente para endereços específicos
- Limitar HTTP para endereços expecíficos -> Para sistemas com interfaces
- Mimitar chamadas simultaneas para rotas Internacionais
- Dropar chamadas após um periodo de tempo

- Configurar IPTABLES e instalar Fail2Ban, assim quando for realizadas varias tentativas de registro o 