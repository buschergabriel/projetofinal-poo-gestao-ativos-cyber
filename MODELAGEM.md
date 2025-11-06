#  Documento de Modelagem de Dados (DER)  
**Sistema de Gestão de Ativos e Riscos (ISO 27001)**  

**Autor:** Gabriel de Oliveira  
**Versão:** 1.0  
**Status:** Sprint 2 – Em definição  

---

## 1. Propósito

Este documento traduz os **Requisitos Funcionais (RF)** e **Requisitos Técnicos (RT)** definidos no Sprint 1 em um **modelo conceitual e lógico de dados**.  
Seu objetivo é orientar a implementação do módulo `models.py`, garantindo que o design do banco (o *como*) esteja coerente com as regras de negócio (o *porquê*).  

---

## 2. Diagrama Entidade-Relacionamento (DER)
> ⚠️ *Nota técnica:* o parser do Draw.io não suporta a sintaxe de herança (`<|--`) e comentários inline.  
> Por isso, a herança é documentada na Seção 3, mas **não é exibida visualmente** no diagrama abaixo.

---


mermaid
erDiagram

Departamento {
    int id PK
    string nome
}

Funcionario {
    int id PK
    string nome
    string email
    int id_departamento FK
}

Ativo {
    int id PK
    string nome
    string criticidade
    string tipo
    int id_funcionario FK
}

AtivoHardware {
    int id PK
    string os
    string ip
    string modelo
}

AtivoSoftware {
    int id PK
    string versao
    date data_expiracao_licenca
    string tipo_licenca
}

Vulnerabilidade {
    int id PK
    string cve_id
    string severidade
}

