## Localizando  Pessoas  Desaparecidas  com  Google  Sheets  e  Gemini  

A  busca  por  pessoas  desaparecidas  é  uma  tarefa  angustiante  e  complexa,  especialmente  quando  envolve  milhares  de  indivíduos.  Este  projeto  visa  a  simplificar  esse  processo,  utilizando  a  inteligência  artificial  do  Google  Gemini  e  a  praticidade  do  Google  Sheets  para  cadastrar  e  consultar  informações  sobre  pessoas  desaparecidas.

**⚠️  Importante:**  O  código  apresentado  neste  tutorial  está  em  fase  inicial  de  desenvolvimento  e  apresenta  algumas  falhas  na  busca  e  registro  que  precisam  ser  corrigidas.  Ele  serve  como  um  guia  inicial  e  pode  ser  aprimorado  para  maior  precisão  e  eficiência.

### Funcionalidades  do  Projeto:

-   **Cadastro  de  Pessoas  Desaparecidas:**  Coleta  informações  essenciais  sobre  a  pessoa  desaparecida  e  as  armazena  em  uma  planilha  Google  Sheets.
    
-   **Consulta  de  Pessoas  Desaparecidas:**  Busca  por  informações  de  indivíduos  cadastrados  na  planilha  utilizando  nome,  idade,  gênero,  cidade,  filiação  e  RG.
    
-   **Geração  de  Mensagens  de  Conforto:**  Se  a  pessoa  não  for  encontrada  no  cadastro,  a  IA  do  Google  Gemini  cria  uma  mensagem  de  conforto  para  a  pessoa  que  está  realizando  a  busca.
    

### Pré-requisitos:

-   Conta  Google  (Gmail)
    
-   Planilha  Google  Sheets  (uma  planilha  será  criada  automaticamente  se  você  ainda  não  tiver  uma)
    
-   Projeto  no  Google  Cloud  Platform  com  a  API  Gemini  habilitada
    
-   API  Key  do  Google  Gemini
    
-   Bibliotecas  Python:  google-auth,  google-generativeai,  google-cloud-secret-manager,  google-api-python-client,  gspread,  numpy,  pandas
    

### Passo  1:  Configuração  do  Ambiente

1.  **Instale  as  bibliotecas  Python:**
```ruby
!pip install -q google-auth 
!pip install -U -q google-generativeai 
!pip install -q google-cloud-secret-manager !pip install -q google-api-python-client 
!pip install -q gspread 
!pip install -q numpy !pip install -q pandas
```
1.  **Importe  as  bibliotecas:**
```ruby
from googleapiclient.discovery import build import gspread 
import numpy as np 
import pandas as pd 
import google.generativeai as genai 
import json 
from google.colab 
import auth auth.authenticate_user() 
import os 
from google.cloud import secretmanager 
from oauth2client.service_account import ServiceAccountCredentials
```
1.  **Configure  seu  projeto:**
```ruby
os.environ["GOOGLE_CLOUD_PROJECT"] = "SEU_PROJECT_ID"  # Substitua pelo ID do seu projeto GOOGLE_API_KEY = "SUA_API_KEY"  # Substitua pela sua API Key do Gemini
genai.configure(api_key=GOOGLE_API_KEY) 

projeto_id = "SEU_PROJECT_ID" 
nome_do_segredo = "projeto_desaparecidos"  # Nome do segredo no Secret Manager
```
### Passo  2:  Acesso  à  Planilha  Google  Sheets
```ruby
def acessar_planilha(): 
escopo ='https://spreadsheets.google.com/feeds', 'https://www.googleapis.com/auth/drive'] 
creds = ServiceAccountCredentials.from_json_keyfile_dict(json.loads(chave_json), escopo) cliente = gspread.authorize(creds) 
planilha = cliente.open_by_url('https://docs.google.com/spreadsheets/d/1pr1jjq3rU5-UOdh-p4niJ62OZeImQ0zqLGne7wQZBmM/edit') # URL da planilha 
return planilha.worksheet('Lista de Pessoas Desaparecidas') # Nome da aba da planilha
```
**Explicação:**

-   Esta  função  define  o  acesso  à  planilha  Google  Sheets  utilizando  as  credenciais  geradas  no  Secret  Manager.
    
-   Você  precisará  substituir  a  URL  da  planilha  e  o  nome  da  aba,  caso  sejam  diferentes.
- 
### Passo  3:  Cadastro  de  Pessoas  Desaparecidas
```ruby
def adicionar_pessoa(nome, idade, genero, cidade, filiacao, rg, celular): 
planilha = acessar_planilha() 
nova_linha = [nome, idade, genero, cidade, filiacao, rg, celular] planilha.append_row(nova_linha)
```
**Explicação:**

-   Esta  função  adiciona  uma  nova  linha  à  planilha  com  as  informações  da  pessoa  desaparecida.
- 
### Passo  4:  Consultar  Pessoa  Desaparecida
```ruby
def consultar_pessoa(nome, idade, genero, cidade, filiacao, rg): 
planilha = acessar_planilha() 
dados = planilha.get_all_records() 
for pessoa in dados:
if (pessoa['Nome'] == nome and 
pessoa['Idade'] == int(idade) and pessoa['Genero'] == genero and 
pessoa['Cidade'] == cidade and pessoa['Filiação'] == filiacao and 
pessoa['Rg'] == rg): 
return pessoa return  None
```
**Explicação:**
-   Esta  função  busca  na  planilha  uma  pessoa  com  as  informações  fornecidas.
    
