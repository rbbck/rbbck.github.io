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
ExifTool Version Number         : 12.42  
File Name                       : sakurapwnedletter.svg  
Directory                       : .  
File Size                       : 850 kB  
File Modification Date/Time     : 2022:06:28 01:28:26-03:00  
File Access Date/Time           : 2022:06:28 01:28:26-03:00  
File Inode Change Date/Time     : 2022:06:28 01:28:26-03:00  
File Permissions                : -rw-r--r--  
File Type                       : SVG  
File Type Extension             : svg  
MIME Type                       : image/svg+xml  
Xmlns                           : http://www.w3.org/2000/svg  
Image Width                     : 116.29175mm  
Image Height                    : 174.61578mm  
View Box                        : 0 0 116.29175 174.61578  
SVG Version                     : 1.1  
ID                              : svg8  
Version                         : 0.92.5 (2060ec1f9f, 2020-04-08)  
Docname                         : pwnedletter.svg  
Export-filename                 : /home/SakuraSnowAngelAiko/Desktop/pwnedletter.png  
Export-xdpi                     : 96  
Export-ydpi                     : 96  
Metadata ID                     : metadata5  
Work Format                     : image/svg+xml  
Work Type                       : http://purl.org/dc/dcmitype/StillImage  
Work Title                      :
```

Temos inúmeras saídas aqui, mas vamos focar em uma específica. **Export-filename** possui como valor o caminho do arquivo na hora de sua exportação: 
```
Export-filename                 : /home/SakuraSnowAngelAiko/Desktop/pwnedletter.png
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
	
	gpg: WARNING: no command supplied.  Trying to guess what you mean ...  
	pub   rsa3072 2021-01-23 [SC] [expires: 2023-01-22]  
	     A6519F273BF88E9126B0F4C5ECDD0FD294110450  
	uid           SakuraSnowAngel83@protonmail.com  
	sub   rsa3072 2021-01-23 [E] [expires: 2023-01-22]

Revelando o **email de nosso ciberpirata**:
	uid           SakuraSnowAngel83@protonmail.com  

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

---
>Under construction
><pre class=""> ______
|_,.,--\
   ||
   ||
   ##
   ##</pre>


---

{: data-content="Notas de rodapé"}
[^1]: *Open Source Intelligence*
[^2]: *Pretty Good Privacy*
[^3]:  Ler sobre [criptografia de chave pública](https://pt.wikipedia.org/wiki/Criptografia_de_chave_p%C3%BAblica).

