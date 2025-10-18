# Challenge SmartLocation Database 3

---

## Challenge Database 3

Este projeto foi desenvolvido para atender aos requisitos da disciplina **Mastering Relational and Non-Relational Database**, utilizando **Oracle Database** como base relacional.  
O contexto é a empresa **Mottu**, que necessita de um sistema eficiente para gestão de suas motos em diferentes pátios.  
O objetivo foi estruturar um banco de dados que possa servir de integração com aplicações externas (Java, IoT, APIs), garantindo confiabilidade, auditoria e manipulação avançada de dados.

---

### Estrutura do Banco de Dados

Foram criadas as seguintes tabelas principais:
- **PATIO**: Armazena informações sobre os estacionamentos (nome, endereço, capacidade, classe).  
- **MOTO**: Cadastro das motos (placa, modelo, status, vínculo com pátio).  
- **POSICAO**: Registra coordenadas geográficas de cada moto (latitude, longitude, timestamp).  
- **MOVIMENTACAO**: Histórico de entradas, saídas e movimentações das motos.  
- **SENSOR**: Dados de telemetria (nível de bateria, combustível, status ligado/desligado).  
- **MANUTENCAO**: Custos e registros de manutenções realizadas em cada moto.  
- **USUARIO**: Usuários da plataforma, com dados de identificação e forma de pagamento.  
- **AUDITORIA_MOTO**: Criada especificamente para armazenar logs de auditoria da trigger.  

Todas as tabelas possuem **mínimo de 5 registros inseridos** para permitir testes completos das funções e procedures.

---

### Funções

#### Função 1 – `fn_gerar_json_detalhes_moto`
- Recebe o **ID de uma moto**.  
- Retorna os dados no formato **JSON montado manualmente**, incluindo:
  - Placa, modelo, status.  
  - Nome do pátio associado.  
  - Última posição registrada (latitude/longitude).  
  - Último evento de movimentação com data/hora.  
- Implementa tratamento de exceções (`NO_DATA_FOUND`, `TOO_MANY_ROWS`, `VALUE_ERROR`, `OTHERS`).  
- Atende ao requisito de conversão relacional → JSON **sem uso de funções built-in** (`JSON_OBJECT`, etc.).  

#### Função 2 – `fn_calcular_percentual_ocupacao_patio`
- Recebe o **ID de um pátio**.  
- Calcula manualmente a taxa de ocupação = (motos ativas / capacidade total) * 100.  
- Retorna mensagem descritiva com o percentual.  
- Trata exceções como:
  - `NO_DATA_FOUND` → pátio inexistente.  
  - `ZERO_DIVIDE` ou capacidade zero.  
  - `VALUE_ERROR` e erros inesperados.  

---

### Procedures

#### Procedure 1 – `prc_gerar_json_detalhes_todas_motos`
- Percorre todas as motos cadastradas.  
- Realiza **JOIN entre `MOTO` e `PATIO`**.  
- Invoca a função JSON (`fn_gerar_json_detalhes_moto`) para consolidar os resultados em uma lista JSON única.  
- Exibe saída via `DBMS_OUTPUT.PUT_LINE`.  
- Trata exceções (`NO_DATA_FOUND`, `TOO_MANY_ROWS`, `VALUE_ERROR`, lista vazia).  

#### Procedure 2 – `prc_somatorio_manual_motos_por_patio`
- Calcula manualmente o somatório de valores de **manutenção** agrupados por **pátio e moto**.  
- Imprime:
  - Linha detalhada por moto.  
  - Subtotal por pátio.  
  - Total geral no final.  
- Não utiliza recursos automáticos como `ROLLUP`, `CUBE` ou `GROUPING`.  
- Implementa verificações extras:
  - Exceção para valores negativos.  
  - Exceção para ausência de dados.  

---

### Trigger

#### Trigger – `trg_auditoria_moto`
- Criada na tabela `MOTO` para auditoria de operações **INSERT, UPDATE e DELETE**.  
- Registra em `AUDITORIA_MOTO`:
  - Usuário do banco que executou a operação.  
  - Tipo da operação.  
  - Data e hora da execução.  
  - Valores antigos (`:OLD`) e novos (`:NEW`).  
- Garante rastreabilidade das mudanças, atendendo ao requisito de auditoria.  

---

### Testes Implementados
- Execução da **Função 1** para todas as motos, exibindo JSON detalhado.  
- Execução da **Função 2** para todos os pátios, exibindo ocupação percentual.  
- Chamada da **Procedure 1** para consolidar dados em JSON.  
- Execução da **Procedure 2** para cálculos de manutenção com subtotais e total.  
- Testes da **Trigger**, realizando:
  - Inserção de moto fictícia.  
  - Atualização de status.  
  - Exclusão da mesma moto.  
  - Consulta da tabela de auditoria para verificar logs.  

---

## Challenge Database 4

*(Ainda não desenvolvido)*  
