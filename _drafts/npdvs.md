---
title: NPDVs
author: Nilsonlinux
date: 2021-02-15 00:57:00 +0000
categories:
- automacao
tags:
- automacao
test: ''

---
\### NPDVs

\# Script para automatização de tarefas. 

Possue algumas configurações em suas variáveis para uso em desktop em diferentes resoluções de telas.  

\`\`\`RES\`\`\` = Resolução de vídeo  

\`\`\`PDVS_IPS\`\`\` = Finais dos IPs que deseja que o comando seja execultado simultaneamente.  

OS PRINCIPAIS FICAM NAS PRIMEIRAS LINHAS.  

  

By: Nilsonlinux

Comando para instalação (Termux)

\`\`\`bash

pkg update && pkg install sshpass git && apt install openssh && git clone [https://github.com/sistemanpdvs/npdvs.git](https://github.com/sistemanpdvs/npdvs.git "https://github.com/sistemanpdvs/npdvs.git") && chmod +x ./npdvs/* && ./npdvs/npdvs.sh

\`\`\`

Comando para instalação no desktop (Debian based)

\`\`\`bash

sudo apt install rdesktop sshpass git && git clone [https://github.com/sistemanpdvs/npdvs.git](https://github.com/sistemanpdvs/npdvs.git "https://github.com/sistemanpdvs/npdvs.git") && chmod +x ./npdvs/* && ./npdvs/npdvs.sh

\`\`\`

\## Após a instalação, utilize o comando 

\`\`\`bash

./npdvs/npdvs.sh

\`\`\`