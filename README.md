### Alterações em Tabelas Existentes:

- Deverá ser feita a criação da coluna (SituacaoMemed) na Tabela (SWUsuarios). Que defini se o usuário médico em questão

### Criação do Usuário Médico na MEMED:

- Na tela de Ambiente deve ser criada uma nova Flag para configuração do Usuário Médico na Memed.
- A tela de Cadastro de Usuário na Memed deve: Cadastrar Novo Usuário, Editar Cadastro Médico e verificar Status da Integração.
- No Banco de Dados, deverá ser criadas duas tabelas para a Memed: *memedUsuarios* e *memedEspecialidades.*
    
    Na tabela *memedusuários* será salvo os dados Cadastrados pela configuração do Ambiente, e será referenciado essa tabela com a SWUsuarios através da Coluna *CodUsuario.* Ou seja, ao cadastrar novo usuário o sistema faz um insert, se esse CodUsuario já existe nessa tabela ele faz um update. 
    
    Na tela de cadastro do Médico existe uma seleção de especialidades que está listada na *memedEspecialidades* e deve ser utilizado seu ID para salvar o cadastro médico na *memedUsuarios.*
    

### Na API da MEMED o cadastro do Médico:

- Deve ser consultado se já existe o CPF do Médico que está sendo inserido na tela do Medsystem, usando o **GET**:
    
    [https://integrations.api.memed.com.br/v1/sinapse-prescricao/usuarios/](https://integrations.api.memed.com.br/v1/sinapse-prescricao/usuarios/86399807727){xxxxCPFxxxx}
    
- Se o Médico já existe, então ele faz um **Insert** no Banco de Dados, mas na API ele faz um **PATCH** para Editar/Atualizar o cadastro Médico:
    
    No Banco de Dados será feito o Seguinte Insert:
    
    ```sql
    --Cadastrando o Usuário Médico
    INSERT INTO MemedUsuarios (CodUsuario, Nome, Sobrenome, Telefone, CPF, DataNasc, Sexo, Email, CRM, UF, idEspecialidade, StatusIntegracao)
    VALUES (2, 'Agnaldo', 'Siqueira', '35 1111-1111', '11111111100', '1985-01-01', 'M', 'testedoutor@email.com', '123245', 'MG', 5, 1)
    ```
    
    [https://integrations.api.memed.com.br/v1/sinapse-prescricao/usuarios/54628304084](https://integrations.api.memed.com.br/v1/sinapse-prescricao/usuarios/54628304084)
    
    Deverá ser Informado os Campos que foram alterados na tela Medsystem para a API:
    
    ```json
    {
      "data": {
        "type": "usuarios",
              "attributes": {
                  "nome": "Matheus QA ",
                  "sobrenome": "da Silva",
                  "data_nascimento": "01/01/1985",
                  "cpf": "54628304084",
                  "uf": "SP",
                  "sexo": "M",
                  "crm": "812523",
    						"email": "vaiiiii.teste@email.com",
    						"avatar": "string",
          			"telefone": "string"
      },
      "relationships": {
        "especialidade": {
          "data": {
            "id": 5
          }
        }
      }
    }
    }
    ```
    
- Se a API não retornar o cadastro do Médico então ele deve ser **inserido** no Banco de Dados e feito um **Post** na API:
    
    Na Banco de Dados permanece o Insert:
    
    ```sql
    --Cadastrando o Usuário Médico
    INSERT INTO MemedUsuarios (CodUsuario, Nome, Sobrenome, Telefone, CPF, DataNasc, Sexo, Email, CRM, UF, idEspecialidade, StatusIntegracao)
    VALUES (2, 'Agnaldo', 'Siqueira', '35 1111-1111', '11111111100', '1985-01-01', 'M', 'testedoutor@email.com', '123245', 'MG', 5, 1)
    ```
    
    Na API será o seguinte POST:
    
    ```json
    {
            "data": {
             "type": "usuarios",
              "attributes": {
                  "external_id": "11111111100",
                  "nome": "Agnaldo",
                  "sobrenome": "Siqueira",
                  "data_nascimento": "01/01/1985",
                  "cpf": "11111111100",
                  "uf": "MG",
                  "sexo": "M",
                  "crm": "12345",
    						"email": "testedoutor@email.com",
    						"avatar": "string",
          			"telefone": "35 1111-1111"
        
      },
      "relationships": {
        "especialidade": {
          "data": {
            "id": 5
          }
        },
        "cidade": {
          "data": {
            "id": 0
          }
        }
      }
    }
    }
    ```
    
- O Sistema Medsystem deve validar o Status da Integração interno com o Status do cadastro na Memed:
