# limpeza-de-disco
Uma abordagem de como sobre escrever zero (0) em todo disco. 

Sabemos que o computador só enxerga zero e um, com esse conhecimento em mente podemos entender que quando temos um arquivo de foto ele fica distribuído em um pen-drive por exemplo com zeros e uns. Uma vez que você apaga esse arquivo, seja enviando para a lixeira ou formatando, o sistema de arquivos muita das vezes muda o estado dos setores no qual os bits estão alocados dando a impressão de que o arquivo foi excluído realmente, mas não foi muita das vezes permitindo assim que aplicações forenses sejam capazes de recuperar tal arquivo.  

Existe aplicações que são capazes de sanitizar os discos de forma que todos os setores do disco são sobrescritos apenas com zeros (0).  

Essa abordagem tem por fim mostrar o uso da aplicação DC3DD para sanitizar um disco sobre escrevendo todos os setores com zero aplicando a técnica de ‘wipe’. 

 

O meu ambiente é Ubuntu 20.04.5 LTS. 

Abra o terminal e digite:  

dc3dd --version    

Saída: 

dc3dd (dc3dd) 7.2.646 

Copyright (C) 2008 Free Software Foundation, Inc. 

License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html> 

This is free software: you are free to change and redistribute it. 

There is NO WARRANTY, to the extent permitted by law. 

  

Written by Paul Rubin, David MacKenzie, Stuart Kemp, 

Jesse Kornblum, Andrew Médico, Richard Cordovano, and Justin Lowe. 

 

Caso você não obtenha essa saída acima que é a versão que está instalada aqui na minha máquina é hora de instalar: 

 

Instale com o comando: 

sudo apt install dc3dd 

Uma vez instalado, agora a versão será exibida. 

Como objeto de estudo irei sanitizar um pen-drive de 4gb. 

Espete o dispositivo no seu pc/note. 

Digite o comando:  

 sudo fdisk –l 

Ele mostra todos os discos, partições e discos conectados nas portas USB. 

 

Como podemos observar abaixo o primeiro disco que ele mostrou no meu notebook foi o meu ssd que está em /dev/nvme0n1: 

 Disco /dev/nvme0n1: 238,49 GiB, 256060514304 bytes, 500118192 setores 

Disk model: IM2P33F3A NVMe ADATA 256GB               

Unidades: setor de 1 * 512 = 512 bytes 

Tamanho de setor (lógico/físico): 512 bytes / 512 bytes 

Tamanho E/S (mínimo/ótimo): 512 bytes / 512 bytes 

Tipo de rótulo do disco: gpt 

Identificador do disco: 34CE8B2E-3292-49DA-9123-6D9F7452D85C 

  

Dispositivo       Início       Fim   Setores Tamanho Tipo 

/dev/nvme0n1p1      2048    206847    204800    100M Sistema EFI 

/dev/nvme0n1p2    206848    239615     32768     16M Microsoft reservado 

/dev/nvme0n1p3    239616 191851735 191612120   91,4G Microsoft dados básicos 

/dev/nvme0n1p4 191852544 273770495 81917952   39,1G Microsoft dados básicos 

/dev/nvme0n1p5 273772544 499052543 225280000 107,4G Linux sistema de arquivos 

/dev/nvme0n1p6 499052544 500115455   1062912    519M Windows ambiente de recuperação 

 

É de suma importância salientar que no Linux todo disco fica organizado em /dev/uma letra ou nome, como podemos ver abaixo:  

Disco /dev/sda: 3,75 GiB, 4004511744 bytes, 7821312 setores 

Disk model: Cruzer Blade     

Unidades: setor de 1 * 512 = 512 bytes 

Tamanho de setor (lógico/físico): 512 bytes / 512 bytes 

Tamanho E/S (mínimo/ótimo): 512 bytes / 512 bytes 

Tipo de rótulo do disco: dos 

Identificador do disco: 0x500a0dff 

  

Dispositivo Inicializar     Início        Fim    Setores Tamanho Id Tipo 

/dev/sda1               1634493285 3550204804 1915711520 913,5G 6e desconhecida 

/dev/sda2                     2573       2573          0      0B 63 GNU HURD ou SysV 

/dev/sda4                 28049408   28049848        441 220,5K 0 Vazia 

  

Partições lógicas fora da ordem do disco. 

Como você pode observar o pen-drive está em /dev/sda  assim como se tivesse outro pen-drive como vou demonstrar a seguir: 

 

Disco /dev/sdb: 29,45 GiB, 31614566400 bytes, 61747200 setores 

Disk model: Flash Disk       

Unidades: setor de 1 * 512 = 512 bytes 

Tamanho de setor (lógico/físico): 512 bytes / 512 bytes 

Tamanho E/S (mínimo/ótimo): 512 bytes / 512 bytes 

Tipo de rótulo do disco: dos 

Identificador do disco: 0x500a0dff 

  

Dispositivo Inicializar     Início        Fim    Setores Tamanho Id Tipo 

/dev/sdb1               1634493285 3550204804 1915711520 913,5G 6e desconhecida 

/dev/sdb2                     2573       2573          0      0B 63 GNU HURD ou SysV 

/dev/sdb4                 28049408   28049848        441 220,5K 0 Vazia 

  

Partições lógicas fora da ordem do disco. 

Já esse segundo dispo ficou em   /dev/sdb a cada disco em uma porta usb fica assim por diante. 

 

Agora vamos sanitizar o disco em questão: 

Com o terminal aberto digite: 

sudo dc3dd wipe=/dev/nome da partição do seu pen-drive, no meu caso ficou: 

sudo dc3dd wipe=/dev/sda  

Essa letra após o /dev/ pode variar de disco para disco, então é de suma importância limpar o disco certo, uma vez que se você colocar o caminho do seu HD, o mesmo será corrompido, talvez não sendo possível nunca mais recuperar os dados ali contidos. 

A saída da limpeza ficou assim: 

experiment@laboratory:~$ sudo dc3dd wipe=/dev/sda 

  

dc3dd 7.2.646 started at 2023-01-05 13:37:46 -0300 

compiled options: 

command line: dc3dd wipe=/dev/sda 

device size: 7821312 sectors (probed), 4,004,511,744 bytes 

sector size: 512 bytes (probed) 

  4004511744 bytes (3,7 G) copied (100%), 652 s, 5,9 M/s                  

  

input results for pattern `00': 

   7821312 sectors in 

  

output results for device `/dev/sda': 

   7821312 sectors out 

  

dc3dd completed at 2023-01-05 13:48:38 -0300 

   

Com essa informação “Input results for pattern `00': 

   7821312 sectors in” o programa informa que foi sobre escrito 00 em 7821312 setores e o pen-drive está limpo. 

Na literatura acreditasse que aplicando a técnica de wipe duas vezes em um dispositivo é impossível recuperar os dados ali contidos, então muito cuidado na hora de usar essa ferramenta para não perder todos os seus dados.   

Se gostou da dica dá uma estrela, ou um gostei e compartilha com um amigo(a). 

Espero que você tenha gostado dessa explicação de forense do jeito fácil.  Gostou da aplicação acesse man dc3dd para ver mais opções de comando, caso queira tem o manual traduzido aqui: https://github.com/JonatasCosta1983/Manual-traduzido-do-DC3DD- 

Obrigado por ler até aqui!     

 



