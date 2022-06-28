---
layout: post
title: "THM Sakura Room"
category: writeup
lang: pt-br
ref: sakura_room-thm
---

exiftool sakurapwnedletter.svg
SakuraSnowAngelAiko

![[Pasted image 20220627235902.png]]

# Primeiros passos
Esta room é pensada para testar uma variedade de técnicas de **OSINT**  (Open Source Intelligence), iremos passar por um exemplo de **investigação** que visa identificar um número de identificadores e outros pedaços de informação para ajudar a **capturar um cibercriminoso**.

	NOTE: All answers can be obtained via passive OSINT techniques, DO NOT attempt any active techniques such as reaching out to account owners, password resets, etc to solve these challenges.

Também somos avisados de que nenhum **OSINT ativo** será necessário. Veja a seguir **alguns** exemplos distingui-las:

| OSINT Passivo                                          | OSINT Ativo                                                 |
| ------------------------------------------------------ | ----------------------------------------------------------- |
| Não faz contato direto                                 | Pode ocorrer contato entre os lados                         |
| Scans passivos, mais oculto                            | Maior risco de detecção                                     |
| Juntar informações disponíveis, hospedadas em terceiros | Engenharia social, ex: enviar um link malicioso para vítima |

---

# Primeira flag
	What username does the attacker go by?

Nos é pedido o **username** do criminoso. Para obter, nos é fornecido um panorama:
O [OSINT Dojo](https://www.osintdojo.com) foi vítima de um **ciberataque** e durante análises forenses, foi achada uma imagem deixada pelo autor do crime. Quem sabe ela não tem alguma pista que nos permitiria apontar uma origem?

![[Pasted image 20220628011354.png]]

Com isso temos nosso ponto de partida. Em uma imagem podem haver inúmeras informações escondidas, seja porque [alguém escondeu](https://www.gta.ufrj.br/ensino/eel878/redes1-2016-1/16_1/esteganografia/)  lá, ou, como no nosso caso, porque alguém **esqueceu de checar o próprio rastro**. O *Exchangeable image file format* ou simplesmente **EXIF** é um **padrão para armazenar informações na fotografia digital**. Resumidamente, é um conjunto de informações sobre (e contidas em) uma imagem, tais como sua **resolução** (largura e altura), **data de modificação**, **fabricante da câmera**, etc. Em alguns casos, pode conter até mesmo dados de **geolocalização**. 

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

<pre class=""> ______
|_,.,--\
   ||
   ||
   ##
   ##
</pre>