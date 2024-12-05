# # Desafio Software Engineer Júnior - Javascript

## Índice - 
[Descrição](#descrição) - 
[Tecnologias Utilizadas](#tecnologias-utilizadas) - 
[Pré-requisitos](#pré-requisitos) -
[Fluxo 1: Inserção e Atualização de Leads](#fluxo-1)-
[Fluxo 2: Consulta via Webhook](#fluxo-2)-
## Descrição

Projeto voltado para o desenvolvimento de dois fluxos: um para refinamento, atualização e inserção de dados, e outro para recuperação de informações. Este sistema faz parte de um desafio proposto para avaliar as minhas competências técnicas (Davi Beserra Caldas).

## Tecnologias Utilizadas 

-  **[MongoDB](https://www.mongodb.com/pt-br)**: Banco de Dados baseado em Documentação desenvolvido em C++.
-  **[N8N](https://n8n.io)**: Plataforma de automação de código aberto que permite criar fluxos de trabalho personalizados e automatizados. 
-  **[API GEMINI](https://ai.google.dev)**: Assistente de Inteligência Artificial do Google. 
- 
## Pré-requisitos 

- **Gerenciador de Pacotes Node (caso utilize um servidor próprio)**
- **Serviço N8N (online ou em servidor próprio)**
- **Conta ativa no MongoDB** 
- **Acesso a API do Google Gemini** 

## Fluxo-1 
Fluxo no qual existe a inserção de dados a partir de um arquivo CSV no qual vai disponibilizar as informações necessária para que possa ser refinado e enviado para o DataBase no MongoDB. 
 - **Inicialização e Inserção de Dados via CSV** 
	 - Node 1 - **Start WorkFlow**: Este bloco é um **Manual Trigger**, indicando o ponto inicial onde o fluxo começa.
	 
	 - Node 2 - **Leitura do Documento CSV**: Bloco de leitura de arquivo em disco, comumente chamado de **Read/Write Files from Disk**, nele será especificado o diretório onde o arquivo está. É importante ressaltar que para especificar o diretório é necessário por dois "\", como nesse exemplo: "C:\\Users\\davib\\Documents\\Dados.csv". Um dos blocos fundamentais pois nele será identificado o arquivo que contém os dados.
	 
	 - Node 3 - **Extração de Dados do CSV**: Bloco responsável pelo reconhecimento dos dados provenientes do arquivo do bloco anterior, denominado **Extract from File**. Este bloco é importante para criar nossa base de dados bruta em formato JSON.
 - **Refinamento de Dados** 
	 - Node 1.1- **Refinamento de Dados**:  Utilizando um bloco de **Code**, nesta etapa é realizado o refinamento dos principais dados, além da criação dos campos **first_name**, **last_name** e **created_at**. Também são formatados os dados dos campos provenientes do CSV, de modo a seguir as regras propostas pelo desafio e evitar conflitos futuros.
	 
	 - Node 1.2- **Obtém Idade**: Nesse node, no qual, se trata de um **Date e Time**, configurado para que possa retorna a diferença em anos entre a data de aniversário do usuário e a data atual.
	 
	 - Node 1.3-  **Formatação Idade**: Em um novo node de **Code**, os campos de idade são formatados, retornando um valor inteiro sem decimais.
	 
	 - Node 2.1 **Agente AI**: Neste node, há uma bifurcação marcada pelo **Basic LLM Chain**, que recebe um prompt com o parâmetro **birthdate**, a fim de gerar um texto informando se existe uma possível data de aniversário inválida.
	 
 - **Inserção de Dados no DataBase** 
	 - Node  1- **Merge**: Node responsável pela união da estrutura de dados dos nodes bifurcados, combinando a base de dados refinada com a validade da data de aniversário.
	 - Node 2- **MongoDB**: No último nó, ocorre a atualização e inserção de dados no banco de dados. Configurado como "Update" e com o atributo "Upsert", ele será responsável por atualizar campos que já existam, utilizando o CPF, e adicionar novos dados caso não existam.

## Fluxo-2 

Fluxo no qual é inicializado um webhook que aguarda uma requisição contendo um body com um campo phone, o qual será utilizado como parâmetro para buscar dados no MongoDB afim de retornar informações de um usuário.

- **Inicialização do WebHock e formatação do Phone** 
	- Node  1- **Webhock**: Ao iniciar o processo, o webhook fica aguardando uma requisição com o campo phone para ser usado como parâmetro de busca no MongoDB. Para enviar a requisição, use o seguinte comando diretamente na opção de console do navegador, apenas alterando o campo {phone:'1234567890'} com o número que deseja fazer a consulta: 
	-"fetch('http://localhost:5678/webhook-test/retorna-dados-pelo-number', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({phone:'1234567890'})
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error('Erro:', error));
	
	- Node  2- **Formatação do Phone**: Realiza a formatação do número de telefone recebido na requisição, garantindo que ele esteja no formato correto para a busca no banco de dados.
	
- **Busca No Banco de Dados** 
	- Node  1- **MongoDB**: Realiza a consulta no banco de dados MongoDB, buscando os dados relacionados ao número de telefone formatado.
	
- **Resposta Via Console** 
	- Node  1- **If**: Verifica a existência de dados para o número de telefone consultado no banco de dados
	
	- Node  2.1- **Mensagem de Erro**: Caso o número não seja encontrado, exibe uma mensagem de erro na busca no console.
	
	- Node  2.2- **Mensagem de Sucesso**: Caso os dados sejam encontrados, exibe uma mensagem de sucesso.
	
