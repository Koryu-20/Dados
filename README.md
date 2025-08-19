📚 Book Recommender API
Este projeto é uma API RESTful em Python usando o framework Flask. Ele foi criado para demonstrar um sistema de recomendação de livros simples, combinando uma API para cadastro e avaliações com um modelo de machine learning para gerar recomendações personalizadas. A interface é construída com Streamlit.

🚀 Como Rodar
Para executar este projeto, siga os passos abaixo:

Instale as dependências: Certifique-se de que você tem o Python instalado. Navegue até a pasta do projeto no terminal e instale as bibliotecas necessárias.

pip install -r requirements.txt

Inicie o servidor Flask: Em um terminal, execute o arquivo app.py.

python app.py

Inicie a interface Streamlit: Abra um segundo terminal e execute o arquivo streamlit_app.py.

streamlit run streamlit_app.py

A interface da web será aberta automaticamente no seu navegador.

🔎 Explicação do Código
O projeto é dividido em arquivos, cada um com uma responsabilidade específica para manter a organização e a clareza do código.

app.py
Este é o coração da API. Ele define as rotas (endpoints) para as diferentes funcionalidades do sistema.

Rotas:

/register: Rota POST para cadastrar um novo usuário. Recebe nome e genero_preferido e retorna um user_id único.

/books/<user_id>: Rota GET que retorna uma lista dos 30 primeiros livros, que são os livros que o usuário deve avaliar para o modelo de ML.

/rate: Rota POST para registrar ou atualizar a avaliação de um livro por um usuário.

/recommend/<user_id>: Rota GET que chama a função de recomendação e retorna uma lista de livros sugeridos para o usuário.

/user-ratings/<user_id>: Rota GET que lista todas as avaliações feitas por um usuário específico.

books_service.py
Este arquivo é responsável por buscar os dados dos livros de uma fonte externa.

Função load_books(): Conecta-se à API do Google Books para buscar uma lista de livros de diferentes gêneros. Os dados de cada livro (título, autores, gêneros) são armazenados em uma estrutura de dados na memória.

data.py
Este arquivo funciona como um banco de dados em memória para o projeto. As variáveis declaradas aqui mantêm o estado do aplicativo enquanto o servidor está rodando.

users: Dicionário para armazenar dados dos usuários (ID -> nome, gênero preferido).

books: Dicionário para armazenar os detalhes dos livros (ID -> título, autores, gêneros).

ratings: Lista para armazenar as avaliações de livros feitas pelos usuários (lista de dicionários contendo user_id, book_id e nota).

model.py
Este arquivo contém a lógica de machine learning para a recomendação de livros.

_build_genre_index() e _book_features(): Funções auxiliares que preparam os dados dos livros, convertendo os gêneros em um formato numérico (vetor multi-hot) para que o modelo de ML possa processá-los.

recommend_books(): A função principal. Ela é a que decide a melhor abordagem para a recomendação:

Lógica de Fallback: Se o usuário não avaliou livros suficientes (menos de 3), o sistema não pode treinar o modelo. Nesses casos, a função retorna uma lista de livros do gênero que o usuário informou como preferido no cadastro.

Modelo de ML: Se o usuário avaliou 3 ou mais livros, o sistema usa o KNeighborsRegressor da biblioteca scikit-learn. Este modelo aprende os padrões de avaliação do usuário e prevê uma nota para os livros que ele ainda não avaliou. Em seguida, a função retorna os livros com as maiores notas previstas.

streamlit_app.py
Este arquivo é a interface do usuário (a parte visual) construída com Streamlit. Ele faz as requisições para a API Flask (o app.py).

Lógica de Interface:

Cadastro: Permite que o usuário insira seu nome e gênero preferido.

Avaliação: Exibe a lista de livros para avaliação. Para cada livro, há um slider para o usuário dar uma nota de 1 a 5. A avaliação é enviada para a API via uma requisição POST para a rota /rate.

Recomendação: Um botão que, quando clicado, faz uma requisição GET para a rota /recommend/<user_id> para obter as recomendações.

📈 Lógica e Fluxo
O usuário abre a aplicação Streamlit.

Ele se cadastra, e a requisição POST vai para a rota /register na API. O user_id é retornado e armazenado na interface.

A interface exibe a lista de livros para avaliação, obtida da rota /books/<user_id>.

O usuário avalia os livros. Cada avaliação é enviada para a API via a rota /rate.

Quando o usuário clica em "Gerar Recomendações", a interface envia uma requisição para a rota /recommend/<user_id>.

Na API, a função recommend_books verifica o número de avaliações do usuário:

Se for menor que 3, ela retorna uma lista de livros com base no gênero preferido do usuário.

Se for 3 ou mais, ela treina o modelo de KNeighborsRegressor com as avaliações do usuário, prevê notas para os livros não avaliados e retorna os livros com as notas mais altas.

A interface Streamlit recebe a resposta da API e exibe as recomendações ao usuário.