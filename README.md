# Estudo de Análise de Dados com Banco de Dados DVD Rental no PostgreSQL

Este projeto consiste em consultar, analisar e apresentar dados do banco de dados **DVD Rental**, utilizando Python, PostgreSQL e a biblioteca `psycopg2`. Foram realizadas consultas para extrair métricas estatísticas relacionadas a atores, filmes e clientes com base em locações (rentals).

---

## 🗂️ Base de Dados

- **Banco:** `dvdrental` (PostgreSQL)
- **Fonte:** Modelo relacional de locadora de filmes
- **Diagrama:** ![Diagrama ER](dvd-rental-sample-database-diagram.png)

---

## 📦 Requisitos

```bash
pip install psycopg2 pandas tabulate colorama
````

---

## 🔧 Conexão com o Banco

```python
import psycopg2 as pg2
conn = pg2.connect(
    host='localhost',
    port=5432,
    dbname='dvdrental',
    user='postgres',
    password='SUA_SENHA'
)
```

---

## 🎯 Consultas Realizadas

### 1. 🎭 Estatísticas por Ator

Consulta da **média** das variáveis relacionadas aos filmes de cada ator:

* `rental_duration`
* `rental_rate`
* `length`
* `replacement_cost`

```sql
SELECT
    a.actor_id,
    a.first_name,
    a.last_name,
    AVG(f.rental_duration) AS avg_rental_duration,
    AVG(f.rental_rate) AS avg_rental_rate,
    AVG(f.length) AS avg_length,
    AVG(f.replacement_cost) AS avg_replacement_cost
FROM
    actor AS a
    LEFT JOIN film_actor AS fa ON a.actor_id = fa.actor_id
    LEFT JOIN film AS f ON fa.film_id = f.film_id
GROUP BY
    a.actor_id, a.first_name, a.last_name
ORDER BY
    a.actor_id;
```

#### ✅ Resultado:

* 200 atores analisados
* Exemplo de saída:

| actor\_id | first\_name | last\_name | avg\_rental\_duration | avg\_rental\_rate | avg\_length | avg\_replacement\_cost |
| --------- | ----------- | ---------- | --------------------- | ----------------- | ----------- | ---------------------- |
| 1         | Penelope    | Guiness    | 4.84                  | 2.77              | 101.1       | 18.78                  |
| ...       | ...         | ...        | ...                   | ...               | ...         | ...                    |

---

### 2. 💰 Clientes que Mais e Menos Gastaram

Consulta da **quantidade de aluguéis** por cliente, utilizando nome completo e e-mail como identificador.

```sql
SELECT
    c.first_name || ' ' || c.last_name || ' (' || c.email || ')' AS customer_info,
    COUNT(r.*) AS total_rentals
FROM
    customer AS c
    JOIN rental AS r ON c.customer_id = r.customer_id
GROUP BY
    c.customer_id, c.first_name, c.last_name, c.email
ORDER BY
    total_rentals DESC
LIMIT 10;
```

#### 🏆 Top 10 Clientes (Mais aluguéis):

| Ranking | Cliente                                                                                  | Total de Aluguéis |
| ------- | ---------------------------------------------------------------------------------------- | ----------------- |
| 1       | Eleanor Hunt ([eleanor.hunt@sakilacustomer.org](mailto:eleanor.hunt@sakilacustomer.org)) | 46                |
| 2       | Karl Seal ([karl.seal@sakilacustomer.org](mailto:karl.seal@sakilacustomer.org))          | 45                |
| ...     | ...                                                                                      | ...               |

#### 🧾 Bottom 10 Clientes (Menos aluguéis):

Mesma estrutura, ordenada em ordem crescente.

---

## 🎨 Visualização Estilizada (HTML)

Usando `pandas`, `colorama` e `IPython.display`:

```python
from colorama import Fore, init
from IPython.display import HTML

top_spenders['Ranking'] = range(1, 11)
top_spenders_styled = top_spenders.style.apply(
    lambda x: [f"color: {Fore.GREEN if val == 'customer_info' else Fore.CYAN}" for val in x.index], axis=1
)
HTML(top_spenders_styled.to_html(escape=False))
```

---

## 🧠 Conclusões

* Atores variam consideravelmente em relação ao tempo de aluguel e custo médio de substituição dos filmes.
* Existe uma diferença significativa entre os clientes que mais alugam e os que menos alugam.
* Utilizar SQL em conjunto com Python e Pandas facilita o tratamento e visualização dos dados.

---

## 🛠️ Tecnologias Utilizadas

* PostgreSQL
* Python 3
* Bibliotecas: `psycopg2`, `pandas`, `tabulate`, `colorama`, `IPython.display`