-   Se  encontrar  a  pessoa,  retorna  os  dados  da  linha  correspondente.  Caso  contrário,  retorna  None.

### Passo  5:  Coletar  Informações
```ruby
def coletar_informacoes(): 
print("Olá, sou o Gemini. Para te ajudar, preciso de algumas informações sobre a pessoa desaparecida.") 
nome = input("Digite o nome da pessoa: ") 
idade = input("Digite a idade: ") 
genero = input("Digite o gênero: ") 
cidade = input("Digite a cidade: ") 
filiacao = input("Digite a filiação: ") 
rg = input("Digite o RG: ") 
return nome, idade, genero, cidade, filiacao, rg
```
**Explicação:**

-   Esta  função  solicita  as  informações  da  pessoa  desaparecida  ao  usuário.
- 
### Passo  6:  Fluxo  Principal  do  Programa
```ruby
# Fluxo principal 
nome, idade, genero, cidade, filiacao, rg = coletar_informacoes() pessoa_encontrada = consultar_pessoa(nome, idade, genero, cidade, filiacao, rg)
if pessoa_encontrada: 
print(f"Temos informações sobre {nome}.") 
print(f"Ele(a) está no abrigo: {pessoa_encontrada['Abrigo']}") 
print(f"Telefone do abrigo: {pessoa_encontrada['Telefone do Abrigo']}") 
else: generation_config = { 
"candidate_count": 1, 
"temperature": 1, 
} 
safety_settings={ 
'HATE': 'BLOCK_NONE', 
'HARASSMENT': 'BLOCK_NONE', 
'SEXUAL' : 'BLOCK_NONE', 
'DANGEROUS' : 'BLOCK_NONE' 
} 
model = genai.GenerativeModel(
model_name='gemini-1.0-pro', 
generation_config=generation_config, 
safety_settings=safety_settings,
 ) 
prompt = f"Crie uma mensagem de conforto para alguém que está procurando por {nome}, {idade} anos, {genero}, de {cidade}." 
response = model.generate_content(contents=[prompt]) 
print(response.candidates[0].content) 
celular = input("Digite um número de celular para contato: ") 
adicionar_pessoa(nome, idade, genero, cidade, filiacao, rg, celular) 
print(f"As informações foram adicionadas à planilha. Esperamos que {nome} seja encontrado(a) em breve.")
```
**Explicação:**

1.  Coleta  informações  da  pessoa  desaparecida.
    
2.  Consulta  se  a  pessoa  já  está  cadastrada  na  planilha.
    
3.  Se  a  pessoa  for  encontrada,  exibe  as  informações  da  planilha.
    
4.  Se  a  pessoa  não  for  encontrada:
    
    -   Gera  uma  mensagem  de  conforto  utilizando  o  Google  Gemini.
        
    -   Solicita  o  número  de  celular  da  pessoa  que  está  buscando.
        
    -   Adiciona  as  informações  da  pessoa  desaparecida  à  planilha.
        

### Tutorial:  Criando  APIs  em  JSON,  Sheets  e  Secret  Manager

#### **Google  Sheets  API:**

1.  **Criar  uma  planilha:**  Acesse  o  Google  Sheets  e  crie  uma  nova  planilha.
    
2.  **Habilitar  a  API  Google  Sheets:**  Acesse  o  [Google  Cloud  Console](https://console.cloud.google.com/),  selecione  seu  projeto  e  habilite  a  API  Google  Sheets.
    
3.  **Criar  credenciais:**  No  Google  Cloud  Console,  crie  credenciais  do  tipo  "Conta  de  serviço".  Faça  o  download  do  arquivo  JSON  com  a  chave  privada.
    

#### **Secret  Manager:**

1.  **Acessar  o  Secret  Manager:**  No  Google  Cloud  Console,  acesse  o  Secret  Manager.
    
2.  **Criar  um  novo  segredo:**  Crie  um  novo  segredo  e  dê  um  nome  a  ele  (ex:  "projeto_desaparecidos").
    
3.  **Adicionar  o  conteúdo  do  arquivo  JSON:**  Copie  o  conteúdo  do  arquivo  JSON  com  a  chave  privada  da  API  Google  Sheets  e  cole  no  segredo  criado.
    

#### **JSON:**

1.  **Formato  do  arquivo  JSON:**  O  arquivo  JSON  conterá  informações  como  o  ID  do  projeto,  o  email  da  conta  de  serviço  e  a  chave  privada.
    
    ```
    {
      "type": "service_account",
      "project_id": "seu-projeto-id",
      "private_key_id": "sua-chave-privada-id",
      "private_key": "-----BEGIN PRIVATE KEY-----\nSUA_CHAVE_PRIVADA\n-----END PRIVATE KEY-----\n",
      "client_email": "seu-email-de-servico@seu-projeto-id.iam.gserviceaccount.com",
      "client_id": "seu-client-id",
      "auth_uri": "https://accounts.google.com/o/oauth2/auth",
      "token_uri": "https://oauth2.googleapis.com/token",
      "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
      "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/seu-email-de-servico%40seu-projeto-id.iam.gserviceaccount.com"
    }
    ```
    
    content_copyUse code  [with caution](https://support.google.com/legal/answer/13505487).Json
    
2.  **Utilização  no  código:**  Utilize  a  biblioteca  json  para  carregar  o  conteúdo  do  arquivo  JSON  e  acessar  as  informações  da  API.
    

**Autoria:**

Este  código  foi  criado  durante  a  **Imersão  Alura  e  Google  Gemini**  por  **Lucas  Antonio  de  Souza  em  colaboração  com  Gemini-1.5 Pro**.
