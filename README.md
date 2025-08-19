Análise da API de Recomendação de Livros
Este projeto consiste em uma API RESTful desenvolvida em Python, utilizando o framework Flask. Seu objetivo é demonstrar um sistema de recomendação de livros, integrando uma API para gerenciamento de usuários e avaliações com um modelo de machine learning para a geração de recomendações personalizadas. A interface de usuário foi construída com a biblioteca Streamlit.

Como Executar o Projeto
Para colocar o projeto em funcionamento, siga os passos a seguir:

Em primeiro lugar, a execução principal do sistema, é o arquivo streamlit_app.py.  
Você só vai executar ele depois do app.py, porque o app.py tem o carregamento e o salvamento tanto do cadastro e tanto dos livros. 


Instalação das dependências: Certifique-se de que o Python está instalado em seu sistema. Navegue até o diretório do projeto no terminal e instale as bibliotecas necessárias listadas no arquivo requirements.txt usando o comando:

pip install -r requirements.txt

Início do Servidor Flask: Em um terminal, execute o arquivo principal da API (app.py):

python app.py

Início da Interface Streamlit: Abra um segundo terminal e inicie a interface de usuário com o seguinte comando:

streamlit run streamlit_app.py

A aplicação web será automaticamente aberta em seu navegador padrão.

Explicação da Arquitetura do Código
O projeto é estruturado em arquivos, cada um com uma responsabilidade específica para garantir organização e clareza.

app.py
Este é o núcleo da API, responsável por definir as rotas (endpoints) que orquestram as funcionalidades do sistema.

Rotas:

/register (POST): Rota para cadastrar um novo usuário. Requer o nome e o gênero de leitura preferido e retorna um identificador único de usuário (user_id).

/books/<user_id> (GET): Retorna uma lista dos primeiros 30 livros, que são utilizados para a fase inicial de avaliação dos usuários para o modelo de machine learning.

/rate (POST): Rota para registrar ou atualizar a avaliação de um livro por um usuário específico.

/recommend/<user_id> (GET): Gera e retorna recomendações personalizadas para o usuário. O sistema pode recomendar com base no gênero preferido ou, se houver avaliações suficientes, utilizar um modelo de machine learning.

/user-ratings/<user_id> (GET): Retorna todas as avaliações registradas por um usuário.

books_service.py
Este arquivo é responsável pela comunicação com a API do Google Books para buscar dados de livros. Ele busca dados de diversas categorias para popular a lista de livros do sistema.

data.py
Funciona como um banco de dados em memória, armazenando os dados em estruturas simples (dicionários e listas) como users, books, e ratings enquanto o servidor está em execução.

model.py
Contém toda a lógica do sistema de recomendação. A estratégia adotada é a seguinte:

Se o usuário tiver menos de 3 avaliações, o sistema recomenda livros com base no gênero que ele informou como preferido no cadastro.

Se o usuário tiver 3 ou mais avaliações, um modelo de machine learning (KNeighborsRegressor da biblioteca scikit-learn) é treinado com os dados de suas avaliações para prever notas para livros ainda não lidos. O sistema então recomenda os livros com a maior pontuação prevista.

streamlit_app.py
Este arquivo é a interface de usuário, construída com a biblioteca Streamlit. Sua função é interagir com a API Flask, enviando e recebendo dados através de requisições HTTP.

Lógica da Interface:

Cadastro: Permite que o usuário insira seu nome e gênero preferido.

Avaliação: Exibe a lista de livros disponíveis para avaliação, permitindo que o usuário atribua uma nota de 1 a 5 por meio de um controle deslizante. Cada avaliação é enviada para a API via uma requisição POST para a rota /rate.

Recomendação: Um botão na interface aciona uma requisição GET para a rota /recommend/<user_id> para obter as recomendações personalizadas.

Lógica e Fluxo de Execução
O usuário abre a aplicação Streamlit no navegador.

No menu de cadastro, o usuário insere seus dados. A interface envia uma requisição POST para a rota /register da API, que retorna um user_id único.

A interface exibe a lista de livros para avaliação, obtida da rota /books/<user_id>.

O usuário avalia os livros, e cada avaliação é enviada para a API através da rota /rate.

Ao clicar no botão "Gerar Recomendações", a interface faz uma requisição para a rota /recommend/<user_id>.

Na API, a função recommend_books analisa a quantidade de avaliações do usuário. Se forem menos de 3, a recomendação é baseada no gênero preferido. Se forem 3 ou mais, o modelo de machine learning é ativado para prever as notas e retornar os livros com maior pontuação.

