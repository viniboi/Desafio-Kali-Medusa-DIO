Desafio-Kali-Medusa-DIO

Esse é um projeto pratico que a @DIO pede no seu bootcamp Santander - Cibersegurança 2025 na aula (Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux) que no final pede para Implementar, documentar e compartilhar um projeto prático utilizando Kali Linux e a ferramenta Medusa, em conjunto com ambientes vulneráveis (por exemplo, Metasploitable 2 e DVWA), para simular cenários de ataque de força bruta e exercitar medidas de prevenção.

---

## ⚙️ Configuração do Ambiente

### 1 Instalação do VirtualBox e Downloads

- Baixe e instale o https://www.virtualbox.org em sua máquina hospedeira.

- Baixe a imagem ISO ou VM pronta do Kali Linux (https://www.kali.org)

- Baixe a imagem VM do Metasploitable 2 (https://sourceforge.net/projects/metasploitable/files/Metasploitable2)

Opcional: Se for usar o DVWA separadamente (além do Metasploitable 2 que já o inclui), você precisará de uma terceira VM ou um ambiente Docker/LAMP para rodá-lo.

### 2 Configuração das VMs

- crie e instale a VM do Kali Linux no VirtualBox.

- Importe/Crie a VM do Metasploitable 2 (usando o arquivo .vmdk).

.Se nao conseguiu colocar ou instalar o metasploitable 2 segue o link de um guia na propria plataforma da @DIO (https://www.dio.me/articles/tutorial-metasploit-2-na-virtualbox-santander-ciberseguranca-2025-fb85402b3224)

.Se nao conseguiu colocar ou instar o Kali Linux segue o link de um guia na propria platadorma da @DIO (https://web.dio.me/articles/guia-de-como-baixar-e-configurar-o-kali-linux-na-virtualbox-e596a0d110d6?back=/articles)

### 3 Configuração da Rede (Host-Only)

- No VirtualBox, crie uma Rede Somente Host (Host-Only).

- Configure as placas de rede de ambas as VMs para usar essa rede Host-Only (e remova/desabilite o NAT/Bridge se desejar isolamento total, ou use 2 adaptadores se precisar de internet no Kali).

- Verifique a Conectividade: Inicie as duas VMs. No Kali Linux, use o comando **`nmap -sn [faixa_de_IP_da_rede]`** para descobrir o IP do Metasploitable 2. Use **`ping -c [tentativas] [faixa_de_IP_da_rede] `** para garantir que há comunicação entre elas.

## 🕵️ Começando os serviços 

### 1 Enumeração de Serviços (Nmap)

- No Kali, para verifição do ip use o comando: **`ip a`** e para saber aonde esta o seu ip da uma olhada na imagen

| ip a |
|--------|
| <img width="500" height="300" alt="Image" src="https://github.com/user-attachments/assets/38cb10a0-97da-4a10-bb56-105801980b34" /> | 

- No Kali, use o Nmap para verificar quais portas e serviços estão abertos no IP do Metasploitable 2 usando  **`nmap -sV -p 21,22,80,445,139 [faixa_de_IP_da_rede]`** uma explicação rápida sobre os comandos usados no code: -p basicamente ele vai escanear as portas que foi colocado / -sV ele tenta indentificar qual a versão esta rodando em cada porta

| ping |
|--------|
| <img width="500" height="300" alt="Image" src="https://github.com/user-attachments/assets/a9118aca-8a8a-428d-b4d2-f544fff2068e" />

### 2 Criação de Wordlists

- Crie wordlists simples para usuários (users.txt) e senhas (passwords.txt) baseadas em credenciais comuns ou o ambiente Metasploitable 2 (por exemplo, msfadmin, user, password, admin, etc.). Salve-as em uma pasta usando esses comandos no terminal **`echo -e 'user\nmsfadmin\nadmin\nroot' > users.txt`** e **`echo -e "123456\npassword\nquerty\nmsfadmin" > passwords.txt`**

### 3 Ataque de Força Bruta em FTP (Medusa):

- Execute o Medusa para tentar a força bruta no serviço FTP (https://www.kali.org/tools/medusa).

- Comando Exemplo: **`medusa -H [IP_Metasploitable] -u [CAMINHO_WORDLIST_USUARIOS] -P [CAMINHO_WORDLIST_SENHAS] -M ftp -t 6 `** (Ou -U [USUARIO_ALVO] -P [CAMINHO_WORDLIST_SENHAS] -M ftp -t 6) o -t 6 é para usar mais treads para um resultado mais rapido

| medusa |
|--------|
| <img width="500" height="300" alt="Image" src="https://github.com/user-attachments/assets/afe5a8fa-a424-44a0-8adf-880d16f177ae" /> | 

- Capture a tela do comando Medusa em execução e o resultado de sucesso (credenciais encontradas).

- Valide o acesso usando o cliente FTP com as credenciais descobertas com o comando **`ftb [IP_Metasploitable]**`**

| fpt login |
|--------|
| <img width="500" height="300" alt="Image" src="https://github.com/user-attachments/assets/dea573ef-5092-481b-a148-4509fd8ef220" /> | 
  
### 4 Ataque de Força Bruta/Password Spraying em SMB (Medusa):

- Execute o Medusa para tentar a força bruta no serviço SMB

- Dica: Você pode usar uma wordlist grande de usuários e uma senha simples (password spraying) ou listas menores para ambos, se preferir.

- Comando Exemplo (Password Spraying): **`medusa -H [IP_Metasploitable] -U [CAMINHO_WORDLIST_USUARIOS] -p password -M smb`**

- Capture a tela do comando Medusa em execução e o resultado de sucesso.

- Valide o acesso usando uma ferramenta SMB com as credenciais descobertas com o comando **`smbclient [IP_Metasploitable]`**

## 🐍 Força Bruta em Aplicação Web (DVWA)

- Para entrar na Aplicação Web deve abrir um navegador e digitar na barra url: **`[IP_Metasploitable]/dvwa/login.php`**

| dvwa |
|--------|
| <img width="700" height="400" alt="Image" src="https://github.com/user-attachments/assets/ae9a9ed7-cecd-4055-87a8-c19f49c26acc" /> | 

- Curiosidade: você abre o modo desenvolvedor que para cada pessoa muda Fn+f12 ou f12 se nao for nenhum dos dois clique com o botao direito na pagina e selecione o desenvolvedor, com um tipo de janela subindo na sua tela e indo na aba network e tente logar com qualquer usuario e senha que vai mostrar o login failed e pelo request na aba de network vc consegue ver o username: e o password: que foi usado.

| desenvolvedor |
|--------|
| <img width="700" height="700" alt="Image" src="https://github.com/user-attachments/assets/bbb6d831-2559-4aa4-9b4f-0bde6d60c1cf" /> | 

- Agora usando a Força bruta com o medusa voce vai digitar esse comando aqui:
```sh
medusa -h [IP_Metasploitable] U users.txt -P password.txt -M http\
-m PAGE:'/dvwa/login.php' \
- FORM: 'username=^USER^&password=^PASS^&Login Login' \
-m 'FAIL-Login failed' -t 6
```
- O codigo é grande entao descrevi o que cada comando faz:

medusa: O nome da ferramenta, um brute-forcer de login rápido, modular e paralelo.

-h [IP_Metasploitable]: Especifica o host alvo pelo seu endereço IP.

-U users.txt: Fornece um arquivo chamado users.txt contendo uma lista de nomes de usuário a serem testados.

-P pass.txt: Fornece um arquivo chamado pass.txt contendo uma lista de senhas a serem testadas.

-M http: Especifica o módulo a ser usado para o ataque, neste caso, o módulo HTTP.

-m PAGE:'/dvwa/login.php': Passa um parâmetro para o módulo HTTP, especificando o caminho para a página de login no servidor alvo. Neste exemplo, é a página de login do Damn Vulnerable Web Application (DVWA).

-m FORM:'username=^USER^&password=^PASS^&Login=Login': Outro parâmetro para o módulo HTTP, que define os campos do formulário e seus valores. Os marcadores ^USER^ e ^PASS^ são substituídos por nomes de usuário e senhas das listas fornecidas (users.txt e pass.txt).

-m 'FAIL=Falha no login': Especifica a string que indica uma tentativa de login falha. A ferramenta procurará esse texto na resposta do servidor para determinar se as credenciais estavam incorretas.

-t 6: Define o número total de tentativas de login a serem testadas simultaneamente (o número de threads)

| comando/resultado |
|--------|
| <img width="500" height="300" alt="Image" src="https://github.com/user-attachments/assets/2ad32af0-2744-4c6f-a2a9-e5e8a5a75ad9" /> |
| <img width="500" height="300" alt="Image" src="https://github.com/user-attachments/assets/14d3a9e0-f8a0-452e-907d-2ef90086c0fc" /> 

## 🛡️ Medidas de Mitigação contra Ataques de Força Bruta

### Prevenção para Serviço FTP (File Transfer Protocol)
- Bloqueio e Limitação de Taxa:	Use ferramentas como **`fail2ban`** ou configurações do firewall **`(iptables)`** para monitorar os logs de login (erros de senha). Se um IP tentar acessar a conta várias vezes seguidas (ex: 5 tentativas em 5 minutos), ele deve ser bloqueado temporariamente ou permanentemente.
- Uso de Senhas Fortes:	Implemente políticas de senha obrigatórias. Exija senhas longas (mínimo de 12-16 caracteres), complexas (mistura de maiúsculas, minúsculas, números e símbolos) e únicas.
- Desativação de FTP Anônimo	Se não for estritamente necessário, desabilite o acesso de usuários anônimos, pois isso fornece um alvo fácil para testar credenciais.Troca para SFTP/FTPS	Evite o FTP "clássico" (que transmite credenciais em texto puro). Migre para SFTP (via SSH) ou FTPS (FTP sobre SSL/TLS), que criptografam a comunicação, dificultando a interceptação (sniffing) de credenciais válidas.
- Autenticação Baseada em Chave	Para SFTP, sempre que possível, utilize a autenticação baseada em chaves SSH em vez de senhas, tornando a força bruta ineficaz.

### Prevenção para Serviço SMB (Server Message Block)
- Auditoria e Monitoramento: (Fail2ban)	Assim como no FTP, configure o fail2ban ou outras ferramentas de auditoria (como Security Onion ou ELK Stack) para analisar logs de tentativas de login SMB e bloquear IPs maliciosos após um número definido de falhas.
- Restrição de Acesso por Rede: (Firewall)	Utilize o firewall do sistema operacional ou da rede para limitar o acesso à porta SMB (445/139) apenas a IPs confiáveis, sub-redes internas específicas ou VPNs. O SMB nunca deve estar diretamente exposto à internet.
- Política de Bloqueio de Conta:	Configure a política de segurança do domínio/sistema operacional para bloquear temporariamente a conta após um número pequeno de falhas de login (ex: 3 a 5 tentativas).
- Desativação de Contas Inativas/Padrão: Desative ou remova qualquer conta de usuário que não esteja em uso. No caso do Metasploitable 2, as credenciais padrão (como msfadmin ou user) devem ser alteradas imediatamente ou desativadas.
- Enumeração de Usuários:	Aplique patches e configurações para impedir a enumeração de usuários (o processo de descobrir nomes de usuários válidos), o que é frequentemente um primeiro passo para o ataque de força bruta.

### Prevenção para Aplicações Web (DVWA - Formulários de Login)
- Limitação de Taxa (Rate Limiting):	Configure o Web Application Firewall (WAF) ou a aplicação para limitar o número de tentativas de login por segundo/minuto por endereço IP. Isso torna a força bruta inviável (ou muito lenta).
- CAPTCHA e ReCAPTCHA:	Implemente CAPTCHAs que sejam difíceis de serem resolvidos por bots após um número pequeno de falhas de login. Isso adiciona um "teste humano" que as ferramentas de força bruta não conseguem passar facilmente.
- Bloqueio de IP Temporário:	Após N tentativas de login fracassadas, bloqueie temporariamente o IP de origem (similar ao fail2ban).
- Armazenamento Seguro de Senhas (Hashing e Salting):	O lado do servidor NUNCA deve armazenar senhas em texto simples. Use algoritmos de hashing lentos (como Argon2 ou bcrypt) e adicione um salt (valor aleatório único) a cada senha para dificultar ataques de tabela rainbow.
- Autenticação de Múltiplos Fatores (MFA):	Implemente o MFA (2FA). Mesmo que a senha seja descoberta, o invasor ainda precisará de um segundo fator (código no celular, chave física, etc.).

## 🤓 Conclusões/Aprendizados
O desenvolvimento deste projeto prático, focado na simulação de ataques de Força Bruta em um ambiente controlado, foi fundamental para consolidar o entendimento sobre as vulnerabilidades críticas de autenticação e as práticas de defesa essenciais.

- Compreensão de Ataques de Força Bruta: a execução dos testes contra os protocolos FTP, SMB e aplicações Web (DVWA) me mostrou de forma pratica que nao precisa de muito para tentar invadir e que muitos estao sem a devida segurança que necessitava nos sites ou servidores.

- Domínio de Ferramentas de Auditoria: a ferramenta kali linux foi algo enssencial para esse projeto que mostrou que nao so o medusa como muitas outras ferramentas dentro dele fazem o caos dentro de sistemas sem segurança devida.

- Valor da Documentação Técnica: as instruções tecnicas tecnicas por exemplo o host-only deixa claro que as informações tecnicas sao indispensaveis pra fazer um belíssimo trabalho, o github se provou uma ferramenta incrivel para devs/programadores para publicar os seus projetos.

- Conscientização sobre Vulnerabilidades e Mitigação: a troca dos testes para a mitigação foi o ponto alto do aprendizado, ficou muito evidente que falhas (como a falta de Rate Limiting em um formulário de login ou o uso de credenciais padrão no Metasploitable 2) criam ataques faceis e automatizados por ferramentas gratuitas e faceis de se usar e mostram que segurança nao e um bonus e enssencia para todos.

### Reflexão Final

Este desafio não apenas confirmou a teoria aprendida em aula mas também forneceu a experiência prática de configurar, atacar e defender um ambiente. A principal lição é que a segurança de um sistema é tão forte quanto seu ponto de autenticação mais fraco. Como iniciante profissional de segurança, é vital manter a mentalidade de um atacante (para encontrar falhas) e simultaneamente, de um defensor (para implementar as proteções robustas detalhadas na seção de Mitigação), foi incrivel assistir essa aula e ter feito o projeto, olhei bastante projetos publicos entendo como deixava mais profissonal, A estrutura e os tópicos foram validados e aprimorados com o auxílio do Google Gemini, garantindo que todos os requisitos do desafio fossem abordados de forma técnica e organizada.

## Espero que tenham conseguido entender o tema e tambem pra quem tentou replicar os passos ter conseguido o acesso ao ftp,smb e dvwa.
