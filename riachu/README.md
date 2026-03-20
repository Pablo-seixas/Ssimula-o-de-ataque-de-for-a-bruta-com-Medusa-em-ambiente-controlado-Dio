Simulação de ataque de força bruta com Medusa em ambiente controlado

Esse projeto foi desenvolvido com o objetivo de simular ataques reais de força bruta em serviços comuns de rede, utilizando um ambiente isolado para estudo. A proposta foi seguir o desafio utilizando o Medusa, mas também expandir com outras ferramentas para validar os resultados e demonstrar domínio prático.

O ambiente foi montado com uma máquina vulnerável Metasploitable 2 rodando em máquina virtual e um atacante utilizando Kali Linux em container. Inicialmente houve dificuldade de comunicação entre os ambientes por causa das redes isoladas, então foi utilizada a configuração em bridge para colocar todas as máquinas na mesma rede. Isso permitiu simular um cenário mais próximo do real, onde o atacante não possui restrições de rede.

O IP identificado da máquina alvo foi 172.16.0.20. A partir disso, foi iniciado o processo de reconhecimento.

Primeiro foi realizado o mapeamento de serviços com nmap para entender quais portas estavam abertas e quais serviços estavam disponíveis. Esse passo foi essencial para definir os vetores de ataque.

nmap -sV -Pn 172.16.0.20

O resultado mostrou diversos serviços expostos, incluindo FTP, SMB, HTTP, Telnet e banco de dados, caracterizando um ambiente extremamente vulnerável.

Após a enumeração, foi iniciado o ataque de força bruta no serviço FTP utilizando o Medusa, conforme proposto no desafio.

medusa -h 172.16.0.20 -u msfadmin -P passwords.txt -M ftp

O ataque teve sucesso e retornou credenciais válidas. Isso demonstrou a ausência de política de senha forte e falta de mecanismos de proteção contra múltiplas tentativas de login.

Com as credenciais obtidas, foi possível validar o acesso manual ao serviço FTP, confirmando que a exploração era real e não um falso positivo.

ftp 172.16.0.20

Além do Medusa, foi utilizada a ferramenta Hydra para testar autenticação em serviços HTTP. Durante os testes, foi possível observar que a aplicação web não possuía um mecanismo real de autenticação na rota testada, o que gerou múltiplos resultados positivos. Esse comportamento foi analisado como um falso positivo, evidenciando a importância da interpretação dos resultados.

hydra -l admin -P passwords.txt 172.16.0.20 http-get /

Na parte web, foi utilizado curl para inspeção do conteúdo da aplicação e gobuster para descoberta de diretórios. Foram identificados caminhos importantes como dvwa, phpMyAdmin e mutillidae, indicando a presença de aplicações vulneráveis conhecidas.

curl http://172.16.0.20

gobuster dir -u http://172.16.0.20 -w /usr/share/wordlists/dirb/common.txt

Também foi realizada enumeração de compartilhamentos SMB, reforçando a exposição de serviços internos.

smbclient -L //172.16.0.20 -N

Para fortalecer o projeto e validar os resultados com uma abordagem diferente, foi utilizada a ferramenta Ncrack, bastante conhecida para ataques de autenticação em serviços de rede.

ncrack -u msfadmin -P passwords.txt ftp://172.16.0.20

Além disso, foi utilizado o Patator como ferramenta complementar. O uso de múltiplas ferramentas permitiu confirmar a vulnerabilidade e eliminar dúvidas sobre falsos positivos, demonstrando consistência nos resultados.

patator ftp_login host=172.16.0.20 user=msfadmin password=FILE0 0=passwords.txt

O projeto demonstrou que serviços mal configurados e com credenciais fracas podem ser comprometidos com facilidade. A ausência de controle de tentativas, políticas de senha e monitoramento permite que ataques simples tenham sucesso.

Como medidas de mitigação, recomenda-se o uso de senhas fortes, implementação de bloqueio por tentativas, autenticação multifator e monitoramento de acessos suspeitos.

Esse projeto reforça a importância de práticas básicas de segurança e mostra como ferramentas acessíveis podem ser utilizadas para explorar falhas reais em ambientes mal configurados.
# Simula-o-de-ataque-de-for-a-bruta-com-Medusa-em-ambiente-controlado-DIO
# Simula-o-de-ataque-de-for-a-bruta-com-Medusa-em-ambiente-controlado-DIO
