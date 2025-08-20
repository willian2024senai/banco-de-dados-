CREATE DATABASE locadora;
USE locadora;

CREATE TABLE Filmes (
    FilmeID INTEGER PRIMARY KEY,
    Titulo VARCHAR(255),
    AnoLancamento INTEGER,
    Genero VARCHAR(100)
);

CREATE TABLE Clientes (
    ClienteID INTEGER PRIMARY KEY,
    Nome VARCHAR(255),
    Email VARCHAR(255)
);

CREATE TABLE Locacoes (
    LocacaoID INTEGER PRIMARY KEY,
    ClienteID INTEGER,
    FilmeID INTEGER,
    DataLocacao DATE,
    DataDevolucao DATE,
    PrecoLocacao DECIMAL(10,2),
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID),
    FOREIGN KEY (FilmeID) REFERENCES Filmes(FilmeID)
);

INSERT INTO Filmes (FilmeID, Titulo, AnoLancamento, Genero) VALUES
(1, 'Matrix', 1999, 'Ação'),
(2, 'Titanic', 1997, 'Romance'),
(3, 'Vingadores', 2012, 'Ação');

INSERT INTO Clientes (ClienteID, Nome, Email) VALUES
(1, 'João Silva', 'joao@email.com'),
(2, 'Maria Souza', 'maria@email.com'),
(3, 'Carlos Lima', 'carlos@email.com');

INSERT INTO Locacoes (LocacaoID, ClienteID, FilmeID, DataLocacao, DataDevolucao, PrecoLocacao) VALUES
(1, 1, 1, '2023-01-10', '2023-01-15', 10.00),
(2, 2, 2, '2023-02-05', '2023-02-10', 15.00),
(3, 1, 3, '2024-03-12', '2024-03-17', 20.00),
(4, 3, 1, '2024-04-20', '2024-04-25', 25.00),
(5, 2, 3, '2024-05-01', '2024-05-06', 30.00);

SELECT
    Ano,
    Nome,
    TotalGasto
FROM (
    SELECT
        EXTRACT(YEAR FROM l.DataLocacao) AS Ano,
        c.Nome,
        SUM(l.PrecoLocacao) AS TotalGasto,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM l.DataLocacao)
            ORDER BY SUM(l.PrecoLocacao) DESC
        ) AS Ranking
    FROM
        Locacoes l
    JOIN
        Clientes c ON l.ClienteID = c.ClienteID
    GROUP BY
        Ano, c.ClienteID, c.Nome
) AS RankingPorAno
WHERE
    Ranking = 1
ORDER BY
    Ano DESC;
