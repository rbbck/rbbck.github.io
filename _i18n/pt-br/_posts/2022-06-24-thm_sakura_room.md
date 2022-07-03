---
layout: post
title: "THM Sakura Room"
category: writeup
lang: pt-br
ref: sakura_room-thm
---

![](/assets/Pasted image 20220627235902.png)

# Primeiros passos
Esta room é pensada para testar uma variedade de técnicas de **OSINT**[^1], vamos experienciar um exemplo de **investigação** que visa **achar alguns identificadores** e outros **pedaços de informações** afim de capturar um cibercriminoso.

>NOTE: All answers can be obtained via passive OSINT techniques, DO NOT attempt any active techniques such as reaching out to account owners, password resets, etc to solve these challenges.

Também somos avisados de que nenhum **OSINT ativo** será necessário. Veja a seguir **alguns** exemplos para distingui-los:

| OSINT Passivo                                          | OSINT Ativo                                                 |
| ------------------------------------------------------ | ----------------------------------------------------------- |
| Não faz contato direto                                 | Pode ocorrer contato entre os lados                         |
| Scans passivos, mais oculto                            | Postura mais agressiva e intrusiva, maior risco de detecção                                     |
| Juntar informações disponíveis hospedadas em terceiros | Engenharia social, ex: enviar um link malicioso para vítima |

---

# Primeira flag
>What username does the attacker go by?

