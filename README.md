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

- No Kali, para verifição do ip use o comando: **`ip a`** e para saber aonde esta o seu ip da uma olhada nas imagens

- No Kali, use o Nmap para verificar quais portas e serviços estão abertos no IP do Metasploitable 2 usando  **`nmap -sV -p 21,22,80,445,139 [faixa_de_IP_da_rede]`** uma explicação rápida sobre os comandos usados no code: -p basicamente ele vai escanear as portas que foi colocado / -sV ele tenta indentificar qual a versão esta rodando em cada porta

### 2 Criação de Wordlists

- Crie wordlists simples para usuários (users.txt) e senhas (passwords.txt) baseadas em credenciais comuns ou o ambiente Metasploitable 2 (por exemplo, msfadmin, user, password, admin, etc.). Salve-as em uma pasta usando esses comandos no terminal **`echo -e 'user\nmsfadmin\nadmin\nroot' > users.txt`** e **`echo -e "123456\npassword\nquerty\nmsfadmin" > passwords.txt`**

### 3 Ataque de Força Bruta em FTP (Medusa):

- Execute o Medusa para tentar a força bruta no serviço FTP (https://www.kali.org/tools/medusa).

- Comando Exemplo: **`medusa -H [IP_Metasploitable] -u [CAMINHO_WORDLIST_USUARIOS] -P [CAMINHO_WORDLIST_SENHAS] -M ftp -t 6 `** (Ou -U [USUARIO_ALVO] -P [CAMINHO_WORDLIST_SENHAS] -M ftp -t 6) o -t 6 é para usar mais treads para um resultado mais rapido

- Capture a tela do comando Medusa em execução e o resultado de sucesso (credenciais encontradas).

- Valide o acesso usando o cliente FTP com as credenciais descobertas com o comando **`ftb [IP_Metasploitable]**`**
  
### 4 Ataque de Força Bruta/Password Spraying em SMB (Medusa):

- Execute o Medusa para tentar a força bruta no serviço SMB

- Dica: Você pode usar uma wordlist grande de usuários e uma senha simples (password spraying) ou listas menores para ambos, se preferir.

- Comando Exemplo (Password Spraying): **`medusa -H [IP_Metasploitable] -U [CAMINHO_WORDLIST_USUARIOS] -p password -M smb`**

- Capture a tela do comando Medusa em execução e o resultado de sucesso.

- Valide o acesso usando uma ferramenta SMB (como smbclient) com as credenciais descobertas.

## 🐍 Força Bruta em Aplicação Web (DVWA) e Mitigação

- Para entrar na Aplicação Web deve abrir um navegador e digitar na barra url: **`[IP_Metasploitable]/dvwa/login.php**`

- Curiosidade: você abre o modo desenvolvedor que para cada pessoa muda Fn+f12 ou f12 se nao for nenhum dos dois clique com o botao direito na pagina e selecione o desenvolvedor, com um tipo de janela subindo na sua tela e indo na aba network e tente logar com qualquer usuario e senha que vai mostrar o login failed e pelo request na aba de network vc consegue ver o username: e o password: que foi usado.

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

-t 6: Define o número total de tentativas de login a serem testadas simultaneamente (o número de threads).
