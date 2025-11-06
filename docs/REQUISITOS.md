# Documento de Requisitos de Engenharia  
**Sistema de Gestão de Ativos e Riscos (ISO 27001)**  

**Autor:** Gabriel de Oliveira  
**Disciplina:** Programação Orientada a Objetos (ADS – SENAC)  
**Versão:** 1.1 – Revisada em 06/11/2025  

---

## 1. Propósito e Contexto

### 1.1. O Problema
A falta de visibilidade sobre o inventário de ativos de TI (hardware e software) e suas vulnerabilidades associadas é um dos principais riscos de cibersegurança em qualquer organização.  
A **NBR ISO/IEC 27001**, no seu **Anexo A (Controle A.8)**, exige formalmente a identificação e o gerenciamento de ativos como fundamento de um **Sistema de Gestão de Segurança da Informação (SGSI)**.  
Sem esse controle, não há como gerenciar riscos de forma estruturada.

### 1.2. O Objetivo (A Solução)
O sistema proposto (MVP) visa criar um **inventário centralizado de ativos e vulnerabilidades**, executado via **linha de comando (CLI)**.  
Ele fornece o CRUD completo das entidades principais, permitindo à organização responder de forma rápida e rastreável às perguntas:  
**“O que temos?”, “Quem é o dono?” e “Qual é o risco?”**  

Este projeto materializa o **Controle A.8 da ISO 27001** dentro de um contexto acadêmico, aplicando os princípios de **Programação Orientada a Objetos (POO)** e **mapeamento ORM com SQLAlchemy**.

---

## 2. Requisitos Técnicos (RT)  
*(Mapeamento direto com os critérios de avaliação da disciplina)*  

| ID | Critério de Avaliação | Peso | Prioridade | Requisito Técnico Derivado |
| :--- | :--- | :--- | :--- | :--- |
| RT-01 | Uso de SQLAlchemy | 30% | **Alta** | O sistema **deve** usar SQLAlchemy para todas as operações de persistência e mapeamento ORM. |
| RT-02 | Herança em ORM | - | **Alta** | O sistema **deve** implementar Herança em Tabelas (Joined Table) para a entidade `Ativo`. |
| RT-03 | Relacionamento Many-to-Many | - | **Alta** | O sistema **deve** implementar um relacionamento Many-to-Many entre `Ativo` e `Vulnerabilidade`. |
| RT-04 | Polimorfismo Extensível | 15% | **Alta** | O sistema **deve** implementar o método polimórfico `verificar_risco()`, com comportamento distinto para `AtivoHardware` e `AtivoSoftware`, permitindo extensões futuras. |
| RT-05 | Encapsulamento e Reuso | - | **Alta** | O código **deve** aplicar encapsulamento, herança e reuso conforme os princípios da POO. |
| RT-06 | Banco de Dados | - | **Alta** | O sistema **deve** utilizar banco de dados **MySQL hospedado na nuvem** (PlanetScale ou Railway). |
| RT-07 | Qualidade do Código | 10% | **Média** | O código **deve** seguir o padrão PEP 8, conter **docstrings** nas classes e métodos principais e comentários nas lógicas complexas. |
| RT-08 | Interface com o Usuário | 5% | **Média** | O sistema **deve** possuir uma interface CLI intuitiva com feedback textual claro ao usuário. |

---

## 3. Requisitos Não Funcionais (RNF)

| ID | Categoria | Prioridade | Requisito Não Funcional |
| :--- | :--- | :--- | :--- |
| RNF-01 | Persistência | **Alta** | Os dados devem ser armazenados em banco de dados MySQL hospedado em nuvem, garantindo portabilidade e persistência. |
| RNF-02 | Usabilidade | **Média** | A interface CLI deve ser simples e fornecer feedback textual (“Operação realizada com sucesso”, “Erro: item não encontrado”). |
| RNF-03 | Portabilidade | **Média** | O sistema deve ser executável em **Windows**, **Linux** e **macOS**, com Python 3. |
| RNF-04 | Qualidade | **Média** | O código deve seguir **PEP 8**, conter **docstrings** e manter modularização (main.py, models.py, database.py). |
| RNF-05 | Segurança | **Alta** | As credenciais de conexão com o banco de dados devem ser armazenadas em variáveis de ambiente (.env). |
| RNF-06 | Desempenho | **Média** | As operações CRUD devem executar em até **3 segundos** em ambiente padrão. |

---

## 4. Requisitos Funcionais (RF) – CRUD das Entidades

