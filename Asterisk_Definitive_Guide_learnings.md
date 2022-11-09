## Modulos

* módulo -> componente carregável
* modules.conf
    - Habilitar autoload para carregar módulos automaticamente
* asterisk.conf
    - Define algumas configurações de pastas e opções da CLI
* indications.conf 
    - Define parâmetros de sons que se espera de um sistema telefônico

### Aplicativos Dialplan úteis:

* authenticate 
* cdr
* channelredirect
* chanspy
* controlplayback
* dial
* dictate
* directed_pickup
* directory
* disa
* dumpchan
* echo
* exec
* externalivr
* fax
* flash
* followme
* jack
* meetme
* milliwatt
* mixmonitor
* originate
* page
* playback
* playtones
* queue
* read
* record
* sayunixtime
* senddtmf
* skel
* softhangup
* speech_utils
* stack
* system
* talkdetect
* transfer
* userevent
* verbose
* voicemail
* waitforsilence
* waituntil
* while 

### Módulos registro de detalhes de chamada

* adaptive_odbc 
* csv
* custom
* manager
* pgsql
* sqlite3_custom
* syslog

## Drivers de canal úteis

* chan_agent
* chan_alsa
* chan_bridge
* chan_dahdi
* chan_iax2
* chan_local
* chan_multicast_rtp
* chan_oss
* chan_sip
* chan_skinny
* chan_unistim
* chan_usbradio

### Codec Translator 
Realiza a conversão de formatos de stream de áudios entre chamadas

* codec_alaw
* codec_a_mu
* codec_dahdi
* codec_g722
* codec_gsm
* codec_resample
* codec_speex
* codec_ulaw

### Intérprete de formatos 
Tradutores de codecs, usados para reprodução de arquivos, uma gravação armazenada em GSM por exemplo pode ser ouvida em outro canal não use esse codec com um interprete

* format_g729
* format_pcm
* format_sln
* format_wav

### Funções complementares dialplan

* func_aes
* func_audiohookinherit
* func_blacklist
* func_callerid
* func_cdr
* func_channel
* func_curl
* func_cut
* func_db
* func_devstate
* func_dialgroup
* func_dialplan
* func_enum
* func_env
* func_extstate
* func_global
* func_groupcount
* func_lock
* func_logic
* func_math
* func_md5
* func_odbc
* func_pitchshift
* func_rand
* func_realtime
* func_redirecting
* func_sha1
* func_shell
* func_speex
* func_sprintf
* func_srv
* func_strings
* func_sysinfo
* func_timeout
* func_uri
* func_vmcount
* func_volume

### Módulos PBX 
Mecanismos avançados de controle e configuração

* pbx_config
* pbx_dundi
* pbx_realtime
* pbx_spool

### Módulos de recursos

* res_adsi
* res_agi
* res_ais
* res_calendar
* res_calendar_caldav
* res_calendar_exchange
* res_calendar_icalendar
* res_clialiases
* res_config_curl
* res_config_odbc
* res_crypto
* res_curl
* res_fax
* res_fax_spandsp
* res_jabber
* res_monitor
* res_musiconhold
* res_odbc
* res_realtime
* res_rtp_asterisk
* res_timing_timerfd

### Módulos criados pela comunidade 

* cdr_mysql
* format_mp3
* res_config_mysql

## Estrutura de diretórios
* /etc/asterisk -> Módulos asterisk
* /var/lib/asterisk -> Armazenamento de scripts, Agis, audios etc.
* /var/spool/asterisk -> Armazena arquivos que serão alterados com frequência, mensagem de voz, gravação de chamadas
* /var/log/asterisk -> registros de chamada, eventos de canal, logs de depuração, fila, erros, mensagens.
* /etc/asterisk/extensions.conf -> Scripts de fluxo das chamadas, como as chamadas entrantes são tratadas 
* /usr/lib/asterisk/modules -> onde os módulos carregáveis são armazenados
* /var/lib/asterisk/agi-bin -> armazena e carrega scripts AGI
* /var/run/asterisk -> onde o asterisk grava o seu PID Id de processo
* /usr/lib/asterisk/module -> modulos do asterisk

## Inicialização asterisk

Comando: 
* asterisk 
    - h : lista do que posso usar ou man asterisk
    - c: executa em primeiro plano vinculado a minha sessão de user
    - v: quanto mais v mais detalhes na saida do console como verbose
    - d: verbose para depuração do código
    - r ou rasterisk: para executar como daemon
    - T: adiciona data e hora a saída CLI
    - x: executa uma string como se estivesse dentro da CLI