Nos é pedido o **nome de usuário** do criminoso. Além disso, é fornecido um panorama:
>O [OSINT Dojo](https://www.osintdojo.com) foi vítima de um ciberataque e durante análises forenses, foi encontrada uma imagem deixada para trás pelo autor do crime. Quem sabe ela contém alguma pista que nos permitiria apontar uma direção?

![](/assets/Pasted image 20220628011354.png)

Com isso temos nosso ponto de partida. Em uma imagem podem haver inúmeras informações escondidas, seja porque [alguém escondeu](https://www.gta.ufrj.br/ensino/eel878/redes1-2016-1/16_1/esteganografia/)  lá, ou, como no nosso caso, porque alguém **esqueceu de checar o próprio rastro**. O ***Exchangeable image file format*** ou simplesmente **EXIF** é um **padrão para armazenar informações na fotografia digital**. Resumidamente, é um conjunto de informações sobre (e contidas em) uma imagem, tais como sua **resolução**, **data de modificação**, **fabricante da câmera**, etc. Em alguns casos, pode conter até mesmo dados de **geolocalização**. 

Iremos utilizar a ferramenta [ExifTool](https://exiftool.org/), que serve para ler, gravar e editar **metadados**.

```
exiftool sakurapwnedletter.svg
ExifTool Version Number         : 12.42  
File Name                       : sakurapwnedletter.svg  
Directory                       : .  
File Size                       : 850 kB  
File Modification Date/Time     : 2022:06:28 01:28:26-03:00  
File Access Date/Time           : 2022:06:28 01:28:26-03:00  
File Inode Change Date/Time     : 2022:06:28 01:28:26-03:00  
File Permissions                : -rw-r--r--  
File Type                       : SVG  
File Type Extension             : svg  
MIME Type                       : image/svg+xml  
Xmlns                           : http://www.w3.org/2000/svg  
Image Width                     : 116.29175mm  
Image Height                    : 174.61578mm  
View Box                        : 0 0 116.29175 174.61578  
SVG Version                     : 1.1  
ID                              : svg8  
Version                         : 0.92.5 (2060ec1f9f, 2020-04-08)  
Docname                         : pwnedletter.svg  
Export-filename                 : /home/SakuraSnowAngelAiko/Desktop/pwnedletter.png  
Export-xdpi                     : 96  
Export-ydpi                     : 96  
Metadata ID                     : metadata5  
Work Format                     : image/svg+xml  
Work Type                       : http://purl.org/dc/dcmitype/StillImage  
Work Title                      :
```

Temos inúmeras saídas aqui, mas vamos focar em uma específica. **Export-filename** possui como valor o caminho do arquivo na hora de sua exportação: 
```
Export-filename                 : /home/SakuraSnowAngelAiko/Desktop/pwnedletter.png
```
E com isso descobrimos o nome de usuário do nosso atacante. 

---

# Segunda flag
>What is the full email address used by the attacker?

Para o **email do atacante**, tanto é válido utilizar uma ferramenta automatizada quanto ir diretamente nos sites e redes sociais mais conhecidos e inserir o nome de usuário que encontramos antes, ou seja, uma **abordagem manual**.

Uma **ótima ferramenta** é o [Sherlock](https://github.com/sherlock-project/sherlock), escrita em **Python**, que fornecido um nome de usuário, o software irá atrás dele por diversos locais na internet. 

Exemplo de utilização:

```
python3 sherlock.py --verbose SakuraSnowAngelAiko

[*] Checking username SakuraSnowAngelAiko on:  
  
  
[+] [5058ms] GitHub: https://www.github.com/SakuraSnowAngelAiko  
[+] [11563ms] Reddit: https://www.reddit.com/user/SakuraSnowAngelAiko
[...]
```

Uma das saídas é o **GitHub** dele, o que nos é muitíssimo interessante:

![](/assets/Pasted image 20220628232728.png)

Estes são os **repositórios fixados** do nosso atacante:

![](/assets/Pasted image 20220628233553.png)

Um destaque especial para o repositório **PGP**[^2], onde o invasor compartilha sua **chave pública**[^3].

![](/assets/Pasted image 20220629014758.png)

Se baixarmos a chave, rodarmos o [gnu](https://gnupg.org/) **sem fornecer nenhum parâmetro**, passando a **publickey que baixamos**, o programa vai "**printar**" a chave: 

	wget https://github.com/sakurasnowangelaiko/PGP/blob/main/publickey
	gpg publickey
	
	gpg: WARNING: no command supplied.  Trying to guess what you mean ...  
	pub   rsa3072 2021-01-23 [SC] [expires: 2023-01-22]  
	     A6519F273BF88E9126B0F4C5ECDD0FD294110450  
	uid           SakuraSnowAngel83@protonmail.com  
	sub   rsa3072 2021-01-23 [E] [expires: 2023-01-22]

Revelando o **email de nosso ciberpirata**:

	uid           SakuraSnowAngel83@protonmail.com  

---

# Terceira flag
>What is the attacker's full real name?

Essa foi mamão com açúcar! Jogando o **nickname** do GitHub no Google, **nosso primeiro resultado já nos dá a resposta**, **seu nome**:

![](/assets/Pasted image 20220629030444.png)

![](/assets/Pasted image 20220629030620.png)

---

# Quarta flag
>What cryptocurrency does the attacker own a cryptocurrency wallet for?

Nosso objetivo agora é descobrir qual **criptomoeda** o Aiko movimenta. Vocês lembram dos repositórios de nosso *software engineer*?

Se reparar, os fixados somam apenas **cinco**, quando na realidade ele tem **nove**:

![](/assets/Pasted image 20220629032530.png)

Sendo um deles chamado "ETH", acrônimo para "[Ethereum](https://ethereum.org/en/)", uma das **criptomoedas** mais famosas:

![](/assets/Pasted image 20220629032858.png)

Inserindo **ETH** no TryHackMe, a resposta foi aceita, todavia, outros repositórios podem indicar que ele também utilizaria **Litecoin** e **Bitcoin**.

---

# Quinta flag
>What is the attacker's cryptocurrency wallet address?

Para encontrar o **endereço da carteira** do hacker, precisaremos voltar no tempo.
Mas calma, que não precisaremos do **DeLorean** não!

Dêem uma olhada na página do repositório **ETH**:

![](/assets/Pasted image 20220629033224.png)

Nota-se que **dois** ***commits*** foram feitos. Vamos ver o que foi alterado? Clicando no reloginho vemos:

![](/assets/Pasted image 20220629033630.png)

E ao clicar para ver o *commit* "Update miningscript":

![](/assets/Pasted image 20220629033717.png)

É isso mesmo! O **endereço de sua carteira digital** sendo ocultada.

---

# Sexta flag
>What mining pool did the attacker receive payments from on January 23, 2021 UTC?

Inserindo a nossa **flag anterior** no [Etherscan](https://etherscan.io/):

![](/assets/Pasted image 20220629034349.png)

Descobrimos que de fato a carteira existe:

![](/assets/Pasted image 20220629034431.png)

Agora só precisamos ir até as **transações da carteira** e procurar **quem efetuou pagamentos** para este destino **no dia 23 de Janeiro de 2021** como pede a flag:

![](/assets/Pasted image 20220629034918.png)
Desvendamos mais esta! O remetente desta transação foi "**Ethermine**".

---

# Sétima flag
>What other cryptocurrency did the attacker exchange with using their cryptocurrency wallet?

Qual outra **criptomoeda** o nosso alvo utilizou?
Ainda na página das transações nós encontramos esta resposta:

![](/assets/Pasted image 20220629040200.png)

**Tether**! Uma **criptomoeda** que se compromete a ter uma proporção 1:1 em sua cotação com o **dólar estadunidense**

# Oitava flag
>What is the attacker's current Twitter handle? 

Para achar o *nome de usuário* do Twitter de nosso invasor, as seguintes informações são fornecidas:

>"Como haviamos pensado, o cibercriminoso está completamente ciente de que estamos colentando informações sobre ele após o ataque. Ele ainda foi descarado de citar o OSINT Dojo no Twitter e nos provocar por nossos esforços. A conta que foi usada possui um nome diferente do que estávamos rastreando anteriormente, quem sabe há alguma informação adicional para termos alguma ideia de para onde ir?

![](https://raw.githubusercontent.com/OsintDojo/public/main/taunt.png)

Jogando este novo nome de usuário em qualquer buscador deve nos retornar a conta **@SakuraLoverAiko**

Nessa conta, há um tweet explicitando "Olá! Eu sou **@AikoAbe3!**"

![](/assets/Pasted image 20220630212419.png)

---

# Nona flag
>What is the URL for the location where the attacker saved their WiFi  SSIDs and passwords?

Esta flag admito que achei inesperada mas interessante.
Analisando a última conta do Twitter que encontramos, somos capazes de achar um tweet **nada suspeito**:

![](/assets/Pasted image 20220630212522.png)

Imagem completa:

![](https://pbs.twimg.com/media/EsdhaUSVkAAM803?format=png&name=small)

Okay dokey. Então sabemos que o nosso atacante (por algum motivo) **postou suas senhas de acesso WiFi em algum lugar**.
Outro ponto que nos facilita bastante é que o **ID do post está completo**. Só falta saber **onde** esta lista foi hospedada.

Logo em sequência à este tweet, o Aiko responde:

![](/assets/Pasted image 20220630213801.png)

O print cita "**Dark Web**" e "**Deep Paste**" (em caixa alta, ficou até cômico). Então sabemos que o nosso site se encontra na [rede Onion](https://www.torproject.org/). 
A questão aqui se torna agora **COMO** achar este endereço, considerando que **indexar** páginas nessas redes alternativas sempre exige um trâmite.

Ao achar (no Google, ou num DuckDuckGo da vida) algum site que liste endereços famosos .onion, achar o do DeepPaste se torna fácil ([o endereço v3](https://support.torproject.org/onionservices/v2-deprecation/)).

>Pode ocorrer do Deep Paste estar offline no momento em que estiver trabalhando nesta room. Por isso, o TryHackMe disponibiliza o endereço na dica

![](/assets/Pasted image 20220701192938.png)

Depois de encontrá-lo, basta inserir o ID do post no campo de pesquisa e a página irá mostrar. Nele, alguns **SSIDs** e algumas **senhas** foram armazenadas.

![](/assets/Pasted image 20220701193518.png)

---

# Décima flag

>What is the BSSID for the attacker's Home WiFi?

Para achar o BSSID[^4], será preciso uma conta no [wigle](https://wigle.net/) para utilizar a função de pesquisa avançada.

Com apesquisa simples, só conseguimos ter noção da geolocalização do BSSID, não seu identificador:

![](/assets/Pasted image 20220703005717.png)

Já com a pesquisa avançada:

![](/assets/Pasted image 20220703010734.png)

![](/assets/Pasted image 20220703010722.png)

---

# Décima primeira flag

>What airport is closest to the location the attacker shared a photo from prior to getting on their flight?

Dando uma olhada pelo Twitter dele, encontramos isto:

![](/assets/Pasted image 20220701194313.png)

Até o momento, admito não ser bom em **IMINT**[^5], fui pego nesta parte, o que me motivou a querer fazer a [Searchlight - IMINT](https://tryhackme.com/room/searchlightosint), aprender e explorar mais desta área. Tampouco conhecia alguma ferramenta, então tive de recorrer à ajuda de outros. Todavia tentarei ilustrar de forma que todos nós consigamos **aprender!**

Na imagem é possível ver um **obelisco** beeem lá longe no centro da imagem:

![](/assets/Pasted image 20220703012352.png)

Imagino que para os **estadunidenses** seja algo muito identificável só de bater o olho, pra mim foi impossível. Fiquei cerca de meia hora procurando por lugares perto da costa do Japão, ainda sem ter feito a conexão de que o nosso invasor estava viajando não por terras nipônicas, mas sim em **outro país**. 

O obelisco se trata do **Monumento de Washington**:

![](/assets/Pasted image 20220703012933.png)

Depois, procuramos por aeroportos que são próximos ao monumento:

![](/assets/Pasted image 20220703020423.png)

O mais próximo é o *Ronald Reagan Washington National Airport*. O formato desta flag são três caracteres, então o utilizado aqui será o código de aeroporto.

![](/assets/Pasted image 20220703020833.png)

---

# Décima primeira flag

>What airport did the attacker have their last layover in?

![](/assets/Pasted image 20220703021109.png)

Busca reversa pela imagem:

![](/assets/Pasted image 20220703021723.png)

Vemos já no segundo resultado "**Tokyo International Airport [Haneda]**".

E então basta ir atrás do código de 3 dígitos:

![](/assets/Pasted image 20220703022808.png)

---

#  Décima segunda flag 
>What lake can be seen in the map shared by the attacker as they were on their final flight home?

Também do Twitter:

![](/assets/Pasted image 20220703022955.png)

Basta utilizar um mapa e ir atrás, é bem fácil achar o local no Japão se orientando pelas ilhas ao noroeste. Com facilidade conseguimos esta, já que está na ponta de nossos narizes:

![](/assets/Pasted image 20220703023300.png)

![](/assets/Pasted image 20220703023357.png)

![](/assets/Pasted image 20220703023333.png)

# Décima tercecira flag 
> What city does the attacker likely consider "home"?

Duas vias são possíveis para esta flag:
- Podemos pode usar o SSID do WiFi da cidade de Aiko, que conta no Deep Paste
- Ou podemos ver em qual cidade aquele BSSID que colocamos no Wigle anteriormente se encontra!

---

# Notas de rodapé

[^1]: *Open Source Intelligence*
[^2]: *Pretty Good Privacy*
[^3]:  Ler sobre [criptografia de chave pública](https://pt.wikipedia.org/wiki/Criptografia_de_chave_p%C3%BAblica).
[^4]: *Basic Service Set Identifier*; Geralmente se refere ao endereço MAC, isto é, um identificador único, de um adaptador ou de um ponto de acesso ou uma derivação. Referência: https://www.quora.com/What-is-the-difference-between-SSID-BSSID-and-MAC-addresses
[^5]: *Imagery Intelligence*; "Das três fontes de informações mais utilizadas na área de  inteligência, a chamada área de inteligência de imagens, ou imint (imagery intelligence), é a mais recente. [...] o desenvolvimento de imint como uma disciplina especializada de coleta de informações deu-se fundamentalmente a partir da associação entre o uso de câmerasfotográficas e plataformas aero-espaciais." CEPIK, Marco. Serviços de Inteligência: Agilidade e Transparência como Dilemas de Institucionalização. Tese (Doutorado em Ciência Política) – Instituto Universitário de Pesquisas do Rio de Janeiro. Rio de Janeiro, p. 310. 2001. Disponível em: [https://www2.mppa.mp.br/sistemas/gcsubsites/upload/60/Servi%C3%83%C2%A7os%20de%20Intelig%C3%83%C2%AAncia.pdf](https://www2.mppa.mp.br/sistemas/gcsubsites/upload/60/Servi%C3%83%C2%A7os%20de%20Intelig%C3%83%C2%AAncia.pdf)