Departamento ||--o{ Funcionario : "1N"
Funcionario ||--o{ Ativo : "1N"
Ativo }|--|{ Vulnerabilidade : "NM"


---


### 2.1. Entidades Principais

O sistema é composto por **6 entidades** principais:

1. **Departamento** – Agrupa os funcionários (ex: “TI”, “RH”).  
2. **Funcionario** – Representa os usuários responsáveis por ativos.  
3. **Ativo** – Classe base que representa qualquer bem de TI.  
4. **AtivoHardware** – Subclasse de `Ativo`, para equipamentos físicos.  
5. **AtivoSoftware** – Subclasse de `Ativo`, para aplicações e licenças.  
6. **Vulnerabilidade** – Representa falhas ou riscos associados a ativos.

### 2.2. Relacionamentos (Conexões)

| Relacionamento | Tipo | Descrição | Implementação |
| :--- | :--- | :--- | :--- |
| Departamento → Funcionario | 1:N | Um Departamento possui vários Funcionários. | `Funcionario.id_departamento` (FK) |
| Funcionario → Ativo | 1:N | Um Funcionário é responsável por vários Ativos. | `Ativo.id_funcionario` (FK) |
| Ativo ↔ Vulnerabilidade | N:M | Um Ativo pode ter várias Vulnerabilidades; uma Vulnerabilidade pode afetar vários Ativos. | Tabela de associação `ativo_vulnerabilidade` |
| Ativo → AtivoHardware | 1:1 | Herança: AtivoHardware **é um** Ativo. | `AtivoHardware.id` (FK → Ativo.id) |
| Ativo → AtivoSoftware | 1:1 | Herança: AtivoSoftware **é um** Ativo. | `AtivoSoftware.id` (FK → Ativo.id) |

---

## 3. Definição das Classes e Atributos

### 3.1. **Departamento**

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | Integer (PK) | Identificador único. |
| `nome` | String (Not Null, Unique) | Nome do departamento. |
| `funcionarios` | Relationship | Relação One-to-Many com `Funcionario`. |

---

### 3.2. **Funcionario**

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | Integer (PK) | Identificador único. |
| `nome` | String (Not Null) | Nome completo do funcionário. |
| `email` | String (Not Null, Unique) | E-mail institucional. |
| `id_departamento` | Integer (FK → Departamento.id) | Departamento ao qual pertence. |
| `departamento` | Relationship | Relação Many-to-One com `Departamento`. |
| `ativos` | Relationship | Relação One-to-Many com `Ativo`. |

---

### 3.3. **Ativo** *(Classe Base Abstrata)*

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | Integer (PK) | Identificador único. |
| `nome` | String (Not Null) | Nome do ativo (ex: “Servidor DB01”). |
| `criticidade` | String | Nível de criticidade (Baixa, Média, Alta). |
| `tipo` | String (Discriminador) | Define o tipo de ativo (hardware/software). |
| `id_funcionario` | Integer (FK → Funcionario.id) | Responsável pelo ativo. |
| `funcionario` | Relationship | Relação Many-to-One com `Funcionario`. |
| `vulnerabilidades` | Relationship | Relação Many-to-Many com `Vulnerabilidade`, via `ativo_vulnerabilidade`. |
| `verificar_risco()` | Método Abstrato | Método polimórfico implementado nas subclasses. |

---

### 3.4. **AtivoHardware** *(Herda de Ativo)*

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | Integer (PK, FK → Ativo.id) | Herda o ID da tabela `Ativo`. |
| `os` | String | Sistema operacional (ex: “Windows 11 Pro”). |
| `ip` | String (Unique) | Endereço IP da máquina. |
| `modelo` | String | Modelo do hardware. |
| `verificar_risco()` | Método | Retorna **“Crítico”** se `os` for legado (ex: “Windows 10”, “Ubuntu 22.04”). Caso contrário, **“Baixo”**. |

---

### 3.5. **AtivoSoftware** *(Herda de Ativo)*

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | Integer (PK, FK → Ativo.id) | Herda o ID da tabela `Ativo`. |
| `versao` | String | Versão do software (ex: “1.2.5”). |
| `data_expiracao_licenca` | Date | Data de validade da licença. |
| `tipo_licenca` | String | Tipo de licença (Perpétua, Assinatura). |
| `verificar_risco()` | Método | Retorna **“Crítico”** se `data_expiracao_licenca < hoje`. Caso contrário, **“Baixo”**. |

---

### 3.6. **Vulnerabilidade**

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | Integer (PK) | Identificador único. |
| `cve_id` | String (Unique) | Código CVE (ex: “CVE-2025-1234”). |
| `descricao` | Text | Descrição técnica da vulnerabilidade. |
| `severidade` | String | Nível de severidade (Crítica, Alta, Média, Baixa). |
| `ativos` | Relationship | Relação Many-to-Many com `Ativo`, via `ativo_vulnerabilidade`. |

---

### 3.7. **ativo_vulnerabilidade** *(Tabela de Associação N:M)*

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id_ativo` | Integer (PK, FK → Ativo.id) | ID do ativo. |
| `id_vulnerabilidade` | Integer (PK, FK → Vulnerabilidade.id) | ID da vulnerabilidade. |
| `status_correcao` | String | Estado atual da correção (“Pendente”, “Corrigida”, “Risco Aceito”). |

---

## 4. Observações de Implementação

- **Herança ORM:** Usar estratégia *Joined Table Inheritance* no SQLAlchemy.  
- **Relacionamentos:** Declarar os relacionamentos com `back_populates` para facilitar a navegação bidirecional.  
- **Validações:** Implementar métodos de validação básicos no `verificar_risco()` e, futuramente, controle de integridade nos CRUDs.  
- **Discriminador:** O campo `tipo` em `Ativo` servirá como *discriminator* para SQLAlchemy (`polymorphic_on=tipo`).  

---

## 5. Próximo Passo

 **Sprint 2 – Implementação do models.py**  
- Criar as classes conforme este documento.  
- Configurar o `Base` e o `engine` no `database.py`.  
- Testar a criação automática das tabelas no banco MySQL em nuvem.  

---

 **Status Atual:** Modelagem de dados aprovada e pronta para codificação.  
 **Referências:** ISO 27001 (A.8), SQLAlchemy ORM Docs, Critérios Floricultura Flor de Lótus.

