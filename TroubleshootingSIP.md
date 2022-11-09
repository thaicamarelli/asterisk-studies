## Methods SIP

- 1xx -> Informations
- 2xx -> Sucess
- 3xx -> Redirect
- 4xx -> Client Error
- 5xx -> Server Error
- 6xx -> Global Error

## Register 

- É usado quando um dispositivo quer alertar aonde está para outro dispositivos
- Dois dispoditivos podem se registrar com contatos diferentes
- O dispositivo não está autorizado a fazer chamadas somente mostra onde está
- Reduzir o tempo de registro pode causar problemas (o ideal é deixar um maior intervalo entra os pacotes de registro)

```
REGISTER ->
<- 401 Unauthorized
REGISTER With Header -> 
<- 200 OK
```

- O Ip do cliente é encontrado no campo Contact
- O To mostra qual o extension que desejo registrar e qual o dominio do meu servidor 

## SIP HEADER UAC -> Client

- Precisa conter:
    - To
    - From
    - Cseq 
    - Call-ID
    - Max-Forwards
    - VIA

## SIP Request

    METODO sip:pra onde vai o pacote SIP/versão
    Via: indica a localização pra onde a resposta deve ser enviada (respostas)
    Max-Forwards: Numero maximo de saltos até o destino
    Contact: Localização daquele instãncia específica (novas requisições)
    To: Destino da mensagem (usado para registro)
    From: Caller ID quem esta mandando a mensagem
    Call-ID: Identificador Unico da mensagem ID
    CSeq: numero da mensagem 1,2,3 pode ser diferente para o chamador e o chamado
    User-Agent: Mostra o dispositivo do cliente

## SIP HEADERS CUSTOMIZATION

- Podemos alterar o tamanho do header a partir do server ou do client
- Toda messagem SIP carrega um body que é definido no campo Content-Type
- REGISTER não tem um body

## SDP BODY

    v=0
    o=
    s=SIP CALL
    c=
    t0.0
    ...

- Também pode ter body em formato dialog-info ou xml

## Transaction States - Status de transação 

- Calling -> Cliente iniciou uma nova chamada com transação 
- Trying -> Transações que não são INVITES
- Proceeding -> Cliente ou server manda ou recebe uma nova resposta provisoria
- Completed -> Cliente ou servidor recebe uma resposta final 3XX 
- Confirmed -> Cliente ou servidor recebe envia um ACK
- Terminated -> Depois de receber um ACK

### Tempo de completamento de um INVITE

- Após o INVITE o e o 100 trying o cliente tem até 32 segundos para completar a chamada

- Em uma primeira requisição não existe o To tag, apenas em uma solicitação de requisição sequencial
- Algumas plataformas podem apresentar problemas na To tag para transações 

### ACK

- Para respostas positivas como 200OK o ok é uma nova transação 
- Para respostar negativas o ACK é uma mesma transação 

## Codecs 

- Novo codec OPUS pode competir com G722 com apenas 64 de banda e tem grande resistência a pacotes perdidos
- Payload -> pacotes ou cargas enviados pela internet 
- Uma forma de diminuir a largura de banda ou seja o tamanho do frame é incrementado o payload para 60 ms por exemplo assim usando somente 16Kb e não 31Kb
- G711 é o mais usado 

### SDP

- Enviar um INVITE com SDP já passa qual codec será usado
- Quando é recebido quem retonrou é que escolhe o codec
- rtpmap : 0 PCMU -> é o codec escolhido vem na linha de Media Description

### Campos SDP

- m = audio <porta>
- a = codec
- c = IP onde vamos conectar

## RTP

- O RTP é reponsável pela empocatomento da midia, voz, video e DTMF
- Real-Time Transport Control Protocol : Jitter, Delay e CNAME
- Os campos acima são vistos em Interarrival jiter, delay since last SR e Type: CNAME
- Com isso podemos ver pacotes perdidos e qualidade da chamada

## Qualidade da chamada

