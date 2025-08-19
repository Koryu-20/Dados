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

/recommend/<user_id>: Rota GET para gerar e retornar recomendações personalizadas. A recomendação pode ser baseada no gênero preferido (se o usuário avaliou poucos livros) ou em um modelo de ML (se o usuário já avaliou 3 ou mais livros).

/user-ratings/<user_id>: Rota GET para listar todas as avaliações de um usuário específico.

books_service.py
Este arquivo é responsável por interagir com a Google Books API para buscar os livros. Ele preenche a lista de livros do nosso sistema com dados reais de várias categorias.

data.py
Este arquivo age como um banco de dados em memória. As variáveis users, books e ratings são dicionários e listas simples que armazenam os dados enquanto o servidor está em execução.

model.py
Este arquivo contém a lógica do sistema de recomendação.

Lógica do Modelo: Se o usuário avaliou menos de 3 livros, o sistema recomenda livros com base no gênero preferido. Se o usuário avaliou 3 ou mais, ele usa um modelo de Machine Learning (KNeighborsRegressor da biblioteca scikit-learn) para prever notas para livros não avaliados e recomendar os que têm a maior nota prevista.

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

Se for 3 ou mais, ela treina o modelo de KNeighborsRegressor com as avaliações existentes do usuário e prediz notas para os livros restantes, retornando os com maior pontuação prevista.