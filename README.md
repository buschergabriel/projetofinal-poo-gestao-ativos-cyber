# Projeto Final POO: Gestão de Ativos e Riscos (ISO 27001)

**Projeto final da disciplina de Programação Orientada a Objetos (ADS - SENAC)**
**Autor:** Gabriel de Oliveira

---

## 1. Visão e Intencionalidade do Projeto

Este projeto consiste em um sistema de terminal (CLI) para Gestão de Inventário de Ativos de TI e Mapeamento de Vulnerabilidades.

A intencionalidade é dupla:
1.  **Acadêmica:** Cumprir os requisitos da disciplina de POO, aplicando Herança, Polimorfismo e relacionamentos complexos (Many-to-Many) com SQLAlchemy.
2.  **Portfólio (Nível 3):** Criar uma ferramenta de Cibersegurança com valor prático, simulando a base de um sistema de GRC (Governança, Risco e Conformidade) exigido pelo controle **A.8 da NBR ISO 27001**.

## 2. Escopo do MVP (Minimum Viable Product)

O MVP foca em garantir 100% dos critérios de avaliação da disciplina, implementando o CRUD (Create, Read, Update, Delete) das seguintes entidades:

* **Ativos (Hardware e Software):** O inventário base.
* **Vulnerabilidades:** Um catálogo de riscos (CVEs, etc.).
* **Funcionários:** Os "proprietários" dos ativos.
* **Lógica de Negócio:**
    * Associar um Ativo a múltiplas Vulnerabilidades (M2M).
    * Associar um Funcionário a múltiplos Ativos (O2M).
    * Calcular o risco de um Ativo (Polimorfismo).

## 3. Arquitetura e Tecnologias

* **Linguagem:** Python 3
* **Banco de Dados:** MySQL (Hospedado na nuvem via PlanetScale/Railway para portabilidade)
* **ORM:** SQLAlchemy (para mapeamento objeto-relacional)
* **Interface:** CLI (Terminal)

## 4. Como Executar (Instruções)

*(A ser preenchido quando o código estiver funcional)*