* modules.conf 
    - autoload = yes -> carrega todos os módulos da pasta do asterisk na inicialização
    - preload -> modulos caregados primeiro que outros
    - noload = chan_so -> Isso é um exemplo de modulo que não deve ser carregado dentro do asterisk
    - require -> precisa que aquele modulo opere, caso negativo o Asterisk para
    - Na opção make menuselect pode ser escolhido quais modulos serão compilados e instalados sendo assim possível ja daqui remover modulos que não vão ser úteis

* indications.conf
    - Indica tipos sonoros que o ASterisk pode reproduzir 
    - Set(CHANNEL(tonezone)=[yourcountry]) - para setar qual o toque padrão para o seu canal

* musiconhold.conf
    - Podem ser usados programas para converter audios MP3 para o formato de menor processamento no Asterisk [Musiconhold.conf-ASterisk](http://asteriskdocs.org/en/3rd_Edition/asterisk-book-html-chunk/additional_configuration_tasks-musiconhold-conf-file.html)
    - Você pode converter o arquivo MP3 para Wav e em seguida reduzir a resolução 

## Configuração e protocolos Asterisk

* sip.conf e iax.conf -> módulos chan_sip.so e chan_iax2.conf
    - Cada extensão terá seu contexto onde podemos rotear e manipular as ligações no dialplan

## Pares sip
    - type=friend/peer/user
    - Muda a forma que o Asterisk interpreta as solicitações SIP de entrada
    - friend -> Permite corresponder a peer e user. Mais usada
    - peer -> Corresponde as solicitações de entrada a partir de um endereço IP de origem e o número da porta 
    - user -> Corresponde as solicitações de entrada a partir de um nome de usuário no cabeçalho SIP 
* host
    - dynamic é para quando o telefone que está ligando vai nos dizer inde está
* Nat
    - Precisa ser adicionado quando um dispositivo está atrás de uma rede privada, pois o IP que vai no cabeçalho precisa ser um valido
* dtmfmode
    - Define o tom de toque, o mais usado é rfc2833, mas podemos colocar auto para que o Asterisk defina
* modelo - template [] (!)
    - As opções da configuração são sobrescritas pelas do template, porém senhas é sobreposta e codecs em allow e disallow são acrescentados

## Pares Iax
* Feito para ser usado através de NAT
    - Limita o número de portas utilizadas, sinalização de mensagem e audio usam a mesma porta
    - Facilita a passagem pelo firewall
    - pode economizar largura de banda em um link de rede muito usado
```
[general]
encryption = yes
forceencryption = yes -> tambem pode ser setado para forçar a encriptação

[iax-peer]
type=friend
host=IP
trunk=yes
secret=supersecret
context=iax-entrada
deny=0.0.0.0/0.0.0.0
permit=192.168.X.X/255.255.255.0
qualify=yes
```

## Carregar as configurações 
* module reload chan_sip.so ou chan_iax.so

## Segurança 

* Separar /var do resto do disco 
* Deixar /var com maior espaço 
* particionar somente /var 
* Usar comando ntpdate pool.ntp.org para sincronizar hora exata:
    - chkconfig ntpd on
    - service ntpd start
* Sempre instalar o DAHDI

## Dialplan basico

* Etiqueta de prioridade: extensão => 123,n(label),application()

* Answer -> Atende um canal que está tocando
* Progress -> Fornece informações de andamento da chamada para o canal de origem, algumas operadoras esperam por isso, você pode resolver problemas de sinalização estranhos inserindo Progress()
* Playback -> reproduz um som
* Hangup -> Desliga um canal ativo
* Goto -> Vai para outro contexto
* Background -> reproduz um som mas diferente do playback pula o audio quando pressionado uma tecla existente no dialplan e passa a chamada a extensão que corresponde ao digito
* WaitExten -> vem apos o background e espera por um digito para continuar, geralmente setamos um numero em segundos para a espera

## Dial
- disca para um canal sintaxe: Dial(argumento 1: destino) -> este pode ser um ou muitos

- Dial(destino,tempoLimite,opcao,URI)

**Argumentos invalidos podem ser colocados ficando assim: Dial(SIP/101,,m)

URI -> Caso o telefone do destino aceite URI voce pode enviar ele como parametro para aparecer na tela do telefone da pessoa
```
OPCOES:

m -> toca musica em espera ao inves do ring para o chamador
```

### SIP
* Dial(SIP/101&SIP/102&PJSIP/103) -> para ramais
* sintaxe: Dial(SIP/nomeTronco/numero) -> para tronco 
* Dial(SIP/Linha-cacofonix/${EXTEN})

### PJSIP
* sintaxe: Dial(PJSIP/nomeTronco/sip:${numero}@IP:porta)
* Dial(PJSIP/Linha-cacofonix/sip:${EXTEN}@45.226.254.203:5060)

## Variavel

- setando uma variavel: Set(Thaina=SIP/101)
- chamando uma variavel: exten => 1,1,Dial(${Thaina})

### Variavel global 

- Vista por todo o codigo
- devem ser decalaradas no contexto [globals] dessa forma: 
```
[globals]
THAINA=SIP/101
```

### Variavel de canal

- Definidas apenas na duração de um canal ativo
- Algumas podem ser vistas em: https://wiki.asterisk.org/wiki/display/AST/Asterisk+Standard+Channel+Variables
- Definidas no Set()

### Variaveis de ambiente

- Posso usar variaveis de ambiente do linux para o Asterisk ler 
- Chamando: ${ENV(var)} por exemplo

## Sintaxe codigo

- x -> 0 a 9
- z -> 1 a 9
- n -> 2 a 9
- [15-7] -> numero 1 depois 5,6 ou 7
- _. -> qualquer coisa com pelo menos um digito
- _x. -> qualquer numero de 0 a 9 depois qualquer coisa

```
exten => _NXX,1,Noop(qualquer numero que seja o primeiro digito de 2 a 9 e os outros dois de 0 a 9)
```
**Se o Asterisk encontrar mais de um padrão que corresponda ao ramal discado, ele usará o mais específico (da esquerda para a direita) ex.: exten => _555XXXX ou exten => _55512XX e voce discou 5551212 ele cai para a segunda opção**

- EXTEN -> pega o numero discado na entrada do canal
- exten => _XXX,1,SayDigits(${EXTEN:1}) -> fala somente o segundo e terceiro digito cortando o primeiro
- ex.; tenho o numero 94169671111
- ${EXTEN:1:3} -> corta o primeiro digito e conta a partir do segundo 3 digitos que serao mostrados nesse caso 416
- ${EXTEN:-4:4} -> pega a partir dos ultimos 4 os 4 seguintes 1111

### Chamada internacional

- ex.: _1NXXNXXXXXX -> refere o numero 1 que o codigo usado pela maioria das operadoras internacionais seguido do codigo de area que sao 3 digitos seguido do numero - este é qualquer numero de longa distancia inter

## Inclusao de contexto e arquivos 

- include => context -- inclui um contexto no outro
- #include Pasta/arquivo -- inclui um arquivo no outro

## Criptografia nas chamadas

- [general] - sip.comf
- tlsenable = yes
- tlsbindaddr = : :
- setar transport=tls no sip.conf

## Voicemail

- configurar em voicemail.conf
- no dialplan sintaxe de chamada: exten => voicemail(mailbox@context,options)
```
OPTIONS

Argumento	Propósito
b	Instrui o Asterisk a reproduzir a saudação de ocupado para a caixa postal (se nenhuma saudação de ocupado for encontrada, a saudação indisponível será reproduzida).
d([c])	Aceita dígitos a serem processados ​​por context c. Se o contexto não for especificado, o padrão será o contexto atual.
g(#)	Aplica a quantidade de ganho especificada (em decibéis) à gravação. Funciona apenas em canais DAHDI.
s	Suprime a reprodução de instruções para os chamadores após reproduzir a saudação.
u	Instrui o Asterisk a reproduzir a saudação indisponível para a caixa de correio (este é o comportamento padrão).
U	Indica que esta mensagem deve ser marcada como urgente. O efeito mais notável que isso tem é quando o correio de voz é armazenado em um servidor IMAP. Nesse caso, o e-mail será marcado como urgente. Quando o dono da caixa postal liga para o sistema de correio de voz Asterisk, ele também deve ser informado de que a mensagem é urgente.
P	Indica que esta mensagem deve ser marcada como prioritária.
```

### Voicemailmain

- exten => Voicemailmain(mailbox@context,options)
- As mensagens do correio de voz são armazenadas dentro de /var/spool/asterisk/voicemail

```
OPTIONS

Argumento	Propósito
p	Permite que você trate o parâmetro < mailbox > como um prefixo para o número da caixa postal.
g(#)	Aumenta o ganho em #decibéis ao reproduzir mensagens.
s	Ignora a verificação de senha.
a(folder)	Inicia a sessão em uma das seguintes pastas de correio de voz (o padrão é 0):0 - INBOX, 1 - Old, 2 - Work, 3 - Family, 4 - Friends, 5 - Cust1, 6 - Cust2, 7 - Cust3, 8 - Cust4, 9 - Cust5
```

## Dialplan avançado

- Declarar uma expressão - $[ expression] 
- ex.: $[${COUNT} + 1] para contar numero mais 1 ou $[${COUNT} / 2] numero dividido por 2
- Sempre usar Asnwer antes de SayNumber ou SayDigits

### OPERADORES

**BOOLEAN**
- ou = |  ex.: expr1|expr2 
- e = &   ex.: expr1 & expr2
- igual,maior que, maior ou igual,menor,menor ou igual, diferente = {=, >, >=, <, <=, !=} 

**Matematicos**
- expr1{+,-} expr2 mais ou menos
- expr1 {*, /, %} expr2 vezes dividido ou resto de alguma divisao

**Expressao regular**
- expr1:expr2
- A expressão regular é ancorada no início da string com um implícito ^

**Concatenar**
- exten => 124,1,Set(TEST=mundo)
- same => n,Set(NEWTEST=ola${TEST})

## Funcoes 

- sintaxe ${ FUNCTION_NAME( argument)}
- Uma função tambem pode chamar outra função ex.: - { FUNCTION_NAME(${ FUNCTION_NAME( argument)})}
- Listar funções do asterisk na CLI > core show functions

## GotoIf

- GotoIf( expression? destination1: destination2) - 1 verdadeiro 2 e falso
```
exten => 123,1,Set(COUNT=10) 
same => n(start),GotoIf($[${COUNT} > 0]?:goodbye) ; se contador maior que 0 segue se nao goodbye 
same => n,Answer()
same => n,SayNumber(${COUNT}) 
same => n,Set(COUNT=$[${COUNT} - 1]) 
same => n,Goto(start) 
same => n(goodbye),Hangup()
```

## GotoIfTime

- GotoIfTime( times, days_of_week, days_of_month, months? label)
- times = 09:00-17:00
- days_of-week = mon, tue, wed, thu, fri, sate/ou sun --- de segunda a sexta mon-fri --- segunda ou sexta mon&fri
- days_of_month = 1 até 31 --- de 7 a 12 7-12 --- dia 15 e dia 30 15&30
- months = jan-apr de janeiro a abril --- jan&mar&jun janeiro ou março ou junho
- ex.: exten => s,1,GotoIfTime(09:00-17:59,seg-sex,*,*?open,s,1)
das 9 as 17 59 segunda a sexta qualquer dia do mes e no ano vai para
- ex.: exten => s,1,GotoIfTime(09:00-17:59,seg-sex,*,*?open,s,1)

## Macros

- Posso definir um contexto de macro assim:
[macro-teste]
- chamar macro ex.: extensão => 101,1,Macro(teste)
- precisa começar com extensão s
- Retorna automaticamente apos a execução

### Variaves de macro
- ${MACRO_CONTEXT} = contexto que a macro foi chamada
- ${MACRO_EXTEN} = extensão que a macro foi chamada
- ${MACRO_PRIORITY} = prioridade que a macro foi chamada
- ${ARG n } = argumento passado para a macro seguindo a sequencia 1,2,3...

## Gosub

- Definir contexto de Gosub pode ser com qualquer nome porem é recomenavel colocar sub antes para diferenciar:
[subteste]
- funciona igual a macro porem a primeira extensão pode ser qualquer nome, diferente da macro que precisa do s
- precisa chamar Return() no dialplan para retornar apos a execução

## AstDB - Banco de dados do asterisk

- Funciona armazenando uma familia e uma chave
- ex.: exten => 456,1,Set(DB(teste/contagem)=1)
- Se uma chave nomeada countagem já existir na família teste, seu valor será substituído pelo novo valor.
- armazenar direto na CLI comando: database put family key value

### Excluindo dados do AstDB pelo Dialplan

- Excluir uma chave de uma familia usamos DB_DELETE
- exten => 457,1,Verbose(0, O valor era ${DB_DELETE(teste/contagem)})
- Excluir uma familia inteira
- extensão => 457,1,DBdeltree(teste)

### Estacionamento de chamada 

- Controlados em res_parking.conf
- As 4 primeiras configurações são referentes aos tipos de estacionamento de chamadas
- parkext => 700   ramal que segura a chamada pra onde discamos para segurar
- parkpos => 701-720 quantidade de posicoes para armazenamento nesse caso tem 20
- context => parkedcalls contexto para o estacionamento ser ativado
- parkingtime => 45 controla quanto tempo uma chamada pode permanecer guardada

### MeetMe()

- Permite que varias pessoas se comuniquem ao mesmo tempo
- meetme.conf

## Herdar variaveis de canal 

- Sublinhado simples (_) fica disponivel para uma unica transferencia 
- Sublinhado duplo (__) variavel sera herdada por todo o caminho do canal
- ex.: exten => XX,1,Set(_MyVariable=thisValue)
- para ler nao usa o sublinhado somente ${ MyVariable}

## Queues.conf

- persistentmembers	yes,no	Configure yes para armazenar membros adicionados dinamicamente às filas no AstDB para que possam ser adicionados novamente na reinicialização do Asterisk.

- autofill	yes,no	Com autofill no, o aplicativo de fila tentará entregar as chamadas aos agentes de maneira serial. Isso significa que apenas uma chamada tenta ser distribuída aos agentes por vez. Chamadores adicionais não são distribuídos aos agentes até que o chamador esteja conectado a um agente. Com autofill yes, os chamadores são distribuídos aos agentes disponíveis simultaneamente - ideal para callcenters

- monitor-type	MixMonitor, < unspecified>	Se você especificar o valor , MixMonitor o MixMonitor()aplicativo será usado para gravar chamadas na fila. Se você não especificar um valor ou comentar a opção, o Monitor()aplicativo será usado em seu lugar.

- updatecdr	yes,no	Configure yes para preencher o dst channel campo dos registros CDR com o nome de um membro adicionado dinamicamente na resposta. O valor é definido com o AddQueueMember()aplicativo. Esta opção é usada para imitar o comportamento dos chan_agent canais.

- shared_lastcall	yes,no	Esse valor é usado para membros conectados em mais de uma fila para que sua última chamada seja a mesma em todas as filas, para que as filas respeitem o tempo de finalização de outras filas.

- announce	Nome do arquivo do anúncio. Usado para reproduzir um anúncio para o agente que atendeu a chamada, normalmente para que ele saiba de qual fila o chamador está vindo. Útil quando o agente está em várias filas, especialmente quando configurado para atender automaticamente a fila.

- strategy:
```
ringall: toca todos os chamadores disponíveis (padrão)

leastrecent: toca a interface que menos recentemente recebeu uma chamada

fewestcalls: toca a interface que completou o menor número de chamadas nesta fila

random: toca uma interface aleatória

rrmemory: liga para os membros de forma round-robin, lembrando onde paramos por último para a próxima chamada

linear: toca os membros na ordem especificada, sempre começando no início da lista

wrandom: toca um membro aleatório, mas usa as penalidades dos membros como um peso.
```
- servicelevel	Valor em segundos	Usado em estatísticas para determinar o nível de serviço da fila (chamadas atendidas dentro do prazo do nível de serviço).

- context	Contexto do plano de discagem	Permite que um chamador saia da fila pressionando um único dígito DTMF. Se um contexto for especificado e o chamador inserir um número, esse dígito tentará ser correspondido no contexto especificado e a execução do plano de discagem continuará lá.

- penaltymemberslimit	Valor de 0ou maior	Usado para desconsiderar valores de penalidade se o número de membros na fila for menor que o valor especificado.

- timeout	Valor em segundos	Especifica o número de segundos para tocar no dispositivo de um membro. Veja também timeoutpriority.

- retry	Valor em segundos	Especifica o número de segundos de espera antes de tentar o próximo membro da fila se o timeoutvalor se esgotar ao tentar ligar para um membro da fila. 

- weight	Valor de 0ou superior	Define o peso de uma fila. Uma fila com um peso mais alto definido terá a primeira prioridade quando os membros estiverem associados a várias filas.

- wrapuptime	Valor em segundos	O número de segundos para manter um membro indisponível em uma fila após concluir uma chamada.

- autopause	yes, no,all	Habilita/desabilita a pausa automática de membros que não atendem uma chamada. Um valor de allfaz com que esse membro seja pausado em todas as filas das quais ele é membro.

- maxlen Valor de 0 ou superior	Especifica o número máximo de chamadores que podem esperar em uma fila. Um valor zero significa que um número ilimitado de chamadores é permitido na fila.

- setinterfacevar	yes,no	Se definido como yes, as seguintes variáveis ​​de canal serão definidas imediatamente antes de conectar o chamador ao membro da fila:
```
MEMBERINTERFACE: a interface do membro, comoAgent/1234

MEMBERNAME: o nome do membro

MEMBERCALLS: o número de chamadas que a interface recebeu

MEMBERLASTCALL: a última vez que o membro atendeu a uma chamada

MEMBERPENALTY: o valor da penalidade do membro

MEMBERDYNAMIC: indica se o membro foi adicionado dinamicamente à fila ou não

MEMBERREALTIME: indica se o membro está incluído em tempo real ou não
```
- setqueueentryvar	yes,no	Se definido como yes, as seguintes variáveis ​​de canal serão definidas imediatamente antes da chamada ser ponteada:
```
QEHOLDTIME: a quantidade de tempo que o chamador ficou na fila

QEORIGINALPOS: a posição em que o chamador entrou originalmente na fila
```

- setqueuevar	yes,no	Se definido como yes, as seguintes variáveis ​​de canal serão definidas imediatamente antes da chamada ser ponteada:
```
QUEUENAME: o nome da fila

QUEUEMAX: o número máximo de chamadas permitidas nesta fila

QUEUESTRATEGY: o método de estratégia definido para a fila

QUEUECALLS: o número de chamadas atualmente na fila

QUEUEHOLDTIME: o tempo médio atual de espera dos chamadores na fila

QUEUECOMPLETED: o número de chamadas concluídas nesta fila

QUEUEABANDONED: o número de chamadas abandonadas

QUEUESRVLEVEL: o nível de serviço da fila

QUEUESRVLEVELPERF: o desempenho do nível de serviço da fila
```
- announce-frequency	Valor em segundos	Define com que frequência devemos anunciar a posição do chamador e/ou tempo estimado de espera na fila. Defina esse valor como zero para desabilitar.

- min-announce-frequency	Valor em segundos	Especifica o tempo mínimo que deve passar antes de anunciarmos novamente a posição do chamador na fila. Isso é usado quando a posição do chamador pode mudar com frequência, para evitar que o chamador ouça várias atualizações em um curto período de tempo.

- periodic-announce-frequency	Valor em segundos	Indica com que frequência devemos fazer anúncios periódicos ao chamador.

- announce-holdtime	yes, no,once	Define se o tempo de espera estimado deve ser reproduzido junto com os anúncios periódicos. Pode ser definido como yes, no, ou apenas once.

- announce-position	yes, no, limit,more	Define se a posição do chamador na fila deve ser anunciada a ele. Se definido como no, a posição nunca será anunciada. Se definido como yes, a posição do chamador sempre será anunciada. Se o valor for definido como limit, o chamador ouvirá sua posição na fila somente se estiver dentro do limite definido por announce-position-limit. Se o valor for definido como more, o chamador ouvirá sua posição se estiver além do número definido por announce-position-limit.

- announce-position-limit	Número de zero ou maior	Usado se você definiu announce-position como limit ou more

- monitor-format	gsm, wav, wav49, < any valid file format>	Especifica o formato de arquivo a ser usado durante a gravação. Se monitor-formatfor comentado, as chamadas não serão gravada

- joinempty	paused, penalty, inuse, ringing, unavailable, invalid, unknown,wrapup	Controla se um chamador é adicionado à fila quando não há membros disponíveis. Opções separadas por vírgula podem ser incluídas para definir como essa opção determina se os membros estão disponíveis. As definições para os valores são:
```
paused: os membros são considerados indisponíveis se estiverem em pausa.

penalty: os membros são considerados indisponíveis se suas penalidades forem inferiores a QUEUE_MAX_PENALTY.

inuse: os membros são considerados indisponíveis se o dispositivo statusestiver em uso.

ringing: os membros são considerados indisponíveis se o status do dispositivo for Ringing.

unavailable: aplica-se principalmente aos canais de agente; se o agente não estiver conectado, mas for membro da fila, ele será considerado indisponível.

invalid: os membros são considerados indisponíveis se o status do dispositivo for Invalid. Isso geralmente é uma condição de erro.

unknown: os membros são considerados indisponíveis se o status do dispositivo for desconhecido.

wrapup: os membros são considerados indisponíveis se estiverem no tempo de encerramento após a conclusão de uma chamada.
```
- leavewhenempty	paused, penalty, inuse, ringing, unavailable, invalid, unknown,wrapup	Usado para controlar se os chamadores são expulsos da fila quando os membros não estão mais disponíveis para receber chamadas. Consulte para obter mais informações sobre os valoresjoinempty atribuíveis .

- eventwhencalled	yes, no,vars	Se definido como yes, os seguintes eventos do gerenciador serão enviados para a Interface do Gerenciador do Asterisk (AMI):
```
AgentCalled

AgentDump

AgentConnect

AgentComplete

Se definido como vars, todas as variáveis ​​de canal associadas ao agente também serão enviadas à AMI.
```
- eventmemberstatus	yes,no	Se definido como yes, o QueueMemberStatusevento será enviado para a AMI. Observe que isso pode gerar muitos eventos de gerente.

- ringinuse	yes,no	Usado para evitar o envio de chamadas para membros cujo status seja In Use. Lembre-se de nossa discussão na seção anterior de que apenas o driver de canal SIP atualmente é capaz de relatar com precisão esse status.

## Adicionando membros a fila na CLI

- queue add member SIP/member to fila
- queue remove member SIP/member from fila
- queue pause member SIP/member queue fila reason razao
- queue unpause member SIP/member queue fila reason razao

### Apps de plano de discagem para login e logoff dinamicamente

- AddQueueMember()
- RemoveQueueMember()
- PauseQueueMember()
- UnpauseQueueMember()

### Pertencendo a mais de uma fila

- database put queue_agent member/available_queues fila^fila

### Status de disponível e indisponivel

- Setar callcounter=yes no sip.conf
- Isso informa ao asterisk que quando um dispositivo está em chamada ele deve ficar como In use 

### Penalty 

- Esses agentes não atenderão nenhuma chamada da fila de prioridade mais baixa até que a fila de prioridade mais alta seja limpa

- Pode ser definida estaticamente em queues.conf - => SIP/membro,0,membro James Shaw prioridade maior
- Ou dinamicamente passando como parametro em AddQueueMember()

- É possivel alterar a penalidade durante as chamadas configurando em queuerules.conf ex.:
```
[more_members] ; cria um contexto
penaltychange => 60,5,1 ;apos 60 segundos alterar a penalidade para maxima 5 e minima 1
Sempre carregar na CLI - module reload app_queue.so
```

- O comando queue show rules mostra as definições 

### Tipos de prioridade 

- Priorizar cliente na fila QUEUE_PRIO
- Priorizar agente Penalty
- Priorizar chamadas nas filas Weight

#### Peso da fila 

- 0 - > Padrao
- 1 -> Vai ser mais prioritario

### Peso do agente

- 30 -> Padrao
- 40 -> Baixo
- Alto -> 20
- Muito Alto -> 10


### Joinempty e leavewhenempty

 
| Value| (joinempty)| (leavewhenempty) |
|------|-------------|-----------------|
| yes|(empty)|penalty,paused,invalid
| no |penalty,paused,invalid|(empty)
|strict| penalty,paused,invalid,unavailable| penalty,paused,invalid,unavailable
|loose|	penalty,invalid	|penalty,invalid

## queue_log File

- localizado em /var/log/asterisk/queue_log contém informações sobre as filas 
- É ativado por padrao mas pode ser verificado em logger.conf

- Eventos do log Asterisk:

```
Evento	Informação fornecida
ABANDON	Escrito quando um chamador em uma fila desliga antes de sua chamada ser atendida por um agente. Três parâmetros são fornecidos ABANDON: a posição do chamador ao desligar, a posição original do chamador ao entrar na fila e a quantidade de tempo que o chamador esperou antes de desligar.

ADDMEMBER	Escrito quando um membro é adicionado à fila. O nome do canal em ponte será preenchido com o nome do canal adicionado à fila.

AGENTDUMP	Indica que o agente desligou no chamador enquanto o anúncio da fila estava sendo reproduzido, antes de serem ligados em ponte.

AGENTLOGIN	Gravado quando um agente faz login. O campo do canal em ponte conterá algo como Agent/9994se estiver fazendo login com chan_agent, e o primeiro campo de parâmetro conterá o canal de login (por exemplo, SIP/0000FFFF0001).

AGENTLOGOFF	Registrado quando um agente faz logoff, juntamente com um parâmetro que indica por quanto tempo o agente esteve conectado.

COMPLETEAGENT	Gravado quando uma chamada é transferida para um agente e o agente desliga, juntamente com parâmetros que indicam a quantidade de tempo que o chamador ficou na fila, a duração da chamada com o agente e a posição original em que o chamador entrou no fila.

COMPLETECALLER	O mesmo que COMPLETEAGENT, exceto que o chamador desligou e não o agente.

CONFIGRELOAD	Indica que a configuração da fila foi recarregada (por exemplo, por meio do módulo reload app_queue.so ).

CONNECT	Escrito quando o chamador e o agente estão em ponte. Três parâmetros também são gravados: a quantidade de tempo que o chamador esperou na fila, o ID exclusivo do canal do membro da fila para o qual o chamador foi conectado e a quantidade de tempo que o telefone do membro da fila tocou antes de ser atendido.

ENTERQUEUE	Escrito quando um chamador entra na fila. Dois parâmetros também são gravados: o URL (se especificado) e o identificador de chamadas do chamador.

EXITEMPTY	Escrito quando o chamador é removido da fila por falta de agentes disponíveis para atender a chamada (conforme especificado pelo leavewhenemptyparâmetro). Três parâmetros também são escritos: a posição do chamador na fila, a posição original em que o chamador entrou na fila e a quantidade de tempo que o chamador ficou na fila.

EXITWITHKEY	Escrito quando o chamador sai da fila pressionando uma única tecla DTMF em seu telefone para sair da fila e continuar no plano de discagem (conforme habilitado pelo contextparâmetro em queues.conf). Quatro parâmetros são registrados: a chave usada para sair da fila, a posição do chamador na fila ao sair, a posição original em que o chamador entrou na fila e o tempo que o chamador esperou na fila.

EXITWITHTIMEOUT	Escrito quando o chamador é removido da fila devido ao tempo limite (conforme especificado pelo timeoutparâmetro to Queue()). Três parâmetros também são registrados: a posição em que o chamador estava ao sair da fila, a posição original do chamador ao entrar na fila e a quantidade de tempo que o chamador esperou na fila.

PAUSE	Escrito quando um membro da fila é pausado.
PAUSEALL	Escrito quando todos os membros de uma fila estão em pausa.
UNPAUSE	Escrito quando um membro da fila não está pausado.
UNPAUSEALL	Escrito quando todos os membros de uma fila não estão pausados.

PENALTY	Escrito quando a penalidade de um membro é modificada. A penalidade pode ser alterada através de vários meios, como a QUEUE_MEMBER_PENALTY() função, usando a Interface do Gerenciador do Asterisk, ou os comandos da CLI do Asterisk.

REMOVEMEMBER	Escrito quando um membro da fila é removido da fila. O campo do canal da ponte conterá o nome do membro removido da fila.

RINGNOANSWER	Registrado quando um membro da fila é tocado por um período de tempo e o valor de tempo limite para tocar o membro da fila é excedido. Um único parâmetro também será escrito indicando a quantidade de tempo que o ramal do membro tocou.

TRANSFER	Escrito quando um chamador é transferido para outro ramal. Parâmetros adicionais também são escritos, que incluem: o ramal e o contexto para o qual o chamador foi transferido, o tempo de espera do chamador na fila, a quantidade de tempo que o chamador estava falando com um membro da fila e a posição original do chamador. chamador quando ele entrou na fila. [ a ]

SYSCOMPAT	Gravado se um agente tentar atender uma chamada, mas a chamada não puder ser configurada devido a incompatibilidades na configuração da mídia.
```

## Estado de dispositivo 

- Função DEVICE_STATE() mostra o estado do dispositivo - DEVICE_STATE(SIP/ramal)

- Tipos de valores retornáveis:
```
UNKNOWN

NOT_INUSE

INUSE

BUSY

INVALID

UNAVAILABLE

RINGING

RINGINUSE

ONHOLD
```

- É possivel colocar uma dica na extensão para verificar o estado de u dispositivo também:

```
[internal]

exten => 1234,hint,SIP/ramal

Após isso digitando - core show hint 1234 - será impresso o estado atual do dispositivo na CLI
```

- A função ${EXTENSION_STATE(1234@internal)} também pode ser usada para ver o estado do dispositivo a partir da extensão 

## Conectar asterisk com banco de dados ODBC

- Baixar driver do banco de dados odbc
- Realizar o apontamento em odbc.ini
- Realizar o apontamento em res_odbc.conf

- func_odbc.conf -> Onde fica toda a configuração para gerir funções que consultam banco de dados e trazem um resultado para ser usado no dialplan, ex.:

```
[LISTANEGRA] ; nome da função nesse caso BD_LISTANEGRA
prefix=BD ; se definido é usado no lugar de ODBC quando chama a função
dsn=asterisk ; dns definido em res_odbc.conf
;readsql=SELECT ${ARG1} FROM blacklist WHERE number='${ARG2}'
readsql=SELECT ${ARG1} FROM blacklist WHERE ${ARG1}='${SQL_ESC(${ARG2})}'

```

- As funções para se usar no func_odbc e manipular o banco são: readhandle, writehandle, readsql e writesql

- readsql serve quando queremos recuperar uma informação do banco para o dialplan

```
Set(USERS_LOGGED_IN=${HOTDESK_CHECK_PHONE_LOGINS(${LOCATION})})

Passa o ARG1 como a LOCATION
```

- writesql quando queremos inserir uma informação no banco de dados

```
Set(HOTDESK_STATUS(${E})=1,${LOCATION})

Estamos passando o valor da ${E}variável para a HOTDESK_STATUS()função, cujo valor é então acessível na instrução SQL dentro func_odbc.confda ${ARG1}variável. Passamos então dois valores: 1e ${LOCATION}. Eles estão disponíveis para a instrução SQL nas variáveis ${VAL1}e , respectivamente.${VAL2}
```

- Posso usar o array para armazenar varios valores exemplo tendo essa relação:

```
exten => _110[1-5],n,Set(${E}_STATUS=${HOTDESK_INFO(status,${E})})
   same => n,Set(${E}_PIN=${HOTDESK_INFO(pin,${E})})

Poderia recolher em uma só com:

exten => _110[1-5],n,Set(ARRAY(${E}_PIN,${E}_STATUS)=${HOTDESK_INFO(${E})})
```

- ${ODBCROWS} - É uma variavel de canal que retorna quantas linhas foram bem sucedidas em uma função de UPDATE 

## ARA Asterisk em Real Time

- Armazenar arquivos de configuração de /etc/asterisk em uma tabela do banco de dados

- configurado em extconfig.conf