| ID | Descrição | Prioridade | Entidades Mapeadas |
| :--- | :--- | :--- | :--- |
| RF-A01 | O sistema deve permitir **Criar** (cadastrar) um novo Ativo (Hardware ou Software). | **Alta** | `Ativo`, `AtivoHardware`, `AtivoSoftware` |
| RF-A02 | O sistema deve permitir **Ler** (listar) todos os Ativos cadastrados. | **Alta** | `Ativo` |
| RF-A03 | O sistema deve permitir **Buscar** um Ativo específico por ID ou nome. | **Média** | `Ativo` |
| RF-A04 | O sistema deve permitir **Atualizar** informações de um Ativo existente. | **Média** | `Ativo` |
| RF-A05 | O sistema deve permitir **Excluir** um Ativo e seus relacionamentos. | **Baixa** | `Ativo` |
| RF-V01 | O sistema deve permitir **Criar** (cadastrar) uma nova Vulnerabilidade (ex: CVE-2025-XXXX). | **Alta** | `Vulnerabilidade` |
| RF-V02 | O sistema deve permitir **Ler** (listar) todas as Vulnerabilidades cadastradas. | **Alta** | `Vulnerabilidade` |
| RF-V03 | O sistema deve permitir **Atualizar** dados de uma Vulnerabilidade. | **Média** | `Vulnerabilidade` |
| RF-V04 | O sistema deve permitir **Excluir** uma Vulnerabilidade existente. | **Baixa** | `Vulnerabilidade` |
| RF-F01 | O sistema deve permitir **Cadastrar** um Funcionário. | **Média** | `Funcionario` |
| RF-F02 | O sistema deve permitir **Listar** todos os Funcionários e seus Ativos associados. | **Média** | `Funcionario`, `Ativo` |
| RF-F03 | O sistema deve permitir **Atualizar** dados de um Funcionário. | **Baixa** | `Funcionario` |
| RF-F04 | O sistema deve permitir **Excluir** um Funcionário. | **Baixa** | `Funcionario` |

---

## 5. Regras de Negócio (RF-L)

| ID | Descrição da Regra | Prioridade | Entidades Mapeadas |
| :--- | :--- | :--- | :--- |
| RF-L01 | O sistema deve permitir **associar** múltiplas Vulnerabilidades a um Ativo (relação Many-to-Many). | **Alta** | `Ativo`, `Vulnerabilidade`, `ativo_vulnerabilidades` |
| RF-L02 | O sistema deve permitir **associar** múltiplos Ativos a um Funcionário (relação One-to-Many). | **Alta** | `Funcionario`, `Ativo` |
| RF-L03 | O método polimórfico `verificar_risco()` deve retornar um nível de criticidade: **Crítico**, **Médio** ou **Baixo**, conforme as condições definidas. | **Média** | `AtivoHardware`, `AtivoSoftware` |
|  | - Em `AtivoHardware`: retorna **Crítico** se o sistema operacional estiver fora de suporte. | | |
|  | - Em `AtivoSoftware`: retorna **Crítico** se a versão estiver vulnerável ou a licença expirada. | | |

---

## 6. Rastreabilidade entre Requisitos e Implementação

| Requisito | Implementação Planejada | Arquivo |
| :--- | :--- | :--- |
| RT-01 a RT-06 | Conexão ORM e modelagem de classes | `models.py`, `database.py` |
| RF-A01 a RF-A05 | CRUD de Ativos | `main.py`, `models.py` |
| RF-V01 a RF-V04 | CRUD de Vulnerabilidades | `main.py`, `models.py` |
| RF-F01 a RF-F04 | CRUD de Funcionários | `main.py`, `models.py` |
| RF-L01 a RF-L03 | Relacionamentos e polimorfismo | `models.py` |
| RNF-01 a RNF-06 | Configuração e boas práticas | `.env`, `README.md`, `database.py` |

---

## 7. Referências

- **NBR ISO/IEC 27001:2022 – Controle A.8 (Inventário de Ativos de Informação)**  
- **NBR ISO/IEC 27002:2022 – A.12 (Gestão de Vulnerabilidades)**  
- **SQLAlchemy ORM – Documentação Oficial (SQLAlchemy.org)**  
- **Critérios de Avaliação – Projeto Floricultura Flor de Lótus (SENAC)**  

---

 **Status Atual:** Sprint 1 – Definição de Escopo e Requisitos concluída.  
 **Próximo passo:** Início da modelagem ORM e criação das classes no `models.py`.
