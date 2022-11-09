## Instalação 

- make -> recompila asterisk
- make install -> instala o asterisk
- make samples -> fica as informações iniciais nos arquivos ou seja os tutoriais 
- make config -> para permitir o asterisk inicializar com o sistema

## Estrutura de diretórios 

- /etc/asterisk : principais arquivos
- /usr/lib/asterisk: modulos do asterisk -> instalar codecs 
- /var/log/asterisk: logs do asterisk, mensagens da console do asterisk, queue log e log do cdr em csv, full registra tudo da console
- /var/lib/asterisk: AGIs e sounds
- /var/spool/asterisk: monitor ->  fica armazenado as gravações de chamada, outgoing -> tudo que e jogado aqui o asterisk executa, voicemails, recording -> gravação dinamica para URA por exemplo

## CLI

- rasterisk -> roda asterisk com o processo em background
- asterisk -vvvvvcf -> roda asterisk no momento da execução e para apos sair da console
- module reload -> recarrega todos os modulos
- core show channels -> canais ativos no momento
- core show uptime -> quando tempo asterisk esta funcionando 
- core set verbose 10 -> informações na CLI

## SIP.conf

- useragent -> nome que aparece quando registrar minha conta 

### Ramal
- type user - so recebe chamadas
- type peer - so faz chamadas
- type friend - faz e recebe chamada
- host=dynamic - nao sei de qual IP vai registrar ou seja a pessoa pode se registrar de qualquer IP
- qualify=yes - fica medindo a latencia entre o seu ramal e o servidor
- language=pt_BR - por padrao o asterisk vai buscar o audios na pasta setada aqui no ramal
- deny/permit - nega o registro desse ramal por certa rede e permite por outra

### Troncos

- Tronco registado por IP -> nao possui autenticacao de usuario e senha
- Tronco registrado por autenticacao -> precisa de usuario e senha
- usar type=peer 
- register - usado quando vou receber chamada por aquele tronco e nao e por IP

## Dialplan

- _ -> usado quando o numero nao é exato ex.; _100X,
- [1234-9] -> 1 ou 2 ou 3 ou 4 ate o 9
- . = -> um ou mais caracteres
- EXTEN -> valor da extensão

### Variaveis 

- Variavel global -> todos os canais podem obter o valor o setar o valor - setada no contexto globals setada (GLOBAL(nomedavariavel)=valor)
- Variaveis comuns -> setadas no dialplan que so funcionam durante aquela canal ativo setada com Set(nomevariavel=valor)

### Condicionais

- GotoIf -> se verdadeiro vai para um local se for falso para outro, tambem pode ter somente o verdadeiro e o falso segue o dialplan ex.: GotoIf($["${VARIAVEL}" = "VALOR"]?sim:nao)

- GotoIfTime -> verifica data se verdadeiro vai para um local se for falso vai para outro ex.: <time><days of week><days of month><months>?labeltrue:labelfalse

### Macros

- Para chamar no dialplan : Macro(nomedamacro,${argumento})
- Contexto da macro: [macro-nomedamacro]
- ARG1 = primeiro argumento passado para a macro

## Asteriks em realtime 

- É uma forma de conectar módulos do Asterisk ao banco de dados com objetivo de ler as configurações em tempo real
- Se conecta por ODBC Mysql ou Postgre
- Pode ter um ou mais modulos em realtime
- Pode ser usado hibrido respeitando a configuração para que nao sobrescreva

## Segurança

- Cuidado ao setar algum usuário que compartilha permissões com outras aplicações(exemplo usuário do Apache ou do banco)
- Usar usuário exclusivo para asterisk
- permitir apenas acesso deste usuário a pasta do Asterisk
- Nunca utilizar senhas simples para ramal
- Colocar fail2ban no ssh
- Se tiver um IP frequente tetando invadir bloquear o bloco de rede em todos os servidores e no firewall 

## AMI e ARI

- Travar Ips que terão acesso se estiver em IP publico o server ou se os Ips forem dinâmicos trocar as portas default e colocar um firewall como fail2ban
- Não deixar nomes de usuarios padrao ex.: root,asterisk,ampuser
- Dar permissão apenas que o usuário realmente necessite

## PJSIP.conf

- transport -> qual tipo de transport sip vai ser utilizado, UDP,TCP
- endpoint -> equivale ao PEER do chan SIP, o Dial vai para o endpoint
- auth -> bloco de autenticação usuario e senha aponta para o endpoint
- aor -> sao as configuracoes de registro do endpoint (qual numero maximo de contato que eu permito)
- registration -> para registrar em um provedor ou SIP proxy
- identify -> identifica o pacote de entrada para qual endpoint irá, caso de nao tiver vai usar o from do cabeçalho SIP
- domain alias -> permite criar um apelido para um dominio, para caso nao encontra um aor verifica se existe um alias para o dominio
- ACLs -> permit e deny do Chan SIP, quais Ips podem ou nao utilizar aquele endpoint
- contact -> derivam do aor e sao todos os objetos de endereço de registro

### Relacionamento

- endpoint - N Aors - 1 auth - 1 transport - 1 identify

- registration - 1 auth - 1 transport

- aor - N endpoints - N contacts

- contact - N aors

- indentify - 1 endpoint 

## AGI

- Validar se na CLi nao aparece UNKNOWN ou returning 0 que indica que tem algum erro no codigo
- Sempre colocar com permissao de execução 