- Jitter abaixo de 20ms 
- Packet loss = 0
- Latency abaixo de 150ms
- RTCP -XR ou Packet Capture or RTCP Feedback podem ser usados para ter um retorno da qualidade da ligação 
- No caso do RTCP-XR apos finalizar a ligação o SIP manda um PUBLISH com as informações de RTP
- Alguns softphones e hardphones tem opções neles mesmo que retornam as informações RTP

## NAT

- O nat pode afetar o SIP em: VIA HEADER, CONTACT HEADER, SDP BODY
- Esses campos trazem o IP do usuário e devido o nat fazer a troca pode apresentar problemas
- Qual solução para o NAT? Depende do tipo

### Tipos de NAT

#### Cone

- Full Cone estático -> É quando setamos manualmente a porta no roteador, dessa forma teremos uma mapeamento estatico entre o IP interno e o externo

- Restricted Cone dinâmico -> É um nat dinamico, restringe somente por IP aqui o computador por tras do nat precisa começar a comunicação para poder receber as informações de volta

- Port Restricted Cone -> É um nat dinamico, porem restringe a porta e o IP, somente maquinas de especifico IP e porta podem retornar informações para a maquina 

- Todos os tipo cone usam um mesmo mapeamento IP para saber o IP externo

#### Symetric NAT

- Parecido com Port Restricted Cone - É dinâmico mas tem um mapeamento para cada destino, a cada solicitação de uma maquina será gerado um novo mapeamento (porta) para cada comunicação 
- É o tipo de NAT mais dificil de trabalhar 

Tipo | Dinamico | Define porta | Restringe IP e porta
-----|-------| ----- | -----|
Full Cone| NO | YES | NO
Restricted Cone| YES | YES | Only IP
Port Restricted Cone | YES | YES - same | YES
Symmetric | YES | NO | YES 

## Configurar ExternIP para NAT

- sip.conf ou pjsip.conf
    - externip = ip externo do servidor
    - localnet = rede local(se estiver na rede local usa esse ip senao usar externo)

- nat=no -> quando mando um register para o servidor ele indica um campo rpot no VIA HEADER e mesmo como no ele identifica o IP externo e registra mas somente se no softphone tiver a opção de rport automatica habilitada(apesar de no VIA aparecer o IP interno do servidor ) caso contrario não é possivel registrar
- nat=force_rport -> força o NAT a fazer a leitura do IP mesmo que o softphone não tenha habilitado

## Application Layer Gateway - ALG

- Possui no roteador da operadora e NAT no roteador
- Ele ja faz a conversão de NAT para o SIP mesmo sem o rport habilitado

### COMEDIA

- nat=force_rport,comedia
- O RTP é enviado do server para a outra ponta antes mesmo de o usuario inicial enviar o primeiro RTP

## STUN

- Descobre se o usuário está atrás de NAT
- Qual é o IP externo do usuário
- Resolve o NAT do cliente pelo softphone
- Se quiser implementar ver a documentação do RFC
- Não resolve em caso de Symetric NAT porque o STUN resolve somente a primeira vez a porta, no segundo mapeamento ele perde a referencia

## CONCLUSAO NAT

- Para servidores habilitar nat=force_rport,comedia e Symmetric Response Routing(RFC3581)
- para SIP proxies (KAMALIO ou outros ) usar STUN para os clientes e se detactar o hard NAT(Symmetric NAT) e usar o TURN
- ALG e UPNP -> desabilitar ou usar uma porta diferente

## Principais problemas
```
Não consigo fazer chamada
- Verificar o ping e o registro com o servidor 
- Verificar reposta do servidor 
- 401 ou 407 Unauthorized REGISTER e INVITE (tirando a primeira vez que é normal) erro de senha
- 403 Forbidden -> as vezes from domain incorreto ou request URI incorreto, checagem de dominio 
``` 
```
Ligações desconectadas em 30s
- Apos o INVITE o ACK não é enviado ou seja não tem RTP
- Contact header errado
- Gateway atras de NAT
- Cliente nao suporta header route
- endereço de contato atras de NAT 
```

```
Ligações presa
- BYE não enviado
- BYE sendo enviado com to-tag errada
- Para resolver -> habilitar RTP Timeout
- Habilitar SIP keepalive
```

```
Só um lado tem audio ou nenhum audio
- Problema de NAT ou firewall está bloqueando as portas UDP
- Testar ping ente os lados 
- Verificar se o endereço não está atras de NAT
- Em alguns casos o ALG pode interferir, nesse caso alterar a porta padrão do UDP resolve o problema
- Verificar no INVITE e no 200OK se os endereços IP do SDP são os mesmos, caso a porta ou o IP sejam diferentes(Um IP interno e outro IP externo) com certeza ai está o problema de NAT
- Checar se no firewall está habilitado o ALG e desabilitar 
```
```
Erro 400 Bad Request
- parametro duplicado no request
Erro 413 ou 513 Message Too Large 
- mensagens com muitas ofertas de codec
- muitos VIA Headers 
- Muitos Route-Headers 
- Para resolver diminuir o número de codecs ofertados configurando o cliente que está enviando
- 
```

```
Erros 408 480 e 487
- Se for enviada uma chamada(100 trying) e o gateway não existir, o proxy vai gerar o erro 408 dizendo que não consegue contatar o outro lado
- Mas se for enviado um INVITE e o telefone tocar por um tempo e ninguem responder se chegar no tempo limite do servidor será enviado um 408, um CANCEL será enviado e o cliente responde com 487
- Se chegar no tempo limite do cliente será enviado somente um 480
```
```
Erro 483 Too Many Hopes
- Não configurar o servidor com um servidor DNS 
- O servidor envia o request para o DNS server e ele percebe que é ele mesmo e fica em loop
- Checar se foi configurado direito o DNS server
```
```
Erro 488 Not Acceptable Here 
- Associado ao codec em negociação quando um lado não aceita
```

## Problemas com audio nas ligações - Internet

- Perda de pacote -> voz robotica 
- Jitter -> voz distorcida
- Latência -> Colisão de pacotes

### Echo
- Echo sempre é gerado do lado oposto de quem está falando e não de quem está ouvindo
- Usar um cancelador de ruidos
- Usar um handset e não o alto falante

### Problemas de faturamento - cobrança

- Falha na internet ou firewall -> O provedor manda o 200OK ex.: Para finalizar 1000 chamadas e devido o corte da internet somente o lado do provedor tem a finalização, o 200OK não chega no cliente e pode ter divergência entre os dois lados
- Relógio desincronizado
- Ligações presas por muitas horas

## Redundância e Load Balancing usando DNS
- Ver RFC3263
- Usar o DNS para descobrir o Tranporte, IP e Porta
- Descobrir os servidores e a ordem dos servidores
- Se o NAPTR não for encontrado, fica para o dispositivo cliente definir o transporte
- Se o SRV não for encontrado, usar TCP para SIPS e UDP para SIP
- Todas as retransmissões ACKs e CANCEL devem ser enviados para o mesmo host

- Se existir dois dominios caso o primeiro server DNS cair o telefone so vai passar a se registrar com o segundo server, o primeiro so funciona se ele for reiniciado ou se for um novo registro 

- A redundância funcionar depende do cliente então é preciso sempre testar primeiro

- Quando um softphone por exemplo se registra com um servidor através do DNS esse pode ter problemas pra fazer o load balancing pois se registrar com um não vai mais encontrar o outro, funciona em ligação com INVITE porem não com registro 

## Mensagens SIP 

- 100 -> Call Progress
- 180 -> Ring falso não tem midia 
- 183 -> Session in Progress - Ring com SDP estabeleceu uma conexão RTP com o outro lado 

- 300 -> redirecionamentos 
- 400 -> Geralmente são erros de cliente mas pode ser falha no servidor (Caso apareça um específico pesquisar mensagens SIP )
- 500 -> Erros de servidor
- 600 -> Erros globais

