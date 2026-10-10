# alfabetiza-comunidade-bd2
-- DDL: Criação das Tabelas e Restrições de Integridade
CREATE TABLE Usuarios (
    id_usuario SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    senha_hash VARCHAR(255) NOT NULL,
    perfil VARCHAR(20) CHECK (perfil IN ('professor', 'voluntario', 'admin')),
    criado_em TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE Alunos (
    id_aluno SERIAL PRIMARY KEY,
    nome_completo VARCHAR(150) NOT NULL,
    data_nascimento DATE NOT NULL,
    endereco VARCHAR(200),
    contato VARCHAR(20),
    data_cadastro DATE DEFAULT CURRENT_DATE
);

CREATE TABLE Turmas (
    id_turma SERIAL PRIMARY KEY,
    nome_turma VARCHAR(50) NOT NULL,
    ano_letivo INT NOT NULL,
    id_professor INT REFERENCES Usuarios(id_usuario) ON DELETE SET NULL
);

CREATE TABLE Frequencias (
    id_frequencia SERIAL PRIMARY KEY,
    id_aluno INT REFERENCES Alunos(id_aluno) ON DELETE CASCADE,
    id_turma INT REFERENCES Turmas(id_turma) ON DELETE CASCADE,
    data_aula DATE NOT NULL,
    presente BOOLEAN DEFAULT TRUE
);

CREATE TABLE Avaliacoes_Desempenho (
    id_avaliacao SERIAL PRIMARY KEY,
    id_aluno INT REFERENCES Alunos(id_aluno) ON DELETE CASCADE,
    data_avaliacao DATE NOT NULL,
    nivel_leitura VARCHAR(50) NOT NULL,
    observacoes TEXT
);

-- DML: Inserção de Dados (Carga Inicial de Teste)
INSERT INTO Usuarios (nome, email, senha_hash, perfil) 
VALUES ('Geraldo Manoel Gonçalves', 'geraldo@email.com', 'hash123', 'professor');

INSERT INTO Alunos (nome_completo, data_nascimento, endereco, contato) 
VALUES ('João da Silva', '1985-04-12', 'Rua Principal, 100 - Olaria', '21999998888');

INSERT INTO Turmas (nome_turma, ano_letivo, id_professor) 
VALUES ('Alfabetização Módulo 1 - Olaria', 2026, 1);

INSERT INTO Frequencias (id_aluno, id_turma, data_aula, presente) 
VALUES (1, 1, '2026-10-01', TRUE);

INSERT INTO Avaliacoes_Desempenho (id_aluno, data_avaliacao, nivel_leitura, observacoes)
VALUES (1, '2026-10-05', 'Silábico com valor sonoro', 'Apresentou grande avanço na identificação das vogais.');

-- DML: Operações de Atualização e Exclusão
UPDATE Alunos 
SET contato = '21988887777' 
WHERE id_aluno = 1;

DELETE FROM Frequencias 
WHERE id_frequencia = 999;

-- DML: Consultas Avançadas (Junções e Relatórios)
SELECT a.nome_completo, t.nome_turma, f.data_aula, f.presente 
FROM Frequencias f
JOIN Alunos a ON f.id_aluno = a.id_aluno
JOIN Turmas t ON f.id_turma = t.id_turma
WHERE f.data_aula = '2026-10-01';

SELECT a.nome_completo, 
       COUNT(f.id_frequencia) AS total_aulas,
       SUM(CASE WHEN f.presente THEN 1 ELSE 0 END) AS total_presencas
FROM Alunos a
LEFT JOIN Frequencias f ON a.id_aluno = f.id_aluno
GROUP BY a.id_aluno, a.nome_completo;
