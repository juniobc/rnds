## Integração com o SIGTAP

**Objetivo:** Integrar sistemas de terceiros ao Sistema de Gerenciamento da Tabela de Procedimentos, Medicamentos e OPM do SUS (SIGTAP).

**Descrição:**
Esta integração permite que sistemas externos acessem detalhes de procedimentos do SIGTAP. A comunicação é realizada via serviços web SOAP.

**Fluxo da Integração:**
1.  **Requisição:** Sistemas terceiros enviam uma requisição SOAP para o serviço do SIGTAP.
2.  **Autenticação:** A requisição SOAP inclui um token de segurança (`UsernameToken`) no cabeçalho.
3.  **Processamento:** O serviço do SIGTAP processa a requisição e busca os detalhes do procedimento.
4.  **Resposta:** O serviço do SIGTAP retorna uma mensagem SOAP com os detalhes do procedimento.
5.  **Retorno:** Nossa aplicação recebe a resposta SOAP, extrai os dados e retorna para o sistema terceiro em formato de dicionário Python.

**Detalhes Técnicos:**
* **Padrão de Mensagens:** SOAP
* **Autenticação:** `UsernameToken`
* **Serviço Consultado:** SIGTAP
* **Procedimento Consultado:** Detalhar Procedimento

**Exemplo de Requisição (XML):**

```xml
<soap:Envelope xmlns:soap="[http://www.w3.org/2003/05/soap-envelope](http://www.w3.org/2003/05/soap-envelope)"
xmlns:proc="[http://servicos.saude.gov.br/sigtap/v1/procedimentoservice](http://servicos.saude.gov.br/sigtap/v1/procedimentoservice)"
xmlns:proc1="[http://servicos.saude.gov.br/schema/sigtap/procedimento/v1/procedimento](http://servicos.saude.gov.br/schema/sigtap/procedimento/v1/procedimento)"
xmlns:com="[http://servicos.saude.gov.br/schema/corporativo/v1/competencia](http://servicos.saude.gov.br/schema/corporativo/v1/competencia)"
xmlns:det="[http://servicos.saude.gov.br/wsdl/mensageria/sigtap/v1/detalheadicional](http://servicos.saude.gov.br/wsdl/mensageria/sigtap/v1/detalheadicional)"
xmlns:pag="[http://servicos.saude.gov.br/wsdl/mensageria/v1/paginacao](http://servicos.saude.gov.br/wsdl/mensageria/v1/paginacao)">
  <soap:Header>
    <wsse:Security xmlns:wsse="[http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd](http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd)" xmlns:wsu="[http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd](http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd)">
      <wsse:UsernameToken wsu:Id="Id-0001334008436683-000000002c4a1908-1">
        <wsse:Username>SIGTAP.PUBLICO</wsse:Username>
        <wsse:Password Type="[http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText](http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText)">sigtap#2015public</wsse:Password>
      </wsse:UsernameToken>
      <wsu:Timestamp wsu:Id="TS-1">
        <wsu:Created>2024-07-30T10:00:00Z</wsu:Created>
        <wsu:Expires>2024-07-30T10:05:00Z</wsu:Expires>
      </wsu:Timestamp>
    </wsse:Security>
  </soap:Header>
  <soap:Body>
    <proc:requestDetalharProcedimento>
      <proc1:codigoProcedimento>0405050097</proc1:codigoProcedimento>
      <com:competencia>202505</com:competencia>
      <proc:DetalhesAdicionais>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>DESCRICAO</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>CIDS</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>CBOS</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>CATEGORIAS_CBO</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>TIPOS_LEITO</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>SERVICOS_CLASSIFICACOES</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>HABILITACOES</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>GRUPOS_HABILITACAO</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>INCREMENTOS</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>COMPONENTES_REDE</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>ORIGENS_SIGTAP</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>ORIGENS_SIA_SIH</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>REGRAS_CONDICIONADA</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>RENASES</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
        <det:DetalheAdicional>
          <det:categoriaDetalheAdicional>TUSS</det:categoriaDetalheAdicional>
          <det:Paginacao>
            <pag:registroInicial>1</pag:registroInicial>
            <pag:quantidadeRegistros>20</pag:quantidadeRegistros>
            <pag:totalRegistros>100</pag:totalRegistros>
          </det:Paginacao>
        </det:DetalheAdicional>
      </proc:DetalhesAdicionais>
    </proc:requestDetalharProcedimento>
  </soap:Body>
</soap:Envelope>
Exemplo de Retorno (nosso código):return {
            "codigo_procedimento": codigo,
            "nome_procedimento": nome,
            "forma_organizacao": {
                "codigo": forma_organizacao_codigo,
                "nome": forma_organizacao_nome,
                "subgrupo": {
                    "codigo": subgrupo_codigo,
                    "nome": subgrupo_nome,
                    "grupo": {
                        "codigo": grupo_codigo,
                        "nome": grupo_nome
                    }
                }
            },
            "competencia_inicial": competencia_inicial,
            "finalidade_publicacao": finalidade_publicacao,
            "documento_publicacao": {
                "numero_documento": numero_documento,
                "tipo_documento": {
                    "codigo": tipo_documento_codigo,
                    "nome": tipo_documento_nome
                },
                "orgao_origem": {
                    "codigo": orgao_origem_codigo,
                    "nome": orgao_origem_nome
                },
                "data_publicacao": data_publicacao
            },
            "modalidades_atendimento": modalidades_atendimento,
            "complexidade": complexidade,
            "tipo_financiamento": {
                "codigo": tipo_financiamento_codigo,
                "nome": tipo_financiamento_nome
            },
            "instrumentos_registro": instrumentos_registro,
            "quantidade_maxima": quantidade_maxima,
            "sexo_permitido": sexo_permitido,
            "idade_minima_permitida": {
                "quantidade": idade_minima_quantidade,
                "unidade": idade_minima_unidade
            },
            "idade_maxima_permitida": {
                "quantidade": idade_maxima_quantidade,
                "unidade": idade_maxima_unidade
            },
            "valor_sh": valor_sh,
            "valor_sa": valor_sa,
            "valor_sp": valor_sp,
            "atributos_complementares": atributos_complementares,
            "descricao": descricao,
            "cbos_vinculados": cbos_vinculados,
            "categorias_cbo_vinculadas": categorias_cbo_vinculadas,
            "servicos_classificacoes_vinculados": servicos_classificacoes_vinculados,
            "origens_sia_sih": origens_sia_sih,
            "renases_vinculadas": renases_vinculadas,
            "cids_vinculados": cids_vinculados,
            "habilitacoes_vinculadas": habilitacoes_vinculadas,
            "regras_condicionadas_vinculadas": regras_condicionadas_vinculadas
        }